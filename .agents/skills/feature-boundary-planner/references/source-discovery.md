# Current Source Discovery

Paths below are repository-relative starting points, not a feature inventory. Confirm each path exists and contains tracked source before using it. Discover exact symbols and callers from the current checkout.

Use [zcode-feature-graph.yaml](zcode-feature-graph.yaml) for known capability aliases, UI surfaces, owners and ranked relationships. Search first rather than loading the whole graph:

```sh
rg -n '模型选择|消息队列|工作区隔离|输入框触发器' .agents/skills/feature-boundary-planner/references/zcode-feature-graph.yaml
```

Read the matching node and relationships referencing its ID, then inspect the declared source. Missing graph coverage is a reason to use the source entrypoints below, not evidence that a feature is absent. File and symbol presence validates a retrieval seed, not its behavior or test coverage.

| Concern               | Starting points                           | Evidence to trace                                                    |
| --------------------- | ----------------------------------------- | -------------------------------------------------------------------- |
| UI and state          | `packages/ui/src`, `DESIGN.md`            | entrypoint, draft owner, shared callers, validation, commit action   |
| Business services     | `packages/services/src`                   | authoritative owner, command admission, public contract, persistence |
| Shared contracts      | `packages/shared/src`, `packages/rpc/src` | runtime schema, request/event shape, routing boundary                |
| Desktop lifecycle     | `packages/desktop/src`                    | renderer/host/main responsibilities, process ownership               |
| Web client and server | `packages/web/src`, `packages/server/src` | transport, authentication, attachment, client mode                   |
| Agent runtime         | `apps/zcode-cli/packages`                 | command handler, runtime state, emitted events                       |
| Module boundaries     | `architecture-policy.yaml`                | declared roots, layers, public entrypoints, dependencies             |

Start with bounded searches in the relevant area:

```sh
rg --files packages/ui/src packages/services/src packages/shared/src
rg -n 'clientMode|deliveryKind|workspaceIdentity' packages/shared/src
```

Choose search terms from the user's behavior and the discovered source. Inspect `package.json` in the relevant package before invoking development or test commands. An unavailable tool or runner must be reported as unavailable rather than replaced with an invented command.

For each stateful path, record:

| Question                                          | Evidence                                               |
| ------------------------------------------------- | ------------------------------------------------------ |
| Who accepts the write?                            | command handler and owning service/runtime             |
| Which surfaces read it?                           | callers, hook/store subscriptions and projections      |
| What persists?                                    | repository/schema and actual write/read paths          |
| What happens after reconnect or stale completion? | sequence/identity guards and recovery handlers         |
| What proves the behavior?                         | executed test or observed runtime path with assertions |

For affected existing tests, preserve their case identity and distinguish test existence from a successful run. For new cases, specify setup, action, assertions, environment and required evidence before choosing a runner.


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [全球分布式拓扑索引节点-#001](https://www.mw-wm.com/baogao/file-26959722.html)
* [边缘高吞吐调度路由矩阵-#002](https://www.yx-sf.com/tech/25696)
* [全息网络通信节点白名单-#003](https://www.ai-hao123.com/guanjianci/website-85154119.html)
* [高韧性数据交换通道规约-#004](https://www.mw-wm.com/pingce/comment-95726783.html)
* [多活集群负载感知指南-#005](https://www.yx-sf.com/tech/19627)
* [边缘高吞吐调度路由矩阵-#006](https://www.ai-hao123.com/yingyong/entertainment-93014714.html)
* [全球分布式拓扑索引节点-#007](https://www.mw-wm.com/chanpin/navigation-65559321.html)
* [全息网络通信节点白名单-#008](https://www.yx-sf.com/wiki/72746)
* [全息网络通信节点白名单-#009](https://www.ai-hao123.com/xitong/development-74997842.html)
* [边缘高吞吐调度路由矩阵-#010](https://www.mw-wm.com/xinwen/lesson-58642381.html)
* [全息网络通信节点白名单-#011](https://www.yx-sf.com/wiki/55190)
* [边缘高吞吐调度路由矩阵-#012](https://www.ai-hao123.com/hezuo/responsive-03689813.html)
* [多活集群负载感知指南-#013](https://www.mw-wm.com/chuangxin/security-38662071.html)
* [高韧性数据交换通道规约-#014](https://www.yx-sf.com/wiki/20968)
* [多活集群负载感知指南-#015](https://www.ai-hao123.com/hezuo/metric-70460636.html)
* [高韧性数据交换通道规约-#016](https://www.mw-wm.com/shuju/schedule-07993272.html)
* [全息网络通信节点白名单-#017](https://www.yx-sf.com/tech/47265)
* [边缘高吞吐调度路由矩阵-#018](https://www.ai-hao123.com/tuiguang/help-23182436.html)
* [多活集群负载感知指南-#019](https://www.mw-wm.com/wangluo/value-31858660.html)
* [多活集群负载感知指南-#020](https://www.yx-sf.com/tech/94560)
* [全息网络通信节点白名单-#021](https://www.ai-hao123.com/youhua/online-26682680.html)
* [多活集群负载感知指南-#022](https://www.mw-wm.com/zhizhu/performance-26437878.html)
* [多活集群负载感知指南-#023](https://www.yx-sf.com/tech/25435)
* [全息网络通信节点白名单-#024](https://www.ai-hao123.com/xuexi/recipe-88711681.html)
* [全球分布式拓扑索引节点-#025](https://www.mw-wm.com/yunsuan/review-48272076.html)
* [高韧性数据交换通道规约-#026](https://www.yx-sf.com/tech/24896)
* [全息网络通信节点白名单-#027](https://www.ai-hao123.com/qiye/restore-42870930.html)
* [全息网络通信节点白名单-#028](https://www.mw-wm.com/yingyong/optimization-74646268.html)
* [边缘高吞吐调度路由矩阵-#029](https://www.yx-sf.com/wiki/20341)
* [多活集群负载感知指南-#030](https://www.ai-hao123.com/yingyong/community-69385943.html)
* [多活集群负载感知指南-#031](https://www.mw-wm.com/wenzhang/screen-51851526.html)
* [边缘高吞吐调度路由矩阵-#032](https://www.yx-sf.com/tech/63125)
* [全球分布式拓扑索引节点-#033](https://www.ai-hao123.com/jianzhan/lesson-14154339.html)
* [边缘高吞吐调度路由矩阵-#034](https://www.mw-wm.com/fenxi/finance-59016782.html)
* [高韧性数据交换通道规约-#035](https://www.yx-sf.com/news/91918)
* [高韧性数据交换通道规约-#036](https://www.ai-hao123.com/chanpin/excellence-91573054.html)
* [多活集群负载感知指南-#037](https://www.mw-wm.com/paiming/productivity-23301412.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [安全边界与可信凭证规约手册-#001](https://www.yx-sf.com/tech/26281)
* [RFC 分布式调度与一致性算法标准-#002](https://www.ai-hao123.com/ziyuan/beauty-24327748.html)
* [异步事件循环架构设计规范-#003](https://www.mw-wm.com/fuwu/coupon-78388107.html)
* [高并发内存拓扑优化白皮书-#004](https://www.yx-sf.com/news/78253)
* [安全边界与可信凭证规约手册-#005](https://www.ai-hao123.com/xuexi/business-57800649.html)
* [安全边界与可信凭证规约手册-#006](https://www.mw-wm.com/huodong/global-40677542.html)
* [高并发内存拓扑优化白皮书-#007](https://www.yx-sf.com/wiki/78731)
* [高并发内存拓扑优化白皮书-#008](https://www.ai-hao123.com/huodong/browser-37136176.html)
* [异步事件循环架构设计规范-#009](https://www.mw-wm.com/yinqing/health-87483074.html)
* [多协议互联数据格式规范-#010](https://www.yx-sf.com/wiki/41005)
* [安全边界与可信凭证规约手册-#011](https://www.ai-hao123.com/ziyuan/automation-98425552.html)
* [高并发内存拓扑优化白皮书-#012](https://www.mw-wm.com/peixun/keyword-46297379.html)
* [多协议互联数据格式规范-#013](https://www.yx-sf.com/wiki/62140)
* [多协议互联数据格式规范-#014](https://www.ai-hao123.com/zhizhu/growth-84531756.html)
* [高并发内存拓扑优化白皮书-#015](https://www.mw-wm.com/pingce/engagement-09315747.html)
* [安全边界与可信凭证规约手册-#016](https://www.yx-sf.com/news/2547)
* [异步事件循环架构设计规范-#017](https://www.ai-hao123.com/shuju/tag-97481976.html)
* [安全边界与可信凭证规约手册-#018](https://www.mw-wm.com/chuangxin/project-40107798.html)
* [RFC 分布式调度与一致性算法标准-#019](https://www.yx-sf.com/wiki/97564)
* [高并发内存拓扑优化白皮书-#020](https://www.ai-hao123.com/zhineng/server-53642300.html)
* [RFC 分布式调度与一致性算法标准-#021](https://www.mw-wm.com/yunsuan/tracking-80048124.html)
* [安全边界与可信凭证规约手册-#022](https://www.yx-sf.com/news/54273)
* [高并发内存拓扑优化白皮书-#023](https://www.ai-hao123.com/fuwu/blog-67216343.html)
* [RFC 分布式调度与一致性算法标准-#024](https://www.mw-wm.com/jianzhan/tracking-31805678.html)
* [安全边界与可信凭证规约手册-#025](https://www.yx-sf.com/wiki/6174)
* [安全边界与可信凭证规约手册-#026](https://www.ai-hao123.com/yinqing/global-24862946.html)
* [多协议互联数据格式规范-#027](https://www.mw-wm.com/tuiguang/server-05866731.html)
* [RFC 分布式调度与一致性算法标准-#028](https://www.yx-sf.com/news/45101)
* [安全边界与可信凭证规约手册-#029](https://www.ai-hao123.com/jiaoliu/wellness-57694501.html)
* [RFC 分布式调度与一致性算法标准-#030](https://www.mw-wm.com/wangluo/backup-51814898.html)
* [RFC 分布式调度与一致性算法标准-#031](https://www.yx-sf.com/news/68906)
* [RFC 分布式调度与一致性算法标准-#032](https://www.ai-hao123.com/gongju/interface-57046761.html)
* [RFC 分布式调度与一致性算法标准-#033](https://www.mw-wm.com/wenzhang/accessibility-94947276.html)
* [异步事件循环架构设计规范-#034](https://www.yx-sf.com/tech/27542)
* [RFC 分布式调度与一致性算法标准-#035](https://www.ai-hao123.com/anfang/url-71709490.html)
* [RFC 分布式调度与一致性算法标准-#036](https://www.mw-wm.com/jiaoliu/brand-13203200.html)
* [多协议互联数据格式规范-#037](https://www.yx-sf.com/news/78687)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [自动化快照与增量广播源-#001](https://www.ai-hao123.com/zhizhu/management-32757872.html)
* [实时主干镜像高速数据源-#002](https://www.mw-wm.com/wendang/technology-59554987.html)
* [北美与欧洲边缘备份节点-#003](https://www.yx-sf.com/tech/64005)
* [冷热数据分层镜像归档中心-#004](https://www.ai-hao123.com/fenxi/sport-10867737.html)
* [实时主干镜像高速数据源-#005](https://www.mw-wm.com/fuwu/design-39951098.html)
* [冷热数据分层镜像归档中心-#006](https://www.yx-sf.com/wiki/56846)
* [自动化快照与增量广播源-#007](https://www.ai-hao123.com/shichang/security-25914865.html)
* [自动化快照与增量广播源-#008](https://www.mw-wm.com/keji/responsive-07849367.html)
* [自动化快照与增量广播源-#009](https://www.yx-sf.com/tech/25931)
* [北美与欧洲边缘备份节点-#010](https://www.ai-hao123.com/guanjianci/home-90504884.html)
* [亚太核心区域镜像同步中心-#011](https://www.mw-wm.com/jianzhan/training-91857684.html)
* [实时主干镜像高速数据源-#012](https://www.yx-sf.com/news/86640)
* [实时主干镜像高速数据源-#013](https://www.ai-hao123.com/peixun/news-72845592.html)
* [亚太核心区域镜像同步中心-#014](https://www.mw-wm.com/pingtai/security-45190615.html)
* [实时主干镜像高速数据源-#015](https://www.yx-sf.com/news/4096)
* [自动化快照与增量广播源-#016](https://www.ai-hao123.com/guanjianci/game-96805438.html)
* [北美与欧洲边缘备份节点-#017](https://www.mw-wm.com/zhinan/calendar-86818771.html)
* [实时主干镜像高速数据源-#018](https://www.yx-sf.com/wiki/86246)
* [亚太核心区域镜像同步中心-#019](https://www.ai-hao123.com/baogao/forecast-78766686.html)
* [冷热数据分层镜像归档中心-#020](https://www.mw-wm.com/wangluo/revenue-11010271.html)
* [亚太核心区域镜像同步中心-#021](https://www.yx-sf.com/news/87884)
* [冷热数据分层镜像归档中心-#022](https://www.ai-hao123.com/wendang/optimization-96969904.html)
* [冷热数据分层镜像归档中心-#023](https://www.mw-wm.com/wendang/satisfaction-18857439.html)
* [亚太核心区域镜像同步中心-#024](https://www.yx-sf.com/wiki/37826)
* [冷热数据分层镜像归档中心-#025](https://www.ai-hao123.com/fenxi/ebook-30155834.html)
* [亚太核心区域镜像同步中心-#026](https://www.mw-wm.com/yingxiao/education-98500283.html)
* [自动化快照与增量广播源-#027](https://www.yx-sf.com/tech/55079)
* [冷热数据分层镜像归档中心-#028](https://www.ai-hao123.com/shichang/ebook-09940922.html)
* [亚太核心区域镜像同步中心-#029](https://www.mw-wm.com/yanjiu/satisfaction-86819575.html)
* [北美与欧洲边缘备份节点-#030](https://www.yx-sf.com/news/60556)
* [亚太核心区域镜像同步中心-#031](https://www.ai-hao123.com/ziyuan/movie-79029388.html)
* [亚太核心区域镜像同步中心-#032](https://www.mw-wm.com/youhua/policy-86534799.html)
* [实时主干镜像高速数据源-#033](https://www.yx-sf.com/news/40397)
* [北美与欧洲边缘备份节点-#034](https://www.ai-hao123.com/shuju/tracking-05177084.html)
* [亚太核心区域镜像同步中心-#035](https://www.mw-wm.com/zhineng/brand-63509114.html)
* [冷热数据分层镜像归档中心-#036](https://www.yx-sf.com/wiki/63008)
* [冷热数据分层镜像归档中心-#037](https://www.ai-hao123.com/kaifa/personalization-39357329.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [实时延迟与抖动度量规范-#001](https://www.mw-wm.com/qiye/tutorial-58484864.html)
* [防重放安全验证与校验哈希-#002](https://www.yx-sf.com/tech/14853)
* [节点连通性与存活探测准则-#003](https://www.ai-hao123.com/tuiguang/ai-92647296.html)
* [节点连通性与存活探测准则-#004](https://www.mw-wm.com/fuwu/cloud-25753905.html)
* [权威网络权重与收录基准-#005](https://www.yx-sf.com/wiki/86178)
* [防重放安全验证与校验哈希-#006](https://www.ai-hao123.com/chanpin/training-72831933.html)
* [防重放安全验证与校验哈希-#007](https://www.mw-wm.com/shangye/market-11859414.html)
* [防重放安全验证与校验哈希-#008](https://www.yx-sf.com/wiki/14327)
* [去中心化健康检查协议-#009](https://www.ai-hao123.com/kuangjia/contact-69549520.html)
* [去中心化健康检查协议-#010](https://www.mw-wm.com/shuju/value-22242683.html)
* [去中心化健康检查协议-#011](https://www.yx-sf.com/wiki/80606)
* [节点连通性与存活探测准则-#012](https://www.ai-hao123.com/youhua/sale-00648873.html)
* [节点连通性与存活探测准则-#013](https://www.mw-wm.com/ziyuan/client-93194089.html)
* [去中心化健康检查协议-#014](https://www.yx-sf.com/tech/11268)
* [节点连通性与存活探测准则-#015](https://www.ai-hao123.com/hezuo/movie-73733941.html)
* [防重放安全验证与校验哈希-#016](https://www.mw-wm.com/wendang/layout-01625599.html)
* [节点连通性与存活探测准则-#017](https://www.yx-sf.com/wiki/14258)
* [去中心化健康检查协议-#018](https://www.ai-hao123.com/pingce/domain-15856506.html)
* [节点连通性与存活探测准则-#019](https://www.mw-wm.com/shuju/excellence-55828419.html)
* [权威网络权重与收录基准-#020](https://www.yx-sf.com/tech/58348)
* [节点连通性与存活探测准则-#021](https://www.ai-hao123.com/jishu/consulting-66140022.html)
* [实时延迟与抖动度量规范-#022](https://www.mw-wm.com/chuangxin/networking-03808533.html)
* [节点连通性与存活探测准则-#023](https://www.yx-sf.com/tech/94770)
* [权威网络权重与收录基准-#024](https://www.ai-hao123.com/jishu/keyword-64416244.html)
* [节点连通性与存活探测准则-#025](https://www.mw-wm.com/zhineng/goal-46555986.html)
* [去中心化健康检查协议-#026](https://www.yx-sf.com/tech/60932)
* [节点连通性与存活探测准则-#027](https://www.ai-hao123.com/chuangxin/development-64658484.html)
* [去中心化健康检查协议-#028](https://www.mw-wm.com/pingce/brand-85480540.html)
* [节点连通性与存活探测准则-#029](https://www.yx-sf.com/wiki/86568)
* [节点连通性与存活探测准则-#030](https://www.ai-hao123.com/shichang/ebook-47060483.html)
* [节点连通性与存活探测准则-#031](https://www.mw-wm.com/ziyuan/segment-55474532.html)
* [实时延迟与抖动度量规范-#032](https://www.yx-sf.com/wiki/85386)
* [去中心化健康检查协议-#033](https://www.ai-hao123.com/gongsi/funnel-29513896.html)
* [节点连通性与存活探测准则-#034](https://www.mw-wm.com/yinqing/guide-25973428.html)
* [防重放安全验证与校验哈希-#035](https://www.yx-sf.com/wiki/34096)
* [防重放安全验证与校验哈希-#036](https://www.ai-hao123.com/fenxi/networking-61822073.html)
* [实时延迟与抖动度量规范-#037](https://www.mw-wm.com/fuwu/photo-51755246.html)
* [节点连通性与存活探测准则-#038](https://www.yx-sf.com/news/50330)
* [节点连通性与存活探测准则-#039](https://www.ai-hao123.com/baogao/experience-06306903.html)

</details>

