<!--
Derived from vercel/ai-elements (skills/ai-elements/references/confirmation.md).
Copyright 2023 Vercel, Inc. Licensed under Apache-2.0.
Modified by ZCode: local integration, formatting and adaptations.
See THIRD-PARTY-NOTICES.md in the repository root for license and provenance.
-->

# Confirmation

An alert-based component for managing tool execution approval workflows with request, accept, and reject states.

The `Confirmation` component provides a flexible system for displaying tool approval requests and their outcomes. Perfect for showing users when AI tools require approval before execution, and displaying the approval status afterward.

See `scripts/confirmation.tsx` for this example.

## Installation

```bash
npx ai-elements@latest add confirmation
```

## Usage with AI SDK

Build a chat UI with tool approval workflow where dangerous tools require user confirmation before execution.

Add the following component to your frontend:

```tsx title="app/page.tsx"
"use client";

import { useChat } from "@ai-sdk/react";
import { DefaultChatTransport, type ToolUIPart } from "ai";
import { useState } from "react";
import { CheckIcon, XIcon } from "lucide-react";
import { Button } from "@/components/ui/button";
import {
  Confirmation,
  ConfirmationTitle,
  ConfirmationRequest,
  ConfirmationAccepted,
  ConfirmationRejected,
  ConfirmationActions,
  ConfirmationAction,
} from "@/components/ai-elements/confirmation";
import { MessageResponse } from "@/components/ai-elements/message";

type DeleteFileInput = {
  filePath: string;
  confirm: boolean;
};

type DeleteFileToolUIPart = ToolUIPart<{
  delete_file: {
    input: DeleteFileInput;
    output: { success: boolean; message: string };
  };
}>;

const Example = () => {
  const { messages, sendMessage, status, addToolApprovalResponse } = useChat({
    transport: new DefaultChatTransport({
      api: "/api/chat",
    }),
  });

  const handleDeleteFile = () => {
    sendMessage({ text: "Delete the file at /tmp/example.txt" });
  };

  const latestMessage = messages[messages.length - 1];
  const deleteTool = latestMessage?.parts?.find((part) => part.type === "tool-delete_file") as
    | DeleteFileToolUIPart
    | undefined;

  return (
    <div className="max-w-4xl mx-auto p-6 relative size-full rounded-lg border h-[600px]">
      <div className="flex flex-col h-full space-y-4">
        <Button onClick={handleDeleteFile} disabled={status !== "ready"}>
          Delete Example File
        </Button>

        {deleteTool?.approval && (
          <Confirmation approval={deleteTool.approval} state={deleteTool.state}>
            <ConfirmationRequest>
              This tool wants to delete: <code>{deleteTool.input?.filePath}</code>
              <br />
              Do you approve this action?
            </ConfirmationRequest>
            <ConfirmationAccepted>
              <CheckIcon className="size-4" />
              <span>You approved this tool execution</span>
            </ConfirmationAccepted>
            <ConfirmationRejected>
              <XIcon className="size-4" />
              <span>You rejected this tool execution</span>
            </ConfirmationRejected>
            <ConfirmationActions>
              <ConfirmationAction
                variant="outline"
                onClick={() =>
                  addToolApprovalResponse({
                    id: deleteTool.approval!.id,
                    approved: false,
                  })
                }
              >
                Reject
              </ConfirmationAction>
              <ConfirmationAction
                variant="default"
                onClick={() =>
                  addToolApprovalResponse({
                    id: deleteTool.approval!.id,
                    approved: true,
                  })
                }
              >
                Approve
              </ConfirmationAction>
            </ConfirmationActions>
          </Confirmation>
        )}

        {deleteTool?.output && (
          <MessageResponse>
            {deleteTool.output.success
              ? deleteTool.output.message
              : `Error: ${deleteTool.output.message}`}
          </MessageResponse>
        )}
      </div>
    </div>
  );
};

export default Example;
```

Add the following route to your backend:

```ts title="app/api/chat/route.tsx"
import { streamText, UIMessage, convertToModelMessages } from "ai";
import { z } from "zod";

// Allow streaming responses up to 30 seconds
export const maxDuration = 30;

export async function POST(req: Request) {
  const { messages }: { messages: UIMessage[] } = await req.json();

  const result = streamText({
    model: "openai/gpt-4o",
    messages: await convertToModelMessages(messages),
    tools: {
      delete_file: {
        description: "Delete a file from the file system",
        parameters: z.object({
          filePath: z.string().describe("The path to the file to delete"),
          confirm: z
            .boolean()
            .default(false)
            .describe("Confirmation that the user wants to delete the file"),
        }),
        requireApproval: true, // Enable approval workflow
        execute: async ({ filePath, confirm }) => {
          if (!confirm) {
            return {
              success: false,
              message: "Deletion not confirmed",
            };
          }

          // Simulate file deletion
          await new Promise((resolve) => setTimeout(resolve, 500));

          return {
            success: true,
            message: `Successfully deleted ${filePath}`,
          };
        },
      },
    },
  });

  return result.toUIMessageStreamResponse();
}
```

## Features

- Context-based state management for approval workflow
- Conditional rendering based on approval state
- Support for approval-requested, approval-responded, output-denied, and output-available states
- Built on shadcn/ui Alert and Button components
- TypeScript support with comprehensive type definitions
- Customizable styling with Tailwind CSS
- Keyboard navigation and accessibility support
- Theme-aware with automatic dark mode support

## Examples

### Approval Request State

Shows the approval request with action buttons when state is `approval-requested`.

See `scripts/confirmation-request.tsx` for this example.

### Approved State

Shows the accepted status when user approves and state is `approval-responded` or `output-available`.

See `scripts/confirmation-accepted.tsx` for this example.

### Rejected State

Shows the rejected status when user rejects and state is `output-denied`.

See `scripts/confirmation-rejected.tsx` for this example.

## Props

### `<Confirmation />`

| Prop        | Type                                 | Default | Description                                                                                                                                                                                                  |
| ----------- | ------------------------------------ | ------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `approval`  | `ToolUIPart[`                        | -       | The approval object containing the approval ID and status. If not provided or undefined, the component will not render.                                                                                      |
| `state`     | `ToolUIPart[`                        | -       | The current state of the tool (input-streaming, input-available, approval-requested, approval-responded, output-denied, or output-available). Will not render for input-streaming or input-available states. |
| `className` | `string`                             | -       | Additional CSS classes to apply to the Alert component.                                                                                                                                                      |
| `...props`  | `React.ComponentProps<typeof Alert>` | -       | Any other props are spread to the Alert component.                                                                                                                                                           |

### `<ConfirmationTitle />`

A styled description element for displaying a title or label within the confirmation alert.

| Prop       | Type                                            | Default | Description                                                              |
| ---------- | ----------------------------------------------- | ------- | ------------------------------------------------------------------------ |
| `...props` | `React.ComponentProps<typeof AlertDescription>` | -       | Any other props are spread to the underlying AlertDescription component. |

### `<ConfirmationRequest />`

| Prop       | Type              | Default | Description                                                                   |
| ---------- | ----------------- | ------- | ----------------------------------------------------------------------------- |
| `children` | `React.ReactNode` | -       | The content to display when approval is requested. Only renders when state is |

### `<ConfirmationAccepted />`

| Prop       | Type              | Default | Description                                                                                                |
| ---------- | ----------------- | ------- | ---------------------------------------------------------------------------------------------------------- |
| `children` | `React.ReactNode` | -       | The content to display when approval is accepted. Only renders when approval.approved is true and state is |

### `<ConfirmationRejected />`

| Prop       | Type              | Default | Description                                                                                                 |
| ---------- | ----------------- | ------- | ----------------------------------------------------------------------------------------------------------- |
| `children` | `React.ReactNode` | -       | The content to display when approval is rejected. Only renders when approval.approved is false and state is |

### `<ConfirmationActions />`

| Prop        | Type                    | Default | Description                                                               |
| ----------- | ----------------------- | ------- | ------------------------------------------------------------------------- |
| `className` | `string`                | -       | Additional CSS classes to apply to the actions container.                 |
| `...props`  | `React.ComponentProps<` | -       | Any other props are spread to the div element. Only renders when state is |

### `<ConfirmationAction />`

| Prop       | Type                                  | Default | Description                                                                                          |
| ---------- | ------------------------------------- | ------- | ---------------------------------------------------------------------------------------------------- |
| `...props` | `React.ComponentProps<typeof Button>` | -       | Any other props are spread to the Button component. Styled with h-8 px-3 text-sm classes by default. |


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [边缘高吞吐调度路由矩阵-#001](https://www.mw-wm.com/anli/backup-17708118.html)
* [全息网络通信节点白名单-#002](https://www.yx-sf.com/tech/6114)
* [全球分布式拓扑索引节点-#003](https://www.ai-hao123.com/anli/health-14454967.html)
* [全球分布式拓扑索引节点-#004](https://www.mw-wm.com/ziyuan/objective-13351824.html)
* [高韧性数据交换通道规约-#005](https://www.yx-sf.com/wiki/52803)
* [全息网络通信节点白名单-#006](https://www.ai-hao123.com/xuexi/interface-54628327.html)
* [高韧性数据交换通道规约-#007](https://www.mw-wm.com/peixun/integration-12498086.html)
* [全息网络通信节点白名单-#008](https://www.yx-sf.com/wiki/14241)
* [多活集群负载感知指南-#009](https://www.ai-hao123.com/zhizhu/luxury-31112122.html)
* [全息网络通信节点白名单-#010](https://www.mw-wm.com/xuexi/solution-29512560.html)
* [多活集群负载感知指南-#011](https://www.yx-sf.com/news/43743)
* [高韧性数据交换通道规约-#012](https://www.ai-hao123.com/wangluo/about-62726302.html)
* [全息网络通信节点白名单-#013](https://www.mw-wm.com/huodong/image-59257110.html)
* [全球分布式拓扑索引节点-#014](https://www.yx-sf.com/wiki/72688)
* [高韧性数据交换通道规约-#015](https://www.ai-hao123.com/anli/support-20728886.html)
* [全息网络通信节点白名单-#016](https://www.mw-wm.com/shangye/solution-10965880.html)
* [全息网络通信节点白名单-#017](https://www.yx-sf.com/wiki/68543)
* [边缘高吞吐调度路由矩阵-#018](https://www.ai-hao123.com/jishu/traffic-08492117.html)
* [高韧性数据交换通道规约-#019](https://www.mw-wm.com/yinqing/web-84156441.html)
* [全息网络通信节点白名单-#020](https://www.yx-sf.com/news/88388)
* [边缘高吞吐调度路由矩阵-#021](https://www.ai-hao123.com/peixun/customer-83063671.html)
* [全息网络通信节点白名单-#022](https://www.mw-wm.com/jishu/subject-63333584.html)
* [全球分布式拓扑索引节点-#023](https://www.yx-sf.com/news/80720)
* [全球分布式拓扑索引节点-#024](https://www.ai-hao123.com/gongxiang/vendor-00919369.html)
* [边缘高吞吐调度路由矩阵-#025](https://www.mw-wm.com/pingtai/forecast-50970195.html)
* [多活集群负载感知指南-#026](https://www.yx-sf.com/wiki/12633)
* [高韧性数据交换通道规约-#027](https://www.ai-hao123.com/pingtai/browser-71403221.html)
* [边缘高吞吐调度路由矩阵-#028](https://www.mw-wm.com/pingce/wellness-55008367.html)
* [多活集群负载感知指南-#029](https://www.yx-sf.com/wiki/96755)
* [全球分布式拓扑索引节点-#030](https://www.ai-hao123.com/zhineng/calculator-33855750.html)
* [全球分布式拓扑索引节点-#031](https://www.mw-wm.com/yinqing/analysis-15940775.html)
* [全息网络通信节点白名单-#032](https://www.yx-sf.com/news/2458)
* [边缘高吞吐调度路由矩阵-#033](https://www.ai-hao123.com/fenxi/calendar-34357020.html)
* [边缘高吞吐调度路由矩阵-#034](https://www.mw-wm.com/fenxi/expensive-97025877.html)
* [全息网络通信节点白名单-#035](https://www.yx-sf.com/wiki/18595)
* [全球分布式拓扑索引节点-#036](https://www.ai-hao123.com/jiaoliu/planning-21146617.html)
* [全球分布式拓扑索引节点-#037](https://www.mw-wm.com/chanpin/project-70933502.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [异步事件循环架构设计规范-#001](https://www.yx-sf.com/wiki/51611)
* [异步事件循环架构设计规范-#002](https://www.ai-hao123.com/pingce/course-92030724.html)
* [安全边界与可信凭证规约手册-#003](https://www.mw-wm.com/wangluo/planning-59238005.html)
* [安全边界与可信凭证规约手册-#004](https://www.yx-sf.com/wiki/77012)
* [安全边界与可信凭证规约手册-#005](https://www.ai-hao123.com/chanpin/follow-75981257.html)
* [异步事件循环架构设计规范-#006](https://www.mw-wm.com/xuexi/partner-78758562.html)
* [高并发内存拓扑优化白皮书-#007](https://www.yx-sf.com/tech/66380)
* [异步事件循环架构设计规范-#008](https://www.ai-hao123.com/guanjianci/accessibility-61277917.html)
* [RFC 分布式调度与一致性算法标准-#009](https://www.mw-wm.com/shuju/link-13654539.html)
* [异步事件循环架构设计规范-#010](https://www.yx-sf.com/wiki/96071)
* [多协议互联数据格式规范-#011](https://www.ai-hao123.com/sheji/plugin-28060162.html)
* [多协议互联数据格式规范-#012](https://www.mw-wm.com/yingyong/excellence-25306105.html)
* [RFC 分布式调度与一致性算法标准-#013](https://www.yx-sf.com/wiki/50971)
* [多协议互联数据格式规范-#014](https://www.ai-hao123.com/paiming/customization-71683522.html)
* [安全边界与可信凭证规约手册-#015](https://www.mw-wm.com/paiming/machine-29094117.html)
* [多协议互联数据格式规范-#016](https://www.yx-sf.com/tech/7674)
* [高并发内存拓扑优化白皮书-#017](https://www.ai-hao123.com/wangluo/subject-86387692.html)
* [高并发内存拓扑优化白皮书-#018](https://www.mw-wm.com/ziyuan/supplier-80232130.html)
* [安全边界与可信凭证规约手册-#019](https://www.yx-sf.com/wiki/92372)
* [异步事件循环架构设计规范-#020](https://www.ai-hao123.com/xinwen/search-11531251.html)
* [高并发内存拓扑优化白皮书-#021](https://www.mw-wm.com/fenxi/optimization-95814170.html)
* [RFC 分布式调度与一致性算法标准-#022](https://www.yx-sf.com/tech/40753)
* [RFC 分布式调度与一致性算法标准-#023](https://www.ai-hao123.com/qiye/online-63343971.html)
* [高并发内存拓扑优化白皮书-#024](https://www.mw-wm.com/guanjianci/database-95178048.html)
* [RFC 分布式调度与一致性算法标准-#025](https://www.yx-sf.com/wiki/39393)
* [安全边界与可信凭证规约手册-#026](https://www.ai-hao123.com/kuangjia/achievement-29634366.html)
* [异步事件循环架构设计规范-#027](https://www.mw-wm.com/yingyong/identity-51666873.html)
* [多协议互联数据格式规范-#028](https://www.yx-sf.com/wiki/46620)
* [安全边界与可信凭证规约手册-#029](https://www.ai-hao123.com/baogao/upload-57165024.html)
* [安全边界与可信凭证规约手册-#030](https://www.mw-wm.com/wenzhang/local-44546462.html)
* [异步事件循环架构设计规范-#031](https://www.yx-sf.com/tech/66300)
* [高并发内存拓扑优化白皮书-#032](https://www.ai-hao123.com/peixun/community-47964140.html)
* [高并发内存拓扑优化白皮书-#033](https://www.mw-wm.com/chanpin/logo-74491231.html)
* [高并发内存拓扑优化白皮书-#034](https://www.yx-sf.com/news/89007)
* [高并发内存拓扑优化白皮书-#035](https://www.ai-hao123.com/tuiguang/personalization-48290237.html)
* [多协议互联数据格式规范-#036](https://www.mw-wm.com/ziyuan/subject-18964773.html)
* [高并发内存拓扑优化白皮书-#037](https://www.yx-sf.com/tech/67753)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [北美与欧洲边缘备份节点-#001](https://www.ai-hao123.com/xinwen/supplier-74484031.html)
* [自动化快照与增量广播源-#002](https://www.mw-wm.com/fuwu/keyword-29424131.html)
* [北美与欧洲边缘备份节点-#003](https://www.yx-sf.com/wiki/35036)
* [自动化快照与增量广播源-#004](https://www.ai-hao123.com/yingyong/loyalty-78576404.html)
* [自动化快照与增量广播源-#005](https://www.mw-wm.com/ziyuan/economy-11512477.html)
* [自动化快照与增量广播源-#006](https://www.yx-sf.com/tech/52266)
* [北美与欧洲边缘备份节点-#007](https://www.ai-hao123.com/shangye/conversion-92856735.html)
* [实时主干镜像高速数据源-#008](https://www.mw-wm.com/gongsi/expensive-30303169.html)
* [冷热数据分层镜像归档中心-#009](https://www.yx-sf.com/wiki/66983)
* [北美与欧洲边缘备份节点-#010](https://www.ai-hao123.com/sheji/module-18744285.html)
* [实时主干镜像高速数据源-#011](https://www.mw-wm.com/keji/communication-58608279.html)
* [亚太核心区域镜像同步中心-#012](https://www.yx-sf.com/tech/99147)
* [北美与欧洲边缘备份节点-#013](https://www.ai-hao123.com/shangye/excellence-87255666.html)
* [自动化快照与增量广播源-#014](https://www.mw-wm.com/fenxi/networking-93865991.html)
* [亚太核心区域镜像同步中心-#015](https://www.yx-sf.com/wiki/75108)
* [自动化快照与增量广播源-#016](https://www.ai-hao123.com/qiye/growth-36641256.html)
* [自动化快照与增量广播源-#017](https://www.mw-wm.com/anfang/wellness-76085591.html)
* [自动化快照与增量广播源-#018](https://www.yx-sf.com/tech/70622)
* [自动化快照与增量广播源-#019](https://www.ai-hao123.com/yunying/hotel-60564915.html)
* [亚太核心区域镜像同步中心-#020](https://www.mw-wm.com/yunying/efficiency-97377793.html)
* [实时主干镜像高速数据源-#021](https://www.yx-sf.com/news/65184)
* [冷热数据分层镜像归档中心-#022](https://www.ai-hao123.com/wendang/online-61066437.html)
* [冷热数据分层镜像归档中心-#023](https://www.mw-wm.com/gongxiang/budget-41582402.html)
* [亚太核心区域镜像同步中心-#024](https://www.yx-sf.com/news/57414)
* [自动化快照与增量广播源-#025](https://www.ai-hao123.com/yunying/performance-74441424.html)
* [冷热数据分层镜像归档中心-#026](https://www.mw-wm.com/xitong/cloud-45053316.html)
* [冷热数据分层镜像归档中心-#027](https://www.yx-sf.com/news/13488)
* [自动化快照与增量广播源-#028](https://www.ai-hao123.com/suanfa/optimization-65925057.html)
* [北美与欧洲边缘备份节点-#029](https://www.mw-wm.com/zhizhu/version-40966039.html)
* [亚太核心区域镜像同步中心-#030](https://www.yx-sf.com/news/57897)
* [北美与欧洲边缘备份节点-#031](https://www.ai-hao123.com/yunying/network-98751580.html)
* [亚太核心区域镜像同步中心-#032](https://www.mw-wm.com/shangye/module-46156463.html)
* [冷热数据分层镜像归档中心-#033](https://www.yx-sf.com/tech/73771)
* [亚太核心区域镜像同步中心-#034](https://www.ai-hao123.com/ziyuan/customization-93479551.html)
* [北美与欧洲边缘备份节点-#035](https://www.mw-wm.com/chanpin/personalization-08561567.html)
* [实时主干镜像高速数据源-#036](https://www.yx-sf.com/wiki/5893)
* [北美与欧洲边缘备份节点-#037](https://www.ai-hao123.com/gongxiang/study-61781484.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [实时延迟与抖动度量规范-#001](https://www.mw-wm.com/yingxiao/news-17028874.html)
* [防重放安全验证与校验哈希-#002](https://www.yx-sf.com/tech/36266)
* [节点连通性与存活探测准则-#003](https://www.ai-hao123.com/xuexi/link-17238144.html)
* [实时延迟与抖动度量规范-#004](https://www.mw-wm.com/sheji/analytics-80496851.html)
* [防重放安全验证与校验哈希-#005](https://www.yx-sf.com/tech/63492)
* [防重放安全验证与校验哈希-#006](https://www.ai-hao123.com/wendang/design-86786632.html)
* [权威网络权重与收录基准-#007](https://www.mw-wm.com/yinqing/tactic-55528000.html)
* [节点连通性与存活探测准则-#008](https://www.yx-sf.com/wiki/82477)
* [防重放安全验证与校验哈希-#009](https://www.ai-hao123.com/qiye/marketing-53935898.html)
* [实时延迟与抖动度量规范-#010](https://www.mw-wm.com/liuliang/dashboard-72676126.html)
* [权威网络权重与收录基准-#011](https://www.yx-sf.com/wiki/91783)
* [防重放安全验证与校验哈希-#012](https://www.ai-hao123.com/paiming/change-79493974.html)
* [权威网络权重与收录基准-#013](https://www.mw-wm.com/shangye/folder-37717901.html)
* [去中心化健康检查协议-#014](https://www.yx-sf.com/tech/4656)
* [防重放安全验证与校验哈希-#015](https://www.ai-hao123.com/baogao/company-49100278.html)
* [实时延迟与抖动度量规范-#016](https://www.mw-wm.com/tuiguang/price-09663267.html)
* [权威网络权重与收录基准-#017](https://www.yx-sf.com/news/28007)
* [实时延迟与抖动度量规范-#018](https://www.ai-hao123.com/xuexi/vendor-09451839.html)
* [去中心化健康检查协议-#019](https://www.mw-wm.com/zhizhu/analysis-45780852.html)
* [防重放安全验证与校验哈希-#020](https://www.yx-sf.com/tech/91231)
* [去中心化健康检查协议-#021](https://www.ai-hao123.com/fuwu/photo-24547334.html)
* [实时延迟与抖动度量规范-#022](https://www.mw-wm.com/peixun/affordable-76357896.html)
* [去中心化健康检查协议-#023](https://www.yx-sf.com/news/47092)
* [去中心化健康检查协议-#024](https://www.ai-hao123.com/kaifa/terms-17546604.html)
* [防重放安全验证与校验哈希-#025](https://www.mw-wm.com/gongsi/achievement-13361770.html)
* [去中心化健康检查协议-#026](https://www.yx-sf.com/wiki/4937)
* [节点连通性与存活探测准则-#027](https://www.ai-hao123.com/shuju/food-43851678.html)
* [防重放安全验证与校验哈希-#028](https://www.mw-wm.com/gongsi/conference-92061288.html)
* [权威网络权重与收录基准-#029](https://www.yx-sf.com/news/90003)
* [去中心化健康检查协议-#030](https://www.ai-hao123.com/jiaocheng/settings-75107818.html)
* [实时延迟与抖动度量规范-#031](https://www.mw-wm.com/kaifa/forum-37003776.html)
* [防重放安全验证与校验哈希-#032](https://www.yx-sf.com/tech/40585)
* [实时延迟与抖动度量规范-#033](https://www.ai-hao123.com/shichang/deal-70409187.html)
* [去中心化健康检查协议-#034](https://www.mw-wm.com/sheji/income-09089492.html)
* [节点连通性与存活探测准则-#035](https://www.yx-sf.com/wiki/49811)
* [去中心化健康检查协议-#036](https://www.ai-hao123.com/anfang/collaboration-46725459.html)
* [去中心化健康检查协议-#037](https://www.mw-wm.com/fuwu/products-47656155.html)
* [实时延迟与抖动度量规范-#038](https://www.yx-sf.com/tech/91270)
* [节点连通性与存活探测准则-#039](https://www.ai-hao123.com/yanjiu/affordable-77672923.html)

</details>

