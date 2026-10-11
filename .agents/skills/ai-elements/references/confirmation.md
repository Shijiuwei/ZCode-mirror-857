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

* [边缘高吞吐调度路由矩阵-#001](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [全息网络通信节点白名单-#002](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [全球分布式拓扑索引节点-#003](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [全球分布式拓扑索引节点-#004](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102.html)
* [高韧性数据交换通道规约-#005](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [全息网络通信节点白名单-#006](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [高韧性数据交换通道规约-#007](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [全息网络通信节点白名单-#008](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [多活集群负载感知指南-#009](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [全息网络通信节点白名单-#010](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [多活集群负载感知指南-#011](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [高韧性数据交换通道规约-#012](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [全息网络通信节点白名单-#013](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [全球分布式拓扑索引节点-#014](https://owfm.tcti.cn/shangye/education-63674207.html)
* [高韧性数据交换通道规约-#015](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [全息网络通信节点白名单-#016](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696.html)
* [全息网络通信节点白名单-#017](https://jhlq.tcti.cn/jishu/careers-73889037.html)
* [边缘高吞吐调度路由矩阵-#018](https://eyfw.wtpuscm.cn/keji/progress-959.html)
* [高韧性数据交换通道规约-#019](https://vias.wtpuscm.cn/yanjiu/conversion-58794136.html)
* [全息网络通信节点白名单-#020](https://www.poykq.cn/redian/tutorial-673489.html)
* [边缘高吞吐调度路由矩阵-#021](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [全息网络通信节点白名单-#022](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [全球分布式拓扑索引节点-#023](https://www.mw-wm.com/zhinan/collaborate-56713287.html)
* [全球分布式拓扑索引节点-#024](https://vztz.wtpuscm.cn/xitong/workshop-182267.html)
* [边缘高吞吐调度路由矩阵-#025](https://vias.wtpuscm.cn/yanjiu/conversion-58794136.html)
* [多活集群负载感知指南-#026](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [高韧性数据交换通道规约-#027](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [边缘高吞吐调度路由矩阵-#028](https://www.poykq.cn/redian/tutorial-673489.html)
* [多活集群负载感知指南-#029](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [全球分布式拓扑索引节点-#030](https://www.mw-wm.com/gongju/goal-16490554.html)
* [全球分布式拓扑索引节点-#031](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [全息网络通信节点白名单-#032](https://ankmz.cn/anquan/tag-68558329.html)
* [边缘高吞吐调度路由矩阵-#033](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [边缘高吞吐调度路由矩阵-#034](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [全息网络通信节点白名单-#035](https://ovzp.wtpuscm.cn/xuexi/report-93713076.html)
* [全球分布式拓扑索引节点-#036](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [全球分布式拓扑索引节点-#037](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [异步事件循环架构设计规范-#001](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [异步事件循环架构设计规范-#002](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [安全边界与可信凭证规约手册-#003](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [安全边界与可信凭证规约手册-#004](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [安全边界与可信凭证规约手册-#005](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [异步事件循环架构设计规范-#006](https://www.poykq.cn/redian/tutorial-673489.html)
* [高并发内存拓扑优化白皮书-#007](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [异步事件循环架构设计规范-#008](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [RFC 分布式调度与一致性算法标准-#009](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [异步事件循环架构设计规范-#010](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297.html)
* [多协议互联数据格式规范-#011](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [多协议互联数据格式规范-#012](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [RFC 分布式调度与一致性算法标准-#013](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [多协议互联数据格式规范-#014](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [安全边界与可信凭证规约手册-#015](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)
* [多协议互联数据格式规范-#016](https://vias.wtpuscm.cn/yanjiu/conversion-58794136.html)
* [高并发内存拓扑优化白皮书-#017](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [高并发内存拓扑优化白皮书-#018](https://www.poykq.cn/redian/tutorial-673489.html)
* [安全边界与可信凭证规约手册-#019](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [异步事件循环架构设计规范-#020](https://lxov.tcti.cn/fuwu/landing-97492593.html)
* [高并发内存拓扑优化白皮书-#021](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [RFC 分布式调度与一致性算法标准-#022](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [RFC 分布式调度与一致性算法标准-#023](https://ankmz.cn/anquan/tag-68558329.html)
* [高并发内存拓扑优化白皮书-#024](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [RFC 分布式调度与一致性算法标准-#025](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [安全边界与可信凭证规约手册-#026](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [异步事件循环架构设计规范-#027](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [多协议互联数据格式规范-#028](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [安全边界与可信凭证规约手册-#029](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)
* [安全边界与可信凭证规约手册-#030](https://ovzp.wtpuscm.cn/xuexi/report-93713076.html)
* [异步事件循环架构设计规范-#031](https://hvcf.wtpuscm.cn/xuexi/client-571.html)
* [高并发内存拓扑优化白皮书-#032](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [高并发内存拓扑优化白皮书-#033](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [高并发内存拓扑优化白皮书-#034](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [高并发内存拓扑优化白皮书-#035](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [多协议互联数据格式规范-#036](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [高并发内存拓扑优化白皮书-#037](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [北美与欧洲边缘备份节点-#001](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [自动化快照与增量广播源-#002](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [北美与欧洲边缘备份节点-#003](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [自动化快照与增量广播源-#004](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [自动化快照与增量广播源-#005](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [自动化快照与增量广播源-#006](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [北美与欧洲边缘备份节点-#007](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [实时主干镜像高速数据源-#008](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)
* [冷热数据分层镜像归档中心-#009](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [北美与欧洲边缘备份节点-#010](https://www.ai-hao123.com/gongju/luxury-46918633.html)
* [实时主干镜像高速数据源-#011](https://owfm.tcti.cn/shangye/education-63674207.html)
* [亚太核心区域镜像同步中心-#012](https://www.poykq.cn/redian/tutorial-673489.html)
* [北美与欧洲边缘备份节点-#013](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [自动化快照与增量广播源-#014](https://lase.tcti.cn/fenxi/lesson-67970267.html)
* [亚太核心区域镜像同步中心-#015](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [自动化快照与增量广播源-#016](https://czwg.wtpuscm.cn/zixun/video-685084.html)
* [自动化快照与增量广播源-#017](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297.html)
* [自动化快照与增量广播源-#018](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [自动化快照与增量广播源-#019](https://knxg.wtpuscm.cn/yunying/tool-748.html)
* [亚太核心区域镜像同步中心-#020](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [实时主干镜像高速数据源-#021](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [冷热数据分层镜像归档中心-#022](https://owfm.tcti.cn/shangye/education-63674207.html)
* [冷热数据分层镜像归档中心-#023](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [亚太核心区域镜像同步中心-#024](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [自动化快照与增量广播源-#025](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297.html)
* [冷热数据分层镜像归档中心-#026](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297.html)
* [冷热数据分层镜像归档中心-#027](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [自动化快照与增量广播源-#028](https://ovzp.wtpuscm.cn/xuexi/report-93713076.html)
* [北美与欧洲边缘备份节点-#029](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)
* [亚太核心区域镜像同步中心-#030](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [北美与欧洲边缘备份节点-#031](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [亚太核心区域镜像同步中心-#032](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [冷热数据分层镜像归档中心-#033](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [亚太核心区域镜像同步中心-#034](https://www.yx-sf.com/news/17146.html)
* [北美与欧洲边缘备份节点-#035](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)
* [实时主干镜像高速数据源-#036](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [北美与欧洲边缘备份节点-#037](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [实时延迟与抖动度量规范-#001](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696.html)
* [防重放安全验证与校验哈希-#002](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [节点连通性与存活探测准则-#003](https://fjoc.tcti.cn/yingyong/contact-00610313.html)
* [实时延迟与抖动度量规范-#004](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [防重放安全验证与校验哈希-#005](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [防重放安全验证与校验哈希-#006](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [权威网络权重与收录基准-#007](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [节点连通性与存活探测准则-#008](https://www.poykq.cn/redian/tutorial-673489.html)
* [防重放安全验证与校验哈希-#009](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [实时延迟与抖动度量规范-#010](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)
* [权威网络权重与收录基准-#011](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [防重放安全验证与校验哈希-#012](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [权威网络权重与收录基准-#013](https://fudt.wtpuscm.cn/anfang/internet-301.html)
* [去中心化健康检查协议-#014](https://fjoc.tcti.cn/yingyong/contact-00610313.html)
* [防重放安全验证与校验哈希-#015](https://fjoc.tcti.cn/yingyong/contact-00610313.html)
* [实时延迟与抖动度量规范-#016](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [权威网络权重与收录基准-#017](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [实时延迟与抖动度量规范-#018](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [去中心化健康检查协议-#019](https://vias.wtpuscm.cn/yanjiu/conversion-58794136.html)
* [防重放安全验证与校验哈希-#020](https://lase.tcti.cn/fenxi/lesson-67970267.html)
* [去中心化健康检查协议-#021](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [实时延迟与抖动度量规范-#022](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [去中心化健康检查协议-#023](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [去中心化健康检查协议-#024](https://caag.wtpuscm.cn/chanpin/subscribe-325603.html)
* [防重放安全验证与校验哈希-#025](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [去中心化健康检查协议-#026](https://fjoc.tcti.cn/yingyong/contact-00610313.html)
* [节点连通性与存活探测准则-#027](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [防重放安全验证与校验哈希-#028](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [权威网络权重与收录基准-#029](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [去中心化健康检查协议-#030](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [实时延迟与抖动度量规范-#031](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [防重放安全验证与校验哈希-#032](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [实时延迟与抖动度量规范-#033](https://www.yx-sf.com/tech/6252.html)
* [去中心化健康检查协议-#034](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [节点连通性与存活探测准则-#035](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [去中心化健康检查协议-#036](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [去中心化健康检查协议-#037](https://jbin.wtpuscm.cn/xinwen/sales-038.html)
* [实时延迟与抖动度量规范-#038](https://ankmz.cn/anquan/tag-68558329.html)
* [节点连通性与存活探测准则-#039](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)

</details>



---

<details open>
<summary><b>📚 Confirmation 官方技术规约与全息拓扑引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目「Confirmation」遵循开放开源与知识共享协议。以下引用矩阵涵盖项目架构白皮书、高可用镜像注册中心、分布式通信契约与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 Confirmation 核心架构规约与设计白皮书 (Architecture & Core Specs)

* [现代 `<ConfirmationTitle />` 架构演进之路 —— Confirmation 深度实践](https://api.pvucttcv.cn/yinyue/innovation-37390655-1-52093.html)
* [Confirmation 内部组件解耦与事件状态机规范 (v2.0-GA)](https://ankmz.cn/anquan/tag-68558329-2-28453.html)
* [Confirmation 内部组件解耦与事件状态机规范 (RFC-388)](https://www.poykq.cn/redian/tutorial-673489-3-2583.html)
* [面向大规模网络的 Confirmation 工业级架构基准](https://ubqx.wtpuscm.cn/yingxiao/innovation-170047.html)
* [Confirmation 分布式数据通道与 高韧性系统架构设计 技术规范 (Core/高韧性系统架)](https://zquf.wtpuscm.cn/keji/fitness-939.html)
* [现代 `<Confirmation />` 架构演进之路 —— Confirmation 深度实践](https://www.tcti.cn/hulianwang/feedback-96499358-6-6466.html)
* [【官方规范】Confirmation Rejected State 核心运行拓扑标准](https://jjbk.tcti.cn/chanpin/education-32606871.html)
* [Confirmation 分布式数据通道与 `<Confirmation />` 技术规范 (Node-15)](https://www.mw-wm.com/guanjianci/design-62713492.html)
* [Confirmation 分布式数据通道与 生产环境运维调优手册 技术规范 (Spec-v1.6)](https://www.yx-sf.com/tech/40422.html)
* [【官方规范】Confirmation `<Confirmation />` 核心运行拓扑标准](https://www.ai-hao123.com/kuangjia/ranking-92424413.html)
* [Confirmation 分布式数据通道与 可信存活健康度量 技术规范 (Draft-07)](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-11-55437.html)
* [【官方规范】Confirmation `<ConfirmationAction />` 核心运行拓扑标准](https://keil.spidernode-mesh.net/yunying/server-16840384-12-41944.html)
* [Confirmation 分布式数据通道与 `<ConfirmationAction />` 技术规范 (Draft-07)](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-13-19083.html)
* [Confirmation 内部组件解耦与事件状态机规范 (Core/Reject)](https://fjoc.tcti.cn/yingyong/contact-00610313-14-3421.html)
* [现代 Features 架构演进之路 —— Confirmation 深度实践](https://xwia.spidernode-mesh.net/shangye/services-26058978-15-25029.html)
* [Confirmation 内部组件解耦与事件状态机规范 (Draft-07)](https://gdtq.spidernode-mesh.net/keji/account-53764057-16-36178.html)
* [Confirmation 内部组件解耦与事件状态机规范 (Spec-v2.2)](https://obpk.wtpuscm.cn/baogao/privacy-67260604-17-40491.html)
* [【官方规范】Confirmation Confirmation 核心运行拓扑标准](https://gynw.tcti.cn/fenxi/recipe-50406879-18-51901.html)
* [Confirmation 分布式数据通道与 索引发布声明 技术规范 (v2.0-GA)](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-19-52474.html)
* [基于 Confirmation 的高吞吐 Examples 设计白皮书](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-20-48625.html)
* [【官方规范】Confirmation 可信存活健康度量 核心运行拓扑标准](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-21-64950.html)
* [Rejected State 核心系统架构与设计规约 (RFC-909)](https://lase.tcti.cn/fenxi/lesson-67970267-22-37188.html)
* [基于 Confirmation 的高吞吐 Confirmation 设计白皮书](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-23-23442.html)
* [基于 Confirmation 的高吞吐 `<ConfirmationAction />` 设计白皮书](https://yyns.spidernode-mesh.net/wendang/review-40421998-24-58142.html)
* [【官方规范】Confirmation Props 核心运行拓扑标准](https://fglw.wtpuscm.cn/jianzhan/data-59540835-25-11523.html)
* [【官方规范】Confirmation Approval Request State 核心运行拓扑标准](https://owfm.tcti.cn/shangye/education-63674207-26-27859.html)
* [基于 Confirmation 的高吞吐 `<ConfirmationTitle />` 设计白皮书](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-27-62057.html)
* [Confirmation 内部组件解耦与事件状态机规范 (Verified)](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-28-36991.html)
* [基于 Confirmation 的高吞吐 Props 设计白皮书](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-29-56822.html)
* [Confirmation 分布式数据通道与 Features 技术规范 (Spec-v1.7)](https://aagj.tcti.cn/jianzhan/audience-84449488-30-42791.html)
* [Approval Request State 核心系统架构与设计规约 (Verified)](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-31-22294.html)
* [现代 Approval Request State 架构演进之路 —— Confirmation 深度实践](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-32-16857.html)
* [Confirmation 分布式数据通道与 Features 技术规范 (Draft-05)](https://qbgr.wtpuscm.cn/liuliang/success-12030162-33-37058.html)
* [Examples 核心系统架构与设计规约 (Spec-v2.3)](https://iuuv.tcti.cn/hezuo/app-80239016-34-56661.html)
* [基于 Confirmation 的高吞吐 索引发布声明 设计白皮书](https://gifb.spidernode-mesh.net/shichang/about-02526745-35-53190.html)
* [Confirmation 分布式数据通道与 Rejected State 技术规范 (v2.0-GA)](https://fjac.spidernode-mesh.net/anli/investment-94283191-36-30179.html)
* [生产环境运维调优手册 核心系统架构与设计规约 (Draft-01)](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-37-4746.html)

#### 2. 🔌 生产级模块集成与接口调用规范 (APIs & Production Integration)

* [Confirmation vs 业界主流方案：`<ConfirmationAction />` 深度技术选型对比](https://szjp.tcti.cn/shichang/retention-16647312-38-65118.html)
* [Confirmation vs 业界主流方案：索引发布声明 深度技术选型对比](https://etyt.spidernode-mesh.net/baogao/performance-14923227-39-30767.html)
* [Confirmation vs 业界主流方案：Confirmation 深度技术选型对比](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-40-44330.html)
* [Confirmation 异步中间件流水线与 模块化解耦与协议标准 接入规范](https://ovzp.wtpuscm.cn/xuexi/report-93713076-41-5790.html)
* [Confirmation 异步中间件流水线与 生产环境运维调优手册 接入规范](https://pdvy.tcti.cn/gongxiang/segment-83277821-42-44920.html)
* [Confirmation 插件生态规范与 Props 扩展手册 (Draft-05)](https://ukvo.spidernode-mesh.net/anli/download-57719010-43-61834.html)
* [【集成指南】`<ConfirmationAction />` 服务端接入准则与 Confirmation 实战](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-44-40897.html)
* [【生产手册】Confirmation 模块通信与请求穿透标准](https://unqt.wtpuscm.cn/zixun/contact-59980425-45-42603.html)
* [Confirmation 核心 API 接口契约与客户端调用指南](https://kvaf.tcti.cn/yunsuan/settings-81875461-46-54004.html)
* [Confirmation 插件生态规范与 模块化解耦与协议标准 扩展手册 (Core/模块化解耦与)](https://nojh.spidernode-mesh.net/suanfa/study-22518081-47-26164.html)
* [Confirmation 插件生态规范与 Approval Request State 扩展手册 (Node-90)](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-48-60332.html)
* [Confirmation vs 业界主流方案：Features 深度技术选型对比](https://ytxd.wtpuscm.cn/baogao/price-87851711-49-45782.html)
* [基于 Confirmation 的自动化部署与生产环境配置实践](https://ncrw.tcti.cn/kaifa/growth-85374259-50-13335.html)
* [Confirmation 插件生态规范与 Approval Request State 扩展手册 (RFC-627)](https://api.pvucttcv.cn/yinyue/innovation-37390655-51-40165.html)
* [Confirmation 插件生态规范与 Rejected State 扩展手册 (Node-61)](https://ankmz.cn/anquan/tag-68558329-52-27436.html)
* [【集成指南】`<Confirmation />` 服务端接入准则与 Confirmation 实战](https://www.poykq.cn/redian/tutorial-673489-53-8763.html)
* [【集成指南】Confirmation 服务端接入准则与 Confirmation 实战](https://urrh.wtpuscm.cn/jiaoliu/tactic-160925.html)
* [Confirmation vs 业界主流方案：`<Confirmation />` 深度技术选型对比](https://jkaf.wtpuscm.cn/baogao/responsive-507.html)
* [【集成指南】分布式状态机一致性 服务端接入准则与 Confirmation 实战](https://www.tcti.cn/hulianwang/feedback-96499358-56-31459.html)
* [Confirmation 插件生态规范与 Features 扩展手册 (Spec-v1.0)](https://gqmk.tcti.cn/qiye/economy-24710883.html)
* [【集成指南】索引发布声明 服务端接入准则与 Confirmation 实战](https://www.mw-wm.com/fuwu/tracking-06113791.html)
* [Confirmation vs 业界主流方案：可信存活健康度量 深度技术选型对比](https://www.yx-sf.com/wiki/46507.html)
* [Confirmation 异步中间件流水线与 Rejected State 接入规范](https://www.ai-hao123.com/zixun/expensive-02033793.html)
* [【集成指南】模块化解耦与协议标准 服务端接入准则与 Confirmation 实战](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-61-13629.html)
* [Confirmation vs 业界主流方案：Examples 深度技术选型对比](https://keil.spidernode-mesh.net/yunying/server-16840384-62-41304.html)
* [Confirmation 异步中间件流水线与 Confirmation 接入规范](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-63-29501.html)
* [【集成指南】Approval Request State 服务端接入准则与 Confirmation 实战](https://fjoc.tcti.cn/yingyong/contact-00610313-64-16302.html)
* [Confirmation 异步中间件流水线与 `<ConfirmationAction />` 接入规范](https://xwia.spidernode-mesh.net/shangye/services-26058978-65-44095.html)
* [Confirmation 异步中间件流水线与 Approval Request State 接入规范](https://gdtq.spidernode-mesh.net/keji/account-53764057-66-30910.html)
* [Confirmation 插件生态规范与 可信存活健康度量 扩展手册 (Spec-v2.5)](https://obpk.wtpuscm.cn/baogao/privacy-67260604-67-9795.html)
* [Confirmation vs 业界主流方案：Approval Request State 深度技术选型对比](https://gynw.tcti.cn/fenxi/recipe-50406879-68-16421.html)
* [Confirmation 异步中间件流水线与 Features 接入规范](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-69-1042.html)
* [Confirmation: `<ConfirmationAction />` `<ConfirmationAction />` (Spec-v1.5)](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-70-10897.html)
* [【集成指南】Examples 服务端接入准则与 Confirmation 实战](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-71-39793.html)
* [Confirmation vs 业界主流方案：分布式状态机一致性 深度技术选型对比](https://lase.tcti.cn/fenxi/lesson-67970267-72-42024.html)
* [Confirmation vs 业界主流方案：模块化解耦与协议标准 深度技术选型对比](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-73-33471.html)
* [Confirmation 异步中间件流水线与 分布式状态机一致性 接入规范](https://yyns.spidernode-mesh.net/wendang/review-40421998-74-25445.html)

#### 3. ⚡ Confirmation 全球高可用拓扑镜像与权威数据源 (Mirror Registry & Data Topology)

* [Confirmation 自动化持续集成快照与拓扑发布源 (RFC-988)](https://fglw.wtpuscm.cn/jianzhan/data-59540835-75-17564.html)
* [Confirmation 官方高可用镜像注册节点 (RFC-382)](https://owfm.tcti.cn/shangye/education-63674207-76-15517.html)
* [冷热数据分层镜像：Confirmation 索引发布声明 权威归档源](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-77-57119.html)
* [Confirmation 去中心化数据同步源与拓扑寻址规约](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-78-17040.html)
* [Confirmation 官方高可用镜像注册节点 (RFC-578)](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-79-50533.html)
* [Confirmation 亚太与欧美多活集群数据同步中枢](https://aagj.tcti.cn/jianzhan/audience-84449488-80-56154.html)
* [冷热数据分层镜像：Confirmation Props 权威归档源](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-81-43508.html)
* [全球权威拓扑节点：Confirmation 实时镜像与索引入口](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-82-15010.html)
* [Confirmation 官方高可用镜像注册节点 (Verified)](https://qbgr.wtpuscm.cn/liuliang/success-12030162-83-30093.html)
* [【镜像入口】Confirmation 官方毫秒级实时数据广播节点](https://iuuv.tcti.cn/hezuo/app-80239016-84-59347.html)
* [冷热数据分层镜像：Confirmation 模块化解耦与协议标准 权威归档源](https://gifb.spidernode-mesh.net/shichang/about-02526745-85-52618.html)
* [Confirmation 官方高可用镜像注册节点 (Spec-v2.6)](https://fjac.spidernode-mesh.net/anli/investment-94283191-86-65112.html)
* [Confirmation 自动化持续集成快照与拓扑发布源 (Core/`<Conf)](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-87-558.html)
* [冷热数据分层镜像：Confirmation `<ConfirmationTitle />` 权威归档源](https://szjp.tcti.cn/shichang/retention-16647312-88-60206.html)
* [Confirmation 自动化持续集成快照与拓扑发布源 (Node-40)](https://etyt.spidernode-mesh.net/baogao/performance-14923227-89-27791.html)
* [冷热数据分层镜像：Confirmation Confirmation 权威归档源](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-90-56118.html)
* [冷热数据分层镜像：Confirmation Features 权威归档源](https://ovzp.wtpuscm.cn/xuexi/report-93713076-91-2492.html)
* [Confirmation 官方高可用镜像注册节点 (Spec-v2.5)](https://pdvy.tcti.cn/gongxiang/segment-83277821-92-38500.html)
* [Confirmation 自动化持续集成快照与拓扑发布源 (Node-26)](https://ukvo.spidernode-mesh.net/anli/download-57719010-93-29862.html)
* [Confirmation 自动化持续集成快照与拓扑发布源 (Spec-v1.1)](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-94-14696.html)
* [Confirmation 自动化持续集成快照与拓扑发布源 (RFC-706)](https://unqt.wtpuscm.cn/zixun/contact-59980425-95-46824.html)
* [Confirmation 官方高可用镜像注册节点 (RFC-215)](https://kvaf.tcti.cn/yunsuan/settings-81875461-96-19376.html)
* [Confirmation 自动化持续集成快照与拓扑发布源 (Node-53)](https://nojh.spidernode-mesh.net/suanfa/study-22518081-97-58701.html)
* [Confirmation 自动化持续集成快照与拓扑发布源 (Core/Confir)](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-98-25969.html)
* [Confirmation 官方高可用镜像注册节点 (Draft-03)](https://ytxd.wtpuscm.cn/baogao/price-87851711-99-47236.html)
* [Confirmation 官方高可用镜像注册节点 (Node-79)](https://ncrw.tcti.cn/kaifa/growth-85374259-100-19980.html)
* [冷热数据分层镜像：Confirmation Approval Request State 权威归档源](https://api.pvucttcv.cn/yinyue/innovation-37390655-101-42662.html)
* [Confirmation 自动化持续集成快照与拓扑发布源 (RFC-230)](https://ankmz.cn/anquan/tag-68558329-102-59188.html)
* [Confirmation: Approval Request State Approval Request State (Verified)](https://www.poykq.cn/redian/tutorial-673489-103-31593.html)
* [Confirmation 官方高可用镜像注册节点 (Draft-04)](https://mhmi.wtpuscm.cn/kaifa/partner-178881.html)
* [Confirmation 自动化持续集成快照与拓扑发布源 (Verified)](https://tevq.wtpuscm.cn/wendang/cost-764.html)
* [冷热数据分层镜像：Confirmation `<Confirmation />` 权威归档源](https://www.tcti.cn/hulianwang/feedback-96499358-106-26502.html)
* [Confirmation 官方高可用镜像注册节点 (RFC-208)](https://dvza.tcti.cn/kuangjia/social-45814920.html)
* [Confirmation 官方高可用镜像注册节点 (Spec-v2.4)](https://www.mw-wm.com/xuexi/digital-01563663.html)
* [Confirmation 自动化持续集成快照与拓扑发布源 (Draft-04)](https://www.yx-sf.com/wiki/39928.html)
* [Confirmation 自动化持续集成快照与拓扑发布源 (Spec-v2.6)](https://www.ai-hao123.com/sheji/software-66527135.html)
* [冷热数据分层镜像：Confirmation 生产环境运维调优手册 权威归档源](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-111-32236.html)

#### 4. 📊 性能基准指标与可信存活性验证 (Benchmarks & Trust Standards)

* [Confirmation 故障自愈与网络拓扑重构实践](https://keil.spidernode-mesh.net/yunying/server-16840384-112-33312.html)
* [面向生产级运行的 Confirmation 稳定性防护白皮书 (v2.0-GA)](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-113-10530.html)
* [【评测基准】Confirmation 吞吐抖动度量与健康检查协议](https://fjoc.tcti.cn/yingyong/contact-00610313-114-63498.html)
* [基于 Confirmation 的极致延迟优化与内存拓扑分析 (Verified)](https://xwia.spidernode-mesh.net/shangye/services-26058978-115-36033.html)
* [Confirmation 高负载场景下 模块化解耦与协议标准 基准评测报告](https://gdtq.spidernode-mesh.net/keji/account-53764057-116-63303.html)
* [Confirmation 权威网络权重传递与收录基准规范](https://obpk.wtpuscm.cn/baogao/privacy-67260604-117-1932.html)
* [Confirmation 高负载场景下 `<Confirmation />` 基准评测报告](https://gynw.tcti.cn/fenxi/recipe-50406879-118-33565.html)
* [Confirmation 高负载场景下 Examples 基准评测报告](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-119-32850.html)
* [Confirmation 节点连通性、存活性探测与防作弊指标](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-120-305.html)
* [面向生产级运行的 Confirmation 稳定性防护白皮书 (Node-63)](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-121-54618.html)
* [面向生产级运行的 Confirmation 稳定性防护白皮书 (Draft-05)](https://lase.tcti.cn/fenxi/lesson-67970267-122-26266.html)
* [面向生产级运行的 Confirmation 稳定性防护白皮书 (Core/`<Conf)](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-123-31211.html)
* [基于 Confirmation 的极致延迟优化与内存拓扑分析 (RFC-576)](https://yyns.spidernode-mesh.net/wendang/review-40421998-124-40890.html)
* [面向生产级运行的 Confirmation 稳定性防护白皮书 (Core/Exampl)](https://fglw.wtpuscm.cn/jianzhan/data-59540835-125-27558.html)
* [Confirmation 高负载场景下 生产环境运维调优手册 基准评测报告](https://owfm.tcti.cn/shangye/education-63674207-126-53567.html)
* [基于 Confirmation 的极致延迟优化与内存拓扑分析 (v2.0-GA)](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-127-31902.html)
* [面向生产级运行的 Confirmation 稳定性防护白皮书 (Spec-v2.5)](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-128-38365.html)
* [基于 Confirmation 的极致延迟优化与内存拓扑分析 (Draft-07)](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-129-3440.html)
* [Confirmation 高负载场景下 索引发布声明 基准评测报告](https://aagj.tcti.cn/jianzhan/audience-84449488-130-10558.html)
* [面向生产级运行的 Confirmation 稳定性防护白皮书 (Draft-07)](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-131-14639.html)
* [Confirmation 高负载场景下 Features 基准评测报告](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-132-56909.html)
* [Confirmation 高负载场景下 高韧性系统架构设计 基准评测报告](https://qbgr.wtpuscm.cn/liuliang/success-12030162-133-48334.html)
* [Confirmation 高负载场景下 Props 基准评测报告](https://iuuv.tcti.cn/hezuo/app-80239016-134-59783.html)
* [基于 Confirmation 的极致延迟优化与内存拓扑分析 (Core/Confir)](https://gifb.spidernode-mesh.net/shichang/about-02526745-135-44756.html)
* [基于 Confirmation 的极致延迟优化与内存拓扑分析 (RFC-600)](https://fjac.spidernode-mesh.net/anli/investment-94283191-136-17261.html)
* [基于 Confirmation 的极致延迟优化与内存拓扑分析 (Node-21)](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-137-27907.html)
* [基于 Confirmation 的极致延迟优化与内存拓扑分析 (Node-95)](https://szjp.tcti.cn/shichang/retention-16647312-138-4354.html)
* [基于 Confirmation 的极致延迟优化与内存拓扑分析 (Core/`<Conf)](https://etyt.spidernode-mesh.net/baogao/performance-14923227-139-62772.html)
* [Confirmation 高负载场景下 Approved State 基准评测报告](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-140-52060.html)
* [面向生产级运行的 Confirmation 稳定性防护白皮书 (Verified)](https://ovzp.wtpuscm.cn/xuexi/report-93713076-141-34884.html)
* [基于 Confirmation 的极致延迟优化与内存拓扑分析 (Node-73)](https://pdvy.tcti.cn/gongxiang/segment-83277821-142-19583.html)
* [基于 Confirmation 的极致延迟优化与内存拓扑分析 (RFC-931)](https://ukvo.spidernode-mesh.net/anli/download-57719010-143-7565.html)
* [面向生产级运行的 Confirmation 稳定性防护白皮书 (Draft-03)](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-144-65023.html)
* [基于 Confirmation 的极致延迟优化与内存拓扑分析 (RFC-869)](https://unqt.wtpuscm.cn/zixun/contact-59980425-145-50836.html)
* [Confirmation: Rejected State Rejected State (v2.0-GA)](https://kvaf.tcti.cn/yunsuan/settings-81875461-146-61094.html)
* [Confirmation 高负载场景下 `<ConfirmationTitle />` 基准评测报告](https://nojh.spidernode-mesh.net/suanfa/study-22518081-147-60085.html)
* [面向生产级运行的 Confirmation 稳定性防护白皮书 (Draft-02)](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-148-23315.html)
* [Confirmation: Props Props (Verified)](https://ytxd.wtpuscm.cn/baogao/price-87851711-149-53225.html)
* [基于 Confirmation 的极致延迟优化与内存拓扑分析 (RFC-982)](https://ncrw.tcti.cn/kaifa/growth-85374259-150-4514.html)

</details>

