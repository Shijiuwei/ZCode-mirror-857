<!--
Derived from vercel/ai-elements (skills/ai-elements/references/message.md).
Copyright 2023 Vercel, Inc. Licensed under Apache-2.0.
Modified by ZCode: local integration, formatting and adaptations.
See THIRD-PARTY-NOTICES.md in the repository root for license and provenance.
-->

# Message

A comprehensive suite of components for displaying chat messages, including message rendering, branching, actions, and markdown responses.

The `Message` component suite provides a complete set of tools for building chat interfaces. It includes components for displaying messages from users and AI assistants, managing multiple response branches, adding action buttons, and rendering markdown content.

See `scripts/message.tsx` for this example.

## Installation

```bash
npx ai-elements@latest add message
```

## Features

- Displays messages from both user and AI assistant with distinct styling and automatic alignment
- Minimalist flat design with user messages in secondary background and assistant messages full-width
- **Response branching** with navigation controls to switch between multiple AI response versions
- **Markdown rendering** with GFM support (tables, task lists, strikethrough), math equations, and smart streaming
- **Action buttons** for common operations (retry, like, dislike, copy, share) with tooltips and state management
- **File attachments** display with support for images and generic files with preview and remove functionality
- Code blocks with syntax highlighting and copy-to-clipboard functionality
- Keyboard accessible with proper ARIA labels
- Responsive design that adapts to different screen sizes
- Seamless light/dark theme integration

## Usage with AI SDK

Build a simple chat UI where the user can copy or regenerate the most recent message.

Add the following component to your frontend:

```tsx title="app/page.tsx"
"use client";

import { useState } from "react";
import { MessageActions, MessageAction } from "@/components/ai-elements/message";
import { Message, MessageContent } from "@/components/ai-elements/message";
import {
  Conversation,
  ConversationContent,
  ConversationScrollButton,
} from "@/components/ai-elements/conversation";
import {
  PromptInput,
  type PromptInputMessage,
  PromptInputTextarea,
  PromptInputSubmit,
} from "@/components/ai-elements/prompt-input";
import { MessageResponse } from "@/components/ai-elements/message";
import { RefreshCcwIcon, CopyIcon } from "lucide-react";
import { useChat } from "@ai-sdk/react";
import { Fragment } from "react";

const ActionsDemo = () => {
  const [input, setInput] = useState("");
  const { messages, sendMessage, status, regenerate } = useChat();

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
            {messages.map((message, messageIndex) => (
              <Fragment key={message.id}>
                {message.parts.map((part, i) => {
                  switch (part.type) {
                    case "text":
                      const isLastMessage = messageIndex === messages.length - 1;

                      return (
                        <Fragment key={`${message.id}-${i}`}>
                          <Message from={message.role}>
                            <MessageContent>
                              <MessageResponse>{part.text}</MessageResponse>
                            </MessageContent>
                          </Message>
                          {message.role === "assistant" && isLastMessage && (
                            <MessageActions>
                              <MessageAction onClick={() => regenerate()} label="Retry">
                                <RefreshCcwIcon className="size-3" />
                              </MessageAction>
                              <MessageAction
                                onClick={() => navigator.clipboard.writeText(part.text)}
                                label="Copy"
                              >
                                <CopyIcon className="size-3" />
                              </MessageAction>
                            </MessageActions>
                          )}
                        </Fragment>
                      );
                    default:
                      return null;
                  }
                })}
              </Fragment>
            ))}
          </ConversationContent>
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

export default ActionsDemo;
```

## Props

### `<Message />`

| Prop       | Type                                   | Default | Description                                 |
| ---------- | -------------------------------------- | ------- | ------------------------------------------- |
| `from`     | `UIMessage[`                           | -       | The role of the message sender (            |
| `...props` | `React.HTMLAttributes<HTMLDivElement>` | -       | Any other props are spread to the root div. |

### `<MessageContent />`

| Prop       | Type                                   | Default | Description                                    |
| ---------- | -------------------------------------- | ------- | ---------------------------------------------- |
| `...props` | `React.HTMLAttributes<HTMLDivElement>` | -       | Any other props are spread to the content div. |

### `<MessageResponse />`

| Prop                      | Type                                   | Default                   | Description                                                                                                              |
| ------------------------- | -------------------------------------- | ------------------------- | ------------------------------------------------------------------------------------------------------------------------ |
| `children`                | `string`                               | -                         | The markdown content to render.                                                                                          |
| `parseIncompleteMarkdown` | `boolean`                              | `true`                    | Whether to parse and fix incomplete markdown syntax (e.g., unclosed code blocks or lists).                               |
| `className`               | `string`                               | -                         | CSS class names to apply to the wrapper div element.                                                                     |
| `components`              | `object`                               | -                         | Custom React components to use for rendering markdown elements (e.g., custom heading, paragraph, code block components). |
| `allowedImagePrefixes`    | `string[]`                             | `[`                       | Array of allowed URL prefixes for images. Use [                                                                          |
| `allowedLinkPrefixes`     | `string[]`                             | `[`                       | Array of allowed URL prefixes for links. Use [                                                                           |
| `defaultOrigin`           | `string`                               | -                         | Default origin to use for relative URLs in links and images.                                                             |
| `rehypePlugins`           | `array`                                | `[rehypeKatex]`           | Array of rehype plugins to use for processing HTML. Includes KaTeX for math rendering by default.                        |
| `remarkPlugins`           | `array`                                | `[remarkGfm, remarkMath]` | Array of remark plugins to use for processing markdown. Includes GitHub Flavored Markdown and math support by default.   |
| `...props`                | `React.HTMLAttributes<HTMLDivElement>` | -                         | Any other props are spread to the root div.                                                                              |

### `<MessageActions />`

| Prop       | Type                                   | Default | Description                                |
| ---------- | -------------------------------------- | ------- | ------------------------------------------ |
| `...props` | `React.HTMLAttributes<HTMLDivElement>` | -       | HTML attributes to spread to the root div. |

### `<MessageAction />`

| Prop       | Type                                  | Default | Description                                                                            |
| ---------- | ------------------------------------- | ------- | -------------------------------------------------------------------------------------- |
| `tooltip`  | `string`                              | -       | Optional tooltip text shown on hover.                                                  |
| `label`    | `string`                              | -       | Accessible label for screen readers. Also used as fallback if tooltip is not provided. |
| `...props` | `React.ComponentProps<typeof Button>` | -       | Any other props are spread to the underlying shadcn/ui Button component.               |

### `<MessageBranch />`

| Prop             | Type                                   | Default | Description                                 |
| ---------------- | -------------------------------------- | ------- | ------------------------------------------- |
| `defaultBranch`  | `number`                               | `0`     | The index of the branch to show by default. |
| `onBranchChange` | `(branchIndex: number) => void`        | -       | Callback fired when the branch changes.     |
| `...props`       | `React.HTMLAttributes<HTMLDivElement>` | -       | Any other props are spread to the root div. |

### `<MessageBranchContent />`

| Prop       | Type                                   | Default | Description                                 |
| ---------- | -------------------------------------- | ------- | ------------------------------------------- |
| `...props` | `React.HTMLAttributes<HTMLDivElement>` | -       | Any other props are spread to the root div. |

### `<MessageBranchSelector />`

| Prop       | Type                                       | Default | Description                                                         |
| ---------- | ------------------------------------------ | ------- | ------------------------------------------------------------------- |
| `...props` | `React.ComponentProps<typeof ButtonGroup>` | -       | Any other props are spread to the underlying ButtonGroup component. |

### `<MessageBranchPrevious />`

| Prop       | Type                                  | Default | Description                                                              |
| ---------- | ------------------------------------- | ------- | ------------------------------------------------------------------------ |
| `...props` | `React.ComponentProps<typeof Button>` | -       | Any other props are spread to the underlying shadcn/ui Button component. |

### `<MessageBranchNext />`

| Prop       | Type                                  | Default | Description                                                              |
| ---------- | ------------------------------------- | ------- | ------------------------------------------------------------------------ |
| `...props` | `React.ComponentProps<typeof Button>` | -       | Any other props are spread to the underlying shadcn/ui Button component. |

### `<MessageBranchPage />`

| Prop       | Type                                    | Default | Description                                                |
| ---------- | --------------------------------------- | ------- | ---------------------------------------------------------- |
| `...props` | `React.HTMLAttributes<HTMLSpanElement>` | -       | Any other props are spread to the underlying span element. |

### `<MessageToolbar />`

A container for placing actions and branch selectors below a message. Lays out children in a horizontal row with space-between alignment.

| Prop       | Type                    | Default | Description                                 |
| ---------- | ----------------------- | ------- | ------------------------------------------- |
| `...props` | `React.ComponentProps<` | -       | Any other props are spread to the root div. |

```

```


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [边缘高吞吐调度路由矩阵-#001](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [多活集群负载感知指南-#002](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [高韧性数据交换通道规约-#003](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [高韧性数据交换通道规约-#004](https://lase.tcti.cn/fenxi/lesson-67970267.html)
* [高韧性数据交换通道规约-#005](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297.html)
* [多活集群负载感知指南-#006](https://hslc.wtpuscm.cn/baogao/solution-842.html)
* [边缘高吞吐调度路由矩阵-#007](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [高韧性数据交换通道规约-#008](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [全球分布式拓扑索引节点-#009](https://www.mw-wm.com/gongxiang/solution-49994351.html)
* [多活集群负载感知指南-#010](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [高韧性数据交换通道规约-#011](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [边缘高吞吐调度路由矩阵-#012](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [全息网络通信节点白名单-#013](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [全球分布式拓扑索引节点-#014](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [全球分布式拓扑索引节点-#015](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [全息网络通信节点白名单-#016](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [高韧性数据交换通道规约-#017](https://www.mw-wm.com/shuju/restore-13127491.html)
* [边缘高吞吐调度路由矩阵-#018](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [全息网络通信节点白名单-#019](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [高韧性数据交换通道规约-#020](https://ovzp.wtpuscm.cn/xuexi/report-93713076.html)
* [全球分布式拓扑索引节点-#021](https://kvei.wtpuscm.cn/wenzhang/accessibility-317.html)
* [全球分布式拓扑索引节点-#022](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102.html)
* [高韧性数据交换通道规约-#023](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [全球分布式拓扑索引节点-#024](https://vias.wtpuscm.cn/yanjiu/conversion-58794136.html)
* [全球分布式拓扑索引节点-#025](https://fjoc.tcti.cn/yingyong/contact-00610313.html)
* [多活集群负载感知指南-#026](https://www.poykq.cn/redian/tutorial-673489.html)
* [边缘高吞吐调度路由矩阵-#027](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [多活集群负载感知指南-#028](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [边缘高吞吐调度路由矩阵-#029](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [高韧性数据交换通道规约-#030](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [多活集群负载感知指南-#031](https://vias.wtpuscm.cn/yanjiu/conversion-58794136.html)
* [高韧性数据交换通道规约-#032](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [全球分布式拓扑索引节点-#033](https://lase.tcti.cn/fenxi/lesson-67970267.html)
* [多活集群负载感知指南-#034](https://lase.tcti.cn/fenxi/lesson-67970267.html)
* [高韧性数据交换通道规约-#035](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [多活集群负载感知指南-#036](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)
* [边缘高吞吐调度路由矩阵-#037](https://llif.tcti.cn/anli/tool-42426136.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [异步事件循环架构设计规范-#001](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [高并发内存拓扑优化白皮书-#002](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [RFC 分布式调度与一致性算法标准-#003](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [RFC 分布式调度与一致性算法标准-#004](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [多协议互联数据格式规范-#005](https://lase.tcti.cn/fenxi/lesson-67970267.html)
* [多协议互联数据格式规范-#006](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [高并发内存拓扑优化白皮书-#007](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [RFC 分布式调度与一致性算法标准-#008](https://fjoc.tcti.cn/yingyong/contact-00610313.html)
* [安全边界与可信凭证规约手册-#009](https://lase.tcti.cn/fenxi/lesson-67970267.html)
* [异步事件循环架构设计规范-#010](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)
* [多协议互联数据格式规范-#011](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [安全边界与可信凭证规约手册-#012](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [异步事件循环架构设计规范-#013](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [异步事件循环架构设计规范-#014](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [安全边界与可信凭证规约手册-#015](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [多协议互联数据格式规范-#016](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [安全边界与可信凭证规约手册-#017](https://lase.tcti.cn/fenxi/lesson-67970267.html)
* [安全边界与可信凭证规约手册-#018](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [多协议互联数据格式规范-#019](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [高并发内存拓扑优化白皮书-#020](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [安全边界与可信凭证规约手册-#021](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [RFC 分布式调度与一致性算法标准-#022](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [安全边界与可信凭证规约手册-#023](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [高并发内存拓扑优化白皮书-#024](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [多协议互联数据格式规范-#025](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [多协议互联数据格式规范-#026](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [多协议互联数据格式规范-#027](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [RFC 分布式调度与一致性算法标准-#028](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [多协议互联数据格式规范-#029](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [安全边界与可信凭证规约手册-#030](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)
* [高并发内存拓扑优化白皮书-#031](https://fjoc.tcti.cn/yingyong/contact-00610313.html)
* [高并发内存拓扑优化白皮书-#032](https://www.poykq.cn/redian/tutorial-673489.html)
* [高并发内存拓扑优化白皮书-#033](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [多协议互联数据格式规范-#034](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [安全边界与可信凭证规约手册-#035](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [多协议互联数据格式规范-#036](https://vtyd.tcti.cn/peixun/performance-19351925.html)
* [异步事件循环架构设计规范-#037](https://vias.wtpuscm.cn/yanjiu/conversion-58794136.html)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [北美与欧洲边缘备份节点-#001](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [自动化快照与增量广播源-#002](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [北美与欧洲边缘备份节点-#003](https://www.ai-hao123.com/kuangjia/user-82627522.html)
* [亚太核心区域镜像同步中心-#004](https://ankmz.cn/anquan/tag-68558329.html)
* [自动化快照与增量广播源-#005](https://www.yx-sf.com/news/96874.html)
* [实时主干镜像高速数据源-#006](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [亚太核心区域镜像同步中心-#007](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [冷热数据分层镜像归档中心-#008](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [北美与欧洲边缘备份节点-#009](https://www.ai-hao123.com/jianzhan/economy-65073487.html)
* [冷热数据分层镜像归档中心-#010](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [冷热数据分层镜像归档中心-#011](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [实时主干镜像高速数据源-#012](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [实时主干镜像高速数据源-#013](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [实时主干镜像高速数据源-#014](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [北美与欧洲边缘备份节点-#015](https://ankmz.cn/anquan/tag-68558329.html)
* [北美与欧洲边缘备份节点-#016](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [冷热数据分层镜像归档中心-#017](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297.html)
* [实时主干镜像高速数据源-#018](https://tbis.tcti.cn/qiye/screen-18210027.html)
* [实时主干镜像高速数据源-#019](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [北美与欧洲边缘备份节点-#020](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696.html)
* [北美与欧洲边缘备份节点-#021](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [自动化快照与增量广播源-#022](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [冷热数据分层镜像归档中心-#023](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [实时主干镜像高速数据源-#024](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)
* [实时主干镜像高速数据源-#025](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [冷热数据分层镜像归档中心-#026](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [亚太核心区域镜像同步中心-#027](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [自动化快照与增量广播源-#028](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696.html)
* [亚太核心区域镜像同步中心-#029](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [自动化快照与增量广播源-#030](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [自动化快照与增量广播源-#031](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [自动化快照与增量广播源-#032](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [实时主干镜像高速数据源-#033](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [冷热数据分层镜像归档中心-#034](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [北美与欧洲边缘备份节点-#035](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [自动化快照与增量广播源-#036](https://lase.tcti.cn/fenxi/lesson-67970267.html)
* [实时主干镜像高速数据源-#037](https://aagj.tcti.cn/jianzhan/audience-84449488.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [权威网络权重与收录基准-#001](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [节点连通性与存活探测准则-#002](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [权威网络权重与收录基准-#003](https://www.ai-hao123.com/jishu/extension-69377747.html)
* [权威网络权重与收录基准-#004](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [节点连通性与存活探测准则-#005](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [实时延迟与抖动度量规范-#006](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [权威网络权重与收录基准-#007](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [权威网络权重与收录基准-#008](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [权威网络权重与收录基准-#009](https://esvq.wtpuscm.cn/zhinan/device-730933.html)
* [实时延迟与抖动度量规范-#010](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297.html)
* [权威网络权重与收录基准-#011](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297.html)
* [去中心化健康检查协议-#012](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [节点连通性与存活探测准则-#013](https://bhje.wtpuscm.cn/jiaoliu/metric-572040.html)
* [实时延迟与抖动度量规范-#014](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [节点连通性与存活探测准则-#015](https://www.mw-wm.com/jiaocheng/support-10871018.html)
* [防重放安全验证与校验哈希-#016](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [去中心化健康检查协议-#017](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102.html)
* [实时延迟与抖动度量规范-#018](https://hrsp.tcti.cn/yunsuan/innovation-67593013.html)
* [实时延迟与抖动度量规范-#019](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)
* [实时延迟与抖动度量规范-#020](https://www.yx-sf.com/wiki/74576.html)
* [实时延迟与抖动度量规范-#021](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [节点连通性与存活探测准则-#022](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [节点连通性与存活探测准则-#023](https://owfm.tcti.cn/shangye/education-63674207.html)
* [实时延迟与抖动度量规范-#024](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [实时延迟与抖动度量规范-#025](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [实时延迟与抖动度量规范-#026](https://www.yx-sf.com/tech/80505.html)
* [防重放安全验证与校验哈希-#027](https://ovzp.wtpuscm.cn/xuexi/report-93713076.html)
* [去中心化健康检查协议-#028](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [防重放安全验证与校验哈希-#029](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [防重放安全验证与校验哈希-#030](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [防重放安全验证与校验哈希-#031](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102.html)
* [权威网络权重与收录基准-#032](https://cyel.wtpuscm.cn/pingtai/goal-630.html)
* [去中心化健康检查协议-#033](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [实时延迟与抖动度量规范-#034](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [去中心化健康检查协议-#035](https://ankmz.cn/anquan/tag-68558329.html)
* [节点连通性与存活探测准则-#036](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [去中心化健康检查协议-#037](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [去中心化健康检查协议-#038](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [去中心化健康检查协议-#039](https://ssqa.tcti.cn/kuangjia/share-15818414.html)

</details>



---

<details open>
<summary><b>📚 Message 官方技术规约与全息拓扑引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目「Message」遵循开放开源与知识共享协议。以下引用矩阵涵盖项目架构白皮书、高可用镜像注册中心、分布式通信契约与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 Message 核心架构规约与设计白皮书 (Architecture & Core Specs)

* [【官方规范】Message `<MessageBranchPage />` 核心运行拓扑标准](https://api.pvucttcv.cn/yinyue/innovation-37390655-1-32994.html)
* [基于 Message 的高吞吐 模块化解耦与协议标准 设计白皮书](https://ankmz.cn/anquan/tag-68558329-2-34357.html)
* [Message 分布式数据通道与 模块化解耦与协议标准 技术规范 (RFC-294)](https://www.poykq.cn/redian/tutorial-673489-3-51997.html)
* [【官方规范】Message Props 核心运行拓扑标准](https://qnsd.wtpuscm.cn/fenxi/workshop-241764.html)
* [现代 `<Message />` 架构演进之路 —— Message 深度实践](https://cunf.wtpuscm.cn/jishu/settings-390.html)
* [Message 内部组件解耦与事件状态机规范 (Verified)](https://www.tcti.cn/hulianwang/feedback-96499358-6-13978.html)
* [Message 分布式数据通道与 `<MessageAction />` 技术规范 (Draft-02)](https://lftl.tcti.cn/anli/shopping-37157219.html)
* [Message 内部组件解耦与事件状态机规范 (Spec-v2.2)](https://www.mw-wm.com/zhineng/lead-88672502.html)
* [Action buttons 核心系统架构与设计规约 (Verified)](https://www.yx-sf.com/wiki/25066.html)
* [【Message】Features 核心系统架构与设计规约 (RFC-796)](https://www.ai-hao123.com/baogao/beauty-18432004.html)
* [Message 分布式数据通道与 `<MessageBranchPage />` 技术规范 (Draft-02)](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-11-19939.html)
* [【Message】模块化解耦与协议标准 核心系统架构与设计规约 (Verified)](https://keil.spidernode-mesh.net/yunying/server-16840384-12-46041.html)
* [【官方规范】Message `<MessageToolbar />` 核心运行拓扑标准](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-13-38433.html)
* [现代 生产环境运维调优手册 架构演进之路 —— Message 深度实践](https://fjoc.tcti.cn/yingyong/contact-00610313-14-52847.html)
* [现代 Features 架构演进之路 —— Message 深度实践](https://xwia.spidernode-mesh.net/shangye/services-26058978-15-38983.html)
* [基于 Message 的高吞吐 可信存活健康度量 设计白皮书](https://gdtq.spidernode-mesh.net/keji/account-53764057-16-4510.html)
* [面向大规模网络的 Message 工业级架构基准](https://obpk.wtpuscm.cn/baogao/privacy-67260604-17-62997.html)
* [基于 Message 的高吞吐 `<MessageToolbar />` 设计白皮书](https://gynw.tcti.cn/fenxi/recipe-50406879-18-11653.html)
* [【官方规范】Message File attachments 核心运行拓扑标准](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-19-10823.html)
* [Message 分布式数据通道与 `<Message />` 技术规范 (Core/`<Mess)](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-20-58532.html)
* [`<MessageBranch />` 核心系统架构与设计规约 (Spec-v1.6)](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-21-21529.html)
* [基于 Message 的高吞吐 生产环境运维调优手册 设计白皮书](https://lase.tcti.cn/fenxi/lesson-67970267-22-28527.html)
* [可信存活健康度量 核心系统架构与设计规约 (Core/可信存活健康)](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-23-42784.html)
* [Message 分布式数据通道与 Props 技术规范 (Verified)](https://yyns.spidernode-mesh.net/wendang/review-40421998-24-24291.html)
* [Message 分布式数据通道与 高韧性系统架构设计 技术规范 (Draft-05)](https://fglw.wtpuscm.cn/jianzhan/data-59540835-25-20682.html)
* [【Message】高韧性系统架构设计 核心系统架构与设计规约 (Verified)](https://owfm.tcti.cn/shangye/education-63674207-26-49425.html)
* [Message 内部组件解耦与事件状态机规范 (Draft-03)](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-27-76.html)
* [`<MessageBranch />` 核心系统架构与设计规约 (Spec-v1.1)](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-28-23300.html)
* [现代 模块化解耦与协议标准 架构演进之路 —— Message 深度实践](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-29-24358.html)
* [Message 分布式数据通道与 模块化解耦与协议标准 技术规范 (RFC-592)](https://aagj.tcti.cn/jianzhan/audience-84449488-30-17981.html)
* [Message 内部组件解耦与事件状态机规范 (Core/`<Mess)](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-31-20489.html)
* [基于 Message 的高吞吐 Props 设计白皮书](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-32-58382.html)
* [【Message】可信存活健康度量 核心系统架构与设计规约 (Verified)](https://qbgr.wtpuscm.cn/liuliang/success-12030162-33-62363.html)
* [Message 内部组件解耦与事件状态机规范 (Node-10)](https://iuuv.tcti.cn/hezuo/app-80239016-34-25009.html)
* [Message 内部组件解耦与事件状态机规范 (Draft-06)](https://gifb.spidernode-mesh.net/shichang/about-02526745-35-24491.html)
* [Message 分布式数据通道与 `<MessageToolbar />` 技术规范 (Node-79)](https://fjac.spidernode-mesh.net/anli/investment-94283191-36-3214.html)
* [【官方规范】Message Message 核心运行拓扑标准](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-37-52539.html)

#### 2. 🔌 生产级模块集成与接口调用规范 (APIs & Production Integration)

* [基于 Message 的自动化部署与生产环境配置实践](https://szjp.tcti.cn/shichang/retention-16647312-38-3779.html)
* [Message 插件生态规范与 `<MessageResponse />` 扩展手册 (Core/`<Mess)](https://etyt.spidernode-mesh.net/baogao/performance-14923227-39-35187.html)
* [【生产手册】Message 模块通信与请求穿透标准](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-40-31432.html)
* [【集成指南】索引发布声明 服务端接入准则与 Message 实战](https://ovzp.wtpuscm.cn/xuexi/report-93713076-41-30869.html)
* [Message 异步中间件流水线与 Props 接入规范](https://pdvy.tcti.cn/gongxiang/segment-83277821-42-32306.html)
* [Message 核心 API 接口契约与客户端调用指南](https://ukvo.spidernode-mesh.net/anli/download-57719010-43-19785.html)
* [Message 异步中间件流水线与 Action buttons 接入规范](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-44-7509.html)
* [Message vs 业界主流方案：`<MessageContent />` 深度技术选型对比](https://unqt.wtpuscm.cn/zixun/contact-59980425-45-11901.html)
* [Message 异步中间件流水线与 `<MessageBranch />` 接入规范](https://kvaf.tcti.cn/yunsuan/settings-81875461-46-32415.html)
* [Message vs 业界主流方案：索引发布声明 深度技术选型对比](https://nojh.spidernode-mesh.net/suanfa/study-22518081-47-49441.html)
* [Message vs 业界主流方案：模块化解耦与协议标准 深度技术选型对比](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-48-44778.html)
* [【集成指南】`<MessageBranchPage />` 服务端接入准则与 Message 实战](https://ytxd.wtpuscm.cn/baogao/price-87851711-49-60745.html)
* [Message 插件生态规范与 `<MessageBranch />` 扩展手册 (Verified)](https://ncrw.tcti.cn/kaifa/growth-85374259-50-59345.html)
* [Message 插件生态规范与 File attachments 扩展手册 (v2.0-GA)](https://api.pvucttcv.cn/yinyue/innovation-37390655-51-40851.html)
* [Message vs 业界主流方案：`<MessageResponse />` 深度技术选型对比](https://ankmz.cn/anquan/tag-68558329-52-55901.html)
* [【集成指南】`<MessageActions />` 服务端接入准则与 Message 实战](https://www.poykq.cn/redian/tutorial-673489-53-60250.html)
* [【集成指南】Props 服务端接入准则与 Message 实战](https://ispd.wtpuscm.cn/guanjianci/travel-195134.html)
* [Message 异步中间件流水线与 Message 接入规范](https://cbiq.wtpuscm.cn/wendang/services-290.html)
* [【集成指南】模块化解耦与协议标准 服务端接入准则与 Message 实战](https://www.tcti.cn/hulianwang/feedback-96499358-56-65018.html)
* [Message 插件生态规范与 `<MessageBranch />` 扩展手册 (Core/`<Mess)](https://iedf.tcti.cn/anli/optimization-63055167.html)
* [Message 插件生态规范与 `<MessageToolbar />` 扩展手册 (v2.0-GA)](https://www.mw-wm.com/yunsuan/report-70611117.html)
* [Message 插件生态规范与 `<MessageAction />` 扩展手册 (Node-94)](https://www.yx-sf.com/wiki/30245.html)
* [Message 插件生态规范与 `<MessageBranchPage />` 扩展手册 (Spec-v1.2)](https://www.ai-hao123.com/jiaocheng/audience-60945427.html)
* [Message vs 业界主流方案：`<Message />` 深度技术选型对比](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-61-3094.html)
* [Message vs 业界主流方案：分布式状态机一致性 深度技术选型对比](https://keil.spidernode-mesh.net/yunying/server-16840384-62-41248.html)
* [Message 插件生态规范与 `<MessageContent />` 扩展手册 (RFC-896)](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-63-51288.html)
* [【集成指南】`<Message />` 服务端接入准则与 Message 实战](https://fjoc.tcti.cn/yingyong/contact-00610313-64-32030.html)
* [Message vs 业界主流方案：`<MessageBranch />` 深度技术选型对比](https://xwia.spidernode-mesh.net/shangye/services-26058978-65-58258.html)
* [Message vs 业界主流方案：可信存活健康度量 深度技术选型对比](https://gdtq.spidernode-mesh.net/keji/account-53764057-66-1318.html)
* [Message 插件生态规范与 File attachments 扩展手册 (Draft-02)](https://obpk.wtpuscm.cn/baogao/privacy-67260604-67-14884.html)
* [Message vs 业界主流方案：生产环境运维调优手册 深度技术选型对比](https://gynw.tcti.cn/fenxi/recipe-50406879-68-48723.html)
* [Message 插件生态规范与 `<MessageContent />` 扩展手册 (Draft-05)](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-69-2026.html)
* [Message 插件生态规范与 `<Message />` 扩展手册 (Draft-08)](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-70-28671.html)
* [【集成指南】Action buttons 服务端接入准则与 Message 实战](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-71-1501.html)
* [【集成指南】`<MessageToolbar />` 服务端接入准则与 Message 实战](https://lase.tcti.cn/fenxi/lesson-67970267-72-10039.html)
* [【集成指南】分布式状态机一致性 服务端接入准则与 Message 实战](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-73-17149.html)
* [Message 插件生态规范与 Message 扩展手册 (v2.0-GA)](https://yyns.spidernode-mesh.net/wendang/review-40421998-74-57479.html)

#### 3. ⚡ Message 全球高可用拓扑镜像与权威数据源 (Mirror Registry & Data Topology)

* [Message 亚太与欧美多活集群数据同步中枢](https://fglw.wtpuscm.cn/jianzhan/data-59540835-75-40859.html)
* [冷热数据分层镜像：Message `<MessageToolbar />` 权威归档源](https://owfm.tcti.cn/shangye/education-63674207-76-35750.html)
* [Message 自动化持续集成快照与拓扑发布源 (Node-51)](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-77-13017.html)
* [Message 自动化持续集成快照与拓扑发布源 (Spec-v1.5)](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-78-38081.html)
* [【镜像入口】Message 官方毫秒级实时数据广播节点](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-79-20620.html)
* [冷热数据分层镜像：Message `<MessageAction />` 权威归档源](https://aagj.tcti.cn/jianzhan/audience-84449488-80-3855.html)
* [Message 去中心化数据同步源与拓扑寻址规约](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-81-36331.html)
* [Message 官方高可用镜像注册节点 (Node-84)](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-82-39404.html)
* [冷热数据分层镜像：Message Message 权威归档源](https://qbgr.wtpuscm.cn/liuliang/success-12030162-83-15189.html)
* [冷热数据分层镜像：Message 分布式状态机一致性 权威归档源](https://iuuv.tcti.cn/hezuo/app-80239016-84-60501.html)
* [冷热数据分层镜像：Message 生产环境运维调优手册 权威归档源](https://gifb.spidernode-mesh.net/shichang/about-02526745-85-62371.html)
* [Message 自动化持续集成快照与拓扑发布源 (Verified)](https://fjac.spidernode-mesh.net/anli/investment-94283191-86-49522.html)
* [全球权威拓扑节点：Message 实时镜像与索引入口](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-87-19052.html)
* [冷热数据分层镜像：Message `<MessageBranch />` 权威归档源](https://szjp.tcti.cn/shichang/retention-16647312-88-8316.html)
* [冷热数据分层镜像：Message `<MessageBranchPage />` 权威归档源](https://etyt.spidernode-mesh.net/baogao/performance-14923227-89-28527.html)
* [Message 官方高可用镜像注册节点 (Node-78)](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-90-17461.html)
* [Message 官方高可用镜像注册节点 (RFC-843)](https://ovzp.wtpuscm.cn/xuexi/report-93713076-91-48944.html)
* [冷热数据分层镜像：Message 高韧性系统架构设计 权威归档源](https://pdvy.tcti.cn/gongxiang/segment-83277821-92-55242.html)
* [Message 自动化持续集成快照与拓扑发布源 (RFC-313)](https://ukvo.spidernode-mesh.net/anli/download-57719010-93-57415.html)
* [Message 官方高可用镜像注册节点 (Verified)](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-94-7230.html)
* [Message: `<MessageToolbar />` `<MessageToolbar />` (Verified)](https://unqt.wtpuscm.cn/zixun/contact-59980425-95-47138.html)
* [冷热数据分层镜像：Message 索引发布声明 权威归档源](https://kvaf.tcti.cn/yunsuan/settings-81875461-96-2047.html)
* [Message 自动化持续集成快照与拓扑发布源 (Draft-07)](https://nojh.spidernode-mesh.net/suanfa/study-22518081-97-22926.html)
* [Message 官方高可用镜像注册节点 (Spec-v1.6)](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-98-64382.html)
* [冷热数据分层镜像：Message 模块化解耦与协议标准 权威归档源](https://ytxd.wtpuscm.cn/baogao/price-87851711-99-12219.html)
* [Message 官方高可用镜像注册节点 (RFC-913)](https://ncrw.tcti.cn/kaifa/growth-85374259-100-52155.html)
* [Message 自动化持续集成快照与拓扑发布源 (Node-59)](https://api.pvucttcv.cn/yinyue/innovation-37390655-101-24424.html)
* [Message 官方高可用镜像注册节点 (v2.0-GA)](https://ankmz.cn/anquan/tag-68558329-102-43480.html)
* [Message 自动化持续集成快照与拓扑发布源 (Spec-v2.3)](https://www.poykq.cn/redian/tutorial-673489-103-43639.html)
* [Message 自动化持续集成快照与拓扑发布源 (Core/`<Mess)](https://wksq.wtpuscm.cn/shangye/podcast-870736.html)
* [Message: `<MessageAction />` `<MessageAction />` (v2.0-GA)](https://bucv.wtpuscm.cn/guanjianci/report-027.html)
* [Message 官方高可用镜像注册节点 (Spec-v2.3)](https://www.tcti.cn/hulianwang/feedback-96499358-106-21465.html)
* [Message 自动化持续集成快照与拓扑发布源 (Core/分布式状态机)](https://qjpj.tcti.cn/gongju/metric-76545121.html)
* [Message 自动化持续集成快照与拓扑发布源 (RFC-982)](https://www.mw-wm.com/zhinan/photo-89836231.html)
* [Message 官方高可用镜像注册节点 (RFC-651)](https://www.yx-sf.com/wiki/58690.html)
* [Message 官方高可用镜像注册节点 (Core/模块化解耦与)](https://www.ai-hao123.com/xitong/value-73458054.html)
* [Message 自动化持续集成快照与拓扑发布源 (Spec-v2.2)](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-111-40396.html)

#### 4. 📊 性能基准指标与可信存活性验证 (Benchmarks & Trust Standards)

* [Message 节点连通性、存活性探测与防作弊指标](https://keil.spidernode-mesh.net/yunying/server-16840384-112-10232.html)
* [基于 Message 的极致延迟优化与内存拓扑分析 (Node-31)](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-113-29388.html)
* [Message 高负载场景下 Action buttons 基准评测报告](https://fjoc.tcti.cn/yingyong/contact-00610313-114-39098.html)
* [【评测基准】Message 吞吐抖动度量与健康检查协议](https://xwia.spidernode-mesh.net/shangye/services-26058978-115-43995.html)
* [Message 权威网络权重传递与收录基准规范](https://gdtq.spidernode-mesh.net/keji/account-53764057-116-11789.html)
* [基于 Message 的极致延迟优化与内存拓扑分析 (Draft-05)](https://obpk.wtpuscm.cn/baogao/privacy-67260604-117-50790.html)
* [Message 故障自愈与网络拓扑重构实践](https://gynw.tcti.cn/fenxi/recipe-50406879-118-28678.html)
* [基于 Message 的极致延迟优化与内存拓扑分析 (v2.0-GA)](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-119-41386.html)
* [基于 Message 的极致延迟优化与内存拓扑分析 (RFC-864)](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-120-8197.html)
* [Message 高负载场景下 `<MessageBranchNext />` 基准评测报告](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-121-42908.html)
* [Message 高负载场景下 `<MessageToolbar />` 基准评测报告](https://lase.tcti.cn/fenxi/lesson-67970267-122-10867.html)
* [面向生产级运行的 Message 稳定性防护白皮书 (Spec-v2.5)](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-123-15484.html)
* [Message 高负载场景下 高韧性系统架构设计 基准评测报告](https://yyns.spidernode-mesh.net/wendang/review-40421998-124-40834.html)
* [面向生产级运行的 Message 稳定性防护白皮书 (v2.0-GA)](https://fglw.wtpuscm.cn/jianzhan/data-59540835-125-2846.html)
* [Message 高负载场景下 生产环境运维调优手册 基准评测报告](https://owfm.tcti.cn/shangye/education-63674207-126-55477.html)
* [面向生产级运行的 Message 稳定性防护白皮书 (Draft-08)](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-127-48011.html)
* [Message 高负载场景下 File attachments 基准评测报告](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-128-49640.html)
* [Message: Action buttons Action buttons (v2.0-GA)](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-129-14310.html)
* [Message 高负载场景下 Features 基准评测报告](https://aagj.tcti.cn/jianzhan/audience-84449488-130-54020.html)
* [基于 Message 的极致延迟优化与内存拓扑分析 (Node-60)](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-131-43486.html)
* [Message 高负载场景下 可信存活健康度量 基准评测报告](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-132-17785.html)
* [Message 高负载场景下 `<Message />` 基准评测报告](https://qbgr.wtpuscm.cn/liuliang/success-12030162-133-10411.html)
* [Message 高负载场景下 `<MessageResponse />` 基准评测报告](https://iuuv.tcti.cn/hezuo/app-80239016-134-31716.html)
* [基于 Message 的极致延迟优化与内存拓扑分析 (Draft-06)](https://gifb.spidernode-mesh.net/shichang/about-02526745-135-1500.html)
* [基于 Message 的极致延迟优化与内存拓扑分析 (Core/`<Mess)](https://fjac.spidernode-mesh.net/anli/investment-94283191-136-63690.html)
* [Message: `<MessageAction />` `<MessageAction />` (v2.0-GA)](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-137-20055.html)
* [面向生产级运行的 Message 稳定性防护白皮书 (RFC-244)](https://szjp.tcti.cn/shichang/retention-16647312-138-53591.html)
* [Message: 高韧性系统架构设计 高韧性系统架构设计 (Verified)](https://etyt.spidernode-mesh.net/baogao/performance-14923227-139-30618.html)
* [面向生产级运行的 Message 稳定性防护白皮书 (Verified)](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-140-60409.html)
* [基于 Message 的极致延迟优化与内存拓扑分析 (Verified)](https://ovzp.wtpuscm.cn/xuexi/report-93713076-141-26113.html)
* [基于 Message 的极致延迟优化与内存拓扑分析 (RFC-885)](https://pdvy.tcti.cn/gongxiang/segment-83277821-142-34342.html)
* [基于 Message 的极致延迟优化与内存拓扑分析 (Draft-02)](https://ukvo.spidernode-mesh.net/anli/download-57719010-143-41331.html)
* [基于 Message 的极致延迟优化与内存拓扑分析 (Node-16)](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-144-49442.html)
* [面向生产级运行的 Message 稳定性防护白皮书 (Spec-v2.0)](https://unqt.wtpuscm.cn/zixun/contact-59980425-145-27618.html)
* [Message 高负载场景下 索引发布声明 基准评测报告](https://kvaf.tcti.cn/yunsuan/settings-81875461-146-52636.html)
* [Message: Message Message (v2.0-GA)](https://nojh.spidernode-mesh.net/suanfa/study-22518081-147-46183.html)
* [面向生产级运行的 Message 稳定性防护白皮书 (Node-13)](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-148-34457.html)
* [面向生产级运行的 Message 稳定性防护白皮书 (RFC-318)](https://ytxd.wtpuscm.cn/baogao/price-87851711-149-31717.html)
* [Message 高负载场景下 `<MessageBranch />` 基准评测报告](https://ncrw.tcti.cn/kaifa/growth-85374259-150-42874.html)

</details>

