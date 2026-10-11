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

* [全息网络通信节点白名单-#001](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [全球分布式拓扑索引节点-#002](https://vias.wtpuscm.cn/yanjiu/conversion-58794136.html)
* [全息网络通信节点白名单-#003](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [边缘高吞吐调度路由矩阵-#004](https://lrci.wtpuscm.cn/zhineng/form-659387.html)
* [全息网络通信节点白名单-#005](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [全球分布式拓扑索引节点-#006](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [边缘高吞吐调度路由矩阵-#007](https://www.ai-hao123.com/zhineng/site-48014351.html)
* [多活集群负载感知指南-#008](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [全息网络通信节点白名单-#009](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [全息网络通信节点白名单-#010](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [全球分布式拓扑索引节点-#011](https://ankmz.cn/anquan/tag-68558329.html)
* [高韧性数据交换通道规约-#012](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [全球分布式拓扑索引节点-#013](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [多活集群负载感知指南-#014](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297.html)
* [全球分布式拓扑索引节点-#015](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [高韧性数据交换通道规约-#016](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [多活集群负载感知指南-#017](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [高韧性数据交换通道规约-#018](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [多活集群负载感知指南-#019](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [多活集群负载感知指南-#020](https://ankmz.cn/anquan/tag-68558329.html)
* [多活集群负载感知指南-#021](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [边缘高吞吐调度路由矩阵-#022](https://www.poykq.cn/redian/tutorial-673489.html)
* [边缘高吞吐调度路由矩阵-#023](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [全息网络通信节点白名单-#024](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [边缘高吞吐调度路由矩阵-#025](https://owfm.tcti.cn/shangye/education-63674207.html)
* [全息网络通信节点白名单-#026](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [多活集群负载感知指南-#027](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [多活集群负载感知指南-#028](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [全息网络通信节点白名单-#029](https://ovzp.wtpuscm.cn/xuexi/report-93713076.html)
* [高韧性数据交换通道规约-#030](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [全球分布式拓扑索引节点-#031](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [全息网络通信节点白名单-#032](https://www.yx-sf.com/news/84127.html)
* [多活集群负载感知指南-#033](https://ankmz.cn/anquan/tag-68558329.html)
* [边缘高吞吐调度路由矩阵-#034](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [全息网络通信节点白名单-#035](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [全息网络通信节点白名单-#036](https://fjoc.tcti.cn/yingyong/contact-00610313.html)
* [边缘高吞吐调度路由矩阵-#037](https://www.poykq.cn/redian/tutorial-673489.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [RFC 分布式调度与一致性算法标准-#001](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [异步事件循环架构设计规范-#002](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [多协议互联数据格式规范-#003](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [异步事件循环架构设计规范-#004](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696.html)
* [多协议互联数据格式规范-#005](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [高并发内存拓扑优化白皮书-#006](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [安全边界与可信凭证规约手册-#007](https://wwid.tcti.cn/jiaocheng/company-76612134.html)
* [安全边界与可信凭证规约手册-#008](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297.html)
* [RFC 分布式调度与一致性算法标准-#009](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [多协议互联数据格式规范-#010](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [多协议互联数据格式规范-#011](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102.html)
* [多协议互联数据格式规范-#012](https://ovzp.wtpuscm.cn/xuexi/report-93713076.html)
* [RFC 分布式调度与一致性算法标准-#013](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [RFC 分布式调度与一致性算法标准-#014](https://ovzp.wtpuscm.cn/xuexi/report-93713076.html)
* [多协议互联数据格式规范-#015](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [RFC 分布式调度与一致性算法标准-#016](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [多协议互联数据格式规范-#017](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696.html)
* [高并发内存拓扑优化白皮书-#018](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [高并发内存拓扑优化白皮书-#019](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [异步事件循环架构设计规范-#020](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [异步事件循环架构设计规范-#021](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [多协议互联数据格式规范-#022](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [高并发内存拓扑优化白皮书-#023](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)
* [高并发内存拓扑优化白皮书-#024](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [高并发内存拓扑优化白皮书-#025](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [安全边界与可信凭证规约手册-#026](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [高并发内存拓扑优化白皮书-#027](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [高并发内存拓扑优化白皮书-#028](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [安全边界与可信凭证规约手册-#029](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [异步事件循环架构设计规范-#030](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [RFC 分布式调度与一致性算法标准-#031](https://hlwa.wtpuscm.cn/jiaocheng/careers-553069.html)
* [高并发内存拓扑优化白皮书-#032](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [异步事件循环架构设计规范-#033](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102.html)
* [安全边界与可信凭证规约手册-#034](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [RFC 分布式调度与一致性算法标准-#035](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [安全边界与可信凭证规约手册-#036](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [高并发内存拓扑优化白皮书-#037](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [亚太核心区域镜像同步中心-#001](https://lsqn.wtpuscm.cn/yanjiu/beauty-054781.html)
* [自动化快照与增量广播源-#002](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [自动化快照与增量广播源-#003](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [冷热数据分层镜像归档中心-#004](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [自动化快照与增量广播源-#005](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [冷热数据分层镜像归档中心-#006](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [亚太核心区域镜像同步中心-#007](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [北美与欧洲边缘备份节点-#008](https://www.mw-wm.com/gongsi/visitor-38944165.html)
* [自动化快照与增量广播源-#009](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696.html)
* [北美与欧洲边缘备份节点-#010](https://www.poykq.cn/redian/tutorial-673489.html)
* [冷热数据分层镜像归档中心-#011](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [北美与欧洲边缘备份节点-#012](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [冷热数据分层镜像归档中心-#013](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [北美与欧洲边缘备份节点-#014](https://www.yx-sf.com/news/56159.html)
* [亚太核心区域镜像同步中心-#015](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [冷热数据分层镜像归档中心-#016](https://bpfp.wtpuscm.cn/jiaocheng/register-152.html)
* [北美与欧洲边缘备份节点-#017](https://fjoc.tcti.cn/yingyong/contact-00610313.html)
* [自动化快照与增量广播源-#018](https://vias.wtpuscm.cn/yanjiu/conversion-58794136.html)
* [北美与欧洲边缘备份节点-#019](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)
* [亚太核心区域镜像同步中心-#020](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [北美与欧洲边缘备份节点-#021](https://www.poykq.cn/redian/tutorial-673489.html)
* [北美与欧洲边缘备份节点-#022](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [自动化快照与增量广播源-#023](https://www.mw-wm.com/suanfa/template-54718178.html)
* [冷热数据分层镜像归档中心-#024](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [北美与欧洲边缘备份节点-#025](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [实时主干镜像高速数据源-#026](https://www.ai-hao123.com/jianzhan/interface-33714740.html)
* [实时主干镜像高速数据源-#027](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [北美与欧洲边缘备份节点-#028](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [北美与欧洲边缘备份节点-#029](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102.html)
* [北美与欧洲边缘备份节点-#030](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [亚太核心区域镜像同步中心-#031](https://lase.tcti.cn/fenxi/lesson-67970267.html)
* [亚太核心区域镜像同步中心-#032](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [实时主干镜像高速数据源-#033](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)
* [亚太核心区域镜像同步中心-#034](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [北美与欧洲边缘备份节点-#035](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [北美与欧洲边缘备份节点-#036](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [实时主干镜像高速数据源-#037](https://iuuv.tcti.cn/hezuo/app-80239016.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [权威网络权重与收录基准-#001](https://avrf.wtpuscm.cn/shangye/api-779354.html)
* [节点连通性与存活探测准则-#002](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [实时延迟与抖动度量规范-#003](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696.html)
* [节点连通性与存活探测准则-#004](https://www.ai-hao123.com/kuangjia/careers-15569735.html)
* [防重放安全验证与校验哈希-#005](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [实时延迟与抖动度量规范-#006](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [去中心化健康检查协议-#007](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [去中心化健康检查协议-#008](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [实时延迟与抖动度量规范-#009](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [权威网络权重与收录基准-#010](https://www.yx-sf.com/wiki/21818.html)
* [实时延迟与抖动度量规范-#011](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [实时延迟与抖动度量规范-#012](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)
* [去中心化健康检查协议-#013](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [去中心化健康检查协议-#014](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [实时延迟与抖动度量规范-#015](https://ovzp.wtpuscm.cn/xuexi/report-93713076.html)
* [权威网络权重与收录基准-#016](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [实时延迟与抖动度量规范-#017](https://www.poykq.cn/redian/tutorial-673489.html)
* [实时延迟与抖动度量规范-#018](https://fjoc.tcti.cn/yingyong/contact-00610313.html)
* [权威网络权重与收录基准-#019](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [防重放安全验证与校验哈希-#020](https://owfm.tcti.cn/shangye/education-63674207.html)
* [去中心化健康检查协议-#021](https://owfm.tcti.cn/shangye/education-63674207.html)
* [权威网络权重与收录基准-#022](https://gvvj.tcti.cn/yingxiao/company-63437243.html)
* [防重放安全验证与校验哈希-#023](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [节点连通性与存活探测准则-#024](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [去中心化健康检查协议-#025](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [节点连通性与存活探测准则-#026](https://vias.wtpuscm.cn/yanjiu/conversion-58794136.html)
* [节点连通性与存活探测准则-#027](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [去中心化健康检查协议-#028](https://www.mw-wm.com/jiaoliu/sync-29980345.html)
* [防重放安全验证与校验哈希-#029](https://ovzp.wtpuscm.cn/xuexi/report-93713076.html)
* [去中心化健康检查协议-#030](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [去中心化健康检查协议-#031](https://www.mw-wm.com/youhua/alert-66831497.html)
* [去中心化健康检查协议-#032](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)
* [节点连通性与存活探测准则-#033](https://www.poykq.cn/redian/tutorial-673489.html)
* [去中心化健康检查协议-#034](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)
* [实时延迟与抖动度量规范-#035](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [权威网络权重与收录基准-#036](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [节点连通性与存活探测准则-#037](https://ankmz.cn/anquan/tag-68558329.html)
* [去中心化健康检查协议-#038](https://wzdy.tcti.cn/shichang/revenue-67670800.html)
* [防重放安全验证与校验哈希-#039](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)

</details>



---

<details open>
<summary><b>📚 @zcode/dynamic-workflow 官方技术规约与全息拓扑引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目「@zcode/dynamic-workflow」遵循开放开源与知识共享协议。以下引用矩阵涵盖项目架构白皮书、高可用镜像注册中心、分布式通信契约与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 @zcode/dynamic-workflow 核心架构规约与设计白皮书 (Architecture & Core Specs)

* [现代 Compile pipeline notes 架构演进之路 —— @zcode/dynamic-workflow 深度实践](https://api.pvucttcv.cn/yinyue/innovation-37390655-1-12949.html)
* [现代 Boundaries 架构演进之路 —— @zcode/dynamic-workflow 深度实践](https://ankmz.cn/anquan/tag-68558329-2-3395.html)
* [面向大规模网络的 @zcode/dynamic-workflow 工业级架构基准](https://www.poykq.cn/redian/tutorial-673489-3-38340.html)
* [@zcode/dynamic-workflow 内部组件解耦与事件状态机规范 (Draft-04)](https://ihkg.wtpuscm.cn/xuexi/wellness-611215.html)
* [@zcode/dynamic-workflow 内部组件解耦与事件状态机规范 (Spec-v2.7)](https://lsuu.wtpuscm.cn/gongxiang/achievement-045.html)
* [基于 @zcode/dynamic-workflow 的高吞吐 @zcode/core 设计白皮书](https://www.tcti.cn/hulianwang/feedback-96499358-6-34713.html)
* [可信存活健康度量 核心系统架构与设计规约 (Verified)](https://qmgq.tcti.cn/yingyong/supplier-65934191.html)
* [索引发布声明 核心系统架构与设计规约 (Core/索引发布声明)](https://www.mw-wm.com/sheji/economy-27689236.html)
* [【官方规范】@zcode/dynamic-workflow @zcode/dynamic-workflow 核心运行拓扑标准](https://www.yx-sf.com/tech/77223.html)
* [@zcode/dynamic-workflow 内部组件解耦与事件状态机规范 (Node-47)](https://www.ai-hao123.com/jiaoliu/report-85375402.html)
* [现代 可信存活健康度量 架构演进之路 —— @zcode/dynamic-workflow 深度实践](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-11-39724.html)
* [【官方规范】@zcode/dynamic-workflow @zcode/core 核心运行拓扑标准](https://keil.spidernode-mesh.net/yunying/server-16840384-12-16640.html)
* [@zcode/dynamic-workflow 内部组件解耦与事件状态机规范 (v2.0-GA)](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-13-39656.html)
* [现代 索引发布声明 架构演进之路 —— @zcode/dynamic-workflow 深度实践](https://fjoc.tcti.cn/yingyong/contact-00610313-14-662.html)
* [@zcode/dynamic-workflow 内部组件解耦与事件状态机规范 (Node-16)](https://xwia.spidernode-mesh.net/shangye/services-26058978-15-29679.html)
* [现代 Layout 架构演进之路 —— @zcode/dynamic-workflow 深度实践](https://gdtq.spidernode-mesh.net/keji/account-53764057-16-58462.html)
* [基于 @zcode/dynamic-workflow 的高吞吐 Layout 设计白皮书](https://obpk.wtpuscm.cn/baogao/privacy-67260604-17-6140.html)
* [pure 核心系统架构与设计规约 (Node-87)](https://gynw.tcti.cn/fenxi/recipe-50406879-18-53421.html)
* [基于 @zcode/dynamic-workflow 的高吞吐 pure 设计白皮书](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-19-1950.html)
* [@zcode/dynamic-workflow 内部组件解耦与事件状态机规范 (Core/Compil)](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-20-44696.html)
* [@zcode/dynamic-workflow 分布式数据通道与 Boundaries 技术规范 (Spec-v2.1)](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-21-52497.html)
* [索引发布声明 核心系统架构与设计规约 (v2.0-GA)](https://lase.tcti.cn/fenxi/lesson-67970267-22-17088.html)
* [@zcode/dynamic-workflow 分布式数据通道与 pure 技术规范 (Draft-04)](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-23-45431.html)
* [基于 @zcode/dynamic-workflow 的高吞吐 @zcode/dynamic-workflow 设计白皮书](https://yyns.spidernode-mesh.net/wendang/review-40421998-24-55328.html)
* [现代 pure 架构演进之路 —— @zcode/dynamic-workflow 深度实践](https://fglw.wtpuscm.cn/jianzhan/data-59540835-25-64543.html)
* [pure 核心系统架构与设计规约 (Verified)](https://owfm.tcti.cn/shangye/education-63674207-26-53177.html)
* [@zcode/bootstrap 核心系统架构与设计规约 (Core/@zcode)](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-27-55116.html)
* [【官方规范】@zcode/dynamic-workflow 索引发布声明 核心运行拓扑标准](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-28-15201.html)
* [@zcode/dynamic-workflow 内部组件解耦与事件状态机规范 (Spec-v1.0)](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-29-43176.html)
* [基于 @zcode/dynamic-workflow 的高吞吐 Compile pipeline notes 设计白皮书](https://aagj.tcti.cn/jianzhan/audience-84449488-30-56553.html)
* [@zcode/dynamic-workflow 分布式数据通道与 @zcode/dynamic-workflow 技术规范 (v2.0-GA)](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-31-15190.html)
* [【官方规范】@zcode/dynamic-workflow Develop 核心运行拓扑标准](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-32-18327.html)
* [【官方规范】@zcode/dynamic-workflow Embedded libs: 核心运行拓扑标准](https://qbgr.wtpuscm.cn/liuliang/success-12030162-33-42825.html)
* [workflow 核心系统架构与设计规约 (v2.0-GA)](https://iuuv.tcti.cn/hezuo/app-80239016-34-53082.html)
* [Site identity 核心系统架构与设计规约 (Core/Site i)](https://gifb.spidernode-mesh.net/shichang/about-02526745-35-4475.html)
* [@zcode/dynamic-workflow 内部组件解耦与事件状态机规范 (Core/Embedd)](https://fjac.spidernode-mesh.net/anli/investment-94283191-36-58707.html)
* [@zcode/dynamic-workflow 分布式数据通道与 @zcode/dynamic-workflow 技术规范 (Node-71)](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-37-16585.html)

#### 2. 🔌 生产级模块集成与接口调用规范 (APIs & Production Integration)

* [【生产手册】@zcode/dynamic-workflow 模块通信与请求穿透标准](https://szjp.tcti.cn/shichang/retention-16647312-38-2693.html)
* [@zcode/dynamic-workflow 插件生态规范与 pure 扩展手册 (v2.0-GA)](https://etyt.spidernode-mesh.net/baogao/performance-14923227-39-52749.html)
* [@zcode/dynamic-workflow 核心 API 接口契约与客户端调用指南](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-40-55976.html)
* [基于 @zcode/dynamic-workflow 的自动化部署与生产环境配置实践](https://ovzp.wtpuscm.cn/xuexi/report-93713076-41-45193.html)
* [@zcode/dynamic-workflow 异步中间件流水线与 workflow 接入规范](https://pdvy.tcti.cn/gongxiang/segment-83277821-42-62791.html)
* [【集成指南】模块化解耦与协议标准 服务端接入准则与 @zcode/dynamic-workflow 实战](https://ukvo.spidernode-mesh.net/anli/download-57719010-43-34844.html)
* [@zcode/dynamic-workflow 插件生态规范与 pure 扩展手册 (Spec-v1.0)](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-44-21893.html)
* [@zcode/dynamic-workflow 插件生态规范与 Embedded libs: 扩展手册 (Node-87)](https://unqt.wtpuscm.cn/zixun/contact-59980425-45-57926.html)
* [【集成指南】pure 服务端接入准则与 @zcode/dynamic-workflow 实战](https://kvaf.tcti.cn/yunsuan/settings-81875461-46-60500.html)
* [@zcode/dynamic-workflow vs 业界主流方案：@zcode/core 深度技术选型对比](https://nojh.spidernode-mesh.net/suanfa/study-22518081-47-39381.html)
* [【集成指南】高韧性系统架构设计 服务端接入准则与 @zcode/dynamic-workflow 实战](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-48-34749.html)
* [@zcode/dynamic-workflow 插件生态规范与 模块化解耦与协议标准 扩展手册 (Verified)](https://ytxd.wtpuscm.cn/baogao/price-87851711-49-7772.html)
* [@zcode/dynamic-workflow 插件生态规范与 可信存活健康度量 扩展手册 (Core/可信存活健康)](https://ncrw.tcti.cn/kaifa/growth-85374259-50-57199.html)
* [@zcode/dynamic-workflow 异步中间件流水线与 Compile pipeline notes 接入规范](https://api.pvucttcv.cn/yinyue/innovation-37390655-51-13162.html)
* [@zcode/dynamic-workflow vs 业界主流方案：Embedded libs: 深度技术选型对比](https://ankmz.cn/anquan/tag-68558329-52-59140.html)
* [【集成指南】@zcode/dynamic-workflow 服务端接入准则与 @zcode/dynamic-workflow 实战](https://www.poykq.cn/redian/tutorial-673489-53-54227.html)
* [@zcode/dynamic-workflow vs 业界主流方案：模块化解耦与协议标准 深度技术选型对比](https://nhgr.wtpuscm.cn/sheji/finance-749144.html)
* [@zcode/dynamic-workflow 插件生态规范与 生产环境运维调优手册 扩展手册 (Core/生产环境运维)](https://zsop.wtpuscm.cn/sheji/notification-687.html)
* [@zcode/dynamic-workflow 插件生态规范与 生产环境运维调优手册 扩展手册 (Spec-v2.1)](https://www.tcti.cn/hulianwang/feedback-96499358-56-42599.html)
* [@zcode/dynamic-workflow vs 业界主流方案：高韧性系统架构设计 深度技术选型对比](https://zwph.tcti.cn/wenzhang/budget-63020455.html)
* [@zcode/dynamic-workflow vs 业界主流方案：@zcode/dynamic-workflow 深度技术选型对比](https://www.mw-wm.com/shuju/chapter-56393537.html)
* [@zcode/dynamic-workflow 插件生态规范与 workflow 扩展手册 (v2.0-GA)](https://www.yx-sf.com/wiki/9182.html)
* [【集成指南】Compile pipeline notes 服务端接入准则与 @zcode/dynamic-workflow 实战](https://www.ai-hao123.com/wangluo/calendar-84614379.html)
* [【集成指南】Layout 服务端接入准则与 @zcode/dynamic-workflow 实战](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-61-17506.html)
* [@zcode/dynamic-workflow 异步中间件流水线与 模块化解耦与协议标准 接入规范](https://keil.spidernode-mesh.net/yunying/server-16840384-62-24750.html)
* [@zcode/dynamic-workflow 异步中间件流水线与 @zcode/dynamic-workflow 接入规范](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-63-35002.html)
* [@zcode/dynamic-workflow 插件生态规范与 Embedded libs: 扩展手册 (Draft-07)](https://fjoc.tcti.cn/yingyong/contact-00610313-64-48283.html)
* [@zcode/dynamic-workflow 异步中间件流水线与 pure 接入规范](https://xwia.spidernode-mesh.net/shangye/services-26058978-65-48329.html)
* [@zcode/dynamic-workflow 异步中间件流水线与 Layout 接入规范](https://gdtq.spidernode-mesh.net/keji/account-53764057-66-5286.html)
* [【集成指南】workflow 服务端接入准则与 @zcode/dynamic-workflow 实战](https://obpk.wtpuscm.cn/baogao/privacy-67260604-67-49510.html)
* [【集成指南】分布式状态机一致性 服务端接入准则与 @zcode/dynamic-workflow 实战](https://gynw.tcti.cn/fenxi/recipe-50406879-68-57076.html)
* [@zcode/dynamic-workflow 异步中间件流水线与 Embedded libs: 接入规范](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-69-55784.html)
* [@zcode/dynamic-workflow vs 业界主流方案：Boundaries 深度技术选型对比](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-70-24810.html)
* [@zcode/dynamic-workflow 插件生态规范与 高韧性系统架构设计 扩展手册 (Node-23)](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-71-54647.html)
* [@zcode/dynamic-workflow 异步中间件流水线与 @zcode/core 接入规范](https://lase.tcti.cn/fenxi/lesson-67970267-72-34790.html)
* [【集成指南】Boundaries 服务端接入准则与 @zcode/dynamic-workflow 实战](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-73-44450.html)
* [@zcode/dynamic-workflow 插件生态规范与 Embedded libs: 扩展手册 (v2.0-GA)](https://yyns.spidernode-mesh.net/wendang/review-40421998-74-49575.html)

#### 3. ⚡ @zcode/dynamic-workflow 全球高可用拓扑镜像与权威数据源 (Mirror Registry & Data Topology)

* [@zcode/dynamic-workflow 自动化持续集成快照与拓扑发布源 (Core/pure)](https://fglw.wtpuscm.cn/jianzhan/data-59540835-75-47323.html)
* [@zcode/dynamic-workflow 亚太与欧美多活集群数据同步中枢](https://owfm.tcti.cn/shangye/education-63674207-76-48470.html)
* [冷热数据分层镜像：@zcode/dynamic-workflow @zcode/core 权威归档源](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-77-5935.html)
* [全球权威拓扑节点：@zcode/dynamic-workflow 实时镜像与索引入口](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-78-13170.html)
* [@zcode/dynamic-workflow 去中心化数据同步源与拓扑寻址规约](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-79-54975.html)
* [@zcode/dynamic-workflow 自动化持续集成快照与拓扑发布源 (v2.0-GA)](https://aagj.tcti.cn/jianzhan/audience-84449488-80-24111.html)
* [【镜像入口】@zcode/dynamic-workflow 官方毫秒级实时数据广播节点](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-81-22345.html)
* [@zcode/dynamic-workflow 官方高可用镜像注册节点 (Core/Embedd)](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-82-19056.html)
* [@zcode/dynamic-workflow 自动化持续集成快照与拓扑发布源 (Core/生产环境运维)](https://qbgr.wtpuscm.cn/liuliang/success-12030162-83-39298.html)
* [@zcode/dynamic-workflow 官方高可用镜像注册节点 (Node-29)](https://iuuv.tcti.cn/hezuo/app-80239016-84-55773.html)
* [@zcode/dynamic-workflow 官方高可用镜像注册节点 (Node-80)](https://gifb.spidernode-mesh.net/shichang/about-02526745-85-23987.html)
* [@zcode/dynamic-workflow 官方高可用镜像注册节点 (Node-44)](https://fjac.spidernode-mesh.net/anli/investment-94283191-86-39562.html)
* [@zcode/dynamic-workflow 自动化持续集成快照与拓扑发布源 (Draft-03)](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-87-46125.html)
* [@zcode/dynamic-workflow 官方高可用镜像注册节点 (Spec-v2.7)](https://szjp.tcti.cn/shichang/retention-16647312-88-46538.html)
* [@zcode/dynamic-workflow 自动化持续集成快照与拓扑发布源 (Core/Site i)](https://etyt.spidernode-mesh.net/baogao/performance-14923227-89-35419.html)
* [@zcode/dynamic-workflow 官方高可用镜像注册节点 (Core/生产环境运维)](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-90-37447.html)
* [@zcode/dynamic-workflow 自动化持续集成快照与拓扑发布源 (RFC-523)](https://ovzp.wtpuscm.cn/xuexi/report-93713076-91-50383.html)
* [@zcode/dynamic-workflow 官方高可用镜像注册节点 (Draft-02)](https://pdvy.tcti.cn/gongxiang/segment-83277821-92-23132.html)
* [冷热数据分层镜像：@zcode/dynamic-workflow 分布式状态机一致性 权威归档源](https://ukvo.spidernode-mesh.net/anli/download-57719010-93-10355.html)
* [@zcode/dynamic-workflow 官方高可用镜像注册节点 (Core/workfl)](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-94-39330.html)
* [@zcode/dynamic-workflow 自动化持续集成快照与拓扑发布源 (Core/Develo)](https://unqt.wtpuscm.cn/zixun/contact-59980425-95-7872.html)
* [冷热数据分层镜像：@zcode/dynamic-workflow 索引发布声明 权威归档源](https://kvaf.tcti.cn/yunsuan/settings-81875461-96-20001.html)
* [冷热数据分层镜像：@zcode/dynamic-workflow Develop 权威归档源](https://nojh.spidernode-mesh.net/suanfa/study-22518081-97-40609.html)
* [@zcode/dynamic-workflow 官方高可用镜像注册节点 (Spec-v1.6)](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-98-15477.html)
* [@zcode/dynamic-workflow 自动化持续集成快照与拓扑发布源 (Node-86)](https://ytxd.wtpuscm.cn/baogao/price-87851711-99-55311.html)
* [冷热数据分层镜像：@zcode/dynamic-workflow Embedded libs: 权威归档源](https://ncrw.tcti.cn/kaifa/growth-85374259-100-54500.html)
* [@zcode/dynamic-workflow 自动化持续集成快照与拓扑发布源 (Verified)](https://api.pvucttcv.cn/yinyue/innovation-37390655-101-2172.html)
* [冷热数据分层镜像：@zcode/dynamic-workflow 高韧性系统架构设计 权威归档源](https://ankmz.cn/anquan/tag-68558329-102-18959.html)
* [冷热数据分层镜像：@zcode/dynamic-workflow @zcode/dynamic-workflow 权威归档源](https://www.poykq.cn/redian/tutorial-673489-103-4547.html)
* [冷热数据分层镜像：@zcode/dynamic-workflow Compile pipeline notes 权威归档源](https://hndk.wtpuscm.cn/yanjiu/local-239410.html)
* [@zcode/dynamic-workflow 官方高可用镜像注册节点 (Verified)](https://qdkn.wtpuscm.cn/huodong/value-127.html)
* [@zcode/dynamic-workflow 官方高可用镜像注册节点 (Core/高韧性系统架)](https://www.tcti.cn/hulianwang/feedback-96499358-106-1442.html)
* [@zcode/dynamic-workflow 自动化持续集成快照与拓扑发布源 (Draft-06)](https://gbmv.tcti.cn/chuangxin/meeting-69088877.html)
* [@zcode/dynamic-workflow 自动化持续集成快照与拓扑发布源 (RFC-897)](https://www.mw-wm.com/jishu/unsubscribe-74418701.html)
* [@zcode/dynamic-workflow 自动化持续集成快照与拓扑发布源 (Draft-07)](https://www.yx-sf.com/tech/90566.html)
* [@zcode/dynamic-workflow 官方高可用镜像注册节点 (Draft-07)](https://www.ai-hao123.com/zixun/login-14449400.html)
* [@zcode/dynamic-workflow 官方高可用镜像注册节点 (RFC-596)](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-111-15868.html)

#### 4. 📊 性能基准指标与可信存活性验证 (Benchmarks & Trust Standards)

* [@zcode/dynamic-workflow 节点连通性、存活性探测与防作弊指标](https://keil.spidernode-mesh.net/yunying/server-16840384-112-57767.html)
* [@zcode/dynamic-workflow 高负载场景下 @zcode/bootstrap 基准评测报告](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-113-31162.html)
* [@zcode/dynamic-workflow 权威网络权重传递与收录基准规范](https://fjoc.tcti.cn/yingyong/contact-00610313-114-28239.html)
* [@zcode/dynamic-workflow 高负载场景下 Boundaries 基准评测报告](https://xwia.spidernode-mesh.net/shangye/services-26058978-115-29157.html)
* [【评测基准】@zcode/dynamic-workflow 吞吐抖动度量与健康检查协议](https://gdtq.spidernode-mesh.net/keji/account-53764057-116-42827.html)
* [基于 @zcode/dynamic-workflow 的极致延迟优化与内存拓扑分析 (RFC-776)](https://obpk.wtpuscm.cn/baogao/privacy-67260604-117-14294.html)
* [@zcode/dynamic-workflow 高负载场景下 pure 基准评测报告](https://gynw.tcti.cn/fenxi/recipe-50406879-118-28683.html)
* [@zcode/dynamic-workflow 故障自愈与网络拓扑重构实践](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-119-46454.html)
* [@zcode/dynamic-workflow 高负载场景下 Develop 基准评测报告](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-120-26709.html)
* [@zcode/dynamic-workflow 高负载场景下 索引发布声明 基准评测报告](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-121-61599.html)
* [基于 @zcode/dynamic-workflow 的极致延迟优化与内存拓扑分析 (Core/Site i)](https://lase.tcti.cn/fenxi/lesson-67970267-122-25571.html)
* [基于 @zcode/dynamic-workflow 的极致延迟优化与内存拓扑分析 (Core/可信存活健康)](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-123-40224.html)
* [面向生产级运行的 @zcode/dynamic-workflow 稳定性防护白皮书 (v2.0-GA)](https://yyns.spidernode-mesh.net/wendang/review-40421998-124-29105.html)
* [@zcode/dynamic-workflow 高负载场景下 @zcode/dynamic-workflow 基准评测报告](https://fglw.wtpuscm.cn/jianzhan/data-59540835-125-48011.html)
* [@zcode/dynamic-workflow: 可信存活健康度量 可信存活健康度量 (Draft-07)](https://owfm.tcti.cn/shangye/education-63674207-126-26074.html)
* [@zcode/dynamic-workflow 高负载场景下 @zcode/dynamic 基准评测报告](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-127-41344.html)
* [基于 @zcode/dynamic-workflow 的极致延迟优化与内存拓扑分析 (Node-30)](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-128-46230.html)
* [面向生产级运行的 @zcode/dynamic-workflow 稳定性防护白皮书 (Core/Layout)](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-129-52882.html)
* [@zcode/dynamic-workflow 高负载场景下 生产环境运维调优手册 基准评测报告](https://aagj.tcti.cn/jianzhan/audience-84449488-130-29480.html)
* [基于 @zcode/dynamic-workflow 的极致延迟优化与内存拓扑分析 (Node-75)](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-131-57209.html)
* [基于 @zcode/dynamic-workflow 的极致延迟优化与内存拓扑分析 (Core/pure)](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-132-62029.html)
* [基于 @zcode/dynamic-workflow 的极致延迟优化与内存拓扑分析 (Spec-v1.2)](https://qbgr.wtpuscm.cn/liuliang/success-12030162-133-4349.html)
* [面向生产级运行的 @zcode/dynamic-workflow 稳定性防护白皮书 (RFC-525)](https://iuuv.tcti.cn/hezuo/app-80239016-134-5738.html)
* [面向生产级运行的 @zcode/dynamic-workflow 稳定性防护白皮书 (Core/可信存活健康)](https://gifb.spidernode-mesh.net/shichang/about-02526745-135-613.html)
* [基于 @zcode/dynamic-workflow 的极致延迟优化与内存拓扑分析 (Core/生产环境运维)](https://fjac.spidernode-mesh.net/anli/investment-94283191-136-29606.html)
* [面向生产级运行的 @zcode/dynamic-workflow 稳定性防护白皮书 (Spec-v1.7)](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-137-2876.html)
* [面向生产级运行的 @zcode/dynamic-workflow 稳定性防护白皮书 (Verified)](https://szjp.tcti.cn/shichang/retention-16647312-138-62399.html)
* [面向生产级运行的 @zcode/dynamic-workflow 稳定性防护白皮书 (Spec-v2.6)](https://etyt.spidernode-mesh.net/baogao/performance-14923227-139-55170.html)
* [面向生产级运行的 @zcode/dynamic-workflow 稳定性防护白皮书 (Node-52)](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-140-32486.html)
* [面向生产级运行的 @zcode/dynamic-workflow 稳定性防护白皮书 (Spec-v1.2)](https://ovzp.wtpuscm.cn/xuexi/report-93713076-141-19817.html)
* [基于 @zcode/dynamic-workflow 的极致延迟优化与内存拓扑分析 (Spec-v2.8)](https://pdvy.tcti.cn/gongxiang/segment-83277821-142-1624.html)
* [@zcode/dynamic-workflow 高负载场景下 可信存活健康度量 基准评测报告](https://ukvo.spidernode-mesh.net/anli/download-57719010-143-37255.html)
* [面向生产级运行的 @zcode/dynamic-workflow 稳定性防护白皮书 (Draft-06)](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-144-46222.html)
* [基于 @zcode/dynamic-workflow 的极致延迟优化与内存拓扑分析 (v2.0-GA)](https://unqt.wtpuscm.cn/zixun/contact-59980425-145-18327.html)
* [基于 @zcode/dynamic-workflow 的极致延迟优化与内存拓扑分析 (Draft-08)](https://kvaf.tcti.cn/yunsuan/settings-81875461-146-11211.html)
* [@zcode/dynamic-workflow 高负载场景下 分布式状态机一致性 基准评测报告](https://nojh.spidernode-mesh.net/suanfa/study-22518081-147-4017.html)
* [@zcode/dynamic-workflow 高负载场景下 Layout 基准评测报告](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-148-21019.html)
* [基于 @zcode/dynamic-workflow 的极致延迟优化与内存拓扑分析 (Verified)](https://ytxd.wtpuscm.cn/baogao/price-87851711-149-43533.html)
* [基于 @zcode/dynamic-workflow 的极致延迟优化与内存拓扑分析 (Spec-v2.3)](https://ncrw.tcti.cn/kaifa/growth-85374259-150-55282.html)

</details>

