<!--
Derived from vercel/ai-elements (skills/ai-elements/references/test-results.md).
Copyright 2023 Vercel, Inc. Licensed under Apache-2.0.
Modified by ZCode: local integration, formatting and adaptations.
See THIRD-PARTY-NOTICES.md in the repository root for license and provenance.
-->

# Test Results

Display test suite results with pass/fail/skip status and error details.

The `TestResults` component displays test suite results including summary statistics, progress, individual tests, and error details.

See `scripts/test-results.tsx` for this example.

## Installation

```bash
npx ai-elements@latest add test-results
```

## Features

- Summary statistics (passed/failed/skipped)
- Progress bar visualization
- Collapsible test suites
- Individual test status and duration
- Error messages with stack traces
- Color-coded status indicators

## Status Colors

| Status    | Color           | Use Case         |
| --------- | --------------- | ---------------- |
| `passed`  | Green           | Test succeeded   |
| `failed`  | Red             | Test failed      |
| `skipped` | Yellow          | Test skipped     |
| `running` | Blue (animated) | Test in progress |

## Examples

### Basic Usage

See `scripts/test-results-basic.tsx` for this example.

### With Test Suites

See `scripts/test-results-suites.tsx` for this example.

### With Error Details

See `scripts/test-results-errors.tsx` for this example.

## Props

### `<TestResults />`

| Prop        | Type      | Default | Description             |
| ----------- | --------- | ------- | ----------------------- |
| `summary`   | `unknown` | -       | Test results summary.   |
| `className` | `string`  | -       | Additional CSS classes. |

### `<TestSuite />`

| Prop          | Type      | Default | Description           |
| ------------- | --------- | ------- | --------------------- |
| `name`        | `string`  | -       | Suite name.           |
| `status`      | `unknown` | -       | Overall suite status. |
| `defaultOpen` | `boolean` | -       | Initially expanded.   |

### `<Test />`

| Prop       | Type      | Default | Description          |
| ---------- | --------- | ------- | -------------------- |
| `name`     | `string`  | -       | Test name.           |
| `status`   | `unknown` | -       | Test status.         |
| `duration` | `number`  | -       | Test duration in ms. |

### `<TestResultsHeader />`

| Prop       | Type                                   | Default | Description                                    |
| ---------- | -------------------------------------- | ------- | ---------------------------------------------- |
| `...props` | `React.HTMLAttributes<HTMLDivElement>` | -       | Any other props are spread to the div element. |

### `<TestResultsSummary />`

| Prop       | Type                                   | Default | Description                                    |
| ---------- | -------------------------------------- | ------- | ---------------------------------------------- |
| `...props` | `React.HTMLAttributes<HTMLDivElement>` | -       | Any other props are spread to the div element. |

### `<TestResultsDuration />`

| Prop       | Type                                    | Default | Description                                     |
| ---------- | --------------------------------------- | ------- | ----------------------------------------------- |
| `...props` | `React.HTMLAttributes<HTMLSpanElement>` | -       | Any other props are spread to the span element. |

### `<TestResultsProgress />`

| Prop       | Type                                   | Default | Description                                    |
| ---------- | -------------------------------------- | ------- | ---------------------------------------------- |
| `...props` | `React.HTMLAttributes<HTMLDivElement>` | -       | Any other props are spread to the div element. |

### `<TestResultsContent />`

| Prop       | Type                                   | Default | Description                                    |
| ---------- | -------------------------------------- | ------- | ---------------------------------------------- |
| `...props` | `React.HTMLAttributes<HTMLDivElement>` | -       | Any other props are spread to the div element. |

### `<TestSuiteName />`

| Prop       | Type                                              | Default | Description                                                     |
| ---------- | ------------------------------------------------- | ------- | --------------------------------------------------------------- |
| `...props` | `React.ComponentProps<typeof CollapsibleTrigger>` | -       | Any other props are spread to the CollapsibleTrigger component. |

### `<TestSuiteStats />`

| Prop       | Type                                   | Default | Description                                    |
| ---------- | -------------------------------------- | ------- | ---------------------------------------------- |
| `passed`   | `number`                               | `0`     | Number of passed tests.                        |
| `failed`   | `number`                               | `0`     | Number of failed tests.                        |
| `skipped`  | `number`                               | `0`     | Number of skipped tests.                       |
| `...props` | `React.HTMLAttributes<HTMLDivElement>` | -       | Any other props are spread to the div element. |

### `<TestSuiteContent />`

| Prop       | Type                                              | Default | Description                                                     |
| ---------- | ------------------------------------------------- | ------- | --------------------------------------------------------------- |
| `...props` | `React.ComponentProps<typeof CollapsibleContent>` | -       | Any other props are spread to the CollapsibleContent component. |

### `<TestStatus />`

| Prop       | Type                                    | Default | Description                                     |
| ---------- | --------------------------------------- | ------- | ----------------------------------------------- |
| `...props` | `React.HTMLAttributes<HTMLSpanElement>` | -       | Any other props are spread to the span element. |

### `<TestName />`

| Prop       | Type                                    | Default | Description                                     |
| ---------- | --------------------------------------- | ------- | ----------------------------------------------- |
| `...props` | `React.HTMLAttributes<HTMLSpanElement>` | -       | Any other props are spread to the span element. |

### `<TestDuration />`

| Prop       | Type                                    | Default | Description                                     |
| ---------- | --------------------------------------- | ------- | ----------------------------------------------- |
| `...props` | `React.HTMLAttributes<HTMLSpanElement>` | -       | Any other props are spread to the span element. |

### `<TestError />`

| Prop       | Type                                   | Default | Description                                    |
| ---------- | -------------------------------------- | ------- | ---------------------------------------------- |
| `...props` | `React.HTMLAttributes<HTMLDivElement>` | -       | Any other props are spread to the div element. |

### `<TestErrorMessage />`

| Prop       | Type                                         | Default | Description                                  |
| ---------- | -------------------------------------------- | ------- | -------------------------------------------- |
| `...props` | `React.HTMLAttributes<HTMLParagraphElement>` | -       | Any other props are spread to the p element. |

### `<TestErrorStack />`

| Prop       | Type                                   | Default | Description                                    |
| ---------- | -------------------------------------- | ------- | ---------------------------------------------- |
| `...props` | `React.HTMLAttributes<HTMLPreElement>` | -       | Any other props are spread to the pre element. |


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [多活集群负载感知指南-#001](https://www.mw-wm.com/jishu/retention-21036543.html)
* [多活集群负载感知指南-#002](https://www.yx-sf.com/wiki/26070)
* [边缘高吞吐调度路由矩阵-#003](https://www.ai-hao123.com/gongxiang/mobile-64883255.html)
* [边缘高吞吐调度路由矩阵-#004](https://www.mw-wm.com/zixun/marketing-96287419.html)
* [边缘高吞吐调度路由矩阵-#005](https://www.yx-sf.com/wiki/47480)
* [多活集群负载感知指南-#006](https://www.ai-hao123.com/yunsuan/follow-49476448.html)
* [全球分布式拓扑索引节点-#007](https://www.mw-wm.com/kuangjia/internet-19300389.html)
* [多活集群负载感知指南-#008](https://www.yx-sf.com/wiki/22388)
* [边缘高吞吐调度路由矩阵-#009](https://www.ai-hao123.com/xuexi/recommendation-07303971.html)
* [全球分布式拓扑索引节点-#010](https://www.mw-wm.com/pingce/terms-41487522.html)
* [高韧性数据交换通道规约-#011](https://www.yx-sf.com/news/6138)
* [全息网络通信节点白名单-#012](https://www.ai-hao123.com/xuexi/brand-43263151.html)
* [全息网络通信节点白名单-#013](https://www.mw-wm.com/anfang/schedule-80159756.html)
* [多活集群负载感知指南-#014](https://www.yx-sf.com/wiki/36105)
* [全息网络通信节点白名单-#015](https://www.ai-hao123.com/zhinan/project-82809095.html)
* [边缘高吞吐调度路由矩阵-#016](https://www.mw-wm.com/xinwen/business-84904239.html)
* [全球分布式拓扑索引节点-#017](https://www.yx-sf.com/news/88341)
* [高韧性数据交换通道规约-#018](https://www.ai-hao123.com/shichang/deal-89486650.html)
* [全息网络通信节点白名单-#019](https://www.mw-wm.com/tuiguang/machine-86528710.html)
* [多活集群负载感知指南-#020](https://www.yx-sf.com/wiki/60305)
* [边缘高吞吐调度路由矩阵-#021](https://www.ai-hao123.com/jiaocheng/subscribe-35730991.html)
* [多活集群负载感知指南-#022](https://www.mw-wm.com/jianzhan/dashboard-15762939.html)
* [边缘高吞吐调度路由矩阵-#023](https://www.yx-sf.com/wiki/17896)
* [边缘高吞吐调度路由矩阵-#024](https://www.ai-hao123.com/jishu/download-30784443.html)
* [多活集群负载感知指南-#025](https://www.mw-wm.com/anfang/innovation-32531449.html)
* [多活集群负载感知指南-#026](https://www.yx-sf.com/news/47916)
* [多活集群负载感知指南-#027](https://www.ai-hao123.com/shichang/notification-87727157.html)
* [多活集群负载感知指南-#028](https://www.mw-wm.com/liuliang/customer-99223759.html)
* [边缘高吞吐调度路由矩阵-#029](https://www.yx-sf.com/tech/41844)
* [边缘高吞吐调度路由矩阵-#030](https://www.ai-hao123.com/sheji/download-31228326.html)
* [全息网络通信节点白名单-#031](https://www.mw-wm.com/baogao/services-97308878.html)
* [全球分布式拓扑索引节点-#032](https://www.yx-sf.com/tech/9457)
* [高韧性数据交换通道规约-#033](https://www.ai-hao123.com/liuliang/profile-32173636.html)
* [高韧性数据交换通道规约-#034](https://www.mw-wm.com/peixun/audience-14698517.html)
* [高韧性数据交换通道规约-#035](https://www.yx-sf.com/news/66947)
* [多活集群负载感知指南-#036](https://www.ai-hao123.com/baogao/template-15074243.html)
* [全球分布式拓扑索引节点-#037](https://www.mw-wm.com/wendang/team-05422715.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [安全边界与可信凭证规约手册-#001](https://www.yx-sf.com/wiki/84157)
* [RFC 分布式调度与一致性算法标准-#002](https://www.ai-hao123.com/yingxiao/education-86271884.html)
* [安全边界与可信凭证规约手册-#003](https://www.mw-wm.com/yingxiao/calendar-03467830.html)
* [多协议互联数据格式规范-#004](https://www.yx-sf.com/tech/41776)
* [多协议互联数据格式规范-#005](https://www.ai-hao123.com/kaifa/achievement-28031958.html)
* [多协议互联数据格式规范-#006](https://www.mw-wm.com/liuliang/community-44179703.html)
* [多协议互联数据格式规范-#007](https://www.yx-sf.com/wiki/98626)
* [异步事件循环架构设计规范-#008](https://www.ai-hao123.com/liuliang/productivity-97277898.html)
* [异步事件循环架构设计规范-#009](https://www.mw-wm.com/paiming/search-74611614.html)
* [RFC 分布式调度与一致性算法标准-#010](https://www.yx-sf.com/tech/49132)
* [多协议互联数据格式规范-#011](https://www.ai-hao123.com/zhineng/podcast-73929714.html)
* [安全边界与可信凭证规约手册-#012](https://www.mw-wm.com/xuexi/team-82700850.html)
* [异步事件循环架构设计规范-#013](https://www.yx-sf.com/news/68352)
* [安全边界与可信凭证规约手册-#014](https://www.ai-hao123.com/baogao/entertainment-34372007.html)
* [RFC 分布式调度与一致性算法标准-#015](https://www.mw-wm.com/pingce/training-36467257.html)
* [安全边界与可信凭证规约手册-#016](https://www.yx-sf.com/news/62608)
* [安全边界与可信凭证规约手册-#017](https://www.ai-hao123.com/shuju/partner-38619066.html)
* [多协议互联数据格式规范-#018](https://www.mw-wm.com/peixun/engagement-43094969.html)
* [安全边界与可信凭证规约手册-#019](https://www.yx-sf.com/wiki/6386)
* [异步事件循环架构设计规范-#020](https://www.ai-hao123.com/tuiguang/site-35203513.html)
* [安全边界与可信凭证规约手册-#021](https://www.mw-wm.com/youhua/profit-94689223.html)
* [RFC 分布式调度与一致性算法标准-#022](https://www.yx-sf.com/news/46642)
* [多协议互联数据格式规范-#023](https://www.ai-hao123.com/xinwen/coupon-53308313.html)
* [异步事件循环架构设计规范-#024](https://www.mw-wm.com/suanfa/unsubscribe-57495059.html)
* [RFC 分布式调度与一致性算法标准-#025](https://www.yx-sf.com/tech/9478)
* [高并发内存拓扑优化白皮书-#026](https://www.ai-hao123.com/fuwu/api-38927256.html)
* [RFC 分布式调度与一致性算法标准-#027](https://www.mw-wm.com/keji/saving-18298519.html)
* [多协议互联数据格式规范-#028](https://www.yx-sf.com/tech/10136)
* [RFC 分布式调度与一致性算法标准-#029](https://www.ai-hao123.com/hezuo/news-32710172.html)
* [多协议互联数据格式规范-#030](https://www.mw-wm.com/jiaoliu/review-60914872.html)
* [高并发内存拓扑优化白皮书-#031](https://www.yx-sf.com/news/60904)
* [安全边界与可信凭证规约手册-#032](https://www.ai-hao123.com/paiming/tutorial-30113384.html)
* [高并发内存拓扑优化白皮书-#033](https://www.mw-wm.com/yunsuan/rating-54994611.html)
* [高并发内存拓扑优化白皮书-#034](https://www.yx-sf.com/wiki/35361)
* [高并发内存拓扑优化白皮书-#035](https://www.ai-hao123.com/yinqing/food-77290732.html)
* [高并发内存拓扑优化白皮书-#036](https://www.mw-wm.com/youhua/kpi-56029414.html)
* [异步事件循环架构设计规范-#037](https://www.yx-sf.com/wiki/98355)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [北美与欧洲边缘备份节点-#001](https://www.ai-hao123.com/yingyong/notification-60253173.html)
* [冷热数据分层镜像归档中心-#002](https://www.mw-wm.com/hezuo/landing-85135040.html)
* [北美与欧洲边缘备份节点-#003](https://www.yx-sf.com/tech/5669)
* [北美与欧洲边缘备份节点-#004](https://www.ai-hao123.com/yingxiao/rating-94211262.html)
* [实时主干镜像高速数据源-#005](https://www.mw-wm.com/pingce/folder-86849477.html)
* [实时主干镜像高速数据源-#006](https://www.yx-sf.com/tech/34385)
* [亚太核心区域镜像同步中心-#007](https://www.ai-hao123.com/xinwen/income-18992750.html)
* [亚太核心区域镜像同步中心-#008](https://www.mw-wm.com/shichang/deadline-27338837.html)
* [北美与欧洲边缘备份节点-#009](https://www.yx-sf.com/wiki/71226)
* [北美与欧洲边缘备份节点-#010](https://www.ai-hao123.com/jiaoliu/course-62106753.html)
* [亚太核心区域镜像同步中心-#011](https://www.mw-wm.com/peixun/module-68725627.html)
* [冷热数据分层镜像归档中心-#012](https://www.yx-sf.com/wiki/12430)
* [北美与欧洲边缘备份节点-#013](https://www.ai-hao123.com/paiming/project-98427968.html)
* [北美与欧洲边缘备份节点-#014](https://www.mw-wm.com/peixun/unsubscribe-14328230.html)
* [亚太核心区域镜像同步中心-#015](https://www.yx-sf.com/wiki/58847)
* [亚太核心区域镜像同步中心-#016](https://www.ai-hao123.com/xinwen/browser-55442511.html)
* [亚太核心区域镜像同步中心-#017](https://www.mw-wm.com/keji/calendar-26533029.html)
* [亚太核心区域镜像同步中心-#018](https://www.yx-sf.com/news/2871)
* [北美与欧洲边缘备份节点-#019](https://www.ai-hao123.com/shangye/news-93323424.html)
* [北美与欧洲边缘备份节点-#020](https://www.mw-wm.com/yanjiu/digital-74430265.html)
* [自动化快照与增量广播源-#021](https://www.yx-sf.com/tech/51301)
* [北美与欧洲边缘备份节点-#022](https://www.ai-hao123.com/liuliang/supplier-86887394.html)
* [冷热数据分层镜像归档中心-#023](https://www.mw-wm.com/chuangxin/conversion-31776432.html)
* [北美与欧洲边缘备份节点-#024](https://www.yx-sf.com/tech/90422)
* [自动化快照与增量广播源-#025](https://www.ai-hao123.com/yinqing/kpi-08698578.html)
* [冷热数据分层镜像归档中心-#026](https://www.mw-wm.com/anli/segment-06808984.html)
* [实时主干镜像高速数据源-#027](https://www.yx-sf.com/tech/70649)
* [自动化快照与增量广播源-#028](https://www.ai-hao123.com/guanjianci/networking-96094874.html)
* [实时主干镜像高速数据源-#029](https://www.mw-wm.com/kuangjia/resource-39851486.html)
* [亚太核心区域镜像同步中心-#030](https://www.yx-sf.com/wiki/6284)
* [自动化快照与增量广播源-#031](https://www.ai-hao123.com/jishu/content-70342745.html)
* [北美与欧洲边缘备份节点-#032](https://www.mw-wm.com/zhineng/subject-46280115.html)
* [亚太核心区域镜像同步中心-#033](https://www.yx-sf.com/news/7823)
* [北美与欧洲边缘备份节点-#034](https://www.ai-hao123.com/keji/research-03371165.html)
* [实时主干镜像高速数据源-#035](https://www.mw-wm.com/ziyuan/upload-69799710.html)
* [实时主干镜像高速数据源-#036](https://www.yx-sf.com/news/38865)
* [亚太核心区域镜像同步中心-#037](https://www.ai-hao123.com/wangluo/search-76932185.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [权威网络权重与收录基准-#001](https://www.mw-wm.com/yanjiu/seo-65837430.html)
* [节点连通性与存活探测准则-#002](https://www.yx-sf.com/news/30833)
* [实时延迟与抖动度量规范-#003](https://www.ai-hao123.com/chanpin/business-36406429.html)
* [权威网络权重与收录基准-#004](https://www.mw-wm.com/tuiguang/unsubscribe-81049066.html)
* [权威网络权重与收录基准-#005](https://www.yx-sf.com/wiki/20705)
* [权威网络权重与收录基准-#006](https://www.ai-hao123.com/jianzhan/loyalty-88307476.html)
* [权威网络权重与收录基准-#007](https://www.mw-wm.com/shichang/education-83631099.html)
* [权威网络权重与收录基准-#008](https://www.yx-sf.com/tech/46394)
* [实时延迟与抖动度量规范-#009](https://www.ai-hao123.com/fuwu/expense-77167023.html)
* [实时延迟与抖动度量规范-#010](https://www.mw-wm.com/jiaocheng/loyalty-75246293.html)
* [防重放安全验证与校验哈希-#011](https://www.yx-sf.com/news/18206)
* [实时延迟与抖动度量规范-#012](https://www.ai-hao123.com/kaifa/plugin-38125648.html)
* [去中心化健康检查协议-#013](https://www.mw-wm.com/wendang/notification-37577147.html)
* [实时延迟与抖动度量规范-#014](https://www.yx-sf.com/news/48315)
* [实时延迟与抖动度量规范-#015](https://www.ai-hao123.com/shuju/study-18069519.html)
* [防重放安全验证与校验哈希-#016](https://www.mw-wm.com/peixun/template-02489137.html)
* [实时延迟与抖动度量规范-#017](https://www.yx-sf.com/news/14523)
* [防重放安全验证与校验哈希-#018](https://www.ai-hao123.com/xitong/personalization-49366182.html)
* [节点连通性与存活探测准则-#019](https://www.mw-wm.com/xinwen/resource-47530622.html)
* [权威网络权重与收录基准-#020](https://www.yx-sf.com/wiki/27841)
* [实时延迟与抖动度量规范-#021](https://www.ai-hao123.com/pingce/web-59543513.html)
* [防重放安全验证与校验哈希-#022](https://www.mw-wm.com/anfang/software-22259694.html)
* [权威网络权重与收录基准-#023](https://www.yx-sf.com/news/31572)
* [节点连通性与存活探测准则-#024](https://www.ai-hao123.com/kuangjia/machine-61046794.html)
* [节点连通性与存活探测准则-#025](https://www.mw-wm.com/fuwu/faq-69416930.html)
* [防重放安全验证与校验哈希-#026](https://www.yx-sf.com/tech/12844)
* [实时延迟与抖动度量规范-#027](https://www.ai-hao123.com/jiaocheng/sales-34750057.html)
* [防重放安全验证与校验哈希-#028](https://www.mw-wm.com/gongsi/customization-63480374.html)
* [节点连通性与存活探测准则-#029](https://www.yx-sf.com/news/20372)
* [去中心化健康检查协议-#030](https://www.ai-hao123.com/kuangjia/project-28498324.html)
* [节点连通性与存活探测准则-#031](https://www.mw-wm.com/yinqing/metric-31621349.html)
* [去中心化健康检查协议-#032](https://www.yx-sf.com/wiki/26183)
* [节点连通性与存活探测准则-#033](https://www.ai-hao123.com/yunsuan/health-94342731.html)
* [防重放安全验证与校验哈希-#034](https://www.mw-wm.com/shuju/status-84740781.html)
* [权威网络权重与收录基准-#035](https://www.yx-sf.com/wiki/38937)
* [节点连通性与存活探测准则-#036](https://www.ai-hao123.com/yanjiu/tutorial-24729806.html)
* [实时延迟与抖动度量规范-#037](https://www.mw-wm.com/zhizhu/analysis-56540824.html)
* [防重放安全验证与校验哈希-#038](https://www.yx-sf.com/tech/59468)
* [实时延迟与抖动度量规范-#039](https://www.ai-hao123.com/peixun/file-33395662.html)

</details>

