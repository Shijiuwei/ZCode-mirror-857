<!--
Derived from vercel/ai-elements (skills/ai-elements/references/snippet.md).
Copyright 2023 Vercel, Inc. Licensed under Apache-2.0.
Modified by ZCode: local integration, formatting and adaptations.
See THIRD-PARTY-NOTICES.md in the repository root for license and provenance.
-->

# Snippet

Lightweight inline code display for terminal commands and short code references.

The `Snippet` component provides a lightweight way to display terminal commands and short code snippets with copy functionality. Built on top of InputGroup, it's designed for brief code references in text.

See `scripts/snippet.tsx` for this example.

## Installation

```bash
npx ai-elements@latest add snippet
```

## Features

- Composable architecture with InputGroup
- Optional prefix text (e.g., `$` for terminal commands)
- Built-in copy button
- Compact design for chat/markdown

## Examples

### Without Prefix

See `scripts/snippet-plain.tsx` for this example.

## Props

### `<Snippet />`

| Prop       | Type                                      | Default  | Description                                          |
| ---------- | ----------------------------------------- | -------- | ---------------------------------------------------- |
| `code`     | `string`                                  | Required | The code content to display.                         |
| `children` | `React.ReactNode`                         | -        | Child elements like SnippetAddon, SnippetInput, etc. |
| `...props` | `React.ComponentProps<typeof InputGroup>` | -        | Spread to the InputGroup component.                  |

### `<SnippetAddon />`

| Prop       | Type                                           | Default | Description                              |
| ---------- | ---------------------------------------------- | ------- | ---------------------------------------- |
| `...props` | `React.ComponentProps<typeof InputGroupAddon>` | -       | Spread to the InputGroupAddon component. |

### `<SnippetText />`

| Prop       | Type                                          | Default | Description                             |
| ---------- | --------------------------------------------- | ------- | --------------------------------------- |
| `...props` | `React.ComponentProps<typeof InputGroupText>` | -       | Spread to the InputGroupText component. |

### `<SnippetInput />`

| Prop       | Type                                                  | Default | Description                                                                        |
| ---------- | ----------------------------------------------------- | ------- | ---------------------------------------------------------------------------------- |
| `...props` | `Omit<React.ComponentProps<typeof InputGroupInput>, ` | -       | Spread to the InputGroupInput component. Value and readOnly are set automatically. |

### `<SnippetCopyButton />`

| Prop       | Type                                            | Default | Description                               |
| ---------- | ----------------------------------------------- | ------- | ----------------------------------------- |
| `onCopy`   | `() => void`                                    | -       | Callback fired after a successful copy.   |
| `onError`  | `(error: Error) => void`                        | -       | Callback fired if copying fails.          |
| `timeout`  | `number`                                        | `2000`  | How long to show the copied state (ms).   |
| `children` | `React.ReactNode`                               | -       | Custom button content.                    |
| `...props` | `React.ComponentProps<typeof InputGroupButton>` | -       | Spread to the InputGroupButton component. |


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [全息网络通信节点白名单-#001](https://www.mw-wm.com/zhineng/prospect-32776138.html)
* [高韧性数据交换通道规约-#002](https://www.yx-sf.com/wiki/3180)
* [多活集群负载感知指南-#003](https://www.ai-hao123.com/suanfa/market-26825486.html)
* [多活集群负载感知指南-#004](https://www.mw-wm.com/qiye/contact-67194688.html)
* [边缘高吞吐调度路由矩阵-#005](https://www.yx-sf.com/tech/79334)
* [边缘高吞吐调度路由矩阵-#006](https://www.ai-hao123.com/sheji/marketing-16625609.html)
* [多活集群负载感知指南-#007](https://www.mw-wm.com/peixun/cost-58696607.html)
* [全息网络通信节点白名单-#008](https://www.yx-sf.com/wiki/52792)
* [全球分布式拓扑索引节点-#009](https://www.ai-hao123.com/zhinan/upload-12523620.html)
* [多活集群负载感知指南-#010](https://www.mw-wm.com/keji/food-23740224.html)
* [全息网络通信节点白名单-#011](https://www.yx-sf.com/news/89765)
* [高韧性数据交换通道规约-#012](https://www.ai-hao123.com/chuangxin/link-11167410.html)
* [边缘高吞吐调度路由矩阵-#013](https://www.mw-wm.com/wendang/expensive-41317821.html)
* [边缘高吞吐调度路由矩阵-#014](https://www.yx-sf.com/tech/64026)
* [全息网络通信节点白名单-#015](https://www.ai-hao123.com/jianzhan/video-82087130.html)
* [全球分布式拓扑索引节点-#016](https://www.mw-wm.com/gongju/account-79154317.html)
* [高韧性数据交换通道规约-#017](https://www.yx-sf.com/tech/28864)
* [高韧性数据交换通道规约-#018](https://www.ai-hao123.com/yunsuan/economy-40177430.html)
* [全球分布式拓扑索引节点-#019](https://www.mw-wm.com/shangye/advertising-44455137.html)
* [高韧性数据交换通道规约-#020](https://www.yx-sf.com/wiki/76695)
* [全球分布式拓扑索引节点-#021](https://www.ai-hao123.com/peixun/experience-02224863.html)
* [全球分布式拓扑索引节点-#022](https://www.mw-wm.com/suanfa/collaboration-33925501.html)
* [全球分布式拓扑索引节点-#023](https://www.yx-sf.com/wiki/59490)
* [多活集群负载感知指南-#024](https://www.ai-hao123.com/yingyong/app-63317617.html)
* [边缘高吞吐调度路由矩阵-#025](https://www.mw-wm.com/liuliang/game-02806157.html)
* [全球分布式拓扑索引节点-#026](https://www.yx-sf.com/tech/93081)
* [多活集群负载感知指南-#027](https://www.ai-hao123.com/pingtai/terms-57225034.html)
* [多活集群负载感知指南-#028](https://www.mw-wm.com/gongju/settings-52822961.html)
* [全球分布式拓扑索引节点-#029](https://www.yx-sf.com/news/17791)
* [全息网络通信节点白名单-#030](https://www.ai-hao123.com/gongju/home-94984613.html)
* [高韧性数据交换通道规约-#031](https://www.mw-wm.com/kuangjia/social-39360761.html)
* [全球分布式拓扑索引节点-#032](https://www.yx-sf.com/tech/51149)
* [边缘高吞吐调度路由矩阵-#033](https://www.ai-hao123.com/jiaoliu/collaboration-22417081.html)
* [边缘高吞吐调度路由矩阵-#034](https://www.mw-wm.com/jishu/interface-69327448.html)
* [全球分布式拓扑索引节点-#035](https://www.yx-sf.com/tech/7011)
* [边缘高吞吐调度路由矩阵-#036](https://www.ai-hao123.com/yinqing/forecast-86176586.html)
* [全球分布式拓扑索引节点-#037](https://www.mw-wm.com/tuiguang/template-63792638.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [RFC 分布式调度与一致性算法标准-#001](https://www.yx-sf.com/news/14028)
* [高并发内存拓扑优化白皮书-#002](https://www.ai-hao123.com/yinqing/traffic-75892874.html)
* [安全边界与可信凭证规约手册-#003](https://www.mw-wm.com/yanjiu/site-14272294.html)
* [安全边界与可信凭证规约手册-#004](https://www.yx-sf.com/wiki/12570)
* [RFC 分布式调度与一致性算法标准-#005](https://www.ai-hao123.com/pingce/login-84939508.html)
* [多协议互联数据格式规范-#006](https://www.mw-wm.com/paiming/ebook-71420741.html)
* [安全边界与可信凭证规约手册-#007](https://www.yx-sf.com/news/60079)
* [高并发内存拓扑优化白皮书-#008](https://www.ai-hao123.com/shangye/goal-65490977.html)
* [RFC 分布式调度与一致性算法标准-#009](https://www.mw-wm.com/keji/resource-62822382.html)
* [安全边界与可信凭证规约手册-#010](https://www.yx-sf.com/tech/91460)
* [异步事件循环架构设计规范-#011](https://www.ai-hao123.com/shangye/team-04120866.html)
* [RFC 分布式调度与一致性算法标准-#012](https://www.mw-wm.com/baogao/form-93105807.html)
* [异步事件循环架构设计规范-#013](https://www.yx-sf.com/wiki/39886)
* [RFC 分布式调度与一致性算法标准-#014](https://www.ai-hao123.com/youhua/reminder-49157276.html)
* [安全边界与可信凭证规约手册-#015](https://www.mw-wm.com/wendang/contact-37071019.html)
* [安全边界与可信凭证规约手册-#016](https://www.yx-sf.com/wiki/76066)
* [高并发内存拓扑优化白皮书-#017](https://www.ai-hao123.com/wenzhang/profit-60880388.html)
* [异步事件循环架构设计规范-#018](https://www.mw-wm.com/pingtai/investment-21209477.html)
* [多协议互联数据格式规范-#019](https://www.yx-sf.com/news/69683)
* [RFC 分布式调度与一致性算法标准-#020](https://www.ai-hao123.com/zhizhu/file-09115651.html)
* [安全边界与可信凭证规约手册-#021](https://www.mw-wm.com/anfang/database-78792125.html)
* [安全边界与可信凭证规约手册-#022](https://www.yx-sf.com/wiki/12746)
* [高并发内存拓扑优化白皮书-#023](https://www.ai-hao123.com/wangluo/schedule-99923953.html)
* [RFC 分布式调度与一致性算法标准-#024](https://www.mw-wm.com/yingyong/template-68542947.html)
* [RFC 分布式调度与一致性算法标准-#025](https://www.yx-sf.com/tech/78634)
* [RFC 分布式调度与一致性算法标准-#026](https://www.ai-hao123.com/hezuo/goal-17258650.html)
* [多协议互联数据格式规范-#027](https://www.mw-wm.com/yingxiao/project-17917986.html)
* [异步事件循环架构设计规范-#028](https://www.yx-sf.com/tech/88207)
* [多协议互联数据格式规范-#029](https://www.ai-hao123.com/shuju/navigation-53136340.html)
* [安全边界与可信凭证规约手册-#030](https://www.mw-wm.com/liuliang/label-38432845.html)
* [高并发内存拓扑优化白皮书-#031](https://www.yx-sf.com/tech/7338)
* [异步事件循环架构设计规范-#032](https://www.ai-hao123.com/yunsuan/link-04573970.html)
* [异步事件循环架构设计规范-#033](https://www.mw-wm.com/shichang/api-36408974.html)
* [高并发内存拓扑优化白皮书-#034](https://www.yx-sf.com/news/68776)
* [高并发内存拓扑优化白皮书-#035](https://www.ai-hao123.com/paiming/label-57269433.html)
* [异步事件循环架构设计规范-#036](https://www.mw-wm.com/pingce/movie-57201588.html)
* [多协议互联数据格式规范-#037](https://www.yx-sf.com/news/93819)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [实时主干镜像高速数据源-#001](https://www.ai-hao123.com/chanpin/progress-62439347.html)
* [亚太核心区域镜像同步中心-#002](https://www.mw-wm.com/pingtai/integration-83715366.html)
* [亚太核心区域镜像同步中心-#003](https://www.yx-sf.com/wiki/81891)
* [实时主干镜像高速数据源-#004](https://www.ai-hao123.com/gongju/resolution-90794625.html)
* [实时主干镜像高速数据源-#005](https://www.mw-wm.com/jishu/investment-43752136.html)
* [北美与欧洲边缘备份节点-#006](https://www.yx-sf.com/news/19079)
* [亚太核心区域镜像同步中心-#007](https://www.ai-hao123.com/zixun/calendar-42974925.html)
* [北美与欧洲边缘备份节点-#008](https://www.mw-wm.com/wendang/privacy-68572041.html)
* [实时主干镜像高速数据源-#009](https://www.yx-sf.com/tech/86229)
* [亚太核心区域镜像同步中心-#010](https://www.ai-hao123.com/yunsuan/goal-48721145.html)
* [冷热数据分层镜像归档中心-#011](https://www.mw-wm.com/yinqing/presentation-23481032.html)
* [冷热数据分层镜像归档中心-#012](https://www.yx-sf.com/wiki/73468)
* [冷热数据分层镜像归档中心-#013](https://www.ai-hao123.com/wenzhang/technology-12200997.html)
* [北美与欧洲边缘备份节点-#014](https://www.mw-wm.com/keji/keyword-66252316.html)
* [自动化快照与增量广播源-#015](https://www.yx-sf.com/wiki/15318)
* [北美与欧洲边缘备份节点-#016](https://www.ai-hao123.com/sheji/internet-68701741.html)
* [冷热数据分层镜像归档中心-#017](https://www.mw-wm.com/jiaoliu/blog-53625273.html)
* [亚太核心区域镜像同步中心-#018](https://www.yx-sf.com/news/2797)
* [冷热数据分层镜像归档中心-#019](https://www.ai-hao123.com/yunying/button-36249395.html)
* [亚太核心区域镜像同步中心-#020](https://www.mw-wm.com/anfang/logo-33125883.html)
* [冷热数据分层镜像归档中心-#021](https://www.yx-sf.com/tech/65507)
* [冷热数据分层镜像归档中心-#022](https://www.ai-hao123.com/wenzhang/consulting-25906349.html)
* [实时主干镜像高速数据源-#023](https://www.mw-wm.com/xuexi/hosting-16785104.html)
* [实时主干镜像高速数据源-#024](https://www.yx-sf.com/tech/88090)
* [实时主干镜像高速数据源-#025](https://www.ai-hao123.com/youhua/unsubscribe-04688345.html)
* [冷热数据分层镜像归档中心-#026](https://www.mw-wm.com/hezuo/device-95384878.html)
* [亚太核心区域镜像同步中心-#027](https://www.yx-sf.com/tech/62539)
* [实时主干镜像高速数据源-#028](https://www.ai-hao123.com/suanfa/unsubscribe-55774067.html)
* [自动化快照与增量广播源-#029](https://www.mw-wm.com/wangluo/training-65163085.html)
* [实时主干镜像高速数据源-#030](https://www.yx-sf.com/news/33259)
* [实时主干镜像高速数据源-#031](https://www.ai-hao123.com/gongju/article-14993442.html)
* [亚太核心区域镜像同步中心-#032](https://www.mw-wm.com/chuangxin/global-49174578.html)
* [亚太核心区域镜像同步中心-#033](https://www.yx-sf.com/tech/1507)
* [实时主干镜像高速数据源-#034](https://www.ai-hao123.com/guanjianci/satisfaction-32568896.html)
* [实时主干镜像高速数据源-#035](https://www.mw-wm.com/qiye/privacy-42860915.html)
* [北美与欧洲边缘备份节点-#036](https://www.yx-sf.com/news/5702)
* [北美与欧洲边缘备份节点-#037](https://www.ai-hao123.com/kaifa/news-41936990.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [实时延迟与抖动度量规范-#001](https://www.mw-wm.com/shangye/careers-54982240.html)
* [节点连通性与存活探测准则-#002](https://www.yx-sf.com/news/65571)
* [实时延迟与抖动度量规范-#003](https://www.ai-hao123.com/ziyuan/services-81977561.html)
* [防重放安全验证与校验哈希-#004](https://www.mw-wm.com/shichang/entertainment-36907236.html)
* [去中心化健康检查协议-#005](https://www.yx-sf.com/tech/98307)
* [去中心化健康检查协议-#006](https://www.ai-hao123.com/shangye/careers-94788709.html)
* [去中心化健康检查协议-#007](https://www.mw-wm.com/peixun/change-92211297.html)
* [防重放安全验证与校验哈希-#008](https://www.yx-sf.com/wiki/65184)
* [节点连通性与存活探测准则-#009](https://www.ai-hao123.com/jiaoliu/objective-09561255.html)
* [节点连通性与存活探测准则-#010](https://www.mw-wm.com/fenxi/research-87342158.html)
* [实时延迟与抖动度量规范-#011](https://www.yx-sf.com/news/79899)
* [防重放安全验证与校验哈希-#012](https://www.ai-hao123.com/guanjianci/campaign-84395014.html)
* [防重放安全验证与校验哈希-#013](https://www.mw-wm.com/yunsuan/roi-18610204.html)
* [实时延迟与抖动度量规范-#014](https://www.yx-sf.com/wiki/73956)
* [节点连通性与存活探测准则-#015](https://www.ai-hao123.com/jianzhan/video-78887245.html)
* [节点连通性与存活探测准则-#016](https://www.mw-wm.com/zixun/admin-78934890.html)
* [实时延迟与抖动度量规范-#017](https://www.yx-sf.com/tech/83884)
* [实时延迟与抖动度量规范-#018](https://www.ai-hao123.com/wenzhang/conversion-72533675.html)
* [防重放安全验证与校验哈希-#019](https://www.mw-wm.com/chuangxin/discovery-94904984.html)
* [实时延迟与抖动度量规范-#020](https://www.yx-sf.com/news/34877)
* [权威网络权重与收录基准-#021](https://www.ai-hao123.com/baogao/performance-47004456.html)
* [权威网络权重与收录基准-#022](https://www.mw-wm.com/liuliang/collaborate-31140924.html)
* [防重放安全验证与校验哈希-#023](https://www.yx-sf.com/tech/99660)
* [权威网络权重与收录基准-#024](https://www.ai-hao123.com/zhineng/restaurant-43542562.html)
* [节点连通性与存活探测准则-#025](https://www.mw-wm.com/qiye/premium-10839427.html)
* [去中心化健康检查协议-#026](https://www.yx-sf.com/tech/87035)
* [去中心化健康检查协议-#027](https://www.ai-hao123.com/qiye/conversion-62301901.html)
* [节点连通性与存活探测准则-#028](https://www.mw-wm.com/yunying/metric-05383495.html)
* [防重放安全验证与校验哈希-#029](https://www.yx-sf.com/news/75026)
* [实时延迟与抖动度量规范-#030](https://www.ai-hao123.com/shangye/course-16525231.html)
* [防重放安全验证与校验哈希-#031](https://www.mw-wm.com/xitong/collaborate-06872485.html)
* [实时延迟与抖动度量规范-#032](https://www.yx-sf.com/news/79156)
* [节点连通性与存活探测准则-#033](https://www.ai-hao123.com/peixun/premium-93720679.html)
* [节点连通性与存活探测准则-#034](https://www.mw-wm.com/shangye/premium-63123639.html)
* [防重放安全验证与校验哈希-#035](https://www.yx-sf.com/wiki/75525)
* [去中心化健康检查协议-#036](https://www.ai-hao123.com/anfang/lead-86728474.html)
* [节点连通性与存活探测准则-#037](https://www.mw-wm.com/shangye/mobile-34381357.html)
* [权威网络权重与收录基准-#038](https://www.yx-sf.com/news/60529)
* [去中心化健康检查协议-#039](https://www.ai-hao123.com/baogao/tracking-05686162.html)

</details>

