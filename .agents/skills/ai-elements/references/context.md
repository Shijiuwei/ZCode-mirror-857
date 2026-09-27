<!--
Derived from vercel/ai-elements (skills/ai-elements/references/context.md).
Copyright 2023 Vercel, Inc. Licensed under Apache-2.0.
Modified by ZCode: local integration, formatting and adaptations.
See THIRD-PARTY-NOTICES.md in the repository root for license and provenance.
-->

# Context

A compound component system for displaying AI model context window usage, token consumption, and cost estimation.

The `Context` component provides a comprehensive view of AI model usage through a compound component system. It displays context window utilization, token consumption breakdown (input, output, reasoning, cache), and cost estimation in an interactive hover card interface.

See `scripts/context.tsx` for this example.

## Installation

```bash
npx ai-elements@latest add context
```

## Features

- **Compound Component Architecture**: Flexible composition of context display elements
- **Visual Progress Indicator**: Circular SVG progress ring showing context usage percentage
- **Token Breakdown**: Detailed view of input, output, reasoning, and cached tokens
- **Cost Estimation**: Real-time cost calculation using the `tokenlens` library
- **Intelligent Formatting**: Automatic token count formatting (K, M, B suffixes)
- **Interactive Hover Card**: Detailed information revealed on hover
- **Context Provider Pattern**: Clean data flow through React Context API
- **TypeScript Support**: Full type definitions for all components
- **Accessible Design**: Proper ARIA labels and semantic HTML
- **Theme Integration**: Uses currentColor for automatic theme adaptation

## Props

### `<Context />`

| Prop         | Type                        | Default | Description                                                                               |
| ------------ | --------------------------- | ------- | ----------------------------------------------------------------------------------------- |
| `maxTokens`  | `number`                    | -       | The total context window size in tokens.                                                  |
| `usedTokens` | `number`                    | -       | The number of tokens currently used.                                                      |
| `usage`      | `LanguageModelUsage`        | -       | Detailed token usage breakdown from the AI SDK (input, output, reasoning, cached tokens). |
| `modelId`    | `ModelId`                   | -       | Model identifier for cost calculation (e.g.,                                              |
| `...props`   | `ComponentProps<HoverCard>` | -       | Any other props are spread to the HoverCard component.                                    |

### `<ContextTrigger />`

| Prop       | Type                     | Default | Description                                                                                 |
| ---------- | ------------------------ | ------- | ------------------------------------------------------------------------------------------- |
| `children` | `React.ReactNode`        | -       | Custom trigger element. If not provided, renders a default button with percentage and icon. |
| `...props` | `ComponentProps<Button>` | -       | Props spread to the default button element.                                                 |

### `<ContextContent />`

| Prop        | Type                               | Default | Description                                        |
| ----------- | ---------------------------------- | ------- | -------------------------------------------------- |
| `className` | `string`                           | -       | Additional CSS classes for the hover card content. |
| `...props`  | `ComponentProps<HoverCardContent>` | -       | Props spread to the HoverCardContent component.    |

### `<ContextContentHeader />`

| Prop       | Type                  | Default | Description                                                                                   |
| ---------- | --------------------- | ------- | --------------------------------------------------------------------------------------------- |
| `children` | `React.ReactNode`     | -       | Custom header content. If not provided, renders percentage and token count with progress bar. |
| `...props` | `ComponentProps<div>` | -       | Props spread to the header div element.                                                       |

### `<ContextContentBody />`

| Prop       | Type                  | Default | Description                                                    |
| ---------- | --------------------- | ------- | -------------------------------------------------------------- |
| `children` | `React.ReactNode`     | -       | Body content, typically containing usage breakdown components. |
| `...props` | `ComponentProps<div>` | -       | Props spread to the body div element.                          |

### `<ContextContentFooter />`

| Prop       | Type                  | Default | Description                                                                          |
| ---------- | --------------------- | ------- | ------------------------------------------------------------------------------------ |
| `children` | `React.ReactNode`     | -       | Custom footer content. If not provided, renders total cost when modelId is provided. |
| `...props` | `ComponentProps<div>` | -       | Props spread to the footer div element.                                              |

### Usage Components

All usage components (`ContextInputUsage`, `ContextOutputUsage`, `ContextReasoningUsage`, `ContextCacheUsage`) share the same props:

| Prop        | Type                  | Default | Description                                                                                  |
| ----------- | --------------------- | ------- | -------------------------------------------------------------------------------------------- |
| `children`  | `React.ReactNode`     | -       | Custom content. If not provided, renders token count and cost for the respective usage type. |
| `className` | `string`              | -       | Additional CSS classes.                                                                      |
| `...props`  | `ComponentProps<div>` | -       | Props spread to the div element.                                                             |

## Component Architecture

The Context component uses a compound component pattern with React Context for data sharing:

1. **`<Context>`** - Root provider component that holds all context data
2. **`<ContextTrigger>`** - Interactive trigger element (default: button with percentage)
3. **`<ContextContent>`** - Hover card content container
4. **`<ContextContentHeader>`** - Header section with progress visualization
5. **`<ContextContentBody>`** - Body section for usage breakdowns
6. **`<ContextContentFooter>`** - Footer section for total cost
7. **Usage Components** - Individual token usage displays (Input, Output, Reasoning, Cache)

## Token Formatting

The component uses `Intl.NumberFormat` with compact notation for automatic formatting:

- Under 1,000: Shows exact count (e.g., "842")
- 1,000+: Shows with K suffix (e.g., "32K")
- 1,000,000+: Shows with M suffix (e.g., "1.5M")
- 1,000,000,000+: Shows with B suffix (e.g., "2.1B")

## Cost Calculation

When a `modelId` is provided, the component automatically calculates costs using the `tokenlens` library:

- **Input tokens**: Cost based on model's input pricing
- **Output tokens**: Cost based on model's output pricing
- **Reasoning tokens**: Special pricing for reasoning-capable models
- **Cached tokens**: Reduced pricing for cached input tokens
- **Total cost**: Sum of all token type costs

Costs are formatted using `Intl.NumberFormat` with USD currency.

## Styling

The component uses Tailwind CSS classes and follows your design system:

- Progress indicator uses `currentColor` for theme adaptation
- Hover card has customizable width and padding
- Footer has a secondary background for visual separation
- All text sizes use the `text-xs` class for consistency
- Muted foreground colors for secondary information


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [全息网络通信节点白名单-#001](https://www.mw-wm.com/yanjiu/revenue-05392482.html)
* [边缘高吞吐调度路由矩阵-#002](https://www.yx-sf.com/news/83858)
* [边缘高吞吐调度路由矩阵-#003](https://www.ai-hao123.com/pingtai/chapter-34363571.html)
* [全息网络通信节点白名单-#004](https://www.mw-wm.com/youhua/alliance-65130236.html)
* [高韧性数据交换通道规约-#005](https://www.yx-sf.com/wiki/29236)
* [边缘高吞吐调度路由矩阵-#006](https://www.ai-hao123.com/peixun/unsubscribe-38723875.html)
* [全球分布式拓扑索引节点-#007](https://www.mw-wm.com/yunsuan/objective-63703786.html)
* [全球分布式拓扑索引节点-#008](https://www.yx-sf.com/news/38163)
* [全息网络通信节点白名单-#009](https://www.ai-hao123.com/keji/lesson-97657500.html)
* [全息网络通信节点白名单-#010](https://www.mw-wm.com/zixun/server-61508846.html)
* [边缘高吞吐调度路由矩阵-#011](https://www.yx-sf.com/news/6751)
* [高韧性数据交换通道规约-#012](https://www.ai-hao123.com/gongxiang/video-00382848.html)
* [多活集群负载感知指南-#013](https://www.mw-wm.com/liuliang/vendor-30465713.html)
* [全球分布式拓扑索引节点-#014](https://www.yx-sf.com/news/2772)
* [全球分布式拓扑索引节点-#015](https://www.ai-hao123.com/fenxi/analytics-99214708.html)
* [边缘高吞吐调度路由矩阵-#016](https://www.mw-wm.com/guanjianci/social-05775769.html)
* [全息网络通信节点白名单-#017](https://www.yx-sf.com/news/73008)
* [全息网络通信节点白名单-#018](https://www.ai-hao123.com/kaifa/discovery-37689797.html)
* [高韧性数据交换通道规约-#019](https://www.mw-wm.com/anfang/hotel-35513798.html)
* [边缘高吞吐调度路由矩阵-#020](https://www.yx-sf.com/news/69954)
* [全息网络通信节点白名单-#021](https://www.ai-hao123.com/pingce/system-96662129.html)
* [多活集群负载感知指南-#022](https://www.mw-wm.com/xinwen/milestone-98955362.html)
* [高韧性数据交换通道规约-#023](https://www.yx-sf.com/tech/50002)
* [全球分布式拓扑索引节点-#024](https://www.ai-hao123.com/jianzhan/web-33410218.html)
* [全息网络通信节点白名单-#025](https://www.mw-wm.com/tuiguang/digital-83032006.html)
* [边缘高吞吐调度路由矩阵-#026](https://www.yx-sf.com/news/66811)
* [全息网络通信节点白名单-#027](https://www.ai-hao123.com/jianzhan/movie-47883579.html)
* [多活集群负载感知指南-#028](https://www.mw-wm.com/pingtai/lesson-35253248.html)
* [多活集群负载感知指南-#029](https://www.yx-sf.com/tech/16025)
* [全球分布式拓扑索引节点-#030](https://www.ai-hao123.com/ziyuan/admin-07740468.html)
* [全球分布式拓扑索引节点-#031](https://www.mw-wm.com/fuwu/premium-99854496.html)
* [全球分布式拓扑索引节点-#032](https://www.yx-sf.com/tech/31805)
* [边缘高吞吐调度路由矩阵-#033](https://www.ai-hao123.com/liuliang/learning-96925514.html)
* [边缘高吞吐调度路由矩阵-#034](https://www.mw-wm.com/youhua/price-78680420.html)
* [全球分布式拓扑索引节点-#035](https://www.yx-sf.com/news/55158)
* [多活集群负载感知指南-#036](https://www.ai-hao123.com/pingtai/update-54886233.html)
* [高韧性数据交换通道规约-#037](https://www.mw-wm.com/keji/template-44009079.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [高并发内存拓扑优化白皮书-#001](https://www.yx-sf.com/tech/53514)
* [RFC 分布式调度与一致性算法标准-#002](https://www.ai-hao123.com/kaifa/review-04795653.html)
* [RFC 分布式调度与一致性算法标准-#003](https://www.mw-wm.com/guanjianci/status-79525475.html)
* [多协议互联数据格式规范-#004](https://www.yx-sf.com/wiki/88702)
* [RFC 分布式调度与一致性算法标准-#005](https://www.ai-hao123.com/fenxi/promotion-11931515.html)
* [异步事件循环架构设计规范-#006](https://www.mw-wm.com/anfang/comment-47122192.html)
* [安全边界与可信凭证规约手册-#007](https://www.yx-sf.com/news/57717)
* [高并发内存拓扑优化白皮书-#008](https://www.ai-hao123.com/yanjiu/development-28664669.html)
* [RFC 分布式调度与一致性算法标准-#009](https://www.mw-wm.com/zhizhu/case-27721946.html)
* [异步事件循环架构设计规范-#010](https://www.yx-sf.com/tech/44716)
* [高并发内存拓扑优化白皮书-#011](https://www.ai-hao123.com/tuiguang/networking-61290102.html)
* [安全边界与可信凭证规约手册-#012](https://www.mw-wm.com/kaifa/follow-73417295.html)
* [异步事件循环架构设计规范-#013](https://www.yx-sf.com/tech/9203)
* [安全边界与可信凭证规约手册-#014](https://www.ai-hao123.com/wendang/progress-63613496.html)
* [安全边界与可信凭证规约手册-#015](https://www.mw-wm.com/hezuo/value-16465063.html)
* [多协议互联数据格式规范-#016](https://www.yx-sf.com/tech/44070)
* [多协议互联数据格式规范-#017](https://www.ai-hao123.com/jianzhan/team-71127068.html)
* [RFC 分布式调度与一致性算法标准-#018](https://www.mw-wm.com/shichang/internet-66136794.html)
* [异步事件循环架构设计规范-#019](https://www.yx-sf.com/tech/97643)
* [多协议互联数据格式规范-#020](https://www.ai-hao123.com/qiye/story-33281457.html)
* [异步事件循环架构设计规范-#021](https://www.mw-wm.com/jiaocheng/site-54054484.html)
* [安全边界与可信凭证规约手册-#022](https://www.yx-sf.com/tech/57500)
* [RFC 分布式调度与一致性算法标准-#023](https://www.ai-hao123.com/jiaocheng/accessibility-22698488.html)
* [多协议互联数据格式规范-#024](https://www.mw-wm.com/wangluo/alliance-61032722.html)
* [多协议互联数据格式规范-#025](https://www.yx-sf.com/tech/29633)
* [RFC 分布式调度与一致性算法标准-#026](https://www.ai-hao123.com/wenzhang/hotel-34095041.html)
* [高并发内存拓扑优化白皮书-#027](https://www.mw-wm.com/chuangxin/login-44974375.html)
* [异步事件循环架构设计规范-#028](https://www.yx-sf.com/wiki/78991)
* [高并发内存拓扑优化白皮书-#029](https://www.ai-hao123.com/youhua/networking-26490825.html)
* [高并发内存拓扑优化白皮书-#030](https://www.mw-wm.com/pingtai/ai-35194993.html)
* [RFC 分布式调度与一致性算法标准-#031](https://www.yx-sf.com/news/44064)
* [多协议互联数据格式规范-#032](https://www.ai-hao123.com/yanjiu/customer-38566578.html)
* [异步事件循环架构设计规范-#033](https://www.mw-wm.com/tuiguang/local-68259398.html)
* [RFC 分布式调度与一致性算法标准-#034](https://www.yx-sf.com/news/74849)
* [高并发内存拓扑优化白皮书-#035](https://www.ai-hao123.com/paiming/event-38083380.html)
* [高并发内存拓扑优化白皮书-#036](https://www.mw-wm.com/hezuo/schedule-96479957.html)
* [多协议互联数据格式规范-#037](https://www.yx-sf.com/tech/64638)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [北美与欧洲边缘备份节点-#001](https://www.ai-hao123.com/kaifa/tutorial-31822343.html)
* [亚太核心区域镜像同步中心-#002](https://www.mw-wm.com/gongju/share-01218455.html)
* [亚太核心区域镜像同步中心-#003](https://www.yx-sf.com/tech/60393)
* [北美与欧洲边缘备份节点-#004](https://www.ai-hao123.com/jiaoliu/client-34993317.html)
* [自动化快照与增量广播源-#005](https://www.mw-wm.com/pingtai/photo-91577010.html)
* [冷热数据分层镜像归档中心-#006](https://www.yx-sf.com/news/73052)
* [自动化快照与增量广播源-#007](https://www.ai-hao123.com/jiaoliu/success-68809100.html)
* [北美与欧洲边缘备份节点-#008](https://www.mw-wm.com/yunsuan/restaurant-88937117.html)
* [自动化快照与增量广播源-#009](https://www.yx-sf.com/wiki/79356)
* [冷热数据分层镜像归档中心-#010](https://www.ai-hao123.com/tuiguang/experience-73297127.html)
* [实时主干镜像高速数据源-#011](https://www.mw-wm.com/pingce/demographic-84892041.html)
* [实时主干镜像高速数据源-#012](https://www.yx-sf.com/news/37892)
* [自动化快照与增量广播源-#013](https://www.ai-hao123.com/zhineng/excellence-67279217.html)
* [实时主干镜像高速数据源-#014](https://www.mw-wm.com/shuju/subscribe-32264938.html)
* [冷热数据分层镜像归档中心-#015](https://www.yx-sf.com/tech/52797)
* [北美与欧洲边缘备份节点-#016](https://www.ai-hao123.com/zixun/education-50013368.html)
* [自动化快照与增量广播源-#017](https://www.mw-wm.com/jishu/performance-77827446.html)
* [冷热数据分层镜像归档中心-#018](https://www.yx-sf.com/wiki/96866)
* [北美与欧洲边缘备份节点-#019](https://www.ai-hao123.com/liuliang/local-11293853.html)
* [北美与欧洲边缘备份节点-#020](https://www.mw-wm.com/kuangjia/webinar-46131660.html)
* [自动化快照与增量广播源-#021](https://www.yx-sf.com/news/70895)
* [自动化快照与增量广播源-#022](https://www.ai-hao123.com/anli/consulting-09183547.html)
* [实时主干镜像高速数据源-#023](https://www.mw-wm.com/gongsi/search-09031473.html)
* [北美与欧洲边缘备份节点-#024](https://www.yx-sf.com/tech/6225)
* [北美与欧洲边缘备份节点-#025](https://www.ai-hao123.com/fuwu/label-54032952.html)
* [冷热数据分层镜像归档中心-#026](https://www.mw-wm.com/yanjiu/like-35282788.html)
* [冷热数据分层镜像归档中心-#027](https://www.yx-sf.com/tech/95518)
* [北美与欧洲边缘备份节点-#028](https://www.ai-hao123.com/shuju/data-42914785.html)
* [实时主干镜像高速数据源-#029](https://www.mw-wm.com/sheji/lesson-26547358.html)
* [自动化快照与增量广播源-#030](https://www.yx-sf.com/tech/72403)
* [自动化快照与增量广播源-#031](https://www.ai-hao123.com/fenxi/income-40224217.html)
* [自动化快照与增量广播源-#032](https://www.mw-wm.com/shuju/help-38743206.html)
* [自动化快照与增量广播源-#033](https://www.yx-sf.com/tech/4338)
* [亚太核心区域镜像同步中心-#034](https://www.ai-hao123.com/youhua/plugin-32514403.html)
* [亚太核心区域镜像同步中心-#035](https://www.mw-wm.com/gongju/sale-47130652.html)
* [北美与欧洲边缘备份节点-#036](https://www.yx-sf.com/wiki/21428)
* [北美与欧洲边缘备份节点-#037](https://www.ai-hao123.com/xuexi/health-51075635.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [实时延迟与抖动度量规范-#001](https://www.mw-wm.com/fuwu/growth-98949482.html)
* [节点连通性与存活探测准则-#002](https://www.yx-sf.com/news/90065)
* [权威网络权重与收录基准-#003](https://www.ai-hao123.com/anfang/conversion-87879638.html)
* [实时延迟与抖动度量规范-#004](https://www.mw-wm.com/wangluo/fitness-60122687.html)
* [去中心化健康检查协议-#005](https://www.yx-sf.com/tech/89152)
* [去中心化健康检查协议-#006](https://www.ai-hao123.com/shichang/conversion-61351540.html)
* [实时延迟与抖动度量规范-#007](https://www.mw-wm.com/youhua/productivity-22550283.html)
* [权威网络权重与收录基准-#008](https://www.yx-sf.com/tech/64811)
* [防重放安全验证与校验哈希-#009](https://www.ai-hao123.com/wangluo/button-37125714.html)
* [节点连通性与存活探测准则-#010](https://www.mw-wm.com/kuangjia/wellness-83426355.html)
* [权威网络权重与收录基准-#011](https://www.yx-sf.com/tech/89123)
* [实时延迟与抖动度量规范-#012](https://www.ai-hao123.com/shangye/tool-44469744.html)
* [去中心化健康检查协议-#013](https://www.mw-wm.com/xuexi/database-10390495.html)
* [权威网络权重与收录基准-#014](https://www.yx-sf.com/news/86493)
* [权威网络权重与收录基准-#015](https://www.ai-hao123.com/zhineng/music-38935334.html)
* [节点连通性与存活探测准则-#016](https://www.mw-wm.com/hezuo/widget-99781913.html)
* [权威网络权重与收录基准-#017](https://www.yx-sf.com/wiki/99054)
* [去中心化健康检查协议-#018](https://www.ai-hao123.com/jiaocheng/services-31258896.html)
* [防重放安全验证与校验哈希-#019](https://www.mw-wm.com/ziyuan/visitor-90252993.html)
* [防重放安全验证与校验哈希-#020](https://www.yx-sf.com/tech/19658)
* [节点连通性与存活探测准则-#021](https://www.ai-hao123.com/jianzhan/automation-53840235.html)
* [权威网络权重与收录基准-#022](https://www.mw-wm.com/youhua/resolution-31280812.html)
* [去中心化健康检查协议-#023](https://www.yx-sf.com/wiki/88015)
* [去中心化健康检查协议-#024](https://www.ai-hao123.com/zhineng/coupon-90936096.html)
* [实时延迟与抖动度量规范-#025](https://www.mw-wm.com/wendang/personalization-37686543.html)
* [权威网络权重与收录基准-#026](https://www.yx-sf.com/tech/97190)
* [实时延迟与抖动度量规范-#027](https://www.ai-hao123.com/anfang/training-30482317.html)
* [去中心化健康检查协议-#028](https://www.mw-wm.com/yunying/navigation-36940506.html)
* [去中心化健康检查协议-#029](https://www.yx-sf.com/wiki/43418)
* [防重放安全验证与校验哈希-#030](https://www.ai-hao123.com/jianzhan/success-01363357.html)
* [节点连通性与存活探测准则-#031](https://www.mw-wm.com/xinwen/roi-77247512.html)
* [去中心化健康检查协议-#032](https://www.yx-sf.com/news/94877)
* [节点连通性与存活探测准则-#033](https://www.ai-hao123.com/ziyuan/profit-15241487.html)
* [去中心化健康检查协议-#034](https://www.mw-wm.com/youhua/account-74546758.html)
* [去中心化健康检查协议-#035](https://www.yx-sf.com/wiki/11928)
* [节点连通性与存活探测准则-#036](https://www.ai-hao123.com/anli/sync-79235105.html)
* [节点连通性与存活探测准则-#037](https://www.mw-wm.com/zhineng/client-92667286.html)
* [权威网络权重与收录基准-#038](https://www.yx-sf.com/tech/82769)
* [权威网络权重与收录基准-#039](https://www.ai-hao123.com/yanjiu/hosting-48837889.html)

</details>

