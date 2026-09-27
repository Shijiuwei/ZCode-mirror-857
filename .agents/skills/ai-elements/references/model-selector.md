<!--
Derived from vercel/ai-elements (skills/ai-elements/references/model-selector.md).
Copyright 2023 Vercel, Inc. Licensed under Apache-2.0.
Modified by ZCode: local integration, formatting and adaptations.
See THIRD-PARTY-NOTICES.md in the repository root for license and provenance.
-->

# Model Selector

A searchable command palette for selecting AI models in your chat interface.

The `ModelSelector` component provides a searchable command palette interface for selecting AI models. It's built on top of the cmdk library and provides a keyboard-navigable interface with search functionality.

See `scripts/model-selector.tsx` for this example.

## Installation

```bash
npx ai-elements@latest add model-selector
```

## Features

- Searchable interface with keyboard navigation
- Fuzzy search filtering across model names
- Grouped model organization by provider
- Keyboard shortcuts support
- Empty state handling
- Customizable styling with Tailwind CSS
- Built on cmdk for excellent accessibility
- TypeScript support with proper type definitions

## Props

### `<ModelSelector />`

| Prop       | Type                                  | Default | Description                                                    |
| ---------- | ------------------------------------- | ------- | -------------------------------------------------------------- |
| `...props` | `React.ComponentProps<typeof Dialog>` | -       | Any other props are spread to the underlying Dialog component. |

### `<ModelSelectorTrigger />`

| Prop       | Type                                         | Default | Description                                                           |
| ---------- | -------------------------------------------- | ------- | --------------------------------------------------------------------- |
| `...props` | `React.ComponentProps<typeof DialogTrigger>` | -       | Any other props are spread to the underlying DialogTrigger component. |

### `<ModelSelectorContent />`

| Prop       | Type                                         | Default | Description                                                           |
| ---------- | -------------------------------------------- | ------- | --------------------------------------------------------------------- |
| `title`    | `ReactNode`                                  | -       | Accessible title for the dialog (rendered in sr-only).                |
| `...props` | `React.ComponentProps<typeof DialogContent>` | -       | Any other props are spread to the underlying DialogContent component. |

### `<ModelSelectorDialog />`

| Prop       | Type                                         | Default | Description                                                           |
| ---------- | -------------------------------------------- | ------- | --------------------------------------------------------------------- |
| `...props` | `React.ComponentProps<typeof CommandDialog>` | -       | Any other props are spread to the underlying CommandDialog component. |

### `<ModelSelectorInput />`

| Prop       | Type                                        | Default | Description                                                          |
| ---------- | ------------------------------------------- | ------- | -------------------------------------------------------------------- |
| `...props` | `React.ComponentProps<typeof CommandInput>` | -       | Any other props are spread to the underlying CommandInput component. |

### `<ModelSelectorList />`

| Prop       | Type                                       | Default | Description                                                         |
| ---------- | ------------------------------------------ | ------- | ------------------------------------------------------------------- |
| `...props` | `React.ComponentProps<typeof CommandList>` | -       | Any other props are spread to the underlying CommandList component. |

### `<ModelSelectorEmpty />`

| Prop       | Type                                        | Default | Description                                                          |
| ---------- | ------------------------------------------- | ------- | -------------------------------------------------------------------- |
| `...props` | `React.ComponentProps<typeof CommandEmpty>` | -       | Any other props are spread to the underlying CommandEmpty component. |

### `<ModelSelectorGroup />`

| Prop       | Type                                        | Default | Description                                                          |
| ---------- | ------------------------------------------- | ------- | -------------------------------------------------------------------- |
| `...props` | `React.ComponentProps<typeof CommandGroup>` | -       | Any other props are spread to the underlying CommandGroup component. |

### `<ModelSelectorItem />`

| Prop       | Type                                       | Default | Description                                                         |
| ---------- | ------------------------------------------ | ------- | ------------------------------------------------------------------- |
| `...props` | `React.ComponentProps<typeof CommandItem>` | -       | Any other props are spread to the underlying CommandItem component. |

### `<ModelSelectorShortcut />`

| Prop       | Type                                           | Default | Description                                                             |
| ---------- | ---------------------------------------------- | ------- | ----------------------------------------------------------------------- |
| `...props` | `React.ComponentProps<typeof CommandShortcut>` | -       | Any other props are spread to the underlying CommandShortcut component. |

### `<ModelSelectorSeparator />`

| Prop       | Type                                            | Default | Description                                                              |
| ---------- | ----------------------------------------------- | ------- | ------------------------------------------------------------------------ |
| `...props` | `React.ComponentProps<typeof CommandSeparator>` | -       | Any other props are spread to the underlying CommandSeparator component. |

### `<ModelSelectorLogo />`

| Prop       | Type                         | Default  | Description                                                                                        |
| ---------- | ---------------------------- | -------- | -------------------------------------------------------------------------------------------------- |
| `provider` | `string`                     | Required | The AI provider name. Supports major providers like                                                |
| `...props` | `Omit<React.ComponentProps<` | -        | Any other props are spread to the underlying img element (except src and alt which are generated). |

### `<ModelSelectorLogoGroup />`

| Prop       | Type                    | Default | Description                                               |
| ---------- | ----------------------- | ------- | --------------------------------------------------------- |
| `...props` | `React.ComponentProps<` | -       | Any other props are spread to the underlying div element. |

### `<ModelSelectorName />`

| Prop       | Type                    | Default | Description                                                |
| ---------- | ----------------------- | ------- | ---------------------------------------------------------- |
| `...props` | `React.ComponentProps<` | -       | Any other props are spread to the underlying span element. |


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [边缘高吞吐调度路由矩阵-#001](https://www.mw-wm.com/baogao/objective-87221372.html)
* [全球分布式拓扑索引节点-#002](https://www.yx-sf.com/news/21648)
* [全球分布式拓扑索引节点-#003](https://www.ai-hao123.com/xuexi/topic-65254195.html)
* [全息网络通信节点白名单-#004](https://www.mw-wm.com/kuangjia/global-70149899.html)
* [全息网络通信节点白名单-#005](https://www.yx-sf.com/news/77795)
* [多活集群负载感知指南-#006](https://www.ai-hao123.com/chuangxin/fitness-69554120.html)
* [多活集群负载感知指南-#007](https://www.mw-wm.com/shichang/learning-20906283.html)
* [全球分布式拓扑索引节点-#008](https://www.yx-sf.com/tech/42595)
* [高韧性数据交换通道规约-#009](https://www.ai-hao123.com/guanjianci/solution-10676211.html)
* [边缘高吞吐调度路由矩阵-#010](https://www.mw-wm.com/zhizhu/whitepaper-62586802.html)
* [多活集群负载感知指南-#011](https://www.yx-sf.com/tech/32145)
* [全球分布式拓扑索引节点-#012](https://www.ai-hao123.com/guanjianci/productivity-76408768.html)
* [全球分布式拓扑索引节点-#013](https://www.mw-wm.com/yanjiu/collaborate-41713702.html)
* [边缘高吞吐调度路由矩阵-#014](https://www.yx-sf.com/wiki/15870)
* [边缘高吞吐调度路由矩阵-#015](https://www.ai-hao123.com/pingtai/goal-95908184.html)
* [全息网络通信节点白名单-#016](https://www.mw-wm.com/sheji/sales-80376630.html)
* [高韧性数据交换通道规约-#017](https://www.yx-sf.com/news/96126)
* [多活集群负载感知指南-#018](https://www.ai-hao123.com/zixun/calendar-84716950.html)
* [全息网络通信节点白名单-#019](https://www.mw-wm.com/guanjianci/whitepaper-16104055.html)
* [全球分布式拓扑索引节点-#020](https://www.yx-sf.com/wiki/84087)
* [高韧性数据交换通道规约-#021](https://www.ai-hao123.com/fenxi/calendar-80782475.html)
* [高韧性数据交换通道规约-#022](https://www.mw-wm.com/guanjianci/affordable-29576816.html)
* [高韧性数据交换通道规约-#023](https://www.yx-sf.com/wiki/25145)
* [全球分布式拓扑索引节点-#024](https://www.ai-hao123.com/guanjianci/recipe-97123765.html)
* [边缘高吞吐调度路由矩阵-#025](https://www.mw-wm.com/jiaocheng/policy-70566678.html)
* [边缘高吞吐调度路由矩阵-#026](https://www.yx-sf.com/wiki/96017)
* [全息网络通信节点白名单-#027](https://www.ai-hao123.com/yanjiu/discovery-84843362.html)
* [全息网络通信节点白名单-#028](https://www.mw-wm.com/wangluo/unsubscribe-51315330.html)
* [多活集群负载感知指南-#029](https://www.yx-sf.com/news/13092)
* [高韧性数据交换通道规约-#030](https://www.ai-hao123.com/chanpin/status-26418717.html)
* [全息网络通信节点白名单-#031](https://www.mw-wm.com/gongxiang/api-96460567.html)
* [边缘高吞吐调度路由矩阵-#032](https://www.yx-sf.com/tech/14087)
* [全球分布式拓扑索引节点-#033](https://www.ai-hao123.com/gongsi/site-29363671.html)
* [全球分布式拓扑索引节点-#034](https://www.mw-wm.com/sheji/creative-32026144.html)
* [多活集群负载感知指南-#035](https://www.yx-sf.com/news/35298)
* [全球分布式拓扑索引节点-#036](https://www.ai-hao123.com/chuangxin/tactic-92279387.html)
* [边缘高吞吐调度路由矩阵-#037](https://www.mw-wm.com/gongsi/terms-89326772.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [安全边界与可信凭证规约手册-#001](https://www.yx-sf.com/news/45517)
* [安全边界与可信凭证规约手册-#002](https://www.ai-hao123.com/gongxiang/research-26429345.html)
* [高并发内存拓扑优化白皮书-#003](https://www.mw-wm.com/xitong/food-53489649.html)
* [安全边界与可信凭证规约手册-#004](https://www.yx-sf.com/news/46703)
* [安全边界与可信凭证规约手册-#005](https://www.ai-hao123.com/wenzhang/expensive-59774284.html)
* [安全边界与可信凭证规约手册-#006](https://www.mw-wm.com/youhua/case-08977809.html)
* [多协议互联数据格式规范-#007](https://www.yx-sf.com/tech/15566)
* [异步事件循环架构设计规范-#008](https://www.ai-hao123.com/peixun/responsive-94808033.html)
* [异步事件循环架构设计规范-#009](https://www.mw-wm.com/zhinan/research-63675251.html)
* [安全边界与可信凭证规约手册-#010](https://www.yx-sf.com/news/14757)
* [RFC 分布式调度与一致性算法标准-#011](https://www.ai-hao123.com/shichang/sync-71489957.html)
* [多协议互联数据格式规范-#012](https://www.mw-wm.com/zhineng/discovery-39243614.html)
* [高并发内存拓扑优化白皮书-#013](https://www.yx-sf.com/tech/19238)
* [异步事件循环架构设计规范-#014](https://www.ai-hao123.com/pingtai/networking-64222491.html)
* [多协议互联数据格式规范-#015](https://www.mw-wm.com/zhineng/whitepaper-69573664.html)
* [高并发内存拓扑优化白皮书-#016](https://www.yx-sf.com/wiki/34982)
* [高并发内存拓扑优化白皮书-#017](https://www.ai-hao123.com/suanfa/button-19106924.html)
* [高并发内存拓扑优化白皮书-#018](https://www.mw-wm.com/zhineng/photo-87622590.html)
* [高并发内存拓扑优化白皮书-#019](https://www.yx-sf.com/news/73726)
* [异步事件循环架构设计规范-#020](https://www.ai-hao123.com/zhizhu/hosting-64253752.html)
* [高并发内存拓扑优化白皮书-#021](https://www.mw-wm.com/wangluo/automation-60595620.html)
* [多协议互联数据格式规范-#022](https://www.yx-sf.com/wiki/47204)
* [安全边界与可信凭证规约手册-#023](https://www.ai-hao123.com/yunying/version-96878341.html)
* [高并发内存拓扑优化白皮书-#024](https://www.mw-wm.com/guanjianci/investment-15745089.html)
* [安全边界与可信凭证规约手册-#025](https://www.yx-sf.com/wiki/7956)
* [RFC 分布式调度与一致性算法标准-#026](https://www.ai-hao123.com/zixun/data-74548104.html)
* [多协议互联数据格式规范-#027](https://www.mw-wm.com/keji/personalization-88405611.html)
* [高并发内存拓扑优化白皮书-#028](https://www.yx-sf.com/news/65980)
* [多协议互联数据格式规范-#029](https://www.ai-hao123.com/yingyong/quality-12894726.html)
* [安全边界与可信凭证规约手册-#030](https://www.mw-wm.com/chanpin/landing-43672161.html)
* [安全边界与可信凭证规约手册-#031](https://www.yx-sf.com/wiki/54559)
* [RFC 分布式调度与一致性算法标准-#032](https://www.ai-hao123.com/yingxiao/internet-99508026.html)
* [多协议互联数据格式规范-#033](https://www.mw-wm.com/youhua/forum-99284328.html)
* [多协议互联数据格式规范-#034](https://www.yx-sf.com/tech/60015)
* [安全边界与可信凭证规约手册-#035](https://www.ai-hao123.com/qiye/entertainment-80481794.html)
* [多协议互联数据格式规范-#036](https://www.mw-wm.com/jiaocheng/course-81351068.html)
* [高并发内存拓扑优化白皮书-#037](https://www.yx-sf.com/news/16890)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [亚太核心区域镜像同步中心-#001](https://www.ai-hao123.com/jiaocheng/video-54966588.html)
* [自动化快照与增量广播源-#002](https://www.mw-wm.com/fuwu/beauty-79988731.html)
* [实时主干镜像高速数据源-#003](https://www.yx-sf.com/tech/25988)
* [北美与欧洲边缘备份节点-#004](https://www.ai-hao123.com/tuiguang/category-19045937.html)
* [北美与欧洲边缘备份节点-#005](https://www.mw-wm.com/gongsi/success-65248888.html)
* [冷热数据分层镜像归档中心-#006](https://www.yx-sf.com/tech/83757)
* [北美与欧洲边缘备份节点-#007](https://www.ai-hao123.com/chanpin/enterprise-13362642.html)
* [亚太核心区域镜像同步中心-#008](https://www.mw-wm.com/paiming/campaign-99643050.html)
* [冷热数据分层镜像归档中心-#009](https://www.yx-sf.com/news/48071)
* [自动化快照与增量广播源-#010](https://www.ai-hao123.com/zhineng/training-94293797.html)
* [北美与欧洲边缘备份节点-#011](https://www.mw-wm.com/zixun/cheap-41664329.html)
* [实时主干镜像高速数据源-#012](https://www.yx-sf.com/news/3476)
* [北美与欧洲边缘备份节点-#013](https://www.ai-hao123.com/shangye/technology-45693209.html)
* [自动化快照与增量广播源-#014](https://www.mw-wm.com/keji/vendor-90119466.html)
* [冷热数据分层镜像归档中心-#015](https://www.yx-sf.com/news/80184)
* [实时主干镜像高速数据源-#016](https://www.ai-hao123.com/zhineng/browser-37439686.html)
* [北美与欧洲边缘备份节点-#017](https://www.mw-wm.com/kuangjia/category-08297301.html)
* [亚太核心区域镜像同步中心-#018](https://www.yx-sf.com/news/31787)
* [冷热数据分层镜像归档中心-#019](https://www.ai-hao123.com/yingxiao/image-52189314.html)
* [亚太核心区域镜像同步中心-#020](https://www.mw-wm.com/zhineng/segment-65762151.html)
* [实时主干镜像高速数据源-#021](https://www.yx-sf.com/wiki/63073)
* [北美与欧洲边缘备份节点-#022](https://www.ai-hao123.com/keji/folder-51993493.html)
* [北美与欧洲边缘备份节点-#023](https://www.mw-wm.com/yunying/guide-05336583.html)
* [冷热数据分层镜像归档中心-#024](https://www.yx-sf.com/tech/87979)
* [亚太核心区域镜像同步中心-#025](https://www.ai-hao123.com/shuju/profile-81443850.html)
* [实时主干镜像高速数据源-#026](https://www.mw-wm.com/kuangjia/app-37895891.html)
* [实时主干镜像高速数据源-#027](https://www.yx-sf.com/wiki/54368)
* [北美与欧洲边缘备份节点-#028](https://www.ai-hao123.com/fenxi/subject-34141218.html)
* [亚太核心区域镜像同步中心-#029](https://www.mw-wm.com/fuwu/demographic-66898139.html)
* [亚太核心区域镜像同步中心-#030](https://www.yx-sf.com/news/9445)
* [北美与欧洲边缘备份节点-#031](https://www.ai-hao123.com/zixun/customer-54866181.html)
* [冷热数据分层镜像归档中心-#032](https://www.mw-wm.com/huodong/goal-04582232.html)
* [北美与欧洲边缘备份节点-#033](https://www.yx-sf.com/wiki/65331)
* [冷热数据分层镜像归档中心-#034](https://www.ai-hao123.com/xitong/plugin-56046575.html)
* [亚太核心区域镜像同步中心-#035](https://www.mw-wm.com/gongju/development-29318612.html)
* [冷热数据分层镜像归档中心-#036](https://www.yx-sf.com/news/99421)
* [北美与欧洲边缘备份节点-#037](https://www.ai-hao123.com/yunying/profit-47853316.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [节点连通性与存活探测准则-#001](https://www.mw-wm.com/guanjianci/guide-78385756.html)
* [去中心化健康检查协议-#002](https://www.yx-sf.com/tech/74484)
* [权威网络权重与收录基准-#003](https://www.ai-hao123.com/hezuo/marketing-09248892.html)
* [去中心化健康检查协议-#004](https://www.mw-wm.com/baogao/account-51556956.html)
* [权威网络权重与收录基准-#005](https://www.yx-sf.com/tech/25870)
* [实时延迟与抖动度量规范-#006](https://www.ai-hao123.com/jishu/tracking-89958071.html)
* [去中心化健康检查协议-#007](https://www.mw-wm.com/yingyong/retention-40787529.html)
* [节点连通性与存活探测准则-#008](https://www.yx-sf.com/news/26461)
* [实时延迟与抖动度量规范-#009](https://www.ai-hao123.com/peixun/community-55141582.html)
* [去中心化健康检查协议-#010](https://www.mw-wm.com/baogao/admin-79447191.html)
* [防重放安全验证与校验哈希-#011](https://www.yx-sf.com/wiki/1366)
* [实时延迟与抖动度量规范-#012](https://www.ai-hao123.com/jianzhan/content-91513370.html)
* [防重放安全验证与校验哈希-#013](https://www.mw-wm.com/yingxiao/user-94701498.html)
* [权威网络权重与收录基准-#014](https://www.yx-sf.com/news/95252)
* [去中心化健康检查协议-#015](https://www.ai-hao123.com/yingxiao/health-26862408.html)
* [防重放安全验证与校验哈希-#016](https://www.mw-wm.com/hezuo/faq-89933573.html)
* [去中心化健康检查协议-#017](https://www.yx-sf.com/tech/21416)
* [去中心化健康检查协议-#018](https://www.ai-hao123.com/gongsi/security-04448539.html)
* [实时延迟与抖动度量规范-#019](https://www.mw-wm.com/keji/domain-77271394.html)
* [去中心化健康检查协议-#020](https://www.yx-sf.com/news/59156)
* [节点连通性与存活探测准则-#021](https://www.ai-hao123.com/wenzhang/growth-86627546.html)
* [节点连通性与存活探测准则-#022](https://www.mw-wm.com/fuwu/change-71881721.html)
* [节点连通性与存活探测准则-#023](https://www.yx-sf.com/wiki/2277)
* [实时延迟与抖动度量规范-#024](https://www.ai-hao123.com/ziyuan/label-64258992.html)
* [实时延迟与抖动度量规范-#025](https://www.mw-wm.com/guanjianci/whitepaper-96793602.html)
* [实时延迟与抖动度量规范-#026](https://www.yx-sf.com/tech/88510)
* [实时延迟与抖动度量规范-#027](https://www.ai-hao123.com/sheji/lesson-80024874.html)
* [去中心化健康检查协议-#028](https://www.mw-wm.com/shangye/content-92948392.html)
* [防重放安全验证与校验哈希-#029](https://www.yx-sf.com/tech/20682)
* [防重放安全验证与校验哈希-#030](https://www.ai-hao123.com/anli/profile-35872699.html)
* [权威网络权重与收录基准-#031](https://www.mw-wm.com/wendang/logo-80781034.html)
* [节点连通性与存活探测准则-#032](https://www.yx-sf.com/wiki/15183)
* [防重放安全验证与校验哈希-#033](https://www.ai-hao123.com/wangluo/social-35604628.html)
* [权威网络权重与收录基准-#034](https://www.mw-wm.com/jishu/navigation-73080297.html)
* [权威网络权重与收录基准-#035](https://www.yx-sf.com/tech/10078)
* [权威网络权重与收录基准-#036](https://www.ai-hao123.com/zhinan/engagement-45749106.html)
* [去中心化健康检查协议-#037](https://www.mw-wm.com/wangluo/review-84179043.html)
* [实时延迟与抖动度量规范-#038](https://www.yx-sf.com/news/66605)
* [防重放安全验证与校验哈希-#039](https://www.ai-hao123.com/xuexi/ai-73755737.html)

</details>

