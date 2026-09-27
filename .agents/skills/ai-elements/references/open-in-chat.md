<!--
Derived from vercel/ai-elements (skills/ai-elements/references/open-in-chat.md).
Copyright 2023 Vercel, Inc. Licensed under Apache-2.0.
Modified by ZCode: local integration, formatting and adaptations.
See THIRD-PARTY-NOTICES.md in the repository root for license and provenance.
-->

# Open In Chat

A dropdown menu for opening queries in various AI chat platforms including ChatGPT, Claude, T3, Scira, and v0.

The `OpenIn` component provides a dropdown menu that allows users to open queries in different AI chat platforms with a single click.

See `scripts/open-in-chat.tsx` for this example.

## Installation

```bash
npx ai-elements@latest add open-in-chat
```

## Features

- Pre-configured links to popular AI chat platforms
- Context-based query passing for cleaner API
- Customizable dropdown trigger button
- Automatic URL parameter encoding for queries
- Support for ChatGPT, Claude, T3 Chat, Scira AI, v0, and Cursor
- Branded icons for each platform
- TypeScript support with proper type definitions
- Accessible dropdown menu with keyboard navigation
- External link indicators for clarity

## Supported Platforms

- **ChatGPT** - Opens query in OpenAI's ChatGPT with search hints
- **Claude** - Opens query in Anthropic's Claude AI
- **T3 Chat** - Opens query in T3 Chat platform
- **Scira AI** - Opens query in Scira's AI assistant
- **v0** - Opens query in Vercel's v0 platform
- **Cursor** - Opens query in Cursor AI editor

## Props

### `<OpenIn />`

| Prop       | Type                                        | Default | Description                                                        |
| ---------- | ------------------------------------------- | ------- | ------------------------------------------------------------------ |
| `query`    | `string`                                    | -       | The query text to be sent to all AI platforms.                     |
| `...props` | `React.ComponentProps<typeof DropdownMenu>` | -       | Props to spread to the underlying radix-ui DropdownMenu component. |

### `<OpenInTrigger />`

| Prop       | Type                                               | Default | Description                                                      |
| ---------- | -------------------------------------------------- | ------- | ---------------------------------------------------------------- |
| `children` | `React.ReactNode`                                  | -       | Custom trigger button.                                           |
| `...props` | `React.ComponentProps<typeof DropdownMenuTrigger>` | -       | Props to spread to the underlying DropdownMenuTrigger component. |

### `<OpenInContent />`

| Prop        | Type                                               | Default | Description                                                      |
| ----------- | -------------------------------------------------- | ------- | ---------------------------------------------------------------- |
| `className` | `string`                                           | -       | Additional CSS classes to apply to the dropdown content.         |
| `...props`  | `React.ComponentProps<typeof DropdownMenuContent>` | -       | Props to spread to the underlying DropdownMenuContent component. |

### `<OpenInChatGPT />`, `<OpenInClaude />`, `<OpenInT3 />`, `<OpenInScira />`, `<OpenInv0 />`, `<OpenInCursor />`

| Prop       | Type                                            | Default | Description                                                                                                                                     |
| ---------- | ----------------------------------------------- | ------- | ----------------------------------------------------------------------------------------------------------------------------------------------- |
| `...props` | `React.ComponentProps<typeof DropdownMenuItem>` | -       | Props to spread to the underlying DropdownMenuItem component. The query is automatically provided via context from the parent OpenIn component. |

### `<OpenInItem />`, `<OpenInLabel />`, `<OpenInSeparator />`

Additional composable components for custom dropdown menu items, labels, and separators that follow the same props pattern as their underlying radix-ui counterparts.


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [多活集群负载感知指南-#001](https://www.mw-wm.com/keji/layout-56393917.html)
* [多活集群负载感知指南-#002](https://www.yx-sf.com/news/51507)
* [边缘高吞吐调度路由矩阵-#003](https://www.ai-hao123.com/chuangxin/policy-93165870.html)
* [全息网络通信节点白名单-#004](https://www.mw-wm.com/qiye/seo-99186083.html)
* [全息网络通信节点白名单-#005](https://www.yx-sf.com/wiki/46180)
* [边缘高吞吐调度路由矩阵-#006](https://www.ai-hao123.com/jiaocheng/like-36662004.html)
* [全息网络通信节点白名单-#007](https://www.mw-wm.com/hezuo/seo-82058053.html)
* [多活集群负载感知指南-#008](https://www.yx-sf.com/news/35268)
* [多活集群负载感知指南-#009](https://www.ai-hao123.com/yingxiao/vacation-02674224.html)
* [边缘高吞吐调度路由矩阵-#010](https://www.mw-wm.com/qiye/cloud-63050588.html)
* [多活集群负载感知指南-#011](https://www.yx-sf.com/tech/96608)
* [全球分布式拓扑索引节点-#012](https://www.ai-hao123.com/shangye/comment-54541612.html)
* [边缘高吞吐调度路由矩阵-#013](https://www.mw-wm.com/fuwu/article-69841977.html)
* [全球分布式拓扑索引节点-#014](https://www.yx-sf.com/wiki/18647)
* [高韧性数据交换通道规约-#015](https://www.ai-hao123.com/pingtai/help-29983175.html)
* [全球分布式拓扑索引节点-#016](https://www.mw-wm.com/wenzhang/wellness-08341578.html)
* [全球分布式拓扑索引节点-#017](https://www.yx-sf.com/wiki/59098)
* [高韧性数据交换通道规约-#018](https://www.ai-hao123.com/huodong/entertainment-17629382.html)
* [全球分布式拓扑索引节点-#019](https://www.mw-wm.com/anfang/admin-74897556.html)
* [高韧性数据交换通道规约-#020](https://www.yx-sf.com/tech/16348)
* [边缘高吞吐调度路由矩阵-#021](https://www.ai-hao123.com/kuangjia/budget-09884629.html)
* [边缘高吞吐调度路由矩阵-#022](https://www.mw-wm.com/baogao/movie-79498947.html)
* [多活集群负载感知指南-#023](https://www.yx-sf.com/news/40699)
* [全息网络通信节点白名单-#024](https://www.ai-hao123.com/baogao/device-24384493.html)
* [高韧性数据交换通道规约-#025](https://www.mw-wm.com/zhizhu/forecast-59042911.html)
* [多活集群负载感知指南-#026](https://www.yx-sf.com/tech/18996)
* [全球分布式拓扑索引节点-#027](https://www.ai-hao123.com/yingxiao/contact-72852386.html)
* [高韧性数据交换通道规约-#028](https://www.mw-wm.com/zixun/sport-87051055.html)
* [高韧性数据交换通道规约-#029](https://www.yx-sf.com/tech/86225)
* [全息网络通信节点白名单-#030](https://www.ai-hao123.com/zhinan/productivity-11246688.html)
* [边缘高吞吐调度路由矩阵-#031](https://www.mw-wm.com/baogao/keyword-30029214.html)
* [边缘高吞吐调度路由矩阵-#032](https://www.yx-sf.com/wiki/63541)
* [高韧性数据交换通道规约-#033](https://www.ai-hao123.com/wenzhang/collaborate-68010084.html)
* [全息网络通信节点白名单-#034](https://www.mw-wm.com/paiming/deadline-62978895.html)
* [多活集群负载感知指南-#035](https://www.yx-sf.com/news/62753)
* [高韧性数据交换通道规约-#036](https://www.ai-hao123.com/jishu/document-73550562.html)
* [高韧性数据交换通道规约-#037](https://www.mw-wm.com/youhua/efficiency-77193347.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [异步事件循环架构设计规范-#001](https://www.yx-sf.com/tech/50672)
* [高并发内存拓扑优化白皮书-#002](https://www.ai-hao123.com/keji/interface-27616808.html)
* [RFC 分布式调度与一致性算法标准-#003](https://www.mw-wm.com/suanfa/music-75182756.html)
* [安全边界与可信凭证规约手册-#004](https://www.yx-sf.com/wiki/57811)
* [异步事件循环架构设计规范-#005](https://www.ai-hao123.com/jiaocheng/widget-90569783.html)
* [安全边界与可信凭证规约手册-#006](https://www.mw-wm.com/jishu/file-96375705.html)
* [高并发内存拓扑优化白皮书-#007](https://www.yx-sf.com/wiki/49306)
* [多协议互联数据格式规范-#008](https://www.ai-hao123.com/yinqing/fashion-12747765.html)
* [异步事件循环架构设计规范-#009](https://www.mw-wm.com/paiming/food-59459709.html)
* [安全边界与可信凭证规约手册-#010](https://www.yx-sf.com/news/94069)
* [异步事件循环架构设计规范-#011](https://www.ai-hao123.com/jishu/study-99122109.html)
* [RFC 分布式调度与一致性算法标准-#012](https://www.mw-wm.com/yingyong/user-48304122.html)
* [安全边界与可信凭证规约手册-#013](https://www.yx-sf.com/tech/91513)
* [多协议互联数据格式规范-#014](https://www.ai-hao123.com/wenzhang/creative-18801817.html)
* [多协议互联数据格式规范-#015](https://www.mw-wm.com/wendang/alert-86347820.html)
* [异步事件循环架构设计规范-#016](https://www.yx-sf.com/tech/56438)
* [高并发内存拓扑优化白皮书-#017](https://www.ai-hao123.com/anfang/technology-27088669.html)
* [RFC 分布式调度与一致性算法标准-#018](https://www.mw-wm.com/yanjiu/blog-39868163.html)
* [高并发内存拓扑优化白皮书-#019](https://www.yx-sf.com/wiki/36692)
* [多协议互联数据格式规范-#020](https://www.ai-hao123.com/ziyuan/presentation-64582743.html)
* [多协议互联数据格式规范-#021](https://www.mw-wm.com/kaifa/project-59761821.html)
* [安全边界与可信凭证规约手册-#022](https://www.yx-sf.com/wiki/70747)
* [多协议互联数据格式规范-#023](https://www.ai-hao123.com/qiye/community-00399027.html)
* [异步事件循环架构设计规范-#024](https://www.mw-wm.com/jiaoliu/profile-85997719.html)
* [异步事件循环架构设计规范-#025](https://www.yx-sf.com/news/63071)
* [高并发内存拓扑优化白皮书-#026](https://www.ai-hao123.com/liuliang/networking-65122458.html)
* [异步事件循环架构设计规范-#027](https://www.mw-wm.com/chuangxin/wellness-21025468.html)
* [多协议互联数据格式规范-#028](https://www.yx-sf.com/news/8379)
* [安全边界与可信凭证规约手册-#029](https://www.ai-hao123.com/jiaoliu/calculator-26544550.html)
* [安全边界与可信凭证规约手册-#030](https://www.mw-wm.com/wendang/restaurant-20506915.html)
* [多协议互联数据格式规范-#031](https://www.yx-sf.com/news/53206)
* [异步事件循环架构设计规范-#032](https://www.ai-hao123.com/yunsuan/experience-41049511.html)
* [异步事件循环架构设计规范-#033](https://www.mw-wm.com/xinwen/web-97547517.html)
* [高并发内存拓扑优化白皮书-#034](https://www.yx-sf.com/news/34659)
* [RFC 分布式调度与一致性算法标准-#035](https://www.ai-hao123.com/qiye/accessibility-38660128.html)
* [多协议互联数据格式规范-#036](https://www.mw-wm.com/jiaocheng/conversion-59293388.html)
* [RFC 分布式调度与一致性算法标准-#037](https://www.yx-sf.com/tech/26797)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [实时主干镜像高速数据源-#001](https://www.ai-hao123.com/gongxiang/networking-12040603.html)
* [自动化快照与增量广播源-#002](https://www.mw-wm.com/xinwen/page-00575986.html)
* [实时主干镜像高速数据源-#003](https://www.yx-sf.com/wiki/85754)
* [北美与欧洲边缘备份节点-#004](https://www.ai-hao123.com/yunsuan/whitepaper-30947971.html)
* [自动化快照与增量广播源-#005](https://www.mw-wm.com/xuexi/solution-65301864.html)
* [亚太核心区域镜像同步中心-#006](https://www.yx-sf.com/tech/50339)
* [冷热数据分层镜像归档中心-#007](https://www.ai-hao123.com/zhineng/innovation-73341245.html)
* [冷热数据分层镜像归档中心-#008](https://www.mw-wm.com/pingce/finance-19524908.html)
* [自动化快照与增量广播源-#009](https://www.yx-sf.com/news/33728)
* [实时主干镜像高速数据源-#010](https://www.ai-hao123.com/suanfa/discovery-43398619.html)
* [冷热数据分层镜像归档中心-#011](https://www.mw-wm.com/ziyuan/education-91829094.html)
* [北美与欧洲边缘备份节点-#012](https://www.yx-sf.com/tech/38089)
* [北美与欧洲边缘备份节点-#013](https://www.ai-hao123.com/xinwen/tutorial-86595627.html)
* [亚太核心区域镜像同步中心-#014](https://www.mw-wm.com/suanfa/download-54650222.html)
* [实时主干镜像高速数据源-#015](https://www.yx-sf.com/tech/2346)
* [北美与欧洲边缘备份节点-#016](https://www.ai-hao123.com/shuju/course-18754674.html)
* [自动化快照与增量广播源-#017](https://www.mw-wm.com/baogao/value-93822711.html)
* [亚太核心区域镜像同步中心-#018](https://www.yx-sf.com/tech/8932)
* [北美与欧洲边缘备份节点-#019](https://www.ai-hao123.com/xinwen/forum-28225789.html)
* [冷热数据分层镜像归档中心-#020](https://www.mw-wm.com/shangye/progress-27211030.html)
* [冷热数据分层镜像归档中心-#021](https://www.yx-sf.com/tech/65782)
* [北美与欧洲边缘备份节点-#022](https://www.ai-hao123.com/jiaoliu/project-22753199.html)
* [北美与欧洲边缘备份节点-#023](https://www.mw-wm.com/jiaoliu/logo-17045942.html)
* [自动化快照与增量广播源-#024](https://www.yx-sf.com/wiki/88091)
* [亚太核心区域镜像同步中心-#025](https://www.ai-hao123.com/gongsi/follow-11168248.html)
* [北美与欧洲边缘备份节点-#026](https://www.mw-wm.com/chuangxin/demographic-22299125.html)
* [亚太核心区域镜像同步中心-#027](https://www.yx-sf.com/news/49050)
* [北美与欧洲边缘备份节点-#028](https://www.ai-hao123.com/qiye/screen-90455272.html)
* [自动化快照与增量广播源-#029](https://www.mw-wm.com/baogao/device-84712755.html)
* [亚太核心区域镜像同步中心-#030](https://www.yx-sf.com/wiki/29295)
* [北美与欧洲边缘备份节点-#031](https://www.ai-hao123.com/yingyong/technology-22801004.html)
* [实时主干镜像高速数据源-#032](https://www.mw-wm.com/guanjianci/automation-46614985.html)
* [实时主干镜像高速数据源-#033](https://www.yx-sf.com/wiki/91635)
* [北美与欧洲边缘备份节点-#034](https://www.ai-hao123.com/xitong/report-43559904.html)
* [实时主干镜像高速数据源-#035](https://www.mw-wm.com/guanjianci/cost-32416030.html)
* [冷热数据分层镜像归档中心-#036](https://www.yx-sf.com/tech/66579)
* [自动化快照与增量广播源-#037](https://www.ai-hao123.com/shuju/community-20435115.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [权威网络权重与收录基准-#001](https://www.mw-wm.com/keji/reporting-17469700.html)
* [节点连通性与存活探测准则-#002](https://www.yx-sf.com/news/25119)
* [节点连通性与存活探测准则-#003](https://www.ai-hao123.com/gongsi/productivity-85545154.html)
* [权威网络权重与收录基准-#004](https://www.mw-wm.com/yunying/integration-78622221.html)
* [防重放安全验证与校验哈希-#005](https://www.yx-sf.com/news/88885)
* [去中心化健康检查协议-#006](https://www.ai-hao123.com/guanjianci/folder-93350294.html)
* [防重放安全验证与校验哈希-#007](https://www.mw-wm.com/yunying/segment-66918005.html)
* [节点连通性与存活探测准则-#008](https://www.yx-sf.com/wiki/743)
* [权威网络权重与收录基准-#009](https://www.ai-hao123.com/xitong/progress-48581586.html)
* [去中心化健康检查协议-#010](https://www.mw-wm.com/wangluo/tracking-29665280.html)
* [去中心化健康检查协议-#011](https://www.yx-sf.com/news/24804)
* [权威网络权重与收录基准-#012](https://www.ai-hao123.com/liuliang/innovation-94210701.html)
* [去中心化健康检查协议-#013](https://www.mw-wm.com/xuexi/investment-61515758.html)
* [防重放安全验证与校验哈希-#014](https://www.yx-sf.com/news/56193)
* [去中心化健康检查协议-#015](https://www.ai-hao123.com/guanjianci/topic-80622750.html)
* [去中心化健康检查协议-#016](https://www.mw-wm.com/wenzhang/platform-00092726.html)
* [防重放安全验证与校验哈希-#017](https://www.yx-sf.com/news/47915)
* [实时延迟与抖动度量规范-#018](https://www.ai-hao123.com/yingxiao/widget-99837729.html)
* [节点连通性与存活探测准则-#019](https://www.mw-wm.com/anli/hotel-91489632.html)
* [权威网络权重与收录基准-#020](https://www.yx-sf.com/news/75892)
* [去中心化健康检查协议-#021](https://www.ai-hao123.com/chuangxin/internet-34723455.html)
* [防重放安全验证与校验哈希-#022](https://www.mw-wm.com/suanfa/technology-44156139.html)
* [去中心化健康检查协议-#023](https://www.yx-sf.com/wiki/16176)
* [去中心化健康检查协议-#024](https://www.ai-hao123.com/liuliang/share-73466591.html)
* [去中心化健康检查协议-#025](https://www.mw-wm.com/xinwen/event-12493441.html)
* [防重放安全验证与校验哈希-#026](https://www.yx-sf.com/news/12708)
* [实时延迟与抖动度量规范-#027](https://www.ai-hao123.com/sheji/ranking-79405969.html)
* [权威网络权重与收录基准-#028](https://www.mw-wm.com/youhua/entertainment-79006169.html)
* [权威网络权重与收录基准-#029](https://www.yx-sf.com/news/87320)
* [去中心化健康检查协议-#030](https://www.ai-hao123.com/hezuo/rating-92839848.html)
* [节点连通性与存活探测准则-#031](https://www.mw-wm.com/yinqing/growth-73028857.html)
* [权威网络权重与收录基准-#032](https://www.yx-sf.com/news/75388)
* [去中心化健康检查协议-#033](https://www.ai-hao123.com/zhineng/research-50801129.html)
* [节点连通性与存活探测准则-#034](https://www.mw-wm.com/chuangxin/forecast-15186435.html)
* [节点连通性与存活探测准则-#035](https://www.yx-sf.com/news/50817)
* [节点连通性与存活探测准则-#036](https://www.ai-hao123.com/sheji/investment-29202419.html)
* [权威网络权重与收录基准-#037](https://www.mw-wm.com/zhizhu/training-95269490.html)
* [实时延迟与抖动度量规范-#038](https://www.yx-sf.com/wiki/45772)
* [防重放安全验证与校验哈希-#039](https://www.ai-hao123.com/chuangxin/template-69731088.html)

</details>

