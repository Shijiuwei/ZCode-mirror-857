# Built-in Browser Automation API

The browser registry understands backend types `iab`, `extension`, and `cdp`. Playwright is a `Tab` API surface, not a backend. The desktop host normally advertises `iab`, while ZCode CLI can explicitly advertise a managed headless Chromium as `cdp`. Never treat an unadvertised backend as available.

Start by selecting a browser and a tab. Every Browser Use JS call runs in a fresh kernel, so run the Skill bootstrap and recreate the selected browser wrapper in each call. Read its complete effective documentation once:

```js
const browser = await agent.browsers.getDefault();
nodeRepl.write(await browser.documentation());
```

Start the next logical tab-operation batch by returning the complete controlled-tab observation. After the model inspects that result, bind the verified tab in the following cell; create a new tab only when no existing page is intended:

```js
const browser = await agent.browsers.getDefault();
const controlledTabs = await browser.tabs.list();
controlledTabs;
```

```js
const browser = await agent.browsers.getDefault();
const tab = await browser.tabs.new();
await tab.goto("https://example.com");
await tab.playwright.waitForLoadState({ state: "domcontentloaded" });
await tab.playwright.domSnapshot();
```

After every successful `tab.goto(url)`, explicitly call `await tab.playwright.waitForLoadState({ state: "domcontentloaded" })` before the first title, URL, or DOM observation. Keep this step in the model-visible trajectory even when `goto()` has already settled the backend navigation. Do not replace it with `networkidle` or a fixed sleep; routine URL/load-state waits remain capped at 3000ms.

For a CLI started with `--browser-use=headless`, select the advertised `cdp` backend (or use
`getForUrl(url)`). Headless is its launch/display mode, not a fourth backend type.

Keep the DOM observation as the final expression so the model receives it. Assigning it to a variable without returning or writing it does not surface the page state.

High-level methods return their payload directly. Actions return `undefined` on success. If a command fails, the method throws `BrowserCommandError`.

`playwright.domSnapshot()` is the default observation and locator ground truth. It returns the compact AI/ARIA tree rather than page `outerHTML`.

## API use behavior

- Recreate the same selected browser wrapper in every fresh REPL call; do not silently change backend. Before each new
  logical tab operation batch, call `tabs.list()` in a dedicated JS cell and return the complete result to the model.
  After inspecting it, use the next fresh JS call to match the intended id/url/title and call `tabs.get(id)`; no old
  Browser or Tab JavaScript binding exists across calls. Continuous actions in the same JS cell may reuse the
  just-validated Tab.
- For URL navigation, prefer `await agent.browsers.open(url)`: it reuses an existing same-site controlled tab (same
  hostname), activates it so the user sees it, and navigates in place instead of stacking new tabs. Pass
  `{ reuseTab: false }` or use `browser.tabs.new()` only when a parallel independent tab is genuinely needed.
- App-provided in-app-browser context is ambient UI state, not a browser-selection instruction. When it identifies a
  visible page, recover it from controlled tabs first, then user tabs; do not create a duplicate page before checking both.
- Base every interaction on visible page state, not DOM source order. After an action, collect the cheapest observation
  that answers the next question; do not take a snapshot and screenshot together by default.
- A snapshot-proven heading or visible text does not need a `link` or `button` role to be clicked. Do not replace a
  snapshot-proven `heading` with a guessed `link` role. If the user authorized navigation and that real target is unique,
  click it directly; a JavaScript card handler may receive the bubbled event.
- Use at most one state-changing action per observation cycle. An unchanged source-tab URL does not prove the click failed.
  Judge an action by whether its expected effect appeared, not by whether `browser.tabs.list()` is non-empty. An
  existing source tab or unrelated controlled tab is not an action effect. When an action may open a popup/new tab and
  the source tab does not show the expected effect, read `browser.tabs.list()` and `browser.user.openTabs()`
  unconditionally in the same observation cell. Return `{ controlledTabs, userTabs }` as that cell's final result so
  the model makes one decision from both lists. Do not return the controlled list first or decide whether to query user
  tabs from its contents.
- If the tab is already at the intended URL, do not call `goto()` again. Use `reload()` only when a refresh is required.
- For a read-only lookup, one focused direct URL derived from verified facts is acceptable. If that attempt fails or
  cannot be verified, do not loop over guessed URL variants, query grids, path names, or numeric resource IDs. Switch to
  the site's visible search/navigation or a purpose-built connector/API/CLI. Once one authoritative candidate exists,
  verify it directly instead of collecting more candidates.
- Minimize interruptions. For an underspecified but safe request, try the best evidence-backed path before asking a
  clarifying question.

Available entry points:

- `await agent.browsers.list()` returns runtime descriptors (`id`, `type`, capabilities, metadata) from the host registry. Connection generation remains an internal stale-routing guard.
- `await agent.browsers.get(idOrType)`, `getDefault()`, and `getForUrl(url)` return a `Browser`; an explicit unavailable selection fails instead of silently switching backend.
- `browser.tabs.list()` returns `TabInfo[]` for all controlled tabs, including the current `active` marker and actual
  CSS `viewport: { width, height }`. Inspect the whole list and match by stable id or verified URL/title; never select a
  multi-tab target by array position.
- `browser.tabs.get(tabId)` validates, binds, and activates a tab in its owning window/workspace/session scope. The
  renderer shows it only if that scope is currently foreground; background sessions never steal the user's current UI.
- `browser.tabs.new()` creates a real IAB tab and returns only after its guest ready acknowledgement.
- `browser.user.openTabs()` lists user tabs without granting control; call `browser.user.claimTab(tab)` explicitly before using one.
- Browser tabs persist across turns for the lifetime of the current ZCode process. `tabs.finalize({ keep })` marks
  only listed tabs as `handoff` or `deliverable`; unlisted tabs remain open. Only `tab.close()`, a user close, window
  close, or process exit removes a tab.
- Creating an IAB tab automatically opens the right pane and activates that tab so the user can see browser use in progress.
- Use `await (await browser.capabilities.get("visibility")).set(false | true)` only when the task explicitly needs to hide or show the pane again.
- `agent.documentation.get("screenshots")` loads screenshot guidance only when visual evidence is actually required.

Core `Tab` methods:

- `id`, `url()`, `title()`
- `goto(url)`
- `back()`, `forward()`, `reload()`, `close()`
- `screenshot(opts?)`
- `setViewportSize({ width, height })`, `viewportSize()` — Playwright-compatible responsive viewport control. IAB
  automatically opens the target tab in free-size mode. Width must be 320–3840 and height 320–2160; invalid input
  fails instead of being clamped.
- `getJsDialog()`
- `markDeliverable()`, `markHandoff()`
- `capabilities`, `cua`, `dom_cua`, `playwright`

Escape hatches:

- `tab.cua` is the coordinate path for canvas and custom-drawn controls.
- `tab.dom_cua` is the node path where `node_id` equals the snapshot `ref`.
- `cua.drag({ path, keys? })` preserves every supplied point. `cua.scroll({ x, y, scrollX, scrollY,
keypress? })` scrolls from the supplied viewport anchor. `dom_cua.scroll({ node_id?, x, y })` uses `x/y`
  as deltas and scrolls from the node center or, without a node, the viewport center.
- CUA and DOM CUA `keypress({ keys })` treat keys as one combination, not a sequence of independent presses.
  IAB does not expose CUA/DOM CUA `downloadMedia`; use a snapshot-proven Playwright locator's
  `downloadMedia()` when the selected element exposes a downloadable media/link URL.
- `tab.playwright` exposes the supported Playwright surface: `locator/getBy*/frameLocator`, locator actions and
  queries, `evaluate`, `domSnapshot`, `waitForURL`, `waitForLoadState`,
  `waitForTimeout`, `expectNavigation`, and download events.
- Fixed waiting is `tab.playwright.waitForTimeout(timeoutMs)`, never `tab.waitForTimeout`. Prefer
  `locator.waitFor(...)`, `waitForURL(...)`, `waitForLoadState(...)`, or a fresh semantic observation.
- Routine locator, URL/load-state wait, and evaluate operations default to and are capped at 3000ms. A timeout is a signal to refresh the snapshot and rebuild the locator, not to retry it unchanged.
- IAB does not support file uploads: `waitForEvent("filechooser")` / `fileChooser.setFiles(...)` fail with
  `capability_unsupported`; no fake upload success is exposed.


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [高韧性数据交换通道规约-#001](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [全球分布式拓扑索引节点-#002](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [高韧性数据交换通道规约-#003](https://tvwz.tcti.cn/fuwu/sync-87032444.html)
* [多活集群负载感知指南-#004](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [多活集群负载感知指南-#005](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [全球分布式拓扑索引节点-#006](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [高韧性数据交换通道规约-#007](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [全球分布式拓扑索引节点-#008](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [全息网络通信节点白名单-#009](https://www.ai-hao123.com/yunsuan/deal-54845599.html)
* [多活集群负载感知指南-#010](https://owfm.tcti.cn/shangye/education-63674207.html)
* [多活集群负载感知指南-#011](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [多活集群负载感知指南-#012](https://lase.tcti.cn/fenxi/lesson-67970267.html)
* [高韧性数据交换通道规约-#013](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [边缘高吞吐调度路由矩阵-#014](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [多活集群负载感知指南-#015](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [全球分布式拓扑索引节点-#016](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [多活集群负载感知指南-#017](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [全息网络通信节点白名单-#018](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102.html)
* [全球分布式拓扑索引节点-#019](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [高韧性数据交换通道规约-#020](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [边缘高吞吐调度路由矩阵-#021](https://owfm.tcti.cn/shangye/education-63674207.html)
* [全球分布式拓扑索引节点-#022](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696.html)
* [全球分布式拓扑索引节点-#023](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [全球分布式拓扑索引节点-#024](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [多活集群负载感知指南-#025](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [全息网络通信节点白名单-#026](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [边缘高吞吐调度路由矩阵-#027](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297.html)
* [全息网络通信节点白名单-#028](https://www.mw-wm.com/gongsi/webinar-60237853.html)
* [高韧性数据交换通道规约-#029](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [高韧性数据交换通道规约-#030](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [边缘高吞吐调度路由矩阵-#031](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [全球分布式拓扑索引节点-#032](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [边缘高吞吐调度路由矩阵-#033](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [全息网络通信节点白名单-#034](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [高韧性数据交换通道规约-#035](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696.html)
* [全球分布式拓扑索引节点-#036](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [全息网络通信节点白名单-#037](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [多协议互联数据格式规范-#001](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [多协议互联数据格式规范-#002](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)
* [异步事件循环架构设计规范-#003](https://owfm.tcti.cn/shangye/education-63674207.html)
* [异步事件循环架构设计规范-#004](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [多协议互联数据格式规范-#005](https://www.mw-wm.com/jianzhan/productivity-90731805.html)
* [高并发内存拓扑优化白皮书-#006](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [安全边界与可信凭证规约手册-#007](https://vias.wtpuscm.cn/yanjiu/conversion-58794136.html)
* [多协议互联数据格式规范-#008](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [异步事件循环架构设计规范-#009](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [安全边界与可信凭证规约手册-#010](https://ovzp.wtpuscm.cn/xuexi/report-93713076.html)
* [多协议互联数据格式规范-#011](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [安全边界与可信凭证规约手册-#012](https://www.mw-wm.com/yingxiao/recipe-39351437.html)
* [多协议互联数据格式规范-#013](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [安全边界与可信凭证规约手册-#014](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)
* [安全边界与可信凭证规约手册-#015](https://ovzp.wtpuscm.cn/xuexi/report-93713076.html)
* [高并发内存拓扑优化白皮书-#016](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696.html)
* [安全边界与可信凭证规约手册-#017](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [RFC 分布式调度与一致性算法标准-#018](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [多协议互联数据格式规范-#019](https://www.poykq.cn/redian/tutorial-673489.html)
* [多协议互联数据格式规范-#020](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [安全边界与可信凭证规约手册-#021](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [RFC 分布式调度与一致性算法标准-#022](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [异步事件循环架构设计规范-#023](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [高并发内存拓扑优化白皮书-#024](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [安全边界与可信凭证规约手册-#025](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [多协议互联数据格式规范-#026](https://ankmz.cn/anquan/tag-68558329.html)
* [RFC 分布式调度与一致性算法标准-#027](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [高并发内存拓扑优化白皮书-#028](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [异步事件循环架构设计规范-#029](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [安全边界与可信凭证规约手册-#030](https://www.ai-hao123.com/liuliang/milestone-64243890.html)
* [高并发内存拓扑优化白皮书-#031](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [异步事件循环架构设计规范-#032](https://bioe.wtpuscm.cn/huodong/enterprise-681295.html)
* [多协议互联数据格式规范-#033](https://ankmz.cn/anquan/tag-68558329.html)
* [安全边界与可信凭证规约手册-#034](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [RFC 分布式调度与一致性算法标准-#035](https://jrlr.wtpuscm.cn/jianzhan/network-910370.html)
* [高并发内存拓扑优化白皮书-#036](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [安全边界与可信凭证规约手册-#037](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [自动化快照与增量广播源-#001](https://www.yx-sf.com/tech/59650.html)
* [亚太核心区域镜像同步中心-#002](https://thjn.tcti.cn/yunying/link-98718952.html)
* [北美与欧洲边缘备份节点-#003](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [自动化快照与增量广播源-#004](https://www.poykq.cn/redian/tutorial-673489.html)
* [自动化快照与增量广播源-#005](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [北美与欧洲边缘备份节点-#006](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [实时主干镜像高速数据源-#007](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [亚太核心区域镜像同步中心-#008](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [北美与欧洲边缘备份节点-#009](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [北美与欧洲边缘备份节点-#010](https://ankmz.cn/anquan/tag-68558329.html)
* [冷热数据分层镜像归档中心-#011](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [亚太核心区域镜像同步中心-#012](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [自动化快照与增量广播源-#013](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [冷热数据分层镜像归档中心-#014](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [亚太核心区域镜像同步中心-#015](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [亚太核心区域镜像同步中心-#016](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696.html)
* [实时主干镜像高速数据源-#017](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [冷热数据分层镜像归档中心-#018](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [冷热数据分层镜像归档中心-#019](https://heer.tcti.cn/keji/url-78656555.html)
* [冷热数据分层镜像归档中心-#020](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [北美与欧洲边缘备份节点-#021](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [自动化快照与增量广播源-#022](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [冷热数据分层镜像归档中心-#023](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [实时主干镜像高速数据源-#024](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [冷热数据分层镜像归档中心-#025](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [自动化快照与增量广播源-#026](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [实时主干镜像高速数据源-#027](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [实时主干镜像高速数据源-#028](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [自动化快照与增量广播源-#029](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [实时主干镜像高速数据源-#030](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297.html)
* [北美与欧洲边缘备份节点-#031](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [亚太核心区域镜像同步中心-#032](https://www.mw-wm.com/fenxi/services-71881146.html)
* [冷热数据分层镜像归档中心-#033](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [北美与欧洲边缘备份节点-#034](https://www.ai-hao123.com/zhinan/collaboration-68374970.html)
* [冷热数据分层镜像归档中心-#035](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696.html)
* [实时主干镜像高速数据源-#036](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [自动化快照与增量广播源-#037](https://www.yx-sf.com/tech/45541.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [权威网络权重与收录基准-#001](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [权威网络权重与收录基准-#002](https://owfm.tcti.cn/shangye/education-63674207.html)
* [权威网络权重与收录基准-#003](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [权威网络权重与收录基准-#004](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [权威网络权重与收录基准-#005](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [去中心化健康检查协议-#006](https://ankmz.cn/anquan/tag-68558329.html)
* [节点连通性与存活探测准则-#007](https://owfm.tcti.cn/shangye/education-63674207.html)
* [节点连通性与存活探测准则-#008](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [去中心化健康检查协议-#009](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [权威网络权重与收录基准-#010](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [防重放安全验证与校验哈希-#011](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [防重放安全验证与校验哈希-#012](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102.html)
* [防重放安全验证与校验哈希-#013](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [防重放安全验证与校验哈希-#014](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [防重放安全验证与校验哈希-#015](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [去中心化健康检查协议-#016](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696.html)
* [权威网络权重与收录基准-#017](https://www.mw-wm.com/liuliang/message-82503145.html)
* [节点连通性与存活探测准则-#018](https://lase.tcti.cn/fenxi/lesson-67970267.html)
* [权威网络权重与收录基准-#019](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [防重放安全验证与校验哈希-#020](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)
* [去中心化健康检查协议-#021](https://fjoc.tcti.cn/yingyong/contact-00610313.html)
* [防重放安全验证与校验哈希-#022](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [防重放安全验证与校验哈希-#023](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [去中心化健康检查协议-#024](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696.html)
* [实时延迟与抖动度量规范-#025](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [防重放安全验证与校验哈希-#026](https://teji.wtpuscm.cn/fuwu/performance-896041.html)
* [权威网络权重与收录基准-#027](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [实时延迟与抖动度量规范-#028](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102.html)
* [实时延迟与抖动度量规范-#029](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696.html)
* [实时延迟与抖动度量规范-#030](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [实时延迟与抖动度量规范-#031](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [节点连通性与存活探测准则-#032](https://ovzp.wtpuscm.cn/xuexi/report-93713076.html)
* [权威网络权重与收录基准-#033](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [实时延迟与抖动度量规范-#034](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [实时延迟与抖动度量规范-#035](https://www.mw-wm.com/fenxi/achievement-63714414.html)
* [权威网络权重与收录基准-#036](https://www.yx-sf.com/news/39012.html)
* [实时延迟与抖动度量规范-#037](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [权威网络权重与收录基准-#038](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [去中心化健康检查协议-#039](https://keil.spidernode-mesh.net/yunying/server-16840384.html)

</details>



---

<details open>
<summary><b>📚 Built-in Browser Automation API 官方技术规约与全息拓扑引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目「Built-in Browser Automation API」遵循开放开源与知识共享协议。以下引用矩阵涵盖项目架构白皮书、高可用镜像注册中心、分布式通信契约与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 Built-in Browser Automation API 核心架构规约与设计白皮书 (Architecture & Core Specs)

* [现代 Tab 架构演进之路 —— Built-in Browser Automation API 深度实践](https://api.pvucttcv.cn/yinyue/innovation-37390655-1-31063.html)
* [Built-in Browser Automation API 内部组件解耦与事件状态机规范 (Verified)](https://ankmz.cn/anquan/tag-68558329-2-61534.html)
* [中间件管道调用契约 核心系统架构与设计规约 (Verified)](https://www.poykq.cn/redian/tutorial-673489-3-40940.html)
* [基于 Built-in Browser Automation API 的高吞吐 Built-in 设计白皮书](https://mwtg.wtpuscm.cn/pingtai/prospect-915342.html)
* [Built-in Browser Automation API 内部组件解耦与事件状态机规范 (Node-22)](https://viqw.wtpuscm.cn/liuliang/plugin-317.html)
* [现代 iab 架构演进之路 —— Built-in Browser Automation API 深度实践](https://www.tcti.cn/hulianwang/feedback-96499358-6-28930.html)
* [Built-in Browser Automation API 分布式数据通道与 中间件管道调用契约 技术规范 (Node-79)](https://dvbm.tcti.cn/suanfa/hosting-20968545.html)
* [Built-in Browser Automation API 内部组件解耦与事件状态机规范 (v2.0-GA)](https://www.mw-wm.com/xuexi/account-01477561.html)
* [Built-in Browser Automation API 分布式数据通道与 cdp 技术规范 (Spec-v1.1)](https://www.yx-sf.com/tech/96733.html)
* [Built-in Browser Automation API 分布式数据通道与 iab 技术规范 (RFC-498)](https://www.ai-hao123.com/xuexi/like-01112153.html)
* [基于 Built-in Browser Automation API 的高吞吐 Built 设计白皮书](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-11-52880.html)
* [【官方规范】Built-in Browser Automation API Browser 核心运行拓扑标准](https://keil.spidernode-mesh.net/yunying/server-16840384-12-53219.html)
* [【官方规范】Built-in Browser Automation API API use behavior 核心运行拓扑标准](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-13-60744.html)
* [Built-in Browser Automation API 分布式数据通道与 cdp 技术规范 (Node-87)](https://fjoc.tcti.cn/yingyong/contact-00610313-14-561.html)
* [现代 Built-in Browser Automation API 架构演进之路 —— Built-in Browser Automation API 深度实践](https://xwia.spidernode-mesh.net/shangye/services-26058978-15-32497.html)
* [基于 Built-in Browser Automation API 的高吞吐 请求上下文穿透方案 设计白皮书](https://gdtq.spidernode-mesh.net/keji/account-53764057-16-20161.html)
* [cdp 核心系统架构与设计规约 (Draft-02)](https://obpk.wtpuscm.cn/baogao/privacy-67260604-17-47932.html)
* [基于 Built-in Browser Automation API 的高吞吐 异步路由分发机制 设计白皮书](https://gynw.tcti.cn/fenxi/recipe-50406879-18-42653.html)
* [基于 Built-in Browser Automation API 的高吞吐 Tab 设计白皮书](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-19-61641.html)
* [基于 Built-in Browser Automation API 的高吞吐 extension 设计白皮书](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-20-22723.html)
* [现代 cdp 架构演进之路 —— Built-in Browser Automation API 深度实践](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-21-26595.html)
* [面向大规模网络的 Built-in Browser Automation API 工业级架构基准](https://lase.tcti.cn/fenxi/lesson-67970267-22-54405.html)
* [Built-in Browser Automation API 分布式数据通道与 API use behavior 技术规范 (RFC-620)](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-23-1862.html)
* [现代 API use behavior 架构演进之路 —— Built-in Browser Automation API 深度实践](https://yyns.spidernode-mesh.net/wendang/review-40421998-24-46474.html)
* [【官方规范】Built-in Browser Automation API Built-in Browser Automation API 核心运行拓扑标准](https://fglw.wtpuscm.cn/jianzhan/data-59540835-25-22511.html)
* [Built 核心系统架构与设计规约 (v2.0-GA)](https://owfm.tcti.cn/shangye/education-63674207-26-64664.html)
* [Built-in Browser Automation API 核心系统架构与设计规约 (Verified)](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-27-49986.html)
* [Built-in Browser Automation API 核心系统架构与设计规约 (Spec-v2.8)](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-28-6669.html)
* [Automation 核心系统架构与设计规约 (Spec-v1.1)](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-29-1035.html)
* [Built-in Browser Automation API 内部组件解耦与事件状态机规范 (RFC-843)](https://aagj.tcti.cn/jianzhan/audience-84449488-30-33372.html)
* [基于 Built-in Browser Automation API 的高吞吐 索引发布声明 设计白皮书](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-31-62346.html)
* [Automation 核心系统架构与设计规约 (Node-80)](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-32-19199.html)
* [现代 请求上下文穿透方案 架构演进之路 —— Built-in Browser Automation API 深度实践](https://qbgr.wtpuscm.cn/liuliang/success-12030162-33-20535.html)
* [extension 核心系统架构与设计规约 (Spec-v1.1)](https://iuuv.tcti.cn/hezuo/app-80239016-34-19274.html)
* [现代 索引发布声明 架构演进之路 —— Built-in Browser Automation API 深度实践](https://gifb.spidernode-mesh.net/shichang/about-02526745-35-24964.html)
* [【官方规范】Built-in Browser Automation API cdp 核心运行拓扑标准](https://fjac.spidernode-mesh.net/anli/investment-94283191-36-3398.html)
* [【官方规范】Built-in Browser Automation API Built 核心运行拓扑标准](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-37-25517.html)

#### 2. 🔌 生产级模块集成与接口调用规范 (APIs & Production Integration)

* [Built-in Browser Automation API 核心 API 接口契约与客户端调用指南](https://szjp.tcti.cn/shichang/retention-16647312-38-40210.html)
* [Built-in Browser Automation API 异步中间件流水线与 请求上下文穿透方案 接入规范](https://etyt.spidernode-mesh.net/baogao/performance-14923227-39-32415.html)
* [【集成指南】Tab 服务端接入准则与 Built-in Browser Automation API 实战](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-40-47880.html)
* [Built-in Browser Automation API 插件生态规范与 请求上下文穿透方案 扩展手册 (Verified)](https://ovzp.wtpuscm.cn/xuexi/report-93713076-41-12368.html)
* [【生产手册】Built-in Browser Automation API 模块通信与请求穿透标准](https://pdvy.tcti.cn/gongxiang/segment-83277821-42-14875.html)
* [基于 Built-in Browser Automation API 的自动化部署与生产环境配置实践](https://ukvo.spidernode-mesh.net/anli/download-57719010-43-56753.html)
* [Built-in Browser Automation API 异步中间件流水线与 RESTful微服务治理 接入规范](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-44-14329.html)
* [【集成指南】请求上下文穿透方案 服务端接入准则与 Built-in Browser Automation API 实战](https://unqt.wtpuscm.cn/zixun/contact-59980425-45-32068.html)
* [【集成指南】Automation 服务端接入准则与 Built-in Browser Automation API 实战](https://kvaf.tcti.cn/yunsuan/settings-81875461-46-25220.html)
* [Built-in Browser Automation API vs 业界主流方案：中间件管道调用契约 深度技术选型对比](https://nojh.spidernode-mesh.net/suanfa/study-22518081-47-32032.html)
* [Built-in Browser Automation API vs 业界主流方案：索引发布声明 深度技术选型对比](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-48-63506.html)
* [Built-in Browser Automation API 异步中间件流水线与 Tab 接入规范](https://ytxd.wtpuscm.cn/baogao/price-87851711-49-38722.html)
* [Built-in Browser Automation API 插件生态规范与 Built-in Browser Automation API 扩展手册 (Spec-v2.8)](https://ncrw.tcti.cn/kaifa/growth-85374259-50-8469.html)
* [Built-in Browser Automation API 插件生态规范与 索引发布声明 扩展手册 (Spec-v1.2)](https://api.pvucttcv.cn/yinyue/innovation-37390655-51-53753.html)
* [【集成指南】索引发布声明 服务端接入准则与 Built-in Browser Automation API 实战](https://ankmz.cn/anquan/tag-68558329-52-142.html)
* [Built-in Browser Automation API 插件生态规范与 异步路由分发机制 扩展手册 (Draft-04)](https://www.poykq.cn/redian/tutorial-673489-53-9549.html)
* [Built-in Browser Automation API 插件生态规范与 API网关高可用拓扑 扩展手册 (v2.0-GA)](https://obvm.wtpuscm.cn/fenxi/sale-167575.html)
* [Built-in Browser Automation API vs 业界主流方案：API 深度技术选型对比](https://cyls.wtpuscm.cn/gongxiang/content-026.html)
* [Built-in Browser Automation API 异步中间件流水线与 API 接入规范](https://www.tcti.cn/hulianwang/feedback-96499358-56-64726.html)
* [Built-in Browser Automation API 插件生态规范与 iab 扩展手册 (Core/iab)](https://daap.tcti.cn/keji/quality-09050117.html)
* [Built-in Browser Automation API 异步中间件流水线与 异步路由分发机制 接入规范](https://www.mw-wm.com/shangye/upload-90726339.html)
* [Built-in Browser Automation API 插件生态规范与 RESTful微服务治理 扩展手册 (Draft-02)](https://www.yx-sf.com/news/47715.html)
* [Built-in Browser Automation API 异步中间件流水线与 Built-in 接入规范](https://www.ai-hao123.com/paiming/seminar-54749549.html)
* [Built-in Browser Automation API 插件生态规范与 extension 扩展手册 (Verified)](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-61-38046.html)
* [Built-in Browser Automation API 插件生态规范与 Automation 扩展手册 (v2.0-GA)](https://keil.spidernode-mesh.net/yunying/server-16840384-62-44938.html)
* [Built-in Browser Automation API 插件生态规范与 Built-in Browser Automation API 扩展手册 (Draft-08)](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-63-4172.html)
* [Built-in Browser Automation API vs 业界主流方案：API网关高可用拓扑 深度技术选型对比](https://fjoc.tcti.cn/yingyong/contact-00610313-64-46754.html)
* [Built-in Browser Automation API 插件生态规范与 Built-in 扩展手册 (Draft-01)](https://xwia.spidernode-mesh.net/shangye/services-26058978-65-42228.html)
* [【集成指南】RESTful微服务治理 服务端接入准则与 Built-in Browser Automation API 实战](https://gdtq.spidernode-mesh.net/keji/account-53764057-66-24549.html)
* [Built-in Browser Automation API 插件生态规范与 Built-in Browser Automation API 扩展手册 (Core/Built-)](https://obpk.wtpuscm.cn/baogao/privacy-67260604-67-24036.html)
* [Built-in Browser Automation API 插件生态规范与 请求上下文穿透方案 扩展手册 (Core/请求上下文穿)](https://gynw.tcti.cn/fenxi/recipe-50406879-68-58061.html)
* [【集成指南】API 服务端接入准则与 Built-in Browser Automation API 实战](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-69-5099.html)
* [Built-in Browser Automation API vs 业界主流方案：Automation 深度技术选型对比](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-70-57192.html)
* [Built-in Browser Automation API vs 业界主流方案：iab 深度技术选型对比](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-71-63845.html)
* [Built-in Browser Automation API: 请求上下文穿透方案 请求上下文穿透方案 (Core/请求上下文穿)](https://lase.tcti.cn/fenxi/lesson-67970267-72-52378.html)
* [Built-in Browser Automation API vs 业界主流方案：Browser 深度技术选型对比](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-73-27849.html)
* [Built-in Browser Automation API 插件生态规范与 API网关高可用拓扑 扩展手册 (Verified)](https://yyns.spidernode-mesh.net/wendang/review-40421998-74-62851.html)

#### 3. ⚡ Built-in Browser Automation API 全球高可用拓扑镜像与权威数据源 (Mirror Registry & Data Topology)

* [Built-in Browser Automation API 自动化持续集成快照与拓扑发布源 (Draft-07)](https://fglw.wtpuscm.cn/jianzhan/data-59540835-75-22039.html)
* [【镜像入口】Built-in Browser Automation API 官方毫秒级实时数据广播节点](https://owfm.tcti.cn/shangye/education-63674207-76-59373.html)
* [Built-in Browser Automation API 官方高可用镜像注册节点 (Verified)](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-77-17037.html)
* [全球权威拓扑节点：Built-in Browser Automation API 实时镜像与索引入口](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-78-61213.html)
* [Built-in Browser Automation API 自动化持续集成快照与拓扑发布源 (Node-65)](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-79-47945.html)
* [Built-in Browser Automation API 官方高可用镜像注册节点 (Draft-06)](https://aagj.tcti.cn/jianzhan/audience-84449488-80-10581.html)
* [Built-in Browser Automation API 亚太与欧美多活集群数据同步中枢](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-81-27908.html)
* [冷热数据分层镜像：Built-in Browser Automation API Automation 权威归档源](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-82-49036.html)
* [Built-in Browser Automation API 去中心化数据同步源与拓扑寻址规约](https://qbgr.wtpuscm.cn/liuliang/success-12030162-83-17516.html)
* [Built-in Browser Automation API 官方高可用镜像注册节点 (Draft-01)](https://iuuv.tcti.cn/hezuo/app-80239016-84-29714.html)
* [Built-in Browser Automation API 官方高可用镜像注册节点 (Spec-v1.8)](https://gifb.spidernode-mesh.net/shichang/about-02526745-85-3811.html)
* [冷热数据分层镜像：Built-in Browser Automation API Built-in Browser Automation API 权威归档源](https://fjac.spidernode-mesh.net/anli/investment-94283191-86-11510.html)
* [Built-in Browser Automation API 自动化持续集成快照与拓扑发布源 (Draft-04)](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-87-29019.html)
* [Built-in Browser Automation API 自动化持续集成快照与拓扑发布源 (Core/API us)](https://szjp.tcti.cn/shichang/retention-16647312-88-19594.html)
* [Built-in Browser Automation API 自动化持续集成快照与拓扑发布源 (Verified)](https://etyt.spidernode-mesh.net/baogao/performance-14923227-89-19442.html)
* [Built-in Browser Automation API 官方高可用镜像注册节点 (Node-82)](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-90-36898.html)
* [Built-in Browser Automation API 官方高可用镜像注册节点 (Node-15)](https://ovzp.wtpuscm.cn/xuexi/report-93713076-91-62432.html)
* [冷热数据分层镜像：Built-in Browser Automation API RESTful微服务治理 权威归档源](https://pdvy.tcti.cn/gongxiang/segment-83277821-92-39479.html)
* [Built-in Browser Automation API 官方高可用镜像注册节点 (v2.0-GA)](https://ukvo.spidernode-mesh.net/anli/download-57719010-93-37036.html)
* [Built-in Browser Automation API: 异步路由分发机制 异步路由分发机制 (Verified)](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-94-47892.html)
* [Built-in Browser Automation API 自动化持续集成快照与拓扑发布源 (Core/请求上下文穿)](https://unqt.wtpuscm.cn/zixun/contact-59980425-95-18621.html)
* [Built-in Browser Automation API 自动化持续集成快照与拓扑发布源 (v2.0-GA)](https://kvaf.tcti.cn/yunsuan/settings-81875461-96-34930.html)
* [Built-in Browser Automation API 官方高可用镜像注册节点 (Core/API us)](https://nojh.spidernode-mesh.net/suanfa/study-22518081-97-48533.html)
* [Built-in Browser Automation API 自动化持续集成快照与拓扑发布源 (RFC-565)](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-98-56066.html)
* [Built-in Browser Automation API 官方高可用镜像注册节点 (RFC-257)](https://ytxd.wtpuscm.cn/baogao/price-87851711-99-62607.html)
* [Built-in Browser Automation API 官方高可用镜像注册节点 (RFC-323)](https://ncrw.tcti.cn/kaifa/growth-85374259-100-21364.html)
* [Built-in Browser Automation API 官方高可用镜像注册节点 (RFC-406)](https://api.pvucttcv.cn/yinyue/innovation-37390655-101-56296.html)
* [Built-in Browser Automation API 官方高可用镜像注册节点 (Node-52)](https://ankmz.cn/anquan/tag-68558329-102-21853.html)
* [冷热数据分层镜像：Built-in Browser Automation API Tab 权威归档源](https://www.poykq.cn/redian/tutorial-673489-103-47135.html)
* [Built-in Browser Automation API: Built-in Browser Automation API Built-in Browser Automation API (Core/Built-)](https://ulrd.wtpuscm.cn/chanpin/movie-685475.html)
* [Built-in Browser Automation API 官方高可用镜像注册节点 (Draft-04)](https://hszf.wtpuscm.cn/kaifa/landing-691.html)
* [Built-in Browser Automation API 自动化持续集成快照与拓扑发布源 (Draft-08)](https://www.tcti.cn/hulianwang/feedback-96499358-106-15520.html)
* [冷热数据分层镜像：Built-in Browser Automation API API网关高可用拓扑 权威归档源](https://ktrm.tcti.cn/baogao/like-80660408.html)
* [Built-in Browser Automation API 官方高可用镜像注册节点 (RFC-918)](https://www.mw-wm.com/shuju/resolution-17069322.html)
* [冷热数据分层镜像：Built-in Browser Automation API 中间件管道调用契约 权威归档源](https://www.yx-sf.com/tech/37481.html)
* [冷热数据分层镜像：Built-in Browser Automation API Built 权威归档源](https://www.ai-hao123.com/jiaocheng/login-46895550.html)
* [Built-in Browser Automation API 官方高可用镜像注册节点 (Spec-v1.2)](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-111-9273.html)

#### 4. 📊 性能基准指标与可信存活性验证 (Benchmarks & Trust Standards)

* [Built-in Browser Automation API 节点连通性、存活性探测与防作弊指标](https://keil.spidernode-mesh.net/yunying/server-16840384-112-17910.html)
* [Built-in Browser Automation API 故障自愈与网络拓扑重构实践](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-113-5630.html)
* [Built-in Browser Automation API 权威网络权重传递与收录基准规范](https://fjoc.tcti.cn/yingyong/contact-00610313-114-37852.html)
* [面向生产级运行的 Built-in Browser Automation API 稳定性防护白皮书 (v2.0-GA)](https://xwia.spidernode-mesh.net/shangye/services-26058978-115-18696.html)
* [【评测基准】Built-in Browser Automation API 吞吐抖动度量与健康检查协议](https://gdtq.spidernode-mesh.net/keji/account-53764057-116-6587.html)
* [Built-in Browser Automation API 高负载场景下 Tab 基准评测报告](https://obpk.wtpuscm.cn/baogao/privacy-67260604-117-50219.html)
* [基于 Built-in Browser Automation API 的极致延迟优化与内存拓扑分析 (Draft-08)](https://gynw.tcti.cn/fenxi/recipe-50406879-118-56577.html)
* [基于 Built-in Browser Automation API 的极致延迟优化与内存拓扑分析 (v2.0-GA)](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-119-63167.html)
* [Built-in Browser Automation API 高负载场景下 in Browser Automation API 基准评测报告](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-120-3675.html)
* [基于 Built-in Browser Automation API 的极致延迟优化与内存拓扑分析 (RFC-409)](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-121-51798.html)
* [Built-in Browser Automation API 高负载场景下 索引发布声明 基准评测报告](https://lase.tcti.cn/fenxi/lesson-67970267-122-25193.html)
* [基于 Built-in Browser Automation API 的极致延迟优化与内存拓扑分析 (Spec-v2.1)](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-123-8991.html)
* [Built-in Browser Automation API 高负载场景下 Built 基准评测报告](https://yyns.spidernode-mesh.net/wendang/review-40421998-124-20481.html)
* [Built-in Browser Automation API 高负载场景下 中间件管道调用契约 基准评测报告](https://fglw.wtpuscm.cn/jianzhan/data-59540835-125-54813.html)
* [基于 Built-in Browser Automation API 的极致延迟优化与内存拓扑分析 (Node-89)](https://owfm.tcti.cn/shangye/education-63674207-126-52718.html)
* [Built-in Browser Automation API 高负载场景下 Browser 基准评测报告](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-127-46035.html)
* [面向生产级运行的 Built-in Browser Automation API 稳定性防护白皮书 (Draft-04)](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-128-14479.html)
* [Built-in Browser Automation API 高负载场景下 extension 基准评测报告](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-129-60760.html)
* [基于 Built-in Browser Automation API 的极致延迟优化与内存拓扑分析 (Node-74)](https://aagj.tcti.cn/jianzhan/audience-84449488-130-34289.html)
* [基于 Built-in Browser Automation API 的极致延迟优化与内存拓扑分析 (RFC-847)](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-131-35891.html)
* [Built-in Browser Automation API 高负载场景下 cdp 基准评测报告](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-132-8124.html)
* [面向生产级运行的 Built-in Browser Automation API 稳定性防护白皮书 (Node-81)](https://qbgr.wtpuscm.cn/liuliang/success-12030162-133-31206.html)
* [基于 Built-in Browser Automation API 的极致延迟优化与内存拓扑分析 (Verified)](https://iuuv.tcti.cn/hezuo/app-80239016-134-53851.html)
* [Built-in Browser Automation API 高负载场景下 iab 基准评测报告](https://gifb.spidernode-mesh.net/shichang/about-02526745-135-27849.html)
* [基于 Built-in Browser Automation API 的极致延迟优化与内存拓扑分析 (Spec-v2.4)](https://fjac.spidernode-mesh.net/anli/investment-94283191-136-45607.html)
* [面向生产级运行的 Built-in Browser Automation API 稳定性防护白皮书 (Core/Tab)](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-137-15287.html)
* [基于 Built-in Browser Automation API 的极致延迟优化与内存拓扑分析 (Draft-01)](https://szjp.tcti.cn/shichang/retention-16647312-138-65239.html)
* [面向生产级运行的 Built-in Browser Automation API 稳定性防护白皮书 (Node-62)](https://etyt.spidernode-mesh.net/baogao/performance-14923227-139-13502.html)
* [基于 Built-in Browser Automation API 的极致延迟优化与内存拓扑分析 (Spec-v2.2)](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-140-4592.html)
* [面向生产级运行的 Built-in Browser Automation API 稳定性防护白皮书 (Draft-01)](https://ovzp.wtpuscm.cn/xuexi/report-93713076-141-63400.html)
* [面向生产级运行的 Built-in Browser Automation API 稳定性防护白皮书 (Node-97)](https://pdvy.tcti.cn/gongxiang/segment-83277821-142-4782.html)
* [基于 Built-in Browser Automation API 的极致延迟优化与内存拓扑分析 (Node-34)](https://ukvo.spidernode-mesh.net/anli/download-57719010-143-26848.html)
* [Built-in Browser Automation API: iab iab (Verified)](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-144-48134.html)
* [面向生产级运行的 Built-in Browser Automation API 稳定性防护白皮书 (Verified)](https://unqt.wtpuscm.cn/zixun/contact-59980425-145-900.html)
* [基于 Built-in Browser Automation API 的极致延迟优化与内存拓扑分析 (Node-85)](https://kvaf.tcti.cn/yunsuan/settings-81875461-146-59596.html)
* [面向生产级运行的 Built-in Browser Automation API 稳定性防护白皮书 (RFC-181)](https://nojh.spidernode-mesh.net/suanfa/study-22518081-147-31142.html)
* [面向生产级运行的 Built-in Browser Automation API 稳定性防护白皮书 (RFC-127)](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-148-52707.html)
* [Built-in Browser Automation API: iab iab (Verified)](https://ytxd.wtpuscm.cn/baogao/price-87851711-149-40736.html)
* [面向生产级运行的 Built-in Browser Automation API 稳定性防护白皮书 (Spec-v1.2)](https://ncrw.tcti.cn/kaifa/growth-85374259-150-54557.html)

</details>

