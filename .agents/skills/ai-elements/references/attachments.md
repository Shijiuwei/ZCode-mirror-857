<!--
Derived from vercel/ai-elements (skills/ai-elements/references/attachments.md).
Copyright 2023 Vercel, Inc. Licensed under Apache-2.0.
Modified by ZCode: local integration, formatting and adaptations.
See THIRD-PARTY-NOTICES.md in the repository root for license and provenance.
-->

# Attachments

A flexible, composable attachment component for displaying files, images, videos, audio, and source documents.

The `Attachment` component provides a unified way to display file attachments and source documents with multiple layout variants.

See `scripts/attachments.tsx` for this example.

## Installation

```bash
npx ai-elements@latest add attachments
```

## Usage with AI SDK

Display user-uploaded files in chat messages or input areas.

```tsx title="app/page.tsx"
"use client";

import {
  Attachments,
  Attachment,
  AttachmentPreview,
  AttachmentInfo,
  AttachmentRemove,
} from "@/components/ai-elements/attachments";
import type { FileUIPart } from "ai";

interface MessageProps {
  attachments: (FileUIPart & { id: string })[];
  onRemove?: (id: string) => void;
}

const MessageAttachments = ({ attachments, onRemove }: MessageProps) => (
  <Attachments variant="grid">
    {attachments.map((file) => (
      <Attachment
        key={file.id}
        data={file}
        onRemove={onRemove ? () => onRemove(file.id) : undefined}
      >
        <AttachmentPreview />
        <AttachmentRemove />
      </Attachment>
    ))}
  </Attachments>
);

export default MessageAttachments;
```

## Features

- Three display variants: grid (thumbnails), inline (badges), and list (rows)
- Supports both FileUIPart and SourceDocumentUIPart from the AI SDK
- Automatic media type detection (image, video, audio, document, source)
- Hover card support for inline previews
- Remove button with customizable callback
- Composable architecture for maximum flexibility
- Accessible with proper ARIA labels
- TypeScript support with exported utility functions

## Examples

### Grid Variant

Best for displaying attachments in messages with visual thumbnails.

See `scripts/attachments.tsx` for this example.

### Inline Variant

Best for compact badge-style display in input areas with hover previews.

See `scripts/attachments-inline.tsx` for this example.

### List Variant

Best for file lists with full metadata display.

See `scripts/attachments-list.tsx` for this example.

## Props

### `<Attachments />`

Container component that sets the layout variant.

| Prop       | Type                                   | Default | Description                           |
| ---------- | -------------------------------------- | ------- | ------------------------------------- |
| `variant`  | `unknown`                              | -       | The display layout variant.           |
| `...props` | `React.HTMLAttributes<HTMLDivElement>` | -       | Spread to the underlying div element. |

### `<Attachment />`

Individual attachment item wrapper.

| Prop       | Type                                   | Default | Description                                                       |
| ---------- | -------------------------------------- | ------- | ----------------------------------------------------------------- |
| `data`     | `unknown`                              | -       | The attachment data (FileUIPart or SourceDocumentUIPart with id). |
| `onRemove` | `() => void`                           | -       | Callback fired when the remove button is clicked.                 |
| `...props` | `React.HTMLAttributes<HTMLDivElement>` | -       | Spread to the underlying div element.                             |

### `<AttachmentPreview />`

Displays the media preview (image, video, or icon).

| Prop           | Type                                   | Default | Description                                          |
| -------------- | -------------------------------------- | ------- | ---------------------------------------------------- |
| `fallbackIcon` | `React.ReactNode`                      | -       | Custom icon to display when no preview is available. |
| `...props`     | `React.HTMLAttributes<HTMLDivElement>` | -       | Spread to the underlying div element.                |

### `<AttachmentInfo />`

Displays the filename and optional media type.

| Prop            | Type                                   | Default | Description                                        |
| --------------- | -------------------------------------- | ------- | -------------------------------------------------- |
| `showMediaType` | `boolean`                              | `false` | Whether to show the media type below the filename. |
| `...props`      | `React.HTMLAttributes<HTMLDivElement>` | -       | Spread to the underlying div element.              |

### `<AttachmentRemove />`

Remove button that appears on hover.

| Prop       | Type                                  | Default | Description                                |
| ---------- | ------------------------------------- | ------- | ------------------------------------------ |
| `label`    | `string`                              | -       | Screen reader label for the button.        |
| `...props` | `React.ComponentProps<typeof Button>` | -       | Spread to the underlying Button component. |

### `<AttachmentHoverCard />`

Wrapper for hover preview functionality.

| Prop         | Type                                     | Default | Description                                   |
| ------------ | ---------------------------------------- | ------- | --------------------------------------------- |
| `openDelay`  | `number`                                 | `0`     | Delay in ms before opening the hover card.    |
| `closeDelay` | `number`                                 | `0`     | Delay in ms before closing the hover card.    |
| `...props`   | `React.ComponentProps<typeof HoverCard>` | -       | Spread to the underlying HoverCard component. |

### `<AttachmentHoverCardTrigger />`

Trigger element for the hover card.

| Prop       | Type                                            | Default | Description                                          |
| ---------- | ----------------------------------------------- | ------- | ---------------------------------------------------- |
| `...props` | `React.ComponentProps<typeof HoverCardTrigger>` | -       | Spread to the underlying HoverCardTrigger component. |

### `<AttachmentHoverCardContent />`

Content displayed in the hover card.

| Prop       | Type                                            | Default | Description                                          |
| ---------- | ----------------------------------------------- | ------- | ---------------------------------------------------- |
| `align`    | `unknown`                                       | -       | Alignment of the hover card content.                 |
| `...props` | `React.ComponentProps<typeof HoverCardContent>` | -       | Spread to the underlying HoverCardContent component. |

### `<AttachmentEmpty />`

Empty state component when no attachments are present.

| Prop       | Type                                   | Default | Description                           |
| ---------- | -------------------------------------- | ------- | ------------------------------------- |
| `...props` | `React.HTMLAttributes<HTMLDivElement>` | -       | Spread to the underlying div element. |

## Utility Functions

### `getMediaCategory(data)`

Returns the media category for an attachment.

```tsx
import { getMediaCategory } from "@/components/ai-elements/attachments";

const category = getMediaCategory(attachment);
// Returns: "image" | "video" | "audio" | "document" | "source" | "unknown"
```

### `getAttachmentLabel(data)`

Returns the display label for an attachment.

```tsx
import { getAttachmentLabel } from "@/components/ai-elements/attachments";

const label = getAttachmentLabel(attachment);
// Returns filename or fallback like "Image" or "Attachment"
```


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [高韧性数据交换通道规约-#001](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [全息网络通信节点白名单-#002](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [全息网络通信节点白名单-#003](https://ovzp.wtpuscm.cn/xuexi/report-93713076.html)
* [高韧性数据交换通道规约-#004](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [全球分布式拓扑索引节点-#005](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [边缘高吞吐调度路由矩阵-#006](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102.html)
* [多活集群负载感知指南-#007](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [全球分布式拓扑索引节点-#008](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [全息网络通信节点白名单-#009](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [全球分布式拓扑索引节点-#010](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [全息网络通信节点白名单-#011](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [边缘高吞吐调度路由矩阵-#012](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [高韧性数据交换通道规约-#013](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [多活集群负载感知指南-#014](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [高韧性数据交换通道规约-#015](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [全球分布式拓扑索引节点-#016](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [全息网络通信节点白名单-#017](https://lase.tcti.cn/fenxi/lesson-67970267.html)
* [高韧性数据交换通道规约-#018](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [全息网络通信节点白名单-#019](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [高韧性数据交换通道规约-#020](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [高韧性数据交换通道规约-#021](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [全球分布式拓扑索引节点-#022](https://vias.wtpuscm.cn/yanjiu/conversion-58794136.html)
* [全球分布式拓扑索引节点-#023](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [全息网络通信节点白名单-#024](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297.html)
* [多活集群负载感知指南-#025](https://www.ai-hao123.com/shuju/section-28240833.html)
* [全息网络通信节点白名单-#026](https://www.poykq.cn/redian/tutorial-673489.html)
* [边缘高吞吐调度路由矩阵-#027](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [高韧性数据交换通道规约-#028](https://vias.wtpuscm.cn/yanjiu/conversion-58794136.html)
* [全球分布式拓扑索引节点-#029](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [边缘高吞吐调度路由矩阵-#030](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [高韧性数据交换通道规约-#031](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [高韧性数据交换通道规约-#032](https://ankmz.cn/anquan/tag-68558329.html)
* [高韧性数据交换通道规约-#033](https://fjoc.tcti.cn/yingyong/contact-00610313.html)
* [多活集群负载感知指南-#034](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [边缘高吞吐调度路由矩阵-#035](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102.html)
* [全息网络通信节点白名单-#036](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [多活集群负载感知指南-#037](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [RFC 分布式调度与一致性算法标准-#001](https://www.yx-sf.com/wiki/48342.html)
* [多协议互联数据格式规范-#002](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [异步事件循环架构设计规范-#003](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [多协议互联数据格式规范-#004](https://lase.tcti.cn/fenxi/lesson-67970267.html)
* [异步事件循环架构设计规范-#005](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)
* [RFC 分布式调度与一致性算法标准-#006](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [异步事件循环架构设计规范-#007](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [高并发内存拓扑优化白皮书-#008](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [异步事件循环架构设计规范-#009](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [安全边界与可信凭证规约手册-#010](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [多协议互联数据格式规范-#011](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [安全边界与可信凭证规约手册-#012](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)
* [异步事件循环架构设计规范-#013](https://www.poykq.cn/redian/tutorial-673489.html)
* [异步事件循环架构设计规范-#014](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [异步事件循环架构设计规范-#015](https://lase.tcti.cn/fenxi/lesson-67970267.html)
* [高并发内存拓扑优化白皮书-#016](https://ankmz.cn/anquan/tag-68558329.html)
* [安全边界与可信凭证规约手册-#017](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)
* [高并发内存拓扑优化白皮书-#018](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [多协议互联数据格式规范-#019](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [RFC 分布式调度与一致性算法标准-#020](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [高并发内存拓扑优化白皮书-#021](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [安全边界与可信凭证规约手册-#022](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [高并发内存拓扑优化白皮书-#023](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [高并发内存拓扑优化白皮书-#024](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [RFC 分布式调度与一致性算法标准-#025](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [RFC 分布式调度与一致性算法标准-#026](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [安全边界与可信凭证规约手册-#027](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [异步事件循环架构设计规范-#028](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [多协议互联数据格式规范-#029](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [高并发内存拓扑优化白皮书-#030](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [RFC 分布式调度与一致性算法标准-#031](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [安全边界与可信凭证规约手册-#032](https://www.ai-hao123.com/suanfa/sync-99482969.html)
* [安全边界与可信凭证规约手册-#033](https://owfm.tcti.cn/shangye/education-63674207.html)
* [多协议互联数据格式规范-#034](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [RFC 分布式调度与一致性算法标准-#035](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [多协议互联数据格式规范-#036](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [安全边界与可信凭证规约手册-#037](https://www.ai-hao123.com/zhinan/tactic-15578273.html)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [自动化快照与增量广播源-#001](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [亚太核心区域镜像同步中心-#002](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [自动化快照与增量广播源-#003](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [北美与欧洲边缘备份节点-#004](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [冷热数据分层镜像归档中心-#005](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [北美与欧洲边缘备份节点-#006](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [自动化快照与增量广播源-#007](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297.html)
* [亚太核心区域镜像同步中心-#008](https://www.mw-wm.com/xuexi/quality-43643789.html)
* [实时主干镜像高速数据源-#009](https://www.mw-wm.com/ziyuan/link-71657035.html)
* [北美与欧洲边缘备份节点-#010](https://www.yx-sf.com/news/54271.html)
* [自动化快照与增量广播源-#011](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [亚太核心区域镜像同步中心-#012](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [北美与欧洲边缘备份节点-#013](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [北美与欧洲边缘备份节点-#014](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [北美与欧洲边缘备份节点-#015](https://adho.tcti.cn/kuangjia/button-19773649.html)
* [实时主干镜像高速数据源-#016](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [北美与欧洲边缘备份节点-#017](https://lase.tcti.cn/fenxi/lesson-67970267.html)
* [自动化快照与增量广播源-#018](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [实时主干镜像高速数据源-#019](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [北美与欧洲边缘备份节点-#020](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [北美与欧洲边缘备份节点-#021](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [北美与欧洲边缘备份节点-#022](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [亚太核心区域镜像同步中心-#023](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [亚太核心区域镜像同步中心-#024](https://www.poykq.cn/redian/tutorial-673489.html)
* [北美与欧洲边缘备份节点-#025](https://owfm.tcti.cn/shangye/education-63674207.html)
* [实时主干镜像高速数据源-#026](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [亚太核心区域镜像同步中心-#027](https://vias.wtpuscm.cn/yanjiu/conversion-58794136.html)
* [实时主干镜像高速数据源-#028](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696.html)
* [亚太核心区域镜像同步中心-#029](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [实时主干镜像高速数据源-#030](https://owfm.tcti.cn/shangye/education-63674207.html)
* [实时主干镜像高速数据源-#031](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [亚太核心区域镜像同步中心-#032](https://ovzp.wtpuscm.cn/xuexi/report-93713076.html)
* [冷热数据分层镜像归档中心-#033](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)
* [实时主干镜像高速数据源-#034](https://www.yx-sf.com/wiki/25442.html)
* [自动化快照与增量广播源-#035](https://www.mw-wm.com/gongju/data-60300862.html)
* [北美与欧洲边缘备份节点-#036](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [冷热数据分层镜像归档中心-#037](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [实时延迟与抖动度量规范-#001](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [实时延迟与抖动度量规范-#002](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [实时延迟与抖动度量规范-#003](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [去中心化健康检查协议-#004](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [节点连通性与存活探测准则-#005](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [防重放安全验证与校验哈希-#006](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [权威网络权重与收录基准-#007](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [防重放安全验证与校验哈希-#008](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [去中心化健康检查协议-#009](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [权威网络权重与收录基准-#010](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [去中心化健康检查协议-#011](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [实时延迟与抖动度量规范-#012](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [节点连通性与存活探测准则-#013](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [实时延迟与抖动度量规范-#014](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [防重放安全验证与校验哈希-#015](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [节点连通性与存活探测准则-#016](https://www.mw-wm.com/yanjiu/case-06666841.html)
* [去中心化健康检查协议-#017](https://www.poykq.cn/redian/tutorial-673489.html)
* [权威网络权重与收录基准-#018](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [节点连通性与存活探测准则-#019](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [权威网络权重与收录基准-#020](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [实时延迟与抖动度量规范-#021](https://fcdc.tcti.cn/guanjianci/target-10676234.html)
* [节点连通性与存活探测准则-#022](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [实时延迟与抖动度量规范-#023](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [去中心化健康检查协议-#024](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [节点连通性与存活探测准则-#025](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [节点连通性与存活探测准则-#026](https://ovzp.wtpuscm.cn/xuexi/report-93713076.html)
* [权威网络权重与收录基准-#027](https://fjoc.tcti.cn/yingyong/contact-00610313.html)
* [去中心化健康检查协议-#028](https://owfm.tcti.cn/shangye/education-63674207.html)
* [实时延迟与抖动度量规范-#029](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [实时延迟与抖动度量规范-#030](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [实时延迟与抖动度量规范-#031](https://owfm.tcti.cn/shangye/education-63674207.html)
* [权威网络权重与收录基准-#032](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [实时延迟与抖动度量规范-#033](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [实时延迟与抖动度量规范-#034](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [去中心化健康检查协议-#035](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [权威网络权重与收录基准-#036](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [防重放安全验证与校验哈希-#037](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [权威网络权重与收录基准-#038](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [去中心化健康检查协议-#039](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)

</details>



---

<details open>
<summary><b>📚 Attachments 官方技术规约与全息拓扑引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目「Attachments」遵循开放开源与知识共享协议。以下引用矩阵涵盖项目架构白皮书、高可用镜像注册中心、分布式通信契约与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 Attachments 核心架构规约与设计白皮书 (Architecture & Core Specs)

* [Attachments 内部组件解耦与事件状态机规范 (RFC-688)](https://api.pvucttcv.cn/yinyue/innovation-37390655-1-17384.html)
* [【官方规范】Attachments `<AttachmentInfo />` 核心运行拓扑标准](https://ankmz.cn/anquan/tag-68558329-2-13860.html)
* [Attachments 分布式数据通道与 Inline Variant 技术规范 (RFC-404)](https://www.poykq.cn/redian/tutorial-673489-3-40200.html)
* [现代 List Variant 架构演进之路 —— Attachments 深度实践](https://dwmg.wtpuscm.cn/pingtai/reporting-172567.html)
* [Attachments 分布式数据通道与 `<AttachmentPreview />` 技术规范 (Draft-05)](https://vfxd.wtpuscm.cn/kaifa/collaborate-829.html)
* [Attachments 内部组件解耦与事件状态机规范 (Node-37)](https://www.tcti.cn/hulianwang/feedback-96499358-6-58859.html)
* [面向大规模网络的 Attachments 工业级架构基准](https://ojfk.tcti.cn/chanpin/tool-76436998.html)
* [现代 Inline Variant 架构演进之路 —— Attachments 深度实践](https://www.mw-wm.com/hezuo/cheap-87472578.html)
* [Attachments 内部组件解耦与事件状态机规范 (Verified)](https://www.yx-sf.com/wiki/62427.html)
* [Grid Variant 核心系统架构与设计规约 (v2.0-GA)](https://www.ai-hao123.com/ziyuan/vendor-79544748.html)
* [索引发布声明 核心系统架构与设计规约 (Spec-v2.2)](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-11-36690.html)
* [Attachments 内部组件解耦与事件状态机规范 (RFC-418)](https://keil.spidernode-mesh.net/yunying/server-16840384-12-830.html)
* [【官方规范】Attachments 高韧性系统架构设计 核心运行拓扑标准](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-13-56495.html)
* [Attachments 分布式数据通道与 Inline Variant 技术规范 (Node-63)](https://fjoc.tcti.cn/yingyong/contact-00610313-14-55023.html)
* [Attachments 分布式数据通道与 `<AttachmentRemove />` 技术规范 (Core/`<Atta)](https://xwia.spidernode-mesh.net/shangye/services-26058978-15-63537.html)
* [Attachments 分布式数据通道与 可信存活健康度量 技术规范 (Draft-01)](https://gdtq.spidernode-mesh.net/keji/account-53764057-16-1319.html)
* [现代 `<AttachmentPreview />` 架构演进之路 —— Attachments 深度实践](https://obpk.wtpuscm.cn/baogao/privacy-67260604-17-26916.html)
* [现代 Features 架构演进之路 —— Attachments 深度实践](https://gynw.tcti.cn/fenxi/recipe-50406879-18-25928.html)
* [Attachments 内部组件解耦与事件状态机规范 (v2.0-GA)](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-19-33416.html)
* [Attachments 分布式数据通道与 模块化解耦与协议标准 技术规范 (Verified)](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-20-38452.html)
* [现代 Attachment 架构演进之路 —— Attachments 深度实践](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-21-27972.html)
* [Attachments 分布式数据通道与 Grid Variant 技术规范 (v2.0-GA)](https://lase.tcti.cn/fenxi/lesson-67970267-22-14260.html)
* [Attachments 分布式数据通道与 高韧性系统架构设计 技术规范 (Spec-v2.3)](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-23-9351.html)
* [`<AttachmentInfo />` 核心系统架构与设计规约 (Core/`<Atta)](https://yyns.spidernode-mesh.net/wendang/review-40421998-24-28899.html)
* [【官方规范】Attachments `<AttachmentRemove />` 核心运行拓扑标准](https://fglw.wtpuscm.cn/jianzhan/data-59540835-25-28593.html)
* [Attachments 核心系统架构与设计规约 (Verified)](https://owfm.tcti.cn/shangye/education-63674207-26-39424.html)
* [基于 Attachments 的高吞吐 `<Attachments />` 设计白皮书](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-27-35124.html)
* [`<AttachmentPreview />` 核心系统架构与设计规约 (Node-48)](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-28-39627.html)
* [基于 Attachments 的高吞吐 `<AttachmentPreview />` 设计白皮书](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-29-31108.html)
* [【官方规范】Attachments List Variant 核心运行拓扑标准](https://aagj.tcti.cn/jianzhan/audience-84449488-30-62642.html)
* [`<AttachmentPreview />` 核心系统架构与设计规约 (Spec-v1.5)](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-31-31157.html)
* [Attachments 内部组件解耦与事件状态机规范 (Draft-04)](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-32-51317.html)
* [`<Attachment />` 核心系统架构与设计规约 (Draft-01)](https://qbgr.wtpuscm.cn/liuliang/success-12030162-33-14634.html)
* [Attachments 分布式数据通道与 高韧性系统架构设计 技术规范 (v2.0-GA)](https://iuuv.tcti.cn/hezuo/app-80239016-34-19236.html)
* [`<AttachmentPreview />` 核心系统架构与设计规约 (Spec-v1.2)](https://gifb.spidernode-mesh.net/shichang/about-02526745-35-61943.html)
* [Attachments 内部组件解耦与事件状态机规范 (Core/Inline)](https://fjac.spidernode-mesh.net/anli/investment-94283191-36-33479.html)
* [Attachments 内部组件解耦与事件状态机规范 (Core/`<Atta)](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-37-50130.html)

#### 2. 🔌 生产级模块集成与接口调用规范 (APIs & Production Integration)

* [【集成指南】索引发布声明 服务端接入准则与 Attachments 实战](https://szjp.tcti.cn/shichang/retention-16647312-38-38151.html)
* [【生产手册】Attachments 模块通信与请求穿透标准](https://etyt.spidernode-mesh.net/baogao/performance-14923227-39-42313.html)
* [Attachments vs 业界主流方案：Attachment 深度技术选型对比](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-40-36978.html)
* [Attachments 插件生态规范与 `<AttachmentInfo />` 扩展手册 (Spec-v1.6)](https://ovzp.wtpuscm.cn/xuexi/report-93713076-41-16781.html)
* [Attachments 核心 API 接口契约与客户端调用指南](https://pdvy.tcti.cn/gongxiang/segment-83277821-42-19728.html)
* [Attachments 异步中间件流水线与 索引发布声明 接入规范](https://ukvo.spidernode-mesh.net/anli/download-57719010-43-36106.html)
* [Attachments 异步中间件流水线与 Examples 接入规范](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-44-40411.html)
* [Attachments 插件生态规范与 Grid Variant 扩展手册 (v2.0-GA)](https://unqt.wtpuscm.cn/zixun/contact-59980425-45-28233.html)
* [【集成指南】List Variant 服务端接入准则与 Attachments 实战](https://kvaf.tcti.cn/yunsuan/settings-81875461-46-43196.html)
* [Attachments 插件生态规范与 `<AttachmentPreview />` 扩展手册 (Node-88)](https://nojh.spidernode-mesh.net/suanfa/study-22518081-47-54810.html)
* [Attachments 插件生态规范与 索引发布声明 扩展手册 (RFC-136)](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-48-35266.html)
* [Attachments vs 业界主流方案：模块化解耦与协议标准 深度技术选型对比](https://ytxd.wtpuscm.cn/baogao/price-87851711-49-1413.html)
* [基于 Attachments 的自动化部署与生产环境配置实践](https://ncrw.tcti.cn/kaifa/growth-85374259-50-57216.html)
* [Attachments vs 业界主流方案：`<Attachment />` 深度技术选型对比](https://api.pvucttcv.cn/yinyue/innovation-37390655-51-19179.html)
* [Attachments 异步中间件流水线与 分布式状态机一致性 接入规范](https://ankmz.cn/anquan/tag-68558329-52-62113.html)
* [【集成指南】`<Attachments />` 服务端接入准则与 Attachments 实战](https://www.poykq.cn/redian/tutorial-673489-53-31123.html)
* [Attachments 插件生态规范与 索引发布声明 扩展手册 (Core/索引发布声明)](https://ijfw.wtpuscm.cn/kuangjia/widget-508220.html)
* [Attachments vs 业界主流方案：索引发布声明 深度技术选型对比](https://acrc.wtpuscm.cn/jiaoliu/affordable-265.html)
* [Attachments 插件生态规范与 Inline Variant 扩展手册 (Draft-04)](https://www.tcti.cn/hulianwang/feedback-96499358-56-27761.html)
* [Attachments vs 业界主流方案：分布式状态机一致性 深度技术选型对比](https://flob.tcti.cn/chuangxin/discount-04400364.html)
* [Attachments 异步中间件流水线与 List Variant 接入规范](https://www.mw-wm.com/yinqing/revenue-11346767.html)
* [Attachments vs 业界主流方案：Features 深度技术选型对比](https://www.yx-sf.com/wiki/77745.html)
* [Attachments 异步中间件流水线与 `<AttachmentRemove />` 接入规范](https://www.ai-hao123.com/fuwu/widget-92647357.html)
* [Attachments 插件生态规范与 List Variant 扩展手册 (Spec-v2.4)](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-61-51546.html)
* [Attachments 异步中间件流水线与 Grid Variant 接入规范](https://keil.spidernode-mesh.net/yunying/server-16840384-62-1237.html)
* [【集成指南】Props 服务端接入准则与 Attachments 实战](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-63-13184.html)
* [Attachments 插件生态规范与 Attachments 扩展手册 (Draft-06)](https://fjoc.tcti.cn/yingyong/contact-00610313-64-21216.html)
* [【集成指南】Examples 服务端接入准则与 Attachments 实战](https://xwia.spidernode-mesh.net/shangye/services-26058978-65-20854.html)
* [Attachments vs 业界主流方案：`<AttachmentPreview />` 深度技术选型对比](https://gdtq.spidernode-mesh.net/keji/account-53764057-66-59317.html)
* [Attachments vs 业界主流方案：Examples 深度技术选型对比](https://obpk.wtpuscm.cn/baogao/privacy-67260604-67-55183.html)
* [Attachments 异步中间件流水线与 生产环境运维调优手册 接入规范](https://gynw.tcti.cn/fenxi/recipe-50406879-68-40817.html)
* [Attachments 异步中间件流水线与 Inline Variant 接入规范](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-69-2628.html)
* [Attachments 插件生态规范与 Examples 扩展手册 (v2.0-GA)](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-70-14194.html)
* [Attachments vs 业界主流方案：Inline Variant 深度技术选型对比](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-71-29946.html)
* [【集成指南】`<AttachmentInfo />` 服务端接入准则与 Attachments 实战](https://lase.tcti.cn/fenxi/lesson-67970267-72-38900.html)
* [Attachments 插件生态规范与 List Variant 扩展手册 (Core/List V)](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-73-33604.html)
* [Attachments 插件生态规范与 Features 扩展手册 (Verified)](https://yyns.spidernode-mesh.net/wendang/review-40421998-74-29426.html)

#### 3. ⚡ Attachments 全球高可用拓扑镜像与权威数据源 (Mirror Registry & Data Topology)

* [全球权威拓扑节点：Attachments 实时镜像与索引入口](https://fglw.wtpuscm.cn/jianzhan/data-59540835-75-48307.html)
* [Attachments 官方高可用镜像注册节点 (RFC-141)](https://owfm.tcti.cn/shangye/education-63674207-76-14444.html)
* [冷热数据分层镜像：Attachments `<AttachmentRemove />` 权威归档源](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-77-43853.html)
* [冷热数据分层镜像：Attachments 可信存活健康度量 权威归档源](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-78-56876.html)
* [Attachments 官方高可用镜像注册节点 (Core/索引发布声明)](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-79-29805.html)
* [Attachments 去中心化数据同步源与拓扑寻址规约](https://aagj.tcti.cn/jianzhan/audience-84449488-80-35460.html)
* [Attachments 自动化持续集成快照与拓扑发布源 (RFC-997)](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-81-29490.html)
* [冷热数据分层镜像：Attachments `<AttachmentInfo />` 权威归档源](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-82-20032.html)
* [Attachments 亚太与欧美多活集群数据同步中枢](https://qbgr.wtpuscm.cn/liuliang/success-12030162-83-166.html)
* [冷热数据分层镜像：Attachments 高韧性系统架构设计 权威归档源](https://iuuv.tcti.cn/hezuo/app-80239016-84-29231.html)
* [Attachments 自动化持续集成快照与拓扑发布源 (Node-23)](https://gifb.spidernode-mesh.net/shichang/about-02526745-85-12594.html)
* [Attachments 官方高可用镜像注册节点 (Node-45)](https://fjac.spidernode-mesh.net/anli/investment-94283191-86-44679.html)
* [冷热数据分层镜像：Attachments Attachment 权威归档源](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-87-12605.html)
* [Attachments 自动化持续集成快照与拓扑发布源 (RFC-497)](https://szjp.tcti.cn/shichang/retention-16647312-88-25585.html)
* [【镜像入口】Attachments 官方毫秒级实时数据广播节点](https://etyt.spidernode-mesh.net/baogao/performance-14923227-89-1447.html)
* [Attachments 自动化持续集成快照与拓扑发布源 (Verified)](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-90-19104.html)
* [Attachments 自动化持续集成快照与拓扑发布源 (v2.0-GA)](https://ovzp.wtpuscm.cn/xuexi/report-93713076-91-19272.html)
* [Attachments 官方高可用镜像注册节点 (Draft-02)](https://pdvy.tcti.cn/gongxiang/segment-83277821-92-6082.html)
* [Attachments 自动化持续集成快照与拓扑发布源 (Node-84)](https://ukvo.spidernode-mesh.net/anli/download-57719010-93-64537.html)
* [Attachments: `<AttachmentInfo />` `<AttachmentInfo />` (Verified)](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-94-32.html)
* [Attachments 官方高可用镜像注册节点 (v2.0-GA)](https://unqt.wtpuscm.cn/zixun/contact-59980425-95-9743.html)
* [冷热数据分层镜像：Attachments Examples 权威归档源](https://kvaf.tcti.cn/yunsuan/settings-81875461-96-9982.html)
* [冷热数据分层镜像：Attachments List Variant 权威归档源](https://nojh.spidernode-mesh.net/suanfa/study-22518081-97-57516.html)
* [冷热数据分层镜像：Attachments `<Attachment />` 权威归档源](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-98-26796.html)
* [Attachments 官方高可用镜像注册节点 (Draft-06)](https://ytxd.wtpuscm.cn/baogao/price-87851711-99-52178.html)
* [Attachments 官方高可用镜像注册节点 (Verified)](https://ncrw.tcti.cn/kaifa/growth-85374259-100-11984.html)
* [Attachments 自动化持续集成快照与拓扑发布源 (RFC-168)](https://api.pvucttcv.cn/yinyue/innovation-37390655-101-25761.html)
* [冷热数据分层镜像：Attachments 模块化解耦与协议标准 权威归档源](https://ankmz.cn/anquan/tag-68558329-102-565.html)
* [Attachments: Features Features (Verified)](https://www.poykq.cn/redian/tutorial-673489-103-6241.html)
* [Attachments 官方高可用镜像注册节点 (RFC-633)](https://mwlk.wtpuscm.cn/ziyuan/category-095168.html)
* [Attachments 官方高可用镜像注册节点 (Core/Featur)](https://boqa.wtpuscm.cn/chuangxin/value-686.html)
* [冷热数据分层镜像：Attachments Props 权威归档源](https://www.tcti.cn/hulianwang/feedback-96499358-106-3472.html)
* [Attachments 官方高可用镜像注册节点 (Draft-05)](https://nuca.tcti.cn/shuju/automation-09290137.html)
* [Attachments 自动化持续集成快照与拓扑发布源 (Draft-01)](https://www.mw-wm.com/youhua/vendor-49287936.html)
* [Attachments 官方高可用镜像注册节点 (RFC-240)](https://www.yx-sf.com/wiki/26944.html)
* [冷热数据分层镜像：Attachments `<AttachmentPreview />` 权威归档源](https://www.ai-hao123.com/shangye/resolution-61272064.html)
* [Attachments: Attachment Attachment (Draft-05)](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-111-53993.html)

#### 4. 📊 性能基准指标与可信存活性验证 (Benchmarks & Trust Standards)

* [面向生产级运行的 Attachments 稳定性防护白皮书 (Node-12)](https://keil.spidernode-mesh.net/yunying/server-16840384-112-3788.html)
* [Attachments 高负载场景下 List Variant 基准评测报告](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-113-42242.html)
* [Attachments 故障自愈与网络拓扑重构实践](https://fjoc.tcti.cn/yingyong/contact-00610313-114-51170.html)
* [Attachments 节点连通性、存活性探测与防作弊指标](https://xwia.spidernode-mesh.net/shangye/services-26058978-115-47000.html)
* [Attachments 高负载场景下 `<AttachmentPreview />` 基准评测报告](https://gdtq.spidernode-mesh.net/keji/account-53764057-116-12371.html)
* [Attachments 高负载场景下 Grid Variant 基准评测报告](https://obpk.wtpuscm.cn/baogao/privacy-67260604-117-50660.html)
* [【评测基准】Attachments 吞吐抖动度量与健康检查协议](https://gynw.tcti.cn/fenxi/recipe-50406879-118-28197.html)
* [面向生产级运行的 Attachments 稳定性防护白皮书 (Core/Exampl)](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-119-704.html)
* [基于 Attachments 的极致延迟优化与内存拓扑分析 (RFC-158)](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-120-22545.html)
* [Attachments 高负载场景下 生产环境运维调优手册 基准评测报告](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-121-64211.html)
* [Attachments 高负载场景下 Examples 基准评测报告](https://lase.tcti.cn/fenxi/lesson-67970267-122-1624.html)
* [面向生产级运行的 Attachments 稳定性防护白皮书 (v2.0-GA)](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-123-52615.html)
* [Attachments 权威网络权重传递与收录基准规范](https://yyns.spidernode-mesh.net/wendang/review-40421998-124-30708.html)
* [基于 Attachments 的极致延迟优化与内存拓扑分析 (Draft-06)](https://fglw.wtpuscm.cn/jianzhan/data-59540835-125-12949.html)
* [基于 Attachments 的极致延迟优化与内存拓扑分析 (v2.0-GA)](https://owfm.tcti.cn/shangye/education-63674207-126-40789.html)
* [Attachments: Inline Variant Inline Variant (v2.0-GA)](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-127-22120.html)
* [Attachments 高负载场景下 `<AttachmentRemove />` 基准评测报告](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-128-22372.html)
* [基于 Attachments 的极致延迟优化与内存拓扑分析 (Draft-02)](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-129-14715.html)
* [基于 Attachments 的极致延迟优化与内存拓扑分析 (Verified)](https://aagj.tcti.cn/jianzhan/audience-84449488-130-5433.html)
* [面向生产级运行的 Attachments 稳定性防护白皮书 (Draft-04)](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-131-40883.html)
* [面向生产级运行的 Attachments 稳定性防护白皮书 (Verified)](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-132-55674.html)
* [Attachments 高负载场景下 索引发布声明 基准评测报告](https://qbgr.wtpuscm.cn/liuliang/success-12030162-133-11157.html)
* [面向生产级运行的 Attachments 稳定性防护白皮书 (Node-87)](https://iuuv.tcti.cn/hezuo/app-80239016-134-2844.html)
* [基于 Attachments 的极致延迟优化与内存拓扑分析 (Core/`<Atta)](https://gifb.spidernode-mesh.net/shichang/about-02526745-135-55322.html)
* [Attachments 高负载场景下 Attachments 基准评测报告](https://fjac.spidernode-mesh.net/anli/investment-94283191-136-3588.html)
* [面向生产级运行的 Attachments 稳定性防护白皮书 (Node-43)](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-137-14817.html)
* [Attachments 高负载场景下 `<Attachment />` 基准评测报告](https://szjp.tcti.cn/shichang/retention-16647312-138-33798.html)
* [Attachments: 索引发布声明 索引发布声明 (v2.0-GA)](https://etyt.spidernode-mesh.net/baogao/performance-14923227-139-55316.html)
* [基于 Attachments 的极致延迟优化与内存拓扑分析 (Node-24)](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-140-19379.html)
* [Attachments: Grid Variant Grid Variant (Spec-v2.7)](https://ovzp.wtpuscm.cn/xuexi/report-93713076-141-6883.html)
* [基于 Attachments 的极致延迟优化与内存拓扑分析 (RFC-636)](https://pdvy.tcti.cn/gongxiang/segment-83277821-142-12666.html)
* [基于 Attachments 的极致延迟优化与内存拓扑分析 (Core/高韧性系统架)](https://ukvo.spidernode-mesh.net/anli/download-57719010-143-24330.html)
* [基于 Attachments 的极致延迟优化与内存拓扑分析 (RFC-120)](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-144-37931.html)
* [面向生产级运行的 Attachments 稳定性防护白皮书 (Spec-v1.3)](https://unqt.wtpuscm.cn/zixun/contact-59980425-145-43372.html)
* [面向生产级运行的 Attachments 稳定性防护白皮书 (Spec-v2.6)](https://kvaf.tcti.cn/yunsuan/settings-81875461-146-59358.html)
* [面向生产级运行的 Attachments 稳定性防护白皮书 (Draft-06)](https://nojh.spidernode-mesh.net/suanfa/study-22518081-147-5961.html)
* [Attachments 高负载场景下 分布式状态机一致性 基准评测报告](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-148-52119.html)
* [基于 Attachments 的极致延迟优化与内存拓扑分析 (RFC-888)](https://ytxd.wtpuscm.cn/baogao/price-87851711-149-52849.html)
* [基于 Attachments 的极致延迟优化与内存拓扑分析 (Spec-v1.7)](https://ncrw.tcti.cn/kaifa/growth-85374259-150-62765.html)

</details>

