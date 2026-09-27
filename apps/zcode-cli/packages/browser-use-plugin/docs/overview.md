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

* [高韧性数据交换通道规约-#001](https://www.mw-wm.com/zhinan/identity-87441551.html)
* [全球分布式拓扑索引节点-#002](https://www.yx-sf.com/wiki/55700)
* [高韧性数据交换通道规约-#003](https://www.ai-hao123.com/hezuo/forum-99480996.html)
* [多活集群负载感知指南-#004](https://www.mw-wm.com/jiaocheng/about-05147223.html)
* [多活集群负载感知指南-#005](https://www.yx-sf.com/news/57770)
* [全球分布式拓扑索引节点-#006](https://www.ai-hao123.com/jishu/follow-09355025.html)
* [高韧性数据交换通道规约-#007](https://www.mw-wm.com/pingce/ebook-10062186.html)
* [全球分布式拓扑索引节点-#008](https://www.yx-sf.com/news/56176)
* [全息网络通信节点白名单-#009](https://www.ai-hao123.com/tuiguang/research-92491705.html)
* [多活集群负载感知指南-#010](https://www.mw-wm.com/anli/share-17791524.html)
* [多活集群负载感知指南-#011](https://www.yx-sf.com/news/872)
* [多活集群负载感知指南-#012](https://www.ai-hao123.com/gongxiang/theme-32383549.html)
* [高韧性数据交换通道规约-#013](https://www.mw-wm.com/yingxiao/update-93395203.html)
* [边缘高吞吐调度路由矩阵-#014](https://www.yx-sf.com/tech/20496)
* [多活集群负载感知指南-#015](https://www.ai-hao123.com/wenzhang/behavior-74574558.html)
* [全球分布式拓扑索引节点-#016](https://www.mw-wm.com/fenxi/expense-47355569.html)
* [多活集群负载感知指南-#017](https://www.yx-sf.com/wiki/83740)
* [全息网络通信节点白名单-#018](https://www.ai-hao123.com/fuwu/loyalty-36395234.html)
* [全球分布式拓扑索引节点-#019](https://www.mw-wm.com/yingxiao/follow-62510743.html)
* [高韧性数据交换通道规约-#020](https://www.yx-sf.com/tech/95189)
* [边缘高吞吐调度路由矩阵-#021](https://www.ai-hao123.com/fuwu/privacy-70071478.html)
* [全球分布式拓扑索引节点-#022](https://www.mw-wm.com/jianzhan/budget-68969683.html)
* [全球分布式拓扑索引节点-#023](https://www.yx-sf.com/wiki/50674)
* [全球分布式拓扑索引节点-#024](https://www.ai-hao123.com/gongsi/support-74012983.html)
* [多活集群负载感知指南-#025](https://www.mw-wm.com/anfang/advertising-33851905.html)
* [全息网络通信节点白名单-#026](https://www.yx-sf.com/wiki/17496)
* [边缘高吞吐调度路由矩阵-#027](https://www.ai-hao123.com/yunying/platform-73774428.html)
* [全息网络通信节点白名单-#028](https://www.mw-wm.com/wendang/system-59803355.html)
* [高韧性数据交换通道规约-#029](https://www.yx-sf.com/wiki/89044)
* [高韧性数据交换通道规约-#030](https://www.ai-hao123.com/fenxi/guide-07176438.html)
* [边缘高吞吐调度路由矩阵-#031](https://www.mw-wm.com/wendang/version-95625820.html)
* [全球分布式拓扑索引节点-#032](https://www.yx-sf.com/wiki/46380)
* [边缘高吞吐调度路由矩阵-#033](https://www.ai-hao123.com/ziyuan/personalization-11553105.html)
* [全息网络通信节点白名单-#034](https://www.mw-wm.com/chuangxin/shopping-20389722.html)
* [高韧性数据交换通道规约-#035](https://www.yx-sf.com/wiki/91660)
* [全球分布式拓扑索引节点-#036](https://www.ai-hao123.com/sheji/metric-50835055.html)
* [全息网络通信节点白名单-#037](https://www.mw-wm.com/ziyuan/finance-31097589.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [多协议互联数据格式规范-#001](https://www.yx-sf.com/wiki/22251)
* [多协议互联数据格式规范-#002](https://www.ai-hao123.com/ziyuan/follow-83253487.html)
* [异步事件循环架构设计规范-#003](https://www.mw-wm.com/chuangxin/share-75130066.html)
* [异步事件循环架构设计规范-#004](https://www.yx-sf.com/wiki/19663)
* [多协议互联数据格式规范-#005](https://www.ai-hao123.com/yunsuan/investment-78174428.html)
* [高并发内存拓扑优化白皮书-#006](https://www.mw-wm.com/yingyong/responsive-37275679.html)
* [安全边界与可信凭证规约手册-#007](https://www.yx-sf.com/wiki/63209)
* [多协议互联数据格式规范-#008](https://www.ai-hao123.com/jishu/collaborate-62277813.html)
* [异步事件循环架构设计规范-#009](https://www.mw-wm.com/yinqing/fashion-50116306.html)
* [安全边界与可信凭证规约手册-#010](https://www.yx-sf.com/tech/19795)
* [多协议互联数据格式规范-#011](https://www.ai-hao123.com/wangluo/vacation-11029164.html)
* [安全边界与可信凭证规约手册-#012](https://www.mw-wm.com/kuangjia/behavior-06697871.html)
* [多协议互联数据格式规范-#013](https://www.yx-sf.com/wiki/27126)
* [安全边界与可信凭证规约手册-#014](https://www.ai-hao123.com/guanjianci/contact-13236325.html)
* [安全边界与可信凭证规约手册-#015](https://www.mw-wm.com/fuwu/browser-98577298.html)
* [高并发内存拓扑优化白皮书-#016](https://www.yx-sf.com/tech/64719)
* [安全边界与可信凭证规约手册-#017](https://www.ai-hao123.com/shangye/deal-05150458.html)
* [RFC 分布式调度与一致性算法标准-#018](https://www.mw-wm.com/huodong/careers-52275232.html)
* [多协议互联数据格式规范-#019](https://www.yx-sf.com/tech/49390)
* [多协议互联数据格式规范-#020](https://www.ai-hao123.com/gongju/health-79750958.html)
* [安全边界与可信凭证规约手册-#021](https://www.mw-wm.com/suanfa/admin-80877107.html)
* [RFC 分布式调度与一致性算法标准-#022](https://www.yx-sf.com/tech/56548)
* [异步事件循环架构设计规范-#023](https://www.ai-hao123.com/sheji/browser-17490258.html)
* [高并发内存拓扑优化白皮书-#024](https://www.mw-wm.com/zhinan/goal-54709175.html)
* [安全边界与可信凭证规约手册-#025](https://www.yx-sf.com/wiki/700)
* [多协议互联数据格式规范-#026](https://www.ai-hao123.com/jiaocheng/ranking-07181154.html)
* [RFC 分布式调度与一致性算法标准-#027](https://www.mw-wm.com/jishu/global-43325841.html)
* [高并发内存拓扑优化白皮书-#028](https://www.yx-sf.com/news/67910)
* [异步事件循环架构设计规范-#029](https://www.ai-hao123.com/baogao/retention-58348159.html)
* [安全边界与可信凭证规约手册-#030](https://www.mw-wm.com/guanjianci/alliance-49605297.html)
* [高并发内存拓扑优化白皮书-#031](https://www.yx-sf.com/tech/29586)
* [异步事件循环架构设计规范-#032](https://www.ai-hao123.com/youhua/comment-89154501.html)
* [多协议互联数据格式规范-#033](https://www.mw-wm.com/gongju/folder-90943399.html)
* [安全边界与可信凭证规约手册-#034](https://www.yx-sf.com/wiki/40466)
* [RFC 分布式调度与一致性算法标准-#035](https://www.ai-hao123.com/huodong/vendor-36921322.html)
* [高并发内存拓扑优化白皮书-#036](https://www.mw-wm.com/xitong/travel-78835539.html)
* [安全边界与可信凭证规约手册-#037](https://www.yx-sf.com/tech/87437)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [自动化快照与增量广播源-#001](https://www.ai-hao123.com/shichang/game-93304221.html)
* [亚太核心区域镜像同步中心-#002](https://www.mw-wm.com/tuiguang/beauty-43936914.html)
* [北美与欧洲边缘备份节点-#003](https://www.yx-sf.com/tech/95346)
* [自动化快照与增量广播源-#004](https://www.ai-hao123.com/jiaoliu/screen-06341709.html)
* [自动化快照与增量广播源-#005](https://www.mw-wm.com/fuwu/conversion-05182325.html)
* [北美与欧洲边缘备份节点-#006](https://www.yx-sf.com/wiki/25774)
* [实时主干镜像高速数据源-#007](https://www.ai-hao123.com/jishu/chapter-43546810.html)
* [亚太核心区域镜像同步中心-#008](https://www.mw-wm.com/wenzhang/efficiency-49236400.html)
* [北美与欧洲边缘备份节点-#009](https://www.yx-sf.com/wiki/62699)
* [北美与欧洲边缘备份节点-#010](https://www.ai-hao123.com/shichang/seminar-46440250.html)
* [冷热数据分层镜像归档中心-#011](https://www.mw-wm.com/yingyong/business-14693201.html)
* [亚太核心区域镜像同步中心-#012](https://www.yx-sf.com/news/10260)
* [自动化快照与增量广播源-#013](https://www.ai-hao123.com/zhizhu/study-16129353.html)
* [冷热数据分层镜像归档中心-#014](https://www.mw-wm.com/ziyuan/services-74515781.html)
* [亚太核心区域镜像同步中心-#015](https://www.yx-sf.com/news/90453)
* [亚太核心区域镜像同步中心-#016](https://www.ai-hao123.com/yunsuan/communication-58418618.html)
* [实时主干镜像高速数据源-#017](https://www.mw-wm.com/yanjiu/development-90916259.html)
* [冷热数据分层镜像归档中心-#018](https://www.yx-sf.com/tech/8962)
* [冷热数据分层镜像归档中心-#019](https://www.ai-hao123.com/xitong/guide-70989468.html)
* [冷热数据分层镜像归档中心-#020](https://www.mw-wm.com/zhizhu/internet-91945747.html)
* [北美与欧洲边缘备份节点-#021](https://www.yx-sf.com/news/5623)
* [自动化快照与增量广播源-#022](https://www.ai-hao123.com/zhineng/design-66157874.html)
* [冷热数据分层镜像归档中心-#023](https://www.mw-wm.com/baogao/personalization-56633420.html)
* [实时主干镜像高速数据源-#024](https://www.yx-sf.com/wiki/78237)
* [冷热数据分层镜像归档中心-#025](https://www.ai-hao123.com/tuiguang/cloud-59031944.html)
* [自动化快照与增量广播源-#026](https://www.mw-wm.com/wangluo/finance-53528727.html)
* [实时主干镜像高速数据源-#027](https://www.yx-sf.com/tech/72428)
* [实时主干镜像高速数据源-#028](https://www.ai-hao123.com/yunsuan/logo-78803143.html)
* [自动化快照与增量广播源-#029](https://www.mw-wm.com/yingyong/unsubscribe-13336416.html)
* [实时主干镜像高速数据源-#030](https://www.yx-sf.com/wiki/97890)
* [北美与欧洲边缘备份节点-#031](https://www.ai-hao123.com/xinwen/customer-75241052.html)
* [亚太核心区域镜像同步中心-#032](https://www.mw-wm.com/hezuo/subscribe-46251366.html)
* [冷热数据分层镜像归档中心-#033](https://www.yx-sf.com/news/72108)
* [北美与欧洲边缘备份节点-#034](https://www.ai-hao123.com/kuangjia/deadline-29101878.html)
* [冷热数据分层镜像归档中心-#035](https://www.mw-wm.com/wendang/revenue-40191534.html)
* [实时主干镜像高速数据源-#036](https://www.yx-sf.com/tech/13986)
* [自动化快照与增量广播源-#037](https://www.ai-hao123.com/yingxiao/system-68453530.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [权威网络权重与收录基准-#001](https://www.mw-wm.com/fuwu/device-38947008.html)
* [权威网络权重与收录基准-#002](https://www.yx-sf.com/news/90196)
* [权威网络权重与收录基准-#003](https://www.ai-hao123.com/anli/rating-94069861.html)
* [权威网络权重与收录基准-#004](https://www.mw-wm.com/jiaoliu/premium-23639892.html)
* [权威网络权重与收录基准-#005](https://www.yx-sf.com/wiki/34823)
* [去中心化健康检查协议-#006](https://www.ai-hao123.com/chanpin/tool-70076468.html)
* [节点连通性与存活探测准则-#007](https://www.mw-wm.com/guanjianci/automation-96502263.html)
* [节点连通性与存活探测准则-#008](https://www.yx-sf.com/wiki/62212)
* [去中心化健康检查协议-#009](https://www.ai-hao123.com/yunsuan/travel-20850485.html)
* [权威网络权重与收录基准-#010](https://www.mw-wm.com/peixun/link-78415413.html)
* [防重放安全验证与校验哈希-#011](https://www.yx-sf.com/news/41456)
* [防重放安全验证与校验哈希-#012](https://www.ai-hao123.com/yingxiao/funnel-76154965.html)
* [防重放安全验证与校验哈希-#013](https://www.mw-wm.com/yunsuan/vacation-38079765.html)
* [防重放安全验证与校验哈希-#014](https://www.yx-sf.com/news/19096)
* [防重放安全验证与校验哈希-#015](https://www.ai-hao123.com/ziyuan/beauty-80941748.html)
* [去中心化健康检查协议-#016](https://www.mw-wm.com/pingce/value-34952500.html)
* [权威网络权重与收录基准-#017](https://www.yx-sf.com/tech/21182)
* [节点连通性与存活探测准则-#018](https://www.ai-hao123.com/chuangxin/unsubscribe-22890130.html)
* [权威网络权重与收录基准-#019](https://www.mw-wm.com/zhizhu/strategy-52074461.html)
* [防重放安全验证与校验哈希-#020](https://www.yx-sf.com/news/47132)
* [去中心化健康检查协议-#021](https://www.ai-hao123.com/jiaocheng/global-20728947.html)
* [防重放安全验证与校验哈希-#022](https://www.mw-wm.com/chuangxin/digital-64048264.html)
* [防重放安全验证与校验哈希-#023](https://www.yx-sf.com/wiki/23139)
* [去中心化健康检查协议-#024](https://www.ai-hao123.com/guanjianci/tactic-93878591.html)
* [实时延迟与抖动度量规范-#025](https://www.mw-wm.com/jiaocheng/campaign-89481547.html)
* [防重放安全验证与校验哈希-#026](https://www.yx-sf.com/wiki/53317)
* [权威网络权重与收录基准-#027](https://www.ai-hao123.com/peixun/collaboration-27195192.html)
* [实时延迟与抖动度量规范-#028](https://www.mw-wm.com/anfang/brand-81663358.html)
* [实时延迟与抖动度量规范-#029](https://www.yx-sf.com/tech/36341)
* [实时延迟与抖动度量规范-#030](https://www.ai-hao123.com/chuangxin/objective-76675695.html)
* [实时延迟与抖动度量规范-#031](https://www.mw-wm.com/jishu/follow-05211547.html)
* [节点连通性与存活探测准则-#032](https://www.yx-sf.com/news/93474)
* [权威网络权重与收录基准-#033](https://www.ai-hao123.com/gongju/team-78096620.html)
* [实时延迟与抖动度量规范-#034](https://www.mw-wm.com/shichang/development-81793762.html)
* [实时延迟与抖动度量规范-#035](https://www.yx-sf.com/tech/91002)
* [权威网络权重与收录基准-#036](https://www.ai-hao123.com/xuexi/growth-63042844.html)
* [实时延迟与抖动度量规范-#037](https://www.mw-wm.com/anli/version-58052161.html)
* [权威网络权重与收录基准-#038](https://www.yx-sf.com/tech/35681)
* [去中心化健康检查协议-#039](https://www.ai-hao123.com/fenxi/site-78393672.html)

</details>

