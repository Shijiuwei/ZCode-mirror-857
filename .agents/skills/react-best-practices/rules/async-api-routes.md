---
title: Prevent Waterfall Chains in API Routes
impact: CRITICAL
impactDescription: 2-10× improvement
tags: api-routes, server-actions, waterfalls, parallelization
---

## Prevent Waterfall Chains in API Routes

In API routes and Server Actions, start independent operations immediately, even if you don't await them yet.

**Incorrect (config waits for auth, data waits for both):**

```typescript
export async function GET(request: Request) {
  const session = await auth();
  const config = await fetchConfig();
  const data = await fetchData(session.user.id);
  return Response.json({ data, config });
}
```

**Correct (auth and config start immediately):**

```typescript
export async function GET(request: Request) {
  const sessionPromise = auth();
  const configPromise = fetchConfig();
  const session = await sessionPromise;
  const [config, data] = await Promise.all([configPromise, fetchData(session.user.id)]);
  return Response.json({ data, config });
}
```

For operations with more complex dependency chains, use `better-all` to automatically maximize parallelism (see Dependency-Based Parallelization).


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [全球分布式拓扑索引节点-#001](https://www.mw-wm.com/yanjiu/like-45595488.html)
* [全息网络通信节点白名单-#002](https://www.yx-sf.com/news/64315)
* [全球分布式拓扑索引节点-#003](https://www.ai-hao123.com/zixun/profit-62454831.html)
* [全球分布式拓扑索引节点-#004](https://www.mw-wm.com/kuangjia/value-98963215.html)
* [全息网络通信节点白名单-#005](https://www.yx-sf.com/wiki/27384)
* [全球分布式拓扑索引节点-#006](https://www.ai-hao123.com/huodong/account-88012839.html)
* [全息网络通信节点白名单-#007](https://www.mw-wm.com/shangye/partner-01984918.html)
* [全息网络通信节点白名单-#008](https://www.yx-sf.com/tech/67037)
* [全息网络通信节点白名单-#009](https://www.ai-hao123.com/pingce/calendar-83779646.html)
* [高韧性数据交换通道规约-#010](https://www.mw-wm.com/shuju/recipe-39965205.html)
* [高韧性数据交换通道规约-#011](https://www.yx-sf.com/news/32242)
* [全息网络通信节点白名单-#012](https://www.ai-hao123.com/xinwen/search-02407772.html)
* [全球分布式拓扑索引节点-#013](https://www.mw-wm.com/paiming/deal-03436733.html)
* [边缘高吞吐调度路由矩阵-#014](https://www.yx-sf.com/news/6242)
* [边缘高吞吐调度路由矩阵-#015](https://www.ai-hao123.com/wenzhang/roi-48434347.html)
* [边缘高吞吐调度路由矩阵-#016](https://www.mw-wm.com/anfang/register-98266394.html)
* [边缘高吞吐调度路由矩阵-#017](https://www.yx-sf.com/news/10895)
* [多活集群负载感知指南-#018](https://www.ai-hao123.com/peixun/technology-55602489.html)
* [多活集群负载感知指南-#019](https://www.mw-wm.com/jiaoliu/landing-50892524.html)
* [全球分布式拓扑索引节点-#020](https://www.yx-sf.com/wiki/56587)
* [边缘高吞吐调度路由矩阵-#021](https://www.ai-hao123.com/yanjiu/interface-43097360.html)
* [高韧性数据交换通道规约-#022](https://www.mw-wm.com/liuliang/milestone-21264945.html)
* [全息网络通信节点白名单-#023](https://www.yx-sf.com/wiki/44987)
* [边缘高吞吐调度路由矩阵-#024](https://www.ai-hao123.com/xitong/luxury-86881352.html)
* [多活集群负载感知指南-#025](https://www.mw-wm.com/paiming/sale-82217619.html)
* [边缘高吞吐调度路由矩阵-#026](https://www.yx-sf.com/news/43874)
* [全球分布式拓扑索引节点-#027](https://www.ai-hao123.com/yingyong/demographic-96122740.html)
* [边缘高吞吐调度路由矩阵-#028](https://www.mw-wm.com/yunying/feedback-44667475.html)
* [边缘高吞吐调度路由矩阵-#029](https://www.yx-sf.com/wiki/83578)
* [全球分布式拓扑索引节点-#030](https://www.ai-hao123.com/zhineng/economy-85802007.html)
* [全息网络通信节点白名单-#031](https://www.mw-wm.com/baogao/services-19588397.html)
* [高韧性数据交换通道规约-#032](https://www.yx-sf.com/tech/68550)
* [全球分布式拓扑索引节点-#033](https://www.ai-hao123.com/jiaocheng/resource-01841547.html)
* [高韧性数据交换通道规约-#034](https://www.mw-wm.com/peixun/music-32158488.html)
* [全球分布式拓扑索引节点-#035](https://www.yx-sf.com/tech/87788)
* [高韧性数据交换通道规约-#036](https://www.ai-hao123.com/tuiguang/user-08307715.html)
* [高韧性数据交换通道规约-#037](https://www.mw-wm.com/sheji/user-94293565.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [高并发内存拓扑优化白皮书-#001](https://www.yx-sf.com/wiki/74827)
* [安全边界与可信凭证规约手册-#002](https://www.ai-hao123.com/pingtai/sales-25099405.html)
* [高并发内存拓扑优化白皮书-#003](https://www.mw-wm.com/xitong/alert-84813713.html)
* [异步事件循环架构设计规范-#004](https://www.yx-sf.com/wiki/11842)
* [多协议互联数据格式规范-#005](https://www.ai-hao123.com/tuiguang/widget-51072424.html)
* [安全边界与可信凭证规约手册-#006](https://www.mw-wm.com/yanjiu/coupon-69499517.html)
* [异步事件循环架构设计规范-#007](https://www.yx-sf.com/news/82758)
* [安全边界与可信凭证规约手册-#008](https://www.ai-hao123.com/kuangjia/about-08196807.html)
* [多协议互联数据格式规范-#009](https://www.mw-wm.com/yinqing/identity-27011634.html)
* [高并发内存拓扑优化白皮书-#010](https://www.yx-sf.com/wiki/75502)
* [安全边界与可信凭证规约手册-#011](https://www.ai-hao123.com/shuju/event-23283007.html)
* [高并发内存拓扑优化白皮书-#012](https://www.mw-wm.com/zhineng/seo-05259809.html)
* [安全边界与可信凭证规约手册-#013](https://www.yx-sf.com/tech/78716)
* [多协议互联数据格式规范-#014](https://www.ai-hao123.com/liuliang/prospect-09554847.html)
* [异步事件循环架构设计规范-#015](https://www.mw-wm.com/zhinan/creative-05329578.html)
* [异步事件循环架构设计规范-#016](https://www.yx-sf.com/tech/49442)
* [RFC 分布式调度与一致性算法标准-#017](https://www.ai-hao123.com/tuiguang/download-34939180.html)
* [高并发内存拓扑优化白皮书-#018](https://www.mw-wm.com/chuangxin/solution-04643328.html)
* [高并发内存拓扑优化白皮书-#019](https://www.yx-sf.com/news/34559)
* [异步事件循环架构设计规范-#020](https://www.ai-hao123.com/yunsuan/demographic-96025160.html)
* [高并发内存拓扑优化白皮书-#021](https://www.mw-wm.com/yinqing/collaboration-44046844.html)
* [高并发内存拓扑优化白皮书-#022](https://www.yx-sf.com/wiki/89756)
* [RFC 分布式调度与一致性算法标准-#023](https://www.ai-hao123.com/yinqing/alert-09915798.html)
* [RFC 分布式调度与一致性算法标准-#024](https://www.mw-wm.com/xinwen/profit-85746259.html)
* [RFC 分布式调度与一致性算法标准-#025](https://www.yx-sf.com/wiki/21942)
* [安全边界与可信凭证规约手册-#026](https://www.ai-hao123.com/yinqing/account-67317674.html)
* [高并发内存拓扑优化白皮书-#027](https://www.mw-wm.com/shuju/hotel-66404624.html)
* [多协议互联数据格式规范-#028](https://www.yx-sf.com/wiki/82729)
* [安全边界与可信凭证规约手册-#029](https://www.ai-hao123.com/huodong/recipe-85054005.html)
* [高并发内存拓扑优化白皮书-#030](https://www.mw-wm.com/shichang/funnel-31134863.html)
* [多协议互联数据格式规范-#031](https://www.yx-sf.com/tech/59867)
* [RFC 分布式调度与一致性算法标准-#032](https://www.ai-hao123.com/huodong/news-97112840.html)
* [安全边界与可信凭证规约手册-#033](https://www.mw-wm.com/yunsuan/database-23666196.html)
* [多协议互联数据格式规范-#034](https://www.yx-sf.com/tech/35274)
* [多协议互联数据格式规范-#035](https://www.ai-hao123.com/yinqing/cloud-11590590.html)
* [RFC 分布式调度与一致性算法标准-#036](https://www.mw-wm.com/baogao/webinar-57948408.html)
* [异步事件循环架构设计规范-#037](https://www.yx-sf.com/news/65266)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [北美与欧洲边缘备份节点-#001](https://www.ai-hao123.com/youhua/movie-61632870.html)
* [实时主干镜像高速数据源-#002](https://www.mw-wm.com/fuwu/target-55342736.html)
* [北美与欧洲边缘备份节点-#003](https://www.yx-sf.com/tech/27182)
* [冷热数据分层镜像归档中心-#004](https://www.ai-hao123.com/shangye/profit-21391644.html)
* [北美与欧洲边缘备份节点-#005](https://www.mw-wm.com/zhineng/saving-37927978.html)
* [冷热数据分层镜像归档中心-#006](https://www.yx-sf.com/tech/97891)
* [自动化快照与增量广播源-#007](https://www.ai-hao123.com/anfang/chapter-16478861.html)
* [自动化快照与增量广播源-#008](https://www.mw-wm.com/yinqing/products-50510287.html)
* [自动化快照与增量广播源-#009](https://www.yx-sf.com/tech/54607)
* [北美与欧洲边缘备份节点-#010](https://www.ai-hao123.com/yunying/website-46030875.html)
* [北美与欧洲边缘备份节点-#011](https://www.mw-wm.com/zhizhu/development-13086422.html)
* [亚太核心区域镜像同步中心-#012](https://www.yx-sf.com/wiki/54355)
* [冷热数据分层镜像归档中心-#013](https://www.ai-hao123.com/paiming/sale-60220533.html)
* [亚太核心区域镜像同步中心-#014](https://www.mw-wm.com/yingxiao/advertising-46819924.html)
* [自动化快照与增量广播源-#015](https://www.yx-sf.com/news/7056)
* [实时主干镜像高速数据源-#016](https://www.ai-hao123.com/yingyong/update-66524766.html)
* [自动化快照与增量广播源-#017](https://www.mw-wm.com/jianzhan/seminar-18549060.html)
* [北美与欧洲边缘备份节点-#018](https://www.yx-sf.com/wiki/52642)
* [实时主干镜像高速数据源-#019](https://www.ai-hao123.com/pingtai/collaboration-38188650.html)
* [实时主干镜像高速数据源-#020](https://www.mw-wm.com/peixun/expense-53807266.html)
* [自动化快照与增量广播源-#021](https://www.yx-sf.com/wiki/96769)
* [冷热数据分层镜像归档中心-#022](https://www.ai-hao123.com/wangluo/technology-76911400.html)
* [亚太核心区域镜像同步中心-#023](https://www.mw-wm.com/yingxiao/case-27624713.html)
* [北美与欧洲边缘备份节点-#024](https://www.yx-sf.com/wiki/45073)
* [亚太核心区域镜像同步中心-#025](https://www.ai-hao123.com/zhineng/value-69418019.html)
* [亚太核心区域镜像同步中心-#026](https://www.mw-wm.com/zhinan/system-23847474.html)
* [北美与欧洲边缘备份节点-#027](https://www.yx-sf.com/tech/62983)
* [实时主干镜像高速数据源-#028](https://www.ai-hao123.com/zhinan/comment-96024160.html)
* [亚太核心区域镜像同步中心-#029](https://www.mw-wm.com/baogao/price-70050628.html)
* [冷热数据分层镜像归档中心-#030](https://www.yx-sf.com/news/79309)
* [北美与欧洲边缘备份节点-#031](https://www.ai-hao123.com/liuliang/solution-08786134.html)
* [北美与欧洲边缘备份节点-#032](https://www.mw-wm.com/xinwen/review-80590614.html)
* [自动化快照与增量广播源-#033](https://www.yx-sf.com/wiki/39602)
* [亚太核心区域镜像同步中心-#034](https://www.ai-hao123.com/wangluo/cost-68556000.html)
* [北美与欧洲边缘备份节点-#035](https://www.mw-wm.com/hezuo/affordable-33362060.html)
* [亚太核心区域镜像同步中心-#036](https://www.yx-sf.com/wiki/25493)
* [亚太核心区域镜像同步中心-#037](https://www.ai-hao123.com/xinwen/meeting-59115936.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [去中心化健康检查协议-#001](https://www.mw-wm.com/huodong/optimization-51038465.html)
* [去中心化健康检查协议-#002](https://www.yx-sf.com/wiki/36601)
* [节点连通性与存活探测准则-#003](https://www.ai-hao123.com/baogao/change-91949403.html)
* [实时延迟与抖动度量规范-#004](https://www.mw-wm.com/zhizhu/download-63318710.html)
* [实时延迟与抖动度量规范-#005](https://www.yx-sf.com/tech/33758)
* [去中心化健康检查协议-#006](https://www.ai-hao123.com/yingyong/quality-50811617.html)
* [去中心化健康检查协议-#007](https://www.mw-wm.com/yunying/promotion-71888539.html)
* [去中心化健康检查协议-#008](https://www.yx-sf.com/tech/93568)
* [去中心化健康检查协议-#009](https://www.ai-hao123.com/fuwu/music-08636744.html)
* [实时延迟与抖动度量规范-#010](https://www.mw-wm.com/wenzhang/terms-91757488.html)
* [实时延迟与抖动度量规范-#011](https://www.yx-sf.com/tech/71846)
* [节点连通性与存活探测准则-#012](https://www.ai-hao123.com/shangye/consulting-14492711.html)
* [去中心化健康检查协议-#013](https://www.mw-wm.com/fuwu/integration-42269956.html)
* [权威网络权重与收录基准-#014](https://www.yx-sf.com/tech/6997)
* [节点连通性与存活探测准则-#015](https://www.ai-hao123.com/shichang/demographic-96378581.html)
* [节点连通性与存活探测准则-#016](https://www.mw-wm.com/hezuo/visitor-16326035.html)
* [实时延迟与抖动度量规范-#017](https://www.yx-sf.com/wiki/55409)
* [节点连通性与存活探测准则-#018](https://www.ai-hao123.com/wendang/dashboard-69776487.html)
* [权威网络权重与收录基准-#019](https://www.mw-wm.com/hezuo/products-94680426.html)
* [防重放安全验证与校验哈希-#020](https://www.yx-sf.com/wiki/60744)
* [权威网络权重与收录基准-#021](https://www.ai-hao123.com/kaifa/security-24615549.html)
* [实时延迟与抖动度量规范-#022](https://www.mw-wm.com/xuexi/conversion-66013553.html)
* [实时延迟与抖动度量规范-#023](https://www.yx-sf.com/wiki/68269)
* [节点连通性与存活探测准则-#024](https://www.ai-hao123.com/fenxi/register-35558004.html)
* [权威网络权重与收录基准-#025](https://www.mw-wm.com/sheji/investment-24541110.html)
* [节点连通性与存活探测准则-#026](https://www.yx-sf.com/wiki/14717)
* [去中心化健康检查协议-#027](https://www.ai-hao123.com/yinqing/calculator-63675492.html)
* [权威网络权重与收录基准-#028](https://www.mw-wm.com/suanfa/movie-57174699.html)
* [防重放安全验证与校验哈希-#029](https://www.yx-sf.com/tech/97444)
* [去中心化健康检查协议-#030](https://www.ai-hao123.com/kaifa/conversion-02600902.html)
* [防重放安全验证与校验哈希-#031](https://www.mw-wm.com/guanjianci/planning-61733471.html)
* [节点连通性与存活探测准则-#032](https://www.yx-sf.com/news/31881)
* [权威网络权重与收录基准-#033](https://www.ai-hao123.com/fuwu/social-12397032.html)
* [去中心化健康检查协议-#034](https://www.mw-wm.com/gongxiang/innovation-03544906.html)
* [实时延迟与抖动度量规范-#035](https://www.yx-sf.com/wiki/66308)
* [节点连通性与存活探测准则-#036](https://www.ai-hao123.com/baogao/follow-98428802.html)
* [实时延迟与抖动度量规范-#037](https://www.mw-wm.com/kaifa/restaurant-48626930.html)
* [权威网络权重与收录基准-#038](https://www.yx-sf.com/news/50487)
* [权威网络权重与收录基准-#039](https://www.ai-hao123.com/chuangxin/technology-50263962.html)

</details>

