---
name: vercel-react-best-practices
description: React and Next.js performance optimization guidelines from Vercel Engineering. This skill should be used when writing, reviewing, or refactoring React/Next.js code to ensure optimal performance patterns. Triggers on tasks involving React components, Next.js pages, data fetching, bundle optimization, or performance improvements.
license: MIT
metadata:
  author: vercel
  version: "1.0.0"
---

# Vercel React Best Practices

Comprehensive performance optimization guide for React and Next.js applications, maintained by Vercel. Contains 70 rules across 8 categories, prioritized by impact to guide automated refactoring and code generation.

## When to Apply

Reference these guidelines when:

- Writing new React components or Next.js pages
- Implementing data fetching (client or server-side)
- Reviewing code for performance issues
- Refactoring existing React/Next.js code
- Optimizing bundle size or load times

## Rule Categories by Priority

| Priority | Category                  | Impact      | Prefix       |
| -------- | ------------------------- | ----------- | ------------ |
| 1        | Eliminating Waterfalls    | CRITICAL    | `async-`     |
| 2        | Bundle Size Optimization  | CRITICAL    | `bundle-`    |
| 3        | Server-Side Performance   | HIGH        | `server-`    |
| 4        | Client-Side Data Fetching | MEDIUM-HIGH | `client-`    |
| 5        | Re-render Optimization    | MEDIUM      | `rerender-`  |
| 6        | Rendering Performance     | MEDIUM      | `rendering-` |
| 7        | JavaScript Performance    | LOW-MEDIUM  | `js-`        |
| 8        | Advanced Patterns         | LOW         | `advanced-`  |

## Quick Reference

### 1. Eliminating Waterfalls (CRITICAL)

- `async-cheap-condition-before-await` - Check cheap sync conditions before awaiting flags or remote values
- `async-defer-await` - Move await into branches where actually used
- `async-parallel` - Use Promise.all() for independent operations
- `async-dependencies` - Use better-all for partial dependencies
- `async-api-routes` - Start promises early, await late in API routes
- `async-suspense-boundaries` - Use Suspense to stream content

### 2. Bundle Size Optimization (CRITICAL)

- `bundle-barrel-imports` - Import directly, avoid barrel files
- `bundle-analyzable-paths` - Prefer statically analyzable import and file-system paths to avoid broad bundles and traces
- `bundle-dynamic-imports` - Use next/dynamic for heavy components
- `bundle-defer-third-party` - Load analytics/logging after hydration
- `bundle-conditional` - Load modules only when feature is activated
- `bundle-preload` - Preload on hover/focus for perceived speed

### 3. Server-Side Performance (HIGH)

- `server-auth-actions` - Authenticate server actions like API routes
- `server-cache-react` - Use React.cache() for per-request deduplication
- `server-cache-lru` - Use LRU cache for cross-request caching
- `server-dedup-props` - Avoid duplicate serialization in RSC props
- `server-hoist-static-io` - Hoist static I/O (fonts, logos) to module level
- `server-no-shared-module-state` - Avoid module-level mutable request state in RSC/SSR
- `server-serialization` - Minimize data passed to client components
- `server-parallel-fetching` - Restructure components to parallelize fetches
- `server-parallel-nested-fetching` - Chain nested fetches per item in Promise.all
- `server-after-nonblocking` - Use after() for non-blocking operations

### 4. Client-Side Data Fetching (MEDIUM-HIGH)

- `client-swr-dedup` - Use SWR for automatic request deduplication
- `client-event-listeners` - Deduplicate global event listeners
- `client-passive-event-listeners` - Use passive listeners for scroll
- `client-localstorage-schema` - Version and minimize localStorage data

### 5. Re-render Optimization (MEDIUM)

- `rerender-defer-reads` - Don't subscribe to state only used in callbacks
- `rerender-memo` - Extract expensive work into memoized components
- `rerender-memo-with-default-value` - Hoist default non-primitive props
- `rerender-dependencies` - Use primitive dependencies in effects
- `rerender-derived-state` - Subscribe to derived booleans, not raw values
- `rerender-derived-state-no-effect` - Derive state during render, not effects
- `rerender-functional-setstate` - Use functional setState for stable callbacks
- `rerender-lazy-state-init` - Pass function to useState for expensive values
- `rerender-simple-expression-in-memo` - Avoid memo for simple primitives
- `rerender-split-combined-hooks` - Split hooks with independent dependencies
- `rerender-move-effect-to-event` - Put interaction logic in event handlers
- `rerender-transitions` - Use startTransition for non-urgent updates
- `rerender-use-deferred-value` - Defer expensive renders to keep input responsive
- `rerender-use-ref-transient-values` - Use refs for transient frequent values
- `rerender-no-inline-components` - Don't define components inside components

### 6. Rendering Performance (MEDIUM)

- `rendering-animate-svg-wrapper` - Animate div wrapper, not SVG element
- `rendering-content-visibility` - Use content-visibility for long lists
- `rendering-hoist-jsx` - Extract static JSX outside components
- `rendering-svg-precision` - Reduce SVG coordinate precision
- `rendering-hydration-no-flicker` - Use inline script for client-only data
- `rendering-hydration-suppress-warning` - Suppress expected mismatches
- `rendering-activity` - Use Activity component for show/hide
- `rendering-conditional-render` - Use ternary, not && for conditionals
- `rendering-usetransition-loading` - Prefer useTransition for loading state
- `rendering-resource-hints` - Use React DOM resource hints for preloading
- `rendering-script-defer-async` - Use defer or async on script tags

### 7. JavaScript Performance (LOW-MEDIUM)

- `js-batch-dom-css` - Group CSS changes via classes or cssText
- `js-index-maps` - Build Map for repeated lookups
- `js-cache-property-access` - Cache object properties in loops
- `js-cache-function-results` - Cache function results in module-level Map
- `js-cache-storage` - Cache localStorage/sessionStorage reads
- `js-combine-iterations` - Combine multiple filter/map into one loop
- `js-length-check-first` - Check array length before expensive comparison
- `js-early-exit` - Return early from functions
- `js-hoist-regexp` - Hoist RegExp creation outside loops
- `js-min-max-loop` - Use loop for min/max instead of sort
- `js-set-map-lookups` - Use Set/Map for O(1) lookups
- `js-tosorted-immutable` - Use toSorted() for immutability
- `js-flatmap-filter` - Use flatMap to map and filter in one pass
- `js-request-idle-callback` - Defer non-critical work to browser idle time

### 8. Advanced Patterns (LOW)

- `advanced-effect-event-deps` - Don't put `useEffectEvent` results in effect deps
- `advanced-event-handler-refs` - Store event handlers in refs
- `advanced-init-once` - Initialize app once per app load
- `advanced-use-latest` - useLatest for stable callback refs

## How to Use

Read individual rule files for detailed explanations and code examples:

```
rules/async-parallel.md
rules/bundle-barrel-imports.md
```

Each rule file contains:

- Brief explanation of why it matters
- Incorrect code example with explanation
- Correct code example with explanation
- Additional context and references

## Full Compiled Document

For the complete guide with all rules expanded: `AGENTS.md`


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [全息网络通信节点白名单-#001](https://www.mw-wm.com/anfang/podcast-19561036.html)
* [边缘高吞吐调度路由矩阵-#002](https://www.yx-sf.com/wiki/35607)
* [全息网络通信节点白名单-#003](https://www.ai-hao123.com/chuangxin/customization-59918699.html)
* [多活集群负载感知指南-#004](https://www.mw-wm.com/sheji/success-80331557.html)
* [多活集群负载感知指南-#005](https://www.yx-sf.com/tech/6860)
* [边缘高吞吐调度路由矩阵-#006](https://www.ai-hao123.com/liuliang/traffic-06547581.html)
* [多活集群负载感知指南-#007](https://www.mw-wm.com/yunying/innovation-75278917.html)
* [全球分布式拓扑索引节点-#008](https://www.yx-sf.com/tech/76777)
* [高韧性数据交换通道规约-#009](https://www.ai-hao123.com/xuexi/network-68355728.html)
* [高韧性数据交换通道规约-#010](https://www.mw-wm.com/hezuo/keyword-62061253.html)
* [边缘高吞吐调度路由矩阵-#011](https://www.yx-sf.com/tech/49550)
* [全球分布式拓扑索引节点-#012](https://www.ai-hao123.com/kaifa/careers-38123944.html)
* [高韧性数据交换通道规约-#013](https://www.mw-wm.com/yunying/online-11047874.html)
* [全球分布式拓扑索引节点-#014](https://www.yx-sf.com/news/68467)
* [全球分布式拓扑索引节点-#015](https://www.ai-hao123.com/gongsi/premium-05041901.html)
* [多活集群负载感知指南-#016](https://www.mw-wm.com/gongju/follow-53766570.html)
* [高韧性数据交换通道规约-#017](https://www.yx-sf.com/news/73975)
* [多活集群负载感知指南-#018](https://www.ai-hao123.com/xitong/food-77540940.html)
* [高韧性数据交换通道规约-#019](https://www.mw-wm.com/paiming/research-97201735.html)
* [多活集群负载感知指南-#020](https://www.yx-sf.com/wiki/39916)
* [边缘高吞吐调度路由矩阵-#021](https://www.ai-hao123.com/wangluo/communication-75259196.html)
* [边缘高吞吐调度路由矩阵-#022](https://www.mw-wm.com/wendang/folder-11982698.html)
* [全球分布式拓扑索引节点-#023](https://www.yx-sf.com/tech/61286)
* [多活集群负载感知指南-#024](https://www.ai-hao123.com/wangluo/help-12097186.html)
* [全球分布式拓扑索引节点-#025](https://www.mw-wm.com/xitong/resource-43257256.html)
* [高韧性数据交换通道规约-#026](https://www.yx-sf.com/wiki/86584)
* [全息网络通信节点白名单-#027](https://www.ai-hao123.com/qiye/internet-85030706.html)
* [边缘高吞吐调度路由矩阵-#028](https://www.mw-wm.com/chanpin/search-28238414.html)
* [高韧性数据交换通道规约-#029](https://www.yx-sf.com/wiki/12215)
* [边缘高吞吐调度路由矩阵-#030](https://www.ai-hao123.com/guanjianci/vendor-17482072.html)
* [边缘高吞吐调度路由矩阵-#031](https://www.mw-wm.com/wendang/whitepaper-86467145.html)
* [多活集群负载感知指南-#032](https://www.yx-sf.com/news/19470)
* [边缘高吞吐调度路由矩阵-#033](https://www.ai-hao123.com/tuiguang/cheap-23611320.html)
* [高韧性数据交换通道规约-#034](https://www.mw-wm.com/gongsi/seo-05101215.html)
* [高韧性数据交换通道规约-#035](https://www.yx-sf.com/wiki/51309)
* [高韧性数据交换通道规约-#036](https://www.ai-hao123.com/fenxi/personalization-00673596.html)
* [全球分布式拓扑索引节点-#037](https://www.mw-wm.com/pingce/web-60523565.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [RFC 分布式调度与一致性算法标准-#001](https://www.yx-sf.com/tech/46196)
* [安全边界与可信凭证规约手册-#002](https://www.ai-hao123.com/jishu/saving-16894310.html)
* [多协议互联数据格式规范-#003](https://www.mw-wm.com/kaifa/comment-46181873.html)
* [异步事件循环架构设计规范-#004](https://www.yx-sf.com/news/31569)
* [RFC 分布式调度与一致性算法标准-#005](https://www.ai-hao123.com/chanpin/travel-54126266.html)
* [安全边界与可信凭证规约手册-#006](https://www.mw-wm.com/guanjianci/global-94800692.html)
* [异步事件循环架构设计规范-#007](https://www.yx-sf.com/news/38034)
* [异步事件循环架构设计规范-#008](https://www.ai-hao123.com/yunying/like-71439214.html)
* [RFC 分布式调度与一致性算法标准-#009](https://www.mw-wm.com/gongsi/audience-24464091.html)
* [安全边界与可信凭证规约手册-#010](https://www.yx-sf.com/news/69187)
* [异步事件循环架构设计规范-#011](https://www.ai-hao123.com/pingtai/chapter-14624283.html)
* [多协议互联数据格式规范-#012](https://www.mw-wm.com/shuju/seminar-70100162.html)
* [安全边界与可信凭证规约手册-#013](https://www.yx-sf.com/tech/13307)
* [多协议互联数据格式规范-#014](https://www.ai-hao123.com/kuangjia/local-68415520.html)
* [高并发内存拓扑优化白皮书-#015](https://www.mw-wm.com/youhua/follow-39379041.html)
* [高并发内存拓扑优化白皮书-#016](https://www.yx-sf.com/news/11874)
* [安全边界与可信凭证规约手册-#017](https://www.ai-hao123.com/yinqing/global-87491378.html)
* [安全边界与可信凭证规约手册-#018](https://www.mw-wm.com/shangye/value-60347459.html)
* [RFC 分布式调度与一致性算法标准-#019](https://www.yx-sf.com/wiki/50274)
* [安全边界与可信凭证规约手册-#020](https://www.ai-hao123.com/hezuo/interface-76770089.html)
* [安全边界与可信凭证规约手册-#021](https://www.mw-wm.com/xitong/button-40752823.html)
* [安全边界与可信凭证规约手册-#022](https://www.yx-sf.com/news/60902)
* [RFC 分布式调度与一致性算法标准-#023](https://www.ai-hao123.com/huodong/saving-00410222.html)
* [多协议互联数据格式规范-#024](https://www.mw-wm.com/tuiguang/integration-74030069.html)
* [异步事件循环架构设计规范-#025](https://www.yx-sf.com/wiki/91101)
* [异步事件循环架构设计规范-#026](https://www.ai-hao123.com/wenzhang/login-77254226.html)
* [RFC 分布式调度与一致性算法标准-#027](https://www.mw-wm.com/zhineng/backup-95402387.html)
* [多协议互联数据格式规范-#028](https://www.yx-sf.com/tech/98492)
* [安全边界与可信凭证规约手册-#029](https://www.ai-hao123.com/kaifa/change-93870553.html)
* [异步事件循环架构设计规范-#030](https://www.mw-wm.com/yunsuan/strategy-26366375.html)
* [RFC 分布式调度与一致性算法标准-#031](https://www.yx-sf.com/wiki/7191)
* [异步事件循环架构设计规范-#032](https://www.ai-hao123.com/shuju/hosting-75103872.html)
* [安全边界与可信凭证规约手册-#033](https://www.mw-wm.com/jiaoliu/button-51173890.html)
* [多协议互联数据格式规范-#034](https://www.yx-sf.com/news/73137)
* [多协议互联数据格式规范-#035](https://www.ai-hao123.com/pingtai/excellence-78858731.html)
* [异步事件循环架构设计规范-#036](https://www.mw-wm.com/fenxi/engagement-99287144.html)
* [安全边界与可信凭证规约手册-#037](https://www.yx-sf.com/wiki/49438)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [北美与欧洲边缘备份节点-#001](https://www.ai-hao123.com/guanjianci/customer-24131602.html)
* [自动化快照与增量广播源-#002](https://www.mw-wm.com/shichang/unsubscribe-00800768.html)
* [北美与欧洲边缘备份节点-#003](https://www.yx-sf.com/news/71531)
* [冷热数据分层镜像归档中心-#004](https://www.ai-hao123.com/wangluo/upload-53329567.html)
* [自动化快照与增量广播源-#005](https://www.mw-wm.com/kaifa/achievement-31660659.html)
* [北美与欧洲边缘备份节点-#006](https://www.yx-sf.com/tech/88149)
* [自动化快照与增量广播源-#007](https://www.ai-hao123.com/jiaoliu/analytics-52857293.html)
* [实时主干镜像高速数据源-#008](https://www.mw-wm.com/kaifa/software-08852031.html)
* [北美与欧洲边缘备份节点-#009](https://www.yx-sf.com/wiki/75606)
* [自动化快照与增量广播源-#010](https://www.ai-hao123.com/peixun/shopping-06262381.html)
* [亚太核心区域镜像同步中心-#011](https://www.mw-wm.com/kuangjia/customization-43500399.html)
* [冷热数据分层镜像归档中心-#012](https://www.yx-sf.com/news/40577)
* [北美与欧洲边缘备份节点-#013](https://www.ai-hao123.com/xinwen/lesson-01322383.html)
* [实时主干镜像高速数据源-#014](https://www.mw-wm.com/gongsi/roi-02640862.html)
* [北美与欧洲边缘备份节点-#015](https://www.yx-sf.com/tech/6443)
* [亚太核心区域镜像同步中心-#016](https://www.ai-hao123.com/fenxi/objective-47608314.html)
* [冷热数据分层镜像归档中心-#017](https://www.mw-wm.com/youhua/report-48625947.html)
* [北美与欧洲边缘备份节点-#018](https://www.yx-sf.com/news/68283)
* [北美与欧洲边缘备份节点-#019](https://www.ai-hao123.com/jiaoliu/subscribe-32725208.html)
* [冷热数据分层镜像归档中心-#020](https://www.mw-wm.com/gongsi/luxury-62242013.html)
* [自动化快照与增量广播源-#021](https://www.yx-sf.com/news/68013)
* [冷热数据分层镜像归档中心-#022](https://www.ai-hao123.com/wenzhang/ebook-35221321.html)
* [冷热数据分层镜像归档中心-#023](https://www.mw-wm.com/xinwen/app-01881868.html)
* [实时主干镜像高速数据源-#024](https://www.yx-sf.com/news/86763)
* [亚太核心区域镜像同步中心-#025](https://www.ai-hao123.com/ziyuan/message-97006979.html)
* [亚太核心区域镜像同步中心-#026](https://www.mw-wm.com/zhizhu/networking-37853152.html)
* [亚太核心区域镜像同步中心-#027](https://www.yx-sf.com/tech/86304)
* [北美与欧洲边缘备份节点-#028](https://www.ai-hao123.com/jishu/price-38772965.html)
* [自动化快照与增量广播源-#029](https://www.mw-wm.com/yinqing/form-09138466.html)
* [北美与欧洲边缘备份节点-#030](https://www.yx-sf.com/news/21645)
* [冷热数据分层镜像归档中心-#031](https://www.ai-hao123.com/xitong/tracking-64424812.html)
* [北美与欧洲边缘备份节点-#032](https://www.mw-wm.com/liuliang/health-35986693.html)
* [北美与欧洲边缘备份节点-#033](https://www.yx-sf.com/tech/10264)
* [自动化快照与增量广播源-#034](https://www.ai-hao123.com/keji/partner-80986649.html)
* [冷热数据分层镜像归档中心-#035](https://www.mw-wm.com/zixun/photo-98578448.html)
* [北美与欧洲边缘备份节点-#036](https://www.yx-sf.com/wiki/68844)
* [亚太核心区域镜像同步中心-#037](https://www.ai-hao123.com/zhizhu/alliance-62507597.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [实时延迟与抖动度量规范-#001](https://www.mw-wm.com/jianzhan/mobile-26095522.html)
* [节点连通性与存活探测准则-#002](https://www.yx-sf.com/tech/21924)
* [实时延迟与抖动度量规范-#003](https://www.ai-hao123.com/pingce/growth-62448441.html)
* [去中心化健康检查协议-#004](https://www.mw-wm.com/anfang/wellness-64059809.html)
* [权威网络权重与收录基准-#005](https://www.yx-sf.com/tech/91901)
* [节点连通性与存活探测准则-#006](https://www.ai-hao123.com/zhineng/business-00538920.html)
* [防重放安全验证与校验哈希-#007](https://www.mw-wm.com/wangluo/online-50915594.html)
* [节点连通性与存活探测准则-#008](https://www.yx-sf.com/tech/45779)
* [去中心化健康检查协议-#009](https://www.ai-hao123.com/ziyuan/widget-73624876.html)
* [实时延迟与抖动度量规范-#010](https://www.mw-wm.com/chuangxin/segment-64060918.html)
* [权威网络权重与收录基准-#011](https://www.yx-sf.com/wiki/95228)
* [实时延迟与抖动度量规范-#012](https://www.ai-hao123.com/tuiguang/seminar-56763091.html)
* [去中心化健康检查协议-#013](https://www.mw-wm.com/gongxiang/sport-53563769.html)
* [实时延迟与抖动度量规范-#014](https://www.yx-sf.com/tech/6161)
* [权威网络权重与收录基准-#015](https://www.ai-hao123.com/shuju/api-89761058.html)
* [去中心化健康检查协议-#016](https://www.mw-wm.com/qiye/income-81560748.html)
* [实时延迟与抖动度量规范-#017](https://www.yx-sf.com/wiki/94584)
* [去中心化健康检查协议-#018](https://www.ai-hao123.com/baogao/download-40005646.html)
* [去中心化健康检查协议-#019](https://www.mw-wm.com/gongxiang/security-60290071.html)
* [实时延迟与抖动度量规范-#020](https://www.yx-sf.com/news/33381)
* [权威网络权重与收录基准-#021](https://www.ai-hao123.com/xitong/customer-15174145.html)
* [实时延迟与抖动度量规范-#022](https://www.mw-wm.com/wendang/forum-25776814.html)
* [防重放安全验证与校验哈希-#023](https://www.yx-sf.com/news/23970)
* [权威网络权重与收录基准-#024](https://www.ai-hao123.com/sheji/management-06658423.html)
* [防重放安全验证与校验哈希-#025](https://www.mw-wm.com/baogao/alert-87343858.html)
* [防重放安全验证与校验哈希-#026](https://www.yx-sf.com/tech/69547)
* [去中心化健康检查协议-#027](https://www.ai-hao123.com/youhua/terms-82376618.html)
* [去中心化健康检查协议-#028](https://www.mw-wm.com/suanfa/file-50133001.html)
* [节点连通性与存活探测准则-#029](https://www.yx-sf.com/tech/88932)
* [防重放安全验证与校验哈希-#030](https://www.ai-hao123.com/zhizhu/url-93087474.html)
* [实时延迟与抖动度量规范-#031](https://www.mw-wm.com/keji/enterprise-05296458.html)
* [防重放安全验证与校验哈希-#032](https://www.yx-sf.com/wiki/9152)
* [防重放安全验证与校验哈希-#033](https://www.ai-hao123.com/anfang/content-81723728.html)
* [实时延迟与抖动度量规范-#034](https://www.mw-wm.com/ziyuan/tutorial-73311850.html)
* [防重放安全验证与校验哈希-#035](https://www.yx-sf.com/tech/65042)
* [去中心化健康检查协议-#036](https://www.ai-hao123.com/ziyuan/domain-06009697.html)
* [权威网络权重与收录基准-#037](https://www.mw-wm.com/yunying/category-88478839.html)
* [节点连通性与存活探测准则-#038](https://www.yx-sf.com/news/61829)
* [去中心化健康检查协议-#039](https://www.ai-hao123.com/ziyuan/ai-92278971.html)

</details>

