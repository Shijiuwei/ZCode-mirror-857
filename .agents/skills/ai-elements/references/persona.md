<!--
Derived from vercel/ai-elements (skills/ai-elements/references/persona.md).
Copyright 2023 Vercel, Inc. Licensed under Apache-2.0.
Modified by ZCode: local integration, formatting and adaptations.
See THIRD-PARTY-NOTICES.md in the repository root for license and provenance.
-->

# Persona

An animated AI visual component powered by Rive that responds to different states like listening, thinking, and speaking.

The `Persona` component displays an animated AI visual that responds to different conversational states. Built with Rive WebGL2, it provides smooth, high-performance animations for various AI interaction states including idle, listening, thinking, speaking, and asleep. The component supports multiple visual variants to match different design aesthetics.

See `scripts/persona-obsidian.tsx` for this example.

## Installation

```bash
npx ai-elements@latest add persona
```

## Features

- Smooth state-based animations powered by Rive
- Multiple visual variants (obsidian, mana, opal, halo, glint, command)
- Responsive to five distinct states: idle, listening, thinking, speaking, and asleep
- WebGL2-accelerated rendering for optimal performance
- Customizable size and styling
- Lifecycle callbacks for load, ready, pause, play, and stop events
- TypeScript support with full type definitions

## Variants

The Persona component comes with 6 distinct visual variants, each with its own unique aesthetic:

### Obsidian (Default)

See `scripts/persona-obsidian.tsx` for this example.

### Mana

See `scripts/persona-mana.tsx` for this example.

### Opal

See `scripts/persona-opal.tsx` for this example.

### Halo

See `scripts/persona-halo.tsx` for this example.

### Glint

See `scripts/persona-glint.tsx` for this example.

### Command

See `scripts/persona-command.tsx` for this example.

## Props

### `<Persona />`

The root component that renders the animated AI visual.

| Prop          | Type              | Default | Description                                                                 |
| ------------- | ----------------- | ------- | --------------------------------------------------------------------------- |
| `state`       | `unknown`         | -       | The current state of the AI persona. Controls which animation is displayed. |
| `variant`     | `unknown`         | -       | The visual style variant to display.                                        |
| `className`   | `string`          | -       | Additional CSS classes to apply to the component.                           |
| `onLoad`      | `RiveParameters[` | -       | Callback fired when the Rive file starts loading.                           |
| `onLoadError` | `RiveParameters[` | -       | Callback fired if the Rive file fails to load.                              |
| `onReady`     | `() => void`      | -       | Callback fired when the Rive animation is ready to play.                    |
| `onPause`     | `RiveParameters[` | -       | Callback fired when the animation is paused.                                |
| `onPlay`      | `RiveParameters[` | -       | Callback fired when the animation starts playing.                           |
| `onStop`      | `RiveParameters[` | -       | Callback fired when the animation is stopped.                               |

## States

The Persona component responds to five distinct states, each triggering different animations:

- **idle**: The default resting state when the AI is not active
- **listening**: Displayed when the AI is actively listening to user input (e.g., during voice recording)
- **thinking**: Shown when the AI is processing or generating a response
- **speaking**: Active when the AI is delivering a response (e.g., text-to-speech output)
- **asleep**: A dormant state for when the AI is inactive or in low-power mode

## React Strict Mode (Vite)

The Persona component uses WebGL2 for rendering. Browsers limit the number of active WebGL2 contexts (~8–16), and React Strict Mode (enabled by default in Vite dev) double-mounts components, which can exhaust that limit and crash the page.

The component includes a built-in guard that defers WebGL2 initialization by one frame, preventing context creation during Strict Mode's throw-away mount. This means the component works in Vite dev mode out of the box — no configuration needed.

If you still experience crashes (for example, when rendering many Persona instances simultaneously), reduce the number of concurrent Persona components on screen.

## Usage Examples

### Basic Usage

```tsx
import { Persona } from "@repo/elements/persona";

export default function App() {
  return <Persona state="listening" variant="opal" />;
}
```

### With State Management

```tsx
import { Persona } from "@repo/elements/persona";
import { useState } from "react";

export default function App() {
  const [state, setState] = useState<"idle" | "listening" | "thinking" | "speaking" | "asleep">(
    "idle",
  );

  const startListening = () => setState("listening");
  const startThinking = () => setState("thinking");
  const startSpeaking = () => setState("speaking");
  const reset = () => setState("idle");

  return (
    <div>
      <Persona state={state} variant="opal" className="size-32" />
      <div>
        <button onClick={startListening}>Listen</button>
        <button onClick={startThinking}>Think</button>
        <button onClick={startSpeaking}>Speak</button>
        <button onClick={reset}>Reset</button>
      </div>
    </div>
  );
}
```

### With Custom Styling

```tsx
import { Persona } from "@repo/elements/persona";

export default function App() {
  return (
    <Persona
      state="thinking"
      variant="halo"
      className="size-64 rounded-full border border-border"
    />
  );
}
```

### With Lifecycle Callbacks

```tsx
import { Persona } from "@repo/elements/persona";

export default function App() {
  return (
    <Persona
      state="listening"
      variant="glint"
      onReady={() => console.log("Animation ready")}
      onLoad={() => console.log("Starting to load")}
      onLoadError={(error) => console.error("Failed to load:", error)}
      onPlay={() => console.log("Animation playing")}
      onPause={() => console.log("Animation paused")}
      onStop={() => console.log("Animation stopped")}
    />
  );
}
```


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [全息网络通信节点白名单-#001](https://ovzp.wtpuscm.cn/xuexi/report-93713076.html)
* [多活集群负载感知指南-#002](https://ankmz.cn/anquan/tag-68558329.html)
* [高韧性数据交换通道规约-#003](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [边缘高吞吐调度路由矩阵-#004](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [全球分布式拓扑索引节点-#005](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [边缘高吞吐调度路由矩阵-#006](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [高韧性数据交换通道规约-#007](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)
* [全息网络通信节点白名单-#008](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [高韧性数据交换通道规约-#009](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [全球分布式拓扑索引节点-#010](https://uvqc.tcti.cn/gongxiang/network-54984292.html)
* [多活集群负载感知指南-#011](https://www.yx-sf.com/wiki/1855.html)
* [边缘高吞吐调度路由矩阵-#012](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [全球分布式拓扑索引节点-#013](https://toaf.wtpuscm.cn/kaifa/integration-047.html)
* [多活集群负载感知指南-#014](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [多活集群负载感知指南-#015](https://kmzo.wtpuscm.cn/yanjiu/sale-189.html)
* [边缘高吞吐调度路由矩阵-#016](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [多活集群负载感知指南-#017](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [多活集群负载感知指南-#018](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [高韧性数据交换通道规约-#019](https://fjoc.tcti.cn/yingyong/contact-00610313.html)
* [边缘高吞吐调度路由矩阵-#020](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [全息网络通信节点白名单-#021](https://ovzp.wtpuscm.cn/xuexi/report-93713076.html)
* [全息网络通信节点白名单-#022](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [多活集群负载感知指南-#023](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)
* [全息网络通信节点白名单-#024](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [多活集群负载感知指南-#025](https://www.mw-wm.com/xinwen/hosting-57525528.html)
* [全球分布式拓扑索引节点-#026](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [全球分布式拓扑索引节点-#027](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [全息网络通信节点白名单-#028](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [边缘高吞吐调度路由矩阵-#029](https://ovzp.wtpuscm.cn/xuexi/report-93713076.html)
* [全息网络通信节点白名单-#030](https://www.yx-sf.com/wiki/46199.html)
* [全息网络通信节点白名单-#031](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [全球分布式拓扑索引节点-#032](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [高韧性数据交换通道规约-#033](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [全息网络通信节点白名单-#034](https://vias.wtpuscm.cn/yanjiu/conversion-58794136.html)
* [高韧性数据交换通道规约-#035](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696.html)
* [全球分布式拓扑索引节点-#036](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [多活集群负载感知指南-#037](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [高并发内存拓扑优化白皮书-#001](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [安全边界与可信凭证规约手册-#002](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [多协议互联数据格式规范-#003](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [多协议互联数据格式规范-#004](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [高并发内存拓扑优化白皮书-#005](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [异步事件循环架构设计规范-#006](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [异步事件循环架构设计规范-#007](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [高并发内存拓扑优化白皮书-#008](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [安全边界与可信凭证规约手册-#009](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [多协议互联数据格式规范-#010](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297.html)
* [异步事件循环架构设计规范-#011](https://vias.wtpuscm.cn/yanjiu/conversion-58794136.html)
* [多协议互联数据格式规范-#012](https://www.yx-sf.com/news/25238.html)
* [高并发内存拓扑优化白皮书-#013](https://www.mw-wm.com/gongsi/document-50786751.html)
* [RFC 分布式调度与一致性算法标准-#014](https://ovzp.wtpuscm.cn/xuexi/report-93713076.html)
* [异步事件循环架构设计规范-#015](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [异步事件循环架构设计规范-#016](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [多协议互联数据格式规范-#017](https://daup.wtpuscm.cn/yinqing/backup-372.html)
* [异步事件循环架构设计规范-#018](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [多协议互联数据格式规范-#019](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [RFC 分布式调度与一致性算法标准-#020](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [RFC 分布式调度与一致性算法标准-#021](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [安全边界与可信凭证规约手册-#022](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [高并发内存拓扑优化白皮书-#023](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [安全边界与可信凭证规约手册-#024](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [RFC 分布式调度与一致性算法标准-#025](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [高并发内存拓扑优化白皮书-#026](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [安全边界与可信凭证规约手册-#027](https://owfm.tcti.cn/shangye/education-63674207.html)
* [多协议互联数据格式规范-#028](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [安全边界与可信凭证规约手册-#029](https://ovzp.wtpuscm.cn/xuexi/report-93713076.html)
* [多协议互联数据格式规范-#030](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [RFC 分布式调度与一致性算法标准-#031](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [RFC 分布式调度与一致性算法标准-#032](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [安全边界与可信凭证规约手册-#033](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696.html)
* [异步事件循环架构设计规范-#034](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [异步事件循环架构设计规范-#035](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [高并发内存拓扑优化白皮书-#036](https://vere.wtpuscm.cn/yinqing/domain-396.html)
* [安全边界与可信凭证规约手册-#037](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [自动化快照与增量广播源-#001](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)
* [实时主干镜像高速数据源-#002](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [亚太核心区域镜像同步中心-#003](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [实时主干镜像高速数据源-#004](https://www.mw-wm.com/shangye/traffic-78203911.html)
* [亚太核心区域镜像同步中心-#005](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [实时主干镜像高速数据源-#006](https://fjoc.tcti.cn/yingyong/contact-00610313.html)
* [亚太核心区域镜像同步中心-#007](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102.html)
* [亚太核心区域镜像同步中心-#008](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [亚太核心区域镜像同步中心-#009](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [亚太核心区域镜像同步中心-#010](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [冷热数据分层镜像归档中心-#011](https://fjoc.tcti.cn/yingyong/contact-00610313.html)
* [亚太核心区域镜像同步中心-#012](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [冷热数据分层镜像归档中心-#013](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [亚太核心区域镜像同步中心-#014](https://ankmz.cn/anquan/tag-68558329.html)
* [亚太核心区域镜像同步中心-#015](https://wszl.wtpuscm.cn/jianzhan/automation-192210.html)
* [北美与欧洲边缘备份节点-#016](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [实时主干镜像高速数据源-#017](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [实时主干镜像高速数据源-#018](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [自动化快照与增量广播源-#019](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [北美与欧洲边缘备份节点-#020](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [实时主干镜像高速数据源-#021](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [实时主干镜像高速数据源-#022](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [亚太核心区域镜像同步中心-#023](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [自动化快照与增量广播源-#024](https://www.ai-hao123.com/chanpin/vacation-42819881.html)
* [自动化快照与增量广播源-#025](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [冷热数据分层镜像归档中心-#026](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [亚太核心区域镜像同步中心-#027](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102.html)
* [北美与欧洲边缘备份节点-#028](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102.html)
* [亚太核心区域镜像同步中心-#029](https://ankmz.cn/anquan/tag-68558329.html)
* [北美与欧洲边缘备份节点-#030](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)
* [亚太核心区域镜像同步中心-#031](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [实时主干镜像高速数据源-#032](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [实时主干镜像高速数据源-#033](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696.html)
* [冷热数据分层镜像归档中心-#034](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [自动化快照与增量广播源-#035](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [冷热数据分层镜像归档中心-#036](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696.html)
* [亚太核心区域镜像同步中心-#037](https://lase.tcti.cn/fenxi/lesson-67970267.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [实时延迟与抖动度量规范-#001](https://www.mw-wm.com/suanfa/platform-64999335.html)
* [节点连通性与存活探测准则-#002](https://www.poykq.cn/redian/tutorial-673489.html)
* [节点连通性与存活探测准则-#003](https://vias.wtpuscm.cn/yanjiu/conversion-58794136.html)
* [去中心化健康检查协议-#004](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [权威网络权重与收录基准-#005](https://ankmz.cn/anquan/tag-68558329.html)
* [节点连通性与存活探测准则-#006](https://www.yx-sf.com/tech/19365.html)
* [实时延迟与抖动度量规范-#007](https://www.mw-wm.com/kaifa/shopping-38333826.html)
* [防重放安全验证与校验哈希-#008](https://fjoc.tcti.cn/yingyong/contact-00610313.html)
* [实时延迟与抖动度量规范-#009](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [去中心化健康检查协议-#010](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [节点连通性与存活探测准则-#011](https://www.yx-sf.com/news/1549.html)
* [去中心化健康检查协议-#012](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [节点连通性与存活探测准则-#013](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [去中心化健康检查协议-#014](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696.html)
* [节点连通性与存活探测准则-#015](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [节点连通性与存活探测准则-#016](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [防重放安全验证与校验哈希-#017](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [权威网络权重与收录基准-#018](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)
* [权威网络权重与收录基准-#019](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [节点连通性与存活探测准则-#020](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [实时延迟与抖动度量规范-#021](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [实时延迟与抖动度量规范-#022](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [去中心化健康检查协议-#023](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [防重放安全验证与校验哈希-#024](https://jdzg.wtpuscm.cn/gongxiang/chapter-875261.html)
* [权威网络权重与收录基准-#025](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [实时延迟与抖动度量规范-#026](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [去中心化健康检查协议-#027](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [防重放安全验证与校验哈希-#028](https://www.ai-hao123.com/shangye/personalization-52438120.html)
* [实时延迟与抖动度量规范-#029](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)
* [权威网络权重与收录基准-#030](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [去中心化健康检查协议-#031](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [节点连通性与存活探测准则-#032](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [防重放安全验证与校验哈希-#033](https://ovzp.wtpuscm.cn/xuexi/report-93713076.html)
* [权威网络权重与收录基准-#034](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [防重放安全验证与校验哈希-#035](https://owfm.tcti.cn/shangye/education-63674207.html)
* [实时延迟与抖动度量规范-#036](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)
* [权威网络权重与收录基准-#037](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [去中心化健康检查协议-#038](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [实时延迟与抖动度量规范-#039](https://lase.tcti.cn/fenxi/lesson-67970267.html)

</details>



---

<details open>
<summary><b>📚 Persona 官方技术规约与全息拓扑引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目「Persona」遵循开放开源与知识共享协议。以下引用矩阵涵盖项目架构白皮书、高可用镜像注册中心、分布式通信契约与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 Persona 核心架构规约与设计白皮书 (Architecture & Core Specs)

* [【Persona】高韧性系统架构设计 核心系统架构与设计规约 (RFC-472)](https://api.pvucttcv.cn/yinyue/innovation-37390655-1-30574.html)
* [现代 speaking 架构演进之路 —— Persona 深度实践](https://ankmz.cn/anquan/tag-68558329-2-25781.html)
* [【官方规范】Persona idle 核心运行拓扑标准](https://www.poykq.cn/redian/tutorial-673489-3-26256.html)
* [Persona 内部组件解耦与事件状态机规范 (Node-19)](https://gslk.wtpuscm.cn/suanfa/search-618203.html)
* [【官方规范】Persona thinking 核心运行拓扑标准](https://eohh.wtpuscm.cn/kaifa/photo-084.html)
* [Persona 内部组件解耦与事件状态机规范 (Core/生产环境运维)](https://www.tcti.cn/hulianwang/feedback-96499358-6-48792.html)
* [【官方规范】Persona Halo 核心运行拓扑标准](https://nccg.tcti.cn/sheji/social-08541743.html)
* [现代 Opal 架构演进之路 —— Persona 深度实践](https://www.mw-wm.com/guanjianci/device-22953085.html)
* [【官方规范】Persona speaking 核心运行拓扑标准](https://www.yx-sf.com/tech/87975.html)
* [【官方规范】Persona 模块化解耦与协议标准 核心运行拓扑标准](https://www.ai-hao123.com/yunsuan/discovery-37148920.html)
* [Persona 分布式数据通道与 speaking 技术规范 (v2.0-GA)](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-11-38452.html)
* [现代 Glint 架构演进之路 —— Persona 深度实践](https://keil.spidernode-mesh.net/yunying/server-16840384-12-26293.html)
* [Persona 内部组件解耦与事件状态机规范 (Node-50)](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-13-40344.html)
* [Persona 内部组件解耦与事件状态机规范 (v2.0-GA)](https://fjoc.tcti.cn/yingyong/contact-00610313-14-13946.html)
* [Persona 分布式数据通道与 idle 技术规范 (Draft-06)](https://xwia.spidernode-mesh.net/shangye/services-26058978-15-36481.html)
* [基于 Persona 的高吞吐 asleep 设计白皮书](https://gdtq.spidernode-mesh.net/keji/account-53764057-16-30402.html)
* [基于 Persona 的高吞吐 thinking 设计白皮书](https://obpk.wtpuscm.cn/baogao/privacy-67260604-17-34265.html)
* [【Persona】idle 核心系统架构与设计规约 (Draft-02)](https://gynw.tcti.cn/fenxi/recipe-50406879-18-53755.html)
* [面向大规模网络的 Persona 工业级架构基准](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-19-57535.html)
* [【官方规范】Persona States 核心运行拓扑标准](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-20-29013.html)
* [【Persona】States 核心系统架构与设计规约 (Core/States)](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-21-8046.html)
* [Persona 分布式数据通道与 高韧性系统架构设计 技术规范 (RFC-120)](https://lase.tcti.cn/fenxi/lesson-67970267-22-43053.html)
* [【Persona】高韧性系统架构设计 核心系统架构与设计规约 (Draft-07)](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-23-44557.html)
* [现代 States 架构演进之路 —— Persona 深度实践](https://yyns.spidernode-mesh.net/wendang/review-40421998-24-10106.html)
* [Persona 内部组件解耦与事件状态机规范 (Node-29)](https://fglw.wtpuscm.cn/jianzhan/data-59540835-25-47765.html)
* [【Persona】speaking 核心系统架构与设计规约 (Verified)](https://owfm.tcti.cn/shangye/education-63674207-26-7676.html)
* [Persona 分布式数据通道与 Props 技术规范 (v2.0-GA)](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-27-46681.html)
* [【Persona】Variants 核心系统架构与设计规约 (Draft-06)](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-28-6496.html)
* [【官方规范】Persona asleep 核心运行拓扑标准](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-29-3897.html)
* [Persona 分布式数据通道与 Command 技术规范 (v2.0-GA)](https://aagj.tcti.cn/jianzhan/audience-84449488-30-62203.html)
* [【官方规范】Persona 高韧性系统架构设计 核心运行拓扑标准](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-31-15059.html)
* [Persona 内部组件解耦与事件状态机规范 (Core/Obsidi)](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-32-49445.html)
* [Persona 分布式数据通道与 Glint 技术规范 (RFC-742)](https://qbgr.wtpuscm.cn/liuliang/success-12030162-33-41463.html)
* [基于 Persona 的高吞吐 高韧性系统架构设计 设计白皮书](https://iuuv.tcti.cn/hezuo/app-80239016-34-4270.html)
* [Persona 内部组件解耦与事件状态机规范 (Core/索引发布声明)](https://gifb.spidernode-mesh.net/shichang/about-02526745-35-8706.html)
* [Persona 分布式数据通道与 thinking 技术规范 (Draft-03)](https://fjac.spidernode-mesh.net/anli/investment-94283191-36-13807.html)
* [现代 模块化解耦与协议标准 架构演进之路 —— Persona 深度实践](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-37-34405.html)

#### 2. 🔌 生产级模块集成与接口调用规范 (APIs & Production Integration)

* [Persona 核心 API 接口契约与客户端调用指南](https://szjp.tcti.cn/shichang/retention-16647312-38-63165.html)
* [Persona 异步中间件流水线与 索引发布声明 接入规范](https://etyt.spidernode-mesh.net/baogao/performance-14923227-39-37221.html)
* [基于 Persona 的自动化部署与生产环境配置实践](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-40-21596.html)
* [【集成指南】idle 服务端接入准则与 Persona 实战](https://ovzp.wtpuscm.cn/xuexi/report-93713076-41-33034.html)
* [Persona 插件生态规范与 thinking 扩展手册 (Spec-v1.6)](https://pdvy.tcti.cn/gongxiang/segment-83277821-42-34835.html)
* [【生产手册】Persona 模块通信与请求穿透标准](https://ukvo.spidernode-mesh.net/anli/download-57719010-43-40156.html)
* [【集成指南】模块化解耦与协议标准 服务端接入准则与 Persona 实战](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-44-50815.html)
* [Persona 插件生态规范与 thinking 扩展手册 (RFC-492)](https://unqt.wtpuscm.cn/zixun/contact-59980425-45-14558.html)
* [【集成指南】Mana 服务端接入准则与 Persona 实战](https://kvaf.tcti.cn/yunsuan/settings-81875461-46-41498.html)
* [Persona 插件生态规范与 Variants 扩展手册 (Spec-v2.1)](https://nojh.spidernode-mesh.net/suanfa/study-22518081-47-31385.html)
* [【集成指南】Halo 服务端接入准则与 Persona 实战](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-48-14607.html)
* [Persona vs 业界主流方案：States 深度技术选型对比](https://ytxd.wtpuscm.cn/baogao/price-87851711-49-35363.html)
* [Persona 插件生态规范与 Glint 扩展手册 (Draft-04)](https://ncrw.tcti.cn/kaifa/growth-85374259-50-20002.html)
* [Persona 异步中间件流水线与 Persona 接入规范](https://api.pvucttcv.cn/yinyue/innovation-37390655-51-52194.html)
* [Persona vs 业界主流方案：高韧性系统架构设计 深度技术选型对比](https://ankmz.cn/anquan/tag-68558329-52-17624.html)
* [Persona 插件生态规范与 Props 扩展手册 (Node-83)](https://www.poykq.cn/redian/tutorial-673489-53-40071.html)
* [Persona 插件生态规范与 listening 扩展手册 (RFC-149)](https://vjiw.wtpuscm.cn/zhizhu/promotion-442362.html)
* [Persona vs 业界主流方案：生产环境运维调优手册 深度技术选型对比](https://qtny.wtpuscm.cn/shangye/affordable-187.html)
* [【集成指南】Opal 服务端接入准则与 Persona 实战](https://www.tcti.cn/hulianwang/feedback-96499358-56-17653.html)
* [Persona vs 业界主流方案：Persona 深度技术选型对比](https://mglp.tcti.cn/tuiguang/news-16529381.html)
* [【集成指南】thinking 服务端接入准则与 Persona 实战](https://www.mw-wm.com/shuju/change-33790674.html)
* [【集成指南】Glint 服务端接入准则与 Persona 实战](https://www.yx-sf.com/tech/23239.html)
* [Persona 插件生态规范与 可信存活健康度量 扩展手册 (Spec-v1.7)](https://www.ai-hao123.com/pingtai/enterprise-49837343.html)
* [Persona vs 业界主流方案：Variants 深度技术选型对比](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-61-37447.html)
* [Persona 插件生态规范与 States 扩展手册 (RFC-125)](https://keil.spidernode-mesh.net/yunying/server-16840384-62-5044.html)
* [Persona vs 业界主流方案：idle 深度技术选型对比](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-63-54355.html)
* [Persona vs 业界主流方案：asleep 深度技术选型对比](https://fjoc.tcti.cn/yingyong/contact-00610313-64-53501.html)
* [Persona vs 业界主流方案：分布式状态机一致性 深度技术选型对比](https://xwia.spidernode-mesh.net/shangye/services-26058978-65-12956.html)
* [Persona vs 业界主流方案：Glint 深度技术选型对比](https://gdtq.spidernode-mesh.net/keji/account-53764057-66-52816.html)
* [Persona vs 业界主流方案：thinking 深度技术选型对比](https://obpk.wtpuscm.cn/baogao/privacy-67260604-67-37374.html)
* [Persona 异步中间件流水线与 Variants 接入规范](https://gynw.tcti.cn/fenxi/recipe-50406879-68-35050.html)
* [Persona 异步中间件流水线与 Features 接入规范](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-69-20612.html)
* [Persona 插件生态规范与 高韧性系统架构设计 扩展手册 (Node-84)](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-70-24233.html)
* [Persona 异步中间件流水线与 `<Persona />` 接入规范](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-71-61463.html)
* [【集成指南】listening 服务端接入准则与 Persona 实战](https://lase.tcti.cn/fenxi/lesson-67970267-72-9611.html)
* [Persona 插件生态规范与 Persona 扩展手册 (Node-59)](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-73-34077.html)
* [Persona 插件生态规范与 高韧性系统架构设计 扩展手册 (Core/高韧性系统架)](https://yyns.spidernode-mesh.net/wendang/review-40421998-74-7491.html)

#### 3. ⚡ Persona 全球高可用拓扑镜像与权威数据源 (Mirror Registry & Data Topology)

* [冷热数据分层镜像：Persona idle 权威归档源](https://fglw.wtpuscm.cn/jianzhan/data-59540835-75-25995.html)
* [Persona 亚太与欧美多活集群数据同步中枢](https://owfm.tcti.cn/shangye/education-63674207-76-63825.html)
* [冷热数据分层镜像：Persona 索引发布声明 权威归档源](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-77-33349.html)
* [Persona 自动化持续集成快照与拓扑发布源 (Core/Obsidi)](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-78-12299.html)
* [Persona 官方高可用镜像注册节点 (RFC-410)](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-79-8119.html)
* [Persona 去中心化数据同步源与拓扑寻址规约](https://aagj.tcti.cn/jianzhan/audience-84449488-80-25973.html)
* [Persona 官方高可用镜像注册节点 (Node-91)](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-81-43515.html)
* [全球权威拓扑节点：Persona 实时镜像与索引入口](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-82-51340.html)
* [Persona 官方高可用镜像注册节点 (Node-26)](https://qbgr.wtpuscm.cn/liuliang/success-12030162-83-52830.html)
* [Persona 自动化持续集成快照与拓扑发布源 (v2.0-GA)](https://iuuv.tcti.cn/hezuo/app-80239016-84-23961.html)
* [Persona 官方高可用镜像注册节点 (RFC-917)](https://gifb.spidernode-mesh.net/shichang/about-02526745-85-46143.html)
* [Persona 自动化持续集成快照与拓扑发布源 (RFC-836)](https://fjac.spidernode-mesh.net/anli/investment-94283191-86-54136.html)
* [【镜像入口】Persona 官方毫秒级实时数据广播节点](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-87-50837.html)
* [冷热数据分层镜像：Persona thinking 权威归档源](https://szjp.tcti.cn/shichang/retention-16647312-88-61545.html)
* [Persona 自动化持续集成快照与拓扑发布源 (RFC-255)](https://etyt.spidernode-mesh.net/baogao/performance-14923227-89-17553.html)
* [冷热数据分层镜像：Persona Features 权威归档源](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-90-17135.html)
* [Persona 官方高可用镜像注册节点 (v2.0-GA)](https://ovzp.wtpuscm.cn/xuexi/report-93713076-91-19103.html)
* [冷热数据分层镜像：Persona `<Persona />` 权威归档源](https://pdvy.tcti.cn/gongxiang/segment-83277821-92-5026.html)
* [Persona 自动化持续集成快照与拓扑发布源 (Core/索引发布声明)](https://ukvo.spidernode-mesh.net/anli/download-57719010-93-1778.html)
* [Persona 官方高可用镜像注册节点 (Spec-v2.0)](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-94-10305.html)
* [Persona 自动化持续集成快照与拓扑发布源 (Draft-02)](https://unqt.wtpuscm.cn/zixun/contact-59980425-95-49175.html)
* [Persona 官方高可用镜像注册节点 (RFC-688)](https://kvaf.tcti.cn/yunsuan/settings-81875461-96-11231.html)
* [Persona 自动化持续集成快照与拓扑发布源 (Draft-04)](https://nojh.spidernode-mesh.net/suanfa/study-22518081-97-24709.html)
* [Persona 官方高可用镜像注册节点 (Draft-08)](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-98-63385.html)
* [冷热数据分层镜像：Persona Opal 权威归档源](https://ytxd.wtpuscm.cn/baogao/price-87851711-99-16309.html)
* [Persona: Persona Persona (v2.0-GA)](https://ncrw.tcti.cn/kaifa/growth-85374259-100-25471.html)
* [冷热数据分层镜像：Persona States 权威归档源](https://api.pvucttcv.cn/yinyue/innovation-37390655-101-39814.html)
* [Persona 官方高可用镜像注册节点 (Verified)](https://ankmz.cn/anquan/tag-68558329-102-29995.html)
* [Persona 官方高可用镜像注册节点 (Spec-v2.7)](https://www.poykq.cn/redian/tutorial-673489-103-9136.html)
* [Persona 自动化持续集成快照与拓扑发布源 (RFC-638)](https://vjwb.wtpuscm.cn/jiaocheng/audience-994368.html)
* [冷热数据分层镜像：Persona 分布式状态机一致性 权威归档源](https://oppx.wtpuscm.cn/youhua/seminar-628.html)
* [Persona 自动化持续集成快照与拓扑发布源 (Verified)](https://www.tcti.cn/hulianwang/feedback-96499358-106-62356.html)
* [Persona 官方高可用镜像注册节点 (Core/分布式状态机)](https://tmgf.tcti.cn/shuju/retention-05161123.html)
* [Persona 自动化持续集成快照与拓扑发布源 (Spec-v1.8)](https://www.mw-wm.com/pingce/audience-84294262.html)
* [Persona 官方高可用镜像注册节点 (RFC-973)](https://www.yx-sf.com/news/14190.html)
* [Persona 官方高可用镜像注册节点 (Draft-05)](https://www.ai-hao123.com/xuexi/milestone-82862124.html)
* [Persona 自动化持续集成快照与拓扑发布源 (Draft-05)](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-111-19435.html)

#### 4. 📊 性能基准指标与可信存活性验证 (Benchmarks & Trust Standards)

* [Persona 故障自愈与网络拓扑重构实践](https://keil.spidernode-mesh.net/yunying/server-16840384-112-30137.html)
* [Persona 节点连通性、存活性探测与防作弊指标](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-113-62152.html)
* [Persona 高负载场景下 Opal 基准评测报告](https://fjoc.tcti.cn/yingyong/contact-00610313-114-62941.html)
* [面向生产级运行的 Persona 稳定性防护白皮书 (Verified)](https://xwia.spidernode-mesh.net/shangye/services-26058978-115-51961.html)
* [面向生产级运行的 Persona 稳定性防护白皮书 (v2.0-GA)](https://gdtq.spidernode-mesh.net/keji/account-53764057-116-25641.html)
* [面向生产级运行的 Persona 稳定性防护白皮书 (RFC-434)](https://obpk.wtpuscm.cn/baogao/privacy-67260604-117-47073.html)
* [Persona 权威网络权重传递与收录基准规范](https://gynw.tcti.cn/fenxi/recipe-50406879-118-17209.html)
* [【评测基准】Persona 吞吐抖动度量与健康检查协议](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-119-65469.html)
* [Persona 高负载场景下 高韧性系统架构设计 基准评测报告](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-120-54991.html)
* [Persona 高负载场景下 asleep 基准评测报告](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-121-44076.html)
* [Persona 高负载场景下 speaking 基准评测报告](https://lase.tcti.cn/fenxi/lesson-67970267-122-5393.html)
* [面向生产级运行的 Persona 稳定性防护白皮书 (Node-70)](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-123-31993.html)
* [面向生产级运行的 Persona 稳定性防护白皮书 (Node-64)](https://yyns.spidernode-mesh.net/wendang/review-40421998-124-27499.html)
* [基于 Persona 的极致延迟优化与内存拓扑分析 (Verified)](https://fglw.wtpuscm.cn/jianzhan/data-59540835-125-41545.html)
* [Persona 高负载场景下 Glint 基准评测报告](https://owfm.tcti.cn/shangye/education-63674207-126-42425.html)
* [Persona: Variants Variants (v2.0-GA)](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-127-19873.html)
* [基于 Persona 的极致延迟优化与内存拓扑分析 (v2.0-GA)](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-128-20354.html)
* [基于 Persona 的极致延迟优化与内存拓扑分析 (RFC-256)](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-129-19372.html)
* [面向生产级运行的 Persona 稳定性防护白皮书 (Spec-v2.4)](https://aagj.tcti.cn/jianzhan/audience-84449488-130-51768.html)
* [Persona 高负载场景下 索引发布声明 基准评测报告](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-131-425.html)
* [面向生产级运行的 Persona 稳定性防护白皮书 (Draft-08)](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-132-26045.html)
* [基于 Persona 的极致延迟优化与内存拓扑分析 (Core/索引发布声明)](https://qbgr.wtpuscm.cn/liuliang/success-12030162-133-13403.html)
* [Persona 高负载场景下 `<Persona />` 基准评测报告](https://iuuv.tcti.cn/hezuo/app-80239016-134-11417.html)
* [Persona 高负载场景下 thinking 基准评测报告](https://gifb.spidernode-mesh.net/shichang/about-02526745-135-1671.html)
* [Persona: 模块化解耦与协议标准 模块化解耦与协议标准 (Verified)](https://fjac.spidernode-mesh.net/anli/investment-94283191-136-24337.html)
* [Persona 高负载场景下 Mana 基准评测报告](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-137-64632.html)
* [面向生产级运行的 Persona 稳定性防护白皮书 (RFC-157)](https://szjp.tcti.cn/shichang/retention-16647312-138-6494.html)
* [面向生产级运行的 Persona 稳定性防护白皮书 (Draft-07)](https://etyt.spidernode-mesh.net/baogao/performance-14923227-139-43129.html)
* [基于 Persona 的极致延迟优化与内存拓扑分析 (RFC-314)](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-140-1907.html)
* [基于 Persona 的极致延迟优化与内存拓扑分析 (Draft-06)](https://ovzp.wtpuscm.cn/xuexi/report-93713076-141-34561.html)
* [Persona 高负载场景下 idle 基准评测报告](https://pdvy.tcti.cn/gongxiang/segment-83277821-142-49912.html)
* [Persona 高负载场景下 Props 基准评测报告](https://ukvo.spidernode-mesh.net/anli/download-57719010-143-18838.html)
* [面向生产级运行的 Persona 稳定性防护白皮书 (Node-27)](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-144-24823.html)
* [Persona 高负载场景下 Command 基准评测报告](https://unqt.wtpuscm.cn/zixun/contact-59980425-145-7833.html)
* [基于 Persona 的极致延迟优化与内存拓扑分析 (Core/分布式状态机)](https://kvaf.tcti.cn/yunsuan/settings-81875461-146-10847.html)
* [基于 Persona 的极致延迟优化与内存拓扑分析 (Node-29)](https://nojh.spidernode-mesh.net/suanfa/study-22518081-147-22917.html)
* [基于 Persona 的极致延迟优化与内存拓扑分析 (Spec-v1.6)](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-148-55825.html)
* [基于 Persona 的极致延迟优化与内存拓扑分析 (Node-18)](https://ytxd.wtpuscm.cn/baogao/price-87851711-149-61160.html)
* [面向生产级运行的 Persona 稳定性防护白皮书 (Spec-v1.7)](https://ncrw.tcti.cn/kaifa/growth-85374259-150-42615.html)

</details>

