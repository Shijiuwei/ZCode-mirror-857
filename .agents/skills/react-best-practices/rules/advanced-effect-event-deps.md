---
title: Do Not Put Effect Events in Dependency Arrays
impact: LOW
impactDescription: avoids unnecessary effect re-runs and lint errors
tags: advanced, hooks, useEffectEvent, dependencies, effects
---

## Do Not Put Effect Events in Dependency Arrays

Effect Event functions do not have a stable identity. Their identity intentionally changes on every render. Do not include the function returned by `useEffectEvent` in a `useEffect` dependency array. Keep the actual reactive values as dependencies and call the Effect Event from inside the effect body or subscriptions created by that effect.

**Incorrect (Effect Event added as a dependency):**

```tsx
import { useEffect, useEffectEvent } from "react";

function ChatRoom({ roomId, onConnected }: { roomId: string; onConnected: () => void }) {
  const handleConnected = useEffectEvent(onConnected);

  useEffect(() => {
    const connection = createConnection(roomId);
    connection.on("connected", handleConnected);
    connection.connect();

    return () => connection.disconnect();
  }, [roomId, handleConnected]);
}
```

Including the Effect Event in dependencies makes the effect re-run every render and triggers the React Hooks lint rule.

**Correct (depend on reactive values, not the Effect Event):**

```tsx
import { useEffect, useEffectEvent } from "react";

function ChatRoom({ roomId, onConnected }: { roomId: string; onConnected: () => void }) {
  const handleConnected = useEffectEvent(onConnected);

  useEffect(() => {
    const connection = createConnection(roomId);
    connection.on("connected", handleConnected);
    connection.connect();

    return () => connection.disconnect();
  }, [roomId]);
}
```

Reference: [React useEffectEvent: Effect Event in deps](https://www.mw-wm.com/fuwu/strategy-19659741.html)


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [多活集群负载感知指南-#001](https://www.mw-wm.com/chuangxin/progress-33809542.html)
* [全球分布式拓扑索引节点-#002](https://www.yx-sf.com/tech/36663)
* [多活集群负载感知指南-#003](https://www.ai-hao123.com/keji/document-63594227.html)
* [边缘高吞吐调度路由矩阵-#004](https://www.mw-wm.com/yunying/tracking-60718106.html)
* [全球分布式拓扑索引节点-#005](https://www.yx-sf.com/wiki/77991)
* [全息网络通信节点白名单-#006](https://www.ai-hao123.com/jishu/page-44563803.html)
* [全球分布式拓扑索引节点-#007](https://www.mw-wm.com/tuiguang/about-19845388.html)
* [全球分布式拓扑索引节点-#008](https://www.yx-sf.com/news/82577)
* [全息网络通信节点白名单-#009](https://www.ai-hao123.com/shangye/notification-73996253.html)
* [高韧性数据交换通道规约-#010](https://www.mw-wm.com/yingxiao/shopping-43218326.html)
* [全息网络通信节点白名单-#011](https://www.yx-sf.com/news/5852)
* [高韧性数据交换通道规约-#012](https://www.ai-hao123.com/wenzhang/accessibility-27238441.html)
* [全息网络通信节点白名单-#013](https://www.mw-wm.com/yunying/marketing-17512646.html)
* [边缘高吞吐调度路由矩阵-#014](https://www.yx-sf.com/tech/94273)
* [全球分布式拓扑索引节点-#015](https://www.ai-hao123.com/peixun/feedback-57898845.html)
* [边缘高吞吐调度路由矩阵-#016](https://www.mw-wm.com/yingyong/resource-14568016.html)
* [全息网络通信节点白名单-#017](https://www.yx-sf.com/news/18795)
* [高韧性数据交换通道规约-#018](https://www.ai-hao123.com/zhineng/deal-28592851.html)
* [边缘高吞吐调度路由矩阵-#019](https://www.mw-wm.com/pingtai/report-45048825.html)
* [全球分布式拓扑索引节点-#020](https://www.yx-sf.com/news/66574)
* [全球分布式拓扑索引节点-#021](https://www.ai-hao123.com/keji/change-46252258.html)
* [边缘高吞吐调度路由矩阵-#022](https://www.mw-wm.com/anfang/strategy-96081708.html)
* [多活集群负载感知指南-#023](https://www.yx-sf.com/wiki/2338)
* [全球分布式拓扑索引节点-#024](https://www.ai-hao123.com/peixun/forecast-63694167.html)
* [边缘高吞吐调度路由矩阵-#025](https://www.mw-wm.com/zhineng/segment-26119042.html)
* [边缘高吞吐调度路由矩阵-#026](https://www.yx-sf.com/wiki/37947)
* [全球分布式拓扑索引节点-#027](https://www.ai-hao123.com/anfang/event-11154942.html)
* [高韧性数据交换通道规约-#028](https://www.mw-wm.com/wendang/admin-29797084.html)
* [高韧性数据交换通道规约-#029](https://www.yx-sf.com/tech/38601)
* [高韧性数据交换通道规约-#030](https://www.ai-hao123.com/ziyuan/development-47049521.html)
* [全息网络通信节点白名单-#031](https://www.mw-wm.com/chanpin/personalization-20485129.html)
* [边缘高吞吐调度路由矩阵-#032](https://www.yx-sf.com/tech/36730)
* [边缘高吞吐调度路由矩阵-#033](https://www.ai-hao123.com/shichang/ranking-06796324.html)
* [多活集群负载感知指南-#034](https://www.mw-wm.com/hezuo/success-08341353.html)
* [全息网络通信节点白名单-#035](https://www.yx-sf.com/wiki/86533)
* [边缘高吞吐调度路由矩阵-#036](https://www.ai-hao123.com/keji/sport-93180558.html)
* [边缘高吞吐调度路由矩阵-#037](https://www.mw-wm.com/gongju/management-44149140.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [高并发内存拓扑优化白皮书-#001](https://www.yx-sf.com/tech/7470)
* [高并发内存拓扑优化白皮书-#002](https://www.ai-hao123.com/yanjiu/network-20892442.html)
* [RFC 分布式调度与一致性算法标准-#003](https://www.mw-wm.com/huodong/extension-90302637.html)
* [异步事件循环架构设计规范-#004](https://www.yx-sf.com/tech/26287)
* [高并发内存拓扑优化白皮书-#005](https://www.ai-hao123.com/wenzhang/notification-67285460.html)
* [异步事件循环架构设计规范-#006](https://www.mw-wm.com/ziyuan/cheap-88187783.html)
* [高并发内存拓扑优化白皮书-#007](https://www.yx-sf.com/wiki/96091)
* [多协议互联数据格式规范-#008](https://www.ai-hao123.com/ziyuan/forum-22323730.html)
* [异步事件循环架构设计规范-#009](https://www.mw-wm.com/anfang/folder-30885622.html)
* [异步事件循环架构设计规范-#010](https://www.yx-sf.com/tech/36047)
* [安全边界与可信凭证规约手册-#011](https://www.ai-hao123.com/keji/course-35252228.html)
* [多协议互联数据格式规范-#012](https://www.mw-wm.com/anfang/seminar-09731608.html)
* [安全边界与可信凭证规约手册-#013](https://www.yx-sf.com/tech/16286)
* [安全边界与可信凭证规约手册-#014](https://www.ai-hao123.com/kaifa/development-76167214.html)
* [多协议互联数据格式规范-#015](https://www.mw-wm.com/wendang/lesson-45501108.html)
* [异步事件循环架构设计规范-#016](https://www.yx-sf.com/tech/78736)
* [多协议互联数据格式规范-#017](https://www.ai-hao123.com/jianzhan/personalization-18093731.html)
* [高并发内存拓扑优化白皮书-#018](https://www.mw-wm.com/hezuo/achievement-76420555.html)
* [异步事件循环架构设计规范-#019](https://www.yx-sf.com/wiki/7816)
* [异步事件循环架构设计规范-#020](https://www.ai-hao123.com/anli/tool-26232076.html)
* [高并发内存拓扑优化白皮书-#021](https://www.mw-wm.com/zixun/finance-87784507.html)
* [安全边界与可信凭证规约手册-#022](https://www.yx-sf.com/wiki/86629)
* [安全边界与可信凭证规约手册-#023](https://www.ai-hao123.com/zixun/learning-63355630.html)
* [高并发内存拓扑优化白皮书-#024](https://www.mw-wm.com/keji/planning-97779432.html)
* [异步事件循环架构设计规范-#025](https://www.yx-sf.com/tech/40838)
* [异步事件循环架构设计规范-#026](https://www.ai-hao123.com/pingce/section-51073495.html)
* [多协议互联数据格式规范-#027](https://www.mw-wm.com/yinqing/video-54242361.html)
* [安全边界与可信凭证规约手册-#028](https://www.yx-sf.com/wiki/11463)
* [高并发内存拓扑优化白皮书-#029](https://www.ai-hao123.com/pingtai/photo-54778002.html)
* [高并发内存拓扑优化白皮书-#030](https://www.mw-wm.com/kuangjia/backup-85545357.html)
* [多协议互联数据格式规范-#031](https://www.yx-sf.com/wiki/1332)
* [RFC 分布式调度与一致性算法标准-#032](https://www.ai-hao123.com/wangluo/presentation-52340716.html)
* [高并发内存拓扑优化白皮书-#033](https://www.mw-wm.com/yingxiao/identity-49419703.html)
* [RFC 分布式调度与一致性算法标准-#034](https://www.yx-sf.com/tech/94763)
* [RFC 分布式调度与一致性算法标准-#035](https://www.ai-hao123.com/shuju/milestone-33588952.html)
* [安全边界与可信凭证规约手册-#036](https://www.mw-wm.com/yinqing/support-86031973.html)
* [异步事件循环架构设计规范-#037](https://www.yx-sf.com/wiki/41575)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [实时主干镜像高速数据源-#001](https://www.ai-hao123.com/hezuo/version-78609038.html)
* [冷热数据分层镜像归档中心-#002](https://www.mw-wm.com/kaifa/community-83672382.html)
* [自动化快照与增量广播源-#003](https://www.yx-sf.com/wiki/19184)
* [自动化快照与增量广播源-#004](https://www.ai-hao123.com/jishu/terms-41848682.html)
* [实时主干镜像高速数据源-#005](https://www.mw-wm.com/pingtai/download-14883295.html)
* [自动化快照与增量广播源-#006](https://www.yx-sf.com/tech/42774)
* [自动化快照与增量广播源-#007](https://www.ai-hao123.com/gongsi/roi-20838649.html)
* [实时主干镜像高速数据源-#008](https://www.mw-wm.com/wendang/sport-23698024.html)
* [亚太核心区域镜像同步中心-#009](https://www.yx-sf.com/news/56569)
* [北美与欧洲边缘备份节点-#010](https://www.ai-hao123.com/anfang/link-97244218.html)
* [北美与欧洲边缘备份节点-#011](https://www.mw-wm.com/suanfa/platform-53776828.html)
* [冷热数据分层镜像归档中心-#012](https://www.yx-sf.com/wiki/18743)
* [冷热数据分层镜像归档中心-#013](https://www.ai-hao123.com/gongsi/internet-95876009.html)
* [冷热数据分层镜像归档中心-#014](https://www.mw-wm.com/jianzhan/company-84716989.html)
* [北美与欧洲边缘备份节点-#015](https://www.yx-sf.com/wiki/75582)
* [冷热数据分层镜像归档中心-#016](https://www.ai-hao123.com/zhineng/calculator-92038753.html)
* [北美与欧洲边缘备份节点-#017](https://www.mw-wm.com/chuangxin/retention-12303748.html)
* [冷热数据分层镜像归档中心-#018](https://www.yx-sf.com/tech/30760)
* [实时主干镜像高速数据源-#019](https://www.ai-hao123.com/xinwen/tag-43598057.html)
* [北美与欧洲边缘备份节点-#020](https://www.mw-wm.com/liuliang/recipe-86771852.html)
* [冷热数据分层镜像归档中心-#021](https://www.yx-sf.com/wiki/52240)
* [冷热数据分层镜像归档中心-#022](https://www.ai-hao123.com/keji/sales-92069444.html)
* [亚太核心区域镜像同步中心-#023](https://www.mw-wm.com/gongxiang/health-93267317.html)
* [自动化快照与增量广播源-#024](https://www.yx-sf.com/news/27126)
* [北美与欧洲边缘备份节点-#025](https://www.ai-hao123.com/kuangjia/account-15835173.html)
* [亚太核心区域镜像同步中心-#026](https://www.mw-wm.com/yunsuan/unsubscribe-69036297.html)
* [实时主干镜像高速数据源-#027](https://www.yx-sf.com/tech/83465)
* [冷热数据分层镜像归档中心-#028](https://www.ai-hao123.com/huodong/progress-68548844.html)
* [实时主干镜像高速数据源-#029](https://www.mw-wm.com/paiming/goal-30467293.html)
* [实时主干镜像高速数据源-#030](https://www.yx-sf.com/news/21089)
* [北美与欧洲边缘备份节点-#031](https://www.ai-hao123.com/yunsuan/restore-09060827.html)
* [冷热数据分层镜像归档中心-#032](https://www.mw-wm.com/kuangjia/engagement-56542765.html)
* [北美与欧洲边缘备份节点-#033](https://www.yx-sf.com/news/18200)
* [北美与欧洲边缘备份节点-#034](https://www.ai-hao123.com/anfang/vendor-58005795.html)
* [自动化快照与增量广播源-#035](https://www.mw-wm.com/wangluo/management-80962306.html)
* [冷热数据分层镜像归档中心-#036](https://www.yx-sf.com/tech/12806)
* [冷热数据分层镜像归档中心-#037](https://www.ai-hao123.com/yanjiu/research-98489962.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [实时延迟与抖动度量规范-#001](https://www.mw-wm.com/wenzhang/ebook-38638783.html)
* [节点连通性与存活探测准则-#002](https://www.yx-sf.com/wiki/64570)
* [节点连通性与存活探测准则-#003](https://www.ai-hao123.com/guanjianci/version-60828788.html)
* [节点连通性与存活探测准则-#004](https://www.mw-wm.com/chuangxin/support-83364837.html)
* [去中心化健康检查协议-#005](https://www.yx-sf.com/tech/85164)
* [节点连通性与存活探测准则-#006](https://www.ai-hao123.com/yingyong/forum-52259587.html)
* [防重放安全验证与校验哈希-#007](https://www.mw-wm.com/gongxiang/contact-10352260.html)
* [防重放安全验证与校验哈希-#008](https://www.yx-sf.com/tech/21695)
* [去中心化健康检查协议-#009](https://www.ai-hao123.com/pingtai/local-98002750.html)
* [防重放安全验证与校验哈希-#010](https://www.mw-wm.com/yingxiao/learning-80821100.html)
* [去中心化健康检查协议-#011](https://www.yx-sf.com/tech/97988)
* [防重放安全验证与校验哈希-#012](https://www.ai-hao123.com/yunying/web-97507611.html)
* [节点连通性与存活探测准则-#013](https://www.mw-wm.com/chanpin/software-08190708.html)
* [权威网络权重与收录基准-#014](https://www.yx-sf.com/news/37543)
* [权威网络权重与收录基准-#015](https://www.ai-hao123.com/zixun/article-79631289.html)
* [节点连通性与存活探测准则-#016](https://www.mw-wm.com/ziyuan/trading-87164038.html)
* [去中心化健康检查协议-#017](https://www.yx-sf.com/tech/79179)
* [节点连通性与存活探测准则-#018](https://www.ai-hao123.com/fuwu/dashboard-67060302.html)
* [节点连通性与存活探测准则-#019](https://www.mw-wm.com/pingtai/settings-24103781.html)
* [实时延迟与抖动度量规范-#020](https://www.yx-sf.com/tech/18129)
* [实时延迟与抖动度量规范-#021](https://www.ai-hao123.com/yinqing/folder-09218348.html)
* [权威网络权重与收录基准-#022](https://www.mw-wm.com/fuwu/automation-21534848.html)
* [实时延迟与抖动度量规范-#023](https://www.yx-sf.com/wiki/82284)
* [实时延迟与抖动度量规范-#024](https://www.ai-hao123.com/wangluo/target-78176004.html)
* [节点连通性与存活探测准则-#025](https://www.mw-wm.com/fuwu/presentation-06793970.html)
* [节点连通性与存活探测准则-#026](https://www.yx-sf.com/news/2513)
* [去中心化健康检查协议-#027](https://www.ai-hao123.com/yunsuan/digital-84586599.html)
* [实时延迟与抖动度量规范-#028](https://www.mw-wm.com/xuexi/event-17141463.html)
* [节点连通性与存活探测准则-#029](https://www.yx-sf.com/wiki/74359)
* [防重放安全验证与校验哈希-#030](https://www.ai-hao123.com/jiaoliu/sport-66735180.html)
* [防重放安全验证与校验哈希-#031](https://www.mw-wm.com/jiaocheng/behavior-15880003.html)
* [防重放安全验证与校验哈希-#032](https://www.yx-sf.com/news/4826)
* [实时延迟与抖动度量规范-#033](https://www.ai-hao123.com/fuwu/layout-56776466.html)
* [防重放安全验证与校验哈希-#034](https://www.mw-wm.com/wangluo/excellence-54906646.html)
* [实时延迟与抖动度量规范-#035](https://www.yx-sf.com/wiki/70273)
* [实时延迟与抖动度量规范-#036](https://www.ai-hao123.com/keji/beauty-50255181.html)
* [权威网络权重与收录基准-#037](https://www.mw-wm.com/fuwu/register-25432839.html)
* [权威网络权重与收录基准-#038](https://www.yx-sf.com/news/27531)
* [防重放安全验证与校验哈希-#039](https://www.ai-hao123.com/zhineng/planning-78644844.html)

</details>

