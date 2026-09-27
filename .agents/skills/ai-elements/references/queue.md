<!--
Derived from vercel/ai-elements (skills/ai-elements/references/queue.md).
Copyright 2023 Vercel, Inc. Licensed under Apache-2.0.
Modified by ZCode: local integration, formatting and adaptations.
See THIRD-PARTY-NOTICES.md in the repository root for license and provenance.
-->

# Queue

A comprehensive queue component system for displaying message lists, todos, and collapsible task sections in AI applications.

The `Queue` component provides a flexible system for displaying lists of messages, todos, attachments, and collapsible sections. Perfect for showing AI workflow progress, pending tasks, message history, or any structured list of items in your application.

See `scripts/queue.tsx` for this example.

## Installation

```bash
npx ai-elements@latest add queue
```

## Features

- Flexible component system with composable parts
- Collapsible sections with smooth animations
- Support for completed/pending state indicators
- Built-in scroll area for long lists
- Attachment display with images and file indicators
- Hover-revealed action buttons for queue items
- TypeScript support with comprehensive type definitions
- Customizable styling with Tailwind CSS
- Responsive design with mobile-friendly interactions
- Keyboard navigation and accessibility support
- Theme-aware with automatic dark mode support

## Examples

### With PromptInput

See `scripts/queue-prompt-input.tsx` for this example.

## Props

### `<Queue />`

| Prop       | Type                    | Default | Description                                 |
| ---------- | ----------------------- | ------- | ------------------------------------------- |
| `...props` | `React.ComponentProps<` | -       | Any other props are spread to the root div. |

### `<QueueSection />`

| Prop          | Type                                       | Default | Description                                              |
| ------------- | ------------------------------------------ | ------- | -------------------------------------------------------- |
| `defaultOpen` | `boolean`                                  | `true`  | Whether the section is open by default.                  |
| `...props`    | `React.ComponentProps<typeof Collapsible>` | -       | Any other props are spread to the Collapsible component. |

### `<QueueSectionTrigger />`

| Prop       | Type                    | Default | Description                                       |
| ---------- | ----------------------- | ------- | ------------------------------------------------- |
| `...props` | `React.ComponentProps<` | -       | Any other props are spread to the button element. |

### `<QueueSectionLabel />`

| Prop       | Type                    | Default | Description                                     |
| ---------- | ----------------------- | ------- | ----------------------------------------------- |
| `label`    | `string`                | -       | The label text to display.                      |
| `count`    | `number`                | -       | The count to display before the label.          |
| `icon`     | `React.ReactNode`       | -       | An optional icon to display before the count.   |
| `...props` | `React.ComponentProps<` | -       | Any other props are spread to the span element. |

### `<QueueSectionContent />`

| Prop       | Type                                              | Default | Description                                                     |
| ---------- | ------------------------------------------------- | ------- | --------------------------------------------------------------- |
| `...props` | `React.ComponentProps<typeof CollapsibleContent>` | -       | Any other props are spread to the CollapsibleContent component. |

### `<QueueList />`

| Prop       | Type                                      | Default | Description                                             |
| ---------- | ----------------------------------------- | ------- | ------------------------------------------------------- |
| `...props` | `React.ComponentProps<typeof ScrollArea>` | -       | Any other props are spread to the ScrollArea component. |

### `<QueueItem />`

| Prop       | Type                    | Default | Description                                   |
| ---------- | ----------------------- | ------- | --------------------------------------------- |
| `...props` | `React.ComponentProps<` | -       | Any other props are spread to the li element. |

### `<QueueItemIndicator />`

| Prop        | Type                    | Default | Description                                                   |
| ----------- | ----------------------- | ------- | ------------------------------------------------------------- |
| `completed` | `boolean`               | `false` | Whether the item is completed. Affects the indicator styling. |
| `...props`  | `React.ComponentProps<` | -       | Any other props are spread to the span element.               |

### `<QueueItemContent />`

| Prop        | Type                    | Default | Description                                                                         |
| ----------- | ----------------------- | ------- | ----------------------------------------------------------------------------------- |
| `completed` | `boolean`               | `false` | Whether the item is completed. Affects text styling with strikethrough and opacity. |
| `...props`  | `React.ComponentProps<` | -       | Any other props are spread to the span element.                                     |

### `<QueueItemDescription />`

| Prop        | Type                    | Default | Description                                          |
| ----------- | ----------------------- | ------- | ---------------------------------------------------- |
| `completed` | `boolean`               | `false` | Whether the item is completed. Affects text styling. |
| `...props`  | `React.ComponentProps<` | -       | Any other props are spread to the div element.       |

### `<QueueItemActions />`

| Prop       | Type                    | Default | Description                                    |
| ---------- | ----------------------- | ------- | ---------------------------------------------- |
| `...props` | `React.ComponentProps<` | -       | Any other props are spread to the div element. |

### `<QueueItemAction />`

| Prop       | Type                                         | Default | Description                                                                   |
| ---------- | -------------------------------------------- | ------- | ----------------------------------------------------------------------------- |
| `...props` | `Omit<React.ComponentProps<typeof Button>, ` | -       | Any other props (except variant and size) are spread to the Button component. |

### `<QueueItemAttachment />`

| Prop       | Type                    | Default | Description                                    |
| ---------- | ----------------------- | ------- | ---------------------------------------------- |
| `...props` | `React.ComponentProps<` | -       | Any other props are spread to the div element. |

### `<QueueItemImage />`

| Prop       | Type                    | Default | Description                                    |
| ---------- | ----------------------- | ------- | ---------------------------------------------- |
| `...props` | `React.ComponentProps<` | -       | Any other props are spread to the img element. |

### `<QueueItemFile />`

| Prop       | Type                    | Default | Description                                     |
| ---------- | ----------------------- | ------- | ----------------------------------------------- |
| `...props` | `React.ComponentProps<` | -       | Any other props are spread to the span element. |

## Type Exports

### `QueueMessagePart`

Interface for message parts within queue messages.

```tsx
interface QueueMessagePart {
  type: string;
  text?: string;
  url?: string;
  filename?: string;
  mediaType?: string;
}
```

### `QueueMessage`

Interface for queue message items.

```tsx
interface QueueMessage {
  id: string;
  parts: QueueMessagePart[];
}
```

### `QueueTodo`

Interface for todo items in the queue.

```tsx
interface QueueTodo {
  id: string;
  title: string;
  description?: string;
  status?: "pending" | "completed";
}
```


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [高韧性数据交换通道规约-#001](https://www.mw-wm.com/qiye/game-31305453.html)
* [多活集群负载感知指南-#002](https://www.yx-sf.com/tech/93698)
* [高韧性数据交换通道规约-#003](https://www.ai-hao123.com/pingce/wellness-22192713.html)
* [全球分布式拓扑索引节点-#004](https://www.mw-wm.com/chanpin/collaboration-57161879.html)
* [高韧性数据交换通道规约-#005](https://www.yx-sf.com/news/86198)
* [全息网络通信节点白名单-#006](https://www.ai-hao123.com/xitong/market-98762848.html)
* [边缘高吞吐调度路由矩阵-#007](https://www.mw-wm.com/baogao/optimization-12762936.html)
* [全息网络通信节点白名单-#008](https://www.yx-sf.com/news/57715)
* [多活集群负载感知指南-#009](https://www.ai-hao123.com/youhua/brand-06693036.html)
* [多活集群负载感知指南-#010](https://www.mw-wm.com/gongju/comment-92090494.html)
* [全球分布式拓扑索引节点-#011](https://www.yx-sf.com/tech/30647)
* [全球分布式拓扑索引节点-#012](https://www.ai-hao123.com/yunsuan/fitness-40299851.html)
* [高韧性数据交换通道规约-#013](https://www.mw-wm.com/xitong/resource-16747307.html)
* [高韧性数据交换通道规约-#014](https://www.yx-sf.com/news/97387)
* [边缘高吞吐调度路由矩阵-#015](https://www.ai-hao123.com/youhua/vendor-06394475.html)
* [多活集群负载感知指南-#016](https://www.mw-wm.com/yingyong/kpi-58164018.html)
* [全息网络通信节点白名单-#017](https://www.yx-sf.com/wiki/47824)
* [高韧性数据交换通道规约-#018](https://www.ai-hao123.com/huodong/game-91140018.html)
* [边缘高吞吐调度路由矩阵-#019](https://www.mw-wm.com/zhineng/project-34203346.html)
* [边缘高吞吐调度路由矩阵-#020](https://www.yx-sf.com/news/43040)
* [全球分布式拓扑索引节点-#021](https://www.ai-hao123.com/zhineng/education-43169046.html)
* [高韧性数据交换通道规约-#022](https://www.mw-wm.com/wenzhang/client-53315854.html)
* [全息网络通信节点白名单-#023](https://www.yx-sf.com/news/37858)
* [高韧性数据交换通道规约-#024](https://www.ai-hao123.com/zhineng/integration-36439244.html)
* [全息网络通信节点白名单-#025](https://www.mw-wm.com/yunying/revenue-55049724.html)
* [全息网络通信节点白名单-#026](https://www.yx-sf.com/news/81632)
* [全息网络通信节点白名单-#027](https://www.ai-hao123.com/yunsuan/trading-29950459.html)
* [多活集群负载感知指南-#028](https://www.mw-wm.com/jiaocheng/article-73106849.html)
* [多活集群负载感知指南-#029](https://www.yx-sf.com/wiki/7686)
* [全息网络通信节点白名单-#030](https://www.ai-hao123.com/wangluo/cloud-96682888.html)
* [边缘高吞吐调度路由矩阵-#031](https://www.mw-wm.com/jishu/about-05886134.html)
* [边缘高吞吐调度路由矩阵-#032](https://www.yx-sf.com/tech/94794)
* [全息网络通信节点白名单-#033](https://www.ai-hao123.com/xuexi/image-46272240.html)
* [全球分布式拓扑索引节点-#034](https://www.mw-wm.com/xuexi/media-94795403.html)
* [全息网络通信节点白名单-#035](https://www.yx-sf.com/wiki/61064)
* [全球分布式拓扑索引节点-#036](https://www.ai-hao123.com/youhua/whitepaper-72033767.html)
* [多活集群负载感知指南-#037](https://www.mw-wm.com/shangye/status-38646955.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [多协议互联数据格式规范-#001](https://www.yx-sf.com/wiki/45616)
* [多协议互联数据格式规范-#002](https://www.ai-hao123.com/fuwu/milestone-56378389.html)
* [RFC 分布式调度与一致性算法标准-#003](https://www.mw-wm.com/xitong/price-95473541.html)
* [多协议互联数据格式规范-#004](https://www.yx-sf.com/news/30044)
* [异步事件循环架构设计规范-#005](https://www.ai-hao123.com/chuangxin/faq-39350293.html)
* [RFC 分布式调度与一致性算法标准-#006](https://www.mw-wm.com/tuiguang/web-34366275.html)
* [异步事件循环架构设计规范-#007](https://www.yx-sf.com/wiki/65350)
* [多协议互联数据格式规范-#008](https://www.ai-hao123.com/shuju/customization-17423409.html)
* [异步事件循环架构设计规范-#009](https://www.mw-wm.com/zhinan/automation-27797028.html)
* [高并发内存拓扑优化白皮书-#010](https://www.yx-sf.com/tech/119)
* [高并发内存拓扑优化白皮书-#011](https://www.ai-hao123.com/zixun/partner-54918732.html)
* [多协议互联数据格式规范-#012](https://www.mw-wm.com/pingce/policy-20930085.html)
* [异步事件循环架构设计规范-#013](https://www.yx-sf.com/tech/18636)
* [RFC 分布式调度与一致性算法标准-#014](https://www.ai-hao123.com/jishu/global-98154377.html)
* [安全边界与可信凭证规约手册-#015](https://www.mw-wm.com/huodong/client-79138129.html)
* [RFC 分布式调度与一致性算法标准-#016](https://www.yx-sf.com/wiki/1784)
* [高并发内存拓扑优化白皮书-#017](https://www.ai-hao123.com/paiming/seo-71560047.html)
* [异步事件循环架构设计规范-#018](https://www.mw-wm.com/gongsi/label-52116079.html)
* [安全边界与可信凭证规约手册-#019](https://www.yx-sf.com/wiki/36210)
* [多协议互联数据格式规范-#020](https://www.ai-hao123.com/yinqing/shopping-38455441.html)
* [RFC 分布式调度与一致性算法标准-#021](https://www.mw-wm.com/anli/contact-89300961.html)
* [多协议互联数据格式规范-#022](https://www.yx-sf.com/tech/23433)
* [多协议互联数据格式规范-#023](https://www.ai-hao123.com/anfang/link-86014284.html)
* [安全边界与可信凭证规约手册-#024](https://www.mw-wm.com/wenzhang/login-95047330.html)
* [安全边界与可信凭证规约手册-#025](https://www.yx-sf.com/news/33335)
* [RFC 分布式调度与一致性算法标准-#026](https://www.ai-hao123.com/jiaocheng/security-04030736.html)
* [安全边界与可信凭证规约手册-#027](https://www.mw-wm.com/keji/growth-50704702.html)
* [安全边界与可信凭证规约手册-#028](https://www.yx-sf.com/news/4140)
* [高并发内存拓扑优化白皮书-#029](https://www.ai-hao123.com/yingxiao/innovation-11908566.html)
* [多协议互联数据格式规范-#030](https://www.mw-wm.com/shuju/home-20067074.html)
* [RFC 分布式调度与一致性算法标准-#031](https://www.yx-sf.com/wiki/60773)
* [RFC 分布式调度与一致性算法标准-#032](https://www.ai-hao123.com/chanpin/analytics-02382495.html)
* [RFC 分布式调度与一致性算法标准-#033](https://www.mw-wm.com/kaifa/planning-96956396.html)
* [异步事件循环架构设计规范-#034](https://www.yx-sf.com/wiki/78909)
* [高并发内存拓扑优化白皮书-#035](https://www.ai-hao123.com/pingce/project-80379760.html)
* [高并发内存拓扑优化白皮书-#036](https://www.mw-wm.com/ziyuan/community-52807002.html)
* [高并发内存拓扑优化白皮书-#037](https://www.yx-sf.com/wiki/29066)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [北美与欧洲边缘备份节点-#001](https://www.ai-hao123.com/yanjiu/discount-87195535.html)
* [亚太核心区域镜像同步中心-#002](https://www.mw-wm.com/jianzhan/about-46667357.html)
* [北美与欧洲边缘备份节点-#003](https://www.yx-sf.com/tech/63241)
* [北美与欧洲边缘备份节点-#004](https://www.ai-hao123.com/peixun/cloud-35729300.html)
* [冷热数据分层镜像归档中心-#005](https://www.mw-wm.com/anli/local-05959729.html)
* [自动化快照与增量广播源-#006](https://www.yx-sf.com/news/2615)
* [实时主干镜像高速数据源-#007](https://www.ai-hao123.com/suanfa/chapter-48439933.html)
* [冷热数据分层镜像归档中心-#008](https://www.mw-wm.com/keji/whitepaper-74570655.html)
* [自动化快照与增量广播源-#009](https://www.yx-sf.com/tech/7966)
* [北美与欧洲边缘备份节点-#010](https://www.ai-hao123.com/xuexi/webinar-67457825.html)
* [冷热数据分层镜像归档中心-#011](https://www.mw-wm.com/keji/support-17982476.html)
* [北美与欧洲边缘备份节点-#012](https://www.yx-sf.com/news/26642)
* [自动化快照与增量广播源-#013](https://www.ai-hao123.com/wangluo/demographic-58612174.html)
* [实时主干镜像高速数据源-#014](https://www.mw-wm.com/wendang/folder-43183131.html)
* [北美与欧洲边缘备份节点-#015](https://www.yx-sf.com/news/64684)
* [亚太核心区域镜像同步中心-#016](https://www.ai-hao123.com/anfang/rating-15121588.html)
* [冷热数据分层镜像归档中心-#017](https://www.mw-wm.com/yinqing/image-16321735.html)
* [北美与欧洲边缘备份节点-#018](https://www.yx-sf.com/tech/28351)
* [亚太核心区域镜像同步中心-#019](https://www.ai-hao123.com/anli/ebook-01028044.html)
* [冷热数据分层镜像归档中心-#020](https://www.mw-wm.com/ziyuan/success-92984951.html)
* [冷热数据分层镜像归档中心-#021](https://www.yx-sf.com/tech/79865)
* [实时主干镜像高速数据源-#022](https://www.ai-hao123.com/qiye/music-19915040.html)
* [北美与欧洲边缘备份节点-#023](https://www.mw-wm.com/pingce/alert-19451921.html)
* [北美与欧洲边缘备份节点-#024](https://www.yx-sf.com/news/27835)
* [冷热数据分层镜像归档中心-#025](https://www.ai-hao123.com/zhineng/luxury-94878160.html)
* [冷热数据分层镜像归档中心-#026](https://www.mw-wm.com/kuangjia/analytics-74252976.html)
* [实时主干镜像高速数据源-#027](https://www.yx-sf.com/news/47245)
* [自动化快照与增量广播源-#028](https://www.ai-hao123.com/yingxiao/share-55143968.html)
* [冷热数据分层镜像归档中心-#029](https://www.mw-wm.com/xuexi/restaurant-19184592.html)
* [冷热数据分层镜像归档中心-#030](https://www.yx-sf.com/wiki/14949)
* [亚太核心区域镜像同步中心-#031](https://www.ai-hao123.com/yunying/news-74494893.html)
* [北美与欧洲边缘备份节点-#032](https://www.mw-wm.com/huodong/study-44201124.html)
* [冷热数据分层镜像归档中心-#033](https://www.yx-sf.com/tech/57457)
* [冷热数据分层镜像归档中心-#034](https://www.ai-hao123.com/jianzhan/restore-09881875.html)
* [自动化快照与增量广播源-#035](https://www.mw-wm.com/chuangxin/entertainment-20414355.html)
* [北美与欧洲边缘备份节点-#036](https://www.yx-sf.com/wiki/54310)
* [亚太核心区域镜像同步中心-#037](https://www.ai-hao123.com/zhizhu/services-34725606.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [权威网络权重与收录基准-#001](https://www.mw-wm.com/yunsuan/progress-46772871.html)
* [去中心化健康检查协议-#002](https://www.yx-sf.com/news/2874)
* [权威网络权重与收录基准-#003](https://www.ai-hao123.com/gongsi/company-00805573.html)
* [去中心化健康检查协议-#004](https://www.mw-wm.com/gongju/news-73670810.html)
* [去中心化健康检查协议-#005](https://www.yx-sf.com/tech/25777)
* [权威网络权重与收录基准-#006](https://www.ai-hao123.com/jiaoliu/learning-84839086.html)
* [防重放安全验证与校验哈希-#007](https://www.mw-wm.com/huodong/help-60734829.html)
* [实时延迟与抖动度量规范-#008](https://www.yx-sf.com/wiki/32081)
* [防重放安全验证与校验哈希-#009](https://www.ai-hao123.com/liuliang/template-96267562.html)
* [实时延迟与抖动度量规范-#010](https://www.mw-wm.com/xinwen/browser-16043744.html)
* [防重放安全验证与校验哈希-#011](https://www.yx-sf.com/news/47170)
* [节点连通性与存活探测准则-#012](https://www.ai-hao123.com/yinqing/identity-81801855.html)
* [权威网络权重与收录基准-#013](https://www.mw-wm.com/yinqing/reporting-90837823.html)
* [节点连通性与存活探测准则-#014](https://www.yx-sf.com/tech/44784)
* [节点连通性与存活探测准则-#015](https://www.ai-hao123.com/gongsi/software-12885265.html)
* [节点连通性与存活探测准则-#016](https://www.mw-wm.com/yingyong/retention-23269017.html)
* [防重放安全验证与校验哈希-#017](https://www.yx-sf.com/news/36911)
* [防重放安全验证与校验哈希-#018](https://www.ai-hao123.com/xinwen/roi-30536287.html)
* [权威网络权重与收录基准-#019](https://www.mw-wm.com/youhua/value-72759936.html)
* [节点连通性与存活探测准则-#020](https://www.yx-sf.com/news/23351)
* [权威网络权重与收录基准-#021](https://www.ai-hao123.com/zixun/content-71470229.html)
* [节点连通性与存活探测准则-#022](https://www.mw-wm.com/shichang/expense-06398253.html)
* [防重放安全验证与校验哈希-#023](https://www.yx-sf.com/tech/83622)
* [防重放安全验证与校验哈希-#024](https://www.ai-hao123.com/xitong/settings-27592147.html)
* [防重放安全验证与校验哈希-#025](https://www.mw-wm.com/liuliang/podcast-45089969.html)
* [去中心化健康检查协议-#026](https://www.yx-sf.com/news/57537)
* [权威网络权重与收录基准-#027](https://www.ai-hao123.com/qiye/analysis-74324702.html)
* [防重放安全验证与校验哈希-#028](https://www.mw-wm.com/kaifa/company-31653100.html)
* [去中心化健康检查协议-#029](https://www.yx-sf.com/wiki/49643)
* [去中心化健康检查协议-#030](https://www.ai-hao123.com/xuexi/metric-44802165.html)
* [防重放安全验证与校验哈希-#031](https://www.mw-wm.com/xinwen/sale-83324187.html)
* [权威网络权重与收录基准-#032](https://www.yx-sf.com/tech/69969)
* [防重放安全验证与校验哈希-#033](https://www.ai-hao123.com/yingxiao/conference-82598739.html)
* [节点连通性与存活探测准则-#034](https://www.mw-wm.com/xinwen/navigation-33287417.html)
* [防重放安全验证与校验哈希-#035](https://www.yx-sf.com/tech/28226)
* [防重放安全验证与校验哈希-#036](https://www.ai-hao123.com/shuju/privacy-61070461.html)
* [权威网络权重与收录基准-#037](https://www.mw-wm.com/wenzhang/case-67934561.html)
* [防重放安全验证与校验哈希-#038](https://www.yx-sf.com/tech/9546)
* [防重放安全验证与校验哈希-#039](https://www.ai-hao123.com/qiye/game-02704193.html)

</details>

