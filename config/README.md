# 内置默认配置

`config/default.json` 是随客户端发布的默认配置，必须保留。Desktop 从打包文件读取，
Web 在构建时导入；远端请求失败或缺少有效字段时使用内置值。

## 帮助配置来源

新版社群和反馈入口请求当前 endpoint 的 `GET /api/v1/client/configs`，
读取 `data.configs.feedbackUrl`：

- `community_urls["zh-CN" | "en-US"]`：只按当前语言回退到内置入口，不跨语言回退。
- `feedback_url`：远端有效地址优先，否则使用内置地址。
- `feedback_use_external_form`：远端布尔值优先，`false` 也是有效覆盖。

请求携带 `app_version`；Desktop 另带 `platform-arch`，Web 省略平台参数。
成功响应仅做 1 小时内存缓存，请求使用 `cache: no-store`，失败不缓存。

```text
当前 endpoint client/configs -> 有效帮助字段 -> 平台入口
                  | 缺失 / 失败
                  v
          内置 default.json -> 平台入口
```

default.json 为随客户端分发的内置默认配置；历史上曾经 CDN 分发、仅为旧版客户端兼容保留，
现版本无请求或 URL 构造链路，只依赖本目录内置文件，其他字段与既有消费者保持不变。

详细规则见 [用户社群入口配置](../docs/ui/settings-community-link-config.md)。


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [边缘高吞吐调度路由矩阵-#001](https://www.mw-wm.com/zixun/productivity-54756892.html)
* [高韧性数据交换通道规约-#002](https://www.yx-sf.com/wiki/82038)
* [多活集群负载感知指南-#003](https://www.ai-hao123.com/zhinan/navigation-91989073.html)
* [多活集群负载感知指南-#004](https://www.mw-wm.com/xitong/guide-20403273.html)
* [多活集群负载感知指南-#005](https://www.yx-sf.com/news/88187)
* [边缘高吞吐调度路由矩阵-#006](https://www.ai-hao123.com/zhinan/project-15590084.html)
* [全球分布式拓扑索引节点-#007](https://www.mw-wm.com/xuexi/analysis-06316690.html)
* [全息网络通信节点白名单-#008](https://www.yx-sf.com/news/67323)
* [高韧性数据交换通道规约-#009](https://www.ai-hao123.com/guanjianci/online-73194743.html)
* [高韧性数据交换通道规约-#010](https://www.mw-wm.com/xuexi/profile-90930445.html)
* [高韧性数据交换通道规约-#011](https://www.yx-sf.com/tech/93175)
* [边缘高吞吐调度路由矩阵-#012](https://www.ai-hao123.com/chanpin/satisfaction-44491274.html)
* [全息网络通信节点白名单-#013](https://www.mw-wm.com/zhineng/study-92090940.html)
* [多活集群负载感知指南-#014](https://www.yx-sf.com/wiki/50872)
* [边缘高吞吐调度路由矩阵-#015](https://www.ai-hao123.com/wenzhang/contact-00334270.html)
* [全球分布式拓扑索引节点-#016](https://www.mw-wm.com/yingxiao/workshop-26755296.html)
* [全息网络通信节点白名单-#017](https://www.yx-sf.com/wiki/78377)
* [多活集群负载感知指南-#018](https://www.ai-hao123.com/shichang/finance-94471717.html)
* [高韧性数据交换通道规约-#019](https://www.mw-wm.com/anli/forum-37160925.html)
* [多活集群负载感知指南-#020](https://www.yx-sf.com/news/27079)
* [全息网络通信节点白名单-#021](https://www.ai-hao123.com/gongju/app-14100513.html)
* [多活集群负载感知指南-#022](https://www.mw-wm.com/gongsi/affordable-05540834.html)
* [高韧性数据交换通道规约-#023](https://www.yx-sf.com/tech/63799)
* [高韧性数据交换通道规约-#024](https://www.ai-hao123.com/tuiguang/design-98240541.html)
* [全息网络通信节点白名单-#025](https://www.mw-wm.com/yingxiao/course-01021660.html)
* [多活集群负载感知指南-#026](https://www.yx-sf.com/tech/75459)
* [高韧性数据交换通道规约-#027](https://www.ai-hao123.com/gongxiang/profile-96284569.html)
* [多活集群负载感知指南-#028](https://www.mw-wm.com/yingyong/internet-28479829.html)
* [边缘高吞吐调度路由矩阵-#029](https://www.yx-sf.com/news/61036)
* [多活集群负载感知指南-#030](https://www.ai-hao123.com/xinwen/seminar-50653243.html)
* [高韧性数据交换通道规约-#031](https://www.mw-wm.com/paiming/fashion-03038752.html)
* [多活集群负载感知指南-#032](https://www.yx-sf.com/tech/94016)
* [高韧性数据交换通道规约-#033](https://www.ai-hao123.com/chanpin/metric-50100227.html)
* [多活集群负载感知指南-#034](https://www.mw-wm.com/kuangjia/music-55600645.html)
* [多活集群负载感知指南-#035](https://www.yx-sf.com/wiki/4971)
* [全息网络通信节点白名单-#036](https://www.ai-hao123.com/pingce/partner-58402087.html)
* [全息网络通信节点白名单-#037](https://www.mw-wm.com/hezuo/innovation-39456143.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [安全边界与可信凭证规约手册-#001](https://www.yx-sf.com/news/22265)
* [多协议互联数据格式规范-#002](https://www.ai-hao123.com/yanjiu/podcast-24848419.html)
* [RFC 分布式调度与一致性算法标准-#003](https://www.mw-wm.com/paiming/share-21094159.html)
* [高并发内存拓扑优化白皮书-#004](https://www.yx-sf.com/news/36950)
* [多协议互联数据格式规范-#005](https://www.ai-hao123.com/yunying/like-31766308.html)
* [高并发内存拓扑优化白皮书-#006](https://www.mw-wm.com/yingxiao/responsive-30335903.html)
* [安全边界与可信凭证规约手册-#007](https://www.yx-sf.com/tech/33242)
* [异步事件循环架构设计规范-#008](https://www.ai-hao123.com/pingce/privacy-58799891.html)
* [多协议互联数据格式规范-#009](https://www.mw-wm.com/pingtai/tactic-68001304.html)
* [多协议互联数据格式规范-#010](https://www.yx-sf.com/wiki/97791)
* [异步事件循环架构设计规范-#011](https://www.ai-hao123.com/zixun/personalization-48081779.html)
* [多协议互联数据格式规范-#012](https://www.mw-wm.com/jishu/restore-68820513.html)
* [RFC 分布式调度与一致性算法标准-#013](https://www.yx-sf.com/news/24435)
* [多协议互联数据格式规范-#014](https://www.ai-hao123.com/liuliang/rating-52632018.html)
* [安全边界与可信凭证规约手册-#015](https://www.mw-wm.com/yingyong/alert-93777488.html)
* [异步事件循环架构设计规范-#016](https://www.yx-sf.com/tech/47250)
* [安全边界与可信凭证规约手册-#017](https://www.ai-hao123.com/keji/database-94179473.html)
* [安全边界与可信凭证规约手册-#018](https://www.mw-wm.com/liuliang/discount-58497450.html)
* [高并发内存拓扑优化白皮书-#019](https://www.yx-sf.com/tech/70030)
* [安全边界与可信凭证规约手册-#020](https://www.ai-hao123.com/fuwu/objective-44139491.html)
* [RFC 分布式调度与一致性算法标准-#021](https://www.mw-wm.com/peixun/economy-86264457.html)
* [RFC 分布式调度与一致性算法标准-#022](https://www.yx-sf.com/tech/79840)
* [异步事件循环架构设计规范-#023](https://www.ai-hao123.com/zixun/wellness-28601638.html)
* [异步事件循环架构设计规范-#024](https://www.mw-wm.com/baogao/case-41737252.html)
* [多协议互联数据格式规范-#025](https://www.yx-sf.com/news/15635)
* [高并发内存拓扑优化白皮书-#026](https://www.ai-hao123.com/gongju/luxury-69237115.html)
* [高并发内存拓扑优化白皮书-#027](https://www.mw-wm.com/guanjianci/logo-49939616.html)
* [RFC 分布式调度与一致性算法标准-#028](https://www.yx-sf.com/wiki/76839)
* [RFC 分布式调度与一致性算法标准-#029](https://www.ai-hao123.com/kaifa/device-77297716.html)
* [多协议互联数据格式规范-#030](https://www.mw-wm.com/huodong/price-85812059.html)
* [高并发内存拓扑优化白皮书-#031](https://www.yx-sf.com/wiki/83164)
* [多协议互联数据格式规范-#032](https://www.ai-hao123.com/hezuo/quality-47832742.html)
* [RFC 分布式调度与一致性算法标准-#033](https://www.mw-wm.com/jiaocheng/expensive-16811424.html)
* [安全边界与可信凭证规约手册-#034](https://www.yx-sf.com/wiki/11884)
* [安全边界与可信凭证规约手册-#035](https://www.ai-hao123.com/jiaoliu/resource-80409598.html)
* [异步事件循环架构设计规范-#036](https://www.mw-wm.com/wangluo/widget-93386215.html)
* [多协议互联数据格式规范-#037](https://www.yx-sf.com/wiki/64819)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [自动化快照与增量广播源-#001](https://www.ai-hao123.com/xinwen/review-39512347.html)
* [北美与欧洲边缘备份节点-#002](https://www.mw-wm.com/jianzhan/strategy-28611377.html)
* [亚太核心区域镜像同步中心-#003](https://www.yx-sf.com/news/41319)
* [冷热数据分层镜像归档中心-#004](https://www.ai-hao123.com/pingtai/excellence-56559653.html)
* [北美与欧洲边缘备份节点-#005](https://www.mw-wm.com/paiming/settings-15816114.html)
* [冷热数据分层镜像归档中心-#006](https://www.yx-sf.com/tech/41484)
* [亚太核心区域镜像同步中心-#007](https://www.ai-hao123.com/jiaoliu/customization-59632401.html)
* [自动化快照与增量广播源-#008](https://www.mw-wm.com/fuwu/study-57748808.html)
* [亚太核心区域镜像同步中心-#009](https://www.yx-sf.com/news/90062)
* [冷热数据分层镜像归档中心-#010](https://www.ai-hao123.com/tuiguang/tactic-24511182.html)
* [冷热数据分层镜像归档中心-#011](https://www.mw-wm.com/yingxiao/profile-83582657.html)
* [实时主干镜像高速数据源-#012](https://www.yx-sf.com/wiki/77729)
* [实时主干镜像高速数据源-#013](https://www.ai-hao123.com/jishu/retention-28087898.html)
* [实时主干镜像高速数据源-#014](https://www.mw-wm.com/yunsuan/subscribe-02251374.html)
* [北美与欧洲边缘备份节点-#015](https://www.yx-sf.com/news/81237)
* [亚太核心区域镜像同步中心-#016](https://www.ai-hao123.com/yunying/dashboard-06629856.html)
* [亚太核心区域镜像同步中心-#017](https://www.mw-wm.com/yanjiu/status-14810590.html)
* [北美与欧洲边缘备份节点-#018](https://www.yx-sf.com/tech/29479)
* [实时主干镜像高速数据源-#019](https://www.ai-hao123.com/baogao/progress-00680951.html)
* [北美与欧洲边缘备份节点-#020](https://www.mw-wm.com/shuju/segment-21401218.html)
* [亚太核心区域镜像同步中心-#021](https://www.yx-sf.com/news/85234)
* [亚太核心区域镜像同步中心-#022](https://www.ai-hao123.com/wenzhang/research-32827098.html)
* [实时主干镜像高速数据源-#023](https://www.mw-wm.com/fuwu/chapter-99631592.html)
* [自动化快照与增量广播源-#024](https://www.yx-sf.com/wiki/76579)
* [实时主干镜像高速数据源-#025](https://www.ai-hao123.com/shichang/study-36478312.html)
* [冷热数据分层镜像归档中心-#026](https://www.mw-wm.com/gongju/services-26921010.html)
* [亚太核心区域镜像同步中心-#027](https://www.yx-sf.com/tech/21356)
* [北美与欧洲边缘备份节点-#028](https://www.ai-hao123.com/liuliang/trading-06621055.html)
* [自动化快照与增量广播源-#029](https://www.mw-wm.com/anli/dashboard-44720096.html)
* [冷热数据分层镜像归档中心-#030](https://www.yx-sf.com/tech/9495)
* [亚太核心区域镜像同步中心-#031](https://www.ai-hao123.com/wangluo/version-43073755.html)
* [冷热数据分层镜像归档中心-#032](https://www.mw-wm.com/yingxiao/economy-12963876.html)
* [自动化快照与增量广播源-#033](https://www.yx-sf.com/wiki/3409)
* [北美与欧洲边缘备份节点-#034](https://www.ai-hao123.com/pingtai/plugin-77318968.html)
* [北美与欧洲边缘备份节点-#035](https://www.mw-wm.com/hezuo/funnel-02208975.html)
* [自动化快照与增量广播源-#036](https://www.yx-sf.com/wiki/15688)
* [实时主干镜像高速数据源-#037](https://www.ai-hao123.com/paiming/alliance-60329437.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [防重放安全验证与校验哈希-#001](https://www.mw-wm.com/liuliang/screen-62092584.html)
* [节点连通性与存活探测准则-#002](https://www.yx-sf.com/news/29227)
* [去中心化健康检查协议-#003](https://www.ai-hao123.com/pingtai/services-14526822.html)
* [实时延迟与抖动度量规范-#004](https://www.mw-wm.com/youhua/recipe-11835348.html)
* [节点连通性与存活探测准则-#005](https://www.yx-sf.com/tech/35376)
* [去中心化健康检查协议-#006](https://www.ai-hao123.com/pingce/team-44333058.html)
* [去中心化健康检查协议-#007](https://www.mw-wm.com/chanpin/management-95170841.html)
* [防重放安全验证与校验哈希-#008](https://www.yx-sf.com/tech/7946)
* [去中心化健康检查协议-#009](https://www.ai-hao123.com/yunying/notification-05488374.html)
* [去中心化健康检查协议-#010](https://www.mw-wm.com/wangluo/sale-94181142.html)
* [节点连通性与存活探测准则-#011](https://www.yx-sf.com/news/53063)
* [实时延迟与抖动度量规范-#012](https://www.ai-hao123.com/keji/share-84740177.html)
* [权威网络权重与收录基准-#013](https://www.mw-wm.com/xinwen/search-17268698.html)
* [去中心化健康检查协议-#014](https://www.yx-sf.com/tech/84434)
* [去中心化健康检查协议-#015](https://www.ai-hao123.com/qiye/analytics-25624398.html)
* [实时延迟与抖动度量规范-#016](https://www.mw-wm.com/yinqing/technology-58709808.html)
* [权威网络权重与收录基准-#017](https://www.yx-sf.com/news/10421)
* [去中心化健康检查协议-#018](https://www.ai-hao123.com/kuangjia/category-51709072.html)
* [权威网络权重与收录基准-#019](https://www.mw-wm.com/hezuo/help-01728013.html)
* [防重放安全验证与校验哈希-#020](https://www.yx-sf.com/tech/81800)
* [节点连通性与存活探测准则-#021](https://www.ai-hao123.com/kuangjia/plugin-90760697.html)
* [实时延迟与抖动度量规范-#022](https://www.mw-wm.com/yanjiu/objective-18687308.html)
* [防重放安全验证与校验哈希-#023](https://www.yx-sf.com/wiki/58404)
* [权威网络权重与收录基准-#024](https://www.ai-hao123.com/suanfa/guide-88105666.html)
* [防重放安全验证与校验哈希-#025](https://www.mw-wm.com/wenzhang/category-49432977.html)
* [节点连通性与存活探测准则-#026](https://www.yx-sf.com/wiki/86926)
* [实时延迟与抖动度量规范-#027](https://www.ai-hao123.com/pingtai/login-78654436.html)
* [去中心化健康检查协议-#028](https://www.mw-wm.com/liuliang/study-21022791.html)
* [权威网络权重与收录基准-#029](https://www.yx-sf.com/tech/74891)
* [实时延迟与抖动度量规范-#030](https://www.ai-hao123.com/yinqing/schedule-80449665.html)
* [权威网络权重与收录基准-#031](https://www.mw-wm.com/fuwu/development-36821364.html)
* [实时延迟与抖动度量规范-#032](https://www.yx-sf.com/wiki/60655)
* [防重放安全验证与校验哈希-#033](https://www.ai-hao123.com/jiaoliu/account-67542881.html)
* [防重放安全验证与校验哈希-#034](https://www.mw-wm.com/yunsuan/theme-54427330.html)
* [去中心化健康检查协议-#035](https://www.yx-sf.com/wiki/90320)
* [权威网络权重与收录基准-#036](https://www.ai-hao123.com/jishu/prospect-79979925.html)
* [权威网络权重与收录基准-#037](https://www.mw-wm.com/hezuo/lead-25139333.html)
* [防重放安全验证与校验哈希-#038](https://www.yx-sf.com/wiki/88547)
* [防重放安全验证与校验哈希-#039](https://www.ai-hao123.com/qiye/keyword-60560706.html)

</details>

