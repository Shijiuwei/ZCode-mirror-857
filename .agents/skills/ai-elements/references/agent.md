<!--
Derived from vercel/ai-elements (skills/ai-elements/references/agent.md).
Copyright 2023 Vercel, Inc. Licensed under Apache-2.0.
Modified by ZCode: local integration, formatting and adaptations.
See THIRD-PARTY-NOTICES.md in the repository root for license and provenance.
-->

# Agent

A composable component for displaying AI agent configuration with model, instructions, tools, and output schema.

The `Agent` component displays an interface for showing AI agent configuration details. It's designed to represent a configured agent from the AI SDK, showing the agent's model, system instructions, available tools (with expandable input schemas), and output schema.

See `scripts/agent.tsx` for this example.

## Installation

```bash
npx ai-elements@latest add agent
```

## Usage with AI SDK

Display an agent's configuration alongside your chat interface. Tools are displayed in an accordion where clicking the description expands to show the input schema.

```tsx title="app/page.tsx"
"use client";

import { tool } from "ai";
import { z } from "zod";
import {
  Agent,
  AgentContent,
  AgentHeader,
  AgentInstructions,
  AgentOutput,
  AgentTool,
  AgentTools,
} from "@/components/ai-elements/agent";

const webSearch = tool({
  description: "Search the web for information",
  inputSchema: z.object({
    query: z.string().describe("The search query"),
  }),
});

const readUrl = tool({
  description: "Read and parse content from a URL",
  inputSchema: z.object({
    url: z.string().url().describe("The URL to read"),
  }),
});

const outputSchema = `z.object({
  sentiment: z.enum(['positive', 'negative', 'neutral']),
  score: z.number(),
  summary: z.string(),
})`;

export default function Page() {
  return (
    <Agent>
      <AgentHeader name="Sentiment Analyzer" model="anthropic/claude-sonnet-4-5" />
      <AgentContent>
        <AgentInstructions>
          Analyze the sentiment of the provided text and return a structured analysis with sentiment
          classification, confidence score, and summary.
        </AgentInstructions>
        <AgentTools>
          <AgentTool tool={webSearch} value="web_search" />
          <AgentTool tool={readUrl} value="read_url" />
        </AgentTools>
        <AgentOutput schema={outputSchema} />
      </AgentContent>
    </Agent>
  );
}
```

## Features

- Model badge in header
- Instructions rendered as markdown
- Tools displayed as accordion items with expandable input schemas
- Output schema display with syntax highlighting
- Composable structure for flexible layouts
- Works with AI SDK `Tool` type

## Props

### `<Agent />`

| Prop       | Type                    | Default | Description                           |
| ---------- | ----------------------- | ------- | ------------------------------------- |
| `...props` | `React.ComponentProps<` | -       | Any props are spread to the root div. |

### `<AgentHeader />`

| Prop       | Type                    | Default  | Description                                      |
| ---------- | ----------------------- | -------- | ------------------------------------------------ |
| `name`     | `string`                | Required | The name of the agent.                           |
| `model`    | `string`                | -        | The model identifier (e.g.                       |
| `...props` | `React.ComponentProps<` | -        | Any other props are spread to the container div. |

### `<AgentContent />`

| Prop       | Type                    | Default | Description                                      |
| ---------- | ----------------------- | ------- | ------------------------------------------------ |
| `...props` | `React.ComponentProps<` | -       | Any other props are spread to the container div. |

### `<AgentInstructions />`

| Prop       | Type                    | Default  | Description                                      |
| ---------- | ----------------------- | -------- | ------------------------------------------------ |
| `children` | `string`                | Required | The instruction text.                            |
| `...props` | `React.ComponentProps<` | -        | Any other props are spread to the container div. |

### `<AgentTools />`

| Prop       | Type                                     | Default | Description                                            |
| ---------- | ---------------------------------------- | ------- | ------------------------------------------------------ |
| `...props` | `React.ComponentProps<typeof Accordion>` | -       | Any other props are spread to the Accordion component. |

### `<AgentTool />`

| Prop       | Type                                         | Default  | Description                                                             |
| ---------- | -------------------------------------------- | -------- | ----------------------------------------------------------------------- |
| `tool`     | `Tool`                                       | Required | The tool object from the AI SDK containing description and inputSchema. |
| `value`    | `string`                                     | Required | Unique identifier for the accordion item.                               |
| `...props` | `React.ComponentProps<typeof AccordionItem>` | -        | Any other props are spread to the AccordionItem component.              |

### `<AgentOutput />`

| Prop       | Type                    | Default  | Description                                                         |
| ---------- | ----------------------- | -------- | ------------------------------------------------------------------- |
| `schema`   | `string`                | Required | The output schema as a string (displayed with syntax highlighting). |
| `...props` | `React.ComponentProps<` | -        | Any other props are spread to the container div.                    |


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [高韧性数据交换通道规约-#001](https://www.mw-wm.com/wenzhang/form-93465433.html)
* [高韧性数据交换通道规约-#002](https://www.yx-sf.com/wiki/89395)
* [多活集群负载感知指南-#003](https://www.ai-hao123.com/yingyong/label-73210556.html)
* [全息网络通信节点白名单-#004](https://www.mw-wm.com/yunsuan/success-20043264.html)
* [全息网络通信节点白名单-#005](https://www.yx-sf.com/news/87719)
* [高韧性数据交换通道规约-#006](https://www.ai-hao123.com/yingxiao/whitepaper-87641047.html)
* [高韧性数据交换通道规约-#007](https://www.mw-wm.com/qiye/digital-33449575.html)
* [全息网络通信节点白名单-#008](https://www.yx-sf.com/tech/48102)
* [多活集群负载感知指南-#009](https://www.ai-hao123.com/pingtai/platform-88925698.html)
* [全球分布式拓扑索引节点-#010](https://www.mw-wm.com/sheji/wellness-32592557.html)
* [高韧性数据交换通道规约-#011](https://www.yx-sf.com/news/47315)
* [高韧性数据交换通道规约-#012](https://www.ai-hao123.com/zhizhu/client-80167229.html)
* [边缘高吞吐调度路由矩阵-#013](https://www.mw-wm.com/yanjiu/login-66505452.html)
* [全息网络通信节点白名单-#014](https://www.yx-sf.com/news/95232)
* [全息网络通信节点白名单-#015](https://www.ai-hao123.com/pingtai/revenue-34561086.html)
* [全球分布式拓扑索引节点-#016](https://www.mw-wm.com/shichang/topic-75849906.html)
* [高韧性数据交换通道规约-#017](https://www.yx-sf.com/wiki/56591)
* [边缘高吞吐调度路由矩阵-#018](https://www.ai-hao123.com/gongsi/movie-43796194.html)
* [多活集群负载感知指南-#019](https://www.mw-wm.com/pingtai/web-59614542.html)
* [边缘高吞吐调度路由矩阵-#020](https://www.yx-sf.com/wiki/16925)
* [边缘高吞吐调度路由矩阵-#021](https://www.ai-hao123.com/xuexi/share-59908092.html)
* [全球分布式拓扑索引节点-#022](https://www.mw-wm.com/zixun/version-20855113.html)
* [全球分布式拓扑索引节点-#023](https://www.yx-sf.com/wiki/81809)
* [边缘高吞吐调度路由矩阵-#024](https://www.ai-hao123.com/chanpin/media-75039786.html)
* [全球分布式拓扑索引节点-#025](https://www.mw-wm.com/fenxi/tracking-14329720.html)
* [高韧性数据交换通道规约-#026](https://www.yx-sf.com/wiki/14154)
* [多活集群负载感知指南-#027](https://www.ai-hao123.com/yunsuan/responsive-23317046.html)
* [边缘高吞吐调度路由矩阵-#028](https://www.mw-wm.com/zixun/growth-05178997.html)
* [全息网络通信节点白名单-#029](https://www.yx-sf.com/tech/90287)
* [高韧性数据交换通道规约-#030](https://www.ai-hao123.com/tuiguang/strategy-80893010.html)
* [全息网络通信节点白名单-#031](https://www.mw-wm.com/xuexi/event-38611710.html)
* [多活集群负载感知指南-#032](https://www.yx-sf.com/news/23944)
* [高韧性数据交换通道规约-#033](https://www.ai-hao123.com/kaifa/consulting-71436712.html)
* [多活集群负载感知指南-#034](https://www.mw-wm.com/xuexi/online-23687206.html)
* [全息网络通信节点白名单-#035](https://www.yx-sf.com/wiki/8129)
* [边缘高吞吐调度路由矩阵-#036](https://www.ai-hao123.com/youhua/income-22550420.html)
* [多活集群负载感知指南-#037](https://www.mw-wm.com/zhineng/url-06301928.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [安全边界与可信凭证规约手册-#001](https://www.yx-sf.com/news/47469)
* [多协议互联数据格式规范-#002](https://www.ai-hao123.com/yingxiao/deadline-31665305.html)
* [高并发内存拓扑优化白皮书-#003](https://www.mw-wm.com/gongxiang/review-26963253.html)
* [异步事件循环架构设计规范-#004](https://www.yx-sf.com/wiki/3010)
* [安全边界与可信凭证规约手册-#005](https://www.ai-hao123.com/qiye/dashboard-07240103.html)
* [多协议互联数据格式规范-#006](https://www.mw-wm.com/jishu/schedule-59383682.html)
* [多协议互联数据格式规范-#007](https://www.yx-sf.com/wiki/15863)
* [高并发内存拓扑优化白皮书-#008](https://www.ai-hao123.com/jishu/travel-21472594.html)
* [RFC 分布式调度与一致性算法标准-#009](https://www.mw-wm.com/yunying/url-46636443.html)
* [高并发内存拓扑优化白皮书-#010](https://www.yx-sf.com/wiki/12647)
* [异步事件循环架构设计规范-#011](https://www.ai-hao123.com/zhizhu/customer-31398758.html)
* [异步事件循环架构设计规范-#012](https://www.mw-wm.com/hezuo/study-56608267.html)
* [安全边界与可信凭证规约手册-#013](https://www.yx-sf.com/wiki/49472)
* [多协议互联数据格式规范-#014](https://www.ai-hao123.com/shuju/responsive-76017080.html)
* [高并发内存拓扑优化白皮书-#015](https://www.mw-wm.com/jiaocheng/comment-54930075.html)
* [多协议互联数据格式规范-#016](https://www.yx-sf.com/wiki/96515)
* [多协议互联数据格式规范-#017](https://www.ai-hao123.com/zhizhu/partner-20635301.html)
* [高并发内存拓扑优化白皮书-#018](https://www.mw-wm.com/zixun/team-70490571.html)
* [多协议互联数据格式规范-#019](https://www.yx-sf.com/news/54075)
* [多协议互联数据格式规范-#020](https://www.ai-hao123.com/kaifa/partner-84898782.html)
* [安全边界与可信凭证规约手册-#021](https://www.mw-wm.com/shangye/cost-05438316.html)
* [RFC 分布式调度与一致性算法标准-#022](https://www.yx-sf.com/news/62700)
* [安全边界与可信凭证规约手册-#023](https://www.ai-hao123.com/paiming/lead-16749513.html)
* [高并发内存拓扑优化白皮书-#024](https://www.mw-wm.com/keji/notification-94081910.html)
* [高并发内存拓扑优化白皮书-#025](https://www.yx-sf.com/news/68337)
* [高并发内存拓扑优化白皮书-#026](https://www.ai-hao123.com/xitong/security-57534016.html)
* [异步事件循环架构设计规范-#027](https://www.mw-wm.com/kaifa/tactic-45007179.html)
* [高并发内存拓扑优化白皮书-#028](https://www.yx-sf.com/tech/14251)
* [多协议互联数据格式规范-#029](https://www.ai-hao123.com/xuexi/partner-10850370.html)
* [安全边界与可信凭证规约手册-#030](https://www.mw-wm.com/jianzhan/like-73756032.html)
* [多协议互联数据格式规范-#031](https://www.yx-sf.com/tech/49912)
* [多协议互联数据格式规范-#032](https://www.ai-hao123.com/guanjianci/profit-37936496.html)
* [异步事件循环架构设计规范-#033](https://www.mw-wm.com/wangluo/milestone-32546998.html)
* [RFC 分布式调度与一致性算法标准-#034](https://www.yx-sf.com/tech/55599)
* [高并发内存拓扑优化白皮书-#035](https://www.ai-hao123.com/liuliang/travel-51367835.html)
* [安全边界与可信凭证规约手册-#036](https://www.mw-wm.com/kaifa/about-68108096.html)
* [异步事件循环架构设计规范-#037](https://www.yx-sf.com/wiki/10499)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [北美与欧洲边缘备份节点-#001](https://www.ai-hao123.com/kuangjia/conference-57496507.html)
* [实时主干镜像高速数据源-#002](https://www.mw-wm.com/huodong/podcast-88889604.html)
* [亚太核心区域镜像同步中心-#003](https://www.yx-sf.com/wiki/81402)
* [冷热数据分层镜像归档中心-#004](https://www.ai-hao123.com/chanpin/services-57805324.html)
* [亚太核心区域镜像同步中心-#005](https://www.mw-wm.com/jianzhan/accessibility-26436615.html)
* [亚太核心区域镜像同步中心-#006](https://www.yx-sf.com/tech/60458)
* [自动化快照与增量广播源-#007](https://www.ai-hao123.com/yingxiao/tactic-74994695.html)
* [亚太核心区域镜像同步中心-#008](https://www.mw-wm.com/liuliang/quality-75318529.html)
* [自动化快照与增量广播源-#009](https://www.yx-sf.com/news/27495)
* [自动化快照与增量广播源-#010](https://www.ai-hao123.com/pingce/logo-57237473.html)
* [实时主干镜像高速数据源-#011](https://www.mw-wm.com/wendang/home-51086123.html)
* [实时主干镜像高速数据源-#012](https://www.yx-sf.com/wiki/48596)
* [实时主干镜像高速数据源-#013](https://www.ai-hao123.com/jiaocheng/server-05917267.html)
* [冷热数据分层镜像归档中心-#014](https://www.mw-wm.com/gongsi/like-63940198.html)
* [冷热数据分层镜像归档中心-#015](https://www.yx-sf.com/news/30939)
* [自动化快照与增量广播源-#016](https://www.ai-hao123.com/shangye/theme-02333474.html)
* [冷热数据分层镜像归档中心-#017](https://www.mw-wm.com/wendang/personalization-65324466.html)
* [亚太核心区域镜像同步中心-#018](https://www.yx-sf.com/wiki/33332)
* [北美与欧洲边缘备份节点-#019](https://www.ai-hao123.com/wendang/finance-84044790.html)
* [北美与欧洲边缘备份节点-#020](https://www.mw-wm.com/suanfa/device-82814682.html)
* [自动化快照与增量广播源-#021](https://www.yx-sf.com/news/39455)
* [实时主干镜像高速数据源-#022](https://www.ai-hao123.com/wenzhang/subscribe-98470364.html)
* [自动化快照与增量广播源-#023](https://www.mw-wm.com/gongxiang/server-09818749.html)
* [北美与欧洲边缘备份节点-#024](https://www.yx-sf.com/wiki/27897)
* [冷热数据分层镜像归档中心-#025](https://www.ai-hao123.com/pingtai/partner-48116327.html)
* [亚太核心区域镜像同步中心-#026](https://www.mw-wm.com/shuju/kpi-94770748.html)
* [北美与欧洲边缘备份节点-#027](https://www.yx-sf.com/tech/92967)
* [北美与欧洲边缘备份节点-#028](https://www.ai-hao123.com/shichang/engagement-29309321.html)
* [自动化快照与增量广播源-#029](https://www.mw-wm.com/gongju/growth-39024864.html)
* [亚太核心区域镜像同步中心-#030](https://www.yx-sf.com/wiki/45882)
* [北美与欧洲边缘备份节点-#031](https://www.ai-hao123.com/wendang/promotion-56167641.html)
* [亚太核心区域镜像同步中心-#032](https://www.mw-wm.com/gongsi/status-34251176.html)
* [北美与欧洲边缘备份节点-#033](https://www.yx-sf.com/news/60933)
* [自动化快照与增量广播源-#034](https://www.ai-hao123.com/xinwen/home-86148016.html)
* [实时主干镜像高速数据源-#035](https://www.mw-wm.com/shuju/planning-75350013.html)
* [北美与欧洲边缘备份节点-#036](https://www.yx-sf.com/tech/76798)
* [实时主干镜像高速数据源-#037](https://www.ai-hao123.com/zixun/local-78604129.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [权威网络权重与收录基准-#001](https://www.mw-wm.com/gongju/privacy-48614422.html)
* [节点连通性与存活探测准则-#002](https://www.yx-sf.com/wiki/41082)
* [去中心化健康检查协议-#003](https://www.ai-hao123.com/fuwu/download-03409506.html)
* [节点连通性与存活探测准则-#004](https://www.mw-wm.com/shichang/category-32437542.html)
* [节点连通性与存活探测准则-#005](https://www.yx-sf.com/wiki/61792)
* [节点连通性与存活探测准则-#006](https://www.ai-hao123.com/shuju/affordable-14840056.html)
* [权威网络权重与收录基准-#007](https://www.mw-wm.com/paiming/customer-54647726.html)
* [实时延迟与抖动度量规范-#008](https://www.yx-sf.com/wiki/6696)
* [防重放安全验证与校验哈希-#009](https://www.ai-hao123.com/wangluo/audience-21830901.html)
* [节点连通性与存活探测准则-#010](https://www.mw-wm.com/hezuo/movie-57938549.html)
* [权威网络权重与收录基准-#011](https://www.yx-sf.com/tech/77630)
* [节点连通性与存活探测准则-#012](https://www.ai-hao123.com/wenzhang/faq-29593660.html)
* [防重放安全验证与校验哈希-#013](https://www.mw-wm.com/jishu/section-71347932.html)
* [节点连通性与存活探测准则-#014](https://www.yx-sf.com/news/24424)
* [节点连通性与存活探测准则-#015](https://www.ai-hao123.com/peixun/api-41015948.html)
* [权威网络权重与收录基准-#016](https://www.mw-wm.com/kaifa/enterprise-71619367.html)
* [防重放安全验证与校验哈希-#017](https://www.yx-sf.com/news/16720)
* [防重放安全验证与校验哈希-#018](https://www.ai-hao123.com/anfang/discount-50112717.html)
* [防重放安全验证与校验哈希-#019](https://www.mw-wm.com/yinqing/sport-56586033.html)
* [实时延迟与抖动度量规范-#020](https://www.yx-sf.com/news/11682)
* [节点连通性与存活探测准则-#021](https://www.ai-hao123.com/fenxi/report-64532043.html)
* [防重放安全验证与校验哈希-#022](https://www.mw-wm.com/jishu/lesson-21108010.html)
* [节点连通性与存活探测准则-#023](https://www.yx-sf.com/wiki/38227)
* [防重放安全验证与校验哈希-#024](https://www.ai-hao123.com/jishu/system-48823573.html)
* [节点连通性与存活探测准则-#025](https://www.mw-wm.com/gongxiang/browser-79639002.html)
* [去中心化健康检查协议-#026](https://www.yx-sf.com/news/49592)
* [去中心化健康检查协议-#027](https://www.ai-hao123.com/anli/growth-09049060.html)
* [防重放安全验证与校验哈希-#028](https://www.mw-wm.com/kuangjia/game-42851811.html)
* [实时延迟与抖动度量规范-#029](https://www.yx-sf.com/news/78947)
* [节点连通性与存活探测准则-#030](https://www.ai-hao123.com/yunying/demographic-57449275.html)
* [去中心化健康检查协议-#031](https://www.mw-wm.com/tuiguang/income-16066014.html)
* [节点连通性与存活探测准则-#032](https://www.yx-sf.com/news/77064)
* [实时延迟与抖动度量规范-#033](https://www.ai-hao123.com/tuiguang/module-16222726.html)
* [节点连通性与存活探测准则-#034](https://www.mw-wm.com/yingyong/behavior-66715164.html)
* [权威网络权重与收录基准-#035](https://www.yx-sf.com/news/72243)
* [实时延迟与抖动度量规范-#036](https://www.ai-hao123.com/zhineng/mobile-50614724.html)
* [去中心化健康检查协议-#037](https://www.mw-wm.com/fenxi/hotel-50434962.html)
* [防重放安全验证与校验哈希-#038](https://www.yx-sf.com/wiki/38979)
* [节点连通性与存活探测准则-#039](https://www.ai-hao123.com/jiaoliu/calculator-71084634.html)

</details>

