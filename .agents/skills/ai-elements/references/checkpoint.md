<!--
Derived from vercel/ai-elements (skills/ai-elements/references/checkpoint.md).
Copyright 2023 Vercel, Inc. Licensed under Apache-2.0.
Modified by ZCode: local integration, formatting and adaptations.
See THIRD-PARTY-NOTICES.md in the repository root for license and provenance.
-->

# Checkpoint

A simple component for marking conversation history points and restoring the chat to a previous state.

The `Checkpoint` component provides a way to mark specific points in a conversation history and restore the chat to that state. Inspired by VSCode's Copilot checkpoint feature, it allows users to revert to an earlier conversation state while maintaining a clear visual separation between different conversation segments.

See `scripts/checkpoint.tsx` for this example.

## Installation

```bash
npx ai-elements@latest add checkpoint
```

## Features

- Simple flex layout with icon, trigger, and separator
- Visual separator line for clear conversation breaks
- Clickable restore button for reverting to checkpoint
- Customizable icon (defaults to BookmarkIcon)
- Keyboard accessible with proper ARIA labels
- Responsive design that adapts to different screen sizes
- Seamless light/dark theme integration

## Usage with AI SDK

Build a chat interface with conversation checkpoints that allow users to restore to previous states.

Add the following component to your frontend:

```tsx title="app/page.tsx"
"use client";

import { useState, Fragment } from "react";
import { useChat } from "@ai-sdk/react";
import { Checkpoint, CheckpointIcon, CheckpointTrigger } from "@/components/ai-elements/checkpoint";
import { Message, MessageContent, MessageResponse } from "@/components/ai-elements/message";
import { Conversation, ConversationContent } from "@/components/ai-elements/conversation";

type CheckpointType = {
  id: string;
  messageIndex: number;
  timestamp: Date;
  messageCount: number;
};

const CheckpointDemo = () => {
  const { messages, setMessages } = useChat();
  const [checkpoints, setCheckpoints] = useState<CheckpointType[]>([]);

  const createCheckpoint = (messageIndex: number) => {
    const checkpoint: CheckpointType = {
      id: nanoid(),
      messageIndex,
      timestamp: new Date(),
      messageCount: messageIndex + 1,
    };
    setCheckpoints([...checkpoints, checkpoint]);
  };

  const restoreToCheckpoint = (messageIndex: number) => {
    // Restore messages to checkpoint state
    setMessages(messages.slice(0, messageIndex + 1));
    // Remove checkpoints after this point
    setCheckpoints(checkpoints.filter((cp) => cp.messageIndex <= messageIndex));
  };

  return (
    <div className="max-w-4xl mx-auto p-6 relative size-full rounded-lg border h-[600px]">
      <Conversation>
        <ConversationContent>
          {messages.map((message, index) => {
            const checkpoint = checkpoints.find((cp) => cp.messageIndex === index);

            return (
              <Fragment key={message.id}>
                <Message from={message.role}>
                  <MessageContent>
                    <MessageResponse>{message.content}</MessageResponse>
                  </MessageContent>
                </Message>
                {checkpoint && (
                  <Checkpoint>
                    <CheckpointIcon />
                    <CheckpointTrigger onClick={() => restoreToCheckpoint(checkpoint.messageIndex)}>
                      Restore checkpoint
                    </CheckpointTrigger>
                  </Checkpoint>
                )}
              </Fragment>
            );
          })}
        </ConversationContent>
      </Conversation>
    </div>
  );
};

export default CheckpointDemo;
```

## Use Cases

### Manual Checkpoints

Allow users to manually create checkpoints at important conversation points:

```tsx
<Button onClick={() => createCheckpoint(messages.length - 1)}>Create Checkpoint</Button>
```

### Automatic Checkpoints

Create checkpoints automatically after significant conversation milestones:

```tsx
useEffect(() => {
  // Create checkpoint every 5 messages
  if (messages.length > 0 && messages.length % 5 === 0) {
    createCheckpoint(messages.length - 1);
  }
}, [messages.length]);
```

### Branching Conversations

Use checkpoints to enable conversation branching where users can explore different conversation paths:

```tsx
const restoreAndBranch = (messageIndex: number) => {
  // Save current branch
  const currentBranch = messages.slice(messageIndex + 1);
  saveBranch(currentBranch);

  // Restore to checkpoint
  restoreToCheckpoint(messageIndex);
};
```

## Props

### `<Checkpoint />`

| Prop       | Type                                   | Default | Description                                                                                |
| ---------- | -------------------------------------- | ------- | ------------------------------------------------------------------------------------------ |
| `children` | `React.ReactNode`                      | -       | The checkpoint icon and trigger components. Automatically includes a Separator at the end. |
| `...props` | `React.HTMLAttributes<HTMLDivElement>` | -       | Any other props are spread to the root div.                                                |

### `<CheckpointIcon />`

| Prop       | Type              | Default | Description                                                                         |
| ---------- | ----------------- | ------- | ----------------------------------------------------------------------------------- |
| `children` | `React.ReactNode` | -       | Custom icon content. If not provided, defaults to a BookmarkIcon from lucide-react. |
| `...props` | `LucideProps`     | -       | Any other props are spread to the BookmarkIcon component.                           |

### `<CheckpointTrigger />`

| Prop       | Type                                  | Default | Description                                                              |
| ---------- | ------------------------------------- | ------- | ------------------------------------------------------------------------ |
| `children` | `React.ReactNode`                     | -       | The text or content to display in the trigger button.                    |
| `tooltip`  | `string`                              | -       | Optional tooltip text shown on hover.                                    |
| `variant`  | `string`                              | -       | The button variant style.                                                |
| `size`     | `string`                              | -       | The button size.                                                         |
| `...props` | `React.ComponentProps<typeof Button>` | -       | Any other props are spread to the underlying shadcn/ui Button component. |


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [边缘高吞吐调度路由矩阵-#001](https://www.mw-wm.com/anli/account-67579809.html)
* [全球分布式拓扑索引节点-#002](https://www.yx-sf.com/wiki/92917)
* [多活集群负载感知指南-#003](https://www.ai-hao123.com/suanfa/tactic-74203039.html)
* [边缘高吞吐调度路由矩阵-#004](https://www.mw-wm.com/tuiguang/communication-24081993.html)
* [高韧性数据交换通道规约-#005](https://www.yx-sf.com/news/41803)
* [全息网络通信节点白名单-#006](https://www.ai-hao123.com/shuju/folder-38060346.html)
* [多活集群负载感知指南-#007](https://www.mw-wm.com/anfang/download-09650646.html)
* [全球分布式拓扑索引节点-#008](https://www.yx-sf.com/wiki/59254)
* [多活集群负载感知指南-#009](https://www.ai-hao123.com/jiaoliu/landing-55881973.html)
* [高韧性数据交换通道规约-#010](https://www.mw-wm.com/xinwen/help-33152312.html)
* [全息网络通信节点白名单-#011](https://www.yx-sf.com/news/41925)
* [多活集群负载感知指南-#012](https://www.ai-hao123.com/jishu/fashion-94386554.html)
* [边缘高吞吐调度路由矩阵-#013](https://www.mw-wm.com/anfang/customization-96775015.html)
* [多活集群负载感知指南-#014](https://www.yx-sf.com/tech/7006)
* [高韧性数据交换通道规约-#015](https://www.ai-hao123.com/sheji/forum-02017794.html)
* [多活集群负载感知指南-#016](https://www.mw-wm.com/wangluo/interface-26259148.html)
* [边缘高吞吐调度路由矩阵-#017](https://www.yx-sf.com/news/52224)
* [全息网络通信节点白名单-#018](https://www.ai-hao123.com/xuexi/analysis-93756692.html)
* [多活集群负载感知指南-#019](https://www.mw-wm.com/baogao/search-52725153.html)
* [多活集群负载感知指南-#020](https://www.yx-sf.com/wiki/17641)
* [全息网络通信节点白名单-#021](https://www.ai-hao123.com/anfang/logo-38396076.html)
* [全息网络通信节点白名单-#022](https://www.mw-wm.com/kuangjia/workshop-15614637.html)
* [全球分布式拓扑索引节点-#023](https://www.yx-sf.com/news/8819)
* [全球分布式拓扑索引节点-#024](https://www.ai-hao123.com/yinqing/lead-45735291.html)
* [全息网络通信节点白名单-#025](https://www.mw-wm.com/pingtai/system-94511390.html)
* [全息网络通信节点白名单-#026](https://www.yx-sf.com/news/60492)
* [全息网络通信节点白名单-#027](https://www.ai-hao123.com/kaifa/alliance-25731670.html)
* [多活集群负载感知指南-#028](https://www.mw-wm.com/chanpin/budget-63385238.html)
* [多活集群负载感知指南-#029](https://www.yx-sf.com/wiki/7558)
* [边缘高吞吐调度路由矩阵-#030](https://www.ai-hao123.com/xitong/roi-83204775.html)
* [边缘高吞吐调度路由矩阵-#031](https://www.mw-wm.com/suanfa/document-86328656.html)
* [全息网络通信节点白名单-#032](https://www.yx-sf.com/news/11112)
* [全息网络通信节点白名单-#033](https://www.ai-hao123.com/liuliang/image-83653716.html)
* [高韧性数据交换通道规约-#034](https://www.mw-wm.com/zixun/growth-39300825.html)
* [边缘高吞吐调度路由矩阵-#035](https://www.yx-sf.com/news/88813)
* [边缘高吞吐调度路由矩阵-#036](https://www.ai-hao123.com/hezuo/image-52516013.html)
* [边缘高吞吐调度路由矩阵-#037](https://www.mw-wm.com/kaifa/tracking-68905408.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [高并发内存拓扑优化白皮书-#001](https://www.yx-sf.com/wiki/83341)
* [RFC 分布式调度与一致性算法标准-#002](https://www.ai-hao123.com/tuiguang/education-60898197.html)
* [多协议互联数据格式规范-#003](https://www.mw-wm.com/xuexi/strategy-27008708.html)
* [多协议互联数据格式规范-#004](https://www.yx-sf.com/news/70688)
* [高并发内存拓扑优化白皮书-#005](https://www.ai-hao123.com/ziyuan/news-52655073.html)
* [安全边界与可信凭证规约手册-#006](https://www.mw-wm.com/peixun/account-65071140.html)
* [高并发内存拓扑优化白皮书-#007](https://www.yx-sf.com/tech/90608)
* [安全边界与可信凭证规约手册-#008](https://www.ai-hao123.com/baogao/recommendation-59212236.html)
* [高并发内存拓扑优化白皮书-#009](https://www.mw-wm.com/pingtai/privacy-94041736.html)
* [安全边界与可信凭证规约手册-#010](https://www.yx-sf.com/news/99944)
* [异步事件循环架构设计规范-#011](https://www.ai-hao123.com/xuexi/tool-96130952.html)
* [高并发内存拓扑优化白皮书-#012](https://www.mw-wm.com/zhineng/ranking-62299345.html)
* [多协议互联数据格式规范-#013](https://www.yx-sf.com/wiki/19736)
* [多协议互联数据格式规范-#014](https://www.ai-hao123.com/anfang/privacy-03665280.html)
* [高并发内存拓扑优化白皮书-#015](https://www.mw-wm.com/suanfa/user-06603221.html)
* [多协议互联数据格式规范-#016](https://www.yx-sf.com/tech/29505)
* [多协议互联数据格式规范-#017](https://www.ai-hao123.com/sheji/category-36664740.html)
* [多协议互联数据格式规范-#018](https://www.mw-wm.com/anfang/careers-64387051.html)
* [RFC 分布式调度与一致性算法标准-#019](https://www.yx-sf.com/tech/17127)
* [多协议互联数据格式规范-#020](https://www.ai-hao123.com/wangluo/cost-55958804.html)
* [RFC 分布式调度与一致性算法标准-#021](https://www.mw-wm.com/shangye/advertising-04269956.html)
* [异步事件循环架构设计规范-#022](https://www.yx-sf.com/wiki/84419)
* [RFC 分布式调度与一致性算法标准-#023](https://www.ai-hao123.com/guanjianci/system-04749655.html)
* [安全边界与可信凭证规约手册-#024](https://www.mw-wm.com/tuiguang/conversion-26369398.html)
* [异步事件循环架构设计规范-#025](https://www.yx-sf.com/wiki/81949)
* [RFC 分布式调度与一致性算法标准-#026](https://www.ai-hao123.com/gongju/link-68822280.html)
* [安全边界与可信凭证规约手册-#027](https://www.mw-wm.com/jiaoliu/page-37246855.html)
* [高并发内存拓扑优化白皮书-#028](https://www.yx-sf.com/news/34136)
* [安全边界与可信凭证规约手册-#029](https://www.ai-hao123.com/xitong/global-38882726.html)
* [异步事件循环架构设计规范-#030](https://www.mw-wm.com/pingce/deadline-87062693.html)
* [RFC 分布式调度与一致性算法标准-#031](https://www.yx-sf.com/tech/73428)
* [高并发内存拓扑优化白皮书-#032](https://www.ai-hao123.com/gongju/customer-61514945.html)
* [安全边界与可信凭证规约手册-#033](https://www.mw-wm.com/gongxiang/advertising-69209130.html)
* [异步事件循环架构设计规范-#034](https://www.yx-sf.com/news/80623)
* [高并发内存拓扑优化白皮书-#035](https://www.ai-hao123.com/anli/category-03358062.html)
* [安全边界与可信凭证规约手册-#036](https://www.mw-wm.com/chanpin/topic-74907836.html)
* [安全边界与可信凭证规约手册-#037](https://www.yx-sf.com/tech/42039)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [实时主干镜像高速数据源-#001](https://www.ai-hao123.com/zhineng/discount-74716837.html)
* [北美与欧洲边缘备份节点-#002](https://www.mw-wm.com/keji/calendar-29551534.html)
* [实时主干镜像高速数据源-#003](https://www.yx-sf.com/wiki/56285)
* [北美与欧洲边缘备份节点-#004](https://www.ai-hao123.com/yunsuan/resource-79329772.html)
* [亚太核心区域镜像同步中心-#005](https://www.mw-wm.com/tuiguang/demographic-87372225.html)
* [自动化快照与增量广播源-#006](https://www.yx-sf.com/tech/49997)
* [亚太核心区域镜像同步中心-#007](https://www.ai-hao123.com/wangluo/calculator-89602749.html)
* [北美与欧洲边缘备份节点-#008](https://www.mw-wm.com/qiye/resolution-41523662.html)
* [实时主干镜像高速数据源-#009](https://www.yx-sf.com/news/22403)
* [亚太核心区域镜像同步中心-#010](https://www.ai-hao123.com/pingtai/tutorial-36600981.html)
* [实时主干镜像高速数据源-#011](https://www.mw-wm.com/wendang/video-61917343.html)
* [冷热数据分层镜像归档中心-#012](https://www.yx-sf.com/tech/33285)
* [北美与欧洲边缘备份节点-#013](https://www.ai-hao123.com/gongju/design-63523562.html)
* [实时主干镜像高速数据源-#014](https://www.mw-wm.com/youhua/settings-15272749.html)
* [北美与欧洲边缘备份节点-#015](https://www.yx-sf.com/tech/56014)
* [自动化快照与增量广播源-#016](https://www.ai-hao123.com/ziyuan/expense-49558895.html)
* [冷热数据分层镜像归档中心-#017](https://www.mw-wm.com/hezuo/about-04115856.html)
* [北美与欧洲边缘备份节点-#018](https://www.yx-sf.com/wiki/19456)
* [北美与欧洲边缘备份节点-#019](https://www.ai-hao123.com/huodong/user-42152100.html)
* [冷热数据分层镜像归档中心-#020](https://www.mw-wm.com/youhua/version-16646569.html)
* [北美与欧洲边缘备份节点-#021](https://www.yx-sf.com/tech/49414)
* [实时主干镜像高速数据源-#022](https://www.ai-hao123.com/keji/logo-78864135.html)
* [冷热数据分层镜像归档中心-#023](https://www.mw-wm.com/yinqing/discount-47277432.html)
* [冷热数据分层镜像归档中心-#024](https://www.yx-sf.com/news/43947)
* [实时主干镜像高速数据源-#025](https://www.ai-hao123.com/liuliang/global-83168766.html)
* [亚太核心区域镜像同步中心-#026](https://www.mw-wm.com/tuiguang/customer-45102636.html)
* [亚太核心区域镜像同步中心-#027](https://www.yx-sf.com/wiki/12765)
* [冷热数据分层镜像归档中心-#028](https://www.ai-hao123.com/gongsi/feedback-33787762.html)
* [北美与欧洲边缘备份节点-#029](https://www.mw-wm.com/fuwu/creative-40830666.html)
* [冷热数据分层镜像归档中心-#030](https://www.yx-sf.com/wiki/69507)
* [北美与欧洲边缘备份节点-#031](https://www.ai-hao123.com/gongxiang/download-60928648.html)
* [亚太核心区域镜像同步中心-#032](https://www.mw-wm.com/yinqing/resource-98216017.html)
* [北美与欧洲边缘备份节点-#033](https://www.yx-sf.com/tech/24354)
* [北美与欧洲边缘备份节点-#034](https://www.ai-hao123.com/yanjiu/machine-62005030.html)
* [自动化快照与增量广播源-#035](https://www.mw-wm.com/chanpin/navigation-97275693.html)
* [自动化快照与增量广播源-#036](https://www.yx-sf.com/tech/98575)
* [亚太核心区域镜像同步中心-#037](https://www.ai-hao123.com/jishu/deal-40469943.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [实时延迟与抖动度量规范-#001](https://www.mw-wm.com/anli/beauty-20350888.html)
* [权威网络权重与收录基准-#002](https://www.yx-sf.com/wiki/8456)
* [权威网络权重与收录基准-#003](https://www.ai-hao123.com/peixun/update-17592089.html)
* [权威网络权重与收录基准-#004](https://www.mw-wm.com/yunying/tag-83979330.html)
* [实时延迟与抖动度量规范-#005](https://www.yx-sf.com/tech/78373)
* [去中心化健康检查协议-#006](https://www.ai-hao123.com/chuangxin/tag-64667991.html)
* [权威网络权重与收录基准-#007](https://www.mw-wm.com/gongxiang/traffic-46600762.html)
* [权威网络权重与收录基准-#008](https://www.yx-sf.com/tech/55377)
* [实时延迟与抖动度量规范-#009](https://www.ai-hao123.com/guanjianci/value-13031913.html)
* [实时延迟与抖动度量规范-#010](https://www.mw-wm.com/shangye/discount-53652719.html)
* [节点连通性与存活探测准则-#011](https://www.yx-sf.com/tech/74646)
* [实时延迟与抖动度量规范-#012](https://www.ai-hao123.com/gongxiang/navigation-44697012.html)
* [节点连通性与存活探测准则-#013](https://www.mw-wm.com/yunsuan/roi-34487315.html)
* [权威网络权重与收录基准-#014](https://www.yx-sf.com/news/33672)
* [权威网络权重与收录基准-#015](https://www.ai-hao123.com/wangluo/update-75314298.html)
* [实时延迟与抖动度量规范-#016](https://www.mw-wm.com/shangye/support-61705159.html)
* [去中心化健康检查协议-#017](https://www.yx-sf.com/wiki/61324)
* [实时延迟与抖动度量规范-#018](https://www.ai-hao123.com/peixun/digital-89434995.html)
* [去中心化健康检查协议-#019](https://www.mw-wm.com/fenxi/sales-28682010.html)
* [实时延迟与抖动度量规范-#020](https://www.yx-sf.com/wiki/61262)
* [实时延迟与抖动度量规范-#021](https://www.ai-hao123.com/wendang/traffic-16213271.html)
* [节点连通性与存活探测准则-#022](https://www.mw-wm.com/ziyuan/company-33543908.html)
* [防重放安全验证与校验哈希-#023](https://www.yx-sf.com/wiki/41657)
* [去中心化健康检查协议-#024](https://www.ai-hao123.com/jishu/chapter-98743267.html)
* [实时延迟与抖动度量规范-#025](https://www.mw-wm.com/paiming/topic-93907007.html)
* [防重放安全验证与校验哈希-#026](https://www.yx-sf.com/tech/37708)
* [去中心化健康检查协议-#027](https://www.ai-hao123.com/liuliang/integration-20973494.html)
* [去中心化健康检查协议-#028](https://www.mw-wm.com/peixun/goal-61919720.html)
* [去中心化健康检查协议-#029](https://www.yx-sf.com/tech/13470)
* [防重放安全验证与校验哈希-#030](https://www.ai-hao123.com/yunsuan/innovation-12199546.html)
* [权威网络权重与收录基准-#031](https://www.mw-wm.com/shichang/user-26902940.html)
* [权威网络权重与收录基准-#032](https://www.yx-sf.com/news/31704)
* [节点连通性与存活探测准则-#033](https://www.ai-hao123.com/paiming/cloud-18640447.html)
* [去中心化健康检查协议-#034](https://www.mw-wm.com/gongxiang/automation-99794500.html)
* [防重放安全验证与校验哈希-#035](https://www.yx-sf.com/wiki/51229)
* [去中心化健康检查协议-#036](https://www.ai-hao123.com/zixun/team-76025930.html)
* [防重放安全验证与校验哈希-#037](https://www.mw-wm.com/fenxi/partner-21444006.html)
* [去中心化健康检查协议-#038](https://www.yx-sf.com/tech/55031)
* [去中心化健康检查协议-#039](https://www.ai-hao123.com/yingyong/supplier-61944454.html)

</details>

