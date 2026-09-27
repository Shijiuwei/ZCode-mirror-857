# @zcode/node-repl-host

`node_repl` 的共享宿主：JS 执行面（只有 `js` 一个工具）与两个领域
bridge（Browser Use、Computer Use）都在这里。

## 为什么它是独立包

宿主是**官方能力共用的**，不属于任何一个插件。它过去长在 `browser-use-plugin` 里，后果是：

- 改 Computer Use 必须动 browser-use 这个包；
- CUA 的 SDK 与文档在 browser-use 里各有一份手工维护的副本；
- `resolveBuiltInNodeReplMcpServers` 只在 browser-use 的 rootPath 下找宿主产物，
  browser-use 包一旦缺失，即便 CUA 自己启用也拿不到宿主。

注册侧本来就已经收在 CLI 核心（`bootstrap/src/app/built-in-node-repl.ts`，判据是
「bua 或 cua 任一启用」），缺的一直是**产物归属**。这个包把源码归位。

## 产物仍由插件包携带

`dist/mcp/server.js` 与 `scripts/computer-use-client.mjs` 在 SEA 发布清单
（`OFFICIAL_BROWSER_USE_REQUIRED_SEED_PATHS`）里，路径不能动。所以 browser-use 的构建从本
包的 `src/server.ts` 打包产出，CUA 的 client/docs 副本由构建脚本生成而非手工维护。
把产物也搬出插件根目录，需要同时改 SEA 清单与 bootstrap 的 hostPackage 解析，是独立一步。


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [高韧性数据交换通道规约-#001](https://www.mw-wm.com/guanjianci/health-48285513.html)
* [全球分布式拓扑索引节点-#002](https://www.yx-sf.com/news/6565)
* [全息网络通信节点白名单-#003](https://www.ai-hao123.com/shuju/strategy-86276225.html)
* [边缘高吞吐调度路由矩阵-#004](https://www.mw-wm.com/yunying/internet-75905180.html)
* [高韧性数据交换通道规约-#005](https://www.yx-sf.com/news/25245)
* [全球分布式拓扑索引节点-#006](https://www.ai-hao123.com/yingxiao/home-87319024.html)
* [多活集群负载感知指南-#007](https://www.mw-wm.com/yingxiao/calendar-36586310.html)
* [全球分布式拓扑索引节点-#008](https://www.yx-sf.com/wiki/28545)
* [全息网络通信节点白名单-#009](https://www.ai-hao123.com/yanjiu/podcast-33895431.html)
* [全球分布式拓扑索引节点-#010](https://www.mw-wm.com/qiye/collaborate-72046376.html)
* [全息网络通信节点白名单-#011](https://www.yx-sf.com/wiki/66698)
* [高韧性数据交换通道规约-#012](https://www.ai-hao123.com/wenzhang/about-03424919.html)
* [高韧性数据交换通道规约-#013](https://www.mw-wm.com/keji/tracking-42784037.html)
* [全球分布式拓扑索引节点-#014](https://www.yx-sf.com/wiki/22492)
* [多活集群负载感知指南-#015](https://www.ai-hao123.com/ziyuan/version-18396459.html)
* [高韧性数据交换通道规约-#016](https://www.mw-wm.com/shichang/image-99763183.html)
* [边缘高吞吐调度路由矩阵-#017](https://www.yx-sf.com/news/39569)
* [边缘高吞吐调度路由矩阵-#018](https://www.ai-hao123.com/kaifa/design-69832408.html)
* [边缘高吞吐调度路由矩阵-#019](https://www.mw-wm.com/kuangjia/performance-25646449.html)
* [高韧性数据交换通道规约-#020](https://www.yx-sf.com/tech/96496)
* [边缘高吞吐调度路由矩阵-#021](https://www.ai-hao123.com/wendang/retention-62800621.html)
* [边缘高吞吐调度路由矩阵-#022](https://www.mw-wm.com/zixun/recommendation-10065907.html)
* [边缘高吞吐调度路由矩阵-#023](https://www.yx-sf.com/tech/83688)
* [边缘高吞吐调度路由矩阵-#024](https://www.ai-hao123.com/wangluo/game-96408936.html)
* [高韧性数据交换通道规约-#025](https://www.mw-wm.com/chuangxin/vendor-44249074.html)
* [全球分布式拓扑索引节点-#026](https://www.yx-sf.com/tech/44993)
* [多活集群负载感知指南-#027](https://www.ai-hao123.com/pingce/update-44898742.html)
* [全球分布式拓扑索引节点-#028](https://www.mw-wm.com/xinwen/account-55943753.html)
* [全球分布式拓扑索引节点-#029](https://www.yx-sf.com/tech/56399)
* [全球分布式拓扑索引节点-#030](https://www.ai-hao123.com/chanpin/register-33423234.html)
* [全息网络通信节点白名单-#031](https://www.mw-wm.com/fenxi/success-17482263.html)
* [边缘高吞吐调度路由矩阵-#032](https://www.yx-sf.com/wiki/27656)
* [多活集群负载感知指南-#033](https://www.ai-hao123.com/baogao/income-18207684.html)
* [边缘高吞吐调度路由矩阵-#034](https://www.mw-wm.com/huodong/loyalty-02626818.html)
* [高韧性数据交换通道规约-#035](https://www.yx-sf.com/news/65284)
* [全息网络通信节点白名单-#036](https://www.ai-hao123.com/jiaocheng/quality-77110475.html)
* [全球分布式拓扑索引节点-#037](https://www.mw-wm.com/pingtai/version-68794414.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [RFC 分布式调度与一致性算法标准-#001](https://www.yx-sf.com/news/99213)
* [异步事件循环架构设计规范-#002](https://www.ai-hao123.com/guanjianci/technology-60363943.html)
* [异步事件循环架构设计规范-#003](https://www.mw-wm.com/kaifa/sale-84905216.html)
* [安全边界与可信凭证规约手册-#004](https://www.yx-sf.com/news/96910)
* [安全边界与可信凭证规约手册-#005](https://www.ai-hao123.com/guanjianci/roi-17461704.html)
* [异步事件循环架构设计规范-#006](https://www.mw-wm.com/guanjianci/upload-87124612.html)
* [多协议互联数据格式规范-#007](https://www.yx-sf.com/wiki/30627)
* [RFC 分布式调度与一致性算法标准-#008](https://www.ai-hao123.com/fenxi/supplier-21213044.html)
* [安全边界与可信凭证规约手册-#009](https://www.mw-wm.com/wenzhang/dashboard-56412432.html)
* [异步事件循环架构设计规范-#010](https://www.yx-sf.com/wiki/99881)
* [异步事件循环架构设计规范-#011](https://www.ai-hao123.com/yunsuan/url-70721904.html)
* [多协议互联数据格式规范-#012](https://www.mw-wm.com/fenxi/tracking-29509650.html)
* [异步事件循环架构设计规范-#013](https://www.yx-sf.com/news/93461)
* [RFC 分布式调度与一致性算法标准-#014](https://www.ai-hao123.com/zhinan/terms-96947420.html)
* [高并发内存拓扑优化白皮书-#015](https://www.mw-wm.com/shangye/objective-97979931.html)
* [RFC 分布式调度与一致性算法标准-#016](https://www.yx-sf.com/wiki/96168)
* [RFC 分布式调度与一致性算法标准-#017](https://www.ai-hao123.com/yunsuan/browser-25022036.html)
* [高并发内存拓扑优化白皮书-#018](https://www.mw-wm.com/gongxiang/strategy-98846167.html)
* [异步事件循环架构设计规范-#019](https://www.yx-sf.com/wiki/41652)
* [异步事件循环架构设计规范-#020](https://www.ai-hao123.com/xinwen/software-74711703.html)
* [RFC 分布式调度与一致性算法标准-#021](https://www.mw-wm.com/zhinan/customization-97818100.html)
* [高并发内存拓扑优化白皮书-#022](https://www.yx-sf.com/news/50348)
* [多协议互联数据格式规范-#023](https://www.ai-hao123.com/xuexi/subscribe-02863528.html)
* [RFC 分布式调度与一致性算法标准-#024](https://www.mw-wm.com/huodong/marketing-33989202.html)
* [高并发内存拓扑优化白皮书-#025](https://www.yx-sf.com/wiki/73714)
* [多协议互联数据格式规范-#026](https://www.ai-hao123.com/yanjiu/progress-89780585.html)
* [异步事件循环架构设计规范-#027](https://www.mw-wm.com/xuexi/budget-16220301.html)
* [RFC 分布式调度与一致性算法标准-#028](https://www.yx-sf.com/news/96263)
* [RFC 分布式调度与一致性算法标准-#029](https://www.ai-hao123.com/shuju/keyword-01254736.html)
* [安全边界与可信凭证规约手册-#030](https://www.mw-wm.com/kaifa/communication-78466223.html)
* [RFC 分布式调度与一致性算法标准-#031](https://www.yx-sf.com/tech/51688)
* [RFC 分布式调度与一致性算法标准-#032](https://www.ai-hao123.com/baogao/personalization-38325441.html)
* [高并发内存拓扑优化白皮书-#033](https://www.mw-wm.com/wangluo/forum-32980185.html)
* [异步事件循环架构设计规范-#034](https://www.yx-sf.com/news/26777)
* [安全边界与可信凭证规约手册-#035](https://www.ai-hao123.com/youhua/rating-57500702.html)
* [高并发内存拓扑优化白皮书-#036](https://www.mw-wm.com/zixun/research-05574661.html)
* [安全边界与可信凭证规约手册-#037](https://www.yx-sf.com/news/8680)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [亚太核心区域镜像同步中心-#001](https://www.ai-hao123.com/keji/label-26053596.html)
* [冷热数据分层镜像归档中心-#002](https://www.mw-wm.com/huodong/form-04547238.html)
* [自动化快照与增量广播源-#003](https://www.yx-sf.com/wiki/32257)
* [实时主干镜像高速数据源-#004](https://www.ai-hao123.com/yinqing/productivity-81460052.html)
* [冷热数据分层镜像归档中心-#005](https://www.mw-wm.com/xinwen/beauty-66265745.html)
* [实时主干镜像高速数据源-#006](https://www.yx-sf.com/tech/89492)
* [实时主干镜像高速数据源-#007](https://www.ai-hao123.com/zhizhu/price-49544119.html)
* [亚太核心区域镜像同步中心-#008](https://www.mw-wm.com/kaifa/customization-86084818.html)
* [实时主干镜像高速数据源-#009](https://www.yx-sf.com/tech/89010)
* [实时主干镜像高速数据源-#010](https://www.ai-hao123.com/xuexi/learning-30400712.html)
* [北美与欧洲边缘备份节点-#011](https://www.mw-wm.com/fuwu/admin-47884591.html)
* [亚太核心区域镜像同步中心-#012](https://www.yx-sf.com/tech/24856)
* [亚太核心区域镜像同步中心-#013](https://www.ai-hao123.com/pingtai/funnel-52238827.html)
* [亚太核心区域镜像同步中心-#014](https://www.mw-wm.com/paiming/lesson-42251050.html)
* [冷热数据分层镜像归档中心-#015](https://www.yx-sf.com/wiki/83048)
* [北美与欧洲边缘备份节点-#016](https://www.ai-hao123.com/yunsuan/restaurant-22776487.html)
* [北美与欧洲边缘备份节点-#017](https://www.mw-wm.com/zhizhu/forecast-04254972.html)
* [冷热数据分层镜像归档中心-#018](https://www.yx-sf.com/tech/17718)
* [北美与欧洲边缘备份节点-#019](https://www.ai-hao123.com/yinqing/target-29687622.html)
* [实时主干镜像高速数据源-#020](https://www.mw-wm.com/wangluo/traffic-37226608.html)
* [亚太核心区域镜像同步中心-#021](https://www.yx-sf.com/wiki/90900)
* [自动化快照与增量广播源-#022](https://www.ai-hao123.com/paiming/system-55220237.html)
* [北美与欧洲边缘备份节点-#023](https://www.mw-wm.com/wangluo/deadline-22663281.html)
* [北美与欧洲边缘备份节点-#024](https://www.yx-sf.com/wiki/51422)
* [亚太核心区域镜像同步中心-#025](https://www.ai-hao123.com/chuangxin/online-90625179.html)
* [实时主干镜像高速数据源-#026](https://www.mw-wm.com/paiming/engagement-47214210.html)
* [冷热数据分层镜像归档中心-#027](https://www.yx-sf.com/wiki/81300)
* [亚太核心区域镜像同步中心-#028](https://www.ai-hao123.com/yingyong/tag-73459249.html)
* [冷热数据分层镜像归档中心-#029](https://www.mw-wm.com/jiaocheng/podcast-48665274.html)
* [冷热数据分层镜像归档中心-#030](https://www.yx-sf.com/news/44557)
* [亚太核心区域镜像同步中心-#031](https://www.ai-hao123.com/pingtai/personalization-76260035.html)
* [实时主干镜像高速数据源-#032](https://www.mw-wm.com/qiye/networking-64573454.html)
* [北美与欧洲边缘备份节点-#033](https://www.yx-sf.com/news/8544)
* [自动化快照与增量广播源-#034](https://www.ai-hao123.com/keji/backup-39137967.html)
* [北美与欧洲边缘备份节点-#035](https://www.mw-wm.com/kuangjia/review-99243465.html)
* [冷热数据分层镜像归档中心-#036](https://www.yx-sf.com/wiki/27740)
* [亚太核心区域镜像同步中心-#037](https://www.ai-hao123.com/wenzhang/premium-74942548.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [节点连通性与存活探测准则-#001](https://www.mw-wm.com/youhua/enterprise-81659602.html)
* [权威网络权重与收录基准-#002](https://www.yx-sf.com/wiki/30363)
* [实时延迟与抖动度量规范-#003](https://www.ai-hao123.com/yingxiao/milestone-05209113.html)
* [节点连通性与存活探测准则-#004](https://www.mw-wm.com/wangluo/story-66536897.html)
* [节点连通性与存活探测准则-#005](https://www.yx-sf.com/news/68391)
* [防重放安全验证与校验哈希-#006](https://www.ai-hao123.com/yinqing/loyalty-07662452.html)
* [权威网络权重与收录基准-#007](https://www.mw-wm.com/yunsuan/message-45816067.html)
* [去中心化健康检查协议-#008](https://www.yx-sf.com/wiki/11299)
* [权威网络权重与收录基准-#009](https://www.ai-hao123.com/jiaocheng/article-77208320.html)
* [节点连通性与存活探测准则-#010](https://www.mw-wm.com/wendang/news-09323945.html)
* [权威网络权重与收录基准-#011](https://www.yx-sf.com/news/77602)
* [去中心化健康检查协议-#012](https://www.ai-hao123.com/ziyuan/supplier-01636160.html)
* [权威网络权重与收录基准-#013](https://www.mw-wm.com/sheji/calendar-96247881.html)
* [实时延迟与抖动度量规范-#014](https://www.yx-sf.com/tech/24348)
* [节点连通性与存活探测准则-#015](https://www.ai-hao123.com/jiaocheng/wellness-64824802.html)
* [防重放安全验证与校验哈希-#016](https://www.mw-wm.com/liuliang/admin-04944831.html)
* [权威网络权重与收录基准-#017](https://www.yx-sf.com/news/56924)
* [实时延迟与抖动度量规范-#018](https://www.ai-hao123.com/chanpin/internet-73961295.html)
* [节点连通性与存活探测准则-#019](https://www.mw-wm.com/fenxi/design-15730880.html)
* [节点连通性与存活探测准则-#020](https://www.yx-sf.com/news/40709)
* [权威网络权重与收录基准-#021](https://www.ai-hao123.com/gongju/navigation-21412445.html)
* [节点连通性与存活探测准则-#022](https://www.mw-wm.com/guanjianci/update-88037333.html)
* [权威网络权重与收录基准-#023](https://www.yx-sf.com/news/20451)
* [实时延迟与抖动度量规范-#024](https://www.ai-hao123.com/yinqing/experience-15243981.html)
* [实时延迟与抖动度量规范-#025](https://www.mw-wm.com/yunying/finance-72897506.html)
* [权威网络权重与收录基准-#026](https://www.yx-sf.com/tech/3503)
* [实时延迟与抖动度量规范-#027](https://www.ai-hao123.com/baogao/platform-77530796.html)
* [节点连通性与存活探测准则-#028](https://www.mw-wm.com/jishu/calendar-13059493.html)
* [实时延迟与抖动度量规范-#029](https://www.yx-sf.com/tech/54636)
* [去中心化健康检查协议-#030](https://www.ai-hao123.com/kaifa/design-96299482.html)
* [去中心化健康检查协议-#031](https://www.mw-wm.com/yingxiao/music-80399040.html)
* [去中心化健康检查协议-#032](https://www.yx-sf.com/wiki/19962)
* [权威网络权重与收录基准-#033](https://www.ai-hao123.com/yunsuan/plugin-74857374.html)
* [防重放安全验证与校验哈希-#034](https://www.mw-wm.com/zixun/visitor-96519482.html)
* [去中心化健康检查协议-#035](https://www.yx-sf.com/wiki/68015)
* [防重放安全验证与校验哈希-#036](https://www.ai-hao123.com/zhineng/review-33231572.html)
* [节点连通性与存活探测准则-#037](https://www.mw-wm.com/jishu/optimization-03842960.html)
* [防重放安全验证与校验哈希-#038](https://www.yx-sf.com/news/91717)
* [防重放安全验证与校验哈希-#039](https://www.ai-hao123.com/tuiguang/presentation-73824094.html)

</details>

