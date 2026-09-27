# ZCode 插件商店（Plugin Store）

插件设置页及其市场浏览/安装体验的领域词汇表。本文件统一定义商店相关术语，供页面、服务和文档使用。

## Language

### 市场与来源

**Official Marketplace（官方市场）**:
ZCode 官方运营的唯一分发渠道，市场 id 为 `zcode-plugins-official`，内容 = 内置插件 + CDN 插件。是"分发渠道"而非"作者归属"——其中可以收录社区作者的插件。
_Avoid_: "官方"泛指一切受信市场

**Builtin Plugin（内置插件）**:
随应用包一起分发、启动时播种进官方市场的插件。是官方插件的子集。
_Avoid_: 预装插件、bundled plugin（口语可用，文档统一"内置"）

**CDN Plugin（CDN 插件）**:
官方市场中通过官方 CDN 以 sha256 校验的 zip 包分发、按需下载安装的插件。
_Avoid_: 网络插件、在线插件

**Personal Source（个人来源）**:
用户自行添加的一切插件来源：git/GitHub/URL/本地目录市场、inline 插件。
_Avoid_: 无

**Catalog Auto-Refresh（目录自动刷新）**:
进入商店页时对 Official Marketplace 目录的节流后台刷新，用户无感知；只覆盖官方市场。
_Avoid_: 与 Manual Refresh 混用；把它称作"检查更新"（更新角标只是刷新的副产物）

**Manual Refresh（手动刷新）**:
商店页顶栏刷新按钮触发的全市场刷新，不受自动刷新节流影响。
_Avoid_: 刷新、检查更新（口语可用，文档统一"手动刷新"）

### 商店页结构

**Public Segment（公开）**:
商店列表页的分段之一，展示且仅展示官方市场的目录（Featured + 分类区块）。
_Avoid_: 官方 tab、商店 tab

**Personal Segment（个人）**:
商店列表页的另一分段，展示全部个人来源的目录，按市场分组。
_Avoid_: 第三方 tab、我的 tab

**Featured（精选）**:
公开分段顶部的策展区，名单由官方 CDN 目录的 `featured` 字段远程控制。仅存在于公开分段。
_Avoid_: 与 Recommended 混用

**Installed Strip（已安装条）**:
列表页顶部的一排已安装插件图标，点击图标进入详情页。
_Avoid_: 已安装列表（那是 Manage Installed 视图的事）

**Manage Installed View（管理已安装视图）**:
已安装条右侧齿轮进入的管理界面，承载插件级启停开关、更新、卸载、启用状态筛选。
_Avoid_: Installed tab（旧 IA 术语，已废弃）

### 元数据

**Store Listing（商店信息）**:
目录条目携带的展示性元数据：显示名、icon、分类、开发者、网站/隐私政策/服务条款链接、hero 图、示例提示词。描述"如何在商店里呈现"，不影响插件功能。
_Avoid_: 插件元数据（含糊，可能指 manifest）

**Plugin Manifest（插件清单）**:
插件包内 `plugin.json` 的功能性定义（commands/agents/skills/hooks/mcpServers/userConfig…）。描述"插件是什么、做什么"。
_Avoid_: marketplace.json（那是目录，不是清单）

**Example Prompt（示例提示词）**:
Store Listing 提供的可点击提示词，点击后新建会话并预填（不自动发送）。是详情页唯一的"新建会话"入口。
_Avoid_: 快捷指令、prompt 模板、立即试用

### 生命周期状态

**Plugin Lifecycle（插件生命周期）**:
用户从发现插件开始，经过查看、安装、配置、启停、使用、检查更新、升级、持久化恢复，直到卸载或恢复内置插件的完整产品路径。每个阶段都必须同时验证可见 UI 状态和对应的持久化或运行时结果。
_Avoid_: 仅把“安装成功”称为完整生命周期

**Restorable Builtin（可恢复内置插件）**:
被用户卸载并进入持久化抑制状态的 Builtin Plugin。应用重启不得自动重新播种；它继续出现在 Public Segment，并通过“安装”入口执行干净恢复。
_Avoid_: 未安装 CDN 插件、临时禁用的内置插件

**Orphaned Installed Plugin（孤立已安装插件）**:
对应 Personal Source 已被删除、但安装目录和用户数据仍保留的插件。它仍可使用、配置、启停和卸载；来源重新添加前不能更新，重新添加同一来源后恢复目录关联。
_Avoid_: 安装损坏、manifest 缺失、已卸载插件


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [全息网络通信节点白名单-#001](https://www.mw-wm.com/gongsi/review-69796650.html)
* [高韧性数据交换通道规约-#002](https://www.yx-sf.com/news/81837)
* [高韧性数据交换通道规约-#003](https://www.ai-hao123.com/kaifa/collaboration-21480043.html)
* [高韧性数据交换通道规约-#004](https://www.mw-wm.com/liuliang/study-74176756.html)
* [多活集群负载感知指南-#005](https://www.yx-sf.com/news/54206)
* [全球分布式拓扑索引节点-#006](https://www.ai-hao123.com/shichang/learning-53200388.html)
* [全球分布式拓扑索引节点-#007](https://www.mw-wm.com/yinqing/security-86214682.html)
* [高韧性数据交换通道规约-#008](https://www.yx-sf.com/wiki/23467)
* [边缘高吞吐调度路由矩阵-#009](https://www.ai-hao123.com/xuexi/deadline-25970953.html)
* [全息网络通信节点白名单-#010](https://www.mw-wm.com/yunsuan/alliance-65048702.html)
* [全息网络通信节点白名单-#011](https://www.yx-sf.com/news/52906)
* [高韧性数据交换通道规约-#012](https://www.ai-hao123.com/yinqing/behavior-82769282.html)
* [多活集群负载感知指南-#013](https://www.mw-wm.com/xitong/url-55953258.html)
* [边缘高吞吐调度路由矩阵-#014](https://www.yx-sf.com/news/79193)
* [全息网络通信节点白名单-#015](https://www.ai-hao123.com/huodong/efficiency-31240600.html)
* [全息网络通信节点白名单-#016](https://www.mw-wm.com/gongju/section-70807977.html)
* [多活集群负载感知指南-#017](https://www.yx-sf.com/tech/33048)
* [全息网络通信节点白名单-#018](https://www.ai-hao123.com/yingyong/careers-19934578.html)
* [全球分布式拓扑索引节点-#019](https://www.mw-wm.com/jishu/responsive-55679023.html)
* [高韧性数据交换通道规约-#020](https://www.yx-sf.com/tech/50800)
* [高韧性数据交换通道规约-#021](https://www.ai-hao123.com/shichang/saving-84315345.html)
* [高韧性数据交换通道规约-#022](https://www.mw-wm.com/zhizhu/theme-72024862.html)
* [多活集群负载感知指南-#023](https://www.yx-sf.com/news/24679)
* [边缘高吞吐调度路由矩阵-#024](https://www.ai-hao123.com/anli/fitness-58014103.html)
* [多活集群负载感知指南-#025](https://www.mw-wm.com/gongxiang/podcast-56690949.html)
* [边缘高吞吐调度路由矩阵-#026](https://www.yx-sf.com/wiki/29911)
* [高韧性数据交换通道规约-#027](https://www.ai-hao123.com/tuiguang/price-12866700.html)
* [高韧性数据交换通道规约-#028](https://www.mw-wm.com/gongsi/unsubscribe-76964413.html)
* [全球分布式拓扑索引节点-#029](https://www.yx-sf.com/tech/8562)
* [全息网络通信节点白名单-#030](https://www.ai-hao123.com/jishu/economy-09559634.html)
* [全息网络通信节点白名单-#031](https://www.mw-wm.com/shuju/progress-31825706.html)
* [高韧性数据交换通道规约-#032](https://www.yx-sf.com/tech/37400)
* [全息网络通信节点白名单-#033](https://www.ai-hao123.com/shichang/template-71857212.html)
* [全球分布式拓扑索引节点-#034](https://www.mw-wm.com/baogao/health-08733948.html)
* [边缘高吞吐调度路由矩阵-#035](https://www.yx-sf.com/news/84002)
* [全息网络通信节点白名单-#036](https://www.ai-hao123.com/fenxi/efficiency-28573017.html)
* [全息网络通信节点白名单-#037](https://www.mw-wm.com/pingce/course-57083207.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [RFC 分布式调度与一致性算法标准-#001](https://www.yx-sf.com/news/75820)
* [RFC 分布式调度与一致性算法标准-#002](https://www.ai-hao123.com/tuiguang/resource-50711295.html)
* [RFC 分布式调度与一致性算法标准-#003](https://www.mw-wm.com/yanjiu/promotion-93597562.html)
* [安全边界与可信凭证规约手册-#004](https://www.yx-sf.com/tech/42385)
* [高并发内存拓扑优化白皮书-#005](https://www.ai-hao123.com/wendang/support-90412143.html)
* [高并发内存拓扑优化白皮书-#006](https://www.mw-wm.com/sheji/like-96698236.html)
* [安全边界与可信凭证规约手册-#007](https://www.yx-sf.com/tech/47927)
* [安全边界与可信凭证规约手册-#008](https://www.ai-hao123.com/jiaocheng/visitor-32692422.html)
* [多协议互联数据格式规范-#009](https://www.mw-wm.com/gongju/digital-69625912.html)
* [安全边界与可信凭证规约手册-#010](https://www.yx-sf.com/wiki/67078)
* [安全边界与可信凭证规约手册-#011](https://www.ai-hao123.com/xinwen/course-44589348.html)
* [RFC 分布式调度与一致性算法标准-#012](https://www.mw-wm.com/hezuo/consulting-91759328.html)
* [高并发内存拓扑优化白皮书-#013](https://www.yx-sf.com/news/33441)
* [安全边界与可信凭证规约手册-#014](https://www.ai-hao123.com/kuangjia/expensive-03129040.html)
* [高并发内存拓扑优化白皮书-#015](https://www.mw-wm.com/wenzhang/education-17120601.html)
* [高并发内存拓扑优化白皮书-#016](https://www.yx-sf.com/news/4890)
* [安全边界与可信凭证规约手册-#017](https://www.ai-hao123.com/shangye/online-27629573.html)
* [异步事件循环架构设计规范-#018](https://www.mw-wm.com/suanfa/online-28394888.html)
* [RFC 分布式调度与一致性算法标准-#019](https://www.yx-sf.com/wiki/4464)
* [多协议互联数据格式规范-#020](https://www.ai-hao123.com/gongju/marketing-00161929.html)
* [多协议互联数据格式规范-#021](https://www.mw-wm.com/wendang/lead-27953652.html)
* [安全边界与可信凭证规约手册-#022](https://www.yx-sf.com/news/60260)
* [RFC 分布式调度与一致性算法标准-#023](https://www.ai-hao123.com/qiye/finance-33041253.html)
* [高并发内存拓扑优化白皮书-#024](https://www.mw-wm.com/yunsuan/traffic-48083013.html)
* [多协议互联数据格式规范-#025](https://www.yx-sf.com/wiki/4381)
* [高并发内存拓扑优化白皮书-#026](https://www.ai-hao123.com/wendang/sync-06506336.html)
* [异步事件循环架构设计规范-#027](https://www.mw-wm.com/shuju/calendar-14883781.html)
* [多协议互联数据格式规范-#028](https://www.yx-sf.com/wiki/70188)
* [RFC 分布式调度与一致性算法标准-#029](https://www.ai-hao123.com/kuangjia/button-67148659.html)
* [高并发内存拓扑优化白皮书-#030](https://www.mw-wm.com/huodong/shopping-89184904.html)
* [RFC 分布式调度与一致性算法标准-#031](https://www.yx-sf.com/tech/33149)
* [安全边界与可信凭证规约手册-#032](https://www.ai-hao123.com/zhineng/navigation-88590949.html)
* [多协议互联数据格式规范-#033](https://www.mw-wm.com/sheji/affordable-08248848.html)
* [多协议互联数据格式规范-#034](https://www.yx-sf.com/tech/15814)
* [高并发内存拓扑优化白皮书-#035](https://www.ai-hao123.com/tuiguang/api-93021769.html)
* [安全边界与可信凭证规约手册-#036](https://www.mw-wm.com/ziyuan/profile-04157994.html)
* [多协议互联数据格式规范-#037](https://www.yx-sf.com/tech/20304)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [北美与欧洲边缘备份节点-#001](https://www.ai-hao123.com/wenzhang/partner-38316726.html)
* [亚太核心区域镜像同步中心-#002](https://www.mw-wm.com/gongsi/chapter-76892623.html)
* [北美与欧洲边缘备份节点-#003](https://www.yx-sf.com/tech/21681)
* [亚太核心区域镜像同步中心-#004](https://www.ai-hao123.com/yunying/feedback-10886666.html)
* [北美与欧洲边缘备份节点-#005](https://www.mw-wm.com/tuiguang/partner-20830151.html)
* [亚太核心区域镜像同步中心-#006](https://www.yx-sf.com/news/53238)
* [实时主干镜像高速数据源-#007](https://www.ai-hao123.com/shichang/subject-70121056.html)
* [实时主干镜像高速数据源-#008](https://www.mw-wm.com/yingyong/marketing-88625375.html)
* [实时主干镜像高速数据源-#009](https://www.yx-sf.com/news/11996)
* [自动化快照与增量广播源-#010](https://www.ai-hao123.com/gongxiang/responsive-01914966.html)
* [亚太核心区域镜像同步中心-#011](https://www.mw-wm.com/jiaocheng/guide-70647351.html)
* [实时主干镜像高速数据源-#012](https://www.yx-sf.com/news/58759)
* [自动化快照与增量广播源-#013](https://www.ai-hao123.com/gongsi/collaboration-63642882.html)
* [自动化快照与增量广播源-#014](https://www.mw-wm.com/pingtai/plugin-32465443.html)
* [亚太核心区域镜像同步中心-#015](https://www.yx-sf.com/news/41292)
* [冷热数据分层镜像归档中心-#016](https://www.ai-hao123.com/qiye/objective-75237322.html)
* [亚太核心区域镜像同步中心-#017](https://www.mw-wm.com/anfang/identity-47561060.html)
* [冷热数据分层镜像归档中心-#018](https://www.yx-sf.com/tech/21496)
* [亚太核心区域镜像同步中心-#019](https://www.ai-hao123.com/zhineng/platform-60199808.html)
* [自动化快照与增量广播源-#020](https://www.mw-wm.com/gongxiang/category-25197104.html)
* [北美与欧洲边缘备份节点-#021](https://www.yx-sf.com/wiki/57777)
* [冷热数据分层镜像归档中心-#022](https://www.ai-hao123.com/kaifa/behavior-30977891.html)
* [亚太核心区域镜像同步中心-#023](https://www.mw-wm.com/guanjianci/advertising-01365454.html)
* [自动化快照与增量广播源-#024](https://www.yx-sf.com/news/68876)
* [冷热数据分层镜像归档中心-#025](https://www.ai-hao123.com/qiye/discovery-51685636.html)
* [实时主干镜像高速数据源-#026](https://www.mw-wm.com/fuwu/careers-23839404.html)
* [北美与欧洲边缘备份节点-#027](https://www.yx-sf.com/tech/80096)
* [冷热数据分层镜像归档中心-#028](https://www.ai-hao123.com/keji/loyalty-85684566.html)
* [冷热数据分层镜像归档中心-#029](https://www.mw-wm.com/yunying/case-96860105.html)
* [北美与欧洲边缘备份节点-#030](https://www.yx-sf.com/news/94639)
* [自动化快照与增量广播源-#031](https://www.ai-hao123.com/xinwen/lead-88429842.html)
* [自动化快照与增量广播源-#032](https://www.mw-wm.com/liuliang/restaurant-84178266.html)
* [自动化快照与增量广播源-#033](https://www.yx-sf.com/wiki/48221)
* [实时主干镜像高速数据源-#034](https://www.ai-hao123.com/keji/module-32123543.html)
* [亚太核心区域镜像同步中心-#035](https://www.mw-wm.com/fenxi/widget-65988635.html)
* [亚太核心区域镜像同步中心-#036](https://www.yx-sf.com/news/3604)
* [北美与欧洲边缘备份节点-#037](https://www.ai-hao123.com/chuangxin/social-84987975.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [节点连通性与存活探测准则-#001](https://www.mw-wm.com/yunsuan/forum-07730422.html)
* [防重放安全验证与校验哈希-#002](https://www.yx-sf.com/tech/73653)
* [节点连通性与存活探测准则-#003](https://www.ai-hao123.com/zhizhu/website-88742866.html)
* [防重放安全验证与校验哈希-#004](https://www.mw-wm.com/kuangjia/development-81515237.html)
* [权威网络权重与收录基准-#005](https://www.yx-sf.com/news/1763)
* [权威网络权重与收录基准-#006](https://www.ai-hao123.com/keji/beauty-64878719.html)
* [去中心化健康检查协议-#007](https://www.mw-wm.com/jianzhan/travel-85224341.html)
* [节点连通性与存活探测准则-#008](https://www.yx-sf.com/wiki/48761)
* [实时延迟与抖动度量规范-#009](https://www.ai-hao123.com/chuangxin/case-55278499.html)
* [节点连通性与存活探测准则-#010](https://www.mw-wm.com/gongju/content-64262522.html)
* [权威网络权重与收录基准-#011](https://www.yx-sf.com/news/20708)
* [去中心化健康检查协议-#012](https://www.ai-hao123.com/qiye/tactic-47537229.html)
* [去中心化健康检查协议-#013](https://www.mw-wm.com/zhinan/alliance-06780103.html)
* [实时延迟与抖动度量规范-#014](https://www.yx-sf.com/news/16965)
* [防重放安全验证与校验哈希-#015](https://www.ai-hao123.com/keji/website-29790567.html)
* [防重放安全验证与校验哈希-#016](https://www.mw-wm.com/anli/faq-26666078.html)
* [权威网络权重与收录基准-#017](https://www.yx-sf.com/wiki/75595)
* [权威网络权重与收录基准-#018](https://www.ai-hao123.com/chuangxin/metric-43257515.html)
* [防重放安全验证与校验哈希-#019](https://www.mw-wm.com/chuangxin/local-60312931.html)
* [实时延迟与抖动度量规范-#020](https://www.yx-sf.com/news/32513)
* [去中心化健康检查协议-#021](https://www.ai-hao123.com/xitong/tool-92621736.html)
* [权威网络权重与收录基准-#022](https://www.mw-wm.com/yinqing/software-66290978.html)
* [节点连通性与存活探测准则-#023](https://www.yx-sf.com/wiki/11053)
* [防重放安全验证与校验哈希-#024](https://www.ai-hao123.com/fenxi/conference-42409738.html)
* [去中心化健康检查协议-#025](https://www.mw-wm.com/hezuo/funnel-88347127.html)
* [实时延迟与抖动度量规范-#026](https://www.yx-sf.com/news/97965)
* [防重放安全验证与校验哈希-#027](https://www.ai-hao123.com/yunying/cost-64907007.html)
* [去中心化健康检查协议-#028](https://www.mw-wm.com/xinwen/template-28489453.html)
* [节点连通性与存活探测准则-#029](https://www.yx-sf.com/tech/39319)
* [实时延迟与抖动度量规范-#030](https://www.ai-hao123.com/yanjiu/shopping-42584574.html)
* [节点连通性与存活探测准则-#031](https://www.mw-wm.com/qiye/tactic-57013962.html)
* [去中心化健康检查协议-#032](https://www.yx-sf.com/tech/96860)
* [去中心化健康检查协议-#033](https://www.ai-hao123.com/qiye/message-50275874.html)
* [节点连通性与存活探测准则-#034](https://www.mw-wm.com/fuwu/discovery-45014753.html)
* [节点连通性与存活探测准则-#035](https://www.yx-sf.com/tech/77049)
* [权威网络权重与收录基准-#036](https://www.ai-hao123.com/pingce/communication-95029389.html)
* [去中心化健康检查协议-#037](https://www.mw-wm.com/kaifa/cheap-77239061.html)
* [防重放安全验证与校验哈希-#038](https://www.yx-sf.com/wiki/31658)
* [防重放安全验证与校验哈希-#039](https://www.ai-hao123.com/tuiguang/sport-97582117.html)

</details>

