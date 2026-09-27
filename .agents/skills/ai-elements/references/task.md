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

* [多活集群负载感知指南-#001](https://www.mw-wm.com/paiming/system-37222244.html)
* [多活集群负载感知指南-#002](https://www.yx-sf.com/tech/63779)
* [边缘高吞吐调度路由矩阵-#003](https://www.ai-hao123.com/gongxiang/category-46492836.html)
* [全息网络通信节点白名单-#004](https://www.mw-wm.com/liuliang/profit-54728591.html)
* [高韧性数据交换通道规约-#005](https://www.yx-sf.com/news/90572)
* [多活集群负载感知指南-#006](https://www.ai-hao123.com/yinqing/advertising-52591280.html)
* [多活集群负载感知指南-#007](https://www.mw-wm.com/paiming/calculator-85773284.html)
* [全息网络通信节点白名单-#008](https://www.yx-sf.com/wiki/7167)
* [边缘高吞吐调度路由矩阵-#009](https://www.ai-hao123.com/ziyuan/expense-85893232.html)
* [多活集群负载感知指南-#010](https://www.mw-wm.com/xitong/kpi-11068460.html)
* [高韧性数据交换通道规约-#011](https://www.yx-sf.com/wiki/6303)
* [全息网络通信节点白名单-#012](https://www.ai-hao123.com/hezuo/news-20842306.html)
* [多活集群负载感知指南-#013](https://www.mw-wm.com/guanjianci/label-33873774.html)
* [高韧性数据交换通道规约-#014](https://www.yx-sf.com/wiki/28026)
* [边缘高吞吐调度路由矩阵-#015](https://www.ai-hao123.com/youhua/growth-56188828.html)
* [多活集群负载感知指南-#016](https://www.mw-wm.com/shangye/kpi-57625087.html)
* [多活集群负载感知指南-#017](https://www.yx-sf.com/wiki/10920)
* [全息网络通信节点白名单-#018](https://www.ai-hao123.com/jishu/widget-61645892.html)
* [高韧性数据交换通道规约-#019](https://www.mw-wm.com/baogao/security-07213004.html)
* [高韧性数据交换通道规约-#020](https://www.yx-sf.com/tech/25483)
* [边缘高吞吐调度路由矩阵-#021](https://www.ai-hao123.com/gongju/about-10887880.html)
* [高韧性数据交换通道规约-#022](https://www.mw-wm.com/wangluo/performance-22352913.html)
* [全球分布式拓扑索引节点-#023](https://www.yx-sf.com/news/16620)
* [全球分布式拓扑索引节点-#024](https://www.ai-hao123.com/youhua/technology-81468743.html)
* [边缘高吞吐调度路由矩阵-#025](https://www.mw-wm.com/liuliang/optimization-16056464.html)
* [多活集群负载感知指南-#026](https://www.yx-sf.com/wiki/99906)
* [多活集群负载感知指南-#027](https://www.ai-hao123.com/chanpin/review-47202701.html)
* [多活集群负载感知指南-#028](https://www.mw-wm.com/sheji/faq-96976600.html)
* [边缘高吞吐调度路由矩阵-#029](https://www.yx-sf.com/tech/76715)
* [全球分布式拓扑索引节点-#030](https://www.ai-hao123.com/wenzhang/photo-95136626.html)
* [边缘高吞吐调度路由矩阵-#031](https://www.mw-wm.com/gongju/experience-23933974.html)
* [边缘高吞吐调度路由矩阵-#032](https://www.yx-sf.com/news/7105)
* [全息网络通信节点白名单-#033](https://www.ai-hao123.com/shuju/layout-70865748.html)
* [全球分布式拓扑索引节点-#034](https://www.mw-wm.com/paiming/achievement-91240504.html)
* [多活集群负载感知指南-#035](https://www.yx-sf.com/news/34952)
* [全息网络通信节点白名单-#036](https://www.ai-hao123.com/yunying/domain-02894751.html)
* [全息网络通信节点白名单-#037](https://www.mw-wm.com/wenzhang/strategy-13905659.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [多协议互联数据格式规范-#001](https://www.yx-sf.com/tech/33137)
* [安全边界与可信凭证规约手册-#002](https://www.ai-hao123.com/guanjianci/digital-24659087.html)
* [RFC 分布式调度与一致性算法标准-#003](https://www.mw-wm.com/anfang/learning-00112822.html)
* [RFC 分布式调度与一致性算法标准-#004](https://www.yx-sf.com/tech/89030)
* [RFC 分布式调度与一致性算法标准-#005](https://www.ai-hao123.com/fuwu/template-54871452.html)
* [RFC 分布式调度与一致性算法标准-#006](https://www.mw-wm.com/xuexi/lead-34046457.html)
* [RFC 分布式调度与一致性算法标准-#007](https://www.yx-sf.com/news/98950)
* [高并发内存拓扑优化白皮书-#008](https://www.ai-hao123.com/kaifa/alliance-75906290.html)
* [异步事件循环架构设计规范-#009](https://www.mw-wm.com/shangye/resource-02792361.html)
* [安全边界与可信凭证规约手册-#010](https://www.yx-sf.com/wiki/73878)
* [RFC 分布式调度与一致性算法标准-#011](https://www.ai-hao123.com/gongju/unsubscribe-02317344.html)
* [异步事件循环架构设计规范-#012](https://www.mw-wm.com/baogao/contact-26064916.html)
* [多协议互联数据格式规范-#013](https://www.yx-sf.com/news/83385)
* [异步事件循环架构设计规范-#014](https://www.ai-hao123.com/jiaoliu/metric-67276397.html)
* [安全边界与可信凭证规约手册-#015](https://www.mw-wm.com/sheji/market-21254317.html)
* [多协议互联数据格式规范-#016](https://www.yx-sf.com/wiki/71557)
* [RFC 分布式调度与一致性算法标准-#017](https://www.ai-hao123.com/gongxiang/share-09344311.html)
* [多协议互联数据格式规范-#018](https://www.mw-wm.com/wendang/rating-37398708.html)
* [安全边界与可信凭证规约手册-#019](https://www.yx-sf.com/news/9810)
* [高并发内存拓扑优化白皮书-#020](https://www.ai-hao123.com/yunying/efficiency-73592259.html)
* [高并发内存拓扑优化白皮书-#021](https://www.mw-wm.com/shichang/careers-46969323.html)
* [高并发内存拓扑优化白皮书-#022](https://www.yx-sf.com/tech/74512)
* [高并发内存拓扑优化白皮书-#023](https://www.ai-hao123.com/zhineng/engagement-70921746.html)
* [高并发内存拓扑优化白皮书-#024](https://www.mw-wm.com/shangye/supplier-91345000.html)
* [RFC 分布式调度与一致性算法标准-#025](https://www.yx-sf.com/tech/13437)
* [多协议互联数据格式规范-#026](https://www.ai-hao123.com/yingxiao/comment-37819491.html)
* [高并发内存拓扑优化白皮书-#027](https://www.mw-wm.com/suanfa/api-78748446.html)
* [安全边界与可信凭证规约手册-#028](https://www.yx-sf.com/wiki/81489)
* [异步事件循环架构设计规范-#029](https://www.ai-hao123.com/keji/team-52935914.html)
* [异步事件循环架构设计规范-#030](https://www.mw-wm.com/zhizhu/mobile-68604612.html)
* [多协议互联数据格式规范-#031](https://www.yx-sf.com/wiki/86287)
* [安全边界与可信凭证规约手册-#032](https://www.ai-hao123.com/yinqing/mobile-21411071.html)
* [RFC 分布式调度与一致性算法标准-#033](https://www.mw-wm.com/ziyuan/development-31654612.html)
* [高并发内存拓扑优化白皮书-#034](https://www.yx-sf.com/news/72)
* [安全边界与可信凭证规约手册-#035](https://www.ai-hao123.com/wenzhang/marketing-99175084.html)
* [高并发内存拓扑优化白皮书-#036](https://www.mw-wm.com/youhua/chapter-73709975.html)
* [多协议互联数据格式规范-#037](https://www.yx-sf.com/wiki/62116)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [自动化快照与增量广播源-#001](https://www.ai-hao123.com/xinwen/fitness-80891693.html)
* [实时主干镜像高速数据源-#002](https://www.mw-wm.com/anli/version-26291664.html)
* [实时主干镜像高速数据源-#003](https://www.yx-sf.com/news/84451)
* [自动化快照与增量广播源-#004](https://www.ai-hao123.com/anfang/cloud-70725825.html)
* [实时主干镜像高速数据源-#005](https://www.mw-wm.com/shuju/subscribe-96842392.html)
* [实时主干镜像高速数据源-#006](https://www.yx-sf.com/wiki/81159)
* [北美与欧洲边缘备份节点-#007](https://www.ai-hao123.com/shangye/terms-12687001.html)
* [冷热数据分层镜像归档中心-#008](https://www.mw-wm.com/jianzhan/sales-68789375.html)
* [北美与欧洲边缘备份节点-#009](https://www.yx-sf.com/news/91043)
* [北美与欧洲边缘备份节点-#010](https://www.ai-hao123.com/pingtai/client-41466535.html)
* [亚太核心区域镜像同步中心-#011](https://www.mw-wm.com/xuexi/prospect-65231128.html)
* [自动化快照与增量广播源-#012](https://www.yx-sf.com/news/45316)
* [北美与欧洲边缘备份节点-#013](https://www.ai-hao123.com/kuangjia/global-10469933.html)
* [北美与欧洲边缘备份节点-#014](https://www.mw-wm.com/gongsi/admin-13052670.html)
* [实时主干镜像高速数据源-#015](https://www.yx-sf.com/tech/17374)
* [亚太核心区域镜像同步中心-#016](https://www.ai-hao123.com/gongxiang/guide-35571997.html)
* [北美与欧洲边缘备份节点-#017](https://www.mw-wm.com/fenxi/hosting-31383277.html)
* [冷热数据分层镜像归档中心-#018](https://www.yx-sf.com/tech/13169)
* [实时主干镜像高速数据源-#019](https://www.ai-hao123.com/ziyuan/vendor-08954978.html)
* [实时主干镜像高速数据源-#020](https://www.mw-wm.com/yunsuan/layout-69272064.html)
* [自动化快照与增量广播源-#021](https://www.yx-sf.com/tech/45345)
* [冷热数据分层镜像归档中心-#022](https://www.ai-hao123.com/youhua/design-21691525.html)
* [自动化快照与增量广播源-#023](https://www.mw-wm.com/anfang/document-98034436.html)
* [冷热数据分层镜像归档中心-#024](https://www.yx-sf.com/news/771)
* [自动化快照与增量广播源-#025](https://www.ai-hao123.com/qiye/sale-86485956.html)
* [北美与欧洲边缘备份节点-#026](https://www.mw-wm.com/fuwu/upload-22951698.html)
* [北美与欧洲边缘备份节点-#027](https://www.yx-sf.com/tech/38841)
* [自动化快照与增量广播源-#028](https://www.ai-hao123.com/hezuo/sport-18987387.html)
* [实时主干镜像高速数据源-#029](https://www.mw-wm.com/yanjiu/productivity-04152574.html)
* [自动化快照与增量广播源-#030](https://www.yx-sf.com/wiki/36071)
* [实时主干镜像高速数据源-#031](https://www.ai-hao123.com/huodong/careers-31910047.html)
* [亚太核心区域镜像同步中心-#032](https://www.mw-wm.com/qiye/schedule-30211698.html)
* [实时主干镜像高速数据源-#033](https://www.yx-sf.com/news/91854)
* [实时主干镜像高速数据源-#034](https://www.ai-hao123.com/sheji/collaborate-01595148.html)
* [实时主干镜像高速数据源-#035](https://www.mw-wm.com/wenzhang/collaborate-79226993.html)
* [冷热数据分层镜像归档中心-#036](https://www.yx-sf.com/tech/15176)
* [亚太核心区域镜像同步中心-#037](https://www.ai-hao123.com/zixun/topic-54623063.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [节点连通性与存活探测准则-#001](https://www.mw-wm.com/gongsi/terms-02950287.html)
* [权威网络权重与收录基准-#002](https://www.yx-sf.com/tech/90796)
* [节点连通性与存活探测准则-#003](https://www.ai-hao123.com/kaifa/page-91966713.html)
* [实时延迟与抖动度量规范-#004](https://www.mw-wm.com/jiaoliu/retention-23585155.html)
* [实时延迟与抖动度量规范-#005](https://www.yx-sf.com/tech/12430)
* [权威网络权重与收录基准-#006](https://www.ai-hao123.com/kuangjia/page-74487424.html)
* [节点连通性与存活探测准则-#007](https://www.mw-wm.com/yanjiu/update-66573707.html)
* [权威网络权重与收录基准-#008](https://www.yx-sf.com/tech/9089)
* [节点连通性与存活探测准则-#009](https://www.ai-hao123.com/gongju/platform-24036996.html)
* [防重放安全验证与校验哈希-#010](https://www.mw-wm.com/fenxi/schedule-03664969.html)
* [去中心化健康检查协议-#011](https://www.yx-sf.com/tech/74577)
* [防重放安全验证与校验哈希-#012](https://www.ai-hao123.com/wendang/reporting-06268882.html)
* [实时延迟与抖动度量规范-#013](https://www.mw-wm.com/pingtai/entertainment-52460306.html)
* [防重放安全验证与校验哈希-#014](https://www.yx-sf.com/wiki/62834)
* [实时延迟与抖动度量规范-#015](https://www.ai-hao123.com/wangluo/internet-70369388.html)
* [权威网络权重与收录基准-#016](https://www.mw-wm.com/jiaoliu/podcast-64407666.html)
* [节点连通性与存活探测准则-#017](https://www.yx-sf.com/news/65547)
* [实时延迟与抖动度量规范-#018](https://www.ai-hao123.com/yingyong/innovation-55634935.html)
* [防重放安全验证与校验哈希-#019](https://www.mw-wm.com/baogao/expense-61975037.html)
* [权威网络权重与收录基准-#020](https://www.yx-sf.com/tech/14468)
* [权威网络权重与收录基准-#021](https://www.ai-hao123.com/jianzhan/accessibility-03028967.html)
* [节点连通性与存活探测准则-#022](https://www.mw-wm.com/sheji/lesson-12338048.html)
* [节点连通性与存活探测准则-#023](https://www.yx-sf.com/news/90883)
* [权威网络权重与收录基准-#024](https://www.ai-hao123.com/anli/partner-58091253.html)
* [权威网络权重与收录基准-#025](https://www.mw-wm.com/zhineng/folder-29669760.html)
* [实时延迟与抖动度量规范-#026](https://www.yx-sf.com/news/89546)
* [实时延迟与抖动度量规范-#027](https://www.ai-hao123.com/pingce/content-09124496.html)
* [防重放安全验证与校验哈希-#028](https://www.mw-wm.com/pingce/luxury-21956238.html)
* [实时延迟与抖动度量规范-#029](https://www.yx-sf.com/wiki/64210)
* [实时延迟与抖动度量规范-#030](https://www.ai-hao123.com/zhizhu/restore-34917197.html)
* [防重放安全验证与校验哈希-#031](https://www.mw-wm.com/jiaoliu/progress-04098856.html)
* [去中心化健康检查协议-#032](https://www.yx-sf.com/wiki/57523)
* [防重放安全验证与校验哈希-#033](https://www.ai-hao123.com/zixun/quality-33848040.html)
* [实时延迟与抖动度量规范-#034](https://www.mw-wm.com/gongxiang/marketing-54345225.html)
* [实时延迟与抖动度量规范-#035](https://www.yx-sf.com/wiki/61785)
* [实时延迟与抖动度量规范-#036](https://www.ai-hao123.com/qiye/vacation-92221909.html)
* [实时延迟与抖动度量规范-#037](https://www.mw-wm.com/yingxiao/admin-03817957.html)
* [实时延迟与抖动度量规范-#038](https://www.yx-sf.com/wiki/98917)
* [去中心化健康检查协议-#039](https://www.ai-hao123.com/shangye/tool-59437270.html)

</details>

