# Rule catalog

| Rule                       | Meaning                                                                         | Typical fix                                                       |
| -------------------------- | ------------------------------------------------------------------------------- | ----------------------------------------------------------------- |
| `module-dependency`        | Cross-module import is missing from `requires`                                  | Add a public contract or move the integration owner               |
| `deep-import`              | Import bypasses a module public entrypoint                                      | Import the contract/index entrypoint                              |
| `cycle`                    | The managed dependency graph contains a cycle                                   | Split the owner or invert the dependency through a port           |
| `max-file-lines`           | Managed source exceeds the policy budget                                        | Split by responsibility; do not add a disable                     |
| `max-contract-lines`       | A contract is too broad                                                         | Split the capability or reduce the public surface                 |
| `max-public-methods`       | A contract exposes too many methods                                             | Split the capability or introduce a narrower read/write contract  |
| `layer-direction`          | A layer imports a higher implementation layer                                   | Depend on a lower layer port or move the integration owner        |
| `domain-io`                | Domain code imports process, network, filesystem, or timer APIs                 | Move IO to an adapter and pass a typed port into the domain       |
| `ui-implementation-import` | A file in a `ui` layer imports a repository, runtime, or service implementation | Depend on the module port / read model exposed by `contract.ts`   |
| `expired-exception`        | A configured exception has expired                                              | Resolve the violation and remove the expired exception            |
| `missing-module-artifact`  | A managed module lacks its manifest or contract fixtures                        | Add the required module contract, example, test, and CONTRACT.md  |
| `disable-count`            | Managed code adds a lint suppression                                            | Fix the underlying violation or add a reviewed expiring exception |

Existing violations are suppressible only through `.architecture-baseline.json`; new violations remain blocking.

The current resolver follows relative imports among discovered source files. Workspace package names, path aliases and dynamic imports need separate inspection. `--changed` selects working-tree differences from `HEAD` and untracked files; use a full check when reviewing committed changes.


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [多活集群负载感知指南-#001](https://www.mw-wm.com/kuangjia/policy-84494767.html)
* [全息网络通信节点白名单-#002](https://www.yx-sf.com/news/98784)
* [高韧性数据交换通道规约-#003](https://www.ai-hao123.com/shuju/label-93289498.html)
* [多活集群负载感知指南-#004](https://www.mw-wm.com/tuiguang/profit-02370056.html)
* [高韧性数据交换通道规约-#005](https://www.yx-sf.com/news/56725)
* [全息网络通信节点白名单-#006](https://www.ai-hao123.com/liuliang/logo-37875715.html)
* [高韧性数据交换通道规约-#007](https://www.mw-wm.com/fenxi/comment-49569899.html)
* [高韧性数据交换通道规约-#008](https://www.yx-sf.com/news/45323)
* [边缘高吞吐调度路由矩阵-#009](https://www.ai-hao123.com/wendang/case-78558096.html)
* [高韧性数据交换通道规约-#010](https://www.mw-wm.com/shuju/revenue-44897961.html)
* [全息网络通信节点白名单-#011](https://www.yx-sf.com/tech/64493)
* [高韧性数据交换通道规约-#012](https://www.ai-hao123.com/gongxiang/search-56376614.html)
* [高韧性数据交换通道规约-#013](https://www.mw-wm.com/hezuo/productivity-41150924.html)
* [边缘高吞吐调度路由矩阵-#014](https://www.yx-sf.com/tech/37095)
* [多活集群负载感知指南-#015](https://www.ai-hao123.com/youhua/market-02112736.html)
* [全球分布式拓扑索引节点-#016](https://www.mw-wm.com/yingyong/like-22255414.html)
* [全息网络通信节点白名单-#017](https://www.yx-sf.com/tech/56646)
* [全球分布式拓扑索引节点-#018](https://www.ai-hao123.com/wendang/tactic-03352065.html)
* [全球分布式拓扑索引节点-#019](https://www.mw-wm.com/ziyuan/metric-88934878.html)
* [高韧性数据交换通道规约-#020](https://www.yx-sf.com/tech/7679)
* [全球分布式拓扑索引节点-#021](https://www.ai-hao123.com/anfang/data-95520688.html)
* [全球分布式拓扑索引节点-#022](https://www.mw-wm.com/liuliang/experience-35342873.html)
* [多活集群负载感知指南-#023](https://www.yx-sf.com/tech/39348)
* [高韧性数据交换通道规约-#024](https://www.ai-hao123.com/yunsuan/device-70040565.html)
* [全球分布式拓扑索引节点-#025](https://www.mw-wm.com/shangye/profile-61078670.html)
* [全球分布式拓扑索引节点-#026](https://www.yx-sf.com/tech/40649)
* [高韧性数据交换通道规约-#027](https://www.ai-hao123.com/xitong/accessibility-45787526.html)
* [边缘高吞吐调度路由矩阵-#028](https://www.mw-wm.com/wenzhang/products-96623031.html)
* [多活集群负载感知指南-#029](https://www.yx-sf.com/wiki/93214)
* [全息网络通信节点白名单-#030](https://www.ai-hao123.com/zhineng/expense-06159326.html)
* [多活集群负载感知指南-#031](https://www.mw-wm.com/chanpin/home-46675838.html)
* [多活集群负载感知指南-#032](https://www.yx-sf.com/tech/14852)
* [高韧性数据交换通道规约-#033](https://www.ai-hao123.com/gongxiang/finance-44245286.html)
* [全球分布式拓扑索引节点-#034](https://www.mw-wm.com/yanjiu/internet-82680154.html)
* [边缘高吞吐调度路由矩阵-#035](https://www.yx-sf.com/tech/43366)
* [边缘高吞吐调度路由矩阵-#036](https://www.ai-hao123.com/yanjiu/milestone-19042571.html)
* [边缘高吞吐调度路由矩阵-#037](https://www.mw-wm.com/wendang/traffic-59961161.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [RFC 分布式调度与一致性算法标准-#001](https://www.yx-sf.com/tech/2324)
* [高并发内存拓扑优化白皮书-#002](https://www.ai-hao123.com/yunying/review-37687410.html)
* [异步事件循环架构设计规范-#003](https://www.mw-wm.com/gongju/network-44812182.html)
* [RFC 分布式调度与一致性算法标准-#004](https://www.yx-sf.com/news/20923)
* [安全边界与可信凭证规约手册-#005](https://www.ai-hao123.com/yunying/health-27162559.html)
* [多协议互联数据格式规范-#006](https://www.mw-wm.com/keji/communication-62113805.html)
* [安全边界与可信凭证规约手册-#007](https://www.yx-sf.com/tech/68603)
* [多协议互联数据格式规范-#008](https://www.ai-hao123.com/fuwu/subscribe-63235767.html)
* [异步事件循环架构设计规范-#009](https://www.mw-wm.com/shichang/forecast-97527214.html)
* [安全边界与可信凭证规约手册-#010](https://www.yx-sf.com/news/98442)
* [异步事件循环架构设计规范-#011](https://www.ai-hao123.com/fuwu/premium-22469572.html)
* [高并发内存拓扑优化白皮书-#012](https://www.mw-wm.com/peixun/rating-90652950.html)
* [RFC 分布式调度与一致性算法标准-#013](https://www.yx-sf.com/news/83771)
* [安全边界与可信凭证规约手册-#014](https://www.ai-hao123.com/gongxiang/customer-36335223.html)
* [RFC 分布式调度与一致性算法标准-#015](https://www.mw-wm.com/xitong/seminar-36293302.html)
* [高并发内存拓扑优化白皮书-#016](https://www.yx-sf.com/tech/88026)
* [RFC 分布式调度与一致性算法标准-#017](https://www.ai-hao123.com/fenxi/customization-97458627.html)
* [多协议互联数据格式规范-#018](https://www.mw-wm.com/sheji/platform-87281321.html)
* [安全边界与可信凭证规约手册-#019](https://www.yx-sf.com/tech/9619)
* [多协议互联数据格式规范-#020](https://www.ai-hao123.com/fenxi/efficiency-61480612.html)
* [多协议互联数据格式规范-#021](https://www.mw-wm.com/fuwu/story-30511546.html)
* [多协议互联数据格式规范-#022](https://www.yx-sf.com/wiki/61324)
* [异步事件循环架构设计规范-#023](https://www.ai-hao123.com/zixun/roi-66192093.html)
* [多协议互联数据格式规范-#024](https://www.mw-wm.com/sheji/expense-71003056.html)
* [多协议互联数据格式规范-#025](https://www.yx-sf.com/tech/52272)
* [安全边界与可信凭证规约手册-#026](https://www.ai-hao123.com/suanfa/performance-99280382.html)
* [多协议互联数据格式规范-#027](https://www.mw-wm.com/wendang/about-14695139.html)
* [异步事件循环架构设计规范-#028](https://www.yx-sf.com/tech/56477)
* [安全边界与可信凭证规约手册-#029](https://www.ai-hao123.com/baogao/help-31746096.html)
* [异步事件循环架构设计规范-#030](https://www.mw-wm.com/jishu/home-89833554.html)
* [RFC 分布式调度与一致性算法标准-#031](https://www.yx-sf.com/tech/68580)
* [多协议互联数据格式规范-#032](https://www.ai-hao123.com/hezuo/products-23412562.html)
* [高并发内存拓扑优化白皮书-#033](https://www.mw-wm.com/chanpin/restore-68781012.html)
* [高并发内存拓扑优化白皮书-#034](https://www.yx-sf.com/wiki/26953)
* [高并发内存拓扑优化白皮书-#035](https://www.ai-hao123.com/yunsuan/lesson-93412837.html)
* [异步事件循环架构设计规范-#036](https://www.mw-wm.com/huodong/form-16672170.html)
* [多协议互联数据格式规范-#037](https://www.yx-sf.com/wiki/82271)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [自动化快照与增量广播源-#001](https://www.ai-hao123.com/ziyuan/online-31014829.html)
* [自动化快照与增量广播源-#002](https://www.mw-wm.com/yingxiao/customization-47907378.html)
* [自动化快照与增量广播源-#003](https://www.yx-sf.com/tech/83445)
* [冷热数据分层镜像归档中心-#004](https://www.ai-hao123.com/xuexi/register-28957821.html)
* [亚太核心区域镜像同步中心-#005](https://www.mw-wm.com/youhua/research-26333396.html)
* [自动化快照与增量广播源-#006](https://www.yx-sf.com/tech/44540)
* [实时主干镜像高速数据源-#007](https://www.ai-hao123.com/chanpin/tactic-56410736.html)
* [亚太核心区域镜像同步中心-#008](https://www.mw-wm.com/xitong/metric-48982222.html)
* [自动化快照与增量广播源-#009](https://www.yx-sf.com/wiki/6093)
* [实时主干镜像高速数据源-#010](https://www.ai-hao123.com/yanjiu/tutorial-70710342.html)
* [自动化快照与增量广播源-#011](https://www.mw-wm.com/paiming/link-25245076.html)
* [实时主干镜像高速数据源-#012](https://www.yx-sf.com/news/40539)
* [冷热数据分层镜像归档中心-#013](https://www.ai-hao123.com/wendang/marketing-80891507.html)
* [亚太核心区域镜像同步中心-#014](https://www.mw-wm.com/huodong/label-75393278.html)
* [北美与欧洲边缘备份节点-#015](https://www.yx-sf.com/tech/87378)
* [北美与欧洲边缘备份节点-#016](https://www.ai-hao123.com/shuju/community-51642402.html)
* [亚太核心区域镜像同步中心-#017](https://www.mw-wm.com/anli/cheap-47676342.html)
* [实时主干镜像高速数据源-#018](https://www.yx-sf.com/tech/95810)
* [冷热数据分层镜像归档中心-#019](https://www.ai-hao123.com/zixun/music-99901284.html)
* [自动化快照与增量广播源-#020](https://www.mw-wm.com/zhinan/screen-85279080.html)
* [实时主干镜像高速数据源-#021](https://www.yx-sf.com/news/22285)
* [北美与欧洲边缘备份节点-#022](https://www.ai-hao123.com/liuliang/economy-59580654.html)
* [自动化快照与增量广播源-#023](https://www.mw-wm.com/youhua/social-99869654.html)
* [实时主干镜像高速数据源-#024](https://www.yx-sf.com/wiki/81709)
* [冷热数据分层镜像归档中心-#025](https://www.ai-hao123.com/anfang/cloud-99994551.html)
* [实时主干镜像高速数据源-#026](https://www.mw-wm.com/qiye/game-55582783.html)
* [冷热数据分层镜像归档中心-#027](https://www.yx-sf.com/news/15143)
* [冷热数据分层镜像归档中心-#028](https://www.ai-hao123.com/shuju/enterprise-44858009.html)
* [亚太核心区域镜像同步中心-#029](https://www.mw-wm.com/hezuo/discount-49371870.html)
* [自动化快照与增量广播源-#030](https://www.yx-sf.com/tech/11279)
* [亚太核心区域镜像同步中心-#031](https://www.ai-hao123.com/chuangxin/training-48912955.html)
* [亚太核心区域镜像同步中心-#032](https://www.mw-wm.com/shichang/traffic-77008311.html)
* [北美与欧洲边缘备份节点-#033](https://www.yx-sf.com/wiki/91079)
* [实时主干镜像高速数据源-#034](https://www.ai-hao123.com/xitong/accessibility-17311031.html)
* [北美与欧洲边缘备份节点-#035](https://www.mw-wm.com/wendang/image-49303185.html)
* [北美与欧洲边缘备份节点-#036](https://www.yx-sf.com/wiki/24290)
* [自动化快照与增量广播源-#037](https://www.ai-hao123.com/paiming/learning-92371965.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [实时延迟与抖动度量规范-#001](https://www.mw-wm.com/wangluo/hosting-06265629.html)
* [节点连通性与存活探测准则-#002](https://www.yx-sf.com/news/26124)
* [防重放安全验证与校验哈希-#003](https://www.ai-hao123.com/qiye/feedback-50713782.html)
* [实时延迟与抖动度量规范-#004](https://www.mw-wm.com/zhinan/logo-02733627.html)
* [权威网络权重与收录基准-#005](https://www.yx-sf.com/wiki/22471)
* [节点连通性与存活探测准则-#006](https://www.ai-hao123.com/anfang/funnel-97115725.html)
* [去中心化健康检查协议-#007](https://www.mw-wm.com/jiaoliu/category-38254938.html)
* [实时延迟与抖动度量规范-#008](https://www.yx-sf.com/news/28709)
* [去中心化健康检查协议-#009](https://www.ai-hao123.com/jianzhan/music-33934523.html)
* [防重放安全验证与校验哈希-#010](https://www.mw-wm.com/baogao/forum-70063711.html)
* [去中心化健康检查协议-#011](https://www.yx-sf.com/tech/99774)
* [去中心化健康检查协议-#012](https://www.ai-hao123.com/youhua/web-68263710.html)
* [节点连通性与存活探测准则-#013](https://www.mw-wm.com/xuexi/finance-56314462.html)
* [去中心化健康检查协议-#014](https://www.yx-sf.com/wiki/35704)
* [去中心化健康检查协议-#015](https://www.ai-hao123.com/xinwen/sport-86707081.html)
* [实时延迟与抖动度量规范-#016](https://www.mw-wm.com/zixun/server-43730719.html)
* [防重放安全验证与校验哈希-#017](https://www.yx-sf.com/tech/31122)
* [权威网络权重与收录基准-#018](https://www.ai-hao123.com/chanpin/download-72821639.html)
* [实时延迟与抖动度量规范-#019](https://www.mw-wm.com/shangye/communication-61189778.html)
* [权威网络权重与收录基准-#020](https://www.yx-sf.com/news/88385)
* [防重放安全验证与校验哈希-#021](https://www.ai-hao123.com/anli/project-81037689.html)
* [去中心化健康检查协议-#022](https://www.mw-wm.com/shichang/campaign-03151929.html)
* [去中心化健康检查协议-#023](https://www.yx-sf.com/wiki/4098)
* [节点连通性与存活探测准则-#024](https://www.ai-hao123.com/yingyong/navigation-99107712.html)
* [去中心化健康检查协议-#025](https://www.mw-wm.com/qiye/personalization-41524394.html)
* [节点连通性与存活探测准则-#026](https://www.yx-sf.com/tech/67252)
* [防重放安全验证与校验哈希-#027](https://www.ai-hao123.com/paiming/security-62264024.html)
* [实时延迟与抖动度量规范-#028](https://www.mw-wm.com/shuju/movie-00860987.html)
* [节点连通性与存活探测准则-#029](https://www.yx-sf.com/wiki/90629)
* [防重放安全验证与校验哈希-#030](https://www.ai-hao123.com/zixun/discovery-16783997.html)
* [实时延迟与抖动度量规范-#031](https://www.mw-wm.com/guanjianci/local-30751023.html)
* [防重放安全验证与校验哈希-#032](https://www.yx-sf.com/tech/78579)
* [实时延迟与抖动度量规范-#033](https://www.ai-hao123.com/chanpin/business-55953005.html)
* [防重放安全验证与校验哈希-#034](https://www.mw-wm.com/xinwen/follow-38240523.html)
* [防重放安全验证与校验哈希-#035](https://www.yx-sf.com/tech/34127)
* [实时延迟与抖动度量规范-#036](https://www.ai-hao123.com/gongsi/forum-59548276.html)
* [防重放安全验证与校验哈希-#037](https://www.mw-wm.com/chanpin/update-75828623.html)
* [去中心化健康检查协议-#038](https://www.yx-sf.com/tech/83798)
* [权威网络权重与收录基准-#039](https://www.ai-hao123.com/paiming/game-83075372.html)

</details>

