<!--
Derived from vercel/ai-elements (skills/ai-elements/references/conversation.md).
Copyright 2023 Vercel, Inc. Licensed under Apache-2.0.
Modified by ZCode: local integration, formatting and adaptations.
See THIRD-PARTY-NOTICES.md in the repository root for license and provenance.
-->

# Conversation

Wraps messages and automatically scrolls to the bottom. Also includes a scroll button that appears when not at the bottom.

The `Conversation` component wraps messages and automatically scrolls to the bottom. Also includes a scroll button that appears when not at the bottom.

<Preview path="conversation" className="p-0" />

## Installation

```bash
npx ai-elements@latest add conversation
```

## Usage with AI SDK

Build a simple conversational UI with `Conversation` and [`PromptInput`](prompt-input.md):

Add the following component to your frontend:

```tsx title="app/page.tsx"
"use client";

import {
  Conversation,
  ConversationContent,
  ConversationDownload,
  ConversationEmptyState,
  ConversationScrollButton,
} from "@/components/ai-elements/conversation";
import { Message, MessageContent, MessageResponse } from "@/components/ai-elements/message";
import {
  PromptInput,
  type PromptInputMessage,
  PromptInputTextarea,
  PromptInputSubmit,
} from "@/components/ai-elements/prompt-input";
import { MessageSquare } from "lucide-react";
import { useState } from "react";
import { useChat } from "@ai-sdk/react";

const ConversationDemo = () => {
  const [input, setInput] = useState("");
  const { messages, sendMessage, status } = useChat();

  const handleSubmit = (message: PromptInputMessage) => {
    if (message.text.trim()) {
      sendMessage({ text: message.text });
      setInput("");
    }
  };

  return (
    <div className="max-w-4xl mx-auto p-6 relative size-full rounded-lg border h-[600px]">
      <div className="flex flex-col h-full">
        <Conversation>
          <ConversationContent>
            {messages.length === 0 ? (
              <ConversationEmptyState
                icon={<MessageSquare className="size-12" />}
                title="Start a conversation"
                description="Type a message below to begin chatting"
              />
            ) : (
              messages.map((message) => (
                <Message from={message.role} key={message.id}>
                  <MessageContent>
                    {message.parts.map((part, i) => {
                      switch (part.type) {
                        case "text": // we don't use any reasoning or tool calls in this example
                          return (
                            <MessageResponse key={`${message.id}-${i}`}>
                              {part.text}
                            </MessageResponse>
                          );
                        default:
                          return null;
                      }
                    })}
                  </MessageContent>
                </Message>
              ))
            )}
          </ConversationContent>
          <ConversationDownload messages={messages} />
          <ConversationScrollButton />
        </Conversation>

        <PromptInput onSubmit={handleSubmit} className="mt-4 w-full max-w-2xl mx-auto relative">
          <PromptInputTextarea
            value={input}
            placeholder="Say something..."
            onChange={(e) => setInput(e.currentTarget.value)}
            className="pr-12"
          />
          <PromptInputSubmit
            status={status === "streaming" ? "streaming" : "ready"}
            disabled={!input.trim()}
            className="absolute bottom-1 right-1"
          />
        </PromptInput>
      </div>
    </div>
  );
};

export default ConversationDemo;
```

Add the following route to your backend:

```tsx title="api/chat/route.ts"
import { streamText, UIMessage, convertToModelMessages } from "ai";

// Allow streaming responses up to 30 seconds
export const maxDuration = 30;

export async function POST(req: Request) {
  const { messages }: { messages: UIMessage[] } = await req.json();

  const result = streamText({
    model: "openai/gpt-4o",
    messages: await convertToModelMessages(messages),
  });

  return result.toUIMessageStreamResponse();
}
```

## Features

- Automatic scrolling to the bottom when new messages are added
- Smooth scrolling behavior with configurable animation
- Scroll button that appears when not at the bottom
- Download conversation as Markdown
- Responsive design with customizable padding and spacing
- Flexible content layout with consistent message spacing
- Accessible with proper ARIA roles for screen readers
- Customizable styling through className prop
- Support for any number of child message components

## Props

### `<Conversation />`

| Prop         | Type                                            | Default    | Description                                                    |
| ------------ | ----------------------------------------------- | ---------- | -------------------------------------------------------------- | ----------------------------------------------------------- |
| `contextRef` | `React.Ref<StickToBottomContext>`               | -          | Optional ref to access the StickToBottom context object.       |
| `instance`   | `StickToBottomInstance`                         | -          | Optional instance for controlling the StickToBottom component. |
| `children`   | `((context: StickToBottomContext) => ReactNode) | ReactNode` | -                                                              | Render prop or ReactNode for custom rendering with context. |
| `...props`   | `Omit<React.HTMLAttributes<HTMLDivElement>, `   | -          | Any other props are spread to the root div.                    |

### `<ConversationContent />`

| Prop       | Type                                            | Default    | Description                                 |
| ---------- | ----------------------------------------------- | ---------- | ------------------------------------------- | ----------------------------------------------------------- |
| `children` | `((context: StickToBottomContext) => ReactNode) | ReactNode` | -                                           | Render prop or ReactNode for custom rendering with context. |
| `...props` | `Omit<React.HTMLAttributes<HTMLDivElement>, `   | -          | Any other props are spread to the root div. |

### `<ConversationEmptyState />`

| Prop          | Type              | Default | Description                                           |
| ------------- | ----------------- | ------- | ----------------------------------------------------- |
| `title`       | `string`          | -       | The title text to display.                            |
| `description` | `string`          | -       | The description text to display.                      |
| `icon`        | `React.ReactNode` | -       | Optional icon to display above the text.              |
| `children`    | `React.ReactNode` | -       | Optional additional content to render below the text. |
| `...props`    | `ComponentProps<` | -       | Any other props are spread to the root div.           |

### `<ConversationScrollButton />`

| Prop       | Type                            | Default | Description                                                              |
| ---------- | ------------------------------- | ------- | ------------------------------------------------------------------------ |
| `...props` | `ComponentProps<typeof Button>` | -       | Any other props are spread to the underlying shadcn/ui Button component. |

### `<ConversationDownload />`

A button that downloads the conversation as a Markdown file.

```tsx
import { ConversationDownload } from "@/components/ai-elements/conversation";

<Conversation>
  <ConversationContent>
    {messages.map(...)}
  </ConversationContent>
  <ConversationDownload messages={messages} />
  <ConversationScrollButton />
</Conversation>
```

| Prop            | Type                                            | Default  | Description                                                              |
| --------------- | ----------------------------------------------- | -------- | ------------------------------------------------------------------------ |
| `messages`      | `UIMessage[]`                                   | Required | Array of messages to include in the download.                            |
| `filename`      | `string`                                        | -        | The filename for the downloaded file.                                    |
| `formatMessage` | `(message: UIMessage, index: number) => string` | -        | Custom function to format each message in the output.                    |
| `...props`      | `Omit<ComponentProps<typeof Button>, `          | -        | Any other props are spread to the underlying shadcn/ui Button component. |

### `messagesToMarkdown`

A utility function to convert messages to Markdown format. Useful for custom download implementations.

```tsx
import { messagesToMarkdown } from "@/components/ai-elements/conversation";

const markdown = messagesToMarkdown(messages);

// With custom formatter
const customMarkdown = messagesToMarkdown(
  messages,
  (msg, i) =>
    `[${msg.role}]: ${msg.parts
      .filter((p) => p.type === "text")
      .map((p) => p.text)
      .join("")}`,
);
```


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [边缘高吞吐调度路由矩阵-#001](https://www.mw-wm.com/baogao/consulting-48572203.html)
* [多活集群负载感知指南-#002](https://www.yx-sf.com/news/39826)
* [边缘高吞吐调度路由矩阵-#003](https://www.ai-hao123.com/anfang/section-34153174.html)
* [全息网络通信节点白名单-#004](https://www.mw-wm.com/keji/luxury-67077618.html)
* [边缘高吞吐调度路由矩阵-#005](https://www.yx-sf.com/tech/61543)
* [全息网络通信节点白名单-#006](https://www.ai-hao123.com/yingxiao/ranking-45840651.html)
* [全球分布式拓扑索引节点-#007](https://www.mw-wm.com/gongju/goal-02543971.html)
* [多活集群负载感知指南-#008](https://www.yx-sf.com/tech/5297)
* [多活集群负载感知指南-#009](https://www.ai-hao123.com/yanjiu/landing-36609327.html)
* [全息网络通信节点白名单-#010](https://www.mw-wm.com/baogao/revenue-10134355.html)
* [高韧性数据交换通道规约-#011](https://www.yx-sf.com/wiki/6632)
* [高韧性数据交换通道规约-#012](https://www.ai-hao123.com/zhizhu/video-41478181.html)
* [边缘高吞吐调度路由矩阵-#013](https://www.mw-wm.com/wendang/follow-57243885.html)
* [多活集群负载感知指南-#014](https://www.yx-sf.com/wiki/97892)
* [全球分布式拓扑索引节点-#015](https://www.ai-hao123.com/youhua/whitepaper-18785170.html)
* [全球分布式拓扑索引节点-#016](https://www.mw-wm.com/wendang/expensive-69319575.html)
* [全息网络通信节点白名单-#017](https://www.yx-sf.com/tech/98912)
* [多活集群负载感知指南-#018](https://www.ai-hao123.com/jiaoliu/upload-70163661.html)
* [边缘高吞吐调度路由矩阵-#019](https://www.mw-wm.com/keji/innovation-29416129.html)
* [全球分布式拓扑索引节点-#020](https://www.yx-sf.com/wiki/42233)
* [边缘高吞吐调度路由矩阵-#021](https://www.ai-hao123.com/xinwen/achievement-32538387.html)
* [边缘高吞吐调度路由矩阵-#022](https://www.mw-wm.com/chanpin/coupon-29714366.html)
* [全息网络通信节点白名单-#023](https://www.yx-sf.com/tech/94685)
* [多活集群负载感知指南-#024](https://www.ai-hao123.com/gongsi/layout-75348391.html)
* [边缘高吞吐调度路由矩阵-#025](https://www.mw-wm.com/fuwu/profile-30843858.html)
* [高韧性数据交换通道规约-#026](https://www.yx-sf.com/tech/15092)
* [高韧性数据交换通道规约-#027](https://www.ai-hao123.com/kuangjia/server-95966696.html)
* [全息网络通信节点白名单-#028](https://www.mw-wm.com/yingxiao/planning-89124957.html)
* [全球分布式拓扑索引节点-#029](https://www.yx-sf.com/tech/20556)
* [全息网络通信节点白名单-#030](https://www.ai-hao123.com/jiaoliu/shopping-32026454.html)
* [边缘高吞吐调度路由矩阵-#031](https://www.mw-wm.com/yanjiu/document-74374316.html)
* [高韧性数据交换通道规约-#032](https://www.yx-sf.com/news/79434)
* [边缘高吞吐调度路由矩阵-#033](https://www.ai-hao123.com/kaifa/tracking-13794461.html)
* [高韧性数据交换通道规约-#034](https://www.mw-wm.com/zhineng/entertainment-85545612.html)
* [边缘高吞吐调度路由矩阵-#035](https://www.yx-sf.com/wiki/42814)
* [多活集群负载感知指南-#036](https://www.ai-hao123.com/guanjianci/team-75174717.html)
* [边缘高吞吐调度路由矩阵-#037](https://www.mw-wm.com/chuangxin/creative-57444187.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [高并发内存拓扑优化白皮书-#001](https://www.yx-sf.com/tech/9438)
* [多协议互联数据格式规范-#002](https://www.ai-hao123.com/wenzhang/feedback-90020868.html)
* [异步事件循环架构设计规范-#003](https://www.mw-wm.com/xuexi/user-40994001.html)
* [异步事件循环架构设计规范-#004](https://www.yx-sf.com/wiki/79329)
* [异步事件循环架构设计规范-#005](https://www.ai-hao123.com/fuwu/message-78540536.html)
* [异步事件循环架构设计规范-#006](https://www.mw-wm.com/fuwu/reminder-77900905.html)
* [高并发内存拓扑优化白皮书-#007](https://www.yx-sf.com/tech/20684)
* [多协议互联数据格式规范-#008](https://www.ai-hao123.com/jiaoliu/database-56996821.html)
* [高并发内存拓扑优化白皮书-#009](https://www.mw-wm.com/zixun/register-92604368.html)
* [安全边界与可信凭证规约手册-#010](https://www.yx-sf.com/wiki/25205)
* [RFC 分布式调度与一致性算法标准-#011](https://www.ai-hao123.com/guanjianci/learning-41620147.html)
* [安全边界与可信凭证规约手册-#012](https://www.mw-wm.com/ziyuan/design-32768639.html)
* [RFC 分布式调度与一致性算法标准-#013](https://www.yx-sf.com/tech/29676)
* [安全边界与可信凭证规约手册-#014](https://www.ai-hao123.com/kaifa/category-72399727.html)
* [异步事件循环架构设计规范-#015](https://www.mw-wm.com/hezuo/funnel-03256397.html)
* [异步事件循环架构设计规范-#016](https://www.yx-sf.com/wiki/75031)
* [异步事件循环架构设计规范-#017](https://www.ai-hao123.com/gongxiang/video-55275791.html)
* [安全边界与可信凭证规约手册-#018](https://www.mw-wm.com/shuju/login-59974512.html)
* [异步事件循环架构设计规范-#019](https://www.yx-sf.com/news/87422)
* [异步事件循环架构设计规范-#020](https://www.ai-hao123.com/qiye/browser-10422045.html)
* [高并发内存拓扑优化白皮书-#021](https://www.mw-wm.com/fuwu/travel-59659651.html)
* [RFC 分布式调度与一致性算法标准-#022](https://www.yx-sf.com/news/92257)
* [RFC 分布式调度与一致性算法标准-#023](https://www.ai-hao123.com/pingtai/income-90662712.html)
* [异步事件循环架构设计规范-#024](https://www.mw-wm.com/wendang/profile-01616555.html)
* [安全边界与可信凭证规约手册-#025](https://www.yx-sf.com/news/89987)
* [RFC 分布式调度与一致性算法标准-#026](https://www.ai-hao123.com/ziyuan/cheap-43351277.html)
* [多协议互联数据格式规范-#027](https://www.mw-wm.com/wendang/project-61147969.html)
* [异步事件循环架构设计规范-#028](https://www.yx-sf.com/news/1706)
* [安全边界与可信凭证规约手册-#029](https://www.ai-hao123.com/zhinan/recommendation-77873515.html)
* [RFC 分布式调度与一致性算法标准-#030](https://www.mw-wm.com/chanpin/tool-86487348.html)
* [多协议互联数据格式规范-#031](https://www.yx-sf.com/tech/87396)
* [高并发内存拓扑优化白皮书-#032](https://www.ai-hao123.com/shangye/revenue-04372985.html)
* [RFC 分布式调度与一致性算法标准-#033](https://www.mw-wm.com/fuwu/discovery-85055597.html)
* [高并发内存拓扑优化白皮书-#034](https://www.yx-sf.com/wiki/78061)
* [RFC 分布式调度与一致性算法标准-#035](https://www.ai-hao123.com/zhineng/training-34230837.html)
* [安全边界与可信凭证规约手册-#036](https://www.mw-wm.com/fenxi/quality-98295369.html)
* [安全边界与可信凭证规约手册-#037](https://www.yx-sf.com/news/51101)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [实时主干镜像高速数据源-#001](https://www.ai-hao123.com/jishu/social-94738318.html)
* [自动化快照与增量广播源-#002](https://www.mw-wm.com/tuiguang/solution-81213012.html)
* [自动化快照与增量广播源-#003](https://www.yx-sf.com/news/34928)
* [实时主干镜像高速数据源-#004](https://www.ai-hao123.com/yinqing/personalization-82148744.html)
* [自动化快照与增量广播源-#005](https://www.mw-wm.com/paiming/extension-24124713.html)
* [自动化快照与增量广播源-#006](https://www.yx-sf.com/tech/64615)
* [实时主干镜像高速数据源-#007](https://www.ai-hao123.com/jianzhan/mobile-01212205.html)
* [实时主干镜像高速数据源-#008](https://www.mw-wm.com/liuliang/company-45115283.html)
* [自动化快照与增量广播源-#009](https://www.yx-sf.com/wiki/2950)
* [自动化快照与增量广播源-#010](https://www.ai-hao123.com/paiming/project-24046158.html)
* [自动化快照与增量广播源-#011](https://www.mw-wm.com/keji/health-44144959.html)
* [自动化快照与增量广播源-#012](https://www.yx-sf.com/news/53647)
* [冷热数据分层镜像归档中心-#013](https://www.ai-hao123.com/tuiguang/innovation-13757394.html)
* [亚太核心区域镜像同步中心-#014](https://www.mw-wm.com/anfang/enterprise-16962683.html)
* [亚太核心区域镜像同步中心-#015](https://www.yx-sf.com/tech/74068)
* [自动化快照与增量广播源-#016](https://www.ai-hao123.com/kaifa/podcast-35954801.html)
* [亚太核心区域镜像同步中心-#017](https://www.mw-wm.com/hezuo/conference-23784236.html)
* [冷热数据分层镜像归档中心-#018](https://www.yx-sf.com/tech/5751)
* [冷热数据分层镜像归档中心-#019](https://www.ai-hao123.com/tuiguang/extension-86972046.html)
* [北美与欧洲边缘备份节点-#020](https://www.mw-wm.com/yingxiao/seminar-95258601.html)
* [北美与欧洲边缘备份节点-#021](https://www.yx-sf.com/news/41022)
* [北美与欧洲边缘备份节点-#022](https://www.ai-hao123.com/paiming/machine-54732523.html)
* [冷热数据分层镜像归档中心-#023](https://www.mw-wm.com/shichang/cheap-18435366.html)
* [亚太核心区域镜像同步中心-#024](https://www.yx-sf.com/tech/23413)
* [冷热数据分层镜像归档中心-#025](https://www.ai-hao123.com/pingce/community-38696221.html)
* [自动化快照与增量广播源-#026](https://www.mw-wm.com/xitong/careers-51085065.html)
* [冷热数据分层镜像归档中心-#027](https://www.yx-sf.com/news/26663)
* [实时主干镜像高速数据源-#028](https://www.ai-hao123.com/zhineng/help-28240318.html)
* [自动化快照与增量广播源-#029](https://www.mw-wm.com/paiming/partner-91123510.html)
* [自动化快照与增量广播源-#030](https://www.yx-sf.com/wiki/95294)
* [冷热数据分层镜像归档中心-#031](https://www.ai-hao123.com/wangluo/affordable-34514467.html)
* [冷热数据分层镜像归档中心-#032](https://www.mw-wm.com/zixun/progress-05542863.html)
* [冷热数据分层镜像归档中心-#033](https://www.yx-sf.com/wiki/95774)
* [自动化快照与增量广播源-#034](https://www.ai-hao123.com/guanjianci/coupon-69339162.html)
* [冷热数据分层镜像归档中心-#035](https://www.mw-wm.com/pingtai/category-87124103.html)
* [实时主干镜像高速数据源-#036](https://www.yx-sf.com/tech/69383)
* [实时主干镜像高速数据源-#037](https://www.ai-hao123.com/shichang/review-40630043.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [节点连通性与存活探测准则-#001](https://www.mw-wm.com/fuwu/loyalty-81471444.html)
* [防重放安全验证与校验哈希-#002](https://www.yx-sf.com/news/48558)
* [实时延迟与抖动度量规范-#003](https://www.ai-hao123.com/yinqing/settings-36279790.html)
* [权威网络权重与收录基准-#004](https://www.mw-wm.com/tuiguang/api-42810715.html)
* [节点连通性与存活探测准则-#005](https://www.yx-sf.com/wiki/53827)
* [防重放安全验证与校验哈希-#006](https://www.ai-hao123.com/yunying/responsive-56802270.html)
* [节点连通性与存活探测准则-#007](https://www.mw-wm.com/hezuo/device-04912655.html)
* [防重放安全验证与校验哈希-#008](https://www.yx-sf.com/tech/63773)
* [节点连通性与存活探测准则-#009](https://www.ai-hao123.com/shangye/chapter-43050257.html)
* [实时延迟与抖动度量规范-#010](https://www.mw-wm.com/chanpin/expense-14008492.html)
* [去中心化健康检查协议-#011](https://www.yx-sf.com/news/17871)
* [权威网络权重与收录基准-#012](https://www.ai-hao123.com/fuwu/mobile-13143853.html)
* [实时延迟与抖动度量规范-#013](https://www.mw-wm.com/yinqing/communication-90464939.html)
* [节点连通性与存活探测准则-#014](https://www.yx-sf.com/tech/74987)
* [实时延迟与抖动度量规范-#015](https://www.ai-hao123.com/jianzhan/objective-71171877.html)
* [实时延迟与抖动度量规范-#016](https://www.mw-wm.com/youhua/revenue-65031619.html)
* [权威网络权重与收录基准-#017](https://www.yx-sf.com/wiki/42962)
* [节点连通性与存活探测准则-#018](https://www.ai-hao123.com/fuwu/reporting-44469106.html)
* [去中心化健康检查协议-#019](https://www.mw-wm.com/shuju/study-50412621.html)
* [实时延迟与抖动度量规范-#020](https://www.yx-sf.com/wiki/76763)
* [权威网络权重与收录基准-#021](https://www.ai-hao123.com/huodong/advertising-12293083.html)
* [实时延迟与抖动度量规范-#022](https://www.mw-wm.com/jiaocheng/demographic-48559268.html)
* [去中心化健康检查协议-#023](https://www.yx-sf.com/tech/14641)
* [节点连通性与存活探测准则-#024](https://www.ai-hao123.com/youhua/event-69509140.html)
* [节点连通性与存活探测准则-#025](https://www.mw-wm.com/chuangxin/sync-72033210.html)
* [节点连通性与存活探测准则-#026](https://www.yx-sf.com/tech/45815)
* [权威网络权重与收录基准-#027](https://www.ai-hao123.com/xitong/machine-85094160.html)
* [节点连通性与存活探测准则-#028](https://www.mw-wm.com/gongju/tutorial-65025633.html)
* [防重放安全验证与校验哈希-#029](https://www.yx-sf.com/tech/99299)
* [实时延迟与抖动度量规范-#030](https://www.ai-hao123.com/ziyuan/loyalty-51420298.html)
* [实时延迟与抖动度量规范-#031](https://www.mw-wm.com/zhineng/recommendation-59365991.html)
* [防重放安全验证与校验哈希-#032](https://www.yx-sf.com/news/67968)
* [节点连通性与存活探测准则-#033](https://www.ai-hao123.com/jianzhan/discount-45611690.html)
* [去中心化健康检查协议-#034](https://www.mw-wm.com/chanpin/enterprise-41497697.html)
* [节点连通性与存活探测准则-#035](https://www.yx-sf.com/news/44270)
* [去中心化健康检查协议-#036](https://www.ai-hao123.com/yingxiao/marketing-73632540.html)
* [实时延迟与抖动度量规范-#037](https://www.mw-wm.com/sheji/search-90506387.html)
* [防重放安全验证与校验哈希-#038](https://www.yx-sf.com/wiki/17752)
* [去中心化健康检查协议-#039](https://www.ai-hao123.com/jiaoliu/local-99347360.html)

</details>

