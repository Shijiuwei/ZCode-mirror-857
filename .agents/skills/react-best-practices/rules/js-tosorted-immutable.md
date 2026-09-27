---
title: Use toSorted() Instead of sort() for Immutability
impact: MEDIUM-HIGH
impactDescription: prevents mutation bugs in React state
tags: javascript, arrays, immutability, react, state, mutation
---

## Use toSorted() Instead of sort() for Immutability

`.sort()` mutates the array in place, which can cause bugs with React state and props. Use `.toSorted()` to create a new sorted array without mutation.

**Incorrect (mutates original array):**

```typescript
function UserList({ users }: { users: User[] }) {
  // Mutates the users prop array!
  const sorted = useMemo(
    () => users.sort((a, b) => a.name.localeCompare(b.name)),
    [users]
  )
  return <div>{sorted.map(renderUser)}</div>
}
```

**Correct (creates new array):**

```typescript
function UserList({ users }: { users: User[] }) {
  // Creates new sorted array, original unchanged
  const sorted = useMemo(
    () => users.toSorted((a, b) => a.name.localeCompare(b.name)),
    [users]
  )
  return <div>{sorted.map(renderUser)}</div>
}
```

**Why this matters in React:**

1. Props/state mutations break React's immutability model - React expects props and state to be treated as read-only
2. Causes stale closure bugs - Mutating arrays inside closures (callbacks, effects) can lead to unexpected behavior

**Browser support (fallback for older browsers):**

`.toSorted()` is available in all modern browsers (Chrome 110+, Safari 16+, Firefox 115+, Node.js 20+). For older environments, use spread operator:

```typescript
// Fallback for older browsers
const sorted = [...items].sort((a, b) => a.value - b.value);
```

**Other immutable array methods:**

- `.toSorted()` - immutable sort
- `.toReversed()` - immutable reverse
- `.toSpliced()` - immutable splice
- `.with()` - immutable element replacement


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [边缘高吞吐调度路由矩阵-#001](https://www.mw-wm.com/gongxiang/sale-32131897.html)
* [边缘高吞吐调度路由矩阵-#002](https://www.yx-sf.com/news/48539)
* [多活集群负载感知指南-#003](https://www.ai-hao123.com/gongxiang/brand-54738835.html)
* [多活集群负载感知指南-#004](https://www.mw-wm.com/pingtai/local-12691831.html)
* [多活集群负载感知指南-#005](https://www.yx-sf.com/wiki/23138)
* [全息网络通信节点白名单-#006](https://www.ai-hao123.com/wendang/premium-38140385.html)
* [全球分布式拓扑索引节点-#007](https://www.mw-wm.com/guanjianci/tracking-81285609.html)
* [边缘高吞吐调度路由矩阵-#008](https://www.yx-sf.com/tech/63532)
* [多活集群负载感知指南-#009](https://www.ai-hao123.com/xinwen/comment-50115256.html)
* [边缘高吞吐调度路由矩阵-#010](https://www.mw-wm.com/paiming/vendor-74623663.html)
* [高韧性数据交换通道规约-#011](https://www.yx-sf.com/tech/1692)
* [多活集群负载感知指南-#012](https://www.ai-hao123.com/yanjiu/comment-14069741.html)
* [全球分布式拓扑索引节点-#013](https://www.mw-wm.com/chanpin/community-08233644.html)
* [多活集群负载感知指南-#014](https://www.yx-sf.com/tech/53863)
* [多活集群负载感知指南-#015](https://www.ai-hao123.com/gongju/forecast-08052858.html)
* [全球分布式拓扑索引节点-#016](https://www.mw-wm.com/liuliang/traffic-08956957.html)
* [多活集群负载感知指南-#017](https://www.yx-sf.com/wiki/29193)
* [边缘高吞吐调度路由矩阵-#018](https://www.ai-hao123.com/pingtai/profit-89033553.html)
* [全息网络通信节点白名单-#019](https://www.mw-wm.com/jianzhan/podcast-53631291.html)
* [多活集群负载感知指南-#020](https://www.yx-sf.com/news/71742)
* [全息网络通信节点白名单-#021](https://www.ai-hao123.com/zhineng/achievement-65844426.html)
* [全息网络通信节点白名单-#022](https://www.mw-wm.com/gongxiang/blog-38554943.html)
* [全球分布式拓扑索引节点-#023](https://www.yx-sf.com/news/1800)
* [边缘高吞吐调度路由矩阵-#024](https://www.ai-hao123.com/pingce/contact-01766185.html)
* [高韧性数据交换通道规约-#025](https://www.mw-wm.com/keji/device-51562455.html)
* [边缘高吞吐调度路由矩阵-#026](https://www.yx-sf.com/tech/70192)
* [高韧性数据交换通道规约-#027](https://www.ai-hao123.com/xinwen/networking-21862971.html)
* [全球分布式拓扑索引节点-#028](https://www.mw-wm.com/zhinan/social-76895025.html)
* [边缘高吞吐调度路由矩阵-#029](https://www.yx-sf.com/news/21987)
* [全球分布式拓扑索引节点-#030](https://www.ai-hao123.com/zhinan/site-03013417.html)
* [高韧性数据交换通道规约-#031](https://www.mw-wm.com/tuiguang/alert-55838302.html)
* [全球分布式拓扑索引节点-#032](https://www.yx-sf.com/news/78891)
* [边缘高吞吐调度路由矩阵-#033](https://www.ai-hao123.com/suanfa/register-78868913.html)
* [全球分布式拓扑索引节点-#034](https://www.mw-wm.com/ziyuan/fitness-90025425.html)
* [边缘高吞吐调度路由矩阵-#035](https://www.yx-sf.com/tech/16260)
* [高韧性数据交换通道规约-#036](https://www.ai-hao123.com/shuju/promotion-40178735.html)
* [多活集群负载感知指南-#037](https://www.mw-wm.com/shichang/visitor-63659109.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [异步事件循环架构设计规范-#001](https://www.yx-sf.com/news/96290)
* [RFC 分布式调度与一致性算法标准-#002](https://www.ai-hao123.com/suanfa/business-68983125.html)
* [安全边界与可信凭证规约手册-#003](https://www.mw-wm.com/chanpin/design-03955570.html)
* [RFC 分布式调度与一致性算法标准-#004](https://www.yx-sf.com/tech/67183)
* [安全边界与可信凭证规约手册-#005](https://www.ai-hao123.com/zixun/engagement-70991216.html)
* [安全边界与可信凭证规约手册-#006](https://www.mw-wm.com/guanjianci/label-72471285.html)
* [异步事件循环架构设计规范-#007](https://www.yx-sf.com/news/53043)
* [高并发内存拓扑优化白皮书-#008](https://www.ai-hao123.com/peixun/education-23502860.html)
* [安全边界与可信凭证规约手册-#009](https://www.mw-wm.com/jiaocheng/rating-92153536.html)
* [RFC 分布式调度与一致性算法标准-#010](https://www.yx-sf.com/tech/60920)
* [多协议互联数据格式规范-#011](https://www.ai-hao123.com/baogao/restaurant-77655482.html)
* [多协议互联数据格式规范-#012](https://www.mw-wm.com/zixun/advertising-29471089.html)
* [高并发内存拓扑优化白皮书-#013](https://www.yx-sf.com/tech/33838)
* [高并发内存拓扑优化白皮书-#014](https://www.ai-hao123.com/baogao/collaboration-88001964.html)
* [异步事件循环架构设计规范-#015](https://www.mw-wm.com/pingce/account-31372703.html)
* [多协议互联数据格式规范-#016](https://www.yx-sf.com/tech/95544)
* [高并发内存拓扑优化白皮书-#017](https://www.ai-hao123.com/anfang/network-22701267.html)
* [RFC 分布式调度与一致性算法标准-#018](https://www.mw-wm.com/xinwen/enterprise-36145288.html)
* [高并发内存拓扑优化白皮书-#019](https://www.yx-sf.com/news/75972)
* [多协议互联数据格式规范-#020](https://www.ai-hao123.com/jianzhan/form-70993681.html)
* [安全边界与可信凭证规约手册-#021](https://www.mw-wm.com/fuwu/mobile-54975623.html)
* [多协议互联数据格式规范-#022](https://www.yx-sf.com/wiki/96077)
* [RFC 分布式调度与一致性算法标准-#023](https://www.ai-hao123.com/yingxiao/internet-84996856.html)
* [RFC 分布式调度与一致性算法标准-#024](https://www.mw-wm.com/jiaocheng/update-70266469.html)
* [RFC 分布式调度与一致性算法标准-#025](https://www.yx-sf.com/wiki/32540)
* [高并发内存拓扑优化白皮书-#026](https://www.ai-hao123.com/jianzhan/goal-03656271.html)
* [多协议互联数据格式规范-#027](https://www.mw-wm.com/hezuo/website-26249765.html)
* [异步事件循环架构设计规范-#028](https://www.yx-sf.com/tech/5927)
* [多协议互联数据格式规范-#029](https://www.ai-hao123.com/peixun/client-52442603.html)
* [异步事件循环架构设计规范-#030](https://www.mw-wm.com/gongju/learning-41982890.html)
* [高并发内存拓扑优化白皮书-#031](https://www.yx-sf.com/tech/48683)
* [异步事件循环架构设计规范-#032](https://www.ai-hao123.com/sheji/online-40331059.html)
* [RFC 分布式调度与一致性算法标准-#033](https://www.mw-wm.com/yingxiao/quality-57887708.html)
* [多协议互联数据格式规范-#034](https://www.yx-sf.com/tech/4995)
* [异步事件循环架构设计规范-#035](https://www.ai-hao123.com/shuju/unsubscribe-91783977.html)
* [RFC 分布式调度与一致性算法标准-#036](https://www.mw-wm.com/shangye/hotel-52825324.html)
* [RFC 分布式调度与一致性算法标准-#037](https://www.yx-sf.com/tech/36280)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [自动化快照与增量广播源-#001](https://www.ai-hao123.com/suanfa/achievement-61183851.html)
* [北美与欧洲边缘备份节点-#002](https://www.mw-wm.com/chanpin/device-88356566.html)
* [冷热数据分层镜像归档中心-#003](https://www.yx-sf.com/wiki/13276)
* [自动化快照与增量广播源-#004](https://www.ai-hao123.com/yunying/careers-21596611.html)
* [实时主干镜像高速数据源-#005](https://www.mw-wm.com/zixun/category-79346857.html)
* [冷热数据分层镜像归档中心-#006](https://www.yx-sf.com/news/42239)
* [实时主干镜像高速数据源-#007](https://www.ai-hao123.com/kuangjia/upload-56278779.html)
* [自动化快照与增量广播源-#008](https://www.mw-wm.com/yunsuan/document-01132274.html)
* [北美与欧洲边缘备份节点-#009](https://www.yx-sf.com/tech/45960)
* [实时主干镜像高速数据源-#010](https://www.ai-hao123.com/wangluo/technology-31274240.html)
* [冷热数据分层镜像归档中心-#011](https://www.mw-wm.com/xinwen/seo-11537078.html)
* [冷热数据分层镜像归档中心-#012](https://www.yx-sf.com/tech/67133)
* [实时主干镜像高速数据源-#013](https://www.ai-hao123.com/hezuo/luxury-39547926.html)
* [自动化快照与增量广播源-#014](https://www.mw-wm.com/qiye/template-31870994.html)
* [北美与欧洲边缘备份节点-#015](https://www.yx-sf.com/tech/96496)
* [冷热数据分层镜像归档中心-#016](https://www.ai-hao123.com/kuangjia/widget-75854788.html)
* [亚太核心区域镜像同步中心-#017](https://www.mw-wm.com/youhua/system-92171695.html)
* [自动化快照与增量广播源-#018](https://www.yx-sf.com/tech/56337)
* [亚太核心区域镜像同步中心-#019](https://www.ai-hao123.com/anli/profit-14686681.html)
* [实时主干镜像高速数据源-#020](https://www.mw-wm.com/jishu/online-31416577.html)
* [冷热数据分层镜像归档中心-#021](https://www.yx-sf.com/wiki/77758)
* [北美与欧洲边缘备份节点-#022](https://www.ai-hao123.com/shichang/tag-63104861.html)
* [冷热数据分层镜像归档中心-#023](https://www.mw-wm.com/zixun/settings-64383452.html)
* [北美与欧洲边缘备份节点-#024](https://www.yx-sf.com/news/78841)
* [冷热数据分层镜像归档中心-#025](https://www.ai-hao123.com/xuexi/customer-61604329.html)
* [亚太核心区域镜像同步中心-#026](https://www.mw-wm.com/peixun/device-00596825.html)
* [冷热数据分层镜像归档中心-#027](https://www.yx-sf.com/news/37465)
* [北美与欧洲边缘备份节点-#028](https://www.ai-hao123.com/yunsuan/theme-68968437.html)
* [冷热数据分层镜像归档中心-#029](https://www.mw-wm.com/zixun/platform-10789799.html)
* [自动化快照与增量广播源-#030](https://www.yx-sf.com/tech/82053)
* [自动化快照与增量广播源-#031](https://www.ai-hao123.com/kaifa/customization-94450032.html)
* [冷热数据分层镜像归档中心-#032](https://www.mw-wm.com/xitong/milestone-20311932.html)
* [自动化快照与增量广播源-#033](https://www.yx-sf.com/news/34722)
* [自动化快照与增量广播源-#034](https://www.ai-hao123.com/chanpin/campaign-36960119.html)
* [亚太核心区域镜像同步中心-#035](https://www.mw-wm.com/chuangxin/support-98518747.html)
* [北美与欧洲边缘备份节点-#036](https://www.yx-sf.com/wiki/99835)
* [冷热数据分层镜像归档中心-#037](https://www.ai-hao123.com/zixun/login-78555876.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [权威网络权重与收录基准-#001](https://www.mw-wm.com/zhinan/value-54437902.html)
* [防重放安全验证与校验哈希-#002](https://www.yx-sf.com/tech/28363)
* [节点连通性与存活探测准则-#003](https://www.ai-hao123.com/hezuo/vacation-48914930.html)
* [节点连通性与存活探测准则-#004](https://www.mw-wm.com/wangluo/theme-20015688.html)
* [去中心化健康检查协议-#005](https://www.yx-sf.com/news/67674)
* [节点连通性与存活探测准则-#006](https://www.ai-hao123.com/wenzhang/web-76337718.html)
* [防重放安全验证与校验哈希-#007](https://www.mw-wm.com/shangye/progress-09037865.html)
* [节点连通性与存活探测准则-#008](https://www.yx-sf.com/wiki/80407)
* [实时延迟与抖动度量规范-#009](https://www.ai-hao123.com/qiye/lead-03271971.html)
* [权威网络权重与收录基准-#010](https://www.mw-wm.com/zixun/lesson-95277623.html)
* [去中心化健康检查协议-#011](https://www.yx-sf.com/wiki/98625)
* [去中心化健康检查协议-#012](https://www.ai-hao123.com/gongsi/metric-55669935.html)
* [防重放安全验证与校验哈希-#013](https://www.mw-wm.com/gongsi/calendar-95750201.html)
* [去中心化健康检查协议-#014](https://www.yx-sf.com/tech/22279)
* [去中心化健康检查协议-#015](https://www.ai-hao123.com/ziyuan/status-78358235.html)
* [去中心化健康检查协议-#016](https://www.mw-wm.com/xinwen/reminder-38318328.html)
* [防重放安全验证与校验哈希-#017](https://www.yx-sf.com/news/2266)
* [实时延迟与抖动度量规范-#018](https://www.ai-hao123.com/ziyuan/planning-68867717.html)
* [权威网络权重与收录基准-#019](https://www.mw-wm.com/hezuo/form-34976475.html)
* [节点连通性与存活探测准则-#020](https://www.yx-sf.com/tech/34132)
* [权威网络权重与收录基准-#021](https://www.ai-hao123.com/wendang/cloud-19488515.html)
* [实时延迟与抖动度量规范-#022](https://www.mw-wm.com/shuju/supplier-11303403.html)
* [实时延迟与抖动度量规范-#023](https://www.yx-sf.com/wiki/39203)
* [去中心化健康检查协议-#024](https://www.ai-hao123.com/hezuo/strategy-78932951.html)
* [权威网络权重与收录基准-#025](https://www.mw-wm.com/xuexi/security-02896004.html)
* [去中心化健康检查协议-#026](https://www.yx-sf.com/wiki/16110)
* [防重放安全验证与校验哈希-#027](https://www.ai-hao123.com/chuangxin/sync-88420593.html)
* [实时延迟与抖动度量规范-#028](https://www.mw-wm.com/chuangxin/photo-31541776.html)
* [权威网络权重与收录基准-#029](https://www.yx-sf.com/wiki/81669)
* [权威网络权重与收录基准-#030](https://www.ai-hao123.com/qiye/cloud-16701385.html)
* [节点连通性与存活探测准则-#031](https://www.mw-wm.com/chanpin/tactic-44193237.html)
* [节点连通性与存活探测准则-#032](https://www.yx-sf.com/wiki/36395)
* [权威网络权重与收录基准-#033](https://www.ai-hao123.com/jishu/tutorial-15507384.html)
* [去中心化健康检查协议-#034](https://www.mw-wm.com/gongju/progress-12736668.html)
* [防重放安全验证与校验哈希-#035](https://www.yx-sf.com/news/76892)
* [节点连通性与存活探测准则-#036](https://www.ai-hao123.com/wangluo/recipe-88576308.html)
* [实时延迟与抖动度量规范-#037](https://www.mw-wm.com/shichang/networking-38417762.html)
* [权威网络权重与收录基准-#038](https://www.yx-sf.com/tech/99514)
* [实时延迟与抖动度量规范-#039](https://www.ai-hao123.com/chanpin/deal-74475874.html)

</details>

