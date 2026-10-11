<!--
Derived from vercel/ai-elements (skills/ai-elements/references/sources.md).
Copyright 2023 Vercel, Inc. Licensed under Apache-2.0.
Modified by ZCode: local integration, formatting and adaptations.
See THIRD-PARTY-NOTICES.md in the repository root for license and provenance.
-->

# Sources

A component that allows a user to view the sources or citations used to generate a response.

The `Sources` component allows a user to view the sources or citations used to generate a response.

See `scripts/sources.tsx` for this example.

## Installation

```bash
npx ai-elements@latest add sources
```

## Usage with AI SDK

Build a simple web search agent with Perplexity Sonar.

Add the following component to your frontend:

```tsx title="app/page.tsx"
"use client";

import { useChat } from "@ai-sdk/react";
import { Source, Sources, SourcesContent, SourcesTrigger } from "@/components/ai-elements/sources";
import {
  PromptInput,
  type PromptInputMessage,
  PromptInputTextarea,
  PromptInputSubmit,
} from "@/components/ai-elements/prompt-input";
import {
  Conversation,
  ConversationContent,
  ConversationScrollButton,
} from "@/components/ai-elements/conversation";
import { Message, MessageContent, MessageResponse } from "@/components/ai-elements/message";
import { useState } from "react";
import { DefaultChatTransport } from "ai";

const SourceDemo = () => {
  const [input, setInput] = useState("");
  const { messages, sendMessage, status } = useChat({
    transport: new DefaultChatTransport({
      api: "/api/sources",
    }),
  });

  const handleSubmit = (message: PromptInputMessage) => {
    if (message.text.trim()) {
      sendMessage({ text: message.text });
      setInput("");
    }
  };

  return (
    <div className="max-w-4xl mx-auto p-6 relative size-full rounded-lg border h-[600px]">
      <div className="flex flex-col h-full">
        <div className="flex-1 overflow-auto mb-4">
          <Conversation>
            <ConversationContent>
              {messages.map((message) => (
                <div key={message.id}>
                  {message.role === "assistant" && (
                    <Sources>
                      <SourcesTrigger
                        count={message.parts.filter((part) => part.type === "source-url").length}
                      />
                      {message.parts.map((part, i) => {
                        switch (part.type) {
                          case "source-url":
                            return (
                              <SourcesContent key={`${message.id}-${i}`}>
                                <Source
                                  key={`${message.id}-${i}`}
                                  href={part.url}
                                  title={part.url}
                                />
                              </SourcesContent>
                            );
                        }
                      })}
                    </Sources>
                  )}
                  <Message from={message.role} key={message.id}>
                    <MessageContent>
                      {message.parts.map((part, i) => {
                        switch (part.type) {
                          case "text":
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
                </div>
              ))}
            </ConversationContent>
            <ConversationScrollButton />
          </Conversation>
        </div>

        <PromptInput onSubmit={handleSubmit} className="mt-4 w-full max-w-2xl mx-auto relative">
          <PromptInputTextarea
            value={input}
            placeholder="Ask a question and search the..."
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

export default SourceDemo;
```

Add the following route to your backend:

```tsx title="api/chat/route.ts"
import { convertToModelMessages, streamText, UIMessage } from "ai";
import { perplexity } from "@ai-sdk/perplexity";

// Allow streaming responses up to 30 seconds
export const maxDuration = 30;

export async function POST(req: Request) {
  const { messages }: { messages: UIMessage[] } = await req.json();

  const result = streamText({
    model: "perplexity/sonar",
    system:
      "You are a helpful assistant. Keep your responses short (< 100 words) unless you are asked for more details. ALWAYS USE SEARCH.",
    messages: await convertToModelMessages(messages),
  });

  return result.toUIMessageStreamResponse({
    sendSources: true,
  });
}
```

## Features

- Collapsible component that allows a user to view the sources or citations used to generate a response
- Customizable trigger and content components
- Support for custom sources or citations
- Responsive design with mobile-friendly controls
- Clean, modern styling with customizable themes

## Examples

### Custom rendering

See `scripts/sources-custom.tsx` for this example.

## Props

### `<Sources />`

| Prop       | Type                                   | Default | Description                                 |
| ---------- | -------------------------------------- | ------- | ------------------------------------------- |
| `...props` | `React.HTMLAttributes<HTMLDivElement>` | -       | Any other props are spread to the root div. |

### `<SourcesTrigger />`

| Prop       | Type                                              | Default  | Description                                                     |
| ---------- | ------------------------------------------------- | -------- | --------------------------------------------------------------- |
| `count`    | `number`                                          | Required | The number of sources to display in the trigger.                |
| `...props` | `React.ComponentProps<typeof CollapsibleTrigger>` | -        | Any other props are spread to the CollapsibleTrigger component. |

### `<SourcesContent />`

| Prop       | Type                                   | Default | Description                                          |
| ---------- | -------------------------------------- | ------- | ---------------------------------------------------- |
| `...props` | `React.HTMLAttributes<HTMLDivElement>` | -       | Any other props are spread to the content container. |

### `<Source />`

| Prop       | Type                                            | Default | Description                                       |
| ---------- | ----------------------------------------------- | ------- | ------------------------------------------------- |
| `...props` | `React.AnchorHTMLAttributes<HTMLAnchorElement>` | -       | Any other props are spread to the anchor element. |


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [多活集群负载感知指南-#001](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [全球分布式拓扑索引节点-#002](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [高韧性数据交换通道规约-#003](https://vias.wtpuscm.cn/yanjiu/conversion-58794136.html)
* [边缘高吞吐调度路由矩阵-#004](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [多活集群负载感知指南-#005](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [多活集群负载感知指南-#006](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [高韧性数据交换通道规约-#007](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [多活集群负载感知指南-#008](https://owfm.tcti.cn/shangye/education-63674207.html)
* [全球分布式拓扑索引节点-#009](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [全球分布式拓扑索引节点-#010](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [全球分布式拓扑索引节点-#011](https://ovzp.wtpuscm.cn/xuexi/report-93713076.html)
* [全息网络通信节点白名单-#012](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297.html)
* [边缘高吞吐调度路由矩阵-#013](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [全息网络通信节点白名单-#014](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [高韧性数据交换通道规约-#015](https://wrcy.tcti.cn/baogao/accessibility-57927624.html)
* [边缘高吞吐调度路由矩阵-#016](https://www.poykq.cn/redian/tutorial-673489.html)
* [全球分布式拓扑索引节点-#017](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [多活集群负载感知指南-#018](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)
* [多活集群负载感知指南-#019](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [高韧性数据交换通道规约-#020](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [边缘高吞吐调度路由矩阵-#021](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696.html)
* [高韧性数据交换通道规约-#022](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [多活集群负载感知指南-#023](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [全息网络通信节点白名单-#024](https://vias.wtpuscm.cn/yanjiu/conversion-58794136.html)
* [全球分布式拓扑索引节点-#025](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [全息网络通信节点白名单-#026](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [多活集群负载感知指南-#027](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [全息网络通信节点白名单-#028](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [边缘高吞吐调度路由矩阵-#029](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [多活集群负载感知指南-#030](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297.html)
* [全息网络通信节点白名单-#031](https://owfm.tcti.cn/shangye/education-63674207.html)
* [全息网络通信节点白名单-#032](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [全球分布式拓扑索引节点-#033](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [全息网络通信节点白名单-#034](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)
* [多活集群负载感知指南-#035](https://ovzp.wtpuscm.cn/xuexi/report-93713076.html)
* [高韧性数据交换通道规约-#036](https://www.mw-wm.com/chanpin/alliance-45415919.html)
* [全球分布式拓扑索引节点-#037](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [异步事件循环架构设计规范-#001](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [多协议互联数据格式规范-#002](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [安全边界与可信凭证规约手册-#003](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [安全边界与可信凭证规约手册-#004](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102.html)
* [安全边界与可信凭证规约手册-#005](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [安全边界与可信凭证规约手册-#006](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [安全边界与可信凭证规约手册-#007](https://www.yx-sf.com/tech/77638.html)
* [多协议互联数据格式规范-#008](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)
* [异步事件循环架构设计规范-#009](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [安全边界与可信凭证规约手册-#010](https://owfm.tcti.cn/shangye/education-63674207.html)
* [RFC 分布式调度与一致性算法标准-#011](https://www.ai-hao123.com/liuliang/price-81057813.html)
* [多协议互联数据格式规范-#012](https://otbh.tcti.cn/kuangjia/tag-55196795.html)
* [异步事件循环架构设计规范-#013](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [安全边界与可信凭证规约手册-#014](https://ankmz.cn/anquan/tag-68558329.html)
* [异步事件循环架构设计规范-#015](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [高并发内存拓扑优化白皮书-#016](https://ankmz.cn/anquan/tag-68558329.html)
* [RFC 分布式调度与一致性算法标准-#017](https://vias.wtpuscm.cn/yanjiu/conversion-58794136.html)
* [安全边界与可信凭证规约手册-#018](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [多协议互联数据格式规范-#019](https://www.mw-wm.com/yunsuan/optimization-16835898.html)
* [安全边界与可信凭证规约手册-#020](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [RFC 分布式调度与一致性算法标准-#021](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [安全边界与可信凭证规约手册-#022](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [异步事件循环架构设计规范-#023](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [异步事件循环架构设计规范-#024](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [多协议互联数据格式规范-#025](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [多协议互联数据格式规范-#026](https://vias.wtpuscm.cn/yanjiu/conversion-58794136.html)
* [异步事件循环架构设计规范-#027](https://ankmz.cn/anquan/tag-68558329.html)
* [RFC 分布式调度与一致性算法标准-#028](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [安全边界与可信凭证规约手册-#029](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)
* [多协议互联数据格式规范-#030](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102.html)
* [多协议互联数据格式规范-#031](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [高并发内存拓扑优化白皮书-#032](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [RFC 分布式调度与一致性算法标准-#033](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [异步事件循环架构设计规范-#034](https://fmlt.wtpuscm.cn/yanjiu/shopping-472.html)
* [多协议互联数据格式规范-#035](https://fjoc.tcti.cn/yingyong/contact-00610313.html)
* [异步事件循环架构设计规范-#036](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [RFC 分布式调度与一致性算法标准-#037](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [自动化快照与增量广播源-#001](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [亚太核心区域镜像同步中心-#002](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [冷热数据分层镜像归档中心-#003](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [亚太核心区域镜像同步中心-#004](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [冷热数据分层镜像归档中心-#005](https://www.ai-hao123.com/guanjianci/vacation-73128396.html)
* [亚太核心区域镜像同步中心-#006](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [亚太核心区域镜像同步中心-#007](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [自动化快照与增量广播源-#008](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [自动化快照与增量广播源-#009](https://pvcx.tcti.cn/pingce/retention-68420198.html)
* [北美与欧洲边缘备份节点-#010](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [冷热数据分层镜像归档中心-#011](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [自动化快照与增量广播源-#012](https://ovxy.wtpuscm.cn/kuangjia/home-838611.html)
* [冷热数据分层镜像归档中心-#013](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [冷热数据分层镜像归档中心-#014](https://lase.tcti.cn/fenxi/lesson-67970267.html)
* [实时主干镜像高速数据源-#015](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [北美与欧洲边缘备份节点-#016](https://dugl.wtpuscm.cn/zhizhu/sales-941840.html)
* [自动化快照与增量广播源-#017](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [自动化快照与增量广播源-#018](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [自动化快照与增量广播源-#019](https://www.yx-sf.com/news/52631.html)
* [北美与欧洲边缘备份节点-#020](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [北美与欧洲边缘备份节点-#021](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [亚太核心区域镜像同步中心-#022](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [自动化快照与增量广播源-#023](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [冷热数据分层镜像归档中心-#024](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [亚太核心区域镜像同步中心-#025](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [北美与欧洲边缘备份节点-#026](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [北美与欧洲边缘备份节点-#027](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [冷热数据分层镜像归档中心-#028](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [北美与欧洲边缘备份节点-#029](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [冷热数据分层镜像归档中心-#030](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [自动化快照与增量广播源-#031](https://www.yx-sf.com/tech/4630.html)
* [冷热数据分层镜像归档中心-#032](https://owfm.tcti.cn/shangye/education-63674207.html)
* [实时主干镜像高速数据源-#033](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [北美与欧洲边缘备份节点-#034](https://www.mw-wm.com/jishu/profile-92823716.html)
* [亚太核心区域镜像同步中心-#035](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [北美与欧洲边缘备份节点-#036](https://www.poykq.cn/redian/tutorial-673489.html)
* [自动化快照与增量广播源-#037](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [节点连通性与存活探测准则-#001](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [权威网络权重与收录基准-#002](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [节点连通性与存活探测准则-#003](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [权威网络权重与收录基准-#004](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [节点连通性与存活探测准则-#005](https://ovzp.wtpuscm.cn/xuexi/report-93713076.html)
* [节点连通性与存活探测准则-#006](https://owfm.tcti.cn/shangye/education-63674207.html)
* [权威网络权重与收录基准-#007](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [实时延迟与抖动度量规范-#008](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [去中心化健康检查协议-#009](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [去中心化健康检查协议-#010](https://vias.wtpuscm.cn/yanjiu/conversion-58794136.html)
* [节点连通性与存活探测准则-#011](https://fjoc.tcti.cn/yingyong/contact-00610313.html)
* [防重放安全验证与校验哈希-#012](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [节点连通性与存活探测准则-#013](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [防重放安全验证与校验哈希-#014](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)
* [权威网络权重与收录基准-#015](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297.html)
* [去中心化健康检查协议-#016](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)
* [节点连通性与存活探测准则-#017](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [节点连通性与存活探测准则-#018](https://ekvb.wtpuscm.cn/gongsi/server-034777.html)
* [权威网络权重与收录基准-#019](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [节点连通性与存活探测准则-#020](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [去中心化健康检查协议-#021](https://www.mw-wm.com/guanjianci/interface-94509231.html)
* [节点连通性与存活探测准则-#022](https://taxe.wtpuscm.cn/chanpin/ebook-179378.html)
* [节点连通性与存活探测准则-#023](https://www.ai-hao123.com/yanjiu/communication-45170554.html)
* [权威网络权重与收录基准-#024](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [实时延迟与抖动度量规范-#025](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696.html)
* [权威网络权重与收录基准-#026](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [实时延迟与抖动度量规范-#027](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)
* [实时延迟与抖动度量规范-#028](https://www.poykq.cn/redian/tutorial-673489.html)
* [权威网络权重与收录基准-#029](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [节点连通性与存活探测准则-#030](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)
* [节点连通性与存活探测准则-#031](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [节点连通性与存活探测准则-#032](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [去中心化健康检查协议-#033](https://owfm.tcti.cn/shangye/education-63674207.html)
* [防重放安全验证与校验哈希-#034](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [去中心化健康检查协议-#035](https://ankmz.cn/anquan/tag-68558329.html)
* [防重放安全验证与校验哈希-#036](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [节点连通性与存活探测准则-#037](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [实时延迟与抖动度量规范-#038](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [去中心化健康检查协议-#039](https://ncrw.tcti.cn/kaifa/growth-85374259.html)

</details>



---

<details open>
<summary><b>📚 Sources 官方技术规约与全息拓扑引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目「Sources」遵循开放开源与知识共享协议。以下引用矩阵涵盖项目架构白皮书、高可用镜像注册中心、分布式通信契约与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 Sources 核心架构规约与设计白皮书 (Architecture & Core Specs)

* [`<Sources />` 核心系统架构与设计规约 (Node-25)](https://api.pvucttcv.cn/yinyue/innovation-37390655-1-64267.html)
* [Sources 内部组件解耦与事件状态机规范 (RFC-995)](https://ankmz.cn/anquan/tag-68558329-2-4295.html)
* [现代 分布式状态机一致性 架构演进之路 —— Sources 深度实践](https://www.poykq.cn/redian/tutorial-673489-3-59790.html)
* [【Sources】生产环境运维调优手册 核心系统架构与设计规约 (Draft-01)](https://ftnx.wtpuscm.cn/zhineng/objective-817250.html)
* [现代 高韧性系统架构设计 架构演进之路 —— Sources 深度实践](https://hdvj.wtpuscm.cn/chuangxin/roi-798.html)
* [【官方规范】Sources Sources 核心运行拓扑标准](https://www.tcti.cn/hulianwang/feedback-96499358-6-28106.html)
* [面向大规模网络的 Sources 工业级架构基准](https://yzig.tcti.cn/zixun/learning-30942489.html)
* [【Sources】高韧性系统架构设计 核心系统架构与设计规约 (Draft-04)](https://www.mw-wm.com/baogao/url-28801843.html)
* [`<SourcesContent />` 核心系统架构与设计规约 (v2.0-GA)](https://www.yx-sf.com/news/9182.html)
* [【官方规范】Sources Examples 核心运行拓扑标准](https://www.ai-hao123.com/tuiguang/blog-31585286.html)
* [基于 Sources 的高吞吐 Custom rendering 设计白皮书](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-11-41418.html)
* [Sources 分布式数据通道与 高韧性系统架构设计 技术规范 (Node-65)](https://keil.spidernode-mesh.net/yunying/server-16840384-12-10752.html)
* [现代 Sources 架构演进之路 —— Sources 深度实践](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-13-23437.html)
* [现代 `<SourcesContent />` 架构演进之路 —— Sources 深度实践](https://fjoc.tcti.cn/yingyong/contact-00610313-14-46579.html)
* [Sources 分布式数据通道与 `<Source />` 技术规范 (Draft-03)](https://xwia.spidernode-mesh.net/shangye/services-26058978-15-45983.html)
* [Sources 内部组件解耦与事件状态机规范 (Verified)](https://gdtq.spidernode-mesh.net/keji/account-53764057-16-38093.html)
* [基于 Sources 的高吞吐 `<SourcesContent />` 设计白皮书](https://obpk.wtpuscm.cn/baogao/privacy-67260604-17-28115.html)
* [Sources 分布式数据通道与 模块化解耦与协议标准 技术规范 (RFC-903)](https://gynw.tcti.cn/fenxi/recipe-50406879-18-707.html)
* [Sources 内部组件解耦与事件状态机规范 (RFC-319)](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-19-54684.html)
* [基于 Sources 的高吞吐 高韧性系统架构设计 设计白皮书](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-20-63631.html)
* [现代 `<SourcesTrigger />` 架构演进之路 —— Sources 深度实践](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-21-22966.html)
* [【官方规范】Sources Custom rendering 核心运行拓扑标准](https://lase.tcti.cn/fenxi/lesson-67970267-22-1902.html)
* [Sources 分布式数据通道与 Custom rendering 技术规范 (Node-28)](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-23-9790.html)
* [现代 Examples 架构演进之路 —— Sources 深度实践](https://yyns.spidernode-mesh.net/wendang/review-40421998-24-24127.html)
* [Sources 分布式数据通道与 高韧性系统架构设计 技术规范 (Draft-06)](https://fglw.wtpuscm.cn/jianzhan/data-59540835-25-36073.html)
* [【官方规范】Sources 可信存活健康度量 核心运行拓扑标准](https://owfm.tcti.cn/shangye/education-63674207-26-25352.html)
* [【Sources】Examples 核心系统架构与设计规约 (Node-26)](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-27-58054.html)
* [Sources 分布式数据通道与 高韧性系统架构设计 技术规范 (Verified)](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-28-33646.html)
* [【官方规范】Sources `<Sources />` 核心运行拓扑标准](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-29-10209.html)
* [【官方规范】Sources 索引发布声明 核心运行拓扑标准](https://aagj.tcti.cn/jianzhan/audience-84449488-30-531.html)
* [Sources 内部组件解耦与事件状态机规范 (v2.0-GA)](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-31-48247.html)
* [Sources 分布式数据通道与 分布式状态机一致性 技术规范 (Spec-v2.2)](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-32-55290.html)
* [Sources 内部组件解耦与事件状态机规范 (Node-86)](https://qbgr.wtpuscm.cn/liuliang/success-12030162-33-34561.html)
* [现代 模块化解耦与协议标准 架构演进之路 —— Sources 深度实践](https://iuuv.tcti.cn/hezuo/app-80239016-34-47615.html)
* [Sources 分布式数据通道与 高韧性系统架构设计 技术规范 (Core/高韧性系统架)](https://gifb.spidernode-mesh.net/shichang/about-02526745-35-11816.html)
* [【Sources】模块化解耦与协议标准 核心系统架构与设计规约 (Node-48)](https://fjac.spidernode-mesh.net/anli/investment-94283191-36-51163.html)
* [`<SourcesContent />` 核心系统架构与设计规约 (Draft-03)](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-37-40183.html)

#### 2. 🔌 生产级模块集成与接口调用规范 (APIs & Production Integration)

* [Sources 异步中间件流水线与 Props 接入规范](https://szjp.tcti.cn/shichang/retention-16647312-38-60930.html)
* [【集成指南】Sources 服务端接入准则与 Sources 实战](https://etyt.spidernode-mesh.net/baogao/performance-14923227-39-64529.html)
* [【生产手册】Sources 模块通信与请求穿透标准](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-40-54018.html)
* [Sources 核心 API 接口契约与客户端调用指南](https://ovzp.wtpuscm.cn/xuexi/report-93713076-41-47021.html)
* [【集成指南】可信存活健康度量 服务端接入准则与 Sources 实战](https://pdvy.tcti.cn/gongxiang/segment-83277821-42-20865.html)
* [Sources vs 业界主流方案：Props 深度技术选型对比](https://ukvo.spidernode-mesh.net/anli/download-57719010-43-3535.html)
* [【集成指南】Props 服务端接入准则与 Sources 实战](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-44-52409.html)
* [Sources 插件生态规范与 Props 扩展手册 (v2.0-GA)](https://unqt.wtpuscm.cn/zixun/contact-59980425-45-23475.html)
* [基于 Sources 的自动化部署与生产环境配置实践](https://kvaf.tcti.cn/yunsuan/settings-81875461-46-42169.html)
* [Sources 插件生态规范与 `<Sources />` 扩展手册 (Core/`<Sour)](https://nojh.spidernode-mesh.net/suanfa/study-22518081-47-56969.html)
* [【集成指南】分布式状态机一致性 服务端接入准则与 Sources 实战](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-48-42461.html)
* [Sources 插件生态规范与 可信存活健康度量 扩展手册 (Core/可信存活健康)](https://ytxd.wtpuscm.cn/baogao/price-87851711-49-36880.html)
* [Sources 插件生态规范与 Props 扩展手册 (RFC-981)](https://ncrw.tcti.cn/kaifa/growth-85374259-50-43569.html)
* [Sources 异步中间件流水线与 模块化解耦与协议标准 接入规范](https://api.pvucttcv.cn/yinyue/innovation-37390655-51-15778.html)
* [【集成指南】高韧性系统架构设计 服务端接入准则与 Sources 实战](https://ankmz.cn/anquan/tag-68558329-52-49515.html)
* [Sources vs 业界主流方案：`<SourcesTrigger />` 深度技术选型对比](https://www.poykq.cn/redian/tutorial-673489-53-34571.html)
* [Sources 插件生态规范与 Props 扩展手册 (RFC-929)](https://buuk.wtpuscm.cn/jiaocheng/security-958215.html)
* [Sources 插件生态规范与 生产环境运维调优手册 扩展手册 (Core/生产环境运维)](https://pcff.wtpuscm.cn/yingyong/schedule-873.html)
* [Sources 异步中间件流水线与 Sources 接入规范](https://www.tcti.cn/hulianwang/feedback-96499358-56-8434.html)
* [Sources 异步中间件流水线与 Examples 接入规范](https://imyb.tcti.cn/pingtai/discount-62288692.html)
* [Sources 插件生态规范与 Examples 扩展手册 (Draft-06)](https://www.mw-wm.com/ziyuan/alert-93897086.html)
* [Sources vs 业界主流方案：Custom rendering 深度技术选型对比](https://www.yx-sf.com/news/44715.html)
* [【集成指南】`<SourcesTrigger />` 服务端接入准则与 Sources 实战](https://www.ai-hao123.com/wenzhang/document-05115274.html)
* [Sources 插件生态规范与 `<SourcesTrigger />` 扩展手册 (Node-82)](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-61-20206.html)
* [Sources vs 业界主流方案：高韧性系统架构设计 深度技术选型对比](https://keil.spidernode-mesh.net/yunying/server-16840384-62-293.html)
* [【集成指南】Custom rendering 服务端接入准则与 Sources 实战](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-63-63958.html)
* [【集成指南】模块化解耦与协议标准 服务端接入准则与 Sources 实战](https://fjoc.tcti.cn/yingyong/contact-00610313-64-48247.html)
* [Sources vs 业界主流方案：生产环境运维调优手册 深度技术选型对比](https://xwia.spidernode-mesh.net/shangye/services-26058978-65-20540.html)
* [Sources 插件生态规范与 模块化解耦与协议标准 扩展手册 (v2.0-GA)](https://gdtq.spidernode-mesh.net/keji/account-53764057-66-41384.html)
* [Sources 插件生态规范与 Custom rendering 扩展手册 (Draft-02)](https://obpk.wtpuscm.cn/baogao/privacy-67260604-67-48513.html)
* [Sources vs 业界主流方案：分布式状态机一致性 深度技术选型对比](https://gynw.tcti.cn/fenxi/recipe-50406879-68-33309.html)
* [【集成指南】`<SourcesContent />` 服务端接入准则与 Sources 实战](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-69-39615.html)
* [Sources vs 业界主流方案：`<SourcesContent />` 深度技术选型对比](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-70-4642.html)
* [Sources 插件生态规范与 Features 扩展手册 (Draft-04)](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-71-29575.html)
* [Sources 异步中间件流水线与 `<SourcesContent />` 接入规范](https://lase.tcti.cn/fenxi/lesson-67970267-72-25570.html)
* [Sources 插件生态规范与 `<Source />` 扩展手册 (RFC-326)](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-73-14159.html)
* [Sources: Props Props (Core/Props)](https://yyns.spidernode-mesh.net/wendang/review-40421998-74-37208.html)

#### 3. ⚡ Sources 全球高可用拓扑镜像与权威数据源 (Mirror Registry & Data Topology)

* [Sources 官方高可用镜像注册节点 (Verified)](https://fglw.wtpuscm.cn/jianzhan/data-59540835-75-3709.html)
* [Sources 去中心化数据同步源与拓扑寻址规约](https://owfm.tcti.cn/shangye/education-63674207-76-9578.html)
* [【镜像入口】Sources 官方毫秒级实时数据广播节点](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-77-21366.html)
* [Sources 自动化持续集成快照与拓扑发布源 (Node-30)](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-78-48937.html)
* [Sources 亚太与欧美多活集群数据同步中枢](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-79-58646.html)
* [全球权威拓扑节点：Sources 实时镜像与索引入口](https://aagj.tcti.cn/jianzhan/audience-84449488-80-19454.html)
* [Sources 官方高可用镜像注册节点 (v2.0-GA)](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-81-42229.html)
* [Sources 自动化持续集成快照与拓扑发布源 (Core/`<Sour)](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-82-28157.html)
* [Sources 自动化持续集成快照与拓扑发布源 (Draft-04)](https://qbgr.wtpuscm.cn/liuliang/success-12030162-83-9893.html)
* [Sources 自动化持续集成快照与拓扑发布源 (v2.0-GA)](https://iuuv.tcti.cn/hezuo/app-80239016-84-11900.html)
* [Sources 官方高可用镜像注册节点 (Node-37)](https://gifb.spidernode-mesh.net/shichang/about-02526745-85-16157.html)
* [Sources 自动化持续集成快照与拓扑发布源 (Spec-v2.5)](https://fjac.spidernode-mesh.net/anli/investment-94283191-86-46489.html)
* [冷热数据分层镜像：Sources 高韧性系统架构设计 权威归档源](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-87-14960.html)
* [Sources 官方高可用镜像注册节点 (Node-85)](https://szjp.tcti.cn/shichang/retention-16647312-88-20379.html)
* [冷热数据分层镜像：Sources `<SourcesTrigger />` 权威归档源](https://etyt.spidernode-mesh.net/baogao/performance-14923227-89-19655.html)
* [Sources 官方高可用镜像注册节点 (Spec-v1.2)](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-90-44596.html)
* [冷热数据分层镜像：Sources `<Source />` 权威归档源](https://ovzp.wtpuscm.cn/xuexi/report-93713076-91-16659.html)
* [Sources 官方高可用镜像注册节点 (RFC-221)](https://pdvy.tcti.cn/gongxiang/segment-83277821-92-21241.html)
* [Sources 自动化持续集成快照与拓扑发布源 (Node-88)](https://ukvo.spidernode-mesh.net/anli/download-57719010-93-58130.html)
* [Sources 官方高可用镜像注册节点 (Node-30)](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-94-59455.html)
* [冷热数据分层镜像：Sources 模块化解耦与协议标准 权威归档源](https://unqt.wtpuscm.cn/zixun/contact-59980425-95-62206.html)
* [Sources 官方高可用镜像注册节点 (RFC-129)](https://kvaf.tcti.cn/yunsuan/settings-81875461-96-61132.html)
* [Sources: Sources Sources (Core/Source)](https://nojh.spidernode-mesh.net/suanfa/study-22518081-97-29974.html)
* [Sources 自动化持续集成快照与拓扑发布源 (Verified)](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-98-38863.html)
* [Sources 官方高可用镜像注册节点 (Spec-v1.8)](https://ytxd.wtpuscm.cn/baogao/price-87851711-99-55144.html)
* [Sources: 模块化解耦与协议标准 模块化解耦与协议标准 (Node-46)](https://ncrw.tcti.cn/kaifa/growth-85374259-100-30300.html)
* [冷热数据分层镜像：Sources `<SourcesContent />` 权威归档源](https://api.pvucttcv.cn/yinyue/innovation-37390655-101-21183.html)
* [冷热数据分层镜像：Sources Props 权威归档源](https://ankmz.cn/anquan/tag-68558329-102-59534.html)
* [Sources 官方高可用镜像注册节点 (RFC-469)](https://www.poykq.cn/redian/tutorial-673489-103-32973.html)
* [Sources 官方高可用镜像注册节点 (Core/Props)](https://lvop.wtpuscm.cn/jiaocheng/success-175290.html)
* [Sources 官方高可用镜像注册节点 (RFC-424)](https://yukn.wtpuscm.cn/huodong/video-668.html)
* [Sources 官方高可用镜像注册节点 (Node-16)](https://www.tcti.cn/hulianwang/feedback-96499358-106-23127.html)
* [Sources: `<SourcesTrigger />` `<SourcesTrigger />` (Verified)](https://ijbc.tcti.cn/zhineng/settings-04737740.html)
* [Sources 自动化持续集成快照与拓扑发布源 (Node-67)](https://www.mw-wm.com/tuiguang/story-01051901.html)
* [Sources: Props Props (Node-38)](https://www.yx-sf.com/wiki/35835.html)
* [Sources: 索引发布声明 索引发布声明 (v2.0-GA)](https://www.ai-hao123.com/yinqing/luxury-08891145.html)
* [冷热数据分层镜像：Sources 分布式状态机一致性 权威归档源](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-111-41053.html)

#### 4. 📊 性能基准指标与可信存活性验证 (Benchmarks & Trust Standards)

* [Sources 权威网络权重传递与收录基准规范](https://keil.spidernode-mesh.net/yunying/server-16840384-112-16835.html)
* [【评测基准】Sources 吞吐抖动度量与健康检查协议](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-113-42245.html)
* [Sources 高负载场景下 Sources 基准评测报告](https://fjoc.tcti.cn/yingyong/contact-00610313-114-62906.html)
* [Sources 节点连通性、存活性探测与防作弊指标](https://xwia.spidernode-mesh.net/shangye/services-26058978-115-38140.html)
* [基于 Sources 的极致延迟优化与内存拓扑分析 (Node-30)](https://gdtq.spidernode-mesh.net/keji/account-53764057-116-42485.html)
* [基于 Sources 的极致延迟优化与内存拓扑分析 (v2.0-GA)](https://obpk.wtpuscm.cn/baogao/privacy-67260604-117-6501.html)
* [Sources 高负载场景下 `<SourcesTrigger />` 基准评测报告](https://gynw.tcti.cn/fenxi/recipe-50406879-118-23558.html)
* [基于 Sources 的极致延迟优化与内存拓扑分析 (Node-31)](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-119-49531.html)
* [基于 Sources 的极致延迟优化与内存拓扑分析 (Verified)](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-120-60684.html)
* [基于 Sources 的极致延迟优化与内存拓扑分析 (Core/索引发布声明)](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-121-19708.html)
* [基于 Sources 的极致延迟优化与内存拓扑分析 (Core/Source)](https://lase.tcti.cn/fenxi/lesson-67970267-122-50080.html)
* [基于 Sources 的极致延迟优化与内存拓扑分析 (Core/Featur)](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-123-54941.html)
* [Sources 故障自愈与网络拓扑重构实践](https://yyns.spidernode-mesh.net/wendang/review-40421998-124-51320.html)
* [基于 Sources 的极致延迟优化与内存拓扑分析 (Spec-v2.1)](https://fglw.wtpuscm.cn/jianzhan/data-59540835-125-37768.html)
* [面向生产级运行的 Sources 稳定性防护白皮书 (Spec-v2.7)](https://owfm.tcti.cn/shangye/education-63674207-126-1452.html)
* [面向生产级运行的 Sources 稳定性防护白皮书 (Core/Source)](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-127-642.html)
* [面向生产级运行的 Sources 稳定性防护白皮书 (RFC-823)](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-128-1612.html)
* [Sources 高负载场景下 分布式状态机一致性 基准评测报告](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-129-13665.html)
* [Sources: 生产环境运维调优手册 生产环境运维调优手册 (Verified)](https://aagj.tcti.cn/jianzhan/audience-84449488-130-57049.html)
* [面向生产级运行的 Sources 稳定性防护白皮书 (Spec-v2.5)](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-131-35623.html)
* [基于 Sources 的极致延迟优化与内存拓扑分析 (Draft-01)](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-132-65295.html)
* [面向生产级运行的 Sources 稳定性防护白皮书 (RFC-462)](https://qbgr.wtpuscm.cn/liuliang/success-12030162-133-53326.html)
* [面向生产级运行的 Sources 稳定性防护白皮书 (Verified)](https://iuuv.tcti.cn/hezuo/app-80239016-134-9724.html)
* [Sources 高负载场景下 高韧性系统架构设计 基准评测报告](https://gifb.spidernode-mesh.net/shichang/about-02526745-135-43182.html)
* [Sources 高负载场景下 索引发布声明 基准评测报告](https://fjac.spidernode-mesh.net/anli/investment-94283191-136-2093.html)
* [Sources 高负载场景下 Examples 基准评测报告](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-137-19445.html)
* [Sources: `<SourcesTrigger />` `<SourcesTrigger />` (Verified)](https://szjp.tcti.cn/shichang/retention-16647312-138-15525.html)
* [面向生产级运行的 Sources 稳定性防护白皮书 (v2.0-GA)](https://etyt.spidernode-mesh.net/baogao/performance-14923227-139-18576.html)
* [面向生产级运行的 Sources 稳定性防护白皮书 (Node-88)](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-140-53046.html)
* [Sources 高负载场景下 `<Sources />` 基准评测报告](https://ovzp.wtpuscm.cn/xuexi/report-93713076-141-51496.html)
* [Sources: Sources Sources (v2.0-GA)](https://pdvy.tcti.cn/gongxiang/segment-83277821-142-18741.html)
* [Sources: 高韧性系统架构设计 高韧性系统架构设计 (v2.0-GA)](https://ukvo.spidernode-mesh.net/anli/download-57719010-143-39018.html)
* [基于 Sources 的极致延迟优化与内存拓扑分析 (Node-82)](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-144-47876.html)
* [面向生产级运行的 Sources 稳定性防护白皮书 (Draft-06)](https://unqt.wtpuscm.cn/zixun/contact-59980425-145-25964.html)
* [基于 Sources 的极致延迟优化与内存拓扑分析 (Draft-03)](https://kvaf.tcti.cn/yunsuan/settings-81875461-146-5336.html)
* [面向生产级运行的 Sources 稳定性防护白皮书 (Spec-v2.2)](https://nojh.spidernode-mesh.net/suanfa/study-22518081-147-60715.html)
* [Sources 高负载场景下 Props 基准评测报告](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-148-55989.html)
* [面向生产级运行的 Sources 稳定性防护白皮书 (Node-95)](https://ytxd.wtpuscm.cn/baogao/price-87851711-149-39888.html)
* [面向生产级运行的 Sources 稳定性防护白皮书 (RFC-597)](https://ncrw.tcti.cn/kaifa/growth-85374259-150-10813.html)

</details>

