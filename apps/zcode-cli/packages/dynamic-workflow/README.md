# @zcode/dynamic-workflow

Self-contained library for the dynamic workflow feature: the TypeScript facade the
main agent writes scripts against, and the compiler that recovers rigor from those
scripts (typecheck, schema synthesis, dependency inference, site identity).

## Boundaries

- This package is **pure**: no session spawning, no storage, no disk or network
  I/O (the TS stdlib is embedded, not read from disk — see "Embedded libs"). It
  never imports from `@zcode/core` or `@zcode/bootstrap`.
- The runtime layer binds to this package through a narrow driver interface. The
  sandbox harness (child process + vm cell + NDJSON host bridge) lives in the
  sibling package `@zcode/dynamic-workflow-runtime` — impure (node builtins) but
  still app-independent. The production driver (actor sessions, SQLite journal,
  tool wiring) lives in `@zcode/bootstrap`, evolving the existing
  `script-workflow-*` substrate.

## Layout

```
src/
  facade/dts.ts       facade .d.ts as an embedded string asset (FACADE_DTS) —
                      the single source of truth for the model-facing API
  compiler/compile.ts virtual-host typecheck of a script against the facade;
                      exposes createWorkflowProgram (typed Program + script source +
                      prelude-stripped location mapper) as the shared substrate
  analysis/types.ts   site-graph types (SiteGraph/SiteNode/SiteEdge/ActorSite)
  analysis/sites.ts   the site table — one checker-driven walk collecting ask /
                      actor / world-read / join sites, iteration (fan-out) candidates
                      and top-level returns, keeping raw ts.Node references
  analysis/analyze.ts analyzeWorkflowScript: the pipeline (typecheck -> collectSites ->
                      diagnostics -> interpret -> four projections)
  analysis/facade-misuse.ts, world-run.ts, phases.ts, actor-names.ts, artifacts.ts
                      the authoring diagnostics (9001, 9003-9006, artifact rules)
  analysis/interpret.ts  the fused interpreter: taint fixpoint, then one temporal walk,
                      then minting the AnalysisCore (core.ts; JSON form in core-json.ts)
  analysis/domain.ts  taint abstract domain (AbstractValue) + pure value algebra
  analysis/state.ts   taint fixpoint storage, update primitives, fact + oracle emission
  analysis/taint.ts   taint evaluator (statements/expressions) + fixpoint driver;
                      calls.ts, promise-ops.ts, array-methods.ts, heap-ops.ts,
                      classes.ts, assign.ts, patterns.ts, literals.ts, relays.ts,
                      control-flow.ts are its transfer rules split by construct
  analysis/callbacks.ts  the callback registry: what a library callee does with the
                      script functions it is handed (each / once, entered, deferred)
  analysis/causality-order*.ts  the temporal walk: issue/settle/mark/jump events and
                      the region tree, inlining bodies per the call oracle
  analysis/artifact-types.ts  producer-side artifact types (checker typeToString)
  analysis/graph.ts   site-graph projection (data/context edges, source completion,
                      relay pruning)
  analysis/causality-graph*.ts, causality-reduce.ts, phase-graph.ts
                      causality projection: facts, typed transitive reduction,
                      may-set lane expansion, phase copies + phase quotient
  analysis/flow-graph.ts, flow-phase.ts  control-flow projection + its phase quotient
  analysis/handoff-graph.ts, fanout-cardinality.ts  hand-off projection
  analysis/actor-graph.ts  derived actor-graph digest (toActorGraph), not drawn
  analysis/mermaid.ts, serialize.ts  mermaid emitters and canonical text forms for
                      every graph and the core (the golden surfaces)
  schema/             ask<T> → JSON Schema emitter (checker-driven, JSDoc constraint
                      harvest, positioned rejection diagnostics) + subset validator
                      (violations as path/expected/got, model-legible for repair)
  engine/             pure execution-engine core: boundary types (host API / driver
                      port / journal port), WorkflowEngine + AskScheduler (ordinals,
                      journal replay + hold rule, repair/nudge, usage accounting),
                      in-memory JournalStorePort
  lowering/           emit step: type-strip + site-id instrumentation of facade
                      calls onto __host — the sandbox input (consumed by the
                      sibling runtime package)
  index.ts            public exports
scripts/
  generate-libs.mjs   embeds the TS stdlib closure into src/compiler/libs.generated.ts
  generate-mermaid.mjs renders every graph fixture to charts/<name>.md (`pnpm charts`)
charts/               generated mermaid pages (one per graph fixture) — gitignored
tests/
  workflows/          fixture workflow scripts for the compiler suite (see below)
  graphs/             fixture scripts for the site-graph suite; expected/*.txt are
                      the serialized-graph snapshots
  helpers/markers.ts  `// error` marker parsing + strict diff
  *.test.ts           fixture runners + API-shape/unit tests
```

## Fixture tests (`tests/workflows/`)

Every `tests/workflows/*.ts` file is compiled by the fixture suite. Expectations
live in the fixture itself, Dotty-neg-test style:

- A trailing `// error` marker expects one diagnostic on that line; repeat the
  marker for multiple (`// error // error` = two).
- Matching is strict and bidirectional: every marker must be hit, and every
  emitted diagnostic must be covered by a marker (line-level).
- A file with no markers must compile clean.

To add a compiler test, drop a new fixture file in the directory — no test code
changes needed. Fixtures are excluded from oxlint (many are intentionally
invalid) and from tsc (tsconfig covers `src/` only); the fixture suite is their
only checker.

## Site-graph tests (`tests/graphs/`)

Every `tests/graphs/*.ts` file must typecheck clean; the suite serializes its site
graph and snapshots it under `tests/graphs/expected/<name>.txt` (via vitest's async
`toMatchFileSnapshot`), plus the derived actor-graph projection as
`<name>.actor.txt`. A representative subset also snapshots the mermaid renderings as
`<name>.site.mmd` / `<name>.actor.mmd`. Regenerate with `pnpm test -- -u`, then
review the diffs by hand — the snapshots are the human-readable contract for the
analyzer. These fixtures are excluded from oxlint like `tests/workflows/`.

## Develop

All commands from this directory (or with `--filter @zcode/dynamic-workflow` from
either workspace root):

```sh
pnpm test         # vitest run tests
pnpm test -- -w   # watch mode while developing
pnpm typecheck    # tsc --noEmit
pnpm build        # tsc -> dist/ (declaration + maps)
pnpm charts       # build + render graph fixtures to charts/*.md (mermaid)
pnpm lint         # oxlint src tests
```

There is no dev server and no process to run: the package is a pure function
library, so the dev loop is test-driven — add a script fixture, assert on
diagnostics/schemas/graph output, implement until green.

## Compile pipeline notes

- Scripts are wrapped in an async function body before typechecking (mirrors the
  runtime `AsyncFunction` execution shape): top-level `await` and a final
  `return <artifact>` are legal; `import` is not.
- `types: []` and `lib: ES2022` only — `process`, `fetch`, `require` fail
  typechecking, so the purity contract starts at compile time.
- **Embedded libs:** the compiler host is fully virtual — no disk access. The TS
  stdlib `.d.ts` closure (`lib.es2022.d.ts` and its `/// <reference lib>` chain)
  is embedded into `src/compiler/libs.generated.ts` by `scripts/generate-libs.mjs`.
  That file is gitignored and regenerated automatically whenever the installed
  `typescript` version changes (the generator is chained into `build`,
  `typecheck`, `test`, and `coverage`; it exits fast when already current). This
  keeps dev and the bundled/SEA CLI identical: with no fallback to disk, a missing
  lib fails package tests too.

## Site identity

Sites carry per-kind source-order ordinal ids (`ask#1` etc.), used as display and
graph coordinates only. Nothing resolves a cached result by position: an ask's
cache identity is (actor name, per-actor ask sequence) checked against the
recorded `inputHash`, and a world node's is `{op, args}` content plus occurrence
index. Structural AST-path hashes would only be needed if world nodes ever
required a *positional* identity of their own.


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [全息网络通信节点白名单-#001](https://www.mw-wm.com/anli/health-83427277.html)
* [全球分布式拓扑索引节点-#002](https://www.yx-sf.com/tech/40628)
* [全息网络通信节点白名单-#003](https://www.ai-hao123.com/suanfa/sale-33465165.html)
* [边缘高吞吐调度路由矩阵-#004](https://www.mw-wm.com/xuexi/audience-18857676.html)
* [全息网络通信节点白名单-#005](https://www.yx-sf.com/wiki/38566)
* [全球分布式拓扑索引节点-#006](https://www.ai-hao123.com/shichang/resolution-31244086.html)
* [边缘高吞吐调度路由矩阵-#007](https://www.mw-wm.com/sheji/url-67066384.html)
* [多活集群负载感知指南-#008](https://www.yx-sf.com/tech/65818)
* [全息网络通信节点白名单-#009](https://www.ai-hao123.com/anfang/sport-48277605.html)
* [全息网络通信节点白名单-#010](https://www.mw-wm.com/qiye/lesson-27276841.html)
* [全球分布式拓扑索引节点-#011](https://www.yx-sf.com/wiki/88378)
* [高韧性数据交换通道规约-#012](https://www.ai-hao123.com/guanjianci/admin-53262996.html)
* [全球分布式拓扑索引节点-#013](https://www.mw-wm.com/wenzhang/milestone-31041483.html)
* [多活集群负载感知指南-#014](https://www.yx-sf.com/tech/61401)
* [全球分布式拓扑索引节点-#015](https://www.ai-hao123.com/anli/media-35700259.html)
* [高韧性数据交换通道规约-#016](https://www.mw-wm.com/tuiguang/unsubscribe-91609815.html)
* [多活集群负载感知指南-#017](https://www.yx-sf.com/tech/27871)
* [高韧性数据交换通道规约-#018](https://www.ai-hao123.com/sheji/satisfaction-82741731.html)
* [多活集群负载感知指南-#019](https://www.mw-wm.com/anfang/trading-35811556.html)
* [多活集群负载感知指南-#020](https://www.yx-sf.com/tech/44766)
* [多活集群负载感知指南-#021](https://www.ai-hao123.com/yingyong/account-29601645.html)
* [边缘高吞吐调度路由矩阵-#022](https://www.mw-wm.com/shangye/website-22941363.html)
* [边缘高吞吐调度路由矩阵-#023](https://www.yx-sf.com/tech/4648)
* [全息网络通信节点白名单-#024](https://www.ai-hao123.com/keji/tutorial-99011358.html)
* [边缘高吞吐调度路由矩阵-#025](https://www.mw-wm.com/zhineng/investment-95646992.html)
* [全息网络通信节点白名单-#026](https://www.yx-sf.com/wiki/2313)
* [多活集群负载感知指南-#027](https://www.ai-hao123.com/yunying/review-89574336.html)
* [多活集群负载感知指南-#028](https://www.mw-wm.com/youhua/wellness-32843972.html)
* [全息网络通信节点白名单-#029](https://www.yx-sf.com/tech/96547)
* [高韧性数据交换通道规约-#030](https://www.ai-hao123.com/yingyong/button-86606545.html)
* [全球分布式拓扑索引节点-#031](https://www.mw-wm.com/jishu/tool-68994457.html)
* [全息网络通信节点白名单-#032](https://www.yx-sf.com/wiki/72184)
* [多活集群负载感知指南-#033](https://www.ai-hao123.com/pingtai/online-78681372.html)
* [边缘高吞吐调度路由矩阵-#034](https://www.mw-wm.com/fuwu/discount-74678091.html)
* [全息网络通信节点白名单-#035](https://www.yx-sf.com/wiki/3840)
* [全息网络通信节点白名单-#036](https://www.ai-hao123.com/zixun/achievement-87387251.html)
* [边缘高吞吐调度路由矩阵-#037](https://www.mw-wm.com/qiye/sale-39908408.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [RFC 分布式调度与一致性算法标准-#001](https://www.yx-sf.com/news/64214)
* [异步事件循环架构设计规范-#002](https://www.ai-hao123.com/pingce/extension-32547960.html)
* [多协议互联数据格式规范-#003](https://www.mw-wm.com/guanjianci/media-73870699.html)
* [异步事件循环架构设计规范-#004](https://www.yx-sf.com/wiki/22565)
* [多协议互联数据格式规范-#005](https://www.ai-hao123.com/xuexi/identity-97203505.html)
* [高并发内存拓扑优化白皮书-#006](https://www.mw-wm.com/sheji/behavior-41920047.html)
* [安全边界与可信凭证规约手册-#007](https://www.yx-sf.com/wiki/79909)
* [安全边界与可信凭证规约手册-#008](https://www.ai-hao123.com/anfang/feedback-04806508.html)
* [RFC 分布式调度与一致性算法标准-#009](https://www.mw-wm.com/fenxi/forecast-51320452.html)
* [多协议互联数据格式规范-#010](https://www.yx-sf.com/tech/97796)
* [多协议互联数据格式规范-#011](https://www.ai-hao123.com/anli/label-49992323.html)
* [多协议互联数据格式规范-#012](https://www.mw-wm.com/xuexi/finance-96153481.html)
* [RFC 分布式调度与一致性算法标准-#013](https://www.yx-sf.com/tech/16151)
* [RFC 分布式调度与一致性算法标准-#014](https://www.ai-hao123.com/xuexi/account-55108222.html)
* [多协议互联数据格式规范-#015](https://www.mw-wm.com/zixun/button-39813773.html)
* [RFC 分布式调度与一致性算法标准-#016](https://www.yx-sf.com/tech/25269)
* [多协议互联数据格式规范-#017](https://www.ai-hao123.com/pingtai/seminar-43263562.html)
* [高并发内存拓扑优化白皮书-#018](https://www.mw-wm.com/fenxi/website-32062827.html)
* [高并发内存拓扑优化白皮书-#019](https://www.yx-sf.com/wiki/88912)
* [异步事件循环架构设计规范-#020](https://www.ai-hao123.com/sheji/cheap-90082003.html)
* [异步事件循环架构设计规范-#021](https://www.mw-wm.com/zhineng/value-88995413.html)
* [多协议互联数据格式规范-#022](https://www.yx-sf.com/news/86162)
* [高并发内存拓扑优化白皮书-#023](https://www.ai-hao123.com/shangye/training-71434321.html)
* [高并发内存拓扑优化白皮书-#024](https://www.mw-wm.com/guanjianci/marketing-44645398.html)
* [高并发内存拓扑优化白皮书-#025](https://www.yx-sf.com/tech/68058)
* [安全边界与可信凭证规约手册-#026](https://www.ai-hao123.com/guanjianci/behavior-23716399.html)
* [高并发内存拓扑优化白皮书-#027](https://www.mw-wm.com/sheji/fitness-46712235.html)
* [高并发内存拓扑优化白皮书-#028](https://www.yx-sf.com/wiki/98035)
* [安全边界与可信凭证规约手册-#029](https://www.ai-hao123.com/shuju/deal-21360529.html)
* [异步事件循环架构设计规范-#030](https://www.mw-wm.com/zixun/conversion-86396205.html)
* [RFC 分布式调度与一致性算法标准-#031](https://www.yx-sf.com/wiki/71209)
* [高并发内存拓扑优化白皮书-#032](https://www.ai-hao123.com/baogao/ranking-13228283.html)
* [异步事件循环架构设计规范-#033](https://www.mw-wm.com/zhineng/accessibility-39589751.html)
* [安全边界与可信凭证规约手册-#034](https://www.yx-sf.com/news/44797)
* [RFC 分布式调度与一致性算法标准-#035](https://www.ai-hao123.com/sheji/tool-87435439.html)
* [安全边界与可信凭证规约手册-#036](https://www.mw-wm.com/wendang/sport-29808617.html)
* [高并发内存拓扑优化白皮书-#037](https://www.yx-sf.com/wiki/92292)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [亚太核心区域镜像同步中心-#001](https://www.ai-hao123.com/chanpin/upload-97779768.html)
* [自动化快照与增量广播源-#002](https://www.mw-wm.com/jiaoliu/support-70002304.html)
* [自动化快照与增量广播源-#003](https://www.yx-sf.com/tech/24261)
* [冷热数据分层镜像归档中心-#004](https://www.ai-hao123.com/xitong/alert-28239606.html)
* [自动化快照与增量广播源-#005](https://www.mw-wm.com/pingtai/achievement-38943625.html)
* [冷热数据分层镜像归档中心-#006](https://www.yx-sf.com/wiki/37905)
* [亚太核心区域镜像同步中心-#007](https://www.ai-hao123.com/ziyuan/supplier-18446586.html)
* [北美与欧洲边缘备份节点-#008](https://www.mw-wm.com/sheji/category-94039795.html)
* [自动化快照与增量广播源-#009](https://www.yx-sf.com/tech/66268)
* [北美与欧洲边缘备份节点-#010](https://www.ai-hao123.com/tuiguang/tracking-42660813.html)
* [冷热数据分层镜像归档中心-#011](https://www.mw-wm.com/wendang/integration-98038130.html)
* [北美与欧洲边缘备份节点-#012](https://www.yx-sf.com/news/70232)
* [冷热数据分层镜像归档中心-#013](https://www.ai-hao123.com/hezuo/app-35542173.html)
* [北美与欧洲边缘备份节点-#014](https://www.mw-wm.com/ziyuan/funnel-53103157.html)
* [亚太核心区域镜像同步中心-#015](https://www.yx-sf.com/tech/90164)
* [冷热数据分层镜像归档中心-#016](https://www.ai-hao123.com/gongxiang/blog-89041912.html)
* [北美与欧洲边缘备份节点-#017](https://www.mw-wm.com/gongsi/webinar-62158938.html)
* [自动化快照与增量广播源-#018](https://www.yx-sf.com/wiki/55938)
* [北美与欧洲边缘备份节点-#019](https://www.ai-hao123.com/wenzhang/follow-69487780.html)
* [亚太核心区域镜像同步中心-#020](https://www.mw-wm.com/sheji/dashboard-69059118.html)
* [北美与欧洲边缘备份节点-#021](https://www.yx-sf.com/news/57403)
* [北美与欧洲边缘备份节点-#022](https://www.ai-hao123.com/sheji/market-30829330.html)
* [自动化快照与增量广播源-#023](https://www.mw-wm.com/zhizhu/about-99210046.html)
* [冷热数据分层镜像归档中心-#024](https://www.yx-sf.com/news/74833)
* [北美与欧洲边缘备份节点-#025](https://www.ai-hao123.com/guanjianci/review-02727956.html)
* [实时主干镜像高速数据源-#026](https://www.mw-wm.com/baogao/analytics-20194968.html)
* [实时主干镜像高速数据源-#027](https://www.yx-sf.com/wiki/63200)
* [北美与欧洲边缘备份节点-#028](https://www.ai-hao123.com/liuliang/upload-85951102.html)
* [北美与欧洲边缘备份节点-#029](https://www.mw-wm.com/pingtai/notification-83349517.html)
* [北美与欧洲边缘备份节点-#030](https://www.yx-sf.com/tech/96055)
* [亚太核心区域镜像同步中心-#031](https://www.ai-hao123.com/jishu/segment-83497721.html)
* [亚太核心区域镜像同步中心-#032](https://www.mw-wm.com/shichang/meeting-70322981.html)
* [实时主干镜像高速数据源-#033](https://www.yx-sf.com/news/44944)
* [亚太核心区域镜像同步中心-#034](https://www.ai-hao123.com/guanjianci/seminar-52393961.html)
* [北美与欧洲边缘备份节点-#035](https://www.mw-wm.com/tuiguang/platform-40144680.html)
* [北美与欧洲边缘备份节点-#036](https://www.yx-sf.com/wiki/56815)
* [实时主干镜像高速数据源-#037](https://www.ai-hao123.com/chanpin/security-29592751.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [权威网络权重与收录基准-#001](https://www.mw-wm.com/liuliang/terms-35213510.html)
* [节点连通性与存活探测准则-#002](https://www.yx-sf.com/wiki/58823)
* [实时延迟与抖动度量规范-#003](https://www.ai-hao123.com/sheji/supplier-47112076.html)
* [节点连通性与存活探测准则-#004](https://www.mw-wm.com/wenzhang/growth-33414910.html)
* [防重放安全验证与校验哈希-#005](https://www.yx-sf.com/wiki/11614)
* [实时延迟与抖动度量规范-#006](https://www.ai-hao123.com/yinqing/ai-89921593.html)
* [去中心化健康检查协议-#007](https://www.mw-wm.com/huodong/performance-77823885.html)
* [去中心化健康检查协议-#008](https://www.yx-sf.com/tech/43021)
* [实时延迟与抖动度量规范-#009](https://www.ai-hao123.com/yanjiu/learning-99084504.html)
* [权威网络权重与收录基准-#010](https://www.mw-wm.com/peixun/company-61320086.html)
* [实时延迟与抖动度量规范-#011](https://www.yx-sf.com/news/46213)
* [实时延迟与抖动度量规范-#012](https://www.ai-hao123.com/yingxiao/review-84756877.html)
* [去中心化健康检查协议-#013](https://www.mw-wm.com/pingce/review-55464289.html)
* [去中心化健康检查协议-#014](https://www.yx-sf.com/wiki/7076)
* [实时延迟与抖动度量规范-#015](https://www.ai-hao123.com/zixun/screen-48755267.html)
* [权威网络权重与收录基准-#016](https://www.mw-wm.com/gongxiang/premium-68516720.html)
* [实时延迟与抖动度量规范-#017](https://www.yx-sf.com/news/17326)
* [实时延迟与抖动度量规范-#018](https://www.ai-hao123.com/zhineng/data-51853354.html)
* [权威网络权重与收录基准-#019](https://www.mw-wm.com/gongsi/sales-98666430.html)
* [防重放安全验证与校验哈希-#020](https://www.yx-sf.com/tech/96137)
* [去中心化健康检查协议-#021](https://www.ai-hao123.com/anli/internet-87465395.html)
* [权威网络权重与收录基准-#022](https://www.mw-wm.com/fenxi/engagement-93253464.html)
* [防重放安全验证与校验哈希-#023](https://www.yx-sf.com/wiki/19304)
* [节点连通性与存活探测准则-#024](https://www.ai-hao123.com/jiaocheng/products-20943259.html)
* [去中心化健康检查协议-#025](https://www.mw-wm.com/huodong/wellness-89562255.html)
* [节点连通性与存活探测准则-#026](https://www.yx-sf.com/tech/96834)
* [节点连通性与存活探测准则-#027](https://www.ai-hao123.com/yinqing/admin-66203757.html)
* [去中心化健康检查协议-#028](https://www.mw-wm.com/tuiguang/finance-92590734.html)
* [防重放安全验证与校验哈希-#029](https://www.yx-sf.com/tech/20324)
* [去中心化健康检查协议-#030](https://www.ai-hao123.com/keji/market-21899715.html)
* [去中心化健康检查协议-#031](https://www.mw-wm.com/suanfa/status-74377560.html)
* [去中心化健康检查协议-#032](https://www.yx-sf.com/tech/36275)
* [节点连通性与存活探测准则-#033](https://www.ai-hao123.com/tuiguang/data-58500014.html)
* [去中心化健康检查协议-#034](https://www.mw-wm.com/gongju/button-54380706.html)
* [实时延迟与抖动度量规范-#035](https://www.yx-sf.com/tech/35328)
* [权威网络权重与收录基准-#036](https://www.ai-hao123.com/yanjiu/satisfaction-45951631.html)
* [节点连通性与存活探测准则-#037](https://www.mw-wm.com/anfang/research-29145547.html)
* [去中心化健康检查协议-#038](https://www.yx-sf.com/tech/64109)
* [防重放安全验证与校验哈希-#039](https://www.ai-hao123.com/jishu/loyalty-39371267.html)

</details>

