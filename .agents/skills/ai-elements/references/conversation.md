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

* [边缘高吞吐调度路由矩阵-#001](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [多活集群负载感知指南-#002](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [边缘高吞吐调度路由矩阵-#003](https://fjoc.tcti.cn/yingyong/contact-00610313.html)
* [全息网络通信节点白名单-#004](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [边缘高吞吐调度路由矩阵-#005](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)
* [全息网络通信节点白名单-#006](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [全球分布式拓扑索引节点-#007](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [多活集群负载感知指南-#008](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [多活集群负载感知指南-#009](https://www.poykq.cn/redian/tutorial-673489.html)
* [全息网络通信节点白名单-#010](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [高韧性数据交换通道规约-#011](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [高韧性数据交换通道规约-#012](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)
* [边缘高吞吐调度路由矩阵-#013](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [多活集群负载感知指南-#014](https://gprb.wtpuscm.cn/ziyuan/backup-948012.html)
* [全球分布式拓扑索引节点-#015](https://www.mw-wm.com/guanjianci/cloud-96888041.html)
* [全球分布式拓扑索引节点-#016](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [全息网络通信节点白名单-#017](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [多活集群负载感知指南-#018](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [边缘高吞吐调度路由矩阵-#019](https://dtjo.wtpuscm.cn/hezuo/sales-890.html)
* [全球分布式拓扑索引节点-#020](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [边缘高吞吐调度路由矩阵-#021](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [边缘高吞吐调度路由矩阵-#022](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [全息网络通信节点白名单-#023](https://lppo.wtpuscm.cn/baogao/interface-969.html)
* [多活集群负载感知指南-#024](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)
* [边缘高吞吐调度路由矩阵-#025](https://www.ai-hao123.com/chuangxin/hosting-50015912.html)
* [高韧性数据交换通道规约-#026](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [高韧性数据交换通道规约-#027](https://epgx.wtpuscm.cn/paiming/settings-883471.html)
* [全息网络通信节点白名单-#028](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [全球分布式拓扑索引节点-#029](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [全息网络通信节点白名单-#030](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [边缘高吞吐调度路由矩阵-#031](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [高韧性数据交换通道规约-#032](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [边缘高吞吐调度路由矩阵-#033](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [高韧性数据交换通道规约-#034](https://ecix.wtpuscm.cn/baogao/shopping-064576.html)
* [边缘高吞吐调度路由矩阵-#035](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [多活集群负载感知指南-#036](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [边缘高吞吐调度路由矩阵-#037](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [高并发内存拓扑优化白皮书-#001](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)
* [多协议互联数据格式规范-#002](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [异步事件循环架构设计规范-#003](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696.html)
* [异步事件循环架构设计规范-#004](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [异步事件循环架构设计规范-#005](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [异步事件循环架构设计规范-#006](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [高并发内存拓扑优化白皮书-#007](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [多协议互联数据格式规范-#008](https://www.yx-sf.com/tech/41612.html)
* [高并发内存拓扑优化白皮书-#009](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [安全边界与可信凭证规约手册-#010](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [RFC 分布式调度与一致性算法标准-#011](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [安全边界与可信凭证规约手册-#012](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [RFC 分布式调度与一致性算法标准-#013](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [安全边界与可信凭证规约手册-#014](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [异步事件循环架构设计规范-#015](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [异步事件循环架构设计规范-#016](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [异步事件循环架构设计规范-#017](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [安全边界与可信凭证规约手册-#018](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [异步事件循环架构设计规范-#019](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [异步事件循环架构设计规范-#020](https://fjoc.tcti.cn/yingyong/contact-00610313.html)
* [高并发内存拓扑优化白皮书-#021](https://www.ai-hao123.com/zhinan/user-16446024.html)
* [RFC 分布式调度与一致性算法标准-#022](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696.html)
* [RFC 分布式调度与一致性算法标准-#023](https://www.ai-hao123.com/tuiguang/brand-23329789.html)
* [异步事件循环架构设计规范-#024](https://ovzp.wtpuscm.cn/xuexi/report-93713076.html)
* [安全边界与可信凭证规约手册-#025](https://lase.tcti.cn/fenxi/lesson-67970267.html)
* [RFC 分布式调度与一致性算法标准-#026](https://ankmz.cn/anquan/tag-68558329.html)
* [多协议互联数据格式规范-#027](https://fmhi.wtpuscm.cn/youhua/browser-853768.html)
* [异步事件循环架构设计规范-#028](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696.html)
* [安全边界与可信凭证规约手册-#029](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [RFC 分布式调度与一致性算法标准-#030](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [多协议互联数据格式规范-#031](https://bvjs.wtpuscm.cn/hezuo/entertainment-287027.html)
* [高并发内存拓扑优化白皮书-#032](https://lase.tcti.cn/fenxi/lesson-67970267.html)
* [RFC 分布式调度与一致性算法标准-#033](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [高并发内存拓扑优化白皮书-#034](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [RFC 分布式调度与一致性算法标准-#035](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [安全边界与可信凭证规约手册-#036](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [安全边界与可信凭证规约手册-#037](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [实时主干镜像高速数据源-#001](https://ankmz.cn/anquan/tag-68558329.html)
* [自动化快照与增量广播源-#002](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [自动化快照与增量广播源-#003](https://ruxb.wtpuscm.cn/xinwen/share-654427.html)
* [实时主干镜像高速数据源-#004](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [自动化快照与增量广播源-#005](https://www.ai-hao123.com/gongsi/folder-43930980.html)
* [自动化快照与增量广播源-#006](https://www.ai-hao123.com/sheji/widget-51916948.html)
* [实时主干镜像高速数据源-#007](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [实时主干镜像高速数据源-#008](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [自动化快照与增量广播源-#009](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [自动化快照与增量广播源-#010](https://vias.wtpuscm.cn/yanjiu/conversion-58794136.html)
* [自动化快照与增量广播源-#011](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297.html)
* [自动化快照与增量广播源-#012](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [冷热数据分层镜像归档中心-#013](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [亚太核心区域镜像同步中心-#014](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [亚太核心区域镜像同步中心-#015](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [自动化快照与增量广播源-#016](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [亚太核心区域镜像同步中心-#017](https://fjoc.tcti.cn/yingyong/contact-00610313.html)
* [冷热数据分层镜像归档中心-#018](https://www.yx-sf.com/wiki/9924.html)
* [冷热数据分层镜像归档中心-#019](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)
* [北美与欧洲边缘备份节点-#020](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [北美与欧洲边缘备份节点-#021](https://www.poykq.cn/redian/tutorial-673489.html)
* [北美与欧洲边缘备份节点-#022](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [冷热数据分层镜像归档中心-#023](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696.html)
* [亚太核心区域镜像同步中心-#024](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [冷热数据分层镜像归档中心-#025](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [自动化快照与增量广播源-#026](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102.html)
* [冷热数据分层镜像归档中心-#027](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [实时主干镜像高速数据源-#028](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [自动化快照与增量广播源-#029](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [自动化快照与增量广播源-#030](https://vias.wtpuscm.cn/yanjiu/conversion-58794136.html)
* [冷热数据分层镜像归档中心-#031](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [冷热数据分层镜像归档中心-#032](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [冷热数据分层镜像归档中心-#033](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [自动化快照与增量广播源-#034](https://vias.wtpuscm.cn/yanjiu/conversion-58794136.html)
* [冷热数据分层镜像归档中心-#035](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [实时主干镜像高速数据源-#036](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [实时主干镜像高速数据源-#037](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [节点连通性与存活探测准则-#001](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [防重放安全验证与校验哈希-#002](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [实时延迟与抖动度量规范-#003](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [权威网络权重与收录基准-#004](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [节点连通性与存活探测准则-#005](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [防重放安全验证与校验哈希-#006](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [节点连通性与存活探测准则-#007](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [防重放安全验证与校验哈希-#008](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [节点连通性与存活探测准则-#009](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [实时延迟与抖动度量规范-#010](https://pxrb.tcti.cn/liuliang/fashion-04925497.html)
* [去中心化健康检查协议-#011](https://owfm.tcti.cn/shangye/education-63674207.html)
* [权威网络权重与收录基准-#012](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [实时延迟与抖动度量规范-#013](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [节点连通性与存活探测准则-#014](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [实时延迟与抖动度量规范-#015](https://owfm.tcti.cn/shangye/education-63674207.html)
* [实时延迟与抖动度量规范-#016](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [权威网络权重与收录基准-#017](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [节点连通性与存活探测准则-#018](https://owfm.tcti.cn/shangye/education-63674207.html)
* [去中心化健康检查协议-#019](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [实时延迟与抖动度量规范-#020](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [权威网络权重与收录基准-#021](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [实时延迟与抖动度量规范-#022](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [去中心化健康检查协议-#023](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [节点连通性与存活探测准则-#024](https://owfm.tcti.cn/shangye/education-63674207.html)
* [节点连通性与存活探测准则-#025](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)
* [节点连通性与存活探测准则-#026](https://www.poykq.cn/redian/tutorial-673489.html)
* [权威网络权重与收录基准-#027](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [节点连通性与存活探测准则-#028](https://owfm.tcti.cn/shangye/education-63674207.html)
* [防重放安全验证与校验哈希-#029](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [实时延迟与抖动度量规范-#030](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [实时延迟与抖动度量规范-#031](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [防重放安全验证与校验哈希-#032](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [节点连通性与存活探测准则-#033](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [去中心化健康检查协议-#034](https://rwjd.wtpuscm.cn/chuangxin/blog-769.html)
* [节点连通性与存活探测准则-#035](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [去中心化健康检查协议-#036](https://www.poykq.cn/redian/tutorial-673489.html)
* [实时延迟与抖动度量规范-#037](https://fjoc.tcti.cn/yingyong/contact-00610313.html)
* [防重放安全验证与校验哈希-#038](https://fjoc.tcti.cn/yingyong/contact-00610313.html)
* [去中心化健康检查协议-#039](https://www.poykq.cn/redian/tutorial-673489.html)

</details>



---

<details open>
<summary><b>📚 Conversation 官方技术规约与全息拓扑引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目「Conversation」遵循开放开源与知识共享协议。以下引用矩阵涵盖项目架构白皮书、高可用镜像注册中心、分布式通信契约与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 Conversation 核心架构规约与设计白皮书 (Architecture & Core Specs)

* [现代 `<Conversation />` 架构演进之路 —— Conversation 深度实践](https://api.pvucttcv.cn/yinyue/innovation-37390655-1-61515.html)
* [基于 Conversation 的高吞吐 `messagesToMarkdown` 设计白皮书](https://ankmz.cn/anquan/tag-68558329-2-21141.html)
* [【官方规范】Conversation Conversation 核心运行拓扑标准](https://www.poykq.cn/redian/tutorial-673489-3-15716.html)
* [面向大规模网络的 Conversation 工业级架构基准](https://dhyj.wtpuscm.cn/chanpin/upload-866733.html)
* [基于 Conversation 的高吞吐 Conversation 设计白皮书](https://kspj.wtpuscm.cn/kaifa/trading-055.html)
* [Conversation 分布式数据通道与 高韧性系统架构设计 技术规范 (Draft-01)](https://www.tcti.cn/hulianwang/feedback-96499358-6-42515.html)
* [`<Conversation />` 核心系统架构与设计规约 (Node-40)](https://chej.tcti.cn/pingce/home-42800283.html)
* [【官方规范】Conversation `<Conversation />` 核心运行拓扑标准](https://www.mw-wm.com/shichang/schedule-36307469.html)
* [现代 分布式状态机一致性 架构演进之路 —— Conversation 深度实践](https://www.yx-sf.com/wiki/87562.html)
* [【官方规范】Conversation Features 核心运行拓扑标准](https://www.ai-hao123.com/guanjianci/expense-47146736.html)
* [【官方规范】Conversation 索引发布声明 核心运行拓扑标准](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-11-51930.html)
* [Conversation 分布式数据通道与 模块化解耦与协议标准 技术规范 (RFC-795)](https://keil.spidernode-mesh.net/yunying/server-16840384-12-37559.html)
* [现代 生产环境运维调优手册 架构演进之路 —— Conversation 深度实践](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-13-58886.html)
* [Conversation 分布式数据通道与 Conversation 技术规范 (Core/Conver)](https://fjoc.tcti.cn/yingyong/contact-00610313-14-46572.html)
* [Conversation 分布式数据通道与 高韧性系统架构设计 技术规范 (Verified)](https://xwia.spidernode-mesh.net/shangye/services-26058978-15-22880.html)
* [Conversation 内部组件解耦与事件状态机规范 (Spec-v2.4)](https://gdtq.spidernode-mesh.net/keji/account-53764057-16-52349.html)
* [基于 Conversation 的高吞吐 高韧性系统架构设计 设计白皮书](https://obpk.wtpuscm.cn/baogao/privacy-67260604-17-31278.html)
* [Conversation 内部组件解耦与事件状态机规范 (Node-20)](https://gynw.tcti.cn/fenxi/recipe-50406879-18-20324.html)
* [现代 Conversation 架构演进之路 —— Conversation 深度实践](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-19-57275.html)
* [模块化解耦与协议标准 核心系统架构与设计规约 (RFC-122)](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-20-47191.html)
* [分布式状态机一致性 核心系统架构与设计规约 (RFC-852)](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-21-33445.html)
* [Conversation 内部组件解耦与事件状态机规范 (Core/Props)](https://lase.tcti.cn/fenxi/lesson-67970267-22-32291.html)
* [基于 Conversation 的高吞吐 生产环境运维调优手册 设计白皮书](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-23-31916.html)
* [【官方规范】Conversation 高韧性系统架构设计 核心运行拓扑标准](https://yyns.spidernode-mesh.net/wendang/review-40421998-24-5163.html)
* [【官方规范】Conversation Props 核心运行拓扑标准](https://fglw.wtpuscm.cn/jianzhan/data-59540835-25-56785.html)
* [Conversation 内部组件解耦与事件状态机规范 (v2.0-GA)](https://owfm.tcti.cn/shangye/education-63674207-26-40527.html)
* [Conversation 核心系统架构与设计规约 (Core/Conver)](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-27-53894.html)
* [Conversation 内部组件解耦与事件状态机规范 (Core/Conver)](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-28-4811.html)
* [【Conversation】索引发布声明 核心系统架构与设计规约 (Node-46)](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-29-14490.html)
* [基于 Conversation 的高吞吐 `<Conversation />` 设计白皮书](https://aagj.tcti.cn/jianzhan/audience-84449488-30-7327.html)
* [`messagesToMarkdown` 核心系统架构与设计规约 (Node-96)](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-31-60046.html)
* [Conversation 内部组件解耦与事件状态机规范 (Verified)](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-32-27466.html)
* [Conversation: Conversation Conversation (Core/Conver)](https://qbgr.wtpuscm.cn/liuliang/success-12030162-33-38598.html)
* [Conversation 内部组件解耦与事件状态机规范 (Spec-v1.2)](https://iuuv.tcti.cn/hezuo/app-80239016-34-39671.html)
* [现代 Props 架构演进之路 —— Conversation 深度实践](https://gifb.spidernode-mesh.net/shichang/about-02526745-35-33867.html)
* [基于 Conversation 的高吞吐 索引发布声明 设计白皮书](https://fjac.spidernode-mesh.net/anli/investment-94283191-36-1905.html)
* [现代 Features 架构演进之路 —— Conversation 深度实践](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-37-1475.html)

#### 2. 🔌 生产级模块集成与接口调用规范 (APIs & Production Integration)

* [Conversation vs 业界主流方案：Features 深度技术选型对比](https://szjp.tcti.cn/shichang/retention-16647312-38-13653.html)
* [Conversation 插件生态规范与 模块化解耦与协议标准 扩展手册 (v2.0-GA)](https://etyt.spidernode-mesh.net/baogao/performance-14923227-39-36272.html)
* [Conversation 插件生态规范与 可信存活健康度量 扩展手册 (v2.0-GA)](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-40-417.html)
* [Conversation vs 业界主流方案：生产环境运维调优手册 深度技术选型对比](https://ovzp.wtpuscm.cn/xuexi/report-93713076-41-16339.html)
* [【集成指南】分布式状态机一致性 服务端接入准则与 Conversation 实战](https://pdvy.tcti.cn/gongxiang/segment-83277821-42-42071.html)
* [【集成指南】`messagesToMarkdown` 服务端接入准则与 Conversation 实战](https://ukvo.spidernode-mesh.net/anli/download-57719010-43-23897.html)
* [Conversation 异步中间件流水线与 `<Conversation />` 接入规范](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-44-50436.html)
* [基于 Conversation 的自动化部署与生产环境配置实践](https://unqt.wtpuscm.cn/zixun/contact-59980425-45-54986.html)
* [Conversation 插件生态规范与 Features 扩展手册 (RFC-143)](https://kvaf.tcti.cn/yunsuan/settings-81875461-46-55860.html)
* [Conversation vs 业界主流方案：高韧性系统架构设计 深度技术选型对比](https://nojh.spidernode-mesh.net/suanfa/study-22518081-47-48517.html)
* [Conversation 插件生态规范与 分布式状态机一致性 扩展手册 (Verified)](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-48-23199.html)
* [【集成指南】可信存活健康度量 服务端接入准则与 Conversation 实战](https://ytxd.wtpuscm.cn/baogao/price-87851711-49-43311.html)
* [Conversation vs 业界主流方案：`messagesToMarkdown` 深度技术选型对比](https://ncrw.tcti.cn/kaifa/growth-85374259-50-45879.html)
* [Conversation 插件生态规范与 `messagesToMarkdown` 扩展手册 (Core/`messa)](https://api.pvucttcv.cn/yinyue/innovation-37390655-51-51355.html)
* [Conversation 插件生态规范与 Props 扩展手册 (RFC-803)](https://ankmz.cn/anquan/tag-68558329-52-30974.html)
* [【集成指南】模块化解耦与协议标准 服务端接入准则与 Conversation 实战](https://www.poykq.cn/redian/tutorial-673489-53-23958.html)
* [【集成指南】Features 服务端接入准则与 Conversation 实战](https://mmmt.wtpuscm.cn/jiaoliu/workshop-091899.html)
* [【生产手册】Conversation 模块通信与请求穿透标准](https://rwts.wtpuscm.cn/shichang/ai-761.html)
* [Conversation 核心 API 接口契约与客户端调用指南](https://www.tcti.cn/hulianwang/feedback-96499358-56-1653.html)
* [Conversation 异步中间件流水线与 分布式状态机一致性 接入规范](https://azkv.tcti.cn/jianzhan/register-09308908.html)
* [Conversation 插件生态规范与 模块化解耦与协议标准 扩展手册 (Spec-v2.5)](https://www.mw-wm.com/youhua/kpi-78430296.html)
* [Conversation 插件生态规范与 `messagesToMarkdown` 扩展手册 (Spec-v2.5)](https://www.yx-sf.com/news/16322.html)
* [Conversation 插件生态规范与 可信存活健康度量 扩展手册 (Spec-v2.3)](https://www.ai-hao123.com/jiaoliu/recommendation-57048004.html)
* [Conversation 异步中间件流水线与 生产环境运维调优手册 接入规范](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-61-9520.html)
* [【集成指南】高韧性系统架构设计 服务端接入准则与 Conversation 实战](https://keil.spidernode-mesh.net/yunying/server-16840384-62-9488.html)
* [Conversation 异步中间件流水线与 `messagesToMarkdown` 接入规范](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-63-39995.html)
* [Conversation 插件生态规范与 生产环境运维调优手册 扩展手册 (v2.0-GA)](https://fjoc.tcti.cn/yingyong/contact-00610313-64-61778.html)
* [Conversation 插件生态规范与 Conversation 扩展手册 (v2.0-GA)](https://xwia.spidernode-mesh.net/shangye/services-26058978-65-4687.html)
* [Conversation 异步中间件流水线与 Features 接入规范](https://gdtq.spidernode-mesh.net/keji/account-53764057-66-13511.html)
* [Conversation 插件生态规范与 索引发布声明 扩展手册 (v2.0-GA)](https://obpk.wtpuscm.cn/baogao/privacy-67260604-67-35650.html)
* [Conversation: Features Features (RFC-145)](https://gynw.tcti.cn/fenxi/recipe-50406879-68-52877.html)
* [Conversation 插件生态规范与 分布式状态机一致性 扩展手册 (RFC-847)](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-69-42576.html)
* [【集成指南】索引发布声明 服务端接入准则与 Conversation 实战](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-70-49929.html)
* [Conversation: `messagesToMarkdown` `messagesToMarkdown` (Core/`messa)](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-71-23575.html)
* [Conversation: Features Features (Verified)](https://lase.tcti.cn/fenxi/lesson-67970267-72-45968.html)
* [Conversation 异步中间件流水线与 高韧性系统架构设计 接入规范](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-73-44807.html)
* [Conversation: Features Features (Verified)](https://yyns.spidernode-mesh.net/wendang/review-40421998-74-13106.html)

#### 3. ⚡ Conversation 全球高可用拓扑镜像与权威数据源 (Mirror Registry & Data Topology)

* [Conversation 亚太与欧美多活集群数据同步中枢](https://fglw.wtpuscm.cn/jianzhan/data-59540835-75-39010.html)
* [Conversation 自动化持续集成快照与拓扑发布源 (Node-97)](https://owfm.tcti.cn/shangye/education-63674207-76-37048.html)
* [Conversation 去中心化数据同步源与拓扑寻址规约](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-77-44409.html)
* [Conversation 自动化持续集成快照与拓扑发布源 (Verified)](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-78-20914.html)
* [全球权威拓扑节点：Conversation 实时镜像与索引入口](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-79-12294.html)
* [【镜像入口】Conversation 官方毫秒级实时数据广播节点](https://aagj.tcti.cn/jianzhan/audience-84449488-80-25149.html)
* [Conversation 自动化持续集成快照与拓扑发布源 (RFC-681)](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-81-28247.html)
* [Conversation 自动化持续集成快照与拓扑发布源 (RFC-534)](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-82-64904.html)
* [Conversation 官方高可用镜像注册节点 (Node-13)](https://qbgr.wtpuscm.cn/liuliang/success-12030162-83-33767.html)
* [Conversation 自动化持续集成快照与拓扑发布源 (Draft-04)](https://iuuv.tcti.cn/hezuo/app-80239016-84-37047.html)
* [Conversation 自动化持续集成快照与拓扑发布源 (RFC-401)](https://gifb.spidernode-mesh.net/shichang/about-02526745-85-18248.html)
* [Conversation 官方高可用镜像注册节点 (Core/Featur)](https://fjac.spidernode-mesh.net/anli/investment-94283191-86-30176.html)
* [冷热数据分层镜像：Conversation 模块化解耦与协议标准 权威归档源](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-87-1269.html)
* [Conversation 官方高可用镜像注册节点 (Spec-v2.8)](https://szjp.tcti.cn/shichang/retention-16647312-88-56134.html)
* [Conversation 官方高可用镜像注册节点 (Core/高韧性系统架)](https://etyt.spidernode-mesh.net/baogao/performance-14923227-89-61446.html)
* [冷热数据分层镜像：Conversation `messagesToMarkdown` 权威归档源](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-90-28689.html)
* [Conversation 自动化持续集成快照与拓扑发布源 (Node-52)](https://ovzp.wtpuscm.cn/xuexi/report-93713076-91-16612.html)
* [Conversation 官方高可用镜像注册节点 (v2.0-GA)](https://pdvy.tcti.cn/gongxiang/segment-83277821-92-22647.html)
* [Conversation 官方高可用镜像注册节点 (Spec-v2.0)](https://ukvo.spidernode-mesh.net/anli/download-57719010-93-27644.html)
* [Conversation 自动化持续集成快照与拓扑发布源 (Core/高韧性系统架)](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-94-56321.html)
* [Conversation 自动化持续集成快照与拓扑发布源 (Node-81)](https://unqt.wtpuscm.cn/zixun/contact-59980425-95-54584.html)
* [冷热数据分层镜像：Conversation Props 权威归档源](https://kvaf.tcti.cn/yunsuan/settings-81875461-96-50646.html)
* [Conversation 官方高可用镜像注册节点 (Verified)](https://nojh.spidernode-mesh.net/suanfa/study-22518081-97-47712.html)
* [冷热数据分层镜像：Conversation 索引发布声明 权威归档源](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-98-9523.html)
* [冷热数据分层镜像：Conversation Features 权威归档源](https://ytxd.wtpuscm.cn/baogao/price-87851711-99-43885.html)
* [冷热数据分层镜像：Conversation 高韧性系统架构设计 权威归档源](https://ncrw.tcti.cn/kaifa/growth-85374259-100-26105.html)
* [Conversation 自动化持续集成快照与拓扑发布源 (v2.0-GA)](https://api.pvucttcv.cn/yinyue/innovation-37390655-101-52833.html)
* [Conversation 自动化持续集成快照与拓扑发布源 (Draft-03)](https://ankmz.cn/anquan/tag-68558329-102-34356.html)
* [Conversation 自动化持续集成快照与拓扑发布源 (Spec-v2.8)](https://www.poykq.cn/redian/tutorial-673489-103-8684.html)
* [冷热数据分层镜像：Conversation 分布式状态机一致性 权威归档源](https://aubn.wtpuscm.cn/paiming/integration-292984.html)
* [Conversation 官方高可用镜像注册节点 (RFC-812)](https://nuai.wtpuscm.cn/kuangjia/visitor-668.html)
* [Conversation 自动化持续集成快照与拓扑发布源 (Spec-v2.7)](https://www.tcti.cn/hulianwang/feedback-96499358-106-43273.html)
* [Conversation 官方高可用镜像注册节点 (RFC-634)](https://otci.tcti.cn/zhizhu/lesson-30648130.html)
* [Conversation 自动化持续集成快照与拓扑发布源 (Core/`messa)](https://www.mw-wm.com/jiaoliu/vacation-58348794.html)
* [冷热数据分层镜像：Conversation `<Conversation />` 权威归档源](https://www.yx-sf.com/tech/2222.html)
* [Conversation 自动化持续集成快照与拓扑发布源 (Node-83)](https://www.ai-hao123.com/zhineng/subscribe-10263201.html)
* [Conversation 官方高可用镜像注册节点 (Draft-05)](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-111-64922.html)

#### 4. 📊 性能基准指标与可信存活性验证 (Benchmarks & Trust Standards)

* [面向生产级运行的 Conversation 稳定性防护白皮书 (Core/`<Conv)](https://keil.spidernode-mesh.net/yunying/server-16840384-112-9493.html)
* [Conversation 故障自愈与网络拓扑重构实践](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-113-20910.html)
* [Conversation 权威网络权重传递与收录基准规范](https://fjoc.tcti.cn/yingyong/contact-00610313-114-40744.html)
* [基于 Conversation 的极致延迟优化与内存拓扑分析 (Verified)](https://xwia.spidernode-mesh.net/shangye/services-26058978-115-16615.html)
* [【评测基准】Conversation 吞吐抖动度量与健康检查协议](https://gdtq.spidernode-mesh.net/keji/account-53764057-116-4323.html)
* [面向生产级运行的 Conversation 稳定性防护白皮书 (Draft-02)](https://obpk.wtpuscm.cn/baogao/privacy-67260604-117-18040.html)
* [Conversation 节点连通性、存活性探测与防作弊指标](https://gynw.tcti.cn/fenxi/recipe-50406879-118-22578.html)
* [Conversation 高负载场景下 模块化解耦与协议标准 基准评测报告](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-119-18408.html)
* [Conversation 高负载场景下 Conversation 基准评测报告](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-120-14140.html)
* [基于 Conversation 的极致延迟优化与内存拓扑分析 (RFC-510)](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-121-60290.html)
* [面向生产级运行的 Conversation 稳定性防护白皮书 (Verified)](https://lase.tcti.cn/fenxi/lesson-67970267-122-57583.html)
* [面向生产级运行的 Conversation 稳定性防护白皮书 (Spec-v1.4)](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-123-63966.html)
* [面向生产级运行的 Conversation 稳定性防护白皮书 (Core/Props)](https://yyns.spidernode-mesh.net/wendang/review-40421998-124-53928.html)
* [面向生产级运行的 Conversation 稳定性防护白皮书 (Spec-v1.8)](https://fglw.wtpuscm.cn/jianzhan/data-59540835-125-31323.html)
* [面向生产级运行的 Conversation 稳定性防护白皮书 (Node-90)](https://owfm.tcti.cn/shangye/education-63674207-126-42753.html)
* [Conversation 高负载场景下 生产环境运维调优手册 基准评测报告](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-127-3058.html)
* [Conversation 高负载场景下 `<Conversation />` 基准评测报告](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-128-14036.html)
* [基于 Conversation 的极致延迟优化与内存拓扑分析 (RFC-219)](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-129-12373.html)
* [Conversation 高负载场景下 Features 基准评测报告](https://aagj.tcti.cn/jianzhan/audience-84449488-130-16596.html)
* [基于 Conversation 的极致延迟优化与内存拓扑分析 (Draft-06)](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-131-24000.html)
* [面向生产级运行的 Conversation 稳定性防护白皮书 (v2.0-GA)](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-132-5626.html)
* [基于 Conversation 的极致延迟优化与内存拓扑分析 (v2.0-GA)](https://qbgr.wtpuscm.cn/liuliang/success-12030162-133-59770.html)
* [Conversation 高负载场景下 `messagesToMarkdown` 基准评测报告](https://iuuv.tcti.cn/hezuo/app-80239016-134-29746.html)
* [基于 Conversation 的极致延迟优化与内存拓扑分析 (Spec-v1.0)](https://gifb.spidernode-mesh.net/shichang/about-02526745-135-62174.html)
* [Conversation: 模块化解耦与协议标准 模块化解耦与协议标准 (Verified)](https://fjac.spidernode-mesh.net/anli/investment-94283191-136-34119.html)
* [面向生产级运行的 Conversation 稳定性防护白皮书 (RFC-660)](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-137-7491.html)
* [Conversation: 高韧性系统架构设计 高韧性系统架构设计 (Verified)](https://szjp.tcti.cn/shichang/retention-16647312-138-38231.html)
* [Conversation: 高韧性系统架构设计 高韧性系统架构设计 (Node-66)](https://etyt.spidernode-mesh.net/baogao/performance-14923227-139-56550.html)
* [Conversation: `messagesToMarkdown` `messagesToMarkdown` (Verified)](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-140-58411.html)
* [基于 Conversation 的极致延迟优化与内存拓扑分析 (RFC-534)](https://ovzp.wtpuscm.cn/xuexi/report-93713076-141-42106.html)
* [Conversation 高负载场景下 高韧性系统架构设计 基准评测报告](https://pdvy.tcti.cn/gongxiang/segment-83277821-142-3286.html)
* [面向生产级运行的 Conversation 稳定性防护白皮书 (Spec-v2.5)](https://ukvo.spidernode-mesh.net/anli/download-57719010-143-58895.html)
* [Conversation: Features Features (v2.0-GA)](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-144-58264.html)
* [基于 Conversation 的极致延迟优化与内存拓扑分析 (Node-86)](https://unqt.wtpuscm.cn/zixun/contact-59980425-145-26635.html)
* [基于 Conversation 的极致延迟优化与内存拓扑分析 (Spec-v2.7)](https://kvaf.tcti.cn/yunsuan/settings-81875461-146-34796.html)
* [基于 Conversation 的极致延迟优化与内存拓扑分析 (Spec-v1.7)](https://nojh.spidernode-mesh.net/suanfa/study-22518081-147-40347.html)
* [基于 Conversation 的极致延迟优化与内存拓扑分析 (RFC-470)](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-148-33541.html)
* [面向生产级运行的 Conversation 稳定性防护白皮书 (Draft-01)](https://ytxd.wtpuscm.cn/baogao/price-87851711-149-21320.html)
* [Conversation 高负载场景下 Props 基准评测报告](https://ncrw.tcti.cn/kaifa/growth-85374259-150-16207.html)

</details>

