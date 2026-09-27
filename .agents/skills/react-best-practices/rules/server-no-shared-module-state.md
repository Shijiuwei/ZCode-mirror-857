---
title: Avoid Shared Module State for Request Data
impact: HIGH
impactDescription: prevents concurrency bugs and request data leaks
tags: server, rsc, ssr, concurrency, security, state
---

## Avoid Shared Module State for Request Data

For React Server Components and client components rendered during SSR, avoid using mutable module-level variables to share request-scoped data. Server renders can run concurrently in the same process. If one render writes to shared module state and another render reads it, you can get race conditions, cross-request contamination, and security bugs where one user's data appears in another user's response.

Treat module scope on the server as process-wide shared memory, not request-local state.

**Incorrect (request data leaks across concurrent renders):**

```tsx
let currentUser: User | null = null;

export default async function Page() {
  currentUser = await auth();
  return <Dashboard />;
}

async function Dashboard() {
  return <div>{currentUser?.name}</div>;
}
```

If two requests overlap, request A can set `currentUser`, then request B overwrites it before request A finishes rendering `Dashboard`.

**Correct (keep request data local to the render tree):**

```tsx
export default async function Page() {
  const user = await auth();
  return <Dashboard user={user} />;
}

function Dashboard({ user }: { user: User | null }) {
  return <div>{user?.name}</div>;
}
```

Safe exceptions:

- Immutable static assets or config loaded once at module scope
- Shared caches intentionally designed for cross-request reuse and keyed correctly
- Process-wide singletons that do not store request- or user-specific mutable data

For static assets and config, see [Hoist Static I/O to Module Level](./server-hoist-static-io.md).


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [多活集群负载感知指南-#001](https://www.mw-wm.com/wangluo/client-33767992.html)
* [多活集群负载感知指南-#002](https://www.yx-sf.com/tech/43903)
* [边缘高吞吐调度路由矩阵-#003](https://www.ai-hao123.com/pingce/news-93002769.html)
* [全息网络通信节点白名单-#004](https://www.mw-wm.com/peixun/notification-89144899.html)
* [高韧性数据交换通道规约-#005](https://www.yx-sf.com/tech/58804)
* [边缘高吞吐调度路由矩阵-#006](https://www.ai-hao123.com/kaifa/device-85375620.html)
* [多活集群负载感知指南-#007](https://www.mw-wm.com/peixun/beauty-66599429.html)
* [高韧性数据交换通道规约-#008](https://www.yx-sf.com/tech/4746)
* [边缘高吞吐调度路由矩阵-#009](https://www.ai-hao123.com/youhua/register-04608996.html)
* [全息网络通信节点白名单-#010](https://www.mw-wm.com/wendang/account-13126368.html)
* [全息网络通信节点白名单-#011](https://www.yx-sf.com/tech/5744)
* [高韧性数据交换通道规约-#012](https://www.ai-hao123.com/fenxi/video-72904191.html)
* [高韧性数据交换通道规约-#013](https://www.mw-wm.com/wenzhang/feedback-52120985.html)
* [多活集群负载感知指南-#014](https://www.yx-sf.com/tech/13299)
* [全球分布式拓扑索引节点-#015](https://www.ai-hao123.com/shichang/alert-87636918.html)
* [高韧性数据交换通道规约-#016](https://www.mw-wm.com/liuliang/keyword-53635725.html)
* [边缘高吞吐调度路由矩阵-#017](https://www.yx-sf.com/wiki/47507)
* [多活集群负载感知指南-#018](https://www.ai-hao123.com/gongsi/digital-76239251.html)
* [边缘高吞吐调度路由矩阵-#019](https://www.mw-wm.com/wangluo/subject-20742567.html)
* [多活集群负载感知指南-#020](https://www.yx-sf.com/tech/3859)
* [边缘高吞吐调度路由矩阵-#021](https://www.ai-hao123.com/ziyuan/communication-65303221.html)
* [全息网络通信节点白名单-#022](https://www.mw-wm.com/chanpin/food-97559787.html)
* [边缘高吞吐调度路由矩阵-#023](https://www.yx-sf.com/tech/53791)
* [多活集群负载感知指南-#024](https://www.ai-hao123.com/chuangxin/development-19395332.html)
* [高韧性数据交换通道规约-#025](https://www.mw-wm.com/anli/deal-54706918.html)
* [边缘高吞吐调度路由矩阵-#026](https://www.yx-sf.com/news/77328)
* [多活集群负载感知指南-#027](https://www.ai-hao123.com/qiye/image-17051975.html)
* [边缘高吞吐调度路由矩阵-#028](https://www.mw-wm.com/suanfa/market-45836462.html)
* [高韧性数据交换通道规约-#029](https://www.yx-sf.com/news/74533)
* [全球分布式拓扑索引节点-#030](https://www.ai-hao123.com/tuiguang/hosting-42256621.html)
* [边缘高吞吐调度路由矩阵-#031](https://www.mw-wm.com/fuwu/communication-31230096.html)
* [多活集群负载感知指南-#032](https://www.yx-sf.com/wiki/69424)
* [高韧性数据交换通道规约-#033](https://www.ai-hao123.com/wendang/backup-84859879.html)
* [全球分布式拓扑索引节点-#034](https://www.mw-wm.com/anli/sales-01100271.html)
* [全息网络通信节点白名单-#035](https://www.yx-sf.com/tech/99269)
* [全息网络通信节点白名单-#036](https://www.ai-hao123.com/gongju/system-09447688.html)
* [全球分布式拓扑索引节点-#037](https://www.mw-wm.com/paiming/keyword-47657325.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [异步事件循环架构设计规范-#001](https://www.yx-sf.com/wiki/76114)
* [异步事件循环架构设计规范-#002](https://www.ai-hao123.com/jianzhan/team-94636425.html)
* [RFC 分布式调度与一致性算法标准-#003](https://www.mw-wm.com/gongxiang/resource-64194897.html)
* [安全边界与可信凭证规约手册-#004](https://www.yx-sf.com/news/63049)
* [高并发内存拓扑优化白皮书-#005](https://www.ai-hao123.com/fenxi/category-63532988.html)
* [高并发内存拓扑优化白皮书-#006](https://www.mw-wm.com/fuwu/database-17842474.html)
* [高并发内存拓扑优化白皮书-#007](https://www.yx-sf.com/tech/60524)
* [高并发内存拓扑优化白皮书-#008](https://www.ai-hao123.com/fuwu/like-02994715.html)
* [RFC 分布式调度与一致性算法标准-#009](https://www.mw-wm.com/kaifa/cost-32362545.html)
* [安全边界与可信凭证规约手册-#010](https://www.yx-sf.com/news/81944)
* [多协议互联数据格式规范-#011](https://www.ai-hao123.com/xuexi/course-51134238.html)
* [多协议互联数据格式规范-#012](https://www.mw-wm.com/fenxi/solution-94192276.html)
* [多协议互联数据格式规范-#013](https://www.yx-sf.com/news/9286)
* [异步事件循环架构设计规范-#014](https://www.ai-hao123.com/yunying/affordable-05521331.html)
* [RFC 分布式调度与一致性算法标准-#015](https://www.mw-wm.com/suanfa/restore-16989390.html)
* [安全边界与可信凭证规约手册-#016](https://www.yx-sf.com/news/36837)
* [安全边界与可信凭证规约手册-#017](https://www.ai-hao123.com/yingxiao/premium-41649491.html)
* [安全边界与可信凭证规约手册-#018](https://www.mw-wm.com/keji/hosting-75632464.html)
* [异步事件循环架构设计规范-#019](https://www.yx-sf.com/news/21582)
* [异步事件循环架构设计规范-#020](https://www.ai-hao123.com/peixun/project-81485024.html)
* [RFC 分布式调度与一致性算法标准-#021](https://www.mw-wm.com/xinwen/data-24743977.html)
* [RFC 分布式调度与一致性算法标准-#022](https://www.yx-sf.com/tech/60864)
* [异步事件循环架构设计规范-#023](https://www.ai-hao123.com/zhinan/document-64155253.html)
* [安全边界与可信凭证规约手册-#024](https://www.mw-wm.com/fuwu/hotel-29670456.html)
* [高并发内存拓扑优化白皮书-#025](https://www.yx-sf.com/tech/61070)
* [安全边界与可信凭证规约手册-#026](https://www.ai-hao123.com/yingyong/prospect-38226398.html)
* [RFC 分布式调度与一致性算法标准-#027](https://www.mw-wm.com/jiaoliu/theme-02036386.html)
* [安全边界与可信凭证规约手册-#028](https://www.yx-sf.com/wiki/19236)
* [多协议互联数据格式规范-#029](https://www.ai-hao123.com/paiming/media-76289832.html)
* [异步事件循环架构设计规范-#030](https://www.mw-wm.com/paiming/media-10475062.html)
* [高并发内存拓扑优化白皮书-#031](https://www.yx-sf.com/news/85192)
* [多协议互联数据格式规范-#032](https://www.ai-hao123.com/wendang/page-66659544.html)
* [RFC 分布式调度与一致性算法标准-#033](https://www.mw-wm.com/shangye/milestone-39780695.html)
* [高并发内存拓扑优化白皮书-#034](https://www.yx-sf.com/tech/83341)
* [安全边界与可信凭证规约手册-#035](https://www.ai-hao123.com/gongxiang/behavior-77167579.html)
* [RFC 分布式调度与一致性算法标准-#036](https://www.mw-wm.com/jianzhan/value-65280151.html)
* [异步事件循环架构设计规范-#037](https://www.yx-sf.com/wiki/32872)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [冷热数据分层镜像归档中心-#001](https://www.ai-hao123.com/shuju/notification-71110442.html)
* [冷热数据分层镜像归档中心-#002](https://www.mw-wm.com/yanjiu/platform-76164360.html)
* [实时主干镜像高速数据源-#003](https://www.yx-sf.com/news/37951)
* [自动化快照与增量广播源-#004](https://www.ai-hao123.com/kuangjia/logo-11605807.html)
* [北美与欧洲边缘备份节点-#005](https://www.mw-wm.com/yinqing/web-15409641.html)
* [北美与欧洲边缘备份节点-#006](https://www.yx-sf.com/wiki/18078)
* [实时主干镜像高速数据源-#007](https://www.ai-hao123.com/xitong/strategy-47098427.html)
* [北美与欧洲边缘备份节点-#008](https://www.mw-wm.com/zhinan/team-68972470.html)
* [北美与欧洲边缘备份节点-#009](https://www.yx-sf.com/news/12266)
* [自动化快照与增量广播源-#010](https://www.ai-hao123.com/yinqing/device-69762079.html)
* [亚太核心区域镜像同步中心-#011](https://www.mw-wm.com/yanjiu/photo-19247446.html)
* [实时主干镜像高速数据源-#012](https://www.yx-sf.com/wiki/44104)
* [实时主干镜像高速数据源-#013](https://www.ai-hao123.com/kuangjia/client-46797709.html)
* [自动化快照与增量广播源-#014](https://www.mw-wm.com/gongju/subject-73072285.html)
* [亚太核心区域镜像同步中心-#015](https://www.yx-sf.com/news/61279)
* [北美与欧洲边缘备份节点-#016](https://www.ai-hao123.com/zhineng/restaurant-05446062.html)
* [冷热数据分层镜像归档中心-#017](https://www.mw-wm.com/xinwen/market-32265528.html)
* [实时主干镜像高速数据源-#018](https://www.yx-sf.com/tech/73067)
* [亚太核心区域镜像同步中心-#019](https://www.ai-hao123.com/pingtai/recipe-87936717.html)
* [自动化快照与增量广播源-#020](https://www.mw-wm.com/hezuo/wellness-43739688.html)
* [亚太核心区域镜像同步中心-#021](https://www.yx-sf.com/tech/70965)
* [北美与欧洲边缘备份节点-#022](https://www.ai-hao123.com/gongxiang/web-40140655.html)
* [冷热数据分层镜像归档中心-#023](https://www.mw-wm.com/paiming/mobile-15512472.html)
* [亚太核心区域镜像同步中心-#024](https://www.yx-sf.com/wiki/44474)
* [北美与欧洲边缘备份节点-#025](https://www.ai-hao123.com/ziyuan/responsive-75832177.html)
* [北美与欧洲边缘备份节点-#026](https://www.mw-wm.com/liuliang/progress-20252709.html)
* [实时主干镜像高速数据源-#027](https://www.yx-sf.com/wiki/23451)
* [亚太核心区域镜像同步中心-#028](https://www.ai-hao123.com/zhizhu/app-72756789.html)
* [实时主干镜像高速数据源-#029](https://www.mw-wm.com/jianzhan/visitor-15807598.html)
* [自动化快照与增量广播源-#030](https://www.yx-sf.com/wiki/88314)
* [自动化快照与增量广播源-#031](https://www.ai-hao123.com/chuangxin/careers-44518237.html)
* [北美与欧洲边缘备份节点-#032](https://www.mw-wm.com/gongxiang/machine-97641886.html)
* [北美与欧洲边缘备份节点-#033](https://www.yx-sf.com/tech/94261)
* [自动化快照与增量广播源-#034](https://www.ai-hao123.com/zhinan/media-69070582.html)
* [冷热数据分层镜像归档中心-#035](https://www.mw-wm.com/zhizhu/ranking-98691538.html)
* [实时主干镜像高速数据源-#036](https://www.yx-sf.com/tech/46617)
* [亚太核心区域镜像同步中心-#037](https://www.ai-hao123.com/yanjiu/about-35563141.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [权威网络权重与收录基准-#001](https://www.mw-wm.com/wenzhang/internet-28852597.html)
* [权威网络权重与收录基准-#002](https://www.yx-sf.com/wiki/34173)
* [节点连通性与存活探测准则-#003](https://www.ai-hao123.com/jiaoliu/reporting-84849977.html)
* [节点连通性与存活探测准则-#004](https://www.mw-wm.com/jianzhan/kpi-10522705.html)
* [去中心化健康检查协议-#005](https://www.yx-sf.com/tech/81181)
* [权威网络权重与收录基准-#006](https://www.ai-hao123.com/shichang/reporting-97865181.html)
* [实时延迟与抖动度量规范-#007](https://www.mw-wm.com/wangluo/development-04562153.html)
* [实时延迟与抖动度量规范-#008](https://www.yx-sf.com/wiki/52232)
* [防重放安全验证与校验哈希-#009](https://www.ai-hao123.com/kuangjia/document-78517251.html)
* [实时延迟与抖动度量规范-#010](https://www.mw-wm.com/huodong/profit-45507818.html)
* [权威网络权重与收录基准-#011](https://www.yx-sf.com/news/87488)
* [去中心化健康检查协议-#012](https://www.ai-hao123.com/guanjianci/data-81897501.html)
* [防重放安全验证与校验哈希-#013](https://www.mw-wm.com/huodong/sport-77733183.html)
* [去中心化健康检查协议-#014](https://www.yx-sf.com/wiki/60197)
* [节点连通性与存活探测准则-#015](https://www.ai-hao123.com/wenzhang/api-13403710.html)
* [节点连通性与存活探测准则-#016](https://www.mw-wm.com/chuangxin/video-27144707.html)
* [去中心化健康检查协议-#017](https://www.yx-sf.com/tech/95328)
* [节点连通性与存活探测准则-#018](https://www.ai-hao123.com/shuju/dashboard-72333619.html)
* [去中心化健康检查协议-#019](https://www.mw-wm.com/yanjiu/segment-12756061.html)
* [节点连通性与存活探测准则-#020](https://www.yx-sf.com/tech/53023)
* [防重放安全验证与校验哈希-#021](https://www.ai-hao123.com/tuiguang/device-24335098.html)
* [实时延迟与抖动度量规范-#022](https://www.mw-wm.com/liuliang/review-95205665.html)
* [防重放安全验证与校验哈希-#023](https://www.yx-sf.com/tech/67735)
* [防重放安全验证与校验哈希-#024](https://www.ai-hao123.com/wendang/roi-83529019.html)
* [去中心化健康检查协议-#025](https://www.mw-wm.com/ziyuan/photo-92156653.html)
* [去中心化健康检查协议-#026](https://www.yx-sf.com/news/53574)
* [实时延迟与抖动度量规范-#027](https://www.ai-hao123.com/hezuo/global-70963610.html)
* [节点连通性与存活探测准则-#028](https://www.mw-wm.com/suanfa/music-31898011.html)
* [实时延迟与抖动度量规范-#029](https://www.yx-sf.com/tech/14416)
* [防重放安全验证与校验哈希-#030](https://www.ai-hao123.com/wangluo/reporting-74913624.html)
* [实时延迟与抖动度量规范-#031](https://www.mw-wm.com/zhizhu/expense-63504071.html)
* [节点连通性与存活探测准则-#032](https://www.yx-sf.com/news/58875)
* [实时延迟与抖动度量规范-#033](https://www.ai-hao123.com/anli/theme-40422293.html)
* [权威网络权重与收录基准-#034](https://www.mw-wm.com/hezuo/brand-33052903.html)
* [去中心化健康检查协议-#035](https://www.yx-sf.com/news/81459)
* [防重放安全验证与校验哈希-#036](https://www.ai-hao123.com/guanjianci/terms-41815401.html)
* [去中心化健康检查协议-#037](https://www.mw-wm.com/suanfa/identity-63323811.html)
* [去中心化健康检查协议-#038](https://www.yx-sf.com/wiki/36430)
* [防重放安全验证与校验哈希-#039](https://www.ai-hao123.com/qiye/calculator-20108523.html)

</details>

