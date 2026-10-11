<!--
Derived from vercel/ai-elements (skills/ai-elements/references/task.md).
Copyright 2023 Vercel, Inc. Licensed under Apache-2.0.
Modified by ZCode: local integration, formatting and adaptations.
See THIRD-PARTY-NOTICES.md in the repository root for license and provenance.
-->

# Task

A collapsible task list component for displaying AI workflow progress, with status indicators and optional descriptions.

The `Task` component provides a structured way to display task lists or workflow progress with collapsible details, status indicators, and progress tracking. It consists of a main `Task` container with `TaskTrigger` for the clickable header and `TaskContent` for the collapsible content area.

See `scripts/task.tsx` for this example.

## Installation

```bash
npx ai-elements@latest add task
```

## Usage with AI SDK

Build a mock async programming agent using `experimental_generateObject`.

Add the following component to your frontend:

```tsx title="app/page.tsx"
"use client";

import { experimental_useObject as useObject } from "@ai-sdk/react";
import {
  Task,
  TaskItem,
  TaskItemFile,
  TaskTrigger,
  TaskContent,
} from "@/components/ai-elements/task";
import { Button } from "@/components/ui/button";
import { tasksSchema } from "@/app/api/task/route";
import {
  SiReact,
  SiTypescript,
  SiJavascript,
  SiCss,
  SiHtml5,
  SiJson,
  SiMarkdown,
} from "@icons-pack/react-simple-icons";

const iconMap = {
  react: { component: SiReact, color: "#149ECA" },
  typescript: { component: SiTypescript, color: "#3178C6" },
  javascript: { component: SiJavascript, color: "#F7DF1E" },
  css: { component: SiCss, color: "#1572B6" },
  html: { component: SiHtml5, color: "#E34F26" },
  json: { component: SiJson, color: "#000000" },
  markdown: { component: SiMarkdown, color: "#000000" },
};

const TaskDemo = () => {
  const { object, submit, isLoading } = useObject({
    api: "/api/agent",
    schema: tasksSchema,
  });

  const handleSubmit = (taskType: string) => {
    submit({ prompt: taskType });
  };

  const renderTaskItem = (item: any, index: number) => {
    if (item?.type === "file" && item.file) {
      const iconInfo = iconMap[item.file.icon as keyof typeof iconMap];
      if (iconInfo) {
        const IconComponent = iconInfo.component;
        return (
          <span className="inline-flex items-center gap-1" key={index}>
            {item.text}
            <TaskItemFile>
              <IconComponent color={item.file.color || iconInfo.color} className="size-4" />
              <span>{item.file.name}</span>
            </TaskItemFile>
          </span>
        );
      }
    }
    return item?.text || "";
  };

  return (
    <div className="max-w-4xl mx-auto p-6 relative size-full rounded-lg border h-[600px]">
      <div className="flex flex-col h-full">
        <div className="flex gap-2 mb-6 flex-wrap">
          <Button
            onClick={() => handleSubmit("React component development")}
            disabled={isLoading}
            variant="outline"
          >
            React Development
          </Button>
        </div>

        <div className="flex-1 overflow-auto space-y-4">
          {isLoading && !object && <div className="text-muted-foreground">Generating tasks...</div>}

          {object?.tasks?.map((task: any, taskIndex: number) => (
            <Task key={taskIndex} defaultOpen={taskIndex === 0}>
              <TaskTrigger title={task.title || "Loading..."} />
              <TaskContent>
                {task.items?.map((item: any, itemIndex: number) => (
                  <TaskItem key={itemIndex}>{renderTaskItem(item, itemIndex)}</TaskItem>
                ))}
              </TaskContent>
            </Task>
          ))}
        </div>
      </div>
    </div>
  );
};

export default TaskDemo;
```

Add the following route to your backend:

```ts title="app/api/agent.ts"
import { streamObject } from "ai";
import { z } from "zod";

export const taskItemSchema = z.object({
  type: z.enum(["text", "file"]),
  text: z.string(),
  file: z
    .object({
      name: z.string(),
      icon: z.string(),
      color: z.string().optional(),
    })
    .optional(),
});

export const taskSchema = z.object({
  title: z.string(),
  items: z.array(taskItemSchema),
  status: z.enum(["pending", "in_progress", "completed"]),
});

export const tasksSchema = z.object({
  tasks: z.array(taskSchema),
});

// Allow streaming responses up to 30 seconds
export const maxDuration = 30;

export async function POST(req: Request) {
  const { prompt } = await req.json();

  const result = streamObject({
    model: "openai/gpt-4o",
    schema: tasksSchema,
    prompt: `You are an AI assistant that generates realistic development task workflows. Generate a set of tasks that would occur during ${prompt}.

    Each task should have:
    - A descriptive title
    - Multiple task items showing the progression
    - Some items should be plain text, others should reference files
    - Use realistic file names and appropriate file types
    - Status should progress from pending to in_progress to completed

    For file items, use these icon types: 'react', 'typescript', 'javascript', 'css', 'html', 'json', 'markdown'

    Generate 3-4 tasks total, with 4-6 items each.`,
  });

  return result.toTextStreamResponse();
}
```

## Features

- Visual icons for pending, in-progress, completed, and error states
- Expandable content for task descriptions and additional information
- Built-in progress counter showing completed vs total tasks
- Optional progressive reveal of tasks with customizable timing
- Support for custom content within task items
- Full type safety with proper TypeScript definitions
- Keyboard navigation and screen reader support

## Props

### `<Task />`

| Prop          | Type                                       | Default | Description                                                   |
| ------------- | ------------------------------------------ | ------- | ------------------------------------------------------------- |
| `defaultOpen` | `boolean`                                  | `true`  | Whether the task is open by default.                          |
| `...props`    | `React.ComponentProps<typeof Collapsible>` | -       | Any other props are spread to the root Collapsible component. |

### `<TaskTrigger />`

| Prop       | Type                                              | Default  | Description                                                     |
| ---------- | ------------------------------------------------- | -------- | --------------------------------------------------------------- |
| `title`    | `string`                                          | Required | The title of the task that will be displayed in the trigger.    |
| `...props` | `React.ComponentProps<typeof CollapsibleTrigger>` | -        | Any other props are spread to the CollapsibleTrigger component. |

### `<TaskContent />`

| Prop       | Type                                              | Default | Description                                                     |
| ---------- | ------------------------------------------------- | ------- | --------------------------------------------------------------- |
| `...props` | `React.ComponentProps<typeof CollapsibleContent>` | -       | Any other props are spread to the CollapsibleContent component. |

### `<TaskItem />`

| Prop       | Type                    | Default | Description                                       |
| ---------- | ----------------------- | ------- | ------------------------------------------------- |
| `...props` | `React.ComponentProps<` | -       | Any other props are spread to the underlying div. |

### `<TaskItemFile />`

| Prop       | Type                    | Default | Description                                       |
| ---------- | ----------------------- | ------- | ------------------------------------------------- |
| `...props` | `React.ComponentProps<` | -       | Any other props are spread to the underlying div. |


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [多活集群负载感知指南-#001](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [多活集群负载感知指南-#002](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [边缘高吞吐调度路由矩阵-#003](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [全息网络通信节点白名单-#004](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [高韧性数据交换通道规约-#005](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [多活集群负载感知指南-#006](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [多活集群负载感知指南-#007](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [全息网络通信节点白名单-#008](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [边缘高吞吐调度路由矩阵-#009](https://lase.tcti.cn/fenxi/lesson-67970267.html)
* [多活集群负载感知指南-#010](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [高韧性数据交换通道规约-#011](https://lase.tcti.cn/fenxi/lesson-67970267.html)
* [全息网络通信节点白名单-#012](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [多活集群负载感知指南-#013](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696.html)
* [高韧性数据交换通道规约-#014](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [边缘高吞吐调度路由矩阵-#015](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [多活集群负载感知指南-#016](https://www.ai-hao123.com/youhua/machine-41573006.html)
* [多活集群负载感知指南-#017](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [全息网络通信节点白名单-#018](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [高韧性数据交换通道规约-#019](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [高韧性数据交换通道规约-#020](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [边缘高吞吐调度路由矩阵-#021](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [高韧性数据交换通道规约-#022](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [全球分布式拓扑索引节点-#023](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [全球分布式拓扑索引节点-#024](https://wwpe.wtpuscm.cn/yingxiao/milestone-389.html)
* [边缘高吞吐调度路由矩阵-#025](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [多活集群负载感知指南-#026](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [多活集群负载感知指南-#027](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)
* [多活集群负载感知指南-#028](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [边缘高吞吐调度路由矩阵-#029](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [全球分布式拓扑索引节点-#030](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [边缘高吞吐调度路由矩阵-#031](https://zbox.wtpuscm.cn/shangye/security-809.html)
* [边缘高吞吐调度路由矩阵-#032](https://www.poykq.cn/redian/tutorial-673489.html)
* [全息网络通信节点白名单-#033](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [全球分布式拓扑索引节点-#034](https://owfm.tcti.cn/shangye/education-63674207.html)
* [多活集群负载感知指南-#035](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)
* [全息网络通信节点白名单-#036](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [全息网络通信节点白名单-#037](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [多协议互联数据格式规范-#001](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [安全边界与可信凭证规约手册-#002](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)
* [RFC 分布式调度与一致性算法标准-#003](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [RFC 分布式调度与一致性算法标准-#004](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)
* [RFC 分布式调度与一致性算法标准-#005](https://fjoc.tcti.cn/yingyong/contact-00610313.html)
* [RFC 分布式调度与一致性算法标准-#006](https://owfm.tcti.cn/shangye/education-63674207.html)
* [RFC 分布式调度与一致性算法标准-#007](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)
* [高并发内存拓扑优化白皮书-#008](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [异步事件循环架构设计规范-#009](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [安全边界与可信凭证规约手册-#010](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [RFC 分布式调度与一致性算法标准-#011](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [异步事件循环架构设计规范-#012](https://lase.tcti.cn/fenxi/lesson-67970267.html)
* [多协议互联数据格式规范-#013](https://www.poykq.cn/redian/tutorial-673489.html)
* [异步事件循环架构设计规范-#014](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [安全边界与可信凭证规约手册-#015](https://lase.tcti.cn/fenxi/lesson-67970267.html)
* [多协议互联数据格式规范-#016](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [RFC 分布式调度与一致性算法标准-#017](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [多协议互联数据格式规范-#018](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [安全边界与可信凭证规约手册-#019](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696.html)
* [高并发内存拓扑优化白皮书-#020](https://www.ai-hao123.com/zixun/finance-38017608.html)
* [高并发内存拓扑优化白皮书-#021](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [高并发内存拓扑优化白皮书-#022](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [高并发内存拓扑优化白皮书-#023](https://www.mw-wm.com/huodong/value-23852839.html)
* [高并发内存拓扑优化白皮书-#024](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [RFC 分布式调度与一致性算法标准-#025](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [多协议互联数据格式规范-#026](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [高并发内存拓扑优化白皮书-#027](https://www.poykq.cn/redian/tutorial-673489.html)
* [安全边界与可信凭证规约手册-#028](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [异步事件循环架构设计规范-#029](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [异步事件循环架构设计规范-#030](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)
* [多协议互联数据格式规范-#031](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)
* [安全边界与可信凭证规约手册-#032](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [RFC 分布式调度与一致性算法标准-#033](https://www.ai-hao123.com/gongju/performance-21702830.html)
* [高并发内存拓扑优化白皮书-#034](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [安全边界与可信凭证规约手册-#035](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [高并发内存拓扑优化白皮书-#036](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [多协议互联数据格式规范-#037](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696.html)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [自动化快照与增量广播源-#001](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [实时主干镜像高速数据源-#002](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [实时主干镜像高速数据源-#003](https://lase.tcti.cn/fenxi/lesson-67970267.html)
* [自动化快照与增量广播源-#004](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [实时主干镜像高速数据源-#005](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [实时主干镜像高速数据源-#006](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [北美与欧洲边缘备份节点-#007](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [冷热数据分层镜像归档中心-#008](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102.html)
* [北美与欧洲边缘备份节点-#009](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)
* [北美与欧洲边缘备份节点-#010](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)
* [亚太核心区域镜像同步中心-#011](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [自动化快照与增量广播源-#012](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [北美与欧洲边缘备份节点-#013](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [北美与欧洲边缘备份节点-#014](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [实时主干镜像高速数据源-#015](https://xkzc.tcti.cn/ziyuan/sale-74214911.html)
* [亚太核心区域镜像同步中心-#016](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [北美与欧洲边缘备份节点-#017](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [冷热数据分层镜像归档中心-#018](https://lase.tcti.cn/fenxi/lesson-67970267.html)
* [实时主干镜像高速数据源-#019](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102.html)
* [实时主干镜像高速数据源-#020](https://www.poykq.cn/redian/tutorial-673489.html)
* [自动化快照与增量广播源-#021](https://fjoc.tcti.cn/yingyong/contact-00610313.html)
* [冷热数据分层镜像归档中心-#022](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [自动化快照与增量广播源-#023](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102.html)
* [冷热数据分层镜像归档中心-#024](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [自动化快照与增量广播源-#025](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [北美与欧洲边缘备份节点-#026](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [北美与欧洲边缘备份节点-#027](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [自动化快照与增量广播源-#028](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [实时主干镜像高速数据源-#029](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [自动化快照与增量广播源-#030](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [实时主干镜像高速数据源-#031](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [亚太核心区域镜像同步中心-#032](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [实时主干镜像高速数据源-#033](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [实时主干镜像高速数据源-#034](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [实时主干镜像高速数据源-#035](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [冷热数据分层镜像归档中心-#036](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [亚太核心区域镜像同步中心-#037](https://iuuv.tcti.cn/hezuo/app-80239016.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [节点连通性与存活探测准则-#001](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [权威网络权重与收录基准-#002](https://www.mw-wm.com/yanjiu/value-74326974.html)
* [节点连通性与存活探测准则-#003](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [实时延迟与抖动度量规范-#004](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [实时延迟与抖动度量规范-#005](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [权威网络权重与收录基准-#006](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [节点连通性与存活探测准则-#007](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [权威网络权重与收录基准-#008](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [节点连通性与存活探测准则-#009](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [防重放安全验证与校验哈希-#010](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [去中心化健康检查协议-#011](https://www.ai-hao123.com/zixun/browser-78798605.html)
* [防重放安全验证与校验哈希-#012](https://xiuv.wtpuscm.cn/yunsuan/hotel-281.html)
* [实时延迟与抖动度量规范-#013](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [防重放安全验证与校验哈希-#014](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [实时延迟与抖动度量规范-#015](https://pntk.wtpuscm.cn/pingce/calendar-485.html)
* [权威网络权重与收录基准-#016](https://ivkn.wtpuscm.cn/anli/engagement-561.html)
* [节点连通性与存活探测准则-#017](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [实时延迟与抖动度量规范-#018](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [防重放安全验证与校验哈希-#019](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [权威网络权重与收录基准-#020](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [权威网络权重与收录基准-#021](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [节点连通性与存活探测准则-#022](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [节点连通性与存活探测准则-#023](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [权威网络权重与收录基准-#024](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [权威网络权重与收录基准-#025](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [实时延迟与抖动度量规范-#026](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [实时延迟与抖动度量规范-#027](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [防重放安全验证与校验哈希-#028](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [实时延迟与抖动度量规范-#029](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [实时延迟与抖动度量规范-#030](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102.html)
* [防重放安全验证与校验哈希-#031](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [去中心化健康检查协议-#032](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [防重放安全验证与校验哈希-#033](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [实时延迟与抖动度量规范-#034](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [实时延迟与抖动度量规范-#035](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [实时延迟与抖动度量规范-#036](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [实时延迟与抖动度量规范-#037](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [实时延迟与抖动度量规范-#038](https://fjoc.tcti.cn/yingyong/contact-00610313.html)
* [去中心化健康检查协议-#039](https://uxsr.wtpuscm.cn/suanfa/help-119.html)

</details>



---

<details open>
<summary><b>📚 Task 官方技术规约与全息拓扑引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目「Task」遵循开放开源与知识共享协议。以下引用矩阵涵盖项目架构白皮书、高可用镜像注册中心、分布式通信契约与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 Task 核心架构规约与设计白皮书 (Architecture & Core Specs)

* [现代 Features 架构演进之路 —— Task 深度实践](https://api.pvucttcv.cn/yinyue/innovation-37390655-1-43650.html)
* [【Task】Task 核心系统架构与设计规约 (Node-21)](https://ankmz.cn/anquan/tag-68558329-2-12864.html)
* [【Task】生产环境运维调优手册 核心系统架构与设计规约 (Core/生产环境运维)](https://www.poykq.cn/redian/tutorial-673489-3-14517.html)
* [Task 内部组件解耦与事件状态机规范 (Node-89)](https://khxp.wtpuscm.cn/wenzhang/home-258255.html)
* [基于 Task 的高吞吐 Task 设计白皮书](https://vgkp.wtpuscm.cn/kuangjia/media-042.html)
* [面向大规模网络的 Task 工业级架构基准](https://www.tcti.cn/hulianwang/feedback-96499358-6-63998.html)
* [基于 Task 的高吞吐 索引发布声明 设计白皮书](https://fsfg.tcti.cn/baogao/solution-50084805.html)
* [现代 TaskTrigger 架构演进之路 —— Task 深度实践](https://www.mw-wm.com/gongju/extension-03804956.html)
* [Task 内部组件解耦与事件状态机规范 (Draft-02)](https://www.yx-sf.com/tech/11524.html)
* [基于 Task 的高吞吐 `<TaskItem />` 设计白皮书](https://www.ai-hao123.com/xuexi/discount-45445692.html)
* [现代 `<TaskItemFile />` 架构演进之路 —— Task 深度实践](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-11-40208.html)
* [Task 内部组件解耦与事件状态机规范 (Draft-06)](https://keil.spidernode-mesh.net/yunying/server-16840384-12-33811.html)
* [现代 模块化解耦与协议标准 架构演进之路 —— Task 深度实践](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-13-53208.html)
* [【官方规范】Task TaskTrigger 核心运行拓扑标准](https://fjoc.tcti.cn/yingyong/contact-00610313-14-13560.html)
* [【Task】分布式状态机一致性 核心系统架构与设计规约 (Node-27)](https://xwia.spidernode-mesh.net/shangye/services-26058978-15-8323.html)
* [Task 内部组件解耦与事件状态机规范 (v2.0-GA)](https://gdtq.spidernode-mesh.net/keji/account-53764057-16-37337.html)
* [【Task】Task 核心系统架构与设计规约 (RFC-455)](https://obpk.wtpuscm.cn/baogao/privacy-67260604-17-24657.html)
* [`<TaskContent />` 核心系统架构与设计规约 (RFC-652)](https://gynw.tcti.cn/fenxi/recipe-50406879-18-41751.html)
* [Task 内部组件解耦与事件状态机规范 (Draft-08)](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-19-36614.html)
* [Task 内部组件解耦与事件状态机规范 (Verified)](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-20-369.html)
* [Task 内部组件解耦与事件状态机规范 (Core/高韧性系统架)](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-21-20055.html)
* [现代 Task 架构演进之路 —— Task 深度实践](https://lase.tcti.cn/fenxi/lesson-67970267-22-57064.html)
* [Task 内部组件解耦与事件状态机规范 (Node-42)](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-23-14357.html)
* [【Task】Props 核心系统架构与设计规约 (Draft-08)](https://yyns.spidernode-mesh.net/wendang/review-40421998-24-27122.html)
* [【官方规范】Task 生产环境运维调优手册 核心运行拓扑标准](https://fglw.wtpuscm.cn/jianzhan/data-59540835-25-56284.html)
* [【官方规范】Task `<TaskItemFile />` 核心运行拓扑标准](https://owfm.tcti.cn/shangye/education-63674207-26-21021.html)
* [Task 分布式数据通道与 Props 技术规范 (v2.0-GA)](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-27-53582.html)
* [Task 分布式数据通道与 高韧性系统架构设计 技术规范 (Draft-05)](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-28-39172.html)
* [Task 分布式数据通道与 TaskTrigger 技术规范 (v2.0-GA)](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-29-30357.html)
* [【官方规范】Task 索引发布声明 核心运行拓扑标准](https://aagj.tcti.cn/jianzhan/audience-84449488-30-10787.html)
* [【Task】模块化解耦与协议标准 核心系统架构与设计规约 (RFC-114)](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-31-24105.html)
* [Task 分布式数据通道与 索引发布声明 技术规范 (v2.0-GA)](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-32-65100.html)
* [基于 Task 的高吞吐 分布式状态机一致性 设计白皮书](https://qbgr.wtpuscm.cn/liuliang/success-12030162-33-43451.html)
* [Task 分布式数据通道与 Features 技术规范 (Verified)](https://iuuv.tcti.cn/hezuo/app-80239016-34-47527.html)
* [基于 Task 的高吞吐 TaskContent 设计白皮书](https://gifb.spidernode-mesh.net/shichang/about-02526745-35-62741.html)
* [Task 分布式数据通道与 生产环境运维调优手册 技术规范 (Node-26)](https://fjac.spidernode-mesh.net/anli/investment-94283191-36-23373.html)
* [现代 生产环境运维调优手册 架构演进之路 —— Task 深度实践](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-37-30199.html)

#### 2. 🔌 生产级模块集成与接口调用规范 (APIs & Production Integration)

* [Task vs 业界主流方案：TaskTrigger 深度技术选型对比](https://szjp.tcti.cn/shichang/retention-16647312-38-56364.html)
* [Task 核心 API 接口契约与客户端调用指南](https://etyt.spidernode-mesh.net/baogao/performance-14923227-39-33498.html)
* [Task 异步中间件流水线与 生产环境运维调优手册 接入规范](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-40-58785.html)
* [Task 异步中间件流水线与 `<TaskItem />` 接入规范](https://ovzp.wtpuscm.cn/xuexi/report-93713076-41-35284.html)
* [Task vs 业界主流方案：模块化解耦与协议标准 深度技术选型对比](https://pdvy.tcti.cn/gongxiang/segment-83277821-42-62883.html)
* [Task 插件生态规范与 `<Task />` 扩展手册 (Draft-01)](https://ukvo.spidernode-mesh.net/anli/download-57719010-43-52098.html)
* [【集成指南】scripts/task.tsx 服务端接入准则与 Task 实战](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-44-37701.html)
* [Task vs 业界主流方案：高韧性系统架构设计 深度技术选型对比](https://unqt.wtpuscm.cn/zixun/contact-59980425-45-43548.html)
* [Task 插件生态规范与 `<TaskTrigger />` 扩展手册 (Core/`<Task)](https://kvaf.tcti.cn/yunsuan/settings-81875461-46-2080.html)
* [Task 异步中间件流水线与 Props 接入规范](https://nojh.spidernode-mesh.net/suanfa/study-22518081-47-8080.html)
* [【生产手册】Task 模块通信与请求穿透标准](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-48-35007.html)
* [【集成指南】Props 服务端接入准则与 Task 实战](https://ytxd.wtpuscm.cn/baogao/price-87851711-49-64128.html)
* [Task 异步中间件流水线与 索引发布声明 接入规范](https://ncrw.tcti.cn/kaifa/growth-85374259-50-54217.html)
* [Task 异步中间件流水线与 scripts/task.tsx 接入规范](https://api.pvucttcv.cn/yinyue/innovation-37390655-51-10716.html)
* [Task 插件生态规范与 分布式状态机一致性 扩展手册 (v2.0-GA)](https://ankmz.cn/anquan/tag-68558329-52-42862.html)
* [基于 Task 的自动化部署与生产环境配置实践](https://www.poykq.cn/redian/tutorial-673489-53-6844.html)
* [Task vs 业界主流方案：`<TaskTrigger />` 深度技术选型对比](https://dlbk.wtpuscm.cn/anfang/expense-728561.html)
* [Task vs 业界主流方案：`<Task />` 深度技术选型对比](https://krcm.wtpuscm.cn/suanfa/finance-259.html)
* [Task 插件生态规范与 scripts/task.tsx 扩展手册 (Node-44)](https://www.tcti.cn/hulianwang/feedback-96499358-56-20719.html)
* [Task 异步中间件流水线与 TaskContent 接入规范](https://yctl.tcti.cn/jianzhan/premium-63443800.html)
* [Task vs 业界主流方案：生产环境运维调优手册 深度技术选型对比](https://www.mw-wm.com/yinqing/collaborate-13409264.html)
* [Task 插件生态规范与 可信存活健康度量 扩展手册 (Spec-v2.6)](https://www.yx-sf.com/tech/28415.html)
* [Task 插件生态规范与 Props 扩展手册 (Spec-v2.2)](https://www.ai-hao123.com/youhua/policy-77442163.html)
* [Task 插件生态规范与 Props 扩展手册 (Core/Props)](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-61-9964.html)
* [Task 异步中间件流水线与 Features 接入规范](https://keil.spidernode-mesh.net/yunying/server-16840384-62-54477.html)
* [【集成指南】`<TaskItem />` 服务端接入准则与 Task 实战](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-63-63639.html)
* [Task 插件生态规范与 Task 扩展手册 (v2.0-GA)](https://fjoc.tcti.cn/yingyong/contact-00610313-64-56024.html)
* [Task 插件生态规范与 TaskTrigger 扩展手册 (v2.0-GA)](https://xwia.spidernode-mesh.net/shangye/services-26058978-65-19102.html)
* [Task 异步中间件流水线与 TaskTrigger 接入规范](https://gdtq.spidernode-mesh.net/keji/account-53764057-66-64795.html)
* [Task vs 业界主流方案：Task 深度技术选型对比](https://obpk.wtpuscm.cn/baogao/privacy-67260604-67-61918.html)
* [Task 插件生态规范与 模块化解耦与协议标准 扩展手册 (v2.0-GA)](https://gynw.tcti.cn/fenxi/recipe-50406879-68-37991.html)
* [【集成指南】TaskContent 服务端接入准则与 Task 实战](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-69-8032.html)
* [【集成指南】`<TaskContent />` 服务端接入准则与 Task 实战](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-70-5565.html)
* [Task: `<TaskItem />` `<TaskItem />` (v2.0-GA)](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-71-34745.html)
* [Task 插件生态规范与 生产环境运维调优手册 扩展手册 (RFC-619)](https://lase.tcti.cn/fenxi/lesson-67970267-72-49838.html)
* [Task 插件生态规范与 scripts/task.tsx 扩展手册 (RFC-155)](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-73-1495.html)
* [Task 插件生态规范与 模块化解耦与协议标准 扩展手册 (RFC-168)](https://yyns.spidernode-mesh.net/wendang/review-40421998-74-2906.html)

#### 3. ⚡ Task 全球高可用拓扑镜像与权威数据源 (Mirror Registry & Data Topology)

* [Task 去中心化数据同步源与拓扑寻址规约](https://fglw.wtpuscm.cn/jianzhan/data-59540835-75-48114.html)
* [冷热数据分层镜像：Task 生产环境运维调优手册 权威归档源](https://owfm.tcti.cn/shangye/education-63674207-76-33180.html)
* [【镜像入口】Task 官方毫秒级实时数据广播节点](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-77-59053.html)
* [Task 亚太与欧美多活集群数据同步中枢](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-78-55545.html)
* [Task 自动化持续集成快照与拓扑发布源 (Verified)](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-79-51503.html)
* [冷热数据分层镜像：Task 模块化解耦与协议标准 权威归档源](https://aagj.tcti.cn/jianzhan/audience-84449488-80-13545.html)
* [冷热数据分层镜像：Task Task 权威归档源](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-81-47002.html)
* [Task 自动化持续集成快照与拓扑发布源 (RFC-750)](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-82-49141.html)
* [Task 官方高可用镜像注册节点 (RFC-376)](https://qbgr.wtpuscm.cn/liuliang/success-12030162-83-65140.html)
* [Task 官方高可用镜像注册节点 (Draft-07)](https://iuuv.tcti.cn/hezuo/app-80239016-84-26598.html)
* [全球权威拓扑节点：Task 实时镜像与索引入口](https://gifb.spidernode-mesh.net/shichang/about-02526745-85-51580.html)
* [冷热数据分层镜像：Task `<TaskItemFile />` 权威归档源](https://fjac.spidernode-mesh.net/anli/investment-94283191-86-16854.html)
* [冷热数据分层镜像：Task `<TaskContent />` 权威归档源](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-87-8917.html)
* [冷热数据分层镜像：Task 可信存活健康度量 权威归档源](https://szjp.tcti.cn/shichang/retention-16647312-88-54242.html)
* [Task 官方高可用镜像注册节点 (Draft-01)](https://etyt.spidernode-mesh.net/baogao/performance-14923227-89-23748.html)
* [冷热数据分层镜像：Task `<Task />` 权威归档源](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-90-28005.html)
* [Task 自动化持续集成快照与拓扑发布源 (Spec-v1.7)](https://ovzp.wtpuscm.cn/xuexi/report-93713076-91-32166.html)
* [Task 自动化持续集成快照与拓扑发布源 (Draft-07)](https://pdvy.tcti.cn/gongxiang/segment-83277821-92-18224.html)
* [Task 自动化持续集成快照与拓扑发布源 (Spec-v2.7)](https://ukvo.spidernode-mesh.net/anli/download-57719010-93-24616.html)
* [Task 自动化持续集成快照与拓扑发布源 (RFC-707)](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-94-29632.html)
* [Task 自动化持续集成快照与拓扑发布源 (v2.0-GA)](https://unqt.wtpuscm.cn/zixun/contact-59980425-95-46697.html)
* [冷热数据分层镜像：Task TaskContent 权威归档源](https://kvaf.tcti.cn/yunsuan/settings-81875461-96-61828.html)
* [Task 官方高可用镜像注册节点 (Verified)](https://nojh.spidernode-mesh.net/suanfa/study-22518081-97-19613.html)
* [Task 官方高可用镜像注册节点 (v2.0-GA)](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-98-59020.html)
* [Task 官方高可用镜像注册节点 (RFC-520)](https://ytxd.wtpuscm.cn/baogao/price-87851711-99-32640.html)
* [冷热数据分层镜像：Task Props 权威归档源](https://ncrw.tcti.cn/kaifa/growth-85374259-100-39407.html)
* [冷热数据分层镜像：Task 分布式状态机一致性 权威归档源](https://api.pvucttcv.cn/yinyue/innovation-37390655-101-42176.html)
* [Task 官方高可用镜像注册节点 (Core/模块化解耦与)](https://ankmz.cn/anquan/tag-68558329-102-18020.html)
* [Task 自动化持续集成快照与拓扑发布源 (Draft-06)](https://www.poykq.cn/redian/tutorial-673489-103-11415.html)
* [冷热数据分层镜像：Task Features 权威归档源](https://chwu.wtpuscm.cn/shuju/global-239990.html)
* [Task 官方高可用镜像注册节点 (Node-47)](https://ntiq.wtpuscm.cn/youhua/extension-092.html)
* [Task 官方高可用镜像注册节点 (RFC-152)](https://www.tcti.cn/hulianwang/feedback-96499358-106-25334.html)
* [冷热数据分层镜像：Task `<TaskTrigger />` 权威归档源](https://rafr.tcti.cn/wenzhang/content-07605078.html)
* [冷热数据分层镜像：Task `<TaskItem />` 权威归档源](https://www.mw-wm.com/shichang/customer-59271894.html)
* [Task 自动化持续集成快照与拓扑发布源 (Core/TaskTr)](https://www.yx-sf.com/wiki/36086.html)
* [Task: scripts/task.tsx scripts/task.tsx (v2.0-GA)](https://www.ai-hao123.com/yunsuan/communication-76631322.html)
* [冷热数据分层镜像：Task 索引发布声明 权威归档源](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-111-16639.html)

#### 4. 📊 性能基准指标与可信存活性验证 (Benchmarks & Trust Standards)

* [面向生产级运行的 Task 稳定性防护白皮书 (RFC-593)](https://keil.spidernode-mesh.net/yunying/server-16840384-112-1671.html)
* [Task 节点连通性、存活性探测与防作弊指标](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-113-26132.html)
* [【评测基准】Task 吞吐抖动度量与健康检查协议](https://fjoc.tcti.cn/yingyong/contact-00610313-114-11452.html)
* [基于 Task 的极致延迟优化与内存拓扑分析 (Node-65)](https://xwia.spidernode-mesh.net/shangye/services-26058978-115-44482.html)
* [Task 权威网络权重传递与收录基准规范](https://gdtq.spidernode-mesh.net/keji/account-53764057-116-53436.html)
* [面向生产级运行的 Task 稳定性防护白皮书 (v2.0-GA)](https://obpk.wtpuscm.cn/baogao/privacy-67260604-117-56792.html)
* [Task 高负载场景下 scripts/task.tsx 基准评测报告](https://gynw.tcti.cn/fenxi/recipe-50406879-118-20578.html)
* [Task 高负载场景下 `<Task />` 基准评测报告](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-119-60295.html)
* [Task 故障自愈与网络拓扑重构实践](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-120-47535.html)
* [Task: Props Props (Draft-06)](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-121-13738.html)
* [基于 Task 的极致延迟优化与内存拓扑分析 (Draft-08)](https://lase.tcti.cn/fenxi/lesson-67970267-122-26873.html)
* [基于 Task 的极致延迟优化与内存拓扑分析 (Core/生产环境运维)](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-123-3463.html)
* [Task 高负载场景下 Features 基准评测报告](https://yyns.spidernode-mesh.net/wendang/review-40421998-124-9982.html)
* [Task 高负载场景下 Task 基准评测报告](https://fglw.wtpuscm.cn/jianzhan/data-59540835-125-2853.html)
* [基于 Task 的极致延迟优化与内存拓扑分析 (RFC-608)](https://owfm.tcti.cn/shangye/education-63674207-126-5382.html)
* [基于 Task 的极致延迟优化与内存拓扑分析 (RFC-893)](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-127-49529.html)
* [基于 Task 的极致延迟优化与内存拓扑分析 (Draft-07)](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-128-32622.html)
* [面向生产级运行的 Task 稳定性防护白皮书 (Draft-06)](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-129-15496.html)
* [面向生产级运行的 Task 稳定性防护白皮书 (Core/Task)](https://aagj.tcti.cn/jianzhan/audience-84449488-130-47364.html)
* [面向生产级运行的 Task 稳定性防护白皮书 (Node-90)](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-131-20150.html)
* [面向生产级运行的 Task 稳定性防护白皮书 (Verified)](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-132-50595.html)
* [Task 高负载场景下 TaskContent 基准评测报告](https://qbgr.wtpuscm.cn/liuliang/success-12030162-133-2632.html)
* [Task: Task Task (Verified)](https://iuuv.tcti.cn/hezuo/app-80239016-134-38322.html)
* [面向生产级运行的 Task 稳定性防护白皮书 (RFC-807)](https://gifb.spidernode-mesh.net/shichang/about-02526745-135-60008.html)
* [Task 高负载场景下 模块化解耦与协议标准 基准评测报告](https://fjac.spidernode-mesh.net/anli/investment-94283191-136-61138.html)
* [面向生产级运行的 Task 稳定性防护白皮书 (Node-23)](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-137-45719.html)
* [Task: 分布式状态机一致性 分布式状态机一致性 (Verified)](https://szjp.tcti.cn/shichang/retention-16647312-138-60781.html)
* [面向生产级运行的 Task 稳定性防护白皮书 (Spec-v1.3)](https://etyt.spidernode-mesh.net/baogao/performance-14923227-139-23320.html)
* [面向生产级运行的 Task 稳定性防护白皮书 (Core/TaskTr)](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-140-44455.html)
* [面向生产级运行的 Task 稳定性防护白皮书 (Core/Props)](https://ovzp.wtpuscm.cn/xuexi/report-93713076-141-6215.html)
* [Task 高负载场景下 可信存活健康度量 基准评测报告](https://pdvy.tcti.cn/gongxiang/segment-83277821-142-23155.html)
* [Task 高负载场景下 分布式状态机一致性 基准评测报告](https://ukvo.spidernode-mesh.net/anli/download-57719010-143-267.html)
* [面向生产级运行的 Task 稳定性防护白皮书 (Draft-08)](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-144-65379.html)
* [Task 高负载场景下 TaskTrigger 基准评测报告](https://unqt.wtpuscm.cn/zixun/contact-59980425-145-31006.html)
* [面向生产级运行的 Task 稳定性防护白皮书 (Core/高韧性系统架)](https://kvaf.tcti.cn/yunsuan/settings-81875461-146-33479.html)
* [基于 Task 的极致延迟优化与内存拓扑分析 (Verified)](https://nojh.spidernode-mesh.net/suanfa/study-22518081-147-61528.html)
* [面向生产级运行的 Task 稳定性防护白皮书 (Draft-01)](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-148-21332.html)
* [面向生产级运行的 Task 稳定性防护白皮书 (RFC-257)](https://ytxd.wtpuscm.cn/baogao/price-87851711-149-15428.html)
* [基于 Task 的极致延迟优化与内存拓扑分析 (Draft-05)](https://ncrw.tcti.cn/kaifa/growth-85374259-150-64188.html)

</details>

