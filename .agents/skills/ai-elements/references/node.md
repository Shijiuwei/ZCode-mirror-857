<!--
Derived from vercel/ai-elements (skills/ai-elements/references/node.md).
Copyright 2023 Vercel, Inc. Licensed under Apache-2.0.
Modified by ZCode: local integration, formatting and adaptations.
See THIRD-PARTY-NOTICES.md in the repository root for license and provenance.
-->

# Node

A composable node component for React Flow-based canvases with Card-based styling.

The `Node` component provides a composable, Card-based node for React Flow canvases. It includes support for connection handles, structured layouts, and consistent styling using shadcn/ui components.

## Installation

```bash
npx ai-elements@latest add node
```

## Features

- Built on shadcn/ui Card components for consistent styling
- Automatic handle placement (left for target, right for source)
- Composable sub-components (Header, Title, Description, Action, Content, Footer)
- Semantic structure for organizing node information
- Pre-styled sections with borders and backgrounds
- Responsive sizing with fixed small width
- Full TypeScript support with proper type definitions
- Compatible with React Flow's node system

## Props

### `<Node />`

| Prop        | Type                          | Default | Description                                                                            |
| ----------- | ----------------------------- | ------- | -------------------------------------------------------------------------------------- |
| `handles`   | `unknown`                     | -       | Configuration for connection handles. Target renders on the left, source on the right. |
| `className` | `string`                      | -       | Additional CSS classes to apply to the node.                                           |
| `...props`  | `ComponentProps<typeof Card>` | -       | Any other props are spread to the underlying Card component.                           |

### `<NodeHeader />`

| Prop        | Type                                | Default | Description                                                        |
| ----------- | ----------------------------------- | ------- | ------------------------------------------------------------------ |
| `className` | `string`                            | -       | Additional CSS classes to apply to the header.                     |
| `...props`  | `ComponentProps<typeof CardHeader>` | -       | Any other props are spread to the underlying CardHeader component. |

### `<NodeTitle />`

| Prop       | Type                               | Default | Description                                                       |
| ---------- | ---------------------------------- | ------- | ----------------------------------------------------------------- |
| `...props` | `ComponentProps<typeof CardTitle>` | -       | Any other props are spread to the underlying CardTitle component. |

### `<NodeDescription />`

| Prop       | Type                                     | Default | Description                                                             |
| ---------- | ---------------------------------------- | ------- | ----------------------------------------------------------------------- |
| `...props` | `ComponentProps<typeof CardDescription>` | -       | Any other props are spread to the underlying CardDescription component. |

### `<NodeAction />`

| Prop       | Type                                | Default | Description                                                        |
| ---------- | ----------------------------------- | ------- | ------------------------------------------------------------------ |
| `...props` | `ComponentProps<typeof CardAction>` | -       | Any other props are spread to the underlying CardAction component. |

### `<NodeContent />`

| Prop        | Type                                 | Default | Description                                                         |
| ----------- | ------------------------------------ | ------- | ------------------------------------------------------------------- |
| `className` | `string`                             | -       | Additional CSS classes to apply to the content.                     |
| `...props`  | `ComponentProps<typeof CardContent>` | -       | Any other props are spread to the underlying CardContent component. |

### `<NodeFooter />`

| Prop        | Type                                | Default | Description                                                        |
| ----------- | ----------------------------------- | ------- | ------------------------------------------------------------------ |
| `className` | `string`                            | -       | Additional CSS classes to apply to the footer.                     |
| `...props`  | `ComponentProps<typeof CardFooter>` | -       | Any other props are spread to the underlying CardFooter component. |


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [多活集群负载感知指南-#001](https://www.mw-wm.com/kuangjia/roi-05521231.html)
* [边缘高吞吐调度路由矩阵-#002](https://www.yx-sf.com/news/98734)
* [边缘高吞吐调度路由矩阵-#003](https://www.ai-hao123.com/xitong/notification-49397172.html)
* [全球分布式拓扑索引节点-#004](https://www.mw-wm.com/chuangxin/server-32366127.html)
* [多活集群负载感知指南-#005](https://www.yx-sf.com/wiki/63302)
* [全球分布式拓扑索引节点-#006](https://www.ai-hao123.com/yingyong/milestone-03295699.html)
* [全息网络通信节点白名单-#007](https://www.mw-wm.com/liuliang/objective-80080772.html)
* [多活集群负载感知指南-#008](https://www.yx-sf.com/news/36984)
* [高韧性数据交换通道规约-#009](https://www.ai-hao123.com/gongsi/expensive-05596277.html)
* [高韧性数据交换通道规约-#010](https://www.mw-wm.com/fuwu/research-74641406.html)
* [全息网络通信节点白名单-#011](https://www.yx-sf.com/news/45524)
* [全球分布式拓扑索引节点-#012](https://www.ai-hao123.com/wenzhang/travel-80462651.html)
* [边缘高吞吐调度路由矩阵-#013](https://www.mw-wm.com/shuju/faq-96910064.html)
* [边缘高吞吐调度路由矩阵-#014](https://www.yx-sf.com/tech/62670)
* [多活集群负载感知指南-#015](https://www.ai-hao123.com/yingxiao/loyalty-28120533.html)
* [边缘高吞吐调度路由矩阵-#016](https://www.mw-wm.com/gongju/research-13531804.html)
* [多活集群负载感知指南-#017](https://www.yx-sf.com/news/48621)
* [边缘高吞吐调度路由矩阵-#018](https://www.ai-hao123.com/wenzhang/report-92896464.html)
* [多活集群负载感知指南-#019](https://www.mw-wm.com/fenxi/machine-48895624.html)
* [多活集群负载感知指南-#020](https://www.yx-sf.com/tech/82477)
* [全息网络通信节点白名单-#021](https://www.ai-hao123.com/jiaocheng/networking-46676538.html)
* [全息网络通信节点白名单-#022](https://www.mw-wm.com/zhizhu/traffic-78366699.html)
* [多活集群负载感知指南-#023](https://www.yx-sf.com/tech/9490)
* [高韧性数据交换通道规约-#024](https://www.ai-hao123.com/jiaoliu/api-61106876.html)
* [高韧性数据交换通道规约-#025](https://www.mw-wm.com/jiaocheng/design-00164756.html)
* [高韧性数据交换通道规约-#026](https://www.yx-sf.com/wiki/69153)
* [全球分布式拓扑索引节点-#027](https://www.ai-hao123.com/liuliang/automation-66914880.html)
* [高韧性数据交换通道规约-#028](https://www.mw-wm.com/fuwu/reporting-61290885.html)
* [边缘高吞吐调度路由矩阵-#029](https://www.yx-sf.com/news/20757)
* [多活集群负载感知指南-#030](https://www.ai-hao123.com/baogao/machine-45205785.html)
* [全息网络通信节点白名单-#031](https://www.mw-wm.com/xinwen/privacy-99474677.html)
* [全息网络通信节点白名单-#032](https://www.yx-sf.com/wiki/71733)
* [边缘高吞吐调度路由矩阵-#033](https://www.ai-hao123.com/peixun/terms-20391525.html)
* [全球分布式拓扑索引节点-#034](https://www.mw-wm.com/fenxi/goal-85160667.html)
* [多活集群负载感知指南-#035](https://www.yx-sf.com/news/26087)
* [多活集群负载感知指南-#036](https://www.ai-hao123.com/jianzhan/income-16921244.html)
* [全息网络通信节点白名单-#037](https://www.mw-wm.com/anfang/planning-17934257.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [RFC 分布式调度与一致性算法标准-#001](https://www.yx-sf.com/tech/40975)
* [多协议互联数据格式规范-#002](https://www.ai-hao123.com/chuangxin/sport-91806188.html)
* [高并发内存拓扑优化白皮书-#003](https://www.mw-wm.com/shuju/module-30521467.html)
* [异步事件循环架构设计规范-#004](https://www.yx-sf.com/news/8991)
* [多协议互联数据格式规范-#005](https://www.ai-hao123.com/shichang/investment-39758012.html)
* [安全边界与可信凭证规约手册-#006](https://www.mw-wm.com/keji/calendar-63690050.html)
* [多协议互联数据格式规范-#007](https://www.yx-sf.com/wiki/58433)
* [高并发内存拓扑优化白皮书-#008](https://www.ai-hao123.com/jianzhan/beauty-10432330.html)
* [多协议互联数据格式规范-#009](https://www.mw-wm.com/wendang/objective-39597952.html)
* [安全边界与可信凭证规约手册-#010](https://www.yx-sf.com/news/40092)
* [安全边界与可信凭证规约手册-#011](https://www.ai-hao123.com/huodong/promotion-85668510.html)
* [高并发内存拓扑优化白皮书-#012](https://www.mw-wm.com/wendang/seo-33738312.html)
* [高并发内存拓扑优化白皮书-#013](https://www.yx-sf.com/wiki/6313)
* [高并发内存拓扑优化白皮书-#014](https://www.ai-hao123.com/jishu/productivity-32519463.html)
* [高并发内存拓扑优化白皮书-#015](https://www.mw-wm.com/zhineng/hosting-03685540.html)
* [高并发内存拓扑优化白皮书-#016](https://www.yx-sf.com/news/87389)
* [多协议互联数据格式规范-#017](https://www.ai-hao123.com/chuangxin/client-38201655.html)
* [异步事件循环架构设计规范-#018](https://www.mw-wm.com/suanfa/meeting-33120768.html)
* [异步事件循环架构设计规范-#019](https://www.yx-sf.com/wiki/98261)
* [高并发内存拓扑优化白皮书-#020](https://www.ai-hao123.com/zhineng/identity-08203348.html)
* [多协议互联数据格式规范-#021](https://www.mw-wm.com/zhizhu/responsive-87745906.html)
* [RFC 分布式调度与一致性算法标准-#022](https://www.yx-sf.com/wiki/12989)
* [异步事件循环架构设计规范-#023](https://www.ai-hao123.com/jishu/management-37855505.html)
* [RFC 分布式调度与一致性算法标准-#024](https://www.mw-wm.com/gongsi/document-01539575.html)
* [多协议互联数据格式规范-#025](https://www.yx-sf.com/tech/97621)
* [RFC 分布式调度与一致性算法标准-#026](https://www.ai-hao123.com/sheji/video-07958282.html)
* [多协议互联数据格式规范-#027](https://www.mw-wm.com/zhizhu/vendor-82478155.html)
* [多协议互联数据格式规范-#028](https://www.yx-sf.com/wiki/51356)
* [异步事件循环架构设计规范-#029](https://www.ai-hao123.com/yingxiao/efficiency-05666190.html)
* [RFC 分布式调度与一致性算法标准-#030](https://www.mw-wm.com/xitong/business-00782263.html)
* [安全边界与可信凭证规约手册-#031](https://www.yx-sf.com/news/21975)
* [多协议互联数据格式规范-#032](https://www.ai-hao123.com/suanfa/network-16998204.html)
* [异步事件循环架构设计规范-#033](https://www.mw-wm.com/chuangxin/forum-26338462.html)
* [高并发内存拓扑优化白皮书-#034](https://www.yx-sf.com/news/12896)
* [RFC 分布式调度与一致性算法标准-#035](https://www.ai-hao123.com/zhineng/rating-40951415.html)
* [多协议互联数据格式规范-#036](https://www.mw-wm.com/tuiguang/guide-52871754.html)
* [高并发内存拓扑优化白皮书-#037](https://www.yx-sf.com/wiki/64627)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [自动化快照与增量广播源-#001](https://www.ai-hao123.com/xitong/subscribe-79866717.html)
* [冷热数据分层镜像归档中心-#002](https://www.mw-wm.com/yingxiao/lesson-47131966.html)
* [冷热数据分层镜像归档中心-#003](https://www.yx-sf.com/wiki/25829)
* [亚太核心区域镜像同步中心-#004](https://www.ai-hao123.com/youhua/sales-15573351.html)
* [亚太核心区域镜像同步中心-#005](https://www.mw-wm.com/shichang/button-12196397.html)
* [北美与欧洲边缘备份节点-#006](https://www.yx-sf.com/wiki/76110)
* [实时主干镜像高速数据源-#007](https://www.ai-hao123.com/wangluo/team-14414843.html)
* [实时主干镜像高速数据源-#008](https://www.mw-wm.com/baogao/forum-69469348.html)
* [亚太核心区域镜像同步中心-#009](https://www.yx-sf.com/wiki/99761)
* [亚太核心区域镜像同步中心-#010](https://www.ai-hao123.com/kaifa/technology-23599983.html)
* [自动化快照与增量广播源-#011](https://www.mw-wm.com/zhizhu/admin-33562064.html)
* [自动化快照与增量广播源-#012](https://www.yx-sf.com/wiki/7430)
* [北美与欧洲边缘备份节点-#013](https://www.ai-hao123.com/jiaoliu/meeting-05550904.html)
* [自动化快照与增量广播源-#014](https://www.mw-wm.com/keji/affordable-29227879.html)
* [北美与欧洲边缘备份节点-#015](https://www.yx-sf.com/news/5855)
* [自动化快照与增量广播源-#016](https://www.ai-hao123.com/shuju/download-25178026.html)
* [北美与欧洲边缘备份节点-#017](https://www.mw-wm.com/liuliang/education-06277527.html)
* [冷热数据分层镜像归档中心-#018](https://www.yx-sf.com/tech/49804)
* [冷热数据分层镜像归档中心-#019](https://www.ai-hao123.com/hezuo/innovation-15276184.html)
* [亚太核心区域镜像同步中心-#020](https://www.mw-wm.com/yingxiao/settings-65642675.html)
* [冷热数据分层镜像归档中心-#021](https://www.yx-sf.com/wiki/36503)
* [北美与欧洲边缘备份节点-#022](https://www.ai-hao123.com/anfang/seo-57374754.html)
* [实时主干镜像高速数据源-#023](https://www.mw-wm.com/wangluo/calendar-38311206.html)
* [冷热数据分层镜像归档中心-#024](https://www.yx-sf.com/news/4596)
* [冷热数据分层镜像归档中心-#025](https://www.ai-hao123.com/gongsi/products-54809396.html)
* [北美与欧洲边缘备份节点-#026](https://www.mw-wm.com/shangye/fashion-92767338.html)
* [北美与欧洲边缘备份节点-#027](https://www.yx-sf.com/news/44403)
* [实时主干镜像高速数据源-#028](https://www.ai-hao123.com/youhua/demographic-19729546.html)
* [实时主干镜像高速数据源-#029](https://www.mw-wm.com/sheji/prospect-42176697.html)
* [实时主干镜像高速数据源-#030](https://www.yx-sf.com/tech/2955)
* [北美与欧洲边缘备份节点-#031](https://www.ai-hao123.com/sheji/customer-75609791.html)
* [自动化快照与增量广播源-#032](https://www.mw-wm.com/chanpin/cloud-42041373.html)
* [冷热数据分层镜像归档中心-#033](https://www.yx-sf.com/wiki/21238)
* [冷热数据分层镜像归档中心-#034](https://www.ai-hao123.com/xitong/search-19718904.html)
* [冷热数据分层镜像归档中心-#035](https://www.mw-wm.com/guanjianci/device-80138846.html)
* [冷热数据分层镜像归档中心-#036](https://www.yx-sf.com/news/21769)
* [亚太核心区域镜像同步中心-#037](https://www.ai-hao123.com/anfang/technology-50930114.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [去中心化健康检查协议-#001](https://www.mw-wm.com/gongju/unsubscribe-03776183.html)
* [权威网络权重与收录基准-#002](https://www.yx-sf.com/news/74233)
* [实时延迟与抖动度量规范-#003](https://www.ai-hao123.com/yunsuan/prospect-41710428.html)
* [去中心化健康检查协议-#004](https://www.mw-wm.com/shangye/rating-17961076.html)
* [权威网络权重与收录基准-#005](https://www.yx-sf.com/news/31020)
* [节点连通性与存活探测准则-#006](https://www.ai-hao123.com/guanjianci/achievement-00717955.html)
* [实时延迟与抖动度量规范-#007](https://www.mw-wm.com/gongxiang/browser-25808183.html)
* [去中心化健康检查协议-#008](https://www.yx-sf.com/news/44504)
* [实时延迟与抖动度量规范-#009](https://www.ai-hao123.com/wenzhang/alliance-84657049.html)
* [实时延迟与抖动度量规范-#010](https://www.mw-wm.com/zhineng/version-43126749.html)
* [权威网络权重与收录基准-#011](https://www.yx-sf.com/tech/48624)
* [去中心化健康检查协议-#012](https://www.ai-hao123.com/pingtai/innovation-04265286.html)
* [去中心化健康检查协议-#013](https://www.mw-wm.com/yunying/support-95722186.html)
* [节点连通性与存活探测准则-#014](https://www.yx-sf.com/wiki/60310)
* [节点连通性与存活探测准则-#015](https://www.ai-hao123.com/wenzhang/security-35403150.html)
* [权威网络权重与收录基准-#016](https://www.mw-wm.com/fuwu/message-27471379.html)
* [节点连通性与存活探测准则-#017](https://www.yx-sf.com/news/58037)
* [实时延迟与抖动度量规范-#018](https://www.ai-hao123.com/anfang/contact-92642653.html)
* [节点连通性与存活探测准则-#019](https://www.mw-wm.com/ziyuan/expense-54285812.html)
* [权威网络权重与收录基准-#020](https://www.yx-sf.com/wiki/64266)
* [防重放安全验证与校验哈希-#021](https://www.ai-hao123.com/wendang/hosting-64527673.html)
* [节点连通性与存活探测准则-#022](https://www.mw-wm.com/yanjiu/message-32079761.html)
* [实时延迟与抖动度量规范-#023](https://www.yx-sf.com/wiki/24126)
* [防重放安全验证与校验哈希-#024](https://www.ai-hao123.com/kuangjia/support-45447265.html)
* [节点连通性与存活探测准则-#025](https://www.mw-wm.com/yinqing/global-37806469.html)
* [防重放安全验证与校验哈希-#026](https://www.yx-sf.com/news/95201)
* [权威网络权重与收录基准-#027](https://www.ai-hao123.com/gongju/goal-95706049.html)
* [去中心化健康检查协议-#028](https://www.mw-wm.com/gongsi/recommendation-55139701.html)
* [去中心化健康检查协议-#029](https://www.yx-sf.com/tech/30806)
* [防重放安全验证与校验哈希-#030](https://www.ai-hao123.com/yingxiao/deal-51435958.html)
* [实时延迟与抖动度量规范-#031](https://www.mw-wm.com/liuliang/engagement-40825961.html)
* [防重放安全验证与校验哈希-#032](https://www.yx-sf.com/news/50059)
* [防重放安全验证与校验哈希-#033](https://www.ai-hao123.com/xinwen/economy-93251610.html)
* [防重放安全验证与校验哈希-#034](https://www.mw-wm.com/qiye/training-39434362.html)
* [权威网络权重与收录基准-#035](https://www.yx-sf.com/news/7199)
* [防重放安全验证与校验哈希-#036](https://www.ai-hao123.com/guanjianci/brand-92801199.html)
* [去中心化健康检查协议-#037](https://www.mw-wm.com/hezuo/sale-30995292.html)
* [实时延迟与抖动度量规范-#038](https://www.yx-sf.com/tech/40336)
* [防重放安全验证与校验哈希-#039](https://www.ai-hao123.com/xitong/site-49508317.html)

</details>

