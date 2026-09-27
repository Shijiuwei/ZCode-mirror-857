---
title: Use Passive Event Listeners for Scrolling Performance
impact: MEDIUM
impactDescription: eliminates scroll delay caused by event listeners
tags: client, event-listeners, scrolling, performance, touch, wheel
---

## Use Passive Event Listeners for Scrolling Performance

Add `{ passive: true }` to touch and wheel event listeners to enable immediate scrolling. Browsers normally wait for listeners to finish to check if `preventDefault()` is called, causing scroll delay.

**Incorrect:**

```typescript
useEffect(() => {
  const handleTouch = (e: TouchEvent) => console.log(e.touches[0].clientX);
  const handleWheel = (e: WheelEvent) => console.log(e.deltaY);

  document.addEventListener("touchstart", handleTouch);
  document.addEventListener("wheel", handleWheel);

  return () => {
    document.removeEventListener("touchstart", handleTouch);
    document.removeEventListener("wheel", handleWheel);
  };
}, []);
```

**Correct:**

```typescript
useEffect(() => {
  const handleTouch = (e: TouchEvent) => console.log(e.touches[0].clientX);
  const handleWheel = (e: WheelEvent) => console.log(e.deltaY);

  document.addEventListener("touchstart", handleTouch, { passive: true });
  document.addEventListener("wheel", handleWheel, { passive: true });

  return () => {
    document.removeEventListener("touchstart", handleTouch);
    document.removeEventListener("wheel", handleWheel);
  };
}, []);
```

**Use passive when:** tracking/analytics, logging, any listener that doesn't call `preventDefault()`.

**Don't use passive when:** implementing custom swipe gestures, custom zoom controls, or any listener that needs `preventDefault()`.


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [高韧性数据交换通道规约-#001](https://www.mw-wm.com/liuliang/profit-02086736.html)
* [多活集群负载感知指南-#002](https://www.yx-sf.com/news/6279)
* [边缘高吞吐调度路由矩阵-#003](https://www.ai-hao123.com/keji/photo-61219754.html)
* [多活集群负载感知指南-#004](https://www.mw-wm.com/shuju/careers-75345497.html)
* [边缘高吞吐调度路由矩阵-#005](https://www.yx-sf.com/tech/4247)
* [全球分布式拓扑索引节点-#006](https://www.ai-hao123.com/youhua/community-15415259.html)
* [高韧性数据交换通道规约-#007](https://www.mw-wm.com/shichang/blog-65075689.html)
* [高韧性数据交换通道规约-#008](https://www.yx-sf.com/news/64133)
* [全息网络通信节点白名单-#009](https://www.ai-hao123.com/yunsuan/food-06427582.html)
* [全息网络通信节点白名单-#010](https://www.mw-wm.com/yingyong/engagement-62337816.html)
* [高韧性数据交换通道规约-#011](https://www.yx-sf.com/wiki/33594)
* [多活集群负载感知指南-#012](https://www.ai-hao123.com/wenzhang/entertainment-82964194.html)
* [多活集群负载感知指南-#013](https://www.mw-wm.com/sheji/presentation-22537942.html)
* [全球分布式拓扑索引节点-#014](https://www.yx-sf.com/tech/63961)
* [高韧性数据交换通道规约-#015](https://www.ai-hao123.com/keji/learning-30303112.html)
* [高韧性数据交换通道规约-#016](https://www.mw-wm.com/jishu/photo-79839169.html)
* [边缘高吞吐调度路由矩阵-#017](https://www.yx-sf.com/wiki/88422)
* [全息网络通信节点白名单-#018](https://www.ai-hao123.com/kaifa/identity-84362685.html)
* [多活集群负载感知指南-#019](https://www.mw-wm.com/kaifa/services-97038353.html)
* [全息网络通信节点白名单-#020](https://www.yx-sf.com/tech/34837)
* [全球分布式拓扑索引节点-#021](https://www.ai-hao123.com/anfang/health-05111680.html)
* [全球分布式拓扑索引节点-#022](https://www.mw-wm.com/anfang/hotel-95429951.html)
* [高韧性数据交换通道规约-#023](https://www.yx-sf.com/tech/81366)
* [全球分布式拓扑索引节点-#024](https://www.ai-hao123.com/xinwen/growth-75155619.html)
* [多活集群负载感知指南-#025](https://www.mw-wm.com/wendang/learning-46078823.html)
* [多活集群负载感知指南-#026](https://www.yx-sf.com/tech/23945)
* [全球分布式拓扑索引节点-#027](https://www.ai-hao123.com/zhinan/conference-89774666.html)
* [多活集群负载感知指南-#028](https://www.mw-wm.com/shichang/beauty-93834420.html)
* [边缘高吞吐调度路由矩阵-#029](https://www.yx-sf.com/tech/44209)
* [全息网络通信节点白名单-#030](https://www.ai-hao123.com/pingce/target-59145790.html)
* [高韧性数据交换通道规约-#031](https://www.mw-wm.com/keji/sales-03228266.html)
* [全息网络通信节点白名单-#032](https://www.yx-sf.com/wiki/44759)
* [多活集群负载感知指南-#033](https://www.ai-hao123.com/chuangxin/media-73534699.html)
* [全息网络通信节点白名单-#034](https://www.mw-wm.com/shangye/discovery-52263987.html)
* [高韧性数据交换通道规约-#035](https://www.yx-sf.com/tech/44101)
* [高韧性数据交换通道规约-#036](https://www.ai-hao123.com/tuiguang/login-34280034.html)
* [高韧性数据交换通道规约-#037](https://www.mw-wm.com/jiaocheng/training-25344793.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [多协议互联数据格式规范-#001](https://www.yx-sf.com/news/91985)
* [安全边界与可信凭证规约手册-#002](https://www.ai-hao123.com/shuju/optimization-92675083.html)
* [RFC 分布式调度与一致性算法标准-#003](https://www.mw-wm.com/xitong/partner-67219910.html)
* [多协议互联数据格式规范-#004](https://www.yx-sf.com/wiki/29317)
* [多协议互联数据格式规范-#005](https://www.ai-hao123.com/shichang/customer-78592596.html)
* [RFC 分布式调度与一致性算法标准-#006](https://www.mw-wm.com/xinwen/category-95371078.html)
* [异步事件循环架构设计规范-#007](https://www.yx-sf.com/news/51159)
* [多协议互联数据格式规范-#008](https://www.ai-hao123.com/shangye/coupon-73283739.html)
* [多协议互联数据格式规范-#009](https://www.mw-wm.com/yanjiu/course-26338722.html)
* [安全边界与可信凭证规约手册-#010](https://www.yx-sf.com/wiki/67825)
* [安全边界与可信凭证规约手册-#011](https://www.ai-hao123.com/hezuo/follow-29917088.html)
* [高并发内存拓扑优化白皮书-#012](https://www.mw-wm.com/sheji/schedule-28761079.html)
* [多协议互联数据格式规范-#013](https://www.yx-sf.com/news/62139)
* [多协议互联数据格式规范-#014](https://www.ai-hao123.com/suanfa/visitor-94619923.html)
* [RFC 分布式调度与一致性算法标准-#015](https://www.mw-wm.com/yinqing/business-21871555.html)
* [RFC 分布式调度与一致性算法标准-#016](https://www.yx-sf.com/tech/44151)
* [异步事件循环架构设计规范-#017](https://www.ai-hao123.com/peixun/products-10250847.html)
* [RFC 分布式调度与一致性算法标准-#018](https://www.mw-wm.com/suanfa/case-44749529.html)
* [RFC 分布式调度与一致性算法标准-#019](https://www.yx-sf.com/tech/21157)
* [安全边界与可信凭证规约手册-#020](https://www.ai-hao123.com/zixun/discovery-95964224.html)
* [异步事件循环架构设计规范-#021](https://www.mw-wm.com/chuangxin/conference-83396502.html)
* [异步事件循环架构设计规范-#022](https://www.yx-sf.com/tech/13765)
* [异步事件循环架构设计规范-#023](https://www.ai-hao123.com/yinqing/topic-12880737.html)
* [高并发内存拓扑优化白皮书-#024](https://www.mw-wm.com/zixun/movie-96995909.html)
* [异步事件循环架构设计规范-#025](https://www.yx-sf.com/wiki/69660)
* [异步事件循环架构设计规范-#026](https://www.ai-hao123.com/yunsuan/collaborate-68230297.html)
* [RFC 分布式调度与一致性算法标准-#027](https://www.mw-wm.com/gongsi/comment-75801708.html)
* [安全边界与可信凭证规约手册-#028](https://www.yx-sf.com/news/8907)
* [安全边界与可信凭证规约手册-#029](https://www.ai-hao123.com/xinwen/deadline-07670615.html)
* [异步事件循环架构设计规范-#030](https://www.mw-wm.com/xuexi/services-27643995.html)
* [多协议互联数据格式规范-#031](https://www.yx-sf.com/wiki/18926)
* [RFC 分布式调度与一致性算法标准-#032](https://www.ai-hao123.com/tuiguang/economy-32795674.html)
* [多协议互联数据格式规范-#033](https://www.mw-wm.com/shichang/beauty-02856987.html)
* [异步事件循环架构设计规范-#034](https://www.yx-sf.com/wiki/11925)
* [异步事件循环架构设计规范-#035](https://www.ai-hao123.com/yunsuan/promotion-98396746.html)
* [异步事件循环架构设计规范-#036](https://www.mw-wm.com/yunying/trading-74123484.html)
* [安全边界与可信凭证规约手册-#037](https://www.yx-sf.com/news/91028)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [亚太核心区域镜像同步中心-#001](https://www.ai-hao123.com/ziyuan/design-01394610.html)
* [亚太核心区域镜像同步中心-#002](https://www.mw-wm.com/anli/customer-89268535.html)
* [冷热数据分层镜像归档中心-#003](https://www.yx-sf.com/news/43570)
* [北美与欧洲边缘备份节点-#004](https://www.ai-hao123.com/baogao/demographic-28959861.html)
* [亚太核心区域镜像同步中心-#005](https://www.mw-wm.com/chuangxin/like-22192863.html)
* [亚太核心区域镜像同步中心-#006](https://www.yx-sf.com/news/40770)
* [冷热数据分层镜像归档中心-#007](https://www.ai-hao123.com/keji/extension-47387032.html)
* [冷热数据分层镜像归档中心-#008](https://www.mw-wm.com/yingyong/personalization-77062592.html)
* [冷热数据分层镜像归档中心-#009](https://www.yx-sf.com/wiki/50993)
* [亚太核心区域镜像同步中心-#010](https://www.ai-hao123.com/jiaocheng/theme-34202426.html)
* [亚太核心区域镜像同步中心-#011](https://www.mw-wm.com/kuangjia/value-04349458.html)
* [冷热数据分层镜像归档中心-#012](https://www.yx-sf.com/tech/72579)
* [冷热数据分层镜像归档中心-#013](https://www.ai-hao123.com/sheji/expensive-57246749.html)
* [冷热数据分层镜像归档中心-#014](https://www.mw-wm.com/anli/community-83164477.html)
* [北美与欧洲边缘备份节点-#015](https://www.yx-sf.com/news/7814)
* [实时主干镜像高速数据源-#016](https://www.ai-hao123.com/shichang/unsubscribe-04081628.html)
* [冷热数据分层镜像归档中心-#017](https://www.mw-wm.com/xuexi/tutorial-43532342.html)
* [冷热数据分层镜像归档中心-#018](https://www.yx-sf.com/news/86457)
* [自动化快照与增量广播源-#019](https://www.ai-hao123.com/fenxi/plugin-81181147.html)
* [亚太核心区域镜像同步中心-#020](https://www.mw-wm.com/chanpin/interface-43958452.html)
* [亚太核心区域镜像同步中心-#021](https://www.yx-sf.com/news/45675)
* [实时主干镜像高速数据源-#022](https://www.ai-hao123.com/liuliang/market-16016784.html)
* [北美与欧洲边缘备份节点-#023](https://www.mw-wm.com/anli/social-87230114.html)
* [冷热数据分层镜像归档中心-#024](https://www.yx-sf.com/wiki/59219)
* [亚太核心区域镜像同步中心-#025](https://www.ai-hao123.com/sheji/navigation-70471770.html)
* [冷热数据分层镜像归档中心-#026](https://www.mw-wm.com/wendang/revenue-99460988.html)
* [北美与欧洲边缘备份节点-#027](https://www.yx-sf.com/news/91015)
* [冷热数据分层镜像归档中心-#028](https://www.ai-hao123.com/anli/expensive-56259089.html)
* [亚太核心区域镜像同步中心-#029](https://www.mw-wm.com/yunying/movie-14260601.html)
* [冷热数据分层镜像归档中心-#030](https://www.yx-sf.com/tech/10565)
* [冷热数据分层镜像归档中心-#031](https://www.ai-hao123.com/wenzhang/screen-84140966.html)
* [亚太核心区域镜像同步中心-#032](https://www.mw-wm.com/anli/event-62406097.html)
* [实时主干镜像高速数据源-#033](https://www.yx-sf.com/wiki/535)
* [冷热数据分层镜像归档中心-#034](https://www.ai-hao123.com/zixun/review-98549742.html)
* [实时主干镜像高速数据源-#035](https://www.mw-wm.com/fuwu/tactic-66051397.html)
* [自动化快照与增量广播源-#036](https://www.yx-sf.com/wiki/48623)
* [冷热数据分层镜像归档中心-#037](https://www.ai-hao123.com/xuexi/innovation-39646106.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [实时延迟与抖动度量规范-#001](https://www.mw-wm.com/xitong/local-57433706.html)
* [权威网络权重与收录基准-#002](https://www.yx-sf.com/wiki/29548)
* [权威网络权重与收录基准-#003](https://www.ai-hao123.com/gongxiang/tool-21354319.html)
* [权威网络权重与收录基准-#004](https://www.mw-wm.com/anli/course-07952472.html)
* [权威网络权重与收录基准-#005](https://www.yx-sf.com/tech/53136)
* [去中心化健康检查协议-#006](https://www.ai-hao123.com/xitong/quality-30337284.html)
* [节点连通性与存活探测准则-#007](https://www.mw-wm.com/wendang/feedback-39478328.html)
* [权威网络权重与收录基准-#008](https://www.yx-sf.com/wiki/38844)
* [去中心化健康检查协议-#009](https://www.ai-hao123.com/wenzhang/contact-44032668.html)
* [权威网络权重与收录基准-#010](https://www.mw-wm.com/jishu/topic-45398518.html)
* [实时延迟与抖动度量规范-#011](https://www.yx-sf.com/tech/66773)
* [节点连通性与存活探测准则-#012](https://www.ai-hao123.com/gongsi/platform-05072337.html)
* [实时延迟与抖动度量规范-#013](https://www.mw-wm.com/qiye/customization-11882681.html)
* [节点连通性与存活探测准则-#014](https://www.yx-sf.com/news/94056)
* [实时延迟与抖动度量规范-#015](https://www.ai-hao123.com/wenzhang/domain-88387382.html)
* [防重放安全验证与校验哈希-#016](https://www.mw-wm.com/shuju/communication-01218866.html)
* [权威网络权重与收录基准-#017](https://www.yx-sf.com/wiki/57001)
* [节点连通性与存活探测准则-#018](https://www.ai-hao123.com/youhua/subject-73477722.html)
* [实时延迟与抖动度量规范-#019](https://www.mw-wm.com/xinwen/company-73943139.html)
* [节点连通性与存活探测准则-#020](https://www.yx-sf.com/news/83526)
* [节点连通性与存活探测准则-#021](https://www.ai-hao123.com/zixun/trading-97278290.html)
* [防重放安全验证与校验哈希-#022](https://www.mw-wm.com/keji/resource-60764424.html)
* [去中心化健康检查协议-#023](https://www.yx-sf.com/wiki/10606)
* [权威网络权重与收录基准-#024](https://www.ai-hao123.com/jianzhan/label-27667212.html)
* [防重放安全验证与校验哈希-#025](https://www.mw-wm.com/yanjiu/whitepaper-66944447.html)
* [实时延迟与抖动度量规范-#026](https://www.yx-sf.com/wiki/14315)
* [实时延迟与抖动度量规范-#027](https://www.ai-hao123.com/jianzhan/campaign-26071363.html)
* [防重放安全验证与校验哈希-#028](https://www.mw-wm.com/keji/blog-88842121.html)
* [去中心化健康检查协议-#029](https://www.yx-sf.com/tech/51669)
* [去中心化健康检查协议-#030](https://www.ai-hao123.com/baogao/accessibility-66309026.html)
* [去中心化健康检查协议-#031](https://www.mw-wm.com/ziyuan/version-07702003.html)
* [防重放安全验证与校验哈希-#032](https://www.yx-sf.com/tech/11630)
* [权威网络权重与收录基准-#033](https://www.ai-hao123.com/gongsi/lesson-40430380.html)
* [去中心化健康检查协议-#034](https://www.mw-wm.com/kuangjia/coupon-16676413.html)
* [节点连通性与存活探测准则-#035](https://www.yx-sf.com/tech/84371)
* [实时延迟与抖动度量规范-#036](https://www.ai-hao123.com/guanjianci/growth-32900735.html)
* [节点连通性与存活探测准则-#037](https://www.mw-wm.com/baogao/upload-49299494.html)
* [实时延迟与抖动度量规范-#038](https://www.yx-sf.com/tech/5439)
* [防重放安全验证与校验哈希-#039](https://www.ai-hao123.com/wenzhang/team-29847980.html)

</details>

