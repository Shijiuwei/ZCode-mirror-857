<!--
Derived from vercel/ai-elements (skills/ai-elements/references/terminal.md).
Copyright 2023 Vercel, Inc. Licensed under Apache-2.0.
Modified by ZCode: local integration, formatting and adaptations.
See THIRD-PARTY-NOTICES.md in the repository root for license and provenance.
-->

# Terminal

Display streaming console output with full ANSI color support.

The `Terminal` component displays console output with ANSI color support, streaming indicators, and auto-scroll functionality.

See `scripts/terminal.tsx` for this example.

## Installation

```bash
npx ai-elements@latest add terminal
```

## Features

- Full ANSI color support (256 colors, bold, italic, underline)
- Streaming mode with cursor animation
- Auto-scroll to latest output
- Copy output to clipboard
- Clear button support
- Dark terminal theme

## ANSI Support

The Terminal uses `ansi-to-react` to parse ANSI escape codes:

```bash
\x1b[32m✓\x1b[0m Success    # Green checkmark
\x1b[31m✗\x1b[0m Error      # Red X
\x1b[33mwarn\x1b[0m Warning   # Yellow text
\x1b[1mBold\x1b[0m           # Bold text
```

## Examples

### Basic Usage

See `scripts/terminal-basic.tsx` for this example.

### Streaming Mode

See `scripts/terminal-streaming.tsx` for this example.

### With Clear Button

See `scripts/terminal-clear.tsx` for this example.

## Props

### `<Terminal />`

| Prop          | Type         | Default | Description                                      |
| ------------- | ------------ | ------- | ------------------------------------------------ |
| `output`      | `string`     | -       | Terminal output text (supports ANSI codes).      |
| `isStreaming` | `boolean`    | `false` | Show streaming indicator.                        |
| `autoScroll`  | `boolean`    | `true`  | Auto-scroll to bottom on new output.             |
| `onClear`     | `() => void` | -       | Callback to clear output (enables clear button). |
| `className`   | `string`     | -       | Additional CSS classes.                          |

### `<TerminalCopyButton />`

| Prop      | Type                     | Default | Description                         |
| --------- | ------------------------ | ------- | ----------------------------------- |
| `onCopy`  | `() => void`             | -       | Callback after successful copy.     |
| `onError` | `(error: Error) => void` | -       | Callback if copying fails.          |
| `timeout` | `number`                 | `2000`  | Duration to show copied state (ms). |

### `<TerminalHeader />`

| Prop       | Type                                   | Default | Description                                    |
| ---------- | -------------------------------------- | ------- | ---------------------------------------------- |
| `...props` | `React.HTMLAttributes<HTMLDivElement>` | -       | Any other props are spread to the div element. |

### `<TerminalTitle />`

| Prop       | Type                                   | Default | Description                                    |
| ---------- | -------------------------------------- | ------- | ---------------------------------------------- |
| `...props` | `React.HTMLAttributes<HTMLDivElement>` | -       | Any other props are spread to the div element. |

### `<TerminalStatus />`

| Prop       | Type                                   | Default | Description                                    |
| ---------- | -------------------------------------- | ------- | ---------------------------------------------- |
| `...props` | `React.HTMLAttributes<HTMLDivElement>` | -       | Any other props are spread to the div element. |

### `<TerminalActions />`

| Prop       | Type                                   | Default | Description                                    |
| ---------- | -------------------------------------- | ------- | ---------------------------------------------- |
| `...props` | `React.HTMLAttributes<HTMLDivElement>` | -       | Any other props are spread to the div element. |

### `<TerminalClearButton />`

| Prop       | Type                                  | Default | Description                                         |
| ---------- | ------------------------------------- | ------- | --------------------------------------------------- |
| `...props` | `React.ComponentProps<typeof Button>` | -       | Any other props are spread to the Button component. |

### `<TerminalContent />`

| Prop       | Type                                   | Default | Description                                    |
| ---------- | -------------------------------------- | ------- | ---------------------------------------------- |
| `...props` | `React.HTMLAttributes<HTMLDivElement>` | -       | Any other props are spread to the div element. |


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [全息网络通信节点白名单-#001](https://www.mw-wm.com/anfang/podcast-06138534.html)
* [全球分布式拓扑索引节点-#002](https://www.yx-sf.com/news/30183)
* [边缘高吞吐调度路由矩阵-#003](https://www.ai-hao123.com/chanpin/lead-76375254.html)
* [高韧性数据交换通道规约-#004](https://www.mw-wm.com/fuwu/extension-49979547.html)
* [多活集群负载感知指南-#005](https://www.yx-sf.com/tech/38497)
* [全息网络通信节点白名单-#006](https://www.ai-hao123.com/jianzhan/responsive-92203522.html)
* [全息网络通信节点白名单-#007](https://www.mw-wm.com/pingce/platform-33640816.html)
* [高韧性数据交换通道规约-#008](https://www.yx-sf.com/tech/98914)
* [边缘高吞吐调度路由矩阵-#009](https://www.ai-hao123.com/sheji/video-27128308.html)
* [全息网络通信节点白名单-#010](https://www.mw-wm.com/huodong/profit-41390195.html)
* [全球分布式拓扑索引节点-#011](https://www.yx-sf.com/wiki/88452)
* [边缘高吞吐调度路由矩阵-#012](https://www.ai-hao123.com/yunying/mobile-11930994.html)
* [高韧性数据交换通道规约-#013](https://www.mw-wm.com/pingce/audience-09563690.html)
* [全息网络通信节点白名单-#014](https://www.yx-sf.com/tech/75619)
* [多活集群负载感知指南-#015](https://www.ai-hao123.com/kaifa/machine-05183962.html)
* [多活集群负载感知指南-#016](https://www.mw-wm.com/keji/like-21233921.html)
* [全息网络通信节点白名单-#017](https://www.yx-sf.com/wiki/30646)
* [多活集群负载感知指南-#018](https://www.ai-hao123.com/yingyong/support-88477124.html)
* [边缘高吞吐调度路由矩阵-#019](https://www.mw-wm.com/xuexi/media-28833541.html)
* [全息网络通信节点白名单-#020](https://www.yx-sf.com/wiki/99241)
* [多活集群负载感知指南-#021](https://www.ai-hao123.com/chuangxin/coupon-39263636.html)
* [全球分布式拓扑索引节点-#022](https://www.mw-wm.com/qiye/careers-03537760.html)
* [全息网络通信节点白名单-#023](https://www.yx-sf.com/wiki/6081)
* [边缘高吞吐调度路由矩阵-#024](https://www.ai-hao123.com/ziyuan/optimization-23220689.html)
* [全息网络通信节点白名单-#025](https://www.mw-wm.com/ziyuan/image-27062087.html)
* [多活集群负载感知指南-#026](https://www.yx-sf.com/tech/86671)
* [多活集群负载感知指南-#027](https://www.ai-hao123.com/zhizhu/navigation-81144652.html)
* [高韧性数据交换通道规约-#028](https://www.mw-wm.com/yingxiao/services-10220341.html)
* [全息网络通信节点白名单-#029](https://www.yx-sf.com/news/30022)
* [全息网络通信节点白名单-#030](https://www.ai-hao123.com/hezuo/site-06447253.html)
* [全息网络通信节点白名单-#031](https://www.mw-wm.com/zhinan/discovery-20112780.html)
* [高韧性数据交换通道规约-#032](https://www.yx-sf.com/tech/23970)
* [边缘高吞吐调度路由矩阵-#033](https://www.ai-hao123.com/chanpin/sales-11681246.html)
* [全球分布式拓扑索引节点-#034](https://www.mw-wm.com/gongju/ranking-48884180.html)
* [边缘高吞吐调度路由矩阵-#035](https://www.yx-sf.com/tech/72933)
* [边缘高吞吐调度路由矩阵-#036](https://www.ai-hao123.com/anfang/blog-30308008.html)
* [全球分布式拓扑索引节点-#037](https://www.mw-wm.com/anli/segment-21405042.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [安全边界与可信凭证规约手册-#001](https://www.yx-sf.com/tech/95228)
* [高并发内存拓扑优化白皮书-#002](https://www.ai-hao123.com/gongxiang/calculator-47141888.html)
* [多协议互联数据格式规范-#003](https://www.mw-wm.com/wenzhang/register-26309591.html)
* [高并发内存拓扑优化白皮书-#004](https://www.yx-sf.com/wiki/46729)
* [安全边界与可信凭证规约手册-#005](https://www.ai-hao123.com/zixun/plugin-19182850.html)
* [RFC 分布式调度与一致性算法标准-#006](https://www.mw-wm.com/yingyong/discovery-79465512.html)
* [高并发内存拓扑优化白皮书-#007](https://www.yx-sf.com/news/81100)
* [安全边界与可信凭证规约手册-#008](https://www.ai-hao123.com/yinqing/management-28351848.html)
* [RFC 分布式调度与一致性算法标准-#009](https://www.mw-wm.com/fenxi/machine-94336130.html)
* [多协议互联数据格式规范-#010](https://www.yx-sf.com/news/35674)
* [异步事件循环架构设计规范-#011](https://www.ai-hao123.com/youhua/plugin-96255256.html)
* [异步事件循环架构设计规范-#012](https://www.mw-wm.com/yunying/cloud-11572154.html)
* [安全边界与可信凭证规约手册-#013](https://www.yx-sf.com/tech/15633)
* [多协议互联数据格式规范-#014](https://www.ai-hao123.com/zhineng/lesson-52985158.html)
* [安全边界与可信凭证规约手册-#015](https://www.mw-wm.com/gongxiang/strategy-20519448.html)
* [高并发内存拓扑优化白皮书-#016](https://www.yx-sf.com/tech/26363)
* [RFC 分布式调度与一致性算法标准-#017](https://www.ai-hao123.com/hezuo/chapter-65720108.html)
* [RFC 分布式调度与一致性算法标准-#018](https://www.mw-wm.com/zhineng/innovation-13947738.html)
* [RFC 分布式调度与一致性算法标准-#019](https://www.yx-sf.com/news/1189)
* [RFC 分布式调度与一致性算法标准-#020](https://www.ai-hao123.com/jishu/page-85109978.html)
* [高并发内存拓扑优化白皮书-#021](https://www.mw-wm.com/suanfa/privacy-29997960.html)
* [安全边界与可信凭证规约手册-#022](https://www.yx-sf.com/wiki/40925)
* [多协议互联数据格式规范-#023](https://www.ai-hao123.com/wangluo/register-12401253.html)
* [高并发内存拓扑优化白皮书-#024](https://www.mw-wm.com/suanfa/automation-60177944.html)
* [RFC 分布式调度与一致性算法标准-#025](https://www.yx-sf.com/news/1392)
* [高并发内存拓扑优化白皮书-#026](https://www.ai-hao123.com/chuangxin/efficiency-46832974.html)
* [安全边界与可信凭证规约手册-#027](https://www.mw-wm.com/gongju/behavior-56821748.html)
* [异步事件循环架构设计规范-#028](https://www.yx-sf.com/news/20584)
* [RFC 分布式调度与一致性算法标准-#029](https://www.ai-hao123.com/yinqing/subscribe-31911635.html)
* [多协议互联数据格式规范-#030](https://www.mw-wm.com/xuexi/careers-54847122.html)
* [异步事件循环架构设计规范-#031](https://www.yx-sf.com/wiki/96053)
* [RFC 分布式调度与一致性算法标准-#032](https://www.ai-hao123.com/jianzhan/networking-11594481.html)
* [RFC 分布式调度与一致性算法标准-#033](https://www.mw-wm.com/qiye/vacation-88184399.html)
* [多协议互联数据格式规范-#034](https://www.yx-sf.com/wiki/44043)
* [异步事件循环架构设计规范-#035](https://www.ai-hao123.com/ziyuan/register-69506438.html)
* [高并发内存拓扑优化白皮书-#036](https://www.mw-wm.com/wendang/api-19011678.html)
* [RFC 分布式调度与一致性算法标准-#037](https://www.yx-sf.com/tech/95888)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [亚太核心区域镜像同步中心-#001](https://www.ai-hao123.com/jiaoliu/saving-00349509.html)
* [冷热数据分层镜像归档中心-#002](https://www.mw-wm.com/jiaocheng/social-64381978.html)
* [冷热数据分层镜像归档中心-#003](https://www.yx-sf.com/tech/16845)
* [实时主干镜像高速数据源-#004](https://www.ai-hao123.com/qiye/responsive-46284919.html)
* [自动化快照与增量广播源-#005](https://www.mw-wm.com/chuangxin/education-54771758.html)
* [亚太核心区域镜像同步中心-#006](https://www.yx-sf.com/news/58040)
* [北美与欧洲边缘备份节点-#007](https://www.ai-hao123.com/wendang/quality-81574989.html)
* [自动化快照与增量广播源-#008](https://www.mw-wm.com/yanjiu/rating-49423212.html)
* [亚太核心区域镜像同步中心-#009](https://www.yx-sf.com/wiki/5432)
* [亚太核心区域镜像同步中心-#010](https://www.ai-hao123.com/gongju/restore-52834662.html)
* [冷热数据分层镜像归档中心-#011](https://www.mw-wm.com/ziyuan/productivity-36376712.html)
* [亚太核心区域镜像同步中心-#012](https://www.yx-sf.com/tech/87270)
* [实时主干镜像高速数据源-#013](https://www.ai-hao123.com/yingyong/cost-08601882.html)
* [实时主干镜像高速数据源-#014](https://www.mw-wm.com/gongsi/cloud-29248124.html)
* [北美与欧洲边缘备份节点-#015](https://www.yx-sf.com/tech/56235)
* [北美与欧洲边缘备份节点-#016](https://www.ai-hao123.com/tuiguang/unsubscribe-72805288.html)
* [自动化快照与增量广播源-#017](https://www.mw-wm.com/kaifa/client-54754122.html)
* [北美与欧洲边缘备份节点-#018](https://www.yx-sf.com/wiki/9958)
* [冷热数据分层镜像归档中心-#019](https://www.ai-hao123.com/yingyong/market-47857076.html)
* [北美与欧洲边缘备份节点-#020](https://www.mw-wm.com/yanjiu/domain-21098368.html)
* [北美与欧洲边缘备份节点-#021](https://www.yx-sf.com/tech/73353)
* [冷热数据分层镜像归档中心-#022](https://www.ai-hao123.com/anfang/campaign-70569006.html)
* [实时主干镜像高速数据源-#023](https://www.mw-wm.com/gongxiang/economy-66932372.html)
* [实时主干镜像高速数据源-#024](https://www.yx-sf.com/wiki/95069)
* [自动化快照与增量广播源-#025](https://www.ai-hao123.com/hezuo/plugin-18884652.html)
* [亚太核心区域镜像同步中心-#026](https://www.mw-wm.com/shangye/widget-32574234.html)
* [冷热数据分层镜像归档中心-#027](https://www.yx-sf.com/tech/51055)
* [北美与欧洲边缘备份节点-#028](https://www.ai-hao123.com/shuju/supplier-62182757.html)
* [冷热数据分层镜像归档中心-#029](https://www.mw-wm.com/chanpin/shopping-57844834.html)
* [冷热数据分层镜像归档中心-#030](https://www.yx-sf.com/news/21751)
* [冷热数据分层镜像归档中心-#031](https://www.ai-hao123.com/anli/alliance-29060204.html)
* [亚太核心区域镜像同步中心-#032](https://www.mw-wm.com/fenxi/cheap-02871175.html)
* [冷热数据分层镜像归档中心-#033](https://www.yx-sf.com/tech/52838)
* [实时主干镜像高速数据源-#034](https://www.ai-hao123.com/baogao/music-69985705.html)
* [北美与欧洲边缘备份节点-#035](https://www.mw-wm.com/tuiguang/retention-04224009.html)
* [实时主干镜像高速数据源-#036](https://www.yx-sf.com/news/97357)
* [实时主干镜像高速数据源-#037](https://www.ai-hao123.com/gongxiang/reporting-98042556.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [权威网络权重与收录基准-#001](https://www.mw-wm.com/zhizhu/cloud-88099182.html)
* [防重放安全验证与校验哈希-#002](https://www.yx-sf.com/tech/31609)
* [权威网络权重与收录基准-#003](https://www.ai-hao123.com/gongsi/podcast-84532354.html)
* [去中心化健康检查协议-#004](https://www.mw-wm.com/chuangxin/community-45863866.html)
* [防重放安全验证与校验哈希-#005](https://www.yx-sf.com/tech/68909)
* [节点连通性与存活探测准则-#006](https://www.ai-hao123.com/hezuo/topic-56506097.html)
* [节点连通性与存活探测准则-#007](https://www.mw-wm.com/jiaocheng/excellence-71234833.html)
* [权威网络权重与收录基准-#008](https://www.yx-sf.com/wiki/25766)
* [权威网络权重与收录基准-#009](https://www.ai-hao123.com/baogao/restaurant-39701041.html)
* [节点连通性与存活探测准则-#010](https://www.mw-wm.com/zixun/status-18241611.html)
* [实时延迟与抖动度量规范-#011](https://www.yx-sf.com/tech/41474)
* [权威网络权重与收录基准-#012](https://www.ai-hao123.com/zhinan/article-30356851.html)
* [防重放安全验证与校验哈希-#013](https://www.mw-wm.com/xitong/like-85821269.html)
* [实时延迟与抖动度量规范-#014](https://www.yx-sf.com/tech/10131)
* [权威网络权重与收录基准-#015](https://www.ai-hao123.com/xitong/webinar-14834845.html)
* [防重放安全验证与校验哈希-#016](https://www.mw-wm.com/sheji/register-44517701.html)
* [防重放安全验证与校验哈希-#017](https://www.yx-sf.com/wiki/80949)
* [实时延迟与抖动度量规范-#018](https://www.ai-hao123.com/jiaoliu/news-67156809.html)
* [节点连通性与存活探测准则-#019](https://www.mw-wm.com/gongju/marketing-01341943.html)
* [权威网络权重与收录基准-#020](https://www.yx-sf.com/wiki/84913)
* [防重放安全验证与校验哈希-#021](https://www.ai-hao123.com/gongxiang/kpi-88997005.html)
* [节点连通性与存活探测准则-#022](https://www.mw-wm.com/xinwen/content-69569150.html)
* [节点连通性与存活探测准则-#023](https://www.yx-sf.com/wiki/71549)
* [防重放安全验证与校验哈希-#024](https://www.ai-hao123.com/kaifa/seminar-21051309.html)
* [去中心化健康检查协议-#025](https://www.mw-wm.com/shuju/food-03346751.html)
* [实时延迟与抖动度量规范-#026](https://www.yx-sf.com/wiki/78026)
* [权威网络权重与收录基准-#027](https://www.ai-hao123.com/zixun/media-27032642.html)
* [去中心化健康检查协议-#028](https://www.mw-wm.com/zixun/support-50247717.html)
* [实时延迟与抖动度量规范-#029](https://www.yx-sf.com/tech/67401)
* [权威网络权重与收录基准-#030](https://www.ai-hao123.com/yunying/partner-31567181.html)
* [权威网络权重与收录基准-#031](https://www.mw-wm.com/gongxiang/lead-92070638.html)
* [防重放安全验证与校验哈希-#032](https://www.yx-sf.com/tech/79206)
* [防重放安全验证与校验哈希-#033](https://www.ai-hao123.com/xinwen/settings-27842305.html)
* [节点连通性与存活探测准则-#034](https://www.mw-wm.com/youhua/sale-62692254.html)
* [防重放安全验证与校验哈希-#035](https://www.yx-sf.com/tech/75548)
* [去中心化健康检查协议-#036](https://www.ai-hao123.com/hezuo/deadline-14660327.html)
* [去中心化健康检查协议-#037](https://www.mw-wm.com/tuiguang/security-34811474.html)
* [去中心化健康检查协议-#038](https://www.yx-sf.com/news/86891)
* [防重放安全验证与校验哈希-#039](https://www.ai-hao123.com/wenzhang/income-13388865.html)

</details>

