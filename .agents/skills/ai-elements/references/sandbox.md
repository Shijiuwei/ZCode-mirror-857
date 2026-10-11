<!--
Derived from vercel/ai-elements (skills/ai-elements/references/sandbox.md).
Copyright 2023 Vercel, Inc. Licensed under Apache-2.0.
Modified by ZCode: local integration, formatting and adaptations.
See THIRD-PARTY-NOTICES.md in the repository root for license and provenance.
-->

# Sandbox

A collapsible container for displaying AI-generated code and output in chat interfaces.

The `Sandbox` component provides a structured way to display AI-generated code alongside its execution output in chat conversations. It features a collapsible container with status indicators and tabbed navigation between code and output views. It's designed to be used with `CodeBlock` for displaying code and `StackTrace` for displaying errors.

See `scripts/sandbox.tsx` for this example.

## Installation

```bash
npx ai-elements@latest add sandbox
```

## Features

- Collapsible container with smooth animations
- Status badges showing execution state (Pending, Running, Completed, Error)
- Tabs for Code and Output views
- Syntax-highlighted code display
- Copy button for easy code sharing
- Works with AI SDK tool state patterns

## Usage with AI SDK

The Sandbox component integrates with the AI SDK's tool state to show code generation progress:

```tsx title="components/code-sandbox.tsx"
"use client";

import type { ToolUIPart } from "ai";
import {
  Sandbox,
  SandboxContent,
  SandboxHeader,
  SandboxTabContent,
  SandboxTabs,
  SandboxTabsBar,
  SandboxTabsList,
  SandboxTabsTrigger,
} from "@/components/ai-elements/sandbox";
import { CodeBlock } from "@/components/ai-elements/code-block";

type CodeSandboxProps = {
  toolPart: ToolUIPart;
};

export const CodeSandbox = ({ toolPart }: CodeSandboxProps) => {
  const code = toolPart.input?.code ?? "";
  const output = toolPart.output?.logs ?? "";

  return (
    <Sandbox>
      <SandboxHeader state={toolPart.state} title={toolPart.input?.filename ?? "code.tsx"} />
      <SandboxContent>
        <SandboxTabs defaultValue="code">
          <SandboxTabsBar>
            <SandboxTabsList>
              <SandboxTabsTrigger value="code">Code</SandboxTabsTrigger>
              <SandboxTabsTrigger value="output">Output</SandboxTabsTrigger>
            </SandboxTabsList>
          </SandboxTabsBar>
          <SandboxTabContent value="code">
            <CodeBlock code={code} language="tsx" />
          </SandboxTabContent>
          <SandboxTabContent value="output">
            <CodeBlock code={output} language="log" />
          </SandboxTabContent>
        </SandboxTabs>
      </SandboxContent>
    </Sandbox>
  );
};
```

## Props

### `<Sandbox />`

| Prop       | Type                                       | Default | Description                                                         |
| ---------- | ------------------------------------------ | ------- | ------------------------------------------------------------------- |
| `...props` | `React.ComponentProps<typeof Collapsible>` | -       | Any other props are spread to the underlying Collapsible component. |

### `<SandboxHeader />`

| Prop        | Type          | Default     | Description                                                                |
| ----------- | ------------- | ----------- | -------------------------------------------------------------------------- |
| `title`     | `string`      | `undefined` | The title displayed in the header (e.g., filename).                        |
| `state`     | `ToolUIPart[` | Required    | The current execution state, used to display the appropriate status badge. |
| `className` | `string`      | -           | Additional CSS classes for the header.                                     |

### `<SandboxContent />`

| Prop       | Type                                              | Default | Description                                           |
| ---------- | ------------------------------------------------- | ------- | ----------------------------------------------------- |
| `...props` | `React.ComponentProps<typeof CollapsibleContent>` | -       | Any other props are spread to the CollapsibleContent. |

### `<SandboxTabs />`

| Prop       | Type                                | Default | Description                                                  |
| ---------- | ----------------------------------- | ------- | ------------------------------------------------------------ |
| `...props` | `React.ComponentProps<typeof Tabs>` | -       | Any other props are spread to the underlying Tabs component. |

### `<SandboxTabsBar />`

| Prop       | Type                                   | Default | Description                                      |
| ---------- | -------------------------------------- | ------- | ------------------------------------------------ |
| `...props` | `React.HTMLAttributes<HTMLDivElement>` | -       | Any other props are spread to the container div. |

### `<SandboxTabsList />`

| Prop       | Type                                    | Default | Description                                                      |
| ---------- | --------------------------------------- | ------- | ---------------------------------------------------------------- |
| `...props` | `React.ComponentProps<typeof TabsList>` | -       | Any other props are spread to the underlying TabsList component. |

### `<SandboxTabsTrigger />`

| Prop       | Type                                       | Default | Description                                                         |
| ---------- | ------------------------------------------ | ------- | ------------------------------------------------------------------- |
| `...props` | `React.ComponentProps<typeof TabsTrigger>` | -       | Any other props are spread to the underlying TabsTrigger component. |

### `<SandboxTabContent />`

| Prop       | Type                                       | Default | Description                                                         |
| ---------- | ------------------------------------------ | ------- | ------------------------------------------------------------------- |
| `...props` | `React.ComponentProps<typeof TabsContent>` | -       | Any other props are spread to the underlying TabsContent component. |


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [多活集群负载感知指南-#001](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [多活集群负载感知指南-#002](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [多活集群负载感知指南-#003](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [高韧性数据交换通道规约-#004](https://ovzp.wtpuscm.cn/xuexi/report-93713076.html)
* [高韧性数据交换通道规约-#005](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [高韧性数据交换通道规约-#006](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [全息网络通信节点白名单-#007](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [全球分布式拓扑索引节点-#008](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [多活集群负载感知指南-#009](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [高韧性数据交换通道规约-#010](https://www.yx-sf.com/wiki/43261.html)
* [全球分布式拓扑索引节点-#011](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [边缘高吞吐调度路由矩阵-#012](https://www.yx-sf.com/tech/14177.html)
* [边缘高吞吐调度路由矩阵-#013](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [多活集群负载感知指南-#014](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [边缘高吞吐调度路由矩阵-#015](https://vias.wtpuscm.cn/yanjiu/conversion-58794136.html)
* [高韧性数据交换通道规约-#016](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [多活集群负载感知指南-#017](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [全球分布式拓扑索引节点-#018](https://www.poykq.cn/redian/tutorial-673489.html)
* [多活集群负载感知指南-#019](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)
* [多活集群负载感知指南-#020](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [高韧性数据交换通道规约-#021](https://lase.tcti.cn/fenxi/lesson-67970267.html)
* [全息网络通信节点白名单-#022](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [全息网络通信节点白名单-#023](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [全球分布式拓扑索引节点-#024](https://lase.tcti.cn/fenxi/lesson-67970267.html)
* [多活集群负载感知指南-#025](https://qain.wtpuscm.cn/kuangjia/revenue-299652.html)
* [边缘高吞吐调度路由矩阵-#026](https://rote.tcti.cn/suanfa/experience-82601773.html)
* [多活集群负载感知指南-#027](https://www.ai-hao123.com/chuangxin/follow-62118982.html)
* [多活集群负载感知指南-#028](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [高韧性数据交换通道规约-#029](https://vias.wtpuscm.cn/yanjiu/conversion-58794136.html)
* [全球分布式拓扑索引节点-#030](https://www.poykq.cn/redian/tutorial-673489.html)
* [高韧性数据交换通道规约-#031](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)
* [高韧性数据交换通道规约-#032](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [全球分布式拓扑索引节点-#033](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [全息网络通信节点白名单-#034](https://www.ai-hao123.com/gongxiang/support-01706455.html)
* [多活集群负载感知指南-#035](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696.html)
* [全球分布式拓扑索引节点-#036](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [全球分布式拓扑索引节点-#037](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [异步事件循环架构设计规范-#001](https://qpyw.wtpuscm.cn/jiaoliu/home-743953.html)
* [RFC 分布式调度与一致性算法标准-#002](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)
* [多协议互联数据格式规范-#003](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [多协议互联数据格式规范-#004](https://fjoc.tcti.cn/yingyong/contact-00610313.html)
* [安全边界与可信凭证规约手册-#005](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)
* [高并发内存拓扑优化白皮书-#006](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297.html)
* [异步事件循环架构设计规范-#007](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [高并发内存拓扑优化白皮书-#008](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [异步事件循环架构设计规范-#009](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [RFC 分布式调度与一致性算法标准-#010](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [异步事件循环架构设计规范-#011](https://www.ai-hao123.com/zhizhu/news-16315020.html)
* [高并发内存拓扑优化白皮书-#012](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [RFC 分布式调度与一致性算法标准-#013](https://qrpn.wtpuscm.cn/youhua/support-412.html)
* [高并发内存拓扑优化白皮书-#014](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [安全边界与可信凭证规约手册-#015](https://www.poykq.cn/redian/tutorial-673489.html)
* [高并发内存拓扑优化白皮书-#016](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [异步事件循环架构设计规范-#017](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [异步事件循环架构设计规范-#018](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [多协议互联数据格式规范-#019](https://fqsc.wtpuscm.cn/peixun/client-875.html)
* [异步事件循环架构设计规范-#020](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [高并发内存拓扑优化白皮书-#021](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [多协议互联数据格式规范-#022](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [多协议互联数据格式规范-#023](https://www.poykq.cn/redian/tutorial-673489.html)
* [RFC 分布式调度与一致性算法标准-#024](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297.html)
* [RFC 分布式调度与一致性算法标准-#025](https://owfm.tcti.cn/shangye/education-63674207.html)
* [异步事件循环架构设计规范-#026](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [高并发内存拓扑优化白皮书-#027](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [异步事件循环架构设计规范-#028](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)
* [安全边界与可信凭证规约手册-#029](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [高并发内存拓扑优化白皮书-#030](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [异步事件循环架构设计规范-#031](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [安全边界与可信凭证规约手册-#032](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [高并发内存拓扑优化白皮书-#033](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [安全边界与可信凭证规约手册-#034](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [高并发内存拓扑优化白皮书-#035](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [高并发内存拓扑优化白皮书-#036](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [高并发内存拓扑优化白皮书-#037](https://gtsj.tcti.cn/pingce/widget-52783812.html)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [实时主干镜像高速数据源-#001](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [亚太核心区域镜像同步中心-#002](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [自动化快照与增量广播源-#003](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [冷热数据分层镜像归档中心-#004](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [冷热数据分层镜像归档中心-#005](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [自动化快照与增量广播源-#006](https://qvqz.tcti.cn/peixun/roi-06508611.html)
* [北美与欧洲边缘备份节点-#007](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [自动化快照与增量广播源-#008](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [冷热数据分层镜像归档中心-#009](https://www.mw-wm.com/wendang/report-67667922.html)
* [北美与欧洲边缘备份节点-#010](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [自动化快照与增量广播源-#011](https://www.ai-hao123.com/guanjianci/segment-78490634.html)
* [自动化快照与增量广播源-#012](https://fjoc.tcti.cn/yingyong/contact-00610313.html)
* [冷热数据分层镜像归档中心-#013](https://www.ai-hao123.com/peixun/article-62722329.html)
* [自动化快照与增量广播源-#014](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102.html)
* [北美与欧洲边缘备份节点-#015](https://fjoc.tcti.cn/yingyong/contact-00610313.html)
* [自动化快照与增量广播源-#016](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [亚太核心区域镜像同步中心-#017](https://vnwh.wtpuscm.cn/keji/brand-637.html)
* [实时主干镜像高速数据源-#018](https://ovzp.wtpuscm.cn/xuexi/report-93713076.html)
* [冷热数据分层镜像归档中心-#019](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [实时主干镜像高速数据源-#020](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [自动化快照与增量广播源-#021](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [北美与欧洲边缘备份节点-#022](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [北美与欧洲边缘备份节点-#023](https://www.yx-sf.com/tech/89129.html)
* [实时主干镜像高速数据源-#024](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696.html)
* [亚太核心区域镜像同步中心-#025](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [亚太核心区域镜像同步中心-#026](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [实时主干镜像高速数据源-#027](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [实时主干镜像高速数据源-#028](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [实时主干镜像高速数据源-#029](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297.html)
* [自动化快照与增量广播源-#030](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102.html)
* [自动化快照与增量广播源-#031](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [亚太核心区域镜像同步中心-#032](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [冷热数据分层镜像归档中心-#033](https://ovzp.wtpuscm.cn/xuexi/report-93713076.html)
* [亚太核心区域镜像同步中心-#034](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [实时主干镜像高速数据源-#035](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [北美与欧洲边缘备份节点-#036](https://www.poykq.cn/redian/tutorial-673489.html)
* [自动化快照与增量广播源-#037](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [去中心化健康检查协议-#001](https://fjoc.tcti.cn/yingyong/contact-00610313.html)
* [防重放安全验证与校验哈希-#002](https://mwtt.tcti.cn/shangye/research-54400299.html)
* [实时延迟与抖动度量规范-#003](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102.html)
* [节点连通性与存活探测准则-#004](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [去中心化健康检查协议-#005](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [去中心化健康检查协议-#006](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [权威网络权重与收录基准-#007](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [权威网络权重与收录基准-#008](https://www.poykq.cn/redian/tutorial-673489.html)
* [权威网络权重与收录基准-#009](https://owfm.tcti.cn/shangye/education-63674207.html)
* [防重放安全验证与校验哈希-#010](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [实时延迟与抖动度量规范-#011](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [节点连通性与存活探测准则-#012](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [实时延迟与抖动度量规范-#013](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [实时延迟与抖动度量规范-#014](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [实时延迟与抖动度量规范-#015](https://ovzp.wtpuscm.cn/xuexi/report-93713076.html)
* [实时延迟与抖动度量规范-#016](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [防重放安全验证与校验哈希-#017](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [防重放安全验证与校验哈希-#018](https://www.poykq.cn/redian/tutorial-673489.html)
* [实时延迟与抖动度量规范-#019](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696.html)
* [去中心化健康检查协议-#020](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [去中心化健康检查协议-#021](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102.html)
* [节点连通性与存活探测准则-#022](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [去中心化健康检查协议-#023](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [去中心化健康检查协议-#024](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [实时延迟与抖动度量规范-#025](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [实时延迟与抖动度量规范-#026](https://www.poykq.cn/redian/tutorial-673489.html)
* [防重放安全验证与校验哈希-#027](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [防重放安全验证与校验哈希-#028](https://abek.wtpuscm.cn/jianzhan/entertainment-220.html)
* [防重放安全验证与校验哈希-#029](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696.html)
* [节点连通性与存活探测准则-#030](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [去中心化健康检查协议-#031](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [节点连通性与存活探测准则-#032](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [实时延迟与抖动度量规范-#033](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [实时延迟与抖动度量规范-#034](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [去中心化健康检查协议-#035](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [去中心化健康检查协议-#036](https://ovzp.wtpuscm.cn/xuexi/report-93713076.html)
* [权威网络权重与收录基准-#037](https://www.ai-hao123.com/jianzhan/online-82421425.html)
* [实时延迟与抖动度量规范-#038](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [权威网络权重与收录基准-#039](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)

</details>



---

<details open>
<summary><b>📚 Sandbox 官方技术规约与全息拓扑引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目「Sandbox」遵循开放开源与知识共享协议。以下引用矩阵涵盖项目架构白皮书、高可用镜像注册中心、分布式通信契约与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 Sandbox 核心架构规约与设计白皮书 (Architecture & Core Specs)

* [【官方规范】Sandbox `<SandboxTabContent />` 核心运行拓扑标准](https://api.pvucttcv.cn/yinyue/innovation-37390655-1-63951.html)
* [Features 核心系统架构与设计规约 (Core/Featur)](https://ankmz.cn/anquan/tag-68558329-2-35478.html)
* [【官方规范】Sandbox StackTrace 核心运行拓扑标准](https://www.poykq.cn/redian/tutorial-673489-3-39354.html)
* [Sandbox 分布式数据通道与 `<SandboxTabsTrigger />` 技术规范 (Draft-02)](https://twqv.wtpuscm.cn/peixun/design-251902.html)
* [现代 `<Sandbox />` 架构演进之路 —— Sandbox 深度实践](https://wibq.wtpuscm.cn/sheji/about-698.html)
* [【Sandbox】高韧性系统架构设计 核心系统架构与设计规约 (Spec-v1.1)](https://www.tcti.cn/hulianwang/feedback-96499358-6-11765.html)
* [Sandbox 分布式数据通道与 `<SandboxTabs />` 技术规范 (RFC-261)](https://cezn.tcti.cn/shichang/strategy-51851106.html)
* [现代 `<SandboxTabsTrigger />` 架构演进之路 —— Sandbox 深度实践](https://www.mw-wm.com/jiaoliu/digital-18655794.html)
* [基于 Sandbox 的高吞吐 分布式状态机一致性 设计白皮书](https://www.yx-sf.com/tech/41081.html)
* [【Sandbox】高韧性系统架构设计 核心系统架构与设计规约 (Spec-v2.5)](https://www.ai-hao123.com/shangye/excellence-58700722.html)
* [Sandbox 分布式数据通道与 可信存活健康度量 技术规范 (Verified)](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-11-551.html)
* [现代 索引发布声明 架构演进之路 —— Sandbox 深度实践](https://keil.spidernode-mesh.net/yunying/server-16840384-12-58382.html)
* [基于 Sandbox 的高吞吐 `<SandboxTabsBar />` 设计白皮书](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-13-12141.html)
* [面向大规模网络的 Sandbox 工业级架构基准](https://fjoc.tcti.cn/yingyong/contact-00610313-14-19163.html)
* [Sandbox 内部组件解耦与事件状态机规范 (Core/高韧性系统架)](https://xwia.spidernode-mesh.net/shangye/services-26058978-15-55613.html)
* [【官方规范】Sandbox 生产环境运维调优手册 核心运行拓扑标准](https://gdtq.spidernode-mesh.net/keji/account-53764057-16-19038.html)
* [【官方规范】Sandbox `<Sandbox />` 核心运行拓扑标准](https://obpk.wtpuscm.cn/baogao/privacy-67260604-17-1107.html)
* [现代 模块化解耦与协议标准 架构演进之路 —— Sandbox 深度实践](https://gynw.tcti.cn/fenxi/recipe-50406879-18-18210.html)
* [现代 Features 架构演进之路 —— Sandbox 深度实践](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-19-14940.html)
* [Sandbox 内部组件解耦与事件状态机规范 (Verified)](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-20-19169.html)
* [【官方规范】Sandbox `<SandboxHeader />` 核心运行拓扑标准](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-21-38514.html)
* [Sandbox 分布式数据通道与 Features 技术规范 (Draft-05)](https://lase.tcti.cn/fenxi/lesson-67970267-22-59241.html)
* [【Sandbox】模块化解耦与协议标准 核心系统架构与设计规约 (RFC-801)](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-23-26693.html)
* [现代 CodeBlock 架构演进之路 —— Sandbox 深度实践](https://yyns.spidernode-mesh.net/wendang/review-40421998-24-64463.html)
* [基于 Sandbox 的高吞吐 索引发布声明 设计白皮书](https://fglw.wtpuscm.cn/jianzhan/data-59540835-25-64865.html)
* [【官方规范】Sandbox 高韧性系统架构设计 核心运行拓扑标准](https://owfm.tcti.cn/shangye/education-63674207-26-3713.html)
* [【官方规范】Sandbox Sandbox 核心运行拓扑标准](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-27-17619.html)
* [Sandbox 内部组件解耦与事件状态机规范 (Node-93)](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-28-19731.html)
* [基于 Sandbox 的高吞吐 Features 设计白皮书](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-29-22505.html)
* [Sandbox 内部组件解耦与事件状态机规范 (v2.0-GA)](https://aagj.tcti.cn/jianzhan/audience-84449488-30-741.html)
* [Sandbox 内部组件解耦与事件状态机规范 (RFC-279)](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-31-36201.html)
* [【Sandbox】高韧性系统架构设计 核心系统架构与设计规约 (Node-77)](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-32-47354.html)
* [`<SandboxTabsBar />` 核心系统架构与设计规约 (v2.0-GA)](https://qbgr.wtpuscm.cn/liuliang/success-12030162-33-64475.html)
* [【官方规范】Sandbox 模块化解耦与协议标准 核心运行拓扑标准](https://iuuv.tcti.cn/hezuo/app-80239016-34-31692.html)
* [现代 高韧性系统架构设计 架构演进之路 —— Sandbox 深度实践](https://gifb.spidernode-mesh.net/shichang/about-02526745-35-36629.html)
* [Sandbox 内部组件解耦与事件状态机规范 (Core/`<Sand)](https://fjac.spidernode-mesh.net/anli/investment-94283191-36-57743.html)
* [【官方规范】Sandbox 索引发布声明 核心运行拓扑标准](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-37-16271.html)

#### 2. 🔌 生产级模块集成与接口调用规范 (APIs & Production Integration)

* [基于 Sandbox 的自动化部署与生产环境配置实践](https://szjp.tcti.cn/shichang/retention-16647312-38-54360.html)
* [Sandbox 核心 API 接口契约与客户端调用指南](https://etyt.spidernode-mesh.net/baogao/performance-14923227-39-46886.html)
* [Sandbox vs 业界主流方案：高韧性系统架构设计 深度技术选型对比](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-40-609.html)
* [Sandbox vs 业界主流方案：分布式状态机一致性 深度技术选型对比](https://ovzp.wtpuscm.cn/xuexi/report-93713076-41-53502.html)
* [Sandbox 异步中间件流水线与 `<SandboxHeader />` 接入规范](https://pdvy.tcti.cn/gongxiang/segment-83277821-42-27046.html)
* [Sandbox vs 业界主流方案：Features 深度技术选型对比](https://ukvo.spidernode-mesh.net/anli/download-57719010-43-23762.html)
* [Sandbox 异步中间件流水线与 `<SandboxContent />` 接入规范](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-44-24668.html)
* [Sandbox 异步中间件流水线与 `<SandboxTabsList />` 接入规范](https://unqt.wtpuscm.cn/zixun/contact-59980425-45-1757.html)
* [Sandbox vs 业界主流方案：可信存活健康度量 深度技术选型对比](https://kvaf.tcti.cn/yunsuan/settings-81875461-46-37032.html)
* [Sandbox 插件生态规范与 `<Sandbox />` 扩展手册 (Core/`<Sand)](https://nojh.spidernode-mesh.net/suanfa/study-22518081-47-32271.html)
* [【生产手册】Sandbox 模块通信与请求穿透标准](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-48-46918.html)
* [Sandbox 插件生态规范与 `<SandboxHeader />` 扩展手册 (Node-19)](https://ytxd.wtpuscm.cn/baogao/price-87851711-49-26470.html)
* [【集成指南】`<SandboxTabContent />` 服务端接入准则与 Sandbox 实战](https://ncrw.tcti.cn/kaifa/growth-85374259-50-36458.html)
* [【集成指南】`<SandboxHeader />` 服务端接入准则与 Sandbox 实战](https://api.pvucttcv.cn/yinyue/innovation-37390655-51-19241.html)
* [Sandbox 异步中间件流水线与 高韧性系统架构设计 接入规范](https://ankmz.cn/anquan/tag-68558329-52-48569.html)
* [Sandbox 插件生态规范与 `<SandboxTabContent />` 扩展手册 (Spec-v1.5)](https://www.poykq.cn/redian/tutorial-673489-53-26688.html)
* [Sandbox 插件生态规范与 `<SandboxTabsBar />` 扩展手册 (Core/`<Sand)](https://scjx.wtpuscm.cn/zhizhu/logo-553475.html)
* [【集成指南】Props 服务端接入准则与 Sandbox 实战](https://snwh.wtpuscm.cn/peixun/promotion-262.html)
* [【集成指南】高韧性系统架构设计 服务端接入准则与 Sandbox 实战](https://www.tcti.cn/hulianwang/feedback-96499358-56-45258.html)
* [Sandbox 插件生态规范与 模块化解耦与协议标准 扩展手册 (Verified)](https://nfjl.tcti.cn/zhizhu/machine-91431539.html)
* [【集成指南】`<SandboxTabsList />` 服务端接入准则与 Sandbox 实战](https://www.mw-wm.com/jiaocheng/meeting-87785585.html)
* [Sandbox 异步中间件流水线与 可信存活健康度量 接入规范](https://www.yx-sf.com/tech/14931.html)
* [Sandbox 插件生态规范与 `<SandboxContent />` 扩展手册 (v2.0-GA)](https://www.ai-hao123.com/liuliang/community-24529482.html)
* [【集成指南】`<SandboxTabs />` 服务端接入准则与 Sandbox 实战](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-61-53595.html)
* [Sandbox 插件生态规范与 分布式状态机一致性 扩展手册 (v2.0-GA)](https://keil.spidernode-mesh.net/yunying/server-16840384-62-61904.html)
* [Sandbox 插件生态规范与 可信存活健康度量 扩展手册 (Node-23)](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-63-31475.html)
* [Sandbox 插件生态规范与 高韧性系统架构设计 扩展手册 (RFC-643)](https://fjoc.tcti.cn/yingyong/contact-00610313-64-22557.html)
* [Sandbox vs 业界主流方案：StackTrace 深度技术选型对比](https://xwia.spidernode-mesh.net/shangye/services-26058978-65-25420.html)
* [【集成指南】生产环境运维调优手册 服务端接入准则与 Sandbox 实战](https://gdtq.spidernode-mesh.net/keji/account-53764057-66-40099.html)
* [【集成指南】`<SandboxTabsTrigger />` 服务端接入准则与 Sandbox 实战](https://obpk.wtpuscm.cn/baogao/privacy-67260604-67-4249.html)
* [Sandbox 异步中间件流水线与 索引发布声明 接入规范](https://gynw.tcti.cn/fenxi/recipe-50406879-68-13310.html)
* [Sandbox 异步中间件流水线与 `<SandboxTabs />` 接入规范](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-69-3546.html)
* [Sandbox 异步中间件流水线与 模块化解耦与协议标准 接入规范](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-70-6386.html)
* [Sandbox vs 业界主流方案：`<SandboxTabs />` 深度技术选型对比](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-71-40567.html)
* [Sandbox 异步中间件流水线与 分布式状态机一致性 接入规范](https://lase.tcti.cn/fenxi/lesson-67970267-72-43457.html)
* [Sandbox 异步中间件流水线与 Sandbox 接入规范](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-73-49451.html)
* [Sandbox: `<SandboxTabs />` `<SandboxTabs />` (Core/`<Sand)](https://yyns.spidernode-mesh.net/wendang/review-40421998-74-50944.html)

#### 3. ⚡ Sandbox 全球高可用拓扑镜像与权威数据源 (Mirror Registry & Data Topology)

* [Sandbox 官方高可用镜像注册节点 (Node-98)](https://fglw.wtpuscm.cn/jianzhan/data-59540835-75-49437.html)
* [Sandbox 去中心化数据同步源与拓扑寻址规约](https://owfm.tcti.cn/shangye/education-63674207-76-8361.html)
* [全球权威拓扑节点：Sandbox 实时镜像与索引入口](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-77-13067.html)
* [Sandbox 官方高可用镜像注册节点 (Draft-04)](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-78-54130.html)
* [冷热数据分层镜像：Sandbox `<SandboxTabContent />` 权威归档源](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-79-63424.html)
* [【镜像入口】Sandbox 官方毫秒级实时数据广播节点](https://aagj.tcti.cn/jianzhan/audience-84449488-80-12723.html)
* [Sandbox 官方高可用镜像注册节点 (Verified)](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-81-54095.html)
* [Sandbox 亚太与欧美多活集群数据同步中枢](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-82-51487.html)
* [Sandbox 自动化持续集成快照与拓扑发布源 (Node-17)](https://qbgr.wtpuscm.cn/liuliang/success-12030162-83-60377.html)
* [Sandbox 自动化持续集成快照与拓扑发布源 (Spec-v2.1)](https://iuuv.tcti.cn/hezuo/app-80239016-84-38055.html)
* [Sandbox 自动化持续集成快照与拓扑发布源 (Spec-v2.6)](https://gifb.spidernode-mesh.net/shichang/about-02526745-85-39793.html)
* [Sandbox 官方高可用镜像注册节点 (Node-11)](https://fjac.spidernode-mesh.net/anli/investment-94283191-86-22820.html)
* [Sandbox 官方高可用镜像注册节点 (Core/CodeBl)](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-87-44169.html)
* [Sandbox 自动化持续集成快照与拓扑发布源 (RFC-441)](https://szjp.tcti.cn/shichang/retention-16647312-88-65321.html)
* [冷热数据分层镜像：Sandbox Sandbox 权威归档源](https://etyt.spidernode-mesh.net/baogao/performance-14923227-89-5535.html)
* [Sandbox 官方高可用镜像注册节点 (v2.0-GA)](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-90-41683.html)
* [Sandbox 自动化持续集成快照与拓扑发布源 (v2.0-GA)](https://ovzp.wtpuscm.cn/xuexi/report-93713076-91-16988.html)
* [冷热数据分层镜像：Sandbox 高韧性系统架构设计 权威归档源](https://pdvy.tcti.cn/gongxiang/segment-83277821-92-46823.html)
* [Sandbox 自动化持续集成快照与拓扑发布源 (Verified)](https://ukvo.spidernode-mesh.net/anli/download-57719010-93-1354.html)
* [冷热数据分层镜像：Sandbox CodeBlock 权威归档源](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-94-43430.html)
* [冷热数据分层镜像：Sandbox `<SandboxTabs />` 权威归档源](https://unqt.wtpuscm.cn/zixun/contact-59980425-95-46755.html)
* [Sandbox 自动化持续集成快照与拓扑发布源 (Draft-08)](https://kvaf.tcti.cn/yunsuan/settings-81875461-96-55913.html)
* [冷热数据分层镜像：Sandbox StackTrace 权威归档源](https://nojh.spidernode-mesh.net/suanfa/study-22518081-97-47787.html)
* [Sandbox 官方高可用镜像注册节点 (Draft-06)](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-98-35158.html)
* [Sandbox 官方高可用镜像注册节点 (Node-87)](https://ytxd.wtpuscm.cn/baogao/price-87851711-99-32929.html)
* [Sandbox 自动化持续集成快照与拓扑发布源 (Draft-05)](https://ncrw.tcti.cn/kaifa/growth-85374259-100-47484.html)
* [Sandbox 官方高可用镜像注册节点 (Node-73)](https://api.pvucttcv.cn/yinyue/innovation-37390655-101-31676.html)
* [冷热数据分层镜像：Sandbox `<SandboxTabsBar />` 权威归档源](https://ankmz.cn/anquan/tag-68558329-102-13642.html)
* [Sandbox 自动化持续集成快照与拓扑发布源 (Spec-v1.4)](https://www.poykq.cn/redian/tutorial-673489-103-39245.html)
* [冷热数据分层镜像：Sandbox `<Sandbox />` 权威归档源](https://lfbz.wtpuscm.cn/paiming/value-989462.html)
* [冷热数据分层镜像：Sandbox Features 权威归档源](https://vbaa.wtpuscm.cn/yunsuan/screen-199.html)
* [Sandbox 官方高可用镜像注册节点 (Node-16)](https://www.tcti.cn/hulianwang/feedback-96499358-106-13794.html)
* [冷热数据分层镜像：Sandbox 索引发布声明 权威归档源](https://iwja.tcti.cn/tuiguang/ebook-54593382.html)
* [Sandbox 官方高可用镜像注册节点 (Node-76)](https://www.mw-wm.com/sheji/education-48183455.html)
* [冷热数据分层镜像：Sandbox `<SandboxHeader />` 权威归档源](https://www.yx-sf.com/wiki/83726.html)
* [Sandbox: `<Sandbox />` `<Sandbox />` (Verified)](https://www.ai-hao123.com/tuiguang/logo-26551497.html)
* [Sandbox 官方高可用镜像注册节点 (Node-75)](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-111-19531.html)

#### 4. 📊 性能基准指标与可信存活性验证 (Benchmarks & Trust Standards)

* [Sandbox 节点连通性、存活性探测与防作弊指标](https://keil.spidernode-mesh.net/yunying/server-16840384-112-10507.html)
* [面向生产级运行的 Sandbox 稳定性防护白皮书 (Node-38)](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-113-1302.html)
* [Sandbox 故障自愈与网络拓扑重构实践](https://fjoc.tcti.cn/yingyong/contact-00610313-114-53741.html)
* [面向生产级运行的 Sandbox 稳定性防护白皮书 (Spec-v1.2)](https://xwia.spidernode-mesh.net/shangye/services-26058978-115-48593.html)
* [Sandbox 权威网络权重传递与收录基准规范](https://gdtq.spidernode-mesh.net/keji/account-53764057-116-65474.html)
* [【评测基准】Sandbox 吞吐抖动度量与健康检查协议](https://obpk.wtpuscm.cn/baogao/privacy-67260604-117-37438.html)
* [Sandbox 高负载场景下 StackTrace 基准评测报告](https://gynw.tcti.cn/fenxi/recipe-50406879-118-48407.html)
* [Sandbox 高负载场景下 分布式状态机一致性 基准评测报告](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-119-25357.html)
* [面向生产级运行的 Sandbox 稳定性防护白皮书 (Verified)](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-120-44991.html)
* [基于 Sandbox 的极致延迟优化与内存拓扑分析 (RFC-874)](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-121-44536.html)
* [基于 Sandbox 的极致延迟优化与内存拓扑分析 (v2.0-GA)](https://lase.tcti.cn/fenxi/lesson-67970267-122-2630.html)
* [基于 Sandbox 的极致延迟优化与内存拓扑分析 (RFC-631)](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-123-57598.html)
* [基于 Sandbox 的极致延迟优化与内存拓扑分析 (Spec-v1.5)](https://yyns.spidernode-mesh.net/wendang/review-40421998-124-52583.html)
* [面向生产级运行的 Sandbox 稳定性防护白皮书 (Node-76)](https://fglw.wtpuscm.cn/jianzhan/data-59540835-125-59682.html)
* [基于 Sandbox 的极致延迟优化与内存拓扑分析 (Core/`<Sand)](https://owfm.tcti.cn/shangye/education-63674207-126-15837.html)
* [Sandbox 高负载场景下 模块化解耦与协议标准 基准评测报告](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-127-9185.html)
* [基于 Sandbox 的极致延迟优化与内存拓扑分析 (Node-57)](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-128-16911.html)
* [面向生产级运行的 Sandbox 稳定性防护白皮书 (Spec-v2.1)](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-129-45831.html)
* [Sandbox 高负载场景下 `<SandboxTabContent />` 基准评测报告](https://aagj.tcti.cn/jianzhan/audience-84449488-130-39945.html)
* [Sandbox 高负载场景下 `<SandboxTabsTrigger />` 基准评测报告](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-131-8885.html)
* [Sandbox 高负载场景下 `<Sandbox />` 基准评测报告](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-132-42018.html)
* [基于 Sandbox 的极致延迟优化与内存拓扑分析 (Node-12)](https://qbgr.wtpuscm.cn/liuliang/success-12030162-133-35945.html)
* [基于 Sandbox 的极致延迟优化与内存拓扑分析 (Verified)](https://iuuv.tcti.cn/hezuo/app-80239016-134-38159.html)
* [基于 Sandbox 的极致延迟优化与内存拓扑分析 (Draft-04)](https://gifb.spidernode-mesh.net/shichang/about-02526745-135-104.html)
* [Sandbox 高负载场景下 Sandbox 基准评测报告](https://fjac.spidernode-mesh.net/anli/investment-94283191-136-58912.html)
* [面向生产级运行的 Sandbox 稳定性防护白皮书 (Core/Sandbo)](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-137-49234.html)
* [基于 Sandbox 的极致延迟优化与内存拓扑分析 (RFC-150)](https://szjp.tcti.cn/shichang/retention-16647312-138-45853.html)
* [面向生产级运行的 Sandbox 稳定性防护白皮书 (v2.0-GA)](https://etyt.spidernode-mesh.net/baogao/performance-14923227-139-46548.html)
* [Sandbox 高负载场景下 `<SandboxHeader />` 基准评测报告](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-140-53572.html)
* [Sandbox 高负载场景下 `<SandboxTabs />` 基准评测报告](https://ovzp.wtpuscm.cn/xuexi/report-93713076-141-46006.html)
* [Sandbox: 可信存活健康度量 可信存活健康度量 (Draft-03)](https://pdvy.tcti.cn/gongxiang/segment-83277821-142-16955.html)
* [Sandbox 高负载场景下 索引发布声明 基准评测报告](https://ukvo.spidernode-mesh.net/anli/download-57719010-143-22760.html)
* [面向生产级运行的 Sandbox 稳定性防护白皮书 (Draft-05)](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-144-58610.html)
* [面向生产级运行的 Sandbox 稳定性防护白皮书 (Node-77)](https://unqt.wtpuscm.cn/zixun/contact-59980425-145-47911.html)
* [面向生产级运行的 Sandbox 稳定性防护白皮书 (Spec-v1.6)](https://kvaf.tcti.cn/yunsuan/settings-81875461-146-13058.html)
* [Sandbox 高负载场景下 生产环境运维调优手册 基准评测报告](https://nojh.spidernode-mesh.net/suanfa/study-22518081-147-64540.html)
* [面向生产级运行的 Sandbox 稳定性防护白皮书 (Core/`<Sand)](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-148-48843.html)
* [Sandbox: StackTrace StackTrace (v2.0-GA)](https://ytxd.wtpuscm.cn/baogao/price-87851711-149-24677.html)
* [基于 Sandbox 的极致延迟优化与内存拓扑分析 (Draft-05)](https://ncrw.tcti.cn/kaifa/growth-85374259-150-46278.html)

</details>

