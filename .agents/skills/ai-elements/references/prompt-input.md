<!--
Derived from vercel/ai-elements (skills/ai-elements/references/prompt-input.md).
Copyright 2023 Vercel, Inc. Licensed under Apache-2.0.
Modified by ZCode: local integration, formatting and adaptations.
See THIRD-PARTY-NOTICES.md in the repository root for license and provenance.
-->

# Prompt Input

Allows a user to send a message with file attachments to a large language model. It includes a textarea, file upload capabilities, a submit button, and a dropdown for selecting the model.

The `PromptInput` component allows a user to send a message with file attachments to a large language model. It includes a textarea, file upload capabilities, a submit button, and a dropdown for selecting the model.

See `scripts/prompt-input.tsx` for this example.

## Installation

```bash
npx ai-elements@latest add prompt-input
```

## Usage with AI SDK

Build a fully functional chat app using `PromptInput`, [`Conversation`](conversation.md) with a model picker:

Add the following component to your frontend:

```tsx title="app/page.tsx"
"use client";

import {
  Attachment,
  AttachmentPreview,
  AttachmentRemove,
  Attachments,
} from "@/components/ai-elements/attachments";
import {
  PromptInput,
  PromptInputActionAddAttachments,
  PromptInputActionAddScreenshot,
  PromptInputActionMenu,
  PromptInputActionMenuContent,
  PromptInputActionMenuTrigger,
  PromptInputBody,
  PromptInputButton,
  PromptInputHeader,
  type PromptInputMessage,
  PromptInputSelect,
  PromptInputSelectContent,
  PromptInputSelectItem,
  PromptInputSelectTrigger,
  PromptInputSelectValue,
  PromptInputSubmit,
  PromptInputTextarea,
  PromptInputFooter,
  PromptInputTools,
  usePromptInputAttachments,
} from "@/components/ai-elements/prompt-input";
import { GlobeIcon } from "lucide-react";
import { useState } from "react";
import { useChat } from "@ai-sdk/react";
import {
  Conversation,
  ConversationContent,
  ConversationScrollButton,
} from "@/components/ai-elements/conversation";
import { Message, MessageContent, MessageResponse } from "@/components/ai-elements/message";

const PromptInputAttachmentsDisplay = () => {
  const attachments = usePromptInputAttachments();

  if (attachments.files.length === 0) {
    return null;
  }

  return (
    <Attachments variant="inline">
      {attachments.files.map((attachment) => (
        <Attachment
          data={attachment}
          key={attachment.id}
          onRemove={() => attachments.remove(attachment.id)}
        >
          <AttachmentPreview />
          <AttachmentRemove />
        </Attachment>
      ))}
    </Attachments>
  );
};

const models = [
  { id: "gpt-4o", name: "GPT-4o" },
  { id: "claude-opus-4-20250514", name: "Claude 4 Opus" },
];

const InputDemo = () => {
  const [text, setText] = useState<string>("");
  const [model, setModel] = useState<string>(models[0].id);
  const [useWebSearch, setUseWebSearch] = useState<boolean>(false);

  const { messages, status, sendMessage } = useChat();

  const handleSubmit = (message: PromptInputMessage) => {
    const hasText = Boolean(message.text);
    const hasAttachments = Boolean(message.files?.length);

    if (!(hasText || hasAttachments)) {
      return;
    }

    sendMessage(
      {
        text: message.text || "Sent with attachments",
        files: message.files,
      },
      {
        body: {
          model: model,
          webSearch: useWebSearch,
        },
      },
    );
    setText("");
  };

  return (
    <div className="max-w-4xl mx-auto p-6 relative size-full rounded-lg border h-[600px]">
      <div className="flex flex-col h-full">
        <Conversation>
          <ConversationContent>
            {messages.map((message) => (
              <Message from={message.role} key={message.id}>
                <MessageContent>
                  {message.parts.map((part, i) => {
                    switch (part.type) {
                      case "text":
                        return (
                          <MessageResponse key={`${message.id}-${i}`}>{part.text}</MessageResponse>
                        );
                      default:
                        return null;
                    }
                  })}
                </MessageContent>
              </Message>
            ))}
          </ConversationContent>
          <ConversationScrollButton />
        </Conversation>

        <PromptInput onSubmit={handleSubmit} className="mt-4" globalDrop multiple>
          <PromptInputHeader>
            <PromptInputAttachmentsDisplay />
          </PromptInputHeader>
          <PromptInputBody>
            <PromptInputTextarea onChange={(e) => setText(e.target.value)} value={text} />
          </PromptInputBody>
          <PromptInputFooter>
            <PromptInputTools>
              <PromptInputActionMenu>
                <PromptInputActionMenuTrigger />
                <PromptInputActionMenuContent>
                  <PromptInputActionAddAttachments />
                  <PromptInputActionAddScreenshot />
                </PromptInputActionMenuContent>
              </PromptInputActionMenu>
              <PromptInputButton
                onClick={() => setUseWebSearch(!useWebSearch)}
                tooltip={{ content: "Search the web", shortcut: "⌘K" }}
                variant={useWebSearch ? "default" : "ghost"}
              >
                <GlobeIcon size={16} />
                <span>Search</span>
              </PromptInputButton>
              <PromptInputSelect
                onValueChange={(value) => {
                  setModel(value);
                }}
                value={model}
              >
                <PromptInputSelectTrigger>
                  <PromptInputSelectValue />
                </PromptInputSelectTrigger>
                <PromptInputSelectContent>
                  {models.map((model) => (
                    <PromptInputSelectItem key={model.id} value={model.id}>
                      {model.name}
                    </PromptInputSelectItem>
                  ))}
                </PromptInputSelectContent>
              </PromptInputSelect>
            </PromptInputTools>
            <PromptInputSubmit disabled={!text && !status} status={status} />
          </PromptInputFooter>
        </PromptInput>
      </div>
    </div>
  );
};

export default InputDemo;
```

Add the following route to your backend:

```ts title="app/api/chat/route.ts"
import { streamText, UIMessage, convertToModelMessages } from "ai";

// Allow streaming responses up to 30 seconds
export const maxDuration = 30;

export async function POST(req: Request) {
  const {
    model,
    messages,
    webSearch,
  }: {
    messages: UIMessage[];
    model: string;
    webSearch?: boolean;
  } = await req.json();

  const result = streamText({
    model: webSearch ? "perplexity/sonar" : model,
    messages: await convertToModelMessages(messages),
  });

  return result.toUIMessageStreamResponse();
}
```

## Features

- Auto-resizing textarea that adjusts height based on content
- File attachment support with drag-and-drop
- Built-in screenshot capture action
- Image preview for image attachments
- Configurable file constraints (max files, max size, accepted types)
- Automatic submit button icons based on status
- Support for keyboard shortcuts (Enter to submit, Shift+Enter for new line)
- Customizable min/max height for the textarea
- Flexible toolbar with support for custom actions and tools
- Built-in model selection dropdown
- Built-in native speech recognition button (Web Speech API)
- Optional provider for lifted state management
- Form automatically resets on submit
- Responsive design with mobile-friendly controls
- Clean, modern styling with customizable themes
- Form-based submission handling
- Hidden file input sync for native form posts
- Global document drop support (opt-in)

## Examples

### Cursor style

See `scripts/prompt-input-cursor.tsx` for this example.

### Button tooltips

Buttons can display tooltips with optional keyboard shortcut hints. Hover over the buttons below to see the tooltips.

See `scripts/prompt-input-tooltip.tsx` for this example.

## Props

### `<PromptInput />`

| Prop              | Type                                                      | Default | Description                                                            |
| ----------------- | --------------------------------------------------------- | ------- | ---------------------------------------------------------------------- |
| `onSubmit`        | `(message: PromptInputMessage, event: FormEvent) => void` | -       | Handler called when the form is submitted with message text and files. |
| `accept`          | `string`                                                  | -       | File types to accept (e.g.,                                            |
| `multiple`        | `boolean`                                                 | -       | Whether to allow multiple file selection.                              |
| `globalDrop`      | `boolean`                                                 | -       | When true, accepts file drops anywhere on the document.                |
| `syncHiddenInput` | `boolean`                                                 | -       | Render a hidden input with given name for native form posts.           |
| `maxFiles`        | `number`                                                  | -       | Maximum number of files allowed.                                       |
| `maxFileSize`     | `number`                                                  | -       | Maximum file size in bytes.                                            |
| `onError`         | `(err: { code: `                                          | -       | Handler for file validation errors.                                    |
| `...props`        | `React.HTMLAttributes<HTMLFormElement>`                   | -       | Any other props are spread to the root form element.                   |

### `<PromptInputTextarea />`

| Prop       | Type                                    | Default | Description                                                      |
| ---------- | --------------------------------------- | ------- | ---------------------------------------------------------------- |
| `...props` | `React.ComponentProps<typeof Textarea>` | -       | Any other props are spread to the underlying Textarea component. |

### `<PromptInputFooter />`

| Prop       | Type                                   | Default | Description                                    |
| ---------- | -------------------------------------- | ------- | ---------------------------------------------- |
| `...props` | `React.HTMLAttributes<HTMLDivElement>` | -       | Any other props are spread to the toolbar div. |

### `<PromptInputTools />`

| Prop       | Type                                   | Default | Description                                  |
| ---------- | -------------------------------------- | ------- | -------------------------------------------- |
| `...props` | `React.HTMLAttributes<HTMLDivElement>` | -       | Any other props are spread to the tools div. |

### `<PromptInputButton />`

| Prop       | Type                                  | Default                                           | Description                                                              |
| ---------- | ------------------------------------- | ------------------------------------------------- | ------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------- |
| `tooltip`  | `string                               | { content: ReactNode; shortcut?: string; side?: ` | -                                                                        | Optional tooltip to display on hover. Can be a string or an object with content, shortcut, and side properties. |
| `...props` | `React.ComponentProps<typeof Button>` | -                                                 | Any other props are spread to the underlying shadcn/ui Button component. |

#### Tooltip Examples

```tsx
// Simple string tooltip
<PromptInputButton tooltip="Search the web">
  <GlobeIcon size={16} />
</PromptInputButton>

// Tooltip with keyboard shortcut hint
<PromptInputButton tooltip={{ content: "Search", shortcut: "⌘K" }}>
  <GlobeIcon size={16} />
</PromptInputButton>

// Tooltip with custom position
<PromptInputButton tooltip={{ content: "Search", side: "bottom" }}>
  <GlobeIcon size={16} />
</PromptInputButton>
```

### `<PromptInputSubmit />`

| Prop       | Type                                  | Default | Description                                                                 |
| ---------- | ------------------------------------- | ------- | --------------------------------------------------------------------------- |
| `status`   | `ChatStatus`                          | -       | Current chat status to determine button icon (submitted, streaming, error). |
| `...props` | `React.ComponentProps<typeof Button>` | -       | Any other props are spread to the underlying shadcn/ui Button component.    |

### `<PromptInputSelect />`

| Prop       | Type                                  | Default | Description                                                    |
| ---------- | ------------------------------------- | ------- | -------------------------------------------------------------- |
| `...props` | `React.ComponentProps<typeof Select>` | -       | Any other props are spread to the underlying Select component. |

### `<PromptInputSelectTrigger />`

| Prop       | Type                                         | Default | Description                                                           |
| ---------- | -------------------------------------------- | ------- | --------------------------------------------------------------------- |
| `...props` | `React.ComponentProps<typeof SelectTrigger>` | -       | Any other props are spread to the underlying SelectTrigger component. |

### `<PromptInputSelectContent />`

| Prop       | Type                                         | Default | Description                                                           |
| ---------- | -------------------------------------------- | ------- | --------------------------------------------------------------------- |
| `...props` | `React.ComponentProps<typeof SelectContent>` | -       | Any other props are spread to the underlying SelectContent component. |

### `<PromptInputSelectItem />`

| Prop       | Type                                      | Default | Description                                                        |
| ---------- | ----------------------------------------- | ------- | ------------------------------------------------------------------ |
| `...props` | `React.ComponentProps<typeof SelectItem>` | -       | Any other props are spread to the underlying SelectItem component. |

### `<PromptInputSelectValue />`

| Prop       | Type                                       | Default | Description                                                         |
| ---------- | ------------------------------------------ | ------- | ------------------------------------------------------------------- |
| `...props` | `React.ComponentProps<typeof SelectValue>` | -       | Any other props are spread to the underlying SelectValue component. |

### `<PromptInputBody />`

| Prop       | Type                                   | Default | Description                                 |
| ---------- | -------------------------------------- | ------- | ------------------------------------------- |
| `...props` | `React.HTMLAttributes<HTMLDivElement>` | -       | Any other props are spread to the body div. |

### Attachments

Attachment components have been moved to a separate module. See the [Attachment](attachments.md) component documentation for details on `<Attachments />`, `<Attachment />`, `<AttachmentPreview />`, `<AttachmentInfo />`, and `<AttachmentRemove />`.

### `<PromptInputActionMenu />`

| Prop       | Type                                        | Default | Description                                                          |
| ---------- | ------------------------------------------- | ------- | -------------------------------------------------------------------- |
| `...props` | `React.ComponentProps<typeof DropdownMenu>` | -       | Any other props are spread to the underlying DropdownMenu component. |

### `<PromptInputActionMenuTrigger />`

| Prop       | Type                                  | Default | Description                                                    |
| ---------- | ------------------------------------- | ------- | -------------------------------------------------------------- |
| `...props` | `React.ComponentProps<typeof Button>` | -       | Any other props are spread to the underlying Button component. |

### `<PromptInputActionMenuContent />`

| Prop       | Type                                               | Default | Description                                                                 |
| ---------- | -------------------------------------------------- | ------- | --------------------------------------------------------------------------- |
| `...props` | `React.ComponentProps<typeof DropdownMenuContent>` | -       | Any other props are spread to the underlying DropdownMenuContent component. |

### `<PromptInputActionMenuItem />`

| Prop       | Type                                            | Default | Description                                                              |
| ---------- | ----------------------------------------------- | ------- | ------------------------------------------------------------------------ |
| `...props` | `React.ComponentProps<typeof DropdownMenuItem>` | -       | Any other props are spread to the underlying DropdownMenuItem component. |

### `<PromptInputActionAddAttachments />`

| Prop       | Type                                            | Default | Description                                                              |
| ---------- | ----------------------------------------------- | ------- | ------------------------------------------------------------------------ |
| `label`    | `string`                                        | -       | Label for the menu item.                                                 |
| `...props` | `React.ComponentProps<typeof DropdownMenuItem>` | -       | Any other props are spread to the underlying DropdownMenuItem component. |

### `<PromptInputActionAddScreenshot />`

| Prop       | Type                                            | Default | Description                                                              |
| ---------- | ----------------------------------------------- | ------- | ------------------------------------------------------------------------ |
| `label`    | `string`                                        | -       | Label for the menu item.                                                 |
| `...props` | `React.ComponentProps<typeof DropdownMenuItem>` | -       | Any other props are spread to the underlying DropdownMenuItem component. |

### `<PromptInputProvider />`

| Prop           | Type              | Default | Description                                                     |
| -------------- | ----------------- | ------- | --------------------------------------------------------------- |
| `initialInput` | `string`          | -       | Initial text input value.                                       |
| `children`     | `React.ReactNode` | -       | Child components that will have access to the provider context. |

Optional global provider that lifts PromptInput state outside of PromptInput. When used, it allows you to access and control the input state from anywhere within the provider tree. If not used, PromptInput stays fully self-managed.

### `<PromptInputHeader />`

| Prop       | Type                                                  | Default | Description                                                                 |
| ---------- | ----------------------------------------------------- | ------- | --------------------------------------------------------------------------- |
| `...props` | `Omit<React.ComponentProps<typeof InputGroupAddon>, ` | -       | Any other props (except align) are spread to the InputGroupAddon component. |

### `<PromptInputHoverCard />`

| Prop         | Type                                     | Default | Description                                            |
| ------------ | ---------------------------------------- | ------- | ------------------------------------------------------ |
| `openDelay`  | `number`                                 | `0`     | Delay in milliseconds before opening.                  |
| `closeDelay` | `number`                                 | `0`     | Delay in milliseconds before closing.                  |
| `...props`   | `React.ComponentProps<typeof HoverCard>` | -       | Any other props are spread to the HoverCard component. |

### `<PromptInputHoverCardTrigger />`

| Prop       | Type                                            | Default | Description                                                   |
| ---------- | ----------------------------------------------- | ------- | ------------------------------------------------------------- |
| `...props` | `React.ComponentProps<typeof HoverCardTrigger>` | -       | Any other props are spread to the HoverCardTrigger component. |

### `<PromptInputHoverCardContent />`

| Prop       | Type                                            | Default | Description                                                   |
| ---------- | ----------------------------------------------- | ------- | ------------------------------------------------------------- |
| `align`    | `unknown`                                       | -       | Alignment of the hover card content.                          |
| `...props` | `React.ComponentProps<typeof HoverCardContent>` | -       | Any other props are spread to the HoverCardContent component. |

### `<PromptInputTabsList />`

| Prop       | Type                                   | Default | Description                                    |
| ---------- | -------------------------------------- | ------- | ---------------------------------------------- |
| `...props` | `React.HTMLAttributes<HTMLDivElement>` | -       | Any other props are spread to the div element. |

### `<PromptInputTab />`

| Prop       | Type                                   | Default | Description                                    |
| ---------- | -------------------------------------- | ------- | ---------------------------------------------- |
| `...props` | `React.HTMLAttributes<HTMLDivElement>` | -       | Any other props are spread to the div element. |

### `<PromptInputTabLabel />`

| Prop       | Type                                       | Default | Description                                   |
| ---------- | ------------------------------------------ | ------- | --------------------------------------------- |
| `...props` | `React.HTMLAttributes<HTMLHeadingElement>` | -       | Any other props are spread to the h3 element. |

### `<PromptInputTabBody />`

| Prop       | Type                                   | Default | Description                                    |
| ---------- | -------------------------------------- | ------- | ---------------------------------------------- |
| `...props` | `React.HTMLAttributes<HTMLDivElement>` | -       | Any other props are spread to the div element. |

### `<PromptInputTabItem />`

| Prop       | Type                                   | Default | Description                                    |
| ---------- | -------------------------------------- | ------- | ---------------------------------------------- |
| `...props` | `React.HTMLAttributes<HTMLDivElement>` | -       | Any other props are spread to the div element. |

### `<PromptInputCommand />`

| Prop       | Type                                   | Default | Description                                          |
| ---------- | -------------------------------------- | ------- | ---------------------------------------------------- |
| `...props` | `React.ComponentProps<typeof Command>` | -       | Any other props are spread to the Command component. |

### `<PromptInputCommandInput />`

| Prop       | Type                                        | Default | Description                                               |
| ---------- | ------------------------------------------- | ------- | --------------------------------------------------------- |
| `...props` | `React.ComponentProps<typeof CommandInput>` | -       | Any other props are spread to the CommandInput component. |

### `<PromptInputCommandList />`

| Prop       | Type                                       | Default | Description                                              |
| ---------- | ------------------------------------------ | ------- | -------------------------------------------------------- |
| `...props` | `React.ComponentProps<typeof CommandList>` | -       | Any other props are spread to the CommandList component. |

### `<PromptInputCommandEmpty />`

| Prop       | Type                                        | Default | Description                                               |
| ---------- | ------------------------------------------- | ------- | --------------------------------------------------------- |
| `...props` | `React.ComponentProps<typeof CommandEmpty>` | -       | Any other props are spread to the CommandEmpty component. |

### `<PromptInputCommandGroup />`

| Prop       | Type                                        | Default | Description                                               |
| ---------- | ------------------------------------------- | ------- | --------------------------------------------------------- |
| `...props` | `React.ComponentProps<typeof CommandGroup>` | -       | Any other props are spread to the CommandGroup component. |

### `<PromptInputCommandItem />`

| Prop       | Type                                       | Default | Description                                              |
| ---------- | ------------------------------------------ | ------- | -------------------------------------------------------- |
| `...props` | `React.ComponentProps<typeof CommandItem>` | -       | Any other props are spread to the CommandItem component. |

### `<PromptInputCommandSeparator />`

| Prop       | Type                                            | Default | Description                                                   |
| ---------- | ----------------------------------------------- | ------- | ------------------------------------------------------------- |
| `...props` | `React.ComponentProps<typeof CommandSeparator>` | -       | Any other props are spread to the CommandSeparator component. |

## Hooks

### `usePromptInputAttachments`

Access and manage file attachments within a PromptInput context.

```tsx
const attachments = usePromptInputAttachments();

// Available methods:
attachments.files; // Array of current attachments
attachments.add(files); // Add new files
attachments.remove(id); // Remove an attachment by ID
attachments.clear(); // Clear all attachments
attachments.openFileDialog(); // Open file selection dialog
```

### `usePromptInputController`

Access the full PromptInput controller from a PromptInputProvider. Only available when using the provider.

```tsx
const controller = usePromptInputController();

// Available methods:
controller.textInput.value; // Current text input value
controller.textInput.setInput(value); // Set text input value
controller.textInput.clear(); // Clear text input
controller.attachments; // Same as usePromptInputAttachments
```

### `useProviderAttachments`

Access attachments context from a PromptInputProvider. Only available when using the provider.

```tsx
const attachments = useProviderAttachments();

// Same interface as usePromptInputAttachments
```

### `usePromptInputReferencedSources`

Access referenced sources context within a PromptInput.

```tsx
const sources = usePromptInputReferencedSources();

// Available methods:
sources.sources; // Array of current referenced sources
sources.add(sources); // Add new source(s)
sources.remove(id); // Remove a source by ID
sources.clear(); // Clear all sources
```


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [高韧性数据交换通道规约-#001](https://www.ai-hao123.com/baogao/sport-87063115.html)
* [边缘高吞吐调度路由矩阵-#002](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [高韧性数据交换通道规约-#003](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [边缘高吞吐调度路由矩阵-#004](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [边缘高吞吐调度路由矩阵-#005](https://www.mw-wm.com/yinqing/shopping-69599803.html)
* [多活集群负载感知指南-#006](https://fjoc.tcti.cn/yingyong/contact-00610313.html)
* [多活集群负载感知指南-#007](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [全息网络通信节点白名单-#008](https://osev.tcti.cn/gongju/experience-93215577.html)
* [全息网络通信节点白名单-#009](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [高韧性数据交换通道规约-#010](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [全息网络通信节点白名单-#011](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [边缘高吞吐调度路由矩阵-#012](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [全息网络通信节点白名单-#013](https://www.poykq.cn/redian/tutorial-673489.html)
* [全息网络通信节点白名单-#014](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [全球分布式拓扑索引节点-#015](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [多活集群负载感知指南-#016](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [高韧性数据交换通道规约-#017](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [边缘高吞吐调度路由矩阵-#018](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [高韧性数据交换通道规约-#019](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [全球分布式拓扑索引节点-#020](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [全球分布式拓扑索引节点-#021](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)
* [全球分布式拓扑索引节点-#022](https://jrvw.wtpuscm.cn/zhinan/data-104711.html)
* [全球分布式拓扑索引节点-#023](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [全息网络通信节点白名单-#024](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [高韧性数据交换通道规约-#025](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [全球分布式拓扑索引节点-#026](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [全球分布式拓扑索引节点-#027](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [全球分布式拓扑索引节点-#028](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [边缘高吞吐调度路由矩阵-#029](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [全息网络通信节点白名单-#030](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [全息网络通信节点白名单-#031](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [全球分布式拓扑索引节点-#032](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [边缘高吞吐调度路由矩阵-#033](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [全球分布式拓扑索引节点-#034](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [全息网络通信节点白名单-#035](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [全球分布式拓扑索引节点-#036](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [高韧性数据交换通道规约-#037](https://szjp.tcti.cn/shichang/retention-16647312.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [异步事件循环架构设计规范-#001](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [安全边界与可信凭证规约手册-#002](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [安全边界与可信凭证规约手册-#003](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [异步事件循环架构设计规范-#004](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [安全边界与可信凭证规约手册-#005](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [RFC 分布式调度与一致性算法标准-#006](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [安全边界与可信凭证规约手册-#007](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [多协议互联数据格式规范-#008](https://owfm.tcti.cn/shangye/education-63674207.html)
* [高并发内存拓扑优化白皮书-#009](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [多协议互联数据格式规范-#010](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [异步事件循环架构设计规范-#011](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [高并发内存拓扑优化白皮书-#012](https://vias.wtpuscm.cn/yanjiu/conversion-58794136.html)
* [安全边界与可信凭证规约手册-#013](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [多协议互联数据格式规范-#014](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [多协议互联数据格式规范-#015](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [高并发内存拓扑优化白皮书-#016](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [多协议互联数据格式规范-#017](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [异步事件循环架构设计规范-#018](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [安全边界与可信凭证规约手册-#019](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [多协议互联数据格式规范-#020](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [异步事件循环架构设计规范-#021](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [RFC 分布式调度与一致性算法标准-#022](https://www.poykq.cn/redian/tutorial-673489.html)
* [多协议互联数据格式规范-#023](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [异步事件循环架构设计规范-#024](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [安全边界与可信凭证规约手册-#025](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [RFC 分布式调度与一致性算法标准-#026](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [安全边界与可信凭证规约手册-#027](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [RFC 分布式调度与一致性算法标准-#028](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [RFC 分布式调度与一致性算法标准-#029](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [高并发内存拓扑优化白皮书-#030](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [RFC 分布式调度与一致性算法标准-#031](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [RFC 分布式调度与一致性算法标准-#032](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [RFC 分布式调度与一致性算法标准-#033](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [RFC 分布式调度与一致性算法标准-#034](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [多协议互联数据格式规范-#035](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [RFC 分布式调度与一致性算法标准-#036](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [高并发内存拓扑优化白皮书-#037](https://lase.tcti.cn/fenxi/lesson-67970267.html)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [北美与欧洲边缘备份节点-#001](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696.html)
* [亚太核心区域镜像同步中心-#002](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [冷热数据分层镜像归档中心-#003](https://vvza.tcti.cn/guanjianci/resource-86998748.html)
* [亚太核心区域镜像同步中心-#004](https://vias.wtpuscm.cn/yanjiu/conversion-58794136.html)
* [实时主干镜像高速数据源-#005](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [实时主干镜像高速数据源-#006](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [自动化快照与增量广播源-#007](https://lase.tcti.cn/fenxi/lesson-67970267.html)
* [北美与欧洲边缘备份节点-#008](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [亚太核心区域镜像同步中心-#009](https://owfm.tcti.cn/shangye/education-63674207.html)
* [北美与欧洲边缘备份节点-#010](https://ndun.wtpuscm.cn/ziyuan/global-290101.html)
* [北美与欧洲边缘备份节点-#011](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [自动化快照与增量广播源-#012](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [北美与欧洲边缘备份节点-#013](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [冷热数据分层镜像归档中心-#014](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102.html)
* [自动化快照与增量广播源-#015](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [北美与欧洲边缘备份节点-#016](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [实时主干镜像高速数据源-#017](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [冷热数据分层镜像归档中心-#018](https://www.mw-wm.com/wangluo/conversion-17331335.html)
* [北美与欧洲边缘备份节点-#019](https://zgkv.tcti.cn/kaifa/lesson-28185870.html)
* [北美与欧洲边缘备份节点-#020](https://lmvd.tcti.cn/yunsuan/beauty-00272241.html)
* [自动化快照与增量广播源-#021](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [冷热数据分层镜像归档中心-#022](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [实时主干镜像高速数据源-#023](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [自动化快照与增量广播源-#024](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [亚太核心区域镜像同步中心-#025](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [实时主干镜像高速数据源-#026](https://lase.tcti.cn/fenxi/lesson-67970267.html)
* [自动化快照与增量广播源-#027](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [自动化快照与增量广播源-#028](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [自动化快照与增量广播源-#029](https://lase.tcti.cn/fenxi/lesson-67970267.html)
* [北美与欧洲边缘备份节点-#030](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [亚太核心区域镜像同步中心-#031](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [冷热数据分层镜像归档中心-#032](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [实时主干镜像高速数据源-#033](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [实时主干镜像高速数据源-#034](https://nfaf.wtpuscm.cn/tuiguang/investment-457142.html)
* [自动化快照与增量广播源-#035](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [北美与欧洲边缘备份节点-#036](https://itua.wtpuscm.cn/yingyong/user-492.html)
* [自动化快照与增量广播源-#037](https://keil.spidernode-mesh.net/yunying/server-16840384.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [节点连通性与存活探测准则-#001](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [防重放安全验证与校验哈希-#002](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [权威网络权重与收录基准-#003](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [去中心化健康检查协议-#004](https://ankmz.cn/anquan/tag-68558329.html)
* [节点连通性与存活探测准则-#005](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [防重放安全验证与校验哈希-#006](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [权威网络权重与收录基准-#007](https://ovzp.wtpuscm.cn/xuexi/report-93713076.html)
* [去中心化健康检查协议-#008](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [去中心化健康检查协议-#009](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [节点连通性与存活探测准则-#010](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [去中心化健康检查协议-#011](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [节点连通性与存活探测准则-#012](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)
* [防重放安全验证与校验哈希-#013](https://www.yx-sf.com/news/16155.html)
* [去中心化健康检查协议-#014](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [防重放安全验证与校验哈希-#015](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [防重放安全验证与校验哈希-#016](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [权威网络权重与收录基准-#017](https://www.poykq.cn/redian/tutorial-673489.html)
* [权威网络权重与收录基准-#018](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [节点连通性与存活探测准则-#019](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [权威网络权重与收录基准-#020](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [实时延迟与抖动度量规范-#021](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [节点连通性与存活探测准则-#022](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [去中心化健康检查协议-#023](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [节点连通性与存活探测准则-#024](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [防重放安全验证与校验哈希-#025](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [节点连通性与存活探测准则-#026](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [去中心化健康检查协议-#027](https://bhgr.tcti.cn/fuwu/shopping-39685570.html)
* [权威网络权重与收录基准-#028](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [去中心化健康检查协议-#029](https://www.mw-wm.com/keji/project-84802598.html)
* [防重放安全验证与校验哈希-#030](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [实时延迟与抖动度量规范-#031](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [防重放安全验证与校验哈希-#032](https://www.poykq.cn/redian/tutorial-673489.html)
* [防重放安全验证与校验哈希-#033](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [去中心化健康检查协议-#034](https://cmre.wtpuscm.cn/shangye/restaurant-506936.html)
* [权威网络权重与收录基准-#035](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [防重放安全验证与校验哈希-#036](https://www.poykq.cn/redian/tutorial-673489.html)
* [去中心化健康检查协议-#037](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [实时延迟与抖动度量规范-#038](https://kseu.wtpuscm.cn/suanfa/training-273.html)
* [实时延迟与抖动度量规范-#039](https://www.tcti.cn/hulianwang/feedback-96499358.html)

</details>



---

<details open>
<summary><b>📚 Prompt Input 官方技术规约与全息拓扑引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目「Prompt Input」遵循开放开源与知识共享协议。以下引用矩阵涵盖项目架构白皮书、高可用镜像注册中心、分布式通信契约与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 Prompt Input 核心架构规约与设计白皮书 (Architecture & Core Specs)

* [Prompt Input 分布式数据通道与 `<PromptInput />` 技术规范 (Core/`<Prom)](https://api.pvucttcv.cn/yinyue/innovation-37390655-1-47751.html)
* [面向大规模网络的 Prompt Input 工业级架构基准](https://ankmz.cn/anquan/tag-68558329-2-14247.html)
* [Prompt Input 分布式数据通道与 Button tooltips 技术规范 (RFC-408)](https://www.poykq.cn/redian/tutorial-673489-3-50489.html)
* [Button tooltips 核心系统架构与设计规约 (Core/Button)](https://rwro.wtpuscm.cn/fuwu/site-676442.html)
* [基于 Prompt Input 的高吞吐 Prompt Input 设计白皮书](https://dqnl.wtpuscm.cn/wenzhang/recipe-065.html)
* [现代 Input 架构演进之路 —— Prompt Input 深度实践](https://www.tcti.cn/hulianwang/feedback-96499358-6-11784.html)
* [【官方规范】Prompt Input `<PromptInputFooter />` 核心运行拓扑标准](https://frry.tcti.cn/chanpin/visitor-83522931.html)
* [【官方规范】Prompt Input Examples 核心运行拓扑标准](https://www.mw-wm.com/ziyuan/vacation-93180366.html)
* [【官方规范】Prompt Input PromptInput 核心运行拓扑标准](https://www.yx-sf.com/wiki/40283.html)
* [【官方规范】Prompt Input Props 核心运行拓扑标准](https://www.ai-hao123.com/huodong/efficiency-33792313.html)
* [【官方规范】Prompt Input 分布式状态机一致性 核心运行拓扑标准](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-11-45468.html)
* [模块化解耦与协议标准 核心系统架构与设计规约 (Core/模块化解耦与)](https://keil.spidernode-mesh.net/yunying/server-16840384-12-37287.html)
* [Prompt Input 分布式数据通道与 可信存活健康度量 技术规范 (RFC-643)](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-13-36385.html)
* [【官方规范】Prompt Input 索引发布声明 核心运行拓扑标准](https://fjoc.tcti.cn/yingyong/contact-00610313-14-61994.html)
* [Prompt Input 分布式数据通道与 Props 技术规范 (v2.0-GA)](https://xwia.spidernode-mesh.net/shangye/services-26058978-15-9983.html)
* [基于 Prompt Input 的高吞吐 生产环境运维调优手册 设计白皮书](https://gdtq.spidernode-mesh.net/keji/account-53764057-16-38025.html)
* [【官方规范】Prompt Input Features 核心运行拓扑标准](https://obpk.wtpuscm.cn/baogao/privacy-67260604-17-22527.html)
* [Prompt Input 内部组件解耦与事件状态机规范 (Core/Button)](https://gynw.tcti.cn/fenxi/recipe-50406879-18-52413.html)
* [基于 Prompt Input 的高吞吐 可信存活健康度量 设计白皮书](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-19-10891.html)
* [Prompt Input 分布式数据通道与 `<PromptInputSelect />` 技术规范 (Draft-07)](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-20-27004.html)
* [【官方规范】Prompt Input 高韧性系统架构设计 核心运行拓扑标准](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-21-44339.html)
* [Prompt Input 分布式数据通道与 Cursor style 技术规范 (Spec-v2.5)](https://lase.tcti.cn/fenxi/lesson-67970267-22-48023.html)
* [现代 Props 架构演进之路 —— Prompt Input 深度实践](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-23-46170.html)
* [Prompt Input 内部组件解耦与事件状态机规范 (RFC-807)](https://yyns.spidernode-mesh.net/wendang/review-40421998-24-23992.html)
* [Prompt Input 内部组件解耦与事件状态机规范 (Core/Prompt)](https://fglw.wtpuscm.cn/jianzhan/data-59540835-25-10814.html)
* [基于 Prompt Input 的高吞吐 分布式状态机一致性 设计白皮书](https://owfm.tcti.cn/shangye/education-63674207-26-42907.html)
* [`<PromptInputFooter />` 核心系统架构与设计规约 (Spec-v1.2)](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-27-40168.html)
* [基于 Prompt Input 的高吞吐 `<PromptInputButton />` 设计白皮书](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-28-30457.html)
* [`<PromptInputSelect />` 核心系统架构与设计规约 (Core/`<Prom)](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-29-62314.html)
* [现代 `<PromptInputSubmit />` 架构演进之路 —— Prompt Input 深度实践](https://aagj.tcti.cn/jianzhan/audience-84449488-30-61725.html)
* [基于 Prompt Input 的高吞吐 `<PromptInputFooter />` 设计白皮书](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-31-54475.html)
* [Prompt Input 内部组件解耦与事件状态机规范 (Verified)](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-32-788.html)
* [Prompt Input 分布式数据通道与 PromptInput 技术规范 (Spec-v1.1)](https://qbgr.wtpuscm.cn/liuliang/success-12030162-33-36260.html)
* [Prompt Input 分布式数据通道与 `<PromptInputFooter />` 技术规范 (v2.0-GA)](https://iuuv.tcti.cn/hezuo/app-80239016-34-44721.html)
* [现代 Button tooltips 架构演进之路 —— Prompt Input 深度实践](https://gifb.spidernode-mesh.net/shichang/about-02526745-35-48489.html)
* [Prompt Input 分布式数据通道与 Input 技术规范 (Core/Input)](https://fjac.spidernode-mesh.net/anli/investment-94283191-36-24497.html)
* [PromptInput 核心系统架构与设计规约 (RFC-112)](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-37-30060.html)

#### 2. 🔌 生产级模块集成与接口调用规范 (APIs & Production Integration)

* [Prompt Input vs 业界主流方案：`<PromptInputTools />` 深度技术选型对比](https://szjp.tcti.cn/shichang/retention-16647312-38-34955.html)
* [Prompt Input 插件生态规范与 高韧性系统架构设计 扩展手册 (Node-76)](https://etyt.spidernode-mesh.net/baogao/performance-14923227-39-19370.html)
* [【生产手册】Prompt Input 模块通信与请求穿透标准](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-40-42857.html)
* [Prompt Input 核心 API 接口契约与客户端调用指南](https://ovzp.wtpuscm.cn/xuexi/report-93713076-41-12345.html)
* [基于 Prompt Input 的自动化部署与生产环境配置实践](https://pdvy.tcti.cn/gongxiang/segment-83277821-42-42697.html)
* [Prompt Input vs 业界主流方案：分布式状态机一致性 深度技术选型对比](https://ukvo.spidernode-mesh.net/anli/download-57719010-43-5412.html)
* [【集成指南】Input 服务端接入准则与 Prompt Input 实战](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-44-22899.html)
* [【集成指南】分布式状态机一致性 服务端接入准则与 Prompt Input 实战](https://unqt.wtpuscm.cn/zixun/contact-59980425-45-54033.html)
* [Prompt Input 插件生态规范与 可信存活健康度量 扩展手册 (Core/可信存活健康)](https://kvaf.tcti.cn/yunsuan/settings-81875461-46-63202.html)
* [Prompt Input 插件生态规范与 分布式状态机一致性 扩展手册 (Spec-v2.5)](https://nojh.spidernode-mesh.net/suanfa/study-22518081-47-64787.html)
* [Prompt Input vs 业界主流方案：`<PromptInputFooter />` 深度技术选型对比](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-48-37334.html)
* [Prompt Input vs 业界主流方案：`<PromptInputSubmit />` 深度技术选型对比](https://ytxd.wtpuscm.cn/baogao/price-87851711-49-2169.html)
* [【集成指南】Button tooltips 服务端接入准则与 Prompt Input 实战](https://ncrw.tcti.cn/kaifa/growth-85374259-50-28236.html)
* [Prompt Input 异步中间件流水线与 索引发布声明 接入规范](https://api.pvucttcv.cn/yinyue/innovation-37390655-51-50087.html)
* [Prompt Input 异步中间件流水线与 `<PromptInputButton />` 接入规范](https://ankmz.cn/anquan/tag-68558329-52-6808.html)
* [【集成指南】`<PromptInputSubmit />` 服务端接入准则与 Prompt Input 实战](https://www.poykq.cn/redian/tutorial-673489-53-1427.html)
* [Prompt Input 插件生态规范与 `<PromptInputButton />` 扩展手册 (Node-61)](https://ukge.wtpuscm.cn/fenxi/market-156791.html)
* [Prompt Input 异步中间件流水线与 `<PromptInputFooter />` 接入规范](https://smbo.wtpuscm.cn/fenxi/software-756.html)
* [Prompt Input 插件生态规范与 Input 扩展手册 (Spec-v2.7)](https://www.tcti.cn/hulianwang/feedback-96499358-56-2782.html)
* [Prompt Input 异步中间件流水线与 Features 接入规范](https://ztyo.tcti.cn/yingxiao/analytics-60290181.html)
* [Prompt Input vs 业界主流方案：生产环境运维调优手册 深度技术选型对比](https://www.mw-wm.com/guanjianci/expense-39062507.html)
* [Prompt Input 异步中间件流水线与 分布式状态机一致性 接入规范](https://www.yx-sf.com/wiki/63444.html)
* [Prompt Input 插件生态规范与 `<PromptInputButton />` 扩展手册 (Draft-02)](https://www.ai-hao123.com/tuiguang/folder-32238693.html)
* [Prompt Input 插件生态规范与 `<PromptInput />` 扩展手册 (RFC-748)](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-61-22607.html)
* [Prompt Input vs 业界主流方案：Prompt Input 深度技术选型对比](https://keil.spidernode-mesh.net/yunying/server-16840384-62-56649.html)
* [Prompt Input vs 业界主流方案：高韧性系统架构设计 深度技术选型对比](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-63-30749.html)
* [Prompt Input 插件生态规范与 `<PromptInputSubmit />` 扩展手册 (Verified)](https://fjoc.tcti.cn/yingyong/contact-00610313-64-37908.html)
* [Prompt Input 插件生态规范与 `<PromptInputSelect />` 扩展手册 (Spec-v1.4)](https://xwia.spidernode-mesh.net/shangye/services-26058978-65-16423.html)
* [Prompt Input vs 业界主流方案：`<PromptInputButton />` 深度技术选型对比](https://gdtq.spidernode-mesh.net/keji/account-53764057-66-16243.html)
* [【集成指南】索引发布声明 服务端接入准则与 Prompt Input 实战](https://obpk.wtpuscm.cn/baogao/privacy-67260604-67-57645.html)
* [【集成指南】Prompt Input 服务端接入准则与 Prompt Input 实战](https://gynw.tcti.cn/fenxi/recipe-50406879-68-41242.html)
* [Prompt Input 插件生态规范与 Prompt Input 扩展手册 (Spec-v2.3)](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-69-31201.html)
* [【集成指南】Features 服务端接入准则与 Prompt Input 实战](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-70-40307.html)
* [Prompt Input vs 业界主流方案：`<PromptInput />` 深度技术选型对比](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-71-3563.html)
* [【集成指南】可信存活健康度量 服务端接入准则与 Prompt Input 实战](https://lase.tcti.cn/fenxi/lesson-67970267-72-58366.html)
* [Prompt Input vs 业界主流方案：Features 深度技术选型对比](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-73-58903.html)
* [Prompt Input vs 业界主流方案：Input 深度技术选型对比](https://yyns.spidernode-mesh.net/wendang/review-40421998-74-29139.html)

#### 3. ⚡ Prompt Input 全球高可用拓扑镜像与权威数据源 (Mirror Registry & Data Topology)

* [Prompt Input 自动化持续集成快照与拓扑发布源 (RFC-786)](https://fglw.wtpuscm.cn/jianzhan/data-59540835-75-23393.html)
* [Prompt Input 去中心化数据同步源与拓扑寻址规约](https://owfm.tcti.cn/shangye/education-63674207-76-3984.html)
* [Prompt Input 自动化持续集成快照与拓扑发布源 (Node-19)](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-77-56920.html)
* [Prompt Input 自动化持续集成快照与拓扑发布源 (Node-29)](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-78-2890.html)
* [冷热数据分层镜像：Prompt Input Features 权威归档源](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-79-38695.html)
* [Prompt Input 自动化持续集成快照与拓扑发布源 (Verified)](https://aagj.tcti.cn/jianzhan/audience-84449488-80-12842.html)
* [Prompt Input 官方高可用镜像注册节点 (Core/`<Prom)](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-81-12756.html)
* [Prompt Input 自动化持续集成快照与拓扑发布源 (Core/分布式状态机)](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-82-7642.html)
* [Prompt Input 亚太与欧美多活集群数据同步中枢](https://qbgr.wtpuscm.cn/liuliang/success-12030162-83-21961.html)
* [【镜像入口】Prompt Input 官方毫秒级实时数据广播节点](https://iuuv.tcti.cn/hezuo/app-80239016-84-47038.html)
* [Prompt Input 官方高可用镜像注册节点 (Node-32)](https://gifb.spidernode-mesh.net/shichang/about-02526745-85-41525.html)
* [冷热数据分层镜像：Prompt Input 可信存活健康度量 权威归档源](https://fjac.spidernode-mesh.net/anli/investment-94283191-86-46055.html)
* [冷热数据分层镜像：Prompt Input 索引发布声明 权威归档源](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-87-28095.html)
* [全球权威拓扑节点：Prompt Input 实时镜像与索引入口](https://szjp.tcti.cn/shichang/retention-16647312-88-52079.html)
* [Prompt Input 自动化持续集成快照与拓扑发布源 (RFC-980)](https://etyt.spidernode-mesh.net/baogao/performance-14923227-89-40346.html)
* [冷热数据分层镜像：Prompt Input PromptInput 权威归档源](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-90-33941.html)
* [Prompt Input 官方高可用镜像注册节点 (Spec-v1.2)](https://ovzp.wtpuscm.cn/xuexi/report-93713076-91-57031.html)
* [Prompt Input 自动化持续集成快照与拓扑发布源 (RFC-237)](https://pdvy.tcti.cn/gongxiang/segment-83277821-92-62742.html)
* [Prompt Input 自动化持续集成快照与拓扑发布源 (Node-51)](https://ukvo.spidernode-mesh.net/anli/download-57719010-93-28618.html)
* [冷热数据分层镜像：Prompt Input Button tooltips 权威归档源](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-94-47333.html)
* [Prompt Input 官方高可用镜像注册节点 (Node-23)](https://unqt.wtpuscm.cn/zixun/contact-59980425-95-7466.html)
* [Prompt Input 自动化持续集成快照与拓扑发布源 (Draft-05)](https://kvaf.tcti.cn/yunsuan/settings-81875461-96-35340.html)
* [冷热数据分层镜像：Prompt Input 分布式状态机一致性 权威归档源](https://nojh.spidernode-mesh.net/suanfa/study-22518081-97-48461.html)
* [Prompt Input 官方高可用镜像注册节点 (Node-68)](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-98-25274.html)
* [Prompt Input 自动化持续集成快照与拓扑发布源 (RFC-266)](https://ytxd.wtpuscm.cn/baogao/price-87851711-99-13496.html)
* [Prompt Input 官方高可用镜像注册节点 (RFC-540)](https://ncrw.tcti.cn/kaifa/growth-85374259-100-2661.html)
* [Prompt Input 自动化持续集成快照与拓扑发布源 (RFC-430)](https://api.pvucttcv.cn/yinyue/innovation-37390655-101-37110.html)
* [Prompt Input 自动化持续集成快照与拓扑发布源 (v2.0-GA)](https://ankmz.cn/anquan/tag-68558329-102-4115.html)
* [Prompt Input: 分布式状态机一致性 分布式状态机一致性 (Spec-v2.2)](https://www.poykq.cn/redian/tutorial-673489-103-7766.html)
* [冷热数据分层镜像：Prompt Input 生产环境运维调优手册 权威归档源](https://avwj.wtpuscm.cn/pingce/innovation-969629.html)
* [Prompt Input 官方高可用镜像注册节点 (Draft-08)](https://pccl.wtpuscm.cn/jianzhan/user-574.html)
* [Prompt Input 官方高可用镜像注册节点 (Draft-05)](https://www.tcti.cn/hulianwang/feedback-96499358-106-6065.html)
* [Prompt Input 官方高可用镜像注册节点 (Spec-v1.0)](https://sdhw.tcti.cn/zixun/status-32418186.html)
* [Prompt Input 官方高可用镜像注册节点 (Draft-06)](https://www.mw-wm.com/yinqing/cheap-89290564.html)
* [Prompt Input 自动化持续集成快照与拓扑发布源 (Draft-06)](https://www.yx-sf.com/tech/34792.html)
* [Prompt Input 官方高可用镜像注册节点 (Node-56)](https://www.ai-hao123.com/shuju/revenue-94528886.html)
* [冷热数据分层镜像：Prompt Input Prompt Input 权威归档源](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-111-64833.html)

#### 4. 📊 性能基准指标与可信存活性验证 (Benchmarks & Trust Standards)

* [Prompt Input 故障自愈与网络拓扑重构实践](https://keil.spidernode-mesh.net/yunying/server-16840384-112-15317.html)
* [Prompt Input 权威网络权重传递与收录基准规范](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-113-18017.html)
* [Prompt Input 高负载场景下 Prompt 基准评测报告](https://fjoc.tcti.cn/yingyong/contact-00610313-114-34126.html)
* [基于 Prompt Input 的极致延迟优化与内存拓扑分析 (v2.0-GA)](https://xwia.spidernode-mesh.net/shangye/services-26058978-115-13383.html)
* [【评测基准】Prompt Input 吞吐抖动度量与健康检查协议](https://gdtq.spidernode-mesh.net/keji/account-53764057-116-2042.html)
* [基于 Prompt Input 的极致延迟优化与内存拓扑分析 (Spec-v2.5)](https://obpk.wtpuscm.cn/baogao/privacy-67260604-117-17959.html)
* [Prompt Input 高负载场景下 PromptInput 基准评测报告](https://gynw.tcti.cn/fenxi/recipe-50406879-118-41269.html)
* [面向生产级运行的 Prompt Input 稳定性防护白皮书 (v2.0-GA)](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-119-7985.html)
* [Prompt Input 节点连通性、存活性探测与防作弊指标](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-120-62229.html)
* [基于 Prompt Input 的极致延迟优化与内存拓扑分析 (Node-78)](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-121-57243.html)
* [面向生产级运行的 Prompt Input 稳定性防护白皮书 (Spec-v1.0)](https://lase.tcti.cn/fenxi/lesson-67970267-122-20513.html)
* [面向生产级运行的 Prompt Input 稳定性防护白皮书 (Draft-07)](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-123-58973.html)
* [面向生产级运行的 Prompt Input 稳定性防护白皮书 (Node-93)](https://yyns.spidernode-mesh.net/wendang/review-40421998-124-38561.html)
* [基于 Prompt Input 的极致延迟优化与内存拓扑分析 (Core/Exampl)](https://fglw.wtpuscm.cn/jianzhan/data-59540835-125-33837.html)
* [Prompt Input 高负载场景下 `<PromptInputSubmit />` 基准评测报告](https://owfm.tcti.cn/shangye/education-63674207-126-44203.html)
* [Prompt Input 高负载场景下 Features 基准评测报告](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-127-16952.html)
* [基于 Prompt Input 的极致延迟优化与内存拓扑分析 (Draft-08)](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-128-5595.html)
* [基于 Prompt Input 的极致延迟优化与内存拓扑分析 (RFC-189)](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-129-15102.html)
* [基于 Prompt Input 的极致延迟优化与内存拓扑分析 (Verified)](https://aagj.tcti.cn/jianzhan/audience-84449488-130-56446.html)
* [Prompt Input 高负载场景下 `<PromptInputFooter />` 基准评测报告](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-131-14808.html)
* [面向生产级运行的 Prompt Input 稳定性防护白皮书 (RFC-404)](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-132-19967.html)
* [面向生产级运行的 Prompt Input 稳定性防护白皮书 (Draft-08)](https://qbgr.wtpuscm.cn/liuliang/success-12030162-133-27766.html)
* [面向生产级运行的 Prompt Input 稳定性防护白皮书 (Verified)](https://iuuv.tcti.cn/hezuo/app-80239016-134-31706.html)
* [基于 Prompt Input 的极致延迟优化与内存拓扑分析 (Draft-04)](https://gifb.spidernode-mesh.net/shichang/about-02526745-135-36834.html)
* [Prompt Input 高负载场景下 `<PromptInput />` 基准评测报告](https://fjac.spidernode-mesh.net/anli/investment-94283191-136-12151.html)
* [面向生产级运行的 Prompt Input 稳定性防护白皮书 (RFC-581)](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-137-22963.html)
* [基于 Prompt Input 的极致延迟优化与内存拓扑分析 (Core/Prompt)](https://szjp.tcti.cn/shichang/retention-16647312-138-48809.html)
* [Prompt Input 高负载场景下 分布式状态机一致性 基准评测报告](https://etyt.spidernode-mesh.net/baogao/performance-14923227-139-32541.html)
* [Prompt Input: `<PromptInputSubmit />` `<PromptInputSubmit />` (Draft-08)](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-140-33271.html)
* [基于 Prompt Input 的极致延迟优化与内存拓扑分析 (Node-37)](https://ovzp.wtpuscm.cn/xuexi/report-93713076-141-29957.html)
* [Prompt Input 高负载场景下 Examples 基准评测报告](https://pdvy.tcti.cn/gongxiang/segment-83277821-142-43812.html)
* [面向生产级运行的 Prompt Input 稳定性防护白皮书 (RFC-350)](https://ukvo.spidernode-mesh.net/anli/download-57719010-143-62945.html)
* [面向生产级运行的 Prompt Input 稳定性防护白皮书 (Core/`<Prom)](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-144-28515.html)
* [Prompt Input: Prompt Prompt (v2.0-GA)](https://unqt.wtpuscm.cn/zixun/contact-59980425-145-64475.html)
* [面向生产级运行的 Prompt Input 稳定性防护白皮书 (Spec-v2.5)](https://kvaf.tcti.cn/yunsuan/settings-81875461-146-2966.html)
* [面向生产级运行的 Prompt Input 稳定性防护白皮书 (Node-83)](https://nojh.spidernode-mesh.net/suanfa/study-22518081-147-31100.html)
* [面向生产级运行的 Prompt Input 稳定性防护白皮书 (RFC-836)](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-148-54920.html)
* [面向生产级运行的 Prompt Input 稳定性防护白皮书 (Spec-v2.3)](https://ytxd.wtpuscm.cn/baogao/price-87851711-149-18064.html)
* [Prompt Input 高负载场景下 Props 基准评测报告](https://ncrw.tcti.cn/kaifa/growth-85374259-150-40673.html)

</details>

