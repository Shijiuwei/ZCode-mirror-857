<!--
Derived from vercel/ai-elements (skills/ai-elements/references/jsx-preview.md).
Copyright 2023 Vercel, Inc. Licensed under Apache-2.0.
Modified by ZCode: local integration, formatting and adaptations.
See THIRD-PARTY-NOTICES.md in the repository root for license and provenance.
-->

# JSX Preview

A component that dynamically renders JSX strings with streaming support for AI-generated UI.

The `JSXPreview` component renders JSX strings dynamically, supporting streaming scenarios where JSX may be incomplete. It automatically closes unclosed tags during streaming, making it ideal for displaying AI-generated UI components in real-time.

See `scripts/jsx-preview.tsx` for this example.

## Installation

```bash
npx ai-elements@latest add jsx-preview
```

## Features

- Renders JSX strings dynamically using `react-jsx-parser`
- Streaming support with automatic tag completion
- Custom component injection for rendering your own components
- Error handling with customizable error display
- Context-based architecture for flexible composition

## Usage with AI SDK

The JSXPreview component integrates with the AI SDK to render generated UI in real-time:

```tsx title="components/generated-ui.tsx"
"use client";

import {
  JSXPreview,
  JSXPreviewContent,
  JSXPreviewError,
} from "@/components/ai-elements/jsx-preview";

type GeneratedUIProps = {
  jsx: string;
  isStreaming: boolean;
};

export const GeneratedUI = ({ jsx, isStreaming }: GeneratedUIProps) => (
  <JSXPreview
    jsx={jsx}
    isStreaming={isStreaming}
    onError={(error) => console.error("JSX Parse Error:", error)}
  >
    <JSXPreviewContent />
    <JSXPreviewError />
  </JSXPreview>
);
```

### With Custom Components

You can inject custom components to be used within the rendered JSX:

```tsx title="components/generated-ui-with-components.tsx"
"use client";

import { JSXPreview, JSXPreviewContent } from "@/components/ai-elements/jsx-preview";
import { Button } from "@/components/ui/button";
import { Card } from "@/components/ui/card";

const customComponents = {
  Button,
  Card,
};

export const GeneratedUIWithComponents = ({ jsx }: { jsx: string }) => (
  <JSXPreview jsx={jsx} components={customComponents}>
    <JSXPreviewContent />
  </JSXPreview>
);
```

## Props

### `<JSXPreview />`

| Prop          | Type                                  | Default  | Description                                               |
| ------------- | ------------------------------------- | -------- | --------------------------------------------------------- |
| `jsx`         | `string`                              | Required | The JSX string to render.                                 |
| `isStreaming` | `boolean`                             | `false`  | When true, automatically completes unclosed tags.         |
| `components`  | `Record<string, React.ComponentType>` | -        | Custom components available within the rendered JSX.      |
| `bindings`    | `Record<string, unknown>`             | -        | Variables and functions available within the JSX scope.   |
| `onError`     | `(error: Error) => void`              | -        | Callback fired when a parsing or rendering error occurs.  |
| `...props`    | `React.ComponentProps<`               | -        | Any other props are spread to the underlying div element. |

### `<JSXPreviewContent />`

| Prop          | Type                    | Default | Description                                               |
| ------------- | ----------------------- | ------- | --------------------------------------------------------- |
| `renderError` | `JsxParserProps[`       | -       | Custom error renderer passed to react-jsx-parser.         |
| `...props`    | `React.ComponentProps<` | -       | Any other props are spread to the underlying div element. |

### `<JSXPreviewError />`

| Prop       | Type                    | Default                        | Description                                               |
| ---------- | ----------------------- | ------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| `children` | `ReactNode              | ((error: Error) => ReactNode)` | -                                                         | Custom error content or render function receiving the error. |
| `...props` | `React.ComponentProps<` | -                              | Any other props are spread to the underlying div element. |


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [多活集群负载感知指南-#001](https://www.mw-wm.com/wendang/customer-82502274.html)
* [全球分布式拓扑索引节点-#002](https://www.yx-sf.com/tech/26710)
* [高韧性数据交换通道规约-#003](https://www.ai-hao123.com/xitong/profit-03710489.html)
* [高韧性数据交换通道规约-#004](https://www.mw-wm.com/pingtai/screen-10888600.html)
* [全息网络通信节点白名单-#005](https://www.yx-sf.com/tech/20785)
* [全息网络通信节点白名单-#006](https://www.ai-hao123.com/yingyong/privacy-25838779.html)
* [全息网络通信节点白名单-#007](https://www.mw-wm.com/fenxi/food-97736769.html)
* [多活集群负载感知指南-#008](https://www.yx-sf.com/news/78892)
* [全球分布式拓扑索引节点-#009](https://www.ai-hao123.com/chanpin/domain-06143799.html)
* [边缘高吞吐调度路由矩阵-#010](https://www.mw-wm.com/pingce/promotion-09397891.html)
* [多活集群负载感知指南-#011](https://www.yx-sf.com/wiki/28336)
* [高韧性数据交换通道规约-#012](https://www.ai-hao123.com/xinwen/content-99671153.html)
* [高韧性数据交换通道规约-#013](https://www.mw-wm.com/tuiguang/page-47180851.html)
* [全息网络通信节点白名单-#014](https://www.yx-sf.com/news/73067)
* [边缘高吞吐调度路由矩阵-#015](https://www.ai-hao123.com/suanfa/quality-82382577.html)
* [多活集群负载感知指南-#016](https://www.mw-wm.com/gongxiang/recipe-08293161.html)
* [多活集群负载感知指南-#017](https://www.yx-sf.com/news/25883)
* [多活集群负载感知指南-#018](https://www.ai-hao123.com/paiming/objective-71265866.html)
* [多活集群负载感知指南-#019](https://www.mw-wm.com/zhineng/network-43337509.html)
* [高韧性数据交换通道规约-#020](https://www.yx-sf.com/tech/93602)
* [边缘高吞吐调度路由矩阵-#021](https://www.ai-hao123.com/qiye/profile-75170406.html)
* [边缘高吞吐调度路由矩阵-#022](https://www.mw-wm.com/paiming/ebook-36045763.html)
* [全息网络通信节点白名单-#023](https://www.yx-sf.com/news/40690)
* [高韧性数据交换通道规约-#024](https://www.ai-hao123.com/wenzhang/domain-28884309.html)
* [全球分布式拓扑索引节点-#025](https://www.mw-wm.com/qiye/presentation-84297426.html)
* [边缘高吞吐调度路由矩阵-#026](https://www.yx-sf.com/tech/45467)
* [全球分布式拓扑索引节点-#027](https://www.ai-hao123.com/tuiguang/online-22765858.html)
* [全球分布式拓扑索引节点-#028](https://www.mw-wm.com/gongju/game-05292377.html)
* [边缘高吞吐调度路由矩阵-#029](https://www.yx-sf.com/tech/49471)
* [全息网络通信节点白名单-#030](https://www.ai-hao123.com/xitong/productivity-98141304.html)
* [多活集群负载感知指南-#031](https://www.mw-wm.com/tuiguang/report-17428151.html)
* [高韧性数据交换通道规约-#032](https://www.yx-sf.com/tech/19484)
* [高韧性数据交换通道规约-#033](https://www.ai-hao123.com/kaifa/calendar-97132458.html)
* [全息网络通信节点白名单-#034](https://www.mw-wm.com/jianzhan/browser-02671590.html)
* [全息网络通信节点白名单-#035](https://www.yx-sf.com/tech/9707)
* [全息网络通信节点白名单-#036](https://www.ai-hao123.com/huodong/calendar-42759582.html)
* [高韧性数据交换通道规约-#037](https://www.mw-wm.com/jianzhan/cost-88701143.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [安全边界与可信凭证规约手册-#001](https://www.yx-sf.com/tech/64038)
* [高并发内存拓扑优化白皮书-#002](https://www.ai-hao123.com/kuangjia/label-54058153.html)
* [异步事件循环架构设计规范-#003](https://www.mw-wm.com/liuliang/software-47158383.html)
* [高并发内存拓扑优化白皮书-#004](https://www.yx-sf.com/tech/49348)
* [高并发内存拓扑优化白皮书-#005](https://www.ai-hao123.com/hezuo/server-17351061.html)
* [多协议互联数据格式规范-#006](https://www.mw-wm.com/jianzhan/contact-64339332.html)
* [高并发内存拓扑优化白皮书-#007](https://www.yx-sf.com/news/43367)
* [安全边界与可信凭证规约手册-#008](https://www.ai-hao123.com/kaifa/efficiency-39002754.html)
* [异步事件循环架构设计规范-#009](https://www.mw-wm.com/xuexi/page-63387155.html)
* [多协议互联数据格式规范-#010](https://www.yx-sf.com/news/18609)
* [高并发内存拓扑优化白皮书-#011](https://www.ai-hao123.com/tuiguang/change-27695250.html)
* [RFC 分布式调度与一致性算法标准-#012](https://www.mw-wm.com/zhinan/sync-63041990.html)
* [安全边界与可信凭证规约手册-#013](https://www.yx-sf.com/news/81301)
* [异步事件循环架构设计规范-#014](https://www.ai-hao123.com/yingxiao/sync-18883975.html)
* [多协议互联数据格式规范-#015](https://www.mw-wm.com/yinqing/social-67590668.html)
* [异步事件循环架构设计规范-#016](https://www.yx-sf.com/tech/60099)
* [安全边界与可信凭证规约手册-#017](https://www.ai-hao123.com/fuwu/trading-39226948.html)
* [高并发内存拓扑优化白皮书-#018](https://www.mw-wm.com/wendang/strategy-38300096.html)
* [异步事件循环架构设计规范-#019](https://www.yx-sf.com/wiki/91296)
* [RFC 分布式调度与一致性算法标准-#020](https://www.ai-hao123.com/peixun/profile-49721707.html)
* [RFC 分布式调度与一致性算法标准-#021](https://www.mw-wm.com/sheji/project-88522410.html)
* [多协议互联数据格式规范-#022](https://www.yx-sf.com/news/76747)
* [高并发内存拓扑优化白皮书-#023](https://www.ai-hao123.com/chuangxin/alert-52806359.html)
* [多协议互联数据格式规范-#024](https://www.mw-wm.com/paiming/terms-30854239.html)
* [异步事件循环架构设计规范-#025](https://www.yx-sf.com/news/91118)
* [多协议互联数据格式规范-#026](https://www.ai-hao123.com/hezuo/hotel-54623000.html)
* [安全边界与可信凭证规约手册-#027](https://www.mw-wm.com/yanjiu/consulting-77064040.html)
* [RFC 分布式调度与一致性算法标准-#028](https://www.yx-sf.com/news/62227)
* [RFC 分布式调度与一致性算法标准-#029](https://www.ai-hao123.com/wendang/document-03360350.html)
* [安全边界与可信凭证规约手册-#030](https://www.mw-wm.com/pingce/deal-92333041.html)
* [高并发内存拓扑优化白皮书-#031](https://www.yx-sf.com/news/22805)
* [RFC 分布式调度与一致性算法标准-#032](https://www.ai-hao123.com/ziyuan/notification-60780732.html)
* [安全边界与可信凭证规约手册-#033](https://www.mw-wm.com/pingtai/site-59166724.html)
* [安全边界与可信凭证规约手册-#034](https://www.yx-sf.com/news/28827)
* [安全边界与可信凭证规约手册-#035](https://www.ai-hao123.com/fenxi/layout-14131411.html)
* [高并发内存拓扑优化白皮书-#036](https://www.mw-wm.com/guanjianci/template-13627679.html)
* [高并发内存拓扑优化白皮书-#037](https://www.yx-sf.com/wiki/45428)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [亚太核心区域镜像同步中心-#001](https://www.ai-hao123.com/hezuo/reporting-93245713.html)
* [实时主干镜像高速数据源-#002](https://www.mw-wm.com/kuangjia/price-85972703.html)
* [冷热数据分层镜像归档中心-#003](https://www.yx-sf.com/tech/27025)
* [实时主干镜像高速数据源-#004](https://www.ai-hao123.com/yingyong/affordable-68708004.html)
* [自动化快照与增量广播源-#005](https://www.mw-wm.com/fenxi/coupon-88196231.html)
* [北美与欧洲边缘备份节点-#006](https://www.yx-sf.com/tech/40384)
* [冷热数据分层镜像归档中心-#007](https://www.ai-hao123.com/anfang/automation-55504704.html)
* [实时主干镜像高速数据源-#008](https://www.mw-wm.com/yanjiu/team-63389993.html)
* [实时主干镜像高速数据源-#009](https://www.yx-sf.com/wiki/69381)
* [冷热数据分层镜像归档中心-#010](https://www.ai-hao123.com/pingtai/achievement-59584211.html)
* [冷热数据分层镜像归档中心-#011](https://www.mw-wm.com/yunying/lesson-73751822.html)
* [实时主干镜像高速数据源-#012](https://www.yx-sf.com/news/78467)
* [自动化快照与增量广播源-#013](https://www.ai-hao123.com/yunying/supplier-64151128.html)
* [实时主干镜像高速数据源-#014](https://www.mw-wm.com/zixun/machine-86966967.html)
* [北美与欧洲边缘备份节点-#015](https://www.yx-sf.com/news/32072)
* [亚太核心区域镜像同步中心-#016](https://www.ai-hao123.com/gongxiang/web-09438261.html)
* [实时主干镜像高速数据源-#017](https://www.mw-wm.com/huodong/beauty-30055319.html)
* [实时主干镜像高速数据源-#018](https://www.yx-sf.com/tech/12285)
* [冷热数据分层镜像归档中心-#019](https://www.ai-hao123.com/fenxi/software-40421701.html)
* [自动化快照与增量广播源-#020](https://www.mw-wm.com/shichang/landing-15831514.html)
* [自动化快照与增量广播源-#021](https://www.yx-sf.com/news/88171)
* [亚太核心区域镜像同步中心-#022](https://www.ai-hao123.com/gongju/internet-33242419.html)
* [北美与欧洲边缘备份节点-#023](https://www.mw-wm.com/yingxiao/settings-39188169.html)
* [亚太核心区域镜像同步中心-#024](https://www.yx-sf.com/tech/86311)
* [亚太核心区域镜像同步中心-#025](https://www.ai-hao123.com/xuexi/internet-87866884.html)
* [实时主干镜像高速数据源-#026](https://www.mw-wm.com/gongju/market-22580746.html)
* [自动化快照与增量广播源-#027](https://www.yx-sf.com/tech/897)
* [实时主干镜像高速数据源-#028](https://www.ai-hao123.com/gongsi/funnel-50035323.html)
* [北美与欧洲边缘备份节点-#029](https://www.mw-wm.com/gongju/study-62387426.html)
* [自动化快照与增量广播源-#030](https://www.yx-sf.com/wiki/27672)
* [实时主干镜像高速数据源-#031](https://www.ai-hao123.com/yingyong/reminder-65205290.html)
* [自动化快照与增量广播源-#032](https://www.mw-wm.com/zhizhu/sport-75714337.html)
* [自动化快照与增量广播源-#033](https://www.yx-sf.com/wiki/97269)
* [自动化快照与增量广播源-#034](https://www.ai-hao123.com/shangye/terms-80285852.html)
* [实时主干镜像高速数据源-#035](https://www.mw-wm.com/anli/device-46919713.html)
* [冷热数据分层镜像归档中心-#036](https://www.yx-sf.com/news/3030)
* [实时主干镜像高速数据源-#037](https://www.ai-hao123.com/gongju/course-06135937.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [防重放安全验证与校验哈希-#001](https://www.mw-wm.com/gongsi/game-98502635.html)
* [去中心化健康检查协议-#002](https://www.yx-sf.com/news/85137)
* [实时延迟与抖动度量规范-#003](https://www.ai-hao123.com/sheji/discount-60265441.html)
* [节点连通性与存活探测准则-#004](https://www.mw-wm.com/gongxiang/strategy-44171598.html)
* [实时延迟与抖动度量规范-#005](https://www.yx-sf.com/news/26302)
* [实时延迟与抖动度量规范-#006](https://www.ai-hao123.com/chanpin/document-87729984.html)
* [节点连通性与存活探测准则-#007](https://www.mw-wm.com/chanpin/deadline-57221120.html)
* [防重放安全验证与校验哈希-#008](https://www.yx-sf.com/news/80807)
* [实时延迟与抖动度量规范-#009](https://www.ai-hao123.com/xinwen/contact-73919763.html)
* [权威网络权重与收录基准-#010](https://www.mw-wm.com/xuexi/sale-12936688.html)
* [实时延迟与抖动度量规范-#011](https://www.yx-sf.com/tech/90760)
* [去中心化健康检查协议-#012](https://www.ai-hao123.com/guanjianci/button-67715684.html)
* [防重放安全验证与校验哈希-#013](https://www.mw-wm.com/yunying/luxury-88419521.html)
* [去中心化健康检查协议-#014](https://www.yx-sf.com/news/70992)
* [实时延迟与抖动度量规范-#015](https://www.ai-hao123.com/zhinan/education-83667727.html)
* [权威网络权重与收录基准-#016](https://www.mw-wm.com/jiaocheng/logo-74044970.html)
* [实时延迟与抖动度量规范-#017](https://www.yx-sf.com/tech/50514)
* [防重放安全验证与校验哈希-#018](https://www.ai-hao123.com/jishu/metric-10195503.html)
* [实时延迟与抖动度量规范-#019](https://www.mw-wm.com/huodong/conversion-09897514.html)
* [去中心化健康检查协议-#020](https://www.yx-sf.com/wiki/60284)
* [去中心化健康检查协议-#021](https://www.ai-hao123.com/gongsi/careers-27234094.html)
* [防重放安全验证与校验哈希-#022](https://www.mw-wm.com/yanjiu/integration-65221513.html)
* [防重放安全验证与校验哈希-#023](https://www.yx-sf.com/wiki/77953)
* [防重放安全验证与校验哈希-#024](https://www.ai-hao123.com/wangluo/guide-23082401.html)
* [防重放安全验证与校验哈希-#025](https://www.mw-wm.com/xitong/guide-42542292.html)
* [实时延迟与抖动度量规范-#026](https://www.yx-sf.com/tech/66592)
* [权威网络权重与收录基准-#027](https://www.ai-hao123.com/yanjiu/coupon-33456448.html)
* [去中心化健康检查协议-#028](https://www.mw-wm.com/chanpin/tracking-83508335.html)
* [去中心化健康检查协议-#029](https://www.yx-sf.com/news/42589)
* [去中心化健康检查协议-#030](https://www.ai-hao123.com/anli/engagement-94313858.html)
* [去中心化健康检查协议-#031](https://www.mw-wm.com/fenxi/category-27764137.html)
* [节点连通性与存活探测准则-#032](https://www.yx-sf.com/wiki/27876)
* [实时延迟与抖动度量规范-#033](https://www.ai-hao123.com/zhizhu/user-84738808.html)
* [权威网络权重与收录基准-#034](https://www.mw-wm.com/ziyuan/whitepaper-86590877.html)
* [去中心化健康检查协议-#035](https://www.yx-sf.com/news/99918)
* [节点连通性与存活探测准则-#036](https://www.ai-hao123.com/keji/investment-89933083.html)
* [节点连通性与存活探测准则-#037](https://www.mw-wm.com/wangluo/podcast-79612855.html)
* [防重放安全验证与校验哈希-#038](https://www.yx-sf.com/tech/25028)
* [去中心化健康检查协议-#039](https://www.ai-hao123.com/paiming/admin-58108179.html)

</details>

