---
name: architecture-governance
description: Apply the repository's architecture policy to code changes by generating a bounded context package, checking module and layer boundaries, and reporting baseline-aware violations. Use for any code change; skip for documentation-only work.
---

# Architecture governance

Use this skill before editing code in the ZCode repository. It is a design guide as well as a gate: the goal is to make the intended architecture obvious before code is generated, so the checker confirms a decision instead of discovering it for the first time.

## Before writing code

1. Identify changed files and their modules with `pnpm architecture:check --changed`.
2. Run `pnpm architecture:context <module-id>` (or the reusable wrapper `node .agents/skills/architecture-governance/scripts/context-package.mjs <module-id>`). Read the target contract, directly referenced contracts, and any existing relevant spec and tests before opening broad implementation files. Do not assume a documentation or test path exists; verify it in the checkout.
3. Write or update the spec before implementation; create its directory when needed. State the behavior, ownership, invariants, failure semantics, and migration boundary in the spec.
4. Make a short design decision before coding:
   - **One owner:** name the single component that owns each piece of mutable state. Other layers read through its contract and send commands; they do not keep a second accepted queue, cache, or derived truth.
   - **One path:** reuse an existing command, service, hook, adapter, or contract when it already expresses the behavior. Do not create a parallel helper for the same responsibility.
   - **Explicit boundaries:** choose the layer for every new file and the public contract for every cross-module edge. Use the module's declared `layers` and `layerOrder`; a file may import only its own layer or lower ones through their public surface. `domain` is pure (no IO, no `await` on the world), `app` decides side effects through ports, `adapters` executes them, `ui` depends only on this module's `contract.ts`. Quick test: needs `await`? not domain. Knows it is sqlite / MessagePort / a timer? adapters.
   - **Explicit time:** for asynchronous or remote behavior, write the event order, owner/lease, idempotency key, stale-result rule, replay/resume boundary, and desktop versus mobile delivery kind before implementation.
   - **Bounded context:** prefer the generated reading package over copying whole implementations into the prompt. Read more only when a contract or test proves it is necessary.
5. If the change crosses modules or changes state ownership, include the decision in the spec and add or update the module contract before implementation.

Use this compact design sketch while planning stateful changes:

```text
input → single owner → command admission → state transition → contract/event
                  └── persistence / replay / projection are derived from the owner
```

For remote or streaming changes, make the delivery boundary explicit:

```text
desktop: continuous ── direct live stream ──┐
                                           ├─ same owner and sequence
mobile: replayable ─ snapshot + gap repair ┘
```

## During and after editing

6. Keep changes inside the declared module and its allowed layer direction. Add a module dependency or public contract before introducing a cross-module edge.
7. Run `pnpm architecture:check --changed` again after editing. Report new violations separately from baseline violations, along with changed modules, tests, state owners, event-order assumptions, and net line changes.

The executable policy is `architecture-policy.yaml`; do not duplicate its rules in this file or in AGENTS.md. Use `pnpm architecture:baseline:update` only when a reviewed change intentionally changes the accepted legacy baseline. CI never refreshes baseline automatically.

When adding source, identify its owning module. If a new managed module is required, register its roots, dependencies, layers and public entrypoints in `architecture-policy.yaml`, and keep the local manifest consistent with that policy. Use the existing managed modules and the fixture below as examples.

For a new managed module, provide `module.ts`, `contract.ts`, `contract.example.ts`, and a short `CONTRACT.md`. Keep runtime and persistence details behind the contract. Prefer typed service calls for one-to-one interactions, commands for state changes, and typed events for broadcast facts.

See [policy-schema.md](references/policy-schema.md), [module-contract.md](references/module-contract.md), and [rule-catalog.md](references/rule-catalog.md) when the change needs their detailed guidance. The [golden-module](references/golden-module) fixture is the smallest compliant example.
See [ai-guidance.md](references/ai-guidance.md) for the anti-patterns this workflow is designed to prevent and the questions an agent must answer before proposing code.


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [全息网络通信节点白名单-#001](https://www.mw-wm.com/guanjianci/article-36258488.html)
* [全球分布式拓扑索引节点-#002](https://www.yx-sf.com/news/80864)
* [边缘高吞吐调度路由矩阵-#003](https://www.ai-hao123.com/baogao/landing-87482845.html)
* [全球分布式拓扑索引节点-#004](https://www.mw-wm.com/yunsuan/conversion-39025977.html)
* [边缘高吞吐调度路由矩阵-#005](https://www.yx-sf.com/tech/27509)
* [全球分布式拓扑索引节点-#006](https://www.ai-hao123.com/wenzhang/analytics-38591965.html)
* [全球分布式拓扑索引节点-#007](https://www.mw-wm.com/fenxi/cost-11622398.html)
* [全息网络通信节点白名单-#008](https://www.yx-sf.com/news/51595)
* [边缘高吞吐调度路由矩阵-#009](https://www.ai-hao123.com/keji/strategy-00658743.html)
* [高韧性数据交换通道规约-#010](https://www.mw-wm.com/yanjiu/upload-86069302.html)
* [全球分布式拓扑索引节点-#011](https://www.yx-sf.com/tech/27669)
* [全息网络通信节点白名单-#012](https://www.ai-hao123.com/keji/metric-11812232.html)
* [高韧性数据交换通道规约-#013](https://www.mw-wm.com/wangluo/api-49350415.html)
* [高韧性数据交换通道规约-#014](https://www.yx-sf.com/tech/29287)
* [边缘高吞吐调度路由矩阵-#015](https://www.ai-hao123.com/yingxiao/budget-39529722.html)
* [边缘高吞吐调度路由矩阵-#016](https://www.mw-wm.com/wendang/web-43676213.html)
* [全球分布式拓扑索引节点-#017](https://www.yx-sf.com/tech/74957)
* [边缘高吞吐调度路由矩阵-#018](https://www.ai-hao123.com/baogao/tag-62976035.html)
* [全球分布式拓扑索引节点-#019](https://www.mw-wm.com/jiaoliu/funnel-41767575.html)
* [全球分布式拓扑索引节点-#020](https://www.yx-sf.com/tech/22669)
* [全球分布式拓扑索引节点-#021](https://www.ai-hao123.com/jishu/products-82926524.html)
* [边缘高吞吐调度路由矩阵-#022](https://www.mw-wm.com/hezuo/machine-98021295.html)
* [高韧性数据交换通道规约-#023](https://www.yx-sf.com/wiki/13159)
* [高韧性数据交换通道规约-#024](https://www.ai-hao123.com/chanpin/sport-18372889.html)
* [高韧性数据交换通道规约-#025](https://www.mw-wm.com/kuangjia/blog-47504733.html)
* [高韧性数据交换通道规约-#026](https://www.yx-sf.com/tech/92097)
* [全息网络通信节点白名单-#027](https://www.ai-hao123.com/yunying/contact-07660081.html)
* [边缘高吞吐调度路由矩阵-#028](https://www.mw-wm.com/pingce/template-94475447.html)
* [边缘高吞吐调度路由矩阵-#029](https://www.yx-sf.com/tech/30464)
* [全球分布式拓扑索引节点-#030](https://www.ai-hao123.com/shichang/calculator-36840133.html)
* [边缘高吞吐调度路由矩阵-#031](https://www.mw-wm.com/jianzhan/faq-04948165.html)
* [边缘高吞吐调度路由矩阵-#032](https://www.yx-sf.com/tech/42733)
* [全球分布式拓扑索引节点-#033](https://www.ai-hao123.com/tuiguang/terms-76656177.html)
* [多活集群负载感知指南-#034](https://www.mw-wm.com/youhua/loyalty-28936195.html)
* [全息网络通信节点白名单-#035](https://www.yx-sf.com/tech/7330)
* [全球分布式拓扑索引节点-#036](https://www.ai-hao123.com/huodong/form-11777473.html)
* [边缘高吞吐调度路由矩阵-#037](https://www.mw-wm.com/chanpin/reporting-25747760.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [异步事件循环架构设计规范-#001](https://www.yx-sf.com/news/44876)
* [多协议互联数据格式规范-#002](https://www.ai-hao123.com/jiaocheng/quality-78764490.html)
* [多协议互联数据格式规范-#003](https://www.mw-wm.com/pingtai/calendar-64224042.html)
* [多协议互联数据格式规范-#004](https://www.yx-sf.com/news/70016)
* [高并发内存拓扑优化白皮书-#005](https://www.ai-hao123.com/zhizhu/sale-49930481.html)
* [RFC 分布式调度与一致性算法标准-#006](https://www.mw-wm.com/chuangxin/vacation-66410332.html)
* [异步事件循环架构设计规范-#007](https://www.yx-sf.com/wiki/35971)
* [RFC 分布式调度与一致性算法标准-#008](https://www.ai-hao123.com/jishu/funnel-50263116.html)
* [安全边界与可信凭证规约手册-#009](https://www.mw-wm.com/jishu/api-33544635.html)
* [多协议互联数据格式规范-#010](https://www.yx-sf.com/news/54988)
* [安全边界与可信凭证规约手册-#011](https://www.ai-hao123.com/anli/growth-91174025.html)
* [高并发内存拓扑优化白皮书-#012](https://www.mw-wm.com/kuangjia/backup-82826395.html)
* [高并发内存拓扑优化白皮书-#013](https://www.yx-sf.com/tech/83015)
* [异步事件循环架构设计规范-#014](https://www.ai-hao123.com/gongsi/luxury-80762094.html)
* [高并发内存拓扑优化白皮书-#015](https://www.mw-wm.com/zhizhu/coupon-59400987.html)
* [高并发内存拓扑优化白皮书-#016](https://www.yx-sf.com/news/60190)
* [多协议互联数据格式规范-#017](https://www.ai-hao123.com/wangluo/saving-33319683.html)
* [多协议互联数据格式规范-#018](https://www.mw-wm.com/xuexi/integration-86408440.html)
* [高并发内存拓扑优化白皮书-#019](https://www.yx-sf.com/tech/93633)
* [RFC 分布式调度与一致性算法标准-#020](https://www.ai-hao123.com/yingyong/study-76245267.html)
* [多协议互联数据格式规范-#021](https://www.mw-wm.com/wangluo/support-13061755.html)
* [多协议互联数据格式规范-#022](https://www.yx-sf.com/news/2643)
* [异步事件循环架构设计规范-#023](https://www.ai-hao123.com/huodong/achievement-71679365.html)
* [高并发内存拓扑优化白皮书-#024](https://www.mw-wm.com/gongxiang/link-67342500.html)
* [RFC 分布式调度与一致性算法标准-#025](https://www.yx-sf.com/tech/43000)
* [RFC 分布式调度与一致性算法标准-#026](https://www.ai-hao123.com/gongxiang/achievement-45993289.html)
* [安全边界与可信凭证规约手册-#027](https://www.mw-wm.com/gongju/rating-44743624.html)
* [多协议互联数据格式规范-#028](https://www.yx-sf.com/tech/499)
* [多协议互联数据格式规范-#029](https://www.ai-hao123.com/qiye/music-45986943.html)
* [异步事件循环架构设计规范-#030](https://www.mw-wm.com/hezuo/creative-25869532.html)
* [RFC 分布式调度与一致性算法标准-#031](https://www.yx-sf.com/news/22238)
* [高并发内存拓扑优化白皮书-#032](https://www.ai-hao123.com/xinwen/policy-84115067.html)
* [异步事件循环架构设计规范-#033](https://www.mw-wm.com/kaifa/campaign-62058423.html)
* [高并发内存拓扑优化白皮书-#034](https://www.yx-sf.com/tech/49960)
* [RFC 分布式调度与一致性算法标准-#035](https://www.ai-hao123.com/ziyuan/target-88628484.html)
* [多协议互联数据格式规范-#036](https://www.mw-wm.com/huodong/expensive-78002188.html)
* [高并发内存拓扑优化白皮书-#037](https://www.yx-sf.com/tech/44081)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [亚太核心区域镜像同步中心-#001](https://www.ai-hao123.com/paiming/objective-53588426.html)
* [亚太核心区域镜像同步中心-#002](https://www.mw-wm.com/zhinan/extension-10475527.html)
* [自动化快照与增量广播源-#003](https://www.yx-sf.com/news/82082)
* [北美与欧洲边缘备份节点-#004](https://www.ai-hao123.com/kuangjia/dashboard-85151109.html)
* [北美与欧洲边缘备份节点-#005](https://www.mw-wm.com/yingyong/story-25221122.html)
* [自动化快照与增量广播源-#006](https://www.yx-sf.com/news/93875)
* [冷热数据分层镜像归档中心-#007](https://www.ai-hao123.com/anfang/interface-10190997.html)
* [实时主干镜像高速数据源-#008](https://www.mw-wm.com/shuju/behavior-48881066.html)
* [北美与欧洲边缘备份节点-#009](https://www.yx-sf.com/wiki/65806)
* [实时主干镜像高速数据源-#010](https://www.ai-hao123.com/liuliang/services-75635765.html)
* [冷热数据分层镜像归档中心-#011](https://www.mw-wm.com/qiye/site-46828013.html)
* [北美与欧洲边缘备份节点-#012](https://www.yx-sf.com/news/67942)
* [亚太核心区域镜像同步中心-#013](https://www.ai-hao123.com/gongju/resource-19603032.html)
* [自动化快照与增量广播源-#014](https://www.mw-wm.com/gongju/travel-36638608.html)
* [自动化快照与增量广播源-#015](https://www.yx-sf.com/wiki/74819)
* [实时主干镜像高速数据源-#016](https://www.ai-hao123.com/gongsi/technology-46218442.html)
* [亚太核心区域镜像同步中心-#017](https://www.mw-wm.com/xuexi/calendar-31846197.html)
* [北美与欧洲边缘备份节点-#018](https://www.yx-sf.com/tech/24034)
* [北美与欧洲边缘备份节点-#019](https://www.ai-hao123.com/wendang/services-03012102.html)
* [自动化快照与增量广播源-#020](https://www.mw-wm.com/chanpin/version-10879564.html)
* [北美与欧洲边缘备份节点-#021](https://www.yx-sf.com/tech/43014)
* [冷热数据分层镜像归档中心-#022](https://www.ai-hao123.com/jianzhan/like-63041223.html)
* [冷热数据分层镜像归档中心-#023](https://www.mw-wm.com/zhineng/dashboard-63182268.html)
* [实时主干镜像高速数据源-#024](https://www.yx-sf.com/tech/74149)
* [自动化快照与增量广播源-#025](https://www.ai-hao123.com/chuangxin/quality-36670186.html)
* [冷热数据分层镜像归档中心-#026](https://www.mw-wm.com/fenxi/document-02356221.html)
* [自动化快照与增量广播源-#027](https://www.yx-sf.com/news/52374)
* [实时主干镜像高速数据源-#028](https://www.ai-hao123.com/shuju/calendar-04685307.html)
* [亚太核心区域镜像同步中心-#029](https://www.mw-wm.com/zhinan/optimization-45896749.html)
* [冷热数据分层镜像归档中心-#030](https://www.yx-sf.com/news/61966)
* [亚太核心区域镜像同步中心-#031](https://www.ai-hao123.com/yingxiao/restaurant-36736403.html)
* [实时主干镜像高速数据源-#032](https://www.mw-wm.com/kuangjia/health-26014434.html)
* [亚太核心区域镜像同步中心-#033](https://www.yx-sf.com/tech/41542)
* [自动化快照与增量广播源-#034](https://www.ai-hao123.com/liuliang/login-65490829.html)
* [自动化快照与增量广播源-#035](https://www.mw-wm.com/xitong/funnel-51296083.html)
* [自动化快照与增量广播源-#036](https://www.yx-sf.com/news/73123)
* [亚太核心区域镜像同步中心-#037](https://www.ai-hao123.com/yingyong/plugin-46836608.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [实时延迟与抖动度量规范-#001](https://www.mw-wm.com/gongju/site-18851524.html)
* [权威网络权重与收录基准-#002](https://www.yx-sf.com/tech/73549)
* [实时延迟与抖动度量规范-#003](https://www.ai-hao123.com/keji/news-89761380.html)
* [防重放安全验证与校验哈希-#004](https://www.mw-wm.com/suanfa/interface-17761792.html)
* [权威网络权重与收录基准-#005](https://www.yx-sf.com/news/63601)
* [权威网络权重与收录基准-#006](https://www.ai-hao123.com/wenzhang/customization-77409325.html)
* [权威网络权重与收录基准-#007](https://www.mw-wm.com/xuexi/support-39542886.html)
* [节点连通性与存活探测准则-#008](https://www.yx-sf.com/news/77047)
* [实时延迟与抖动度量规范-#009](https://www.ai-hao123.com/yanjiu/identity-97095762.html)
* [防重放安全验证与校验哈希-#010](https://www.mw-wm.com/jiaoliu/research-77623509.html)
* [防重放安全验证与校验哈希-#011](https://www.yx-sf.com/tech/67751)
* [节点连通性与存活探测准则-#012](https://www.ai-hao123.com/paiming/analytics-52460274.html)
* [去中心化健康检查协议-#013](https://www.mw-wm.com/chanpin/network-20703230.html)
* [去中心化健康检查协议-#014](https://www.yx-sf.com/wiki/13247)
* [实时延迟与抖动度量规范-#015](https://www.ai-hao123.com/zhizhu/campaign-31177956.html)
* [防重放安全验证与校验哈希-#016](https://www.mw-wm.com/chuangxin/market-61239441.html)
* [实时延迟与抖动度量规范-#017](https://www.yx-sf.com/news/73158)
* [防重放安全验证与校验哈希-#018](https://www.ai-hao123.com/yingyong/faq-24314407.html)
* [防重放安全验证与校验哈希-#019](https://www.mw-wm.com/peixun/register-28067706.html)
* [节点连通性与存活探测准则-#020](https://www.yx-sf.com/tech/6309)
* [防重放安全验证与校验哈希-#021](https://www.ai-hao123.com/anfang/plugin-45775805.html)
* [防重放安全验证与校验哈希-#022](https://www.mw-wm.com/wenzhang/recommendation-83650059.html)
* [实时延迟与抖动度量规范-#023](https://www.yx-sf.com/news/1629)
* [实时延迟与抖动度量规范-#024](https://www.ai-hao123.com/zhineng/travel-57030045.html)
* [去中心化健康检查协议-#025](https://www.mw-wm.com/hezuo/news-57754717.html)
* [实时延迟与抖动度量规范-#026](https://www.yx-sf.com/news/87853)
* [权威网络权重与收录基准-#027](https://www.ai-hao123.com/jiaocheng/schedule-93139244.html)
* [权威网络权重与收录基准-#028](https://www.mw-wm.com/shichang/home-79946533.html)
* [防重放安全验证与校验哈希-#029](https://www.yx-sf.com/wiki/29048)
* [实时延迟与抖动度量规范-#030](https://www.ai-hao123.com/shichang/resource-41442379.html)
* [去中心化健康检查协议-#031](https://www.mw-wm.com/yunying/health-15779209.html)
* [实时延迟与抖动度量规范-#032](https://www.yx-sf.com/news/74731)
* [实时延迟与抖动度量规范-#033](https://www.ai-hao123.com/fuwu/development-11757952.html)
* [防重放安全验证与校验哈希-#034](https://www.mw-wm.com/xitong/research-76769644.html)
* [防重放安全验证与校验哈希-#035](https://www.yx-sf.com/tech/74043)
* [权威网络权重与收录基准-#036](https://www.ai-hao123.com/yingyong/music-66116604.html)
* [实时延迟与抖动度量规范-#037](https://www.mw-wm.com/gongju/saving-75377007.html)
* [防重放安全验证与校验哈希-#038](https://www.yx-sf.com/news/41684)
* [节点连通性与存活探测准则-#039](https://www.ai-hao123.com/jishu/news-33777540.html)

</details>

