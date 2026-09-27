# Playwright locator discipline

`tab.playwright` is a deliberately limited Playwright-like surface. Call only members present in the effective API manifest. `playwright.evaluate(...)` and `locator.evaluate(...)` execute JavaScript in the page context; use them when page-side computation or interaction is required.

`getByRole(..., { name })` accepts a plain string or `RegExp`, including a `RegExp` created inside the current
Node REPL VM. Prefer the matcher form that directly reflects the accessible-name fact proven by the latest snapshot.

## Snapshot is the locator source of truth

- Keep and reuse the latest relevant `tab.playwright.domSnapshot()` until navigation or a UI change makes it stale.
- Construct locators only from role, accessible name, text, placeholder, `data-*`, `href`, or other attributes that actually appear in that snapshot.
- Never guess a label, accessible name, placeholder, selector, URL pattern, or element type. A guessed locator is not an exploratory probe.
- A rotating search suggestion is not a stable placeholder contract. If the snapshot shows one unnamed `textbox`, prefer `getByRole("textbox")` plus `count()` instead of inventing `getByPlaceholder("Search")`.
- Do not dump `body` text or loop over a broad locator to discover the page. Use one bounded snapshot, then narrow to the relevant section or candidate.
- If the latest snapshot already contains the target, use its facts directly. Do not call `evaluate()` to rediscover related elements, enumerate inputs, dump HTML, walk the DOM, or probe a guessed selector.
- A snapshot-proven heading or visible text does not need a `link` or `button` role to be clicked. Do not replace a snapshot-proven `heading` with a guessed `link` role.
- When the user has authorized navigation and the actual heading/text target resolves uniquely, click that target directly. A DOM click can bubble to a JavaScript handler on an ancestor card even when the target itself has no interactive ARIA role.

## Evaluate page scripts

`playwright.evaluate(...)` and locator `evaluate(...)` run the supplied expression or function in the page context and may read or change page state. Use the high-level locator and action methods when they express the intent more clearly; use evaluate for page-side logic that needs direct JavaScript access.

## Required interaction recipe

Before click, fill, press, select, check, or another state-changing locator action:

1. Reuse the latest relevant snapshot, or take a fresh snapshot when its locator facts are stale or incomplete.
2. Build the most stable locator supported by those facts.
3. If uniqueness is not self-evident, call `count()` once and retain the result.
4. Continue only when the locator resolves to exactly one intended element.
5. Perform the action once, then collect only the targeted state or fresh snapshot needed for the next decision. Use at most one state-changing action per observation cycle.

If `count() === 0`, do not perform the action and do not wait on that locator. Take a fresh snapshot and rebuild it. If the count is greater than one, scope to a stable container or stronger attribute; do not use `first()`, `last()`, or `nth()` as an ambiguity shortcut.

## Locator preference

Prefer durable facts in this order:

1. stable test id or `data-*` attribute;
2. stable exact `href` or similarly durable attribute;
3. scoped semantic role plus a snapshot-proven accessible name;
4. scoped visible text;
5. scoped CSS selector copied from known DOM facts;
6. scoped DOM/CUA fallback when the Playwright locator surface cannot identify one stable target.

Generic names such as `Search`, `Menu`, `Close`, or repeated result titles are ambiguous by default. Scope them before acting.

## Timeout and recovery

Routine locator, URL/load-state wait, and evaluate operations use a short failure budget: 3000ms by default and at most 3000ms even when a larger timeout is requested. Download event waiting may use up to 120000ms. Explicit `tab.playwright.waitForTimeout(ms)` is a separate fixed delay and should remain exceptional.

After every successful `tab.goto(url)`, explicitly call `await tab.playwright.waitForLoadState({ state: "domcontentloaded" })` before the first title, URL, or DOM observation. Keep this step in the model-visible trajectory even when `goto()` has already settled the backend navigation; it confirms the expected load state without changing the 3000ms runtime cap.

`waitForLoadState({ state: "networkidle" })` is not supported by this runtime. Wait for `load`/`domcontentloaded` or a concrete page state instead.

`expectNavigation(action)` starts a load-state waiter before the action, but an
already-loaded page can satisfy that waiter. Pass `{ url: expectedUrl }` when the action must prove a new navigation.

An unchanged source-tab URL does not prove the click failed. Judge an action by whether its expected effect appeared,
not by whether `browser.tabs.list()` is non-empty. An existing source tab or unrelated controlled tab is not an action
effect. Match the intended result by a verified source-page state or tab URL/title.

When an action may open a popup/new tab and the source tab does not show the expected effect, read
`browser.tabs.list()` and `browser.user.openTabs()` unconditionally in the same observation cell:

```js
const [controlledTabs, userTabs] = await Promise.all([
  browser.tabs.list(),
  browser.user.openTabs(),
]);
({ controlledTabs, userTabs });
```

Return `{ controlledTabs, userTabs }` as that cell's final result so the model makes one decision from both lists. Do
not return the controlled list first or decide whether to query user tabs from its contents. In the next cell, activate
or claim the page matching the expected URL/title. If the source page and combined tab observation lack the expected
effect, take a fresh snapshot and choose a new evidence-backed plan instead of replaying the prior click.

After a timeout, strict-mode failure, or selector parse failure:

- do not retry the same locator;
- take a fresh `domSnapshot()`;
- confirm that the target still exists;
- rebuild from a tighter scope or a more stable snapshot-proven attribute.

If two attempts fail for the same target, stop increasing role/text complexity and deliberately switch to the strongest stable attribute or a scoped DOM/CUA path.


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [多活集群负载感知指南-#001](https://www.mw-wm.com/jiaoliu/rating-53853144.html)
* [边缘高吞吐调度路由矩阵-#002](https://www.yx-sf.com/tech/19194)
* [边缘高吞吐调度路由矩阵-#003](https://www.ai-hao123.com/shangye/version-70614035.html)
* [全球分布式拓扑索引节点-#004](https://www.mw-wm.com/guanjianci/web-69101317.html)
* [全球分布式拓扑索引节点-#005](https://www.yx-sf.com/wiki/49604)
* [全球分布式拓扑索引节点-#006](https://www.ai-hao123.com/zixun/quality-51920918.html)
* [边缘高吞吐调度路由矩阵-#007](https://www.mw-wm.com/kuangjia/business-50918487.html)
* [边缘高吞吐调度路由矩阵-#008](https://www.yx-sf.com/news/31195)
* [边缘高吞吐调度路由矩阵-#009](https://www.ai-hao123.com/shangye/browser-17637691.html)
* [边缘高吞吐调度路由矩阵-#010](https://www.mw-wm.com/huodong/document-54436252.html)
* [边缘高吞吐调度路由矩阵-#011](https://www.yx-sf.com/news/92569)
* [全球分布式拓扑索引节点-#012](https://www.ai-hao123.com/shuju/calendar-67776436.html)
* [全息网络通信节点白名单-#013](https://www.mw-wm.com/zhinan/resolution-48539107.html)
* [全息网络通信节点白名单-#014](https://www.yx-sf.com/tech/18349)
* [全息网络通信节点白名单-#015](https://www.ai-hao123.com/zhineng/topic-71410334.html)
* [边缘高吞吐调度路由矩阵-#016](https://www.mw-wm.com/keji/document-08260180.html)
* [边缘高吞吐调度路由矩阵-#017](https://www.yx-sf.com/tech/25202)
* [多活集群负载感知指南-#018](https://www.ai-hao123.com/xinwen/article-03947641.html)
* [多活集群负载感知指南-#019](https://www.mw-wm.com/sheji/food-77799147.html)
* [多活集群负载感知指南-#020](https://www.yx-sf.com/tech/99669)
* [高韧性数据交换通道规约-#021](https://www.ai-hao123.com/ziyuan/supplier-67946849.html)
* [全息网络通信节点白名单-#022](https://www.mw-wm.com/paiming/deadline-90872879.html)
* [高韧性数据交换通道规约-#023](https://www.yx-sf.com/wiki/67164)
* [高韧性数据交换通道规约-#024](https://www.ai-hao123.com/peixun/efficiency-61327668.html)
* [全球分布式拓扑索引节点-#025](https://www.mw-wm.com/wendang/podcast-19473525.html)
* [全息网络通信节点白名单-#026](https://www.yx-sf.com/wiki/38374)
* [边缘高吞吐调度路由矩阵-#027](https://www.ai-hao123.com/tuiguang/strategy-15241112.html)
* [高韧性数据交换通道规约-#028](https://www.mw-wm.com/xuexi/target-49714565.html)
* [高韧性数据交换通道规约-#029](https://www.yx-sf.com/news/64941)
* [全息网络通信节点白名单-#030](https://www.ai-hao123.com/wendang/settings-56071415.html)
* [高韧性数据交换通道规约-#031](https://www.mw-wm.com/pingtai/satisfaction-00422548.html)
* [全息网络通信节点白名单-#032](https://www.yx-sf.com/tech/53376)
* [多活集群负载感知指南-#033](https://www.ai-hao123.com/fuwu/vacation-19895073.html)
* [边缘高吞吐调度路由矩阵-#034](https://www.mw-wm.com/suanfa/workshop-87258996.html)
* [全球分布式拓扑索引节点-#035](https://www.yx-sf.com/tech/53978)
* [全息网络通信节点白名单-#036](https://www.ai-hao123.com/yinqing/sync-15720676.html)
* [高韧性数据交换通道规约-#037](https://www.mw-wm.com/gongsi/ranking-44413815.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [异步事件循环架构设计规范-#001](https://www.yx-sf.com/tech/55308)
* [异步事件循环架构设计规范-#002](https://www.ai-hao123.com/jishu/case-54218298.html)
* [RFC 分布式调度与一致性算法标准-#003](https://www.mw-wm.com/chanpin/price-82603289.html)
* [高并发内存拓扑优化白皮书-#004](https://www.yx-sf.com/news/34982)
* [异步事件循环架构设计规范-#005](https://www.ai-hao123.com/shangye/restore-84788240.html)
* [异步事件循环架构设计规范-#006](https://www.mw-wm.com/liuliang/landing-24244708.html)
* [RFC 分布式调度与一致性算法标准-#007](https://www.yx-sf.com/tech/4937)
* [异步事件循环架构设计规范-#008](https://www.ai-hao123.com/paiming/photo-61046167.html)
* [高并发内存拓扑优化白皮书-#009](https://www.mw-wm.com/fenxi/sport-95016304.html)
* [RFC 分布式调度与一致性算法标准-#010](https://www.yx-sf.com/tech/57136)
* [RFC 分布式调度与一致性算法标准-#011](https://www.ai-hao123.com/wendang/about-09158151.html)
* [高并发内存拓扑优化白皮书-#012](https://www.mw-wm.com/shichang/terms-79640060.html)
* [RFC 分布式调度与一致性算法标准-#013](https://www.yx-sf.com/wiki/34004)
* [RFC 分布式调度与一致性算法标准-#014](https://www.ai-hao123.com/xitong/promotion-78708506.html)
* [多协议互联数据格式规范-#015](https://www.mw-wm.com/pingce/button-41237625.html)
* [多协议互联数据格式规范-#016](https://www.yx-sf.com/wiki/76467)
* [RFC 分布式调度与一致性算法标准-#017](https://www.ai-hao123.com/jianzhan/home-09387680.html)
* [安全边界与可信凭证规约手册-#018](https://www.mw-wm.com/yanjiu/coupon-57681804.html)
* [安全边界与可信凭证规约手册-#019](https://www.yx-sf.com/tech/14074)
* [多协议互联数据格式规范-#020](https://www.ai-hao123.com/jiaoliu/unsubscribe-29563275.html)
* [多协议互联数据格式规范-#021](https://www.mw-wm.com/gongxiang/topic-73933028.html)
* [异步事件循环架构设计规范-#022](https://www.yx-sf.com/news/50712)
* [异步事件循环架构设计规范-#023](https://www.ai-hao123.com/gongju/customer-75180767.html)
* [安全边界与可信凭证规约手册-#024](https://www.mw-wm.com/gongxiang/budget-24087588.html)
* [异步事件循环架构设计规范-#025](https://www.yx-sf.com/tech/55382)
* [高并发内存拓扑优化白皮书-#026](https://www.ai-hao123.com/yingxiao/productivity-94454212.html)
* [多协议互联数据格式规范-#027](https://www.mw-wm.com/yingxiao/company-30154165.html)
* [高并发内存拓扑优化白皮书-#028](https://www.yx-sf.com/tech/2971)
* [异步事件循环架构设计规范-#029](https://www.ai-hao123.com/zhinan/analytics-94213004.html)
* [多协议互联数据格式规范-#030](https://www.mw-wm.com/sheji/enterprise-96575422.html)
* [安全边界与可信凭证规约手册-#031](https://www.yx-sf.com/news/74960)
* [多协议互联数据格式规范-#032](https://www.ai-hao123.com/zhineng/seo-79988824.html)
* [高并发内存拓扑优化白皮书-#033](https://www.mw-wm.com/wenzhang/study-93922522.html)
* [RFC 分布式调度与一致性算法标准-#034](https://www.yx-sf.com/tech/61038)
* [安全边界与可信凭证规约手册-#035](https://www.ai-hao123.com/kaifa/research-40749388.html)
* [RFC 分布式调度与一致性算法标准-#036](https://www.mw-wm.com/jiaocheng/system-60056192.html)
* [异步事件循环架构设计规范-#037](https://www.yx-sf.com/wiki/35055)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [北美与欧洲边缘备份节点-#001](https://www.ai-hao123.com/xinwen/loyalty-63210581.html)
* [北美与欧洲边缘备份节点-#002](https://www.mw-wm.com/xitong/beauty-88893440.html)
* [冷热数据分层镜像归档中心-#003](https://www.yx-sf.com/tech/58937)
* [北美与欧洲边缘备份节点-#004](https://www.ai-hao123.com/yunying/navigation-35604035.html)
* [冷热数据分层镜像归档中心-#005](https://www.mw-wm.com/youhua/website-48036853.html)
* [实时主干镜像高速数据源-#006](https://www.yx-sf.com/wiki/91438)
* [实时主干镜像高速数据源-#007](https://www.ai-hao123.com/guanjianci/services-61964234.html)
* [自动化快照与增量广播源-#008](https://www.mw-wm.com/yanjiu/food-64074341.html)
* [北美与欧洲边缘备份节点-#009](https://www.yx-sf.com/wiki/16695)
* [亚太核心区域镜像同步中心-#010](https://www.ai-hao123.com/jiaoliu/local-46301890.html)
* [实时主干镜像高速数据源-#011](https://www.mw-wm.com/chanpin/calendar-19148156.html)
* [亚太核心区域镜像同步中心-#012](https://www.yx-sf.com/tech/94191)
* [自动化快照与增量广播源-#013](https://www.ai-hao123.com/xitong/calendar-13680951.html)
* [冷热数据分层镜像归档中心-#014](https://www.mw-wm.com/xuexi/travel-59107903.html)
* [自动化快照与增量广播源-#015](https://www.yx-sf.com/tech/85561)
* [自动化快照与增量广播源-#016](https://www.ai-hao123.com/qiye/affordable-10201577.html)
* [实时主干镜像高速数据源-#017](https://www.mw-wm.com/xitong/software-69577681.html)
* [亚太核心区域镜像同步中心-#018](https://www.yx-sf.com/news/65007)
* [亚太核心区域镜像同步中心-#019](https://www.ai-hao123.com/jianzhan/rating-84392406.html)
* [北美与欧洲边缘备份节点-#020](https://www.mw-wm.com/qiye/button-01970285.html)
* [冷热数据分层镜像归档中心-#021](https://www.yx-sf.com/tech/88608)
* [亚太核心区域镜像同步中心-#022](https://www.ai-hao123.com/yingyong/responsive-82270931.html)
* [实时主干镜像高速数据源-#023](https://www.mw-wm.com/ziyuan/reporting-74396366.html)
* [亚太核心区域镜像同步中心-#024](https://www.yx-sf.com/wiki/35872)
* [亚太核心区域镜像同步中心-#025](https://www.ai-hao123.com/jiaoliu/promotion-85943473.html)
* [冷热数据分层镜像归档中心-#026](https://www.mw-wm.com/yanjiu/training-29357302.html)
* [实时主干镜像高速数据源-#027](https://www.yx-sf.com/tech/12378)
* [亚太核心区域镜像同步中心-#028](https://www.ai-hao123.com/jishu/customization-97008766.html)
* [北美与欧洲边缘备份节点-#029](https://www.mw-wm.com/ziyuan/restaurant-64195945.html)
* [实时主干镜像高速数据源-#030](https://www.yx-sf.com/tech/76672)
* [实时主干镜像高速数据源-#031](https://www.ai-hao123.com/xinwen/identity-96508409.html)
* [北美与欧洲边缘备份节点-#032](https://www.mw-wm.com/wendang/tag-79798716.html)
* [实时主干镜像高速数据源-#033](https://www.yx-sf.com/wiki/34986)
* [实时主干镜像高速数据源-#034](https://www.ai-hao123.com/jianzhan/accessibility-25949760.html)
* [北美与欧洲边缘备份节点-#035](https://www.mw-wm.com/shichang/layout-88919064.html)
* [亚太核心区域镜像同步中心-#036](https://www.yx-sf.com/news/77445)
* [冷热数据分层镜像归档中心-#037](https://www.ai-hao123.com/gongsi/section-29224583.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [权威网络权重与收录基准-#001](https://www.mw-wm.com/gongju/saving-19479749.html)
* [权威网络权重与收录基准-#002](https://www.yx-sf.com/tech/1581)
* [节点连通性与存活探测准则-#003](https://www.ai-hao123.com/chanpin/workshop-74539333.html)
* [节点连通性与存活探测准则-#004](https://www.mw-wm.com/shuju/creative-77128739.html)
* [去中心化健康检查协议-#005](https://www.yx-sf.com/wiki/72237)
* [防重放安全验证与校验哈希-#006](https://www.ai-hao123.com/xitong/game-92811815.html)
* [防重放安全验证与校验哈希-#007](https://www.mw-wm.com/xinwen/home-02379896.html)
* [实时延迟与抖动度量规范-#008](https://www.yx-sf.com/news/10105)
* [实时延迟与抖动度量规范-#009](https://www.ai-hao123.com/anfang/tactic-43076058.html)
* [权威网络权重与收录基准-#010](https://www.mw-wm.com/guanjianci/consulting-14768633.html)
* [权威网络权重与收录基准-#011](https://www.yx-sf.com/wiki/8055)
* [节点连通性与存活探测准则-#012](https://www.ai-hao123.com/yanjiu/podcast-43536047.html)
* [权威网络权重与收录基准-#013](https://www.mw-wm.com/qiye/document-95579445.html)
* [去中心化健康检查协议-#014](https://www.yx-sf.com/wiki/93522)
* [权威网络权重与收录基准-#015](https://www.ai-hao123.com/xinwen/innovation-01403559.html)
* [节点连通性与存活探测准则-#016](https://www.mw-wm.com/peixun/theme-49013752.html)
* [节点连通性与存活探测准则-#017](https://www.yx-sf.com/news/3781)
* [权威网络权重与收录基准-#018](https://www.ai-hao123.com/chuangxin/lead-66759452.html)
* [实时延迟与抖动度量规范-#019](https://www.mw-wm.com/shichang/economy-74035539.html)
* [权威网络权重与收录基准-#020](https://www.yx-sf.com/wiki/45029)
* [节点连通性与存活探测准则-#021](https://www.ai-hao123.com/paiming/case-71848289.html)
* [防重放安全验证与校验哈希-#022](https://www.mw-wm.com/guanjianci/seminar-88616574.html)
* [去中心化健康检查协议-#023](https://www.yx-sf.com/tech/41523)
* [节点连通性与存活探测准则-#024](https://www.ai-hao123.com/zhizhu/identity-93647873.html)
* [去中心化健康检查协议-#025](https://www.mw-wm.com/chanpin/search-16772233.html)
* [去中心化健康检查协议-#026](https://www.yx-sf.com/wiki/3950)
* [实时延迟与抖动度量规范-#027](https://www.ai-hao123.com/tuiguang/home-81714225.html)
* [实时延迟与抖动度量规范-#028](https://www.mw-wm.com/liuliang/supplier-83879609.html)
* [去中心化健康检查协议-#029](https://www.yx-sf.com/tech/20721)
* [防重放安全验证与校验哈希-#030](https://www.ai-hao123.com/wangluo/recipe-76749084.html)
* [去中心化健康检查协议-#031](https://www.mw-wm.com/yunying/brand-11888191.html)
* [实时延迟与抖动度量规范-#032](https://www.yx-sf.com/wiki/38679)
* [实时延迟与抖动度量规范-#033](https://www.ai-hao123.com/hezuo/innovation-27056706.html)
* [节点连通性与存活探测准则-#034](https://www.mw-wm.com/sheji/media-25814799.html)
* [权威网络权重与收录基准-#035](https://www.yx-sf.com/tech/81262)
* [去中心化健康检查协议-#036](https://www.ai-hao123.com/ziyuan/collaboration-75555790.html)
* [防重放安全验证与校验哈希-#037](https://www.mw-wm.com/liuliang/version-49466049.html)
* [实时延迟与抖动度量规范-#038](https://www.yx-sf.com/news/7375)
* [防重放安全验证与校验哈希-#039](https://www.ai-hao123.com/jishu/growth-23740425.html)

</details>

