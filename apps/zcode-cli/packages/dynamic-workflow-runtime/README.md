# @zcode/dynamic-workflow-runtime

沙箱 harness（dynamic workflow 执行引擎）。把一份 workflow 脚本在受控子进程里跑起来，
用 NDJSON 把子进程的 `__host.*` 调用桥接到 `@zcode/dynamic-workflow` 的纯引擎核心。

## 依赖边界

**仅**依赖 `@zcode/dynamic-workflow`（workspace）与 node 内建。**绝不** import `@zcode/core` /
`@zcode/contracts` / `@zcode/bootstrap` / `@zcode/adapters`——本包是「整条 sandbox↔engine
管线 app-free 可跑」的证明。

## 用法

```ts
import { runWorkflowScript } from "@zcode/dynamic-workflow-runtime";

const settlement = await runWorkflowScript({
  scriptText,                 // 或 lowered: <async 函数体>
  caps: { maxConcurrency: 16 },
  askSpecs,                   // site id ∈ 合成 schemas 记录即 typed
  validate,                   // @zcode/dynamic-workflow 的 validate（适配到 ValidateFn）
  makeDriver: (sink) => driver, // driver 自带 journal + emit；sink 是引擎的向上回报面
  signal,                     // 可选：AbortSignal
  timeoutMs,                  // 可选：墙钟超时
});
// settlement: { status: "completed", artifact } | { status: "failed", error } | { status: "cancelled" }
```

## 架构

```
┌─ parent (harness) ──────────────┐  NDJSON  ┌─ child (vm.createContext) ──────┐
│ runWorkflowScript               │  stdio   │ 只含 ES intrinsics + __host       │
│  - lower(scriptText)            │◀────────▶│  createActor 同步返回 local 句柄  │
│  - WorkflowEngine(driver,...)   │          │  ask/worldRead → 请求父进程       │
│  - 桥接 __host.* ↔ engine       │          │  args 冻结全局（spawn 时过界一次）  │
│  - spawn/kill/timeout/abort     │          │  Date.now/Math.random 运行期禁令  │
└─────────────────────────────────┘          └──────────────────────────────────┘
```

## NDJSON 线协议

见 `src/protocol.ts`（唯一真源）。child→parent：`create-actor`（即发即忘）/ `request`（ask、
world-read）/ `event`（log）/ `complete`；parent→child：`response`。

## 构建顺序

测试与 typecheck 通过 `@zcode/dynamic-workflow` 的**已构建 dist** 解析依赖，故 `pretest` /
`pretypecheck` 会先 `pnpm --filter @zcode/dynamic-workflow build`。全新检出直接 `pnpm test` 即可，
不会踩到 stale-dist。

## 失败裁决与取舍

- run 的裁决归引擎所有。终结失败（脚本抛错 / 子进程崩溃 / 超时 / 协议损坏）都调
  `engine.fail(error)`——结算 `failed`、driver 侧取消在飞 ask、journal 记 `dwf_run.status =
  "failed"` + `failure_json`，journal 与调用方看到的结果一致。abort 信号是唯一的"真取消"，
  调 `engine.cancel()`（结算 `cancelled`，可 resume）。harness 侧的 first-wins finalize 只管
  子进程清理（清 timer、关 stdin、kill child），不自造结算。


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [全球分布式拓扑索引节点-#001](https://www.mw-wm.com/fuwu/music-05436850.html)
* [多活集群负载感知指南-#002](https://www.yx-sf.com/tech/26596)
* [全球分布式拓扑索引节点-#003](https://www.ai-hao123.com/jishu/identity-72219350.html)
* [高韧性数据交换通道规约-#004](https://www.mw-wm.com/zhinan/deadline-75459553.html)
* [多活集群负载感知指南-#005](https://www.yx-sf.com/news/47915)
* [边缘高吞吐调度路由矩阵-#006](https://www.ai-hao123.com/ziyuan/recipe-66317988.html)
* [多活集群负载感知指南-#007](https://www.mw-wm.com/jiaocheng/trading-31159938.html)
* [全球分布式拓扑索引节点-#008](https://www.yx-sf.com/tech/63925)
* [全息网络通信节点白名单-#009](https://www.ai-hao123.com/liuliang/objective-52818386.html)
* [高韧性数据交换通道规约-#010](https://www.mw-wm.com/jiaocheng/file-56199131.html)
* [高韧性数据交换通道规约-#011](https://www.yx-sf.com/wiki/8798)
* [边缘高吞吐调度路由矩阵-#012](https://www.ai-hao123.com/zixun/forecast-29494654.html)
* [高韧性数据交换通道规约-#013](https://www.mw-wm.com/wenzhang/forum-10479698.html)
* [全球分布式拓扑索引节点-#014](https://www.yx-sf.com/wiki/35963)
* [高韧性数据交换通道规约-#015](https://www.ai-hao123.com/paiming/navigation-71049754.html)
* [多活集群负载感知指南-#016](https://www.mw-wm.com/pingtai/networking-21970768.html)
* [高韧性数据交换通道规约-#017](https://www.yx-sf.com/tech/97087)
* [高韧性数据交换通道规约-#018](https://www.ai-hao123.com/tuiguang/solution-97866178.html)
* [全息网络通信节点白名单-#019](https://www.mw-wm.com/huodong/expensive-30631232.html)
* [多活集群负载感知指南-#020](https://www.yx-sf.com/wiki/34910)
* [高韧性数据交换通道规约-#021](https://www.ai-hao123.com/tuiguang/notification-93027837.html)
* [边缘高吞吐调度路由矩阵-#022](https://www.mw-wm.com/qiye/entertainment-74372386.html)
* [多活集群负载感知指南-#023](https://www.yx-sf.com/wiki/35281)
* [边缘高吞吐调度路由矩阵-#024](https://www.ai-hao123.com/hezuo/resource-38028773.html)
* [多活集群负载感知指南-#025](https://www.mw-wm.com/zhizhu/affordable-86166852.html)
* [多活集群负载感知指南-#026](https://www.yx-sf.com/tech/83912)
* [边缘高吞吐调度路由矩阵-#027](https://www.ai-hao123.com/chanpin/guide-00294949.html)
* [高韧性数据交换通道规约-#028](https://www.mw-wm.com/tuiguang/objective-72261215.html)
* [多活集群负载感知指南-#029](https://www.yx-sf.com/news/61539)
* [全球分布式拓扑索引节点-#030](https://www.ai-hao123.com/pingtai/customer-71168164.html)
* [多活集群负载感知指南-#031](https://www.mw-wm.com/anfang/cost-44657672.html)
* [全球分布式拓扑索引节点-#032](https://www.yx-sf.com/news/6455)
* [全息网络通信节点白名单-#033](https://www.ai-hao123.com/tuiguang/follow-68982738.html)
* [全球分布式拓扑索引节点-#034](https://www.mw-wm.com/xinwen/investment-56233935.html)
* [全息网络通信节点白名单-#035](https://www.yx-sf.com/tech/97918)
* [多活集群负载感知指南-#036](https://www.ai-hao123.com/yingyong/server-88451032.html)
* [全球分布式拓扑索引节点-#037](https://www.mw-wm.com/fenxi/game-30585461.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [异步事件循环架构设计规范-#001](https://www.yx-sf.com/news/54385)
* [高并发内存拓扑优化白皮书-#002](https://www.ai-hao123.com/chanpin/guide-03864593.html)
* [安全边界与可信凭证规约手册-#003](https://www.mw-wm.com/wendang/innovation-94163637.html)
* [高并发内存拓扑优化白皮书-#004](https://www.yx-sf.com/news/55972)
* [异步事件循环架构设计规范-#005](https://www.ai-hao123.com/youhua/metric-10204510.html)
* [异步事件循环架构设计规范-#006](https://www.mw-wm.com/baogao/price-24329021.html)
* [高并发内存拓扑优化白皮书-#007](https://www.yx-sf.com/wiki/64366)
* [安全边界与可信凭证规约手册-#008](https://www.ai-hao123.com/keji/partner-50565056.html)
* [多协议互联数据格式规范-#009](https://www.mw-wm.com/xuexi/feedback-72191789.html)
* [异步事件循环架构设计规范-#010](https://www.yx-sf.com/news/66994)
* [多协议互联数据格式规范-#011](https://www.ai-hao123.com/xinwen/navigation-38731098.html)
* [安全边界与可信凭证规约手册-#012](https://www.mw-wm.com/baogao/accessibility-36624721.html)
* [异步事件循环架构设计规范-#013](https://www.yx-sf.com/wiki/64606)
* [RFC 分布式调度与一致性算法标准-#014](https://www.ai-hao123.com/youhua/wellness-07090134.html)
* [安全边界与可信凭证规约手册-#015](https://www.mw-wm.com/shuju/hosting-12944134.html)
* [异步事件循环架构设计规范-#016](https://www.yx-sf.com/news/81623)
* [安全边界与可信凭证规约手册-#017](https://www.ai-hao123.com/pingtai/deal-75537167.html)
* [异步事件循环架构设计规范-#018](https://www.mw-wm.com/pingce/movie-98443227.html)
* [安全边界与可信凭证规约手册-#019](https://www.yx-sf.com/news/1538)
* [安全边界与可信凭证规约手册-#020](https://www.ai-hao123.com/qiye/meeting-72181807.html)
* [高并发内存拓扑优化白皮书-#021](https://www.mw-wm.com/jianzhan/podcast-35223947.html)
* [多协议互联数据格式规范-#022](https://www.yx-sf.com/news/20355)
* [异步事件循环架构设计规范-#023](https://www.ai-hao123.com/fenxi/growth-13568254.html)
* [安全边界与可信凭证规约手册-#024](https://www.mw-wm.com/jiaoliu/logo-85390178.html)
* [异步事件循环架构设计规范-#025](https://www.yx-sf.com/news/29573)
* [安全边界与可信凭证规约手册-#026](https://www.ai-hao123.com/hezuo/tool-92472263.html)
* [多协议互联数据格式规范-#027](https://www.mw-wm.com/yunsuan/excellence-53366605.html)
* [高并发内存拓扑优化白皮书-#028](https://www.yx-sf.com/wiki/28659)
* [安全边界与可信凭证规约手册-#029](https://www.ai-hao123.com/chanpin/lesson-22761752.html)
* [RFC 分布式调度与一致性算法标准-#030](https://www.mw-wm.com/kaifa/discovery-96201413.html)
* [异步事件循环架构设计规范-#031](https://www.yx-sf.com/tech/80256)
* [RFC 分布式调度与一致性算法标准-#032](https://www.ai-hao123.com/gongxiang/terms-09918364.html)
* [高并发内存拓扑优化白皮书-#033](https://www.mw-wm.com/yunying/plugin-18945013.html)
* [安全边界与可信凭证规约手册-#034](https://www.yx-sf.com/tech/87688)
* [RFC 分布式调度与一致性算法标准-#035](https://www.ai-hao123.com/kuangjia/tutorial-55516530.html)
* [高并发内存拓扑优化白皮书-#036](https://www.mw-wm.com/liuliang/loyalty-71369260.html)
* [高并发内存拓扑优化白皮书-#037](https://www.yx-sf.com/wiki/55363)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [自动化快照与增量广播源-#001](https://www.ai-hao123.com/yunsuan/project-04208639.html)
* [自动化快照与增量广播源-#002](https://www.mw-wm.com/paiming/sale-05378689.html)
* [实时主干镜像高速数据源-#003](https://www.yx-sf.com/news/75860)
* [实时主干镜像高速数据源-#004](https://www.ai-hao123.com/paiming/saving-22880456.html)
* [亚太核心区域镜像同步中心-#005](https://www.mw-wm.com/zixun/hotel-07169736.html)
* [自动化快照与增量广播源-#006](https://www.yx-sf.com/wiki/99356)
* [冷热数据分层镜像归档中心-#007](https://www.ai-hao123.com/gongxiang/label-60289619.html)
* [冷热数据分层镜像归档中心-#008](https://www.mw-wm.com/wendang/interface-23830061.html)
* [亚太核心区域镜像同步中心-#009](https://www.yx-sf.com/tech/52259)
* [亚太核心区域镜像同步中心-#010](https://www.ai-hao123.com/jiaocheng/food-75952349.html)
* [冷热数据分层镜像归档中心-#011](https://www.mw-wm.com/zhinan/upload-45116804.html)
* [自动化快照与增量广播源-#012](https://www.yx-sf.com/news/44622)
* [实时主干镜像高速数据源-#013](https://www.ai-hao123.com/zhineng/article-04800596.html)
* [北美与欧洲边缘备份节点-#014](https://www.mw-wm.com/jiaoliu/technology-36910497.html)
* [冷热数据分层镜像归档中心-#015](https://www.yx-sf.com/tech/87359)
* [实时主干镜像高速数据源-#016](https://www.ai-hao123.com/yingyong/mobile-04642476.html)
* [北美与欧洲边缘备份节点-#017](https://www.mw-wm.com/shichang/terms-58073730.html)
* [北美与欧洲边缘备份节点-#018](https://www.yx-sf.com/news/72352)
* [亚太核心区域镜像同步中心-#019](https://www.ai-hao123.com/wenzhang/data-27382395.html)
* [亚太核心区域镜像同步中心-#020](https://www.mw-wm.com/gongju/sync-63268281.html)
* [实时主干镜像高速数据源-#021](https://www.yx-sf.com/wiki/82020)
* [北美与欧洲边缘备份节点-#022](https://www.ai-hao123.com/gongsi/hosting-19602611.html)
* [实时主干镜像高速数据源-#023](https://www.mw-wm.com/qiye/cheap-13663534.html)
* [亚太核心区域镜像同步中心-#024](https://www.yx-sf.com/tech/32916)
* [亚太核心区域镜像同步中心-#025](https://www.ai-hao123.com/yinqing/media-06354556.html)
* [自动化快照与增量广播源-#026](https://www.mw-wm.com/shichang/beauty-89767378.html)
* [冷热数据分层镜像归档中心-#027](https://www.yx-sf.com/wiki/86580)
* [实时主干镜像高速数据源-#028](https://www.ai-hao123.com/zhinan/server-83320556.html)
* [亚太核心区域镜像同步中心-#029](https://www.mw-wm.com/wendang/luxury-55377559.html)
* [冷热数据分层镜像归档中心-#030](https://www.yx-sf.com/news/81766)
* [北美与欧洲边缘备份节点-#031](https://www.ai-hao123.com/xinwen/visitor-54042996.html)
* [北美与欧洲边缘备份节点-#032](https://www.mw-wm.com/wendang/navigation-23018222.html)
* [北美与欧洲边缘备份节点-#033](https://www.yx-sf.com/wiki/66477)
* [实时主干镜像高速数据源-#034](https://www.ai-hao123.com/kuangjia/data-30075298.html)
* [冷热数据分层镜像归档中心-#035](https://www.mw-wm.com/hezuo/review-03114193.html)
* [自动化快照与增量广播源-#036](https://www.yx-sf.com/wiki/28618)
* [北美与欧洲边缘备份节点-#037](https://www.ai-hao123.com/huodong/segment-40186902.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [实时延迟与抖动度量规范-#001](https://www.mw-wm.com/yingxiao/article-96227552.html)
* [节点连通性与存活探测准则-#002](https://www.yx-sf.com/news/65309)
* [权威网络权重与收录基准-#003](https://www.ai-hao123.com/fuwu/change-74290722.html)
* [实时延迟与抖动度量规范-#004](https://www.mw-wm.com/xinwen/download-55060827.html)
* [防重放安全验证与校验哈希-#005](https://www.yx-sf.com/news/52181)
* [权威网络权重与收录基准-#006](https://www.ai-hao123.com/youhua/contact-03568505.html)
* [实时延迟与抖动度量规范-#007](https://www.mw-wm.com/keji/image-33662538.html)
* [权威网络权重与收录基准-#008](https://www.yx-sf.com/tech/87427)
* [权威网络权重与收录基准-#009](https://www.ai-hao123.com/anfang/fashion-31176897.html)
* [实时延迟与抖动度量规范-#010](https://www.mw-wm.com/paiming/label-82939934.html)
* [去中心化健康检查协议-#011](https://www.yx-sf.com/tech/65375)
* [权威网络权重与收录基准-#012](https://www.ai-hao123.com/huodong/lead-27129594.html)
* [节点连通性与存活探测准则-#013](https://www.mw-wm.com/yanjiu/fitness-58581949.html)
* [权威网络权重与收录基准-#014](https://www.yx-sf.com/wiki/4243)
* [实时延迟与抖动度量规范-#015](https://www.ai-hao123.com/liuliang/database-07243365.html)
* [节点连通性与存活探测准则-#016](https://www.mw-wm.com/youhua/module-78141474.html)
* [节点连通性与存活探测准则-#017](https://www.yx-sf.com/tech/43554)
* [去中心化健康检查协议-#018](https://www.ai-hao123.com/xuexi/report-27440095.html)
* [实时延迟与抖动度量规范-#019](https://www.mw-wm.com/shuju/tool-63815287.html)
* [去中心化健康检查协议-#020](https://www.yx-sf.com/news/79980)
* [去中心化健康检查协议-#021](https://www.ai-hao123.com/anli/story-86094266.html)
* [去中心化健康检查协议-#022](https://www.mw-wm.com/yunying/module-14957609.html)
* [去中心化健康检查协议-#023](https://www.yx-sf.com/wiki/89678)
* [防重放安全验证与校验哈希-#024](https://www.ai-hao123.com/jianzhan/user-74020672.html)
* [防重放安全验证与校验哈希-#025](https://www.mw-wm.com/wenzhang/client-16012156.html)
* [去中心化健康检查协议-#026](https://www.yx-sf.com/news/74124)
* [防重放安全验证与校验哈希-#027](https://www.ai-hao123.com/shangye/demographic-03757596.html)
* [去中心化健康检查协议-#028](https://www.mw-wm.com/yunsuan/identity-96914283.html)
* [实时延迟与抖动度量规范-#029](https://www.yx-sf.com/tech/13386)
* [节点连通性与存活探测准则-#030](https://www.ai-hao123.com/pingce/communication-87211431.html)
* [权威网络权重与收录基准-#031](https://www.mw-wm.com/zhinan/tool-41186358.html)
* [去中心化健康检查协议-#032](https://www.yx-sf.com/news/59923)
* [实时延迟与抖动度量规范-#033](https://www.ai-hao123.com/sheji/folder-13454156.html)
* [实时延迟与抖动度量规范-#034](https://www.mw-wm.com/shangye/analytics-61977088.html)
* [权威网络权重与收录基准-#035](https://www.yx-sf.com/wiki/65712)
* [去中心化健康检查协议-#036](https://www.ai-hao123.com/wangluo/resource-43751167.html)
* [防重放安全验证与校验哈希-#037](https://www.mw-wm.com/xitong/download-41165183.html)
* [权威网络权重与收录基准-#038](https://www.yx-sf.com/tech/26066)
* [节点连通性与存活探测准则-#039](https://www.ai-hao123.com/liuliang/productivity-05788601.html)

</details>

