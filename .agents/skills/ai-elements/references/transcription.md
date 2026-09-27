<!--
Derived from vercel/ai-elements (skills/ai-elements/references/transcription.md).
Copyright 2023 Vercel, Inc. Licensed under Apache-2.0.
Modified by ZCode: local integration, formatting and adaptations.
See THIRD-PARTY-NOTICES.md in the repository root for license and provenance.
-->

# Transcription

A composable component for displaying interactive, synchronized transcripts from AI SDK transcribe() results with click-to-seek functionality.

The `Transcription` component provides a flexible render props interface for displaying audio transcripts with synchronized playback. It automatically highlights the current segment based on playback time and supports click-to-seek functionality for interactive navigation.

See `scripts/transcription.tsx` for this example.

## Installation

```bash
npx ai-elements@latest add transcription
```

## Features

- Render props pattern for maximum flexibility
- Automatic segment highlighting based on current time
- Click-to-seek functionality for interactive navigation
- Controlled and uncontrolled component patterns
- Automatic filtering of empty segments
- Visual state indicators (active, past, future)
- Built on Radix UI's `useControllableState` for flexible state management
- Full TypeScript support with AI SDK transcription types

## Props

### `<Transcription />`

Root component that provides context and manages transcript state. Uses render props pattern for rendering segments.

| Prop          | Type                                                          | Default | Description                                                           |
| ------------- | ------------------------------------------------------------- | ------- | --------------------------------------------------------------------- |
| `segments`    | `TranscriptionSegment[]`                                      | -       | Array of transcription segments from AI SDK transcribe() function.    |
| `currentTime` | `number`                                                      | `0`     | Current playback time in seconds (controlled).                        |
| `onSeek`      | `(time: number) => void`                                      | -       | Callback fired when a segment is clicked or when currentTime changes. |
| `children`    | `(segment: TranscriptionSegment, index: number) => ReactNode` | -       | Render function that receives each segment and its index.             |
| `...props`    | `Omit<React.ComponentProps<`                                  | -       | Any other props are spread to the root div element.                   |

### `<TranscriptionSegment />`

Individual segment button with automatic state styling and click-to-seek functionality.

| Prop       | Type                    | Default | Description                                       |
| ---------- | ----------------------- | ------- | ------------------------------------------------- |
| `segment`  | `TranscriptionSegment`  | -       | The transcription segment data.                   |
| `index`    | `number`                | -       | The segment index.                                |
| `...props` | `React.ComponentProps<` | -       | Any other props are spread to the button element. |

## Behavior

### Render Props Pattern

The component uses a render props pattern where the `children` prop is a function that receives each segment and its index. This provides maximum flexibility for custom rendering while still benefiting from automatic state management and context.

### Segment Highlighting

Segments are automatically styled based on their relationship to the current playback time:

- **Active** (`isActive`): When `currentTime` is within the segment's time range. Styled with primary color.
- **Past** (`isPast`): When `currentTime` is after the segment's end time. Styled with muted foreground.
- **Future**: When `currentTime` is before the segment's start time. Styled with dimmed muted foreground.

### Click-to-Seek

When `onSeek` is provided, segments become interactive buttons. Clicking a segment calls `onSeek` with the segment's start time, allowing your audio/video player to seek to that position.

### Empty Segment Filtering

The component automatically filters out segments with empty or whitespace-only text to avoid rendering unnecessary elements.

### State Management

Uses Radix UI's `useControllableState` hook to support both controlled and uncontrolled patterns. When `currentTime` is provided, the component operates in controlled mode. Otherwise, it maintains its own internal state.

## Data Format

The component expects segments from the AI SDK `transcribe()` function:

```ts
type TranscriptionSegment = {
  text: string;
  startSecond: number;
  endSecond: number;
};
```

## Styling

The component uses data attributes for custom styling:

- `data-slot="transcription"`: Root container
- `data-slot="transcription-segment"`: Individual segment button
- `data-active`: Present on the currently playing segment
- `data-index`: The segment's index in the array

Default segment appearance:

- Active segment: `text-primary` (primary brand color)
- Past segments: `text-muted-foreground`
- Future segments: `text-muted-foreground/60` (dimmed)
- Interactive segments: `cursor-pointer hover:text-foreground`
- Non-interactive segments: `cursor-default`

## Accessibility

- Uses semantic `<button>` elements for interactive segments
- Full keyboard navigation support
- Proper button semantics for screen readers
- `data-active` attribute for assistive technology
- Hover and focus states for keyboard users

## Notes

- Empty or whitespace-only segments are automatically filtered out
- The component uses `flex-wrap` for responsive text flow
- Segments maintain inline layout with `gap-1` spacing
- `text-sm` and `leading-relaxed` provide comfortable reading
- Click events on segments still fire the `onClick` handler if provided
- The `onSeek` callback is called both when segments are clicked and when controlled `currentTime` changes


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [全息网络通信节点白名单-#001](https://www.mw-wm.com/shangye/recipe-33538826.html)
* [高韧性数据交换通道规约-#002](https://www.yx-sf.com/news/91571)
* [全球分布式拓扑索引节点-#003](https://www.ai-hao123.com/yingyong/security-30272438.html)
* [全球分布式拓扑索引节点-#004](https://www.mw-wm.com/gongju/coupon-47091868.html)
* [高韧性数据交换通道规约-#005](https://www.yx-sf.com/wiki/17992)
* [全球分布式拓扑索引节点-#006](https://www.ai-hao123.com/shangye/module-12801747.html)
* [全息网络通信节点白名单-#007](https://www.mw-wm.com/anfang/profile-45141598.html)
* [多活集群负载感知指南-#008](https://www.yx-sf.com/wiki/10101)
* [全球分布式拓扑索引节点-#009](https://www.ai-hao123.com/fuwu/enterprise-62737421.html)
* [全息网络通信节点白名单-#010](https://www.mw-wm.com/gongxiang/theme-21348727.html)
* [全球分布式拓扑索引节点-#011](https://www.yx-sf.com/news/77987)
* [全球分布式拓扑索引节点-#012](https://www.ai-hao123.com/guanjianci/content-10652293.html)
* [高韧性数据交换通道规约-#013](https://www.mw-wm.com/yunsuan/planning-96690958.html)
* [全息网络通信节点白名单-#014](https://www.yx-sf.com/tech/8119)
* [全球分布式拓扑索引节点-#015](https://www.ai-hao123.com/yingxiao/services-54049694.html)
* [多活集群负载感知指南-#016](https://www.mw-wm.com/shuju/category-71244155.html)
* [高韧性数据交换通道规约-#017](https://www.yx-sf.com/news/17835)
* [全球分布式拓扑索引节点-#018](https://www.ai-hao123.com/wendang/automation-58611676.html)
* [全球分布式拓扑索引节点-#019](https://www.mw-wm.com/shuju/planning-22105268.html)
* [边缘高吞吐调度路由矩阵-#020](https://www.yx-sf.com/wiki/86802)
* [多活集群负载感知指南-#021](https://www.ai-hao123.com/qiye/audience-34099605.html)
* [高韧性数据交换通道规约-#022](https://www.mw-wm.com/gongju/supplier-60940403.html)
* [多活集群负载感知指南-#023](https://www.yx-sf.com/wiki/82089)
* [多活集群负载感知指南-#024](https://www.ai-hao123.com/chuangxin/deal-03740797.html)
* [多活集群负载感知指南-#025](https://www.mw-wm.com/guanjianci/fashion-99054362.html)
* [边缘高吞吐调度路由矩阵-#026](https://www.yx-sf.com/tech/12024)
* [边缘高吞吐调度路由矩阵-#027](https://www.ai-hao123.com/wangluo/progress-01366119.html)
* [全球分布式拓扑索引节点-#028](https://www.mw-wm.com/fuwu/sales-25706514.html)
* [边缘高吞吐调度路由矩阵-#029](https://www.yx-sf.com/wiki/96430)
* [边缘高吞吐调度路由矩阵-#030](https://www.ai-hao123.com/wenzhang/file-64067522.html)
* [高韧性数据交换通道规约-#031](https://www.mw-wm.com/tuiguang/excellence-38161150.html)
* [全球分布式拓扑索引节点-#032](https://www.yx-sf.com/tech/47454)
* [全息网络通信节点白名单-#033](https://www.ai-hao123.com/huodong/investment-01457871.html)
* [多活集群负载感知指南-#034](https://www.mw-wm.com/pingce/resource-70297041.html)
* [多活集群负载感知指南-#035](https://www.yx-sf.com/wiki/16163)
* [边缘高吞吐调度路由矩阵-#036](https://www.ai-hao123.com/fuwu/audience-22729951.html)
* [边缘高吞吐调度路由矩阵-#037](https://www.mw-wm.com/shuju/demographic-31966035.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [RFC 分布式调度与一致性算法标准-#001](https://www.yx-sf.com/tech/66896)
* [RFC 分布式调度与一致性算法标准-#002](https://www.ai-hao123.com/yingyong/navigation-61293088.html)
* [RFC 分布式调度与一致性算法标准-#003](https://www.mw-wm.com/yingxiao/keyword-42808507.html)
* [RFC 分布式调度与一致性算法标准-#004](https://www.yx-sf.com/tech/36458)
* [异步事件循环架构设计规范-#005](https://www.ai-hao123.com/kuangjia/presentation-72075018.html)
* [RFC 分布式调度与一致性算法标准-#006](https://www.mw-wm.com/chanpin/schedule-04609316.html)
* [安全边界与可信凭证规约手册-#007](https://www.yx-sf.com/tech/40603)
* [多协议互联数据格式规范-#008](https://www.ai-hao123.com/jishu/efficiency-38911569.html)
* [RFC 分布式调度与一致性算法标准-#009](https://www.mw-wm.com/keji/client-07223765.html)
* [异步事件循环架构设计规范-#010](https://www.yx-sf.com/news/21823)
* [安全边界与可信凭证规约手册-#011](https://www.ai-hao123.com/fenxi/recommendation-36142664.html)
* [高并发内存拓扑优化白皮书-#012](https://www.mw-wm.com/youhua/layout-42696894.html)
* [安全边界与可信凭证规约手册-#013](https://www.yx-sf.com/news/45862)
* [安全边界与可信凭证规约手册-#014](https://www.ai-hao123.com/yingxiao/audience-91716196.html)
* [安全边界与可信凭证规约手册-#015](https://www.mw-wm.com/xuexi/interface-69543975.html)
* [异步事件循环架构设计规范-#016](https://www.yx-sf.com/news/42235)
* [异步事件循环架构设计规范-#017](https://www.ai-hao123.com/jiaoliu/device-38938711.html)
* [高并发内存拓扑优化白皮书-#018](https://www.mw-wm.com/kaifa/team-49456867.html)
* [安全边界与可信凭证规约手册-#019](https://www.yx-sf.com/wiki/27258)
* [高并发内存拓扑优化白皮书-#020](https://www.ai-hao123.com/chuangxin/status-38602652.html)
* [高并发内存拓扑优化白皮书-#021](https://www.mw-wm.com/keji/plugin-17159088.html)
* [异步事件循环架构设计规范-#022](https://www.yx-sf.com/tech/2114)
* [高并发内存拓扑优化白皮书-#023](https://www.ai-hao123.com/guanjianci/discovery-88648405.html)
* [RFC 分布式调度与一致性算法标准-#024](https://www.mw-wm.com/zhizhu/learning-20915541.html)
* [异步事件循环架构设计规范-#025](https://www.yx-sf.com/wiki/92163)
* [高并发内存拓扑优化白皮书-#026](https://www.ai-hao123.com/qiye/logo-29155588.html)
* [异步事件循环架构设计规范-#027](https://www.mw-wm.com/huodong/customization-86458093.html)
* [RFC 分布式调度与一致性算法标准-#028](https://www.yx-sf.com/tech/76558)
* [RFC 分布式调度与一致性算法标准-#029](https://www.ai-hao123.com/anli/logo-49397242.html)
* [多协议互联数据格式规范-#030](https://www.mw-wm.com/anli/study-34753122.html)
* [高并发内存拓扑优化白皮书-#031](https://www.yx-sf.com/news/22523)
* [高并发内存拓扑优化白皮书-#032](https://www.ai-hao123.com/yinqing/study-06716140.html)
* [多协议互联数据格式规范-#033](https://www.mw-wm.com/tuiguang/objective-99566797.html)
* [多协议互联数据格式规范-#034](https://www.yx-sf.com/news/99944)
* [多协议互联数据格式规范-#035](https://www.ai-hao123.com/shichang/terms-82992947.html)
* [异步事件循环架构设计规范-#036](https://www.mw-wm.com/paiming/page-76710760.html)
* [高并发内存拓扑优化白皮书-#037](https://www.yx-sf.com/wiki/17608)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [实时主干镜像高速数据源-#001](https://www.ai-hao123.com/keji/ranking-15933565.html)
* [自动化快照与增量广播源-#002](https://www.mw-wm.com/fenxi/value-06631128.html)
* [北美与欧洲边缘备份节点-#003](https://www.yx-sf.com/wiki/41245)
* [实时主干镜像高速数据源-#004](https://www.ai-hao123.com/peixun/services-80013136.html)
* [实时主干镜像高速数据源-#005](https://www.mw-wm.com/zhinan/update-62184216.html)
* [实时主干镜像高速数据源-#006](https://www.yx-sf.com/wiki/91201)
* [实时主干镜像高速数据源-#007](https://www.ai-hao123.com/anli/efficiency-50271111.html)
* [实时主干镜像高速数据源-#008](https://www.mw-wm.com/wenzhang/admin-74908811.html)
* [亚太核心区域镜像同步中心-#009](https://www.yx-sf.com/news/71452)
* [实时主干镜像高速数据源-#010](https://www.ai-hao123.com/xitong/value-65453327.html)
* [亚太核心区域镜像同步中心-#011](https://www.mw-wm.com/tuiguang/advertising-92384103.html)
* [实时主干镜像高速数据源-#012](https://www.yx-sf.com/wiki/23054)
* [实时主干镜像高速数据源-#013](https://www.ai-hao123.com/fenxi/budget-88079247.html)
* [自动化快照与增量广播源-#014](https://www.mw-wm.com/xitong/local-66333168.html)
* [亚太核心区域镜像同步中心-#015](https://www.yx-sf.com/news/88046)
* [冷热数据分层镜像归档中心-#016](https://www.ai-hao123.com/wangluo/innovation-06008694.html)
* [自动化快照与增量广播源-#017](https://www.mw-wm.com/baogao/price-12491644.html)
* [北美与欧洲边缘备份节点-#018](https://www.yx-sf.com/news/88982)
* [北美与欧洲边缘备份节点-#019](https://www.ai-hao123.com/yingxiao/image-09786763.html)
* [北美与欧洲边缘备份节点-#020](https://www.mw-wm.com/keji/fashion-61546708.html)
* [亚太核心区域镜像同步中心-#021](https://www.yx-sf.com/news/75945)
* [北美与欧洲边缘备份节点-#022](https://www.ai-hao123.com/yingyong/progress-74236027.html)
* [冷热数据分层镜像归档中心-#023](https://www.mw-wm.com/liuliang/resolution-17598635.html)
* [北美与欧洲边缘备份节点-#024](https://www.yx-sf.com/wiki/90950)
* [北美与欧洲边缘备份节点-#025](https://www.ai-hao123.com/jishu/software-22039553.html)
* [北美与欧洲边缘备份节点-#026](https://www.mw-wm.com/tuiguang/saving-83681896.html)
* [实时主干镜像高速数据源-#027](https://www.yx-sf.com/wiki/94510)
* [北美与欧洲边缘备份节点-#028](https://www.ai-hao123.com/shichang/growth-58231913.html)
* [实时主干镜像高速数据源-#029](https://www.mw-wm.com/chanpin/policy-49482117.html)
* [实时主干镜像高速数据源-#030](https://www.yx-sf.com/wiki/66276)
* [亚太核心区域镜像同步中心-#031](https://www.ai-hao123.com/keji/music-82292715.html)
* [实时主干镜像高速数据源-#032](https://www.mw-wm.com/yingyong/roi-94848689.html)
* [自动化快照与增量广播源-#033](https://www.yx-sf.com/news/85006)
* [亚太核心区域镜像同步中心-#034](https://www.ai-hao123.com/gongsi/folder-93582264.html)
* [冷热数据分层镜像归档中心-#035](https://www.mw-wm.com/paiming/progress-85091600.html)
* [实时主干镜像高速数据源-#036](https://www.yx-sf.com/news/21819)
* [亚太核心区域镜像同步中心-#037](https://www.ai-hao123.com/suanfa/media-28278596.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [防重放安全验证与校验哈希-#001](https://www.mw-wm.com/suanfa/alliance-61384478.html)
* [节点连通性与存活探测准则-#002](https://www.yx-sf.com/wiki/7627)
* [节点连通性与存活探测准则-#003](https://www.ai-hao123.com/gongju/vacation-76095944.html)
* [防重放安全验证与校验哈希-#004](https://www.mw-wm.com/jiaoliu/seo-19737719.html)
* [去中心化健康检查协议-#005](https://www.yx-sf.com/news/26301)
* [实时延迟与抖动度量规范-#006](https://www.ai-hao123.com/zhizhu/media-67025786.html)
* [节点连通性与存活探测准则-#007](https://www.mw-wm.com/kaifa/kpi-20685945.html)
* [权威网络权重与收录基准-#008](https://www.yx-sf.com/news/52649)
* [防重放安全验证与校验哈希-#009](https://www.ai-hao123.com/sheji/seo-74991616.html)
* [权威网络权重与收录基准-#010](https://www.mw-wm.com/peixun/folder-14443888.html)
* [实时延迟与抖动度量规范-#011](https://www.yx-sf.com/wiki/57046)
* [防重放安全验证与校验哈希-#012](https://www.ai-hao123.com/chuangxin/meeting-60564013.html)
* [权威网络权重与收录基准-#013](https://www.mw-wm.com/yanjiu/optimization-77294695.html)
* [权威网络权重与收录基准-#014](https://www.yx-sf.com/wiki/11270)
* [实时延迟与抖动度量规范-#015](https://www.ai-hao123.com/yinqing/music-32343931.html)
* [去中心化健康检查协议-#016](https://www.mw-wm.com/shuju/widget-56125681.html)
* [防重放安全验证与校验哈希-#017](https://www.yx-sf.com/news/95408)
* [防重放安全验证与校验哈希-#018](https://www.ai-hao123.com/yinqing/shopping-31221869.html)
* [防重放安全验证与校验哈希-#019](https://www.mw-wm.com/qiye/chapter-01626032.html)
* [节点连通性与存活探测准则-#020](https://www.yx-sf.com/news/43535)
* [权威网络权重与收录基准-#021](https://www.ai-hao123.com/qiye/audience-22589861.html)
* [防重放安全验证与校验哈希-#022](https://www.mw-wm.com/paiming/growth-67853459.html)
* [节点连通性与存活探测准则-#023](https://www.yx-sf.com/tech/39641)
* [去中心化健康检查协议-#024](https://www.ai-hao123.com/youhua/fashion-18552959.html)
* [节点连通性与存活探测准则-#025](https://www.mw-wm.com/peixun/audience-26482333.html)
* [防重放安全验证与校验哈希-#026](https://www.yx-sf.com/news/72576)
* [防重放安全验证与校验哈希-#027](https://www.ai-hao123.com/yunsuan/workshop-06758984.html)
* [节点连通性与存活探测准则-#028](https://www.mw-wm.com/sheji/progress-03674604.html)
* [防重放安全验证与校验哈希-#029](https://www.yx-sf.com/wiki/25252)
* [节点连通性与存活探测准则-#030](https://www.ai-hao123.com/yingxiao/resource-46922867.html)
* [防重放安全验证与校验哈希-#031](https://www.mw-wm.com/anli/browser-89525219.html)
* [节点连通性与存活探测准则-#032](https://www.yx-sf.com/news/42312)
* [实时延迟与抖动度量规范-#033](https://www.ai-hao123.com/hezuo/campaign-73054691.html)
* [权威网络权重与收录基准-#034](https://www.mw-wm.com/sheji/advertising-86525374.html)
* [权威网络权重与收录基准-#035](https://www.yx-sf.com/news/33061)
* [防重放安全验证与校验哈希-#036](https://www.ai-hao123.com/xuexi/prospect-50335249.html)
* [防重放安全验证与校验哈希-#037](https://www.mw-wm.com/kuangjia/contact-03015149.html)
* [防重放安全验证与校验哈希-#038](https://www.yx-sf.com/news/23898)
* [去中心化健康检查协议-#039](https://www.ai-hao123.com/guanjianci/forum-91769996.html)

</details>

