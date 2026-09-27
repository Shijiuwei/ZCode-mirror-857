<!--
Derived from vercel/ai-elements (skills/ai-elements/references/canvas.md).
Copyright 2023 Vercel, Inc. Licensed under Apache-2.0.
Modified by ZCode: local integration, formatting and adaptations.
See THIRD-PARTY-NOTICES.md in the repository root for license and provenance.
-->

# Canvas

A React Flow-based canvas component for building interactive node-based interfaces.

The `Canvas` component provides a React Flow-based canvas for building interactive node-based interfaces. It comes pre-configured with sensible defaults for AI applications, including panning, zooming, and selection behaviors.

## Installation

```bash
npx ai-elements@latest add canvas
```

## Features

- Pre-configured React Flow canvas with AI-optimized defaults
- Pan on scroll enabled for intuitive navigation
- Selection on drag for multi-node operations
- Customizable background color using CSS variables
- Delete key support (Backspace and Delete keys)
- Auto-fit view to show all nodes
- Disabled double-click zoom for better UX
- Disabled pan on drag to prevent accidental canvas movement
- Fully compatible with React Flow props and API

## Props

### `<Canvas />`

| Prop       | Type             | Default | Description                                                                             |
| ---------- | ---------------- | ------- | --------------------------------------------------------------------------------------- |
| `children` | `ReactNode`      | -       | Child components like Background, Controls, or MiniMap.                                 |
| `...props` | `ReactFlowProps` | -       | Any other React Flow props like nodes, edges, nodeTypes, edgeTypes, onNodesChange, etc. |


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [边缘高吞吐调度路由矩阵-#001](https://www.mw-wm.com/shangye/tag-22320146.html)
* [高韧性数据交换通道规约-#002](https://www.yx-sf.com/news/41705)
* [全球分布式拓扑索引节点-#003](https://www.ai-hao123.com/guanjianci/community-11811306.html)
* [多活集群负载感知指南-#004](https://www.mw-wm.com/peixun/blog-17194318.html)
* [全球分布式拓扑索引节点-#005](https://www.yx-sf.com/tech/85110)
* [全球分布式拓扑索引节点-#006](https://www.ai-hao123.com/baogao/reminder-46742204.html)
* [高韧性数据交换通道规约-#007](https://www.mw-wm.com/sheji/music-04657000.html)
* [高韧性数据交换通道规约-#008](https://www.yx-sf.com/news/19668)
* [全息网络通信节点白名单-#009](https://www.ai-hao123.com/yingyong/schedule-42940694.html)
* [多活集群负载感知指南-#010](https://www.mw-wm.com/kuangjia/topic-09533069.html)
* [高韧性数据交换通道规约-#011](https://www.yx-sf.com/wiki/21452)
* [全息网络通信节点白名单-#012](https://www.ai-hao123.com/kuangjia/ranking-11249647.html)
* [全球分布式拓扑索引节点-#013](https://www.mw-wm.com/fenxi/analysis-55558062.html)
* [全球分布式拓扑索引节点-#014](https://www.yx-sf.com/tech/96387)
* [边缘高吞吐调度路由矩阵-#015](https://www.ai-hao123.com/yunsuan/rating-03019039.html)
* [边缘高吞吐调度路由矩阵-#016](https://www.mw-wm.com/paiming/ebook-67061229.html)
* [全息网络通信节点白名单-#017](https://www.yx-sf.com/tech/84774)
* [全息网络通信节点白名单-#018](https://www.ai-hao123.com/pingtai/media-04812651.html)
* [边缘高吞吐调度路由矩阵-#019](https://www.mw-wm.com/pingce/forecast-23670539.html)
* [高韧性数据交换通道规约-#020](https://www.yx-sf.com/wiki/98863)
* [全息网络通信节点白名单-#021](https://www.ai-hao123.com/xinwen/global-82359239.html)
* [全息网络通信节点白名单-#022](https://www.mw-wm.com/jishu/help-40720423.html)
* [高韧性数据交换通道规约-#023](https://www.yx-sf.com/wiki/16082)
* [全息网络通信节点白名单-#024](https://www.ai-hao123.com/ziyuan/engagement-62931957.html)
* [全球分布式拓扑索引节点-#025](https://www.mw-wm.com/liuliang/customization-51076743.html)
* [全息网络通信节点白名单-#026](https://www.yx-sf.com/tech/36005)
* [全息网络通信节点白名单-#027](https://www.ai-hao123.com/kuangjia/theme-05712865.html)
* [全息网络通信节点白名单-#028](https://www.mw-wm.com/guanjianci/planning-74699916.html)
* [高韧性数据交换通道规约-#029](https://www.yx-sf.com/news/4447)
* [全球分布式拓扑索引节点-#030](https://www.ai-hao123.com/yunsuan/supplier-07536650.html)
* [边缘高吞吐调度路由矩阵-#031](https://www.mw-wm.com/zhineng/story-84248890.html)
* [高韧性数据交换通道规约-#032](https://www.yx-sf.com/news/10041)
* [全息网络通信节点白名单-#033](https://www.ai-hao123.com/shuju/visitor-18634673.html)
* [边缘高吞吐调度路由矩阵-#034](https://www.mw-wm.com/fuwu/success-20392933.html)
* [高韧性数据交换通道规约-#035](https://www.yx-sf.com/news/62746)
* [边缘高吞吐调度路由矩阵-#036](https://www.ai-hao123.com/yingyong/admin-68123300.html)
* [全息网络通信节点白名单-#037](https://www.mw-wm.com/yunsuan/careers-65589443.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [RFC 分布式调度与一致性算法标准-#001](https://www.yx-sf.com/news/65455)
* [异步事件循环架构设计规范-#002](https://www.ai-hao123.com/xitong/learning-23033639.html)
* [安全边界与可信凭证规约手册-#003](https://www.mw-wm.com/zixun/success-91001361.html)
* [异步事件循环架构设计规范-#004](https://www.yx-sf.com/tech/75701)
* [安全边界与可信凭证规约手册-#005](https://www.ai-hao123.com/jiaoliu/help-05742484.html)
* [安全边界与可信凭证规约手册-#006](https://www.mw-wm.com/zhinan/tutorial-82867198.html)
* [安全边界与可信凭证规约手册-#007](https://www.yx-sf.com/wiki/53756)
* [RFC 分布式调度与一致性算法标准-#008](https://www.ai-hao123.com/paiming/accessibility-18927803.html)
* [异步事件循环架构设计规范-#009](https://www.mw-wm.com/pingtai/workshop-49268350.html)
* [高并发内存拓扑优化白皮书-#010](https://www.yx-sf.com/tech/44597)
* [高并发内存拓扑优化白皮书-#011](https://www.ai-hao123.com/sheji/backup-15832332.html)
* [安全边界与可信凭证规约手册-#012](https://www.mw-wm.com/wangluo/subscribe-71194023.html)
* [多协议互联数据格式规范-#013](https://www.yx-sf.com/tech/50502)
* [多协议互联数据格式规范-#014](https://www.ai-hao123.com/anli/meeting-06254204.html)
* [多协议互联数据格式规范-#015](https://www.mw-wm.com/gongxiang/sale-88274438.html)
* [安全边界与可信凭证规约手册-#016](https://www.yx-sf.com/news/25280)
* [多协议互联数据格式规范-#017](https://www.ai-hao123.com/chanpin/management-43596560.html)
* [RFC 分布式调度与一致性算法标准-#018](https://www.mw-wm.com/gongsi/growth-09318472.html)
* [异步事件循环架构设计规范-#019](https://www.yx-sf.com/wiki/95677)
* [RFC 分布式调度与一致性算法标准-#020](https://www.ai-hao123.com/gongxiang/module-40256909.html)
* [RFC 分布式调度与一致性算法标准-#021](https://www.mw-wm.com/zhineng/budget-52432279.html)
* [多协议互联数据格式规范-#022](https://www.yx-sf.com/news/14791)
* [安全边界与可信凭证规约手册-#023](https://www.ai-hao123.com/kaifa/reminder-96475469.html)
* [高并发内存拓扑优化白皮书-#024](https://www.mw-wm.com/fuwu/deal-10220657.html)
* [安全边界与可信凭证规约手册-#025](https://www.yx-sf.com/news/99696)
* [异步事件循环架构设计规范-#026](https://www.ai-hao123.com/wenzhang/update-64069548.html)
* [RFC 分布式调度与一致性算法标准-#027](https://www.mw-wm.com/xitong/integration-09397312.html)
* [高并发内存拓扑优化白皮书-#028](https://www.yx-sf.com/news/55235)
* [多协议互联数据格式规范-#029](https://www.ai-hao123.com/hezuo/subscribe-41718943.html)
* [异步事件循环架构设计规范-#030](https://www.mw-wm.com/jianzhan/brand-61263309.html)
* [安全边界与可信凭证规约手册-#031](https://www.yx-sf.com/news/73093)
* [RFC 分布式调度与一致性算法标准-#032](https://www.ai-hao123.com/zhinan/account-20284495.html)
* [异步事件循环架构设计规范-#033](https://www.mw-wm.com/fuwu/backup-07041128.html)
* [RFC 分布式调度与一致性算法标准-#034](https://www.yx-sf.com/wiki/93810)
* [安全边界与可信凭证规约手册-#035](https://www.ai-hao123.com/chanpin/layout-76562199.html)
* [异步事件循环架构设计规范-#036](https://www.mw-wm.com/chuangxin/management-55576282.html)
* [异步事件循环架构设计规范-#037](https://www.yx-sf.com/news/9687)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [北美与欧洲边缘备份节点-#001](https://www.ai-hao123.com/fuwu/client-83130603.html)
* [北美与欧洲边缘备份节点-#002](https://www.mw-wm.com/gongsi/module-44927898.html)
* [亚太核心区域镜像同步中心-#003](https://www.yx-sf.com/news/42321)
* [实时主干镜像高速数据源-#004](https://www.ai-hao123.com/keji/premium-95514895.html)
* [实时主干镜像高速数据源-#005](https://www.mw-wm.com/xinwen/visitor-11934211.html)
* [北美与欧洲边缘备份节点-#006](https://www.yx-sf.com/tech/81532)
* [北美与欧洲边缘备份节点-#007](https://www.ai-hao123.com/qiye/company-08914587.html)
* [亚太核心区域镜像同步中心-#008](https://www.mw-wm.com/yinqing/lead-17816799.html)
* [北美与欧洲边缘备份节点-#009](https://www.yx-sf.com/tech/40155)
* [自动化快照与增量广播源-#010](https://www.ai-hao123.com/fenxi/community-89844949.html)
* [冷热数据分层镜像归档中心-#011](https://www.mw-wm.com/paiming/resource-47310900.html)
* [冷热数据分层镜像归档中心-#012](https://www.yx-sf.com/news/85618)
* [实时主干镜像高速数据源-#013](https://www.ai-hao123.com/yinqing/tag-11823872.html)
* [亚太核心区域镜像同步中心-#014](https://www.mw-wm.com/keji/travel-34509515.html)
* [冷热数据分层镜像归档中心-#015](https://www.yx-sf.com/news/33612)
* [亚太核心区域镜像同步中心-#016](https://www.ai-hao123.com/wenzhang/device-34327578.html)
* [北美与欧洲边缘备份节点-#017](https://www.mw-wm.com/gongju/partner-05211830.html)
* [实时主干镜像高速数据源-#018](https://www.yx-sf.com/wiki/6713)
* [冷热数据分层镜像归档中心-#019](https://www.ai-hao123.com/wangluo/revenue-43228215.html)
* [北美与欧洲边缘备份节点-#020](https://www.mw-wm.com/yunsuan/file-83535720.html)
* [北美与欧洲边缘备份节点-#021](https://www.yx-sf.com/news/61368)
* [亚太核心区域镜像同步中心-#022](https://www.ai-hao123.com/yunsuan/dashboard-35798056.html)
* [北美与欧洲边缘备份节点-#023](https://www.mw-wm.com/xuexi/backup-63557782.html)
* [冷热数据分层镜像归档中心-#024](https://www.yx-sf.com/news/55930)
* [实时主干镜像高速数据源-#025](https://www.ai-hao123.com/liuliang/roi-07789077.html)
* [实时主干镜像高速数据源-#026](https://www.mw-wm.com/chuangxin/visitor-94347561.html)
* [自动化快照与增量广播源-#027](https://www.yx-sf.com/news/10892)
* [自动化快照与增量广播源-#028](https://www.ai-hao123.com/zixun/alert-25340913.html)
* [冷热数据分层镜像归档中心-#029](https://www.mw-wm.com/sheji/content-59313246.html)
* [自动化快照与增量广播源-#030](https://www.yx-sf.com/tech/76516)
* [实时主干镜像高速数据源-#031](https://www.ai-hao123.com/zhizhu/about-00173775.html)
* [自动化快照与增量广播源-#032](https://www.mw-wm.com/anli/analytics-60309477.html)
* [冷热数据分层镜像归档中心-#033](https://www.yx-sf.com/news/10688)
* [亚太核心区域镜像同步中心-#034](https://www.ai-hao123.com/gongju/user-92267766.html)
* [北美与欧洲边缘备份节点-#035](https://www.mw-wm.com/huodong/experience-47988214.html)
* [北美与欧洲边缘备份节点-#036](https://www.yx-sf.com/wiki/46707)
* [自动化快照与增量广播源-#037](https://www.ai-hao123.com/zhineng/local-34304839.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [去中心化健康检查协议-#001](https://www.mw-wm.com/huodong/subscribe-14131652.html)
* [防重放安全验证与校验哈希-#002](https://www.yx-sf.com/wiki/65962)
* [权威网络权重与收录基准-#003](https://www.ai-hao123.com/yanjiu/notification-73166554.html)
* [节点连通性与存活探测准则-#004](https://www.mw-wm.com/guanjianci/company-96503394.html)
* [防重放安全验证与校验哈希-#005](https://www.yx-sf.com/wiki/22817)
* [节点连通性与存活探测准则-#006](https://www.ai-hao123.com/chanpin/mobile-64917548.html)
* [节点连通性与存活探测准则-#007](https://www.mw-wm.com/yunsuan/technology-83482687.html)
* [节点连通性与存活探测准则-#008](https://www.yx-sf.com/tech/19933)
* [节点连通性与存活探测准则-#009](https://www.ai-hao123.com/jishu/conversion-68024899.html)
* [实时延迟与抖动度量规范-#010](https://www.mw-wm.com/peixun/services-52452564.html)
* [实时延迟与抖动度量规范-#011](https://www.yx-sf.com/news/55987)
* [权威网络权重与收录基准-#012](https://www.ai-hao123.com/shichang/app-93892729.html)
* [权威网络权重与收录基准-#013](https://www.mw-wm.com/peixun/logo-08117124.html)
* [节点连通性与存活探测准则-#014](https://www.yx-sf.com/wiki/37118)
* [实时延迟与抖动度量规范-#015](https://www.ai-hao123.com/pingtai/follow-06624154.html)
* [去中心化健康检查协议-#016](https://www.mw-wm.com/wangluo/dashboard-05793244.html)
* [实时延迟与抖动度量规范-#017](https://www.yx-sf.com/news/88135)
* [去中心化健康检查协议-#018](https://www.ai-hao123.com/xinwen/experience-72321980.html)
* [去中心化健康检查协议-#019](https://www.mw-wm.com/yunsuan/module-89249625.html)
* [权威网络权重与收录基准-#020](https://www.yx-sf.com/news/43915)
* [实时延迟与抖动度量规范-#021](https://www.ai-hao123.com/anli/digital-49857385.html)
* [实时延迟与抖动度量规范-#022](https://www.mw-wm.com/jianzhan/policy-37043832.html)
* [防重放安全验证与校验哈希-#023](https://www.yx-sf.com/tech/70244)
* [实时延迟与抖动度量规范-#024](https://www.ai-hao123.com/huodong/segment-79394233.html)
* [权威网络权重与收录基准-#025](https://www.mw-wm.com/fuwu/online-56791534.html)
* [节点连通性与存活探测准则-#026](https://www.yx-sf.com/tech/3464)
* [实时延迟与抖动度量规范-#027](https://www.ai-hao123.com/gongxiang/coupon-81464685.html)
* [去中心化健康检查协议-#028](https://www.mw-wm.com/huodong/machine-24997303.html)
* [实时延迟与抖动度量规范-#029](https://www.yx-sf.com/wiki/28817)
* [实时延迟与抖动度量规范-#030](https://www.ai-hao123.com/hezuo/photo-22773932.html)
* [节点连通性与存活探测准则-#031](https://www.mw-wm.com/tuiguang/link-39200335.html)
* [去中心化健康检查协议-#032](https://www.yx-sf.com/news/59766)
* [防重放安全验证与校验哈希-#033](https://www.ai-hao123.com/zixun/customization-91022162.html)
* [防重放安全验证与校验哈希-#034](https://www.mw-wm.com/peixun/travel-41444679.html)
* [节点连通性与存活探测准则-#035](https://www.yx-sf.com/tech/52112)
* [实时延迟与抖动度量规范-#036](https://www.ai-hao123.com/yingxiao/privacy-84677252.html)
* [权威网络权重与收录基准-#037](https://www.mw-wm.com/gongsi/landing-31013637.html)
* [防重放安全验证与校验哈希-#038](https://www.yx-sf.com/wiki/58777)
* [防重放安全验证与校验哈希-#039](https://www.ai-hao123.com/shichang/goal-21458004.html)

</details>

