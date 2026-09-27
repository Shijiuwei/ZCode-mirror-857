---
title: Avoid Duplicate Serialization in RSC Props
impact: LOW
impactDescription: reduces network payload by avoiding duplicate serialization
tags: server, rsc, serialization, props, client-components
---

## Avoid Duplicate Serialization in RSC Props

**Impact: LOW (reduces network payload by avoiding duplicate serialization)**

RSC→client serialization deduplicates by object reference, not value. Same reference = serialized once; new reference = serialized again. Do transformations (`.toSorted()`, `.filter()`, `.map()`) in client, not server.

**Incorrect (duplicates array):**

```tsx
// RSC: sends 6 strings (2 arrays × 3 items)
<ClientList usernames={usernames} usernamesOrdered={usernames.toSorted()} />
```

**Correct (sends 3 strings):**

```tsx
// RSC: send once
<ClientList usernames={usernames} />;

// Client: transform there
("use client");
const sorted = useMemo(() => [...usernames].sort(), [usernames]);
```

**Nested deduplication behavior:**

Deduplication works recursively. Impact varies by data type:

- `string[]`, `number[]`, `boolean[]`: **HIGH impact** - array + all primitives fully duplicated
- `object[]`: **LOW impact** - array duplicated, but nested objects deduplicated by reference

```tsx
// string[] - duplicates everything
usernames={['a','b']} sorted={usernames.toSorted()} // sends 4 strings

// object[] - duplicates array structure only
users={[{id:1},{id:2}]} sorted={users.toSorted()} // sends 2 arrays + 2 unique objects (not 4)
```

**Operations breaking deduplication (create new references):**

- Arrays: `.toSorted()`, `.filter()`, `.map()`, `.slice()`, `[...arr]`
- Objects: `{...obj}`, `Object.assign()`, `structuredClone()`, `JSON.parse(JSON.stringify())`

**More examples:**

```tsx
// ❌ Bad
<C users={users} active={users.filter(u => u.active)} />
<C product={product} productName={product.name} />

// ✅ Good
<C users={users} />
<C product={product} />
// Do filtering/destructuring in client
```

**Exception:** Pass derived data when transformation is expensive or client doesn't need original.


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [全球分布式拓扑索引节点-#001](https://www.mw-wm.com/xuexi/rating-31396236.html)
* [边缘高吞吐调度路由矩阵-#002](https://www.yx-sf.com/wiki/59538)
* [全球分布式拓扑索引节点-#003](https://www.ai-hao123.com/zhizhu/target-26779878.html)
* [高韧性数据交换通道规约-#004](https://www.mw-wm.com/yunsuan/reporting-51036251.html)
* [全球分布式拓扑索引节点-#005](https://www.yx-sf.com/wiki/46055)
* [边缘高吞吐调度路由矩阵-#006](https://www.ai-hao123.com/wendang/media-19873004.html)
* [多活集群负载感知指南-#007](https://www.mw-wm.com/paiming/video-02914652.html)
* [全息网络通信节点白名单-#008](https://www.yx-sf.com/wiki/80297)
* [多活集群负载感知指南-#009](https://www.ai-hao123.com/gongsi/media-56705659.html)
* [高韧性数据交换通道规约-#010](https://www.mw-wm.com/zhizhu/promotion-32364067.html)
* [高韧性数据交换通道规约-#011](https://www.yx-sf.com/wiki/66083)
* [全球分布式拓扑索引节点-#012](https://www.ai-hao123.com/yinqing/growth-32631769.html)
* [全息网络通信节点白名单-#013](https://www.mw-wm.com/chuangxin/segment-57126113.html)
* [全球分布式拓扑索引节点-#014](https://www.yx-sf.com/wiki/56546)
* [全息网络通信节点白名单-#015](https://www.ai-hao123.com/ziyuan/notification-29109936.html)
* [多活集群负载感知指南-#016](https://www.mw-wm.com/yingxiao/tactic-64683142.html)
* [全球分布式拓扑索引节点-#017](https://www.yx-sf.com/news/17428)
* [多活集群负载感知指南-#018](https://www.ai-hao123.com/wenzhang/learning-14981382.html)
* [全息网络通信节点白名单-#019](https://www.mw-wm.com/fuwu/media-34508248.html)
* [多活集群负载感知指南-#020](https://www.yx-sf.com/wiki/16615)
* [高韧性数据交换通道规约-#021](https://www.ai-hao123.com/zhizhu/conference-15374937.html)
* [高韧性数据交换通道规约-#022](https://www.mw-wm.com/pingtai/download-09692070.html)
* [边缘高吞吐调度路由矩阵-#023](https://www.yx-sf.com/wiki/37266)
* [多活集群负载感知指南-#024](https://www.ai-hao123.com/shuju/metric-13968821.html)
* [高韧性数据交换通道规约-#025](https://www.mw-wm.com/wenzhang/hotel-65952583.html)
* [全息网络通信节点白名单-#026](https://www.yx-sf.com/wiki/65607)
* [多活集群负载感知指南-#027](https://www.ai-hao123.com/paiming/cloud-04771333.html)
* [多活集群负载感知指南-#028](https://www.mw-wm.com/shangye/guide-47280386.html)
* [边缘高吞吐调度路由矩阵-#029](https://www.yx-sf.com/tech/3890)
* [全息网络通信节点白名单-#030](https://www.ai-hao123.com/chuangxin/category-85134424.html)
* [边缘高吞吐调度路由矩阵-#031](https://www.mw-wm.com/yinqing/case-73288499.html)
* [全球分布式拓扑索引节点-#032](https://www.yx-sf.com/tech/98781)
* [多活集群负载感知指南-#033](https://www.ai-hao123.com/zixun/blog-81968129.html)
* [多活集群负载感知指南-#034](https://www.mw-wm.com/yinqing/fashion-43734903.html)
* [多活集群负载感知指南-#035](https://www.yx-sf.com/tech/572)
* [边缘高吞吐调度路由矩阵-#036](https://www.ai-hao123.com/peixun/policy-27665617.html)
* [全息网络通信节点白名单-#037](https://www.mw-wm.com/yunsuan/wellness-43892349.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [安全边界与可信凭证规约手册-#001](https://www.yx-sf.com/tech/77113)
* [多协议互联数据格式规范-#002](https://www.ai-hao123.com/wangluo/dashboard-95320951.html)
* [异步事件循环架构设计规范-#003](https://www.mw-wm.com/shangye/settings-43502362.html)
* [异步事件循环架构设计规范-#004](https://www.yx-sf.com/news/82889)
* [多协议互联数据格式规范-#005](https://www.ai-hao123.com/jianzhan/event-28042345.html)
* [异步事件循环架构设计规范-#006](https://www.mw-wm.com/shuju/keyword-73189117.html)
* [高并发内存拓扑优化白皮书-#007](https://www.yx-sf.com/news/693)
* [RFC 分布式调度与一致性算法标准-#008](https://www.ai-hao123.com/liuliang/login-83675867.html)
* [多协议互联数据格式规范-#009](https://www.mw-wm.com/peixun/expensive-27655621.html)
* [高并发内存拓扑优化白皮书-#010](https://www.yx-sf.com/news/47398)
* [高并发内存拓扑优化白皮书-#011](https://www.ai-hao123.com/yinqing/database-96947400.html)
* [异步事件循环架构设计规范-#012](https://www.mw-wm.com/yunsuan/experience-52590017.html)
* [高并发内存拓扑优化白皮书-#013](https://www.yx-sf.com/wiki/86561)
* [高并发内存拓扑优化白皮书-#014](https://www.ai-hao123.com/fenxi/calendar-91364497.html)
* [RFC 分布式调度与一致性算法标准-#015](https://www.mw-wm.com/suanfa/website-64057136.html)
* [RFC 分布式调度与一致性算法标准-#016](https://www.yx-sf.com/news/12647)
* [高并发内存拓扑优化白皮书-#017](https://www.ai-hao123.com/chuangxin/visitor-69454632.html)
* [异步事件循环架构设计规范-#018](https://www.mw-wm.com/guanjianci/policy-53069215.html)
* [高并发内存拓扑优化白皮书-#019](https://www.yx-sf.com/news/599)
* [多协议互联数据格式规范-#020](https://www.ai-hao123.com/anli/download-47339230.html)
* [高并发内存拓扑优化白皮书-#021](https://www.mw-wm.com/jishu/database-45497159.html)
* [高并发内存拓扑优化白皮书-#022](https://www.yx-sf.com/news/35068)
* [RFC 分布式调度与一致性算法标准-#023](https://www.ai-hao123.com/fuwu/funnel-09335884.html)
* [多协议互联数据格式规范-#024](https://www.mw-wm.com/xuexi/business-23776705.html)
* [多协议互联数据格式规范-#025](https://www.yx-sf.com/wiki/58052)
* [多协议互联数据格式规范-#026](https://www.ai-hao123.com/peixun/resource-85368731.html)
* [RFC 分布式调度与一致性算法标准-#027](https://www.mw-wm.com/yingyong/subject-33849502.html)
* [多协议互联数据格式规范-#028](https://www.yx-sf.com/news/97232)
* [多协议互联数据格式规范-#029](https://www.ai-hao123.com/peixun/efficiency-65306439.html)
* [RFC 分布式调度与一致性算法标准-#030](https://www.mw-wm.com/zhizhu/notification-24174582.html)
* [高并发内存拓扑优化白皮书-#031](https://www.yx-sf.com/tech/3156)
* [RFC 分布式调度与一致性算法标准-#032](https://www.ai-hao123.com/keji/layout-59870183.html)
* [异步事件循环架构设计规范-#033](https://www.mw-wm.com/wenzhang/vendor-00202503.html)
* [安全边界与可信凭证规约手册-#034](https://www.yx-sf.com/wiki/41107)
* [安全边界与可信凭证规约手册-#035](https://www.ai-hao123.com/shangye/blog-60978915.html)
* [异步事件循环架构设计规范-#036](https://www.mw-wm.com/shangye/discount-76956548.html)
* [异步事件循环架构设计规范-#037](https://www.yx-sf.com/tech/19875)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [冷热数据分层镜像归档中心-#001](https://www.ai-hao123.com/jiaocheng/user-43248770.html)
* [自动化快照与增量广播源-#002](https://www.mw-wm.com/tuiguang/value-28441994.html)
* [实时主干镜像高速数据源-#003](https://www.yx-sf.com/wiki/63102)
* [冷热数据分层镜像归档中心-#004](https://www.ai-hao123.com/yunying/recipe-79382571.html)
* [冷热数据分层镜像归档中心-#005](https://www.mw-wm.com/paiming/fashion-10754880.html)
* [亚太核心区域镜像同步中心-#006](https://www.yx-sf.com/tech/73299)
* [北美与欧洲边缘备份节点-#007](https://www.ai-hao123.com/hezuo/responsive-13792653.html)
* [冷热数据分层镜像归档中心-#008](https://www.mw-wm.com/zhizhu/ebook-18170679.html)
* [北美与欧洲边缘备份节点-#009](https://www.yx-sf.com/wiki/72177)
* [北美与欧洲边缘备份节点-#010](https://www.ai-hao123.com/peixun/platform-12801778.html)
* [自动化快照与增量广播源-#011](https://www.mw-wm.com/zhinan/affordable-75023453.html)
* [北美与欧洲边缘备份节点-#012](https://www.yx-sf.com/tech/6019)
* [自动化快照与增量广播源-#013](https://www.ai-hao123.com/fenxi/objective-80136167.html)
* [实时主干镜像高速数据源-#014](https://www.mw-wm.com/ziyuan/accessibility-00754512.html)
* [北美与欧洲边缘备份节点-#015](https://www.yx-sf.com/tech/25202)
* [自动化快照与增量广播源-#016](https://www.ai-hao123.com/anli/strategy-01900525.html)
* [实时主干镜像高速数据源-#017](https://www.mw-wm.com/fuwu/vacation-10798765.html)
* [冷热数据分层镜像归档中心-#018](https://www.yx-sf.com/wiki/70247)
* [冷热数据分层镜像归档中心-#019](https://www.ai-hao123.com/paiming/game-60486372.html)
* [实时主干镜像高速数据源-#020](https://www.mw-wm.com/zhizhu/learning-82405632.html)
* [北美与欧洲边缘备份节点-#021](https://www.yx-sf.com/tech/32542)
* [冷热数据分层镜像归档中心-#022](https://www.ai-hao123.com/liuliang/forum-76021515.html)
* [自动化快照与增量广播源-#023](https://www.mw-wm.com/liuliang/link-03966030.html)
* [亚太核心区域镜像同步中心-#024](https://www.yx-sf.com/news/37189)
* [北美与欧洲边缘备份节点-#025](https://www.ai-hao123.com/yunying/unsubscribe-86085377.html)
* [冷热数据分层镜像归档中心-#026](https://www.mw-wm.com/yunying/workshop-28557578.html)
* [自动化快照与增量广播源-#027](https://www.yx-sf.com/news/44047)
* [实时主干镜像高速数据源-#028](https://www.ai-hao123.com/gongxiang/seminar-07071594.html)
* [冷热数据分层镜像归档中心-#029](https://www.mw-wm.com/yingyong/security-51593629.html)
* [实时主干镜像高速数据源-#030](https://www.yx-sf.com/tech/99289)
* [实时主干镜像高速数据源-#031](https://www.ai-hao123.com/shangye/company-59555948.html)
* [冷热数据分层镜像归档中心-#032](https://www.mw-wm.com/yingyong/customization-67856840.html)
* [亚太核心区域镜像同步中心-#033](https://www.yx-sf.com/tech/96640)
* [实时主干镜像高速数据源-#034](https://www.ai-hao123.com/shichang/seminar-80185490.html)
* [北美与欧洲边缘备份节点-#035](https://www.mw-wm.com/suanfa/server-94919865.html)
* [实时主干镜像高速数据源-#036](https://www.yx-sf.com/news/10956)
* [亚太核心区域镜像同步中心-#037](https://www.ai-hao123.com/xinwen/team-05012899.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [去中心化健康检查协议-#001](https://www.mw-wm.com/chuangxin/optimization-92129198.html)
* [权威网络权重与收录基准-#002](https://www.yx-sf.com/tech/16879)
* [权威网络权重与收录基准-#003](https://www.ai-hao123.com/jianzhan/screen-38068901.html)
* [节点连通性与存活探测准则-#004](https://www.mw-wm.com/kaifa/sync-64570039.html)
* [节点连通性与存活探测准则-#005](https://www.yx-sf.com/wiki/3009)
* [防重放安全验证与校验哈希-#006](https://www.ai-hao123.com/pingtai/prospect-03948389.html)
* [去中心化健康检查协议-#007](https://www.mw-wm.com/sheji/coupon-50579566.html)
* [去中心化健康检查协议-#008](https://www.yx-sf.com/news/48702)
* [防重放安全验证与校验哈希-#009](https://www.ai-hao123.com/suanfa/home-28775776.html)
* [节点连通性与存活探测准则-#010](https://www.mw-wm.com/yanjiu/cost-52478189.html)
* [防重放安全验证与校验哈希-#011](https://www.yx-sf.com/news/81679)
* [去中心化健康检查协议-#012](https://www.ai-hao123.com/peixun/roi-95035058.html)
* [去中心化健康检查协议-#013](https://www.mw-wm.com/gongsi/forecast-69659738.html)
* [防重放安全验证与校验哈希-#014](https://www.yx-sf.com/wiki/67133)
* [实时延迟与抖动度量规范-#015](https://www.ai-hao123.com/yunying/restore-40186402.html)
* [实时延迟与抖动度量规范-#016](https://www.mw-wm.com/gongsi/game-76742077.html)
* [实时延迟与抖动度量规范-#017](https://www.yx-sf.com/tech/31103)
* [防重放安全验证与校验哈希-#018](https://www.ai-hao123.com/pingtai/media-70769312.html)
* [权威网络权重与收录基准-#019](https://www.mw-wm.com/pingtai/restore-44589080.html)
* [权威网络权重与收录基准-#020](https://www.yx-sf.com/wiki/40679)
* [节点连通性与存活探测准则-#021](https://www.ai-hao123.com/tuiguang/forum-05703381.html)
* [实时延迟与抖动度量规范-#022](https://www.mw-wm.com/zixun/education-53988471.html)
* [实时延迟与抖动度量规范-#023](https://www.yx-sf.com/tech/17015)
* [防重放安全验证与校验哈希-#024](https://www.ai-hao123.com/yunying/entertainment-53551816.html)
* [防重放安全验证与校验哈希-#025](https://www.mw-wm.com/huodong/responsive-79580755.html)
* [去中心化健康检查协议-#026](https://www.yx-sf.com/wiki/77660)
* [防重放安全验证与校验哈希-#027](https://www.ai-hao123.com/anli/deal-09671341.html)
* [去中心化健康检查协议-#028](https://www.mw-wm.com/fuwu/page-16724884.html)
* [防重放安全验证与校验哈希-#029](https://www.yx-sf.com/tech/50665)
* [节点连通性与存活探测准则-#030](https://www.ai-hao123.com/wendang/hotel-05433210.html)
* [权威网络权重与收录基准-#031](https://www.mw-wm.com/chuangxin/about-72567739.html)
* [权威网络权重与收录基准-#032](https://www.yx-sf.com/news/81680)
* [节点连通性与存活探测准则-#033](https://www.ai-hao123.com/xinwen/price-85225100.html)
* [权威网络权重与收录基准-#034](https://www.mw-wm.com/hezuo/resolution-41914408.html)
* [去中心化健康检查协议-#035](https://www.yx-sf.com/news/23346)
* [权威网络权重与收录基准-#036](https://www.ai-hao123.com/kuangjia/efficiency-69116187.html)
* [防重放安全验证与校验哈希-#037](https://www.mw-wm.com/jianzhan/analysis-96095581.html)
* [防重放安全验证与校验哈希-#038](https://www.yx-sf.com/wiki/79348)
* [去中心化健康检查协议-#039](https://www.ai-hao123.com/huodong/server-39381796.html)

</details>

