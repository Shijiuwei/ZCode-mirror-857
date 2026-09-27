<!--
Derived from vercel/ai-elements (skills/ai-elements/references/artifact.md).
Copyright 2023 Vercel, Inc. Licensed under Apache-2.0.
Modified by ZCode: local integration, formatting and adaptations.
See THIRD-PARTY-NOTICES.md in the repository root for license and provenance.
-->

# Artifact

A container component for displaying generated content like code, documents, or other outputs with built-in actions.

The `Artifact` component provides a structured container for displaying generated content like code, documents, or other outputs with built-in header actions.

See `scripts/artifact.tsx` for this example.

## Installation

```bash
npx ai-elements@latest add artifact
```

## Features

- Structured container with header and content areas
- Built-in header with title and description support
- Flexible action buttons with tooltips
- Customizable styling for all subcomponents
- Support for close buttons and action groups
- Clean, modern design with border and shadow
- Responsive layout that adapts to content
- TypeScript support with proper type definitions
- Composable architecture for maximum flexibility

## Examples

### With Code Display

See `scripts/artifact.tsx` for this example.

## Props

### `<Artifact />`

| Prop       | Type                                   | Default | Description                                               |
| ---------- | -------------------------------------- | ------- | --------------------------------------------------------- |
| `...props` | `React.HTMLAttributes<HTMLDivElement>` | -       | Any other props are spread to the underlying div element. |

### `<ArtifactHeader />`

| Prop       | Type                                   | Default | Description                                               |
| ---------- | -------------------------------------- | ------- | --------------------------------------------------------- |
| `...props` | `React.HTMLAttributes<HTMLDivElement>` | -       | Any other props are spread to the underlying div element. |

### `<ArtifactTitle />`

| Prop       | Type                                         | Default | Description                                                     |
| ---------- | -------------------------------------------- | ------- | --------------------------------------------------------------- |
| `...props` | `React.HTMLAttributes<HTMLParagraphElement>` | -       | Any other props are spread to the underlying paragraph element. |

### `<ArtifactDescription />`

| Prop       | Type                                         | Default | Description                                                     |
| ---------- | -------------------------------------------- | ------- | --------------------------------------------------------------- |
| `...props` | `React.HTMLAttributes<HTMLParagraphElement>` | -       | Any other props are spread to the underlying paragraph element. |

### `<ArtifactActions />`

| Prop       | Type                                   | Default | Description                                               |
| ---------- | -------------------------------------- | ------- | --------------------------------------------------------- |
| `...props` | `React.HTMLAttributes<HTMLDivElement>` | -       | Any other props are spread to the underlying div element. |

### `<ArtifactAction />`

| Prop       | Type                                  | Default | Description                                                              |
| ---------- | ------------------------------------- | ------- | ------------------------------------------------------------------------ |
| `tooltip`  | `string`                              | -       | Tooltip text to display on hover.                                        |
| `label`    | `string`                              | -       | Screen reader label for the action button.                               |
| `icon`     | `LucideIcon`                          | -       | Lucide icon component to display in the button.                          |
| `...props` | `React.ComponentProps<typeof Button>` | -       | Any other props are spread to the underlying shadcn/ui Button component. |

### `<ArtifactClose />`

| Prop       | Type                                  | Default | Description                                                              |
| ---------- | ------------------------------------- | ------- | ------------------------------------------------------------------------ |
| `...props` | `React.ComponentProps<typeof Button>` | -       | Any other props are spread to the underlying shadcn/ui Button component. |

### `<ArtifactContent />`

| Prop       | Type                                   | Default | Description                                               |
| ---------- | -------------------------------------- | ------- | --------------------------------------------------------- |
| `...props` | `React.HTMLAttributes<HTMLDivElement>` | -       | Any other props are spread to the underlying div element. |


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [全息网络通信节点白名单-#001](https://www.mw-wm.com/pingce/vacation-25851398.html)
* [边缘高吞吐调度路由矩阵-#002](https://www.yx-sf.com/tech/43926)
* [全息网络通信节点白名单-#003](https://www.ai-hao123.com/zhineng/search-71775393.html)
* [多活集群负载感知指南-#004](https://www.mw-wm.com/gongsi/advertising-20394964.html)
* [边缘高吞吐调度路由矩阵-#005](https://www.yx-sf.com/tech/15781)
* [全球分布式拓扑索引节点-#006](https://www.ai-hao123.com/yingyong/customization-09543304.html)
* [边缘高吞吐调度路由矩阵-#007](https://www.mw-wm.com/anfang/review-39586518.html)
* [边缘高吞吐调度路由矩阵-#008](https://www.yx-sf.com/tech/96922)
* [多活集群负载感知指南-#009](https://www.ai-hao123.com/yunying/home-79914681.html)
* [边缘高吞吐调度路由矩阵-#010](https://www.mw-wm.com/peixun/integration-39804687.html)
* [高韧性数据交换通道规约-#011](https://www.yx-sf.com/tech/52041)
* [多活集群负载感知指南-#012](https://www.ai-hao123.com/peixun/browser-39020813.html)
* [多活集群负载感知指南-#013](https://www.mw-wm.com/huodong/recommendation-07491538.html)
* [多活集群负载感知指南-#014](https://www.yx-sf.com/wiki/61205)
* [高韧性数据交换通道规约-#015](https://www.ai-hao123.com/qiye/luxury-66266489.html)
* [全球分布式拓扑索引节点-#016](https://www.mw-wm.com/kaifa/economy-94432164.html)
* [全球分布式拓扑索引节点-#017](https://www.yx-sf.com/wiki/67880)
* [多活集群负载感知指南-#018](https://www.ai-hao123.com/pingce/profit-43976035.html)
* [全息网络通信节点白名单-#019](https://www.mw-wm.com/yingyong/layout-17660255.html)
* [多活集群负载感知指南-#020](https://www.yx-sf.com/wiki/20182)
* [边缘高吞吐调度路由矩阵-#021](https://www.ai-hao123.com/kaifa/mobile-18846298.html)
* [全球分布式拓扑索引节点-#022](https://www.mw-wm.com/kuangjia/budget-15159609.html)
* [全息网络通信节点白名单-#023](https://www.yx-sf.com/news/32357)
* [高韧性数据交换通道规约-#024](https://www.ai-hao123.com/zhineng/cloud-18114805.html)
* [高韧性数据交换通道规约-#025](https://www.mw-wm.com/kuangjia/collaboration-29082483.html)
* [高韧性数据交换通道规约-#026](https://www.yx-sf.com/wiki/54894)
* [多活集群负载感知指南-#027](https://www.ai-hao123.com/kaifa/movie-38418989.html)
* [高韧性数据交换通道规约-#028](https://www.mw-wm.com/xuexi/success-57031684.html)
* [全息网络通信节点白名单-#029](https://www.yx-sf.com/tech/10497)
* [多活集群负载感知指南-#030](https://www.ai-hao123.com/fenxi/online-39844479.html)
* [全息网络通信节点白名单-#031](https://www.mw-wm.com/ziyuan/trading-28259795.html)
* [多活集群负载感知指南-#032](https://www.yx-sf.com/tech/77136)
* [高韧性数据交换通道规约-#033](https://www.ai-hao123.com/fenxi/domain-57728326.html)
* [全球分布式拓扑索引节点-#034](https://www.mw-wm.com/suanfa/discovery-27927991.html)
* [高韧性数据交换通道规约-#035](https://www.yx-sf.com/wiki/80717)
* [多活集群负载感知指南-#036](https://www.ai-hao123.com/zhizhu/efficiency-64293274.html)
* [全息网络通信节点白名单-#037](https://www.mw-wm.com/tuiguang/whitepaper-41030694.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [安全边界与可信凭证规约手册-#001](https://www.yx-sf.com/news/79687)
* [高并发内存拓扑优化白皮书-#002](https://www.ai-hao123.com/ziyuan/responsive-91804546.html)
* [多协议互联数据格式规范-#003](https://www.mw-wm.com/paiming/message-62185335.html)
* [RFC 分布式调度与一致性算法标准-#004](https://www.yx-sf.com/news/28609)
* [异步事件循环架构设计规范-#005](https://www.ai-hao123.com/yunying/partner-47195394.html)
* [异步事件循环架构设计规范-#006](https://www.mw-wm.com/shuju/document-53322462.html)
* [多协议互联数据格式规范-#007](https://www.yx-sf.com/news/32760)
* [RFC 分布式调度与一致性算法标准-#008](https://www.ai-hao123.com/wangluo/lead-49400483.html)
* [多协议互联数据格式规范-#009](https://www.mw-wm.com/wendang/user-61727251.html)
* [安全边界与可信凭证规约手册-#010](https://www.yx-sf.com/wiki/89845)
* [安全边界与可信凭证规约手册-#011](https://www.ai-hao123.com/paiming/blog-45294790.html)
* [RFC 分布式调度与一致性算法标准-#012](https://www.mw-wm.com/jianzhan/creative-95914647.html)
* [多协议互联数据格式规范-#013](https://www.yx-sf.com/wiki/21796)
* [安全边界与可信凭证规约手册-#014](https://www.ai-hao123.com/fenxi/services-39388772.html)
* [异步事件循环架构设计规范-#015](https://www.mw-wm.com/wangluo/deadline-11044665.html)
* [多协议互联数据格式规范-#016](https://www.yx-sf.com/tech/55787)
* [高并发内存拓扑优化白皮书-#017](https://www.ai-hao123.com/baogao/retention-91732974.html)
* [安全边界与可信凭证规约手册-#018](https://www.mw-wm.com/kuangjia/funnel-60954500.html)
* [多协议互联数据格式规范-#019](https://www.yx-sf.com/wiki/48944)
* [多协议互联数据格式规范-#020](https://www.ai-hao123.com/jiaocheng/schedule-14247617.html)
* [RFC 分布式调度与一致性算法标准-#021](https://www.mw-wm.com/sheji/creative-92345304.html)
* [高并发内存拓扑优化白皮书-#022](https://www.yx-sf.com/wiki/40108)
* [RFC 分布式调度与一致性算法标准-#023](https://www.ai-hao123.com/pingce/network-07175395.html)
* [RFC 分布式调度与一致性算法标准-#024](https://www.mw-wm.com/anli/education-13136292.html)
* [高并发内存拓扑优化白皮书-#025](https://www.yx-sf.com/tech/25132)
* [多协议互联数据格式规范-#026](https://www.ai-hao123.com/wendang/discount-32892249.html)
* [安全边界与可信凭证规约手册-#027](https://www.mw-wm.com/gongxiang/analysis-43843145.html)
* [安全边界与可信凭证规约手册-#028](https://www.yx-sf.com/news/52595)
* [多协议互联数据格式规范-#029](https://www.ai-hao123.com/jianzhan/alliance-80797800.html)
* [安全边界与可信凭证规约手册-#030](https://www.mw-wm.com/zhinan/excellence-27132476.html)
* [RFC 分布式调度与一致性算法标准-#031](https://www.yx-sf.com/tech/9999)
* [安全边界与可信凭证规约手册-#032](https://www.ai-hao123.com/gongsi/template-76893883.html)
* [安全边界与可信凭证规约手册-#033](https://www.mw-wm.com/zhinan/recommendation-02266866.html)
* [安全边界与可信凭证规约手册-#034](https://www.yx-sf.com/news/8826)
* [安全边界与可信凭证规约手册-#035](https://www.ai-hao123.com/anfang/client-47852853.html)
* [异步事件循环架构设计规范-#036](https://www.mw-wm.com/zhineng/project-06962041.html)
* [异步事件循环架构设计规范-#037](https://www.yx-sf.com/wiki/25710)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [自动化快照与增量广播源-#001](https://www.ai-hao123.com/kaifa/restaurant-98792921.html)
* [北美与欧洲边缘备份节点-#002](https://www.mw-wm.com/huodong/customization-69881546.html)
* [北美与欧洲边缘备份节点-#003](https://www.yx-sf.com/news/66081)
* [亚太核心区域镜像同步中心-#004](https://www.ai-hao123.com/zhizhu/resource-54288917.html)
* [自动化快照与增量广播源-#005](https://www.mw-wm.com/chuangxin/terms-81759076.html)
* [自动化快照与增量广播源-#006](https://www.yx-sf.com/news/74160)
* [自动化快照与增量广播源-#007](https://www.ai-hao123.com/peixun/entertainment-01063389.html)
* [亚太核心区域镜像同步中心-#008](https://www.mw-wm.com/fenxi/workshop-85158354.html)
* [北美与欧洲边缘备份节点-#009](https://www.yx-sf.com/news/35195)
* [冷热数据分层镜像归档中心-#010](https://www.ai-hao123.com/jiaocheng/profit-06448747.html)
* [自动化快照与增量广播源-#011](https://www.mw-wm.com/shangye/client-36197456.html)
* [实时主干镜像高速数据源-#012](https://www.yx-sf.com/news/23390)
* [亚太核心区域镜像同步中心-#013](https://www.ai-hao123.com/jishu/form-82700059.html)
* [实时主干镜像高速数据源-#014](https://www.mw-wm.com/yingyong/collaboration-62476590.html)
* [亚太核心区域镜像同步中心-#015](https://www.yx-sf.com/tech/78696)
* [自动化快照与增量广播源-#016](https://www.ai-hao123.com/jiaocheng/button-90655275.html)
* [实时主干镜像高速数据源-#017](https://www.mw-wm.com/jiaoliu/lead-77663173.html)
* [冷热数据分层镜像归档中心-#018](https://www.yx-sf.com/news/31366)
* [冷热数据分层镜像归档中心-#019](https://www.ai-hao123.com/guanjianci/web-52996406.html)
* [北美与欧洲边缘备份节点-#020](https://www.mw-wm.com/paiming/sales-16235034.html)
* [冷热数据分层镜像归档中心-#021](https://www.yx-sf.com/tech/47374)
* [冷热数据分层镜像归档中心-#022](https://www.ai-hao123.com/kuangjia/review-40840292.html)
* [自动化快照与增量广播源-#023](https://www.mw-wm.com/liuliang/products-53900345.html)
* [实时主干镜像高速数据源-#024](https://www.yx-sf.com/news/90348)
* [实时主干镜像高速数据源-#025](https://www.ai-hao123.com/zhineng/profile-33119306.html)
* [实时主干镜像高速数据源-#026](https://www.mw-wm.com/pingtai/market-00656017.html)
* [亚太核心区域镜像同步中心-#027](https://www.yx-sf.com/tech/2044)
* [亚太核心区域镜像同步中心-#028](https://www.ai-hao123.com/wendang/accessibility-98594132.html)
* [亚太核心区域镜像同步中心-#029](https://www.mw-wm.com/fenxi/forecast-80056985.html)
* [自动化快照与增量广播源-#030](https://www.yx-sf.com/news/71286)
* [北美与欧洲边缘备份节点-#031](https://www.ai-hao123.com/jiaoliu/document-55096644.html)
* [冷热数据分层镜像归档中心-#032](https://www.mw-wm.com/shichang/media-31830376.html)
* [实时主干镜像高速数据源-#033](https://www.yx-sf.com/news/41557)
* [实时主干镜像高速数据源-#034](https://www.ai-hao123.com/chanpin/section-38026143.html)
* [亚太核心区域镜像同步中心-#035](https://www.mw-wm.com/baogao/sport-72552674.html)
* [实时主干镜像高速数据源-#036](https://www.yx-sf.com/wiki/16369)
* [实时主干镜像高速数据源-#037](https://www.ai-hao123.com/wenzhang/innovation-56032670.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [实时延迟与抖动度量规范-#001](https://www.mw-wm.com/fenxi/device-47326394.html)
* [防重放安全验证与校验哈希-#002](https://www.yx-sf.com/wiki/4379)
* [实时延迟与抖动度量规范-#003](https://www.ai-hao123.com/keji/hosting-88082173.html)
* [防重放安全验证与校验哈希-#004](https://www.mw-wm.com/zhineng/home-24156260.html)
* [实时延迟与抖动度量规范-#005](https://www.yx-sf.com/news/11012)
* [节点连通性与存活探测准则-#006](https://www.ai-hao123.com/youhua/database-04934564.html)
* [节点连通性与存活探测准则-#007](https://www.mw-wm.com/qiye/message-58062047.html)
* [防重放安全验证与校验哈希-#008](https://www.yx-sf.com/tech/55426)
* [防重放安全验证与校验哈希-#009](https://www.ai-hao123.com/chuangxin/ranking-40995524.html)
* [实时延迟与抖动度量规范-#010](https://www.mw-wm.com/chuangxin/value-03168904.html)
* [节点连通性与存活探测准则-#011](https://www.yx-sf.com/wiki/19922)
* [节点连通性与存活探测准则-#012](https://www.ai-hao123.com/wenzhang/vendor-22252745.html)
* [节点连通性与存活探测准则-#013](https://www.mw-wm.com/liuliang/innovation-23047871.html)
* [权威网络权重与收录基准-#014](https://www.yx-sf.com/news/7553)
* [实时延迟与抖动度量规范-#015](https://www.ai-hao123.com/tuiguang/training-20133100.html)
* [实时延迟与抖动度量规范-#016](https://www.mw-wm.com/yinqing/network-24198963.html)
* [节点连通性与存活探测准则-#017](https://www.yx-sf.com/wiki/46526)
* [权威网络权重与收录基准-#018](https://www.ai-hao123.com/jishu/networking-48913490.html)
* [去中心化健康检查协议-#019](https://www.mw-wm.com/jiaoliu/alliance-75545380.html)
* [节点连通性与存活探测准则-#020](https://www.yx-sf.com/wiki/32045)
* [实时延迟与抖动度量规范-#021](https://www.ai-hao123.com/shangye/target-65139843.html)
* [防重放安全验证与校验哈希-#022](https://www.mw-wm.com/wenzhang/business-99802861.html)
* [防重放安全验证与校验哈希-#023](https://www.yx-sf.com/wiki/45234)
* [节点连通性与存活探测准则-#024](https://www.ai-hao123.com/xitong/tutorial-72183536.html)
* [实时延迟与抖动度量规范-#025](https://www.mw-wm.com/wenzhang/ranking-48104813.html)
* [防重放安全验证与校验哈希-#026](https://www.yx-sf.com/wiki/41185)
* [去中心化健康检查协议-#027](https://www.ai-hao123.com/gongju/ranking-89238569.html)
* [实时延迟与抖动度量规范-#028](https://www.mw-wm.com/suanfa/register-11151983.html)
* [节点连通性与存活探测准则-#029](https://www.yx-sf.com/news/83883)
* [防重放安全验证与校验哈希-#030](https://www.ai-hao123.com/huodong/browser-88468355.html)
* [防重放安全验证与校验哈希-#031](https://www.mw-wm.com/tuiguang/conference-66020842.html)
* [实时延迟与抖动度量规范-#032](https://www.yx-sf.com/news/61750)
* [防重放安全验证与校验哈希-#033](https://www.ai-hao123.com/kaifa/help-22653611.html)
* [去中心化健康检查协议-#034](https://www.mw-wm.com/jianzhan/campaign-06950878.html)
* [防重放安全验证与校验哈希-#035](https://www.yx-sf.com/tech/16565)
* [权威网络权重与收录基准-#036](https://www.ai-hao123.com/anli/products-81475326.html)
* [节点连通性与存活探测准则-#037](https://www.mw-wm.com/huodong/message-19945585.html)
* [实时延迟与抖动度量规范-#038](https://www.yx-sf.com/tech/36381)
* [节点连通性与存活探测准则-#039](https://www.ai-hao123.com/shangye/course-31925628.html)

</details>

