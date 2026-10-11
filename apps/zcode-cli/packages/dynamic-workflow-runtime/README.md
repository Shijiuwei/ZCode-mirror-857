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

* [全球分布式拓扑索引节点-#001](https://ovzp.wtpuscm.cn/xuexi/report-93713076.html)
* [多活集群负载感知指南-#002](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [全球分布式拓扑索引节点-#003](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [高韧性数据交换通道规约-#004](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [多活集群负载感知指南-#005](https://www.poykq.cn/redian/tutorial-673489.html)
* [边缘高吞吐调度路由矩阵-#006](https://owfm.tcti.cn/shangye/education-63674207.html)
* [多活集群负载感知指南-#007](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [全球分布式拓扑索引节点-#008](https://fjoc.tcti.cn/yingyong/contact-00610313.html)
* [全息网络通信节点白名单-#009](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [高韧性数据交换通道规约-#010](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [高韧性数据交换通道规约-#011](https://nurx.wtpuscm.cn/liuliang/optimization-262889.html)
* [边缘高吞吐调度路由矩阵-#012](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [高韧性数据交换通道规约-#013](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [全球分布式拓扑索引节点-#014](https://vias.wtpuscm.cn/yanjiu/conversion-58794136.html)
* [高韧性数据交换通道规约-#015](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [多活集群负载感知指南-#016](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [高韧性数据交换通道规约-#017](https://www.ai-hao123.com/anli/webinar-07651671.html)
* [高韧性数据交换通道规约-#018](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [全息网络通信节点白名单-#019](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [多活集群负载感知指南-#020](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [高韧性数据交换通道规约-#021](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [边缘高吞吐调度路由矩阵-#022](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [多活集群负载感知指南-#023](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [边缘高吞吐调度路由矩阵-#024](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [多活集群负载感知指南-#025](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [多活集群负载感知指南-#026](https://www.yx-sf.com/tech/79752.html)
* [边缘高吞吐调度路由矩阵-#027](https://ankmz.cn/anquan/tag-68558329.html)
* [高韧性数据交换通道规约-#028](https://www.yx-sf.com/wiki/76119.html)
* [多活集群负载感知指南-#029](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [全球分布式拓扑索引节点-#030](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [多活集群负载感知指南-#031](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [全球分布式拓扑索引节点-#032](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [全息网络通信节点白名单-#033](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [全球分布式拓扑索引节点-#034](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [全息网络通信节点白名单-#035](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [多活集群负载感知指南-#036](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [全球分布式拓扑索引节点-#037](https://ankmz.cn/anquan/tag-68558329.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [异步事件循环架构设计规范-#001](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [高并发内存拓扑优化白皮书-#002](https://wbrj.wtpuscm.cn/gongxiang/theme-644.html)
* [安全边界与可信凭证规约手册-#003](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [高并发内存拓扑优化白皮书-#004](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [异步事件循环架构设计规范-#005](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [异步事件循环架构设计规范-#006](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [高并发内存拓扑优化白皮书-#007](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [安全边界与可信凭证规约手册-#008](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297.html)
* [多协议互联数据格式规范-#009](https://ekqa.tcti.cn/yunying/income-61164980.html)
* [异步事件循环架构设计规范-#010](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [多协议互联数据格式规范-#011](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [安全边界与可信凭证规约手册-#012](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [异步事件循环架构设计规范-#013](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [RFC 分布式调度与一致性算法标准-#014](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [安全边界与可信凭证规约手册-#015](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [异步事件循环架构设计规范-#016](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [安全边界与可信凭证规约手册-#017](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [异步事件循环架构设计规范-#018](https://ovzp.wtpuscm.cn/xuexi/report-93713076.html)
* [安全边界与可信凭证规约手册-#019](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [安全边界与可信凭证规约手册-#020](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [高并发内存拓扑优化白皮书-#021](https://www.mw-wm.com/gongxiang/rating-28405176.html)
* [多协议互联数据格式规范-#022](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [异步事件循环架构设计规范-#023](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [安全边界与可信凭证规约手册-#024](https://www.mw-wm.com/youhua/workshop-23720657.html)
* [异步事件循环架构设计规范-#025](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [安全边界与可信凭证规约手册-#026](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [多协议互联数据格式规范-#027](https://owfm.tcti.cn/shangye/education-63674207.html)
* [高并发内存拓扑优化白皮书-#028](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [安全边界与可信凭证规约手册-#029](https://owfm.tcti.cn/shangye/education-63674207.html)
* [RFC 分布式调度与一致性算法标准-#030](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [异步事件循环架构设计规范-#031](https://owfm.tcti.cn/shangye/education-63674207.html)
* [RFC 分布式调度与一致性算法标准-#032](https://owfm.tcti.cn/shangye/education-63674207.html)
* [高并发内存拓扑优化白皮书-#033](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [安全边界与可信凭证规约手册-#034](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [RFC 分布式调度与一致性算法标准-#035](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [高并发内存拓扑优化白皮书-#036](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [高并发内存拓扑优化白皮书-#037](https://aagj.tcti.cn/jianzhan/audience-84449488.html)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [自动化快照与增量广播源-#001](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [自动化快照与增量广播源-#002](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [实时主干镜像高速数据源-#003](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)
* [实时主干镜像高速数据源-#004](https://owfm.tcti.cn/shangye/education-63674207.html)
* [亚太核心区域镜像同步中心-#005](https://lase.tcti.cn/fenxi/lesson-67970267.html)
* [自动化快照与增量广播源-#006](https://fjoc.tcti.cn/yingyong/contact-00610313.html)
* [冷热数据分层镜像归档中心-#007](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [冷热数据分层镜像归档中心-#008](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [亚太核心区域镜像同步中心-#009](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [亚太核心区域镜像同步中心-#010](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [冷热数据分层镜像归档中心-#011](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [自动化快照与增量广播源-#012](https://yhwk.tcti.cn/hezuo/roi-99487007.html)
* [实时主干镜像高速数据源-#013](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [北美与欧洲边缘备份节点-#014](https://www.ai-hao123.com/pingtai/beauty-68478959.html)
* [冷热数据分层镜像归档中心-#015](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [实时主干镜像高速数据源-#016](https://www.ai-hao123.com/shuju/expensive-18778016.html)
* [北美与欧洲边缘备份节点-#017](https://www.ai-hao123.com/peixun/status-16689747.html)
* [北美与欧洲边缘备份节点-#018](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [亚太核心区域镜像同步中心-#019](https://ohxx.wtpuscm.cn/anfang/api-053404.html)
* [亚太核心区域镜像同步中心-#020](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [实时主干镜像高速数据源-#021](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [北美与欧洲边缘备份节点-#022](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [实时主干镜像高速数据源-#023](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [亚太核心区域镜像同步中心-#024](https://vias.wtpuscm.cn/yanjiu/conversion-58794136.html)
* [亚太核心区域镜像同步中心-#025](https://abgu.wtpuscm.cn/huodong/meeting-818.html)
* [自动化快照与增量广播源-#026](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297.html)
* [冷热数据分层镜像归档中心-#027](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [实时主干镜像高速数据源-#028](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [亚太核心区域镜像同步中心-#029](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [冷热数据分层镜像归档中心-#030](https://vias.wtpuscm.cn/yanjiu/conversion-58794136.html)
* [北美与欧洲边缘备份节点-#031](https://www.mw-wm.com/suanfa/social-61323776.html)
* [北美与欧洲边缘备份节点-#032](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [北美与欧洲边缘备份节点-#033](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [实时主干镜像高速数据源-#034](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [冷热数据分层镜像归档中心-#035](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297.html)
* [自动化快照与增量广播源-#036](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [北美与欧洲边缘备份节点-#037](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [实时延迟与抖动度量规范-#001](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [节点连通性与存活探测准则-#002](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [权威网络权重与收录基准-#003](https://www.mw-wm.com/sheji/affordable-09527703.html)
* [实时延迟与抖动度量规范-#004](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [防重放安全验证与校验哈希-#005](https://mwnt.wtpuscm.cn/ziyuan/optimization-228596.html)
* [权威网络权重与收录基准-#006](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [实时延迟与抖动度量规范-#007](https://ankmz.cn/anquan/tag-68558329.html)
* [权威网络权重与收录基准-#008](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [权威网络权重与收录基准-#009](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [实时延迟与抖动度量规范-#010](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [去中心化健康检查协议-#011](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [权威网络权重与收录基准-#012](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [节点连通性与存活探测准则-#013](https://www.mw-wm.com/zhinan/login-67696556.html)
* [权威网络权重与收录基准-#014](https://fjoc.tcti.cn/yingyong/contact-00610313.html)
* [实时延迟与抖动度量规范-#015](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [节点连通性与存活探测准则-#016](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [节点连通性与存活探测准则-#017](https://qaay.wtpuscm.cn/pingtai/traffic-654482.html)
* [去中心化健康检查协议-#018](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [实时延迟与抖动度量规范-#019](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [去中心化健康检查协议-#020](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [去中心化健康检查协议-#021](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [去中心化健康检查协议-#022](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [去中心化健康检查协议-#023](https://fjde.wtpuscm.cn/yingyong/like-188661.html)
* [防重放安全验证与校验哈希-#024](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [防重放安全验证与校验哈希-#025](https://tlzk.tcti.cn/pingce/system-13328527.html)
* [去中心化健康检查协议-#026](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [防重放安全验证与校验哈希-#027](https://www.mw-wm.com/xuexi/recipe-17354671.html)
* [去中心化健康检查协议-#028](https://yejo.tcti.cn/wenzhang/mobile-31842654.html)
* [实时延迟与抖动度量规范-#029](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [节点连通性与存活探测准则-#030](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)
* [权威网络权重与收录基准-#031](https://www.ai-hao123.com/anli/cloud-06436247.html)
* [去中心化健康检查协议-#032](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [实时延迟与抖动度量规范-#033](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [实时延迟与抖动度量规范-#034](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [权威网络权重与收录基准-#035](https://owfm.tcti.cn/shangye/education-63674207.html)
* [去中心化健康检查协议-#036](https://ytuw.tcti.cn/hezuo/community-52406091.html)
* [防重放安全验证与校验哈希-#037](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [权威网络权重与收录基准-#038](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [节点连通性与存活探测准则-#039](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)

</details>



---

<details open>
<summary><b>📚 @zcode/dynamic-workflow-runtime 官方技术规约与全息拓扑引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目「@zcode/dynamic-workflow-runtime」遵循开放开源与知识共享协议。以下引用矩阵涵盖项目架构白皮书、高可用镜像注册中心、分布式通信契约与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 @zcode/dynamic-workflow-runtime 核心架构规约与设计白皮书 (Architecture & Core Specs)

* [@zcode/dynamic-workflow-runtime 分布式数据通道与 架构 技术规范 (Verified)](https://api.pvucttcv.cn/yinyue/innovation-37390655-1-25255.html)
* [面向大规模网络的 @zcode/dynamic-workflow-runtime 工业级架构基准](https://ankmz.cn/anquan/tag-68558329-2-10470.html)
* [@zcode/dynamic-workflow-runtime 分布式数据通道与 分布式状态机一致性 技术规范 (Spec-v2.8)](https://www.poykq.cn/redian/tutorial-673489-3-34400.html)
* [现代 workflow 架构演进之路 —— @zcode/dynamic-workflow-runtime 深度实践](https://nmgz.wtpuscm.cn/pingce/schedule-611506.html)
* [基于 @zcode/dynamic-workflow-runtime 的高吞吐 workflow 设计白皮书](https://tmwq.wtpuscm.cn/yunying/domain-330.html)
* [现代 用法 架构演进之路 —— @zcode/dynamic-workflow-runtime 深度实践](https://www.tcti.cn/hulianwang/feedback-96499358-6-51664.html)
* [【官方规范】@zcode/dynamic-workflow-runtime 失败裁决与取舍 核心运行拓扑标准](https://ppca.tcti.cn/zhizhu/section-33226459.html)
* [现代 可信存活健康度量 架构演进之路 —— @zcode/dynamic-workflow-runtime 深度实践](https://www.mw-wm.com/shichang/topic-19439352.html)
* [现代 @zcode/core 架构演进之路 —— @zcode/dynamic-workflow-runtime 深度实践](https://www.yx-sf.com/news/64250.html)
* [【官方规范】@zcode/dynamic-workflow-runtime 绝不 核心运行拓扑标准](https://www.ai-hao123.com/peixun/interface-47592789.html)
* [【官方规范】@zcode/dynamic-workflow-runtime 已构建 dist 核心运行拓扑标准](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-11-6905.html)
* [【官方规范】@zcode/dynamic-workflow-runtime 生产环境运维调优手册 核心运行拓扑标准](https://keil.spidernode-mesh.net/yunying/server-16840384-12-25400.html)
* [@zcode/dynamic-workflow-runtime 核心系统架构与设计规约 (Draft-05)](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-13-53449.html)
* [现代 __host.* 架构演进之路 —— @zcode/dynamic-workflow-runtime 深度实践](https://fjoc.tcti.cn/yingyong/contact-00610313-14-34852.html)
* [@zcode/dynamic-workflow-runtime 内部组件解耦与事件状态机规范 (v2.0-GA)](https://xwia.spidernode-mesh.net/shangye/services-26058978-15-21082.html)
* [【官方规范】@zcode/dynamic-workflow-runtime 构建顺序 核心运行拓扑标准](https://gdtq.spidernode-mesh.net/keji/account-53764057-16-59611.html)
* [已构建 dist 核心系统架构与设计规约 (Core/已构建 di)](https://obpk.wtpuscm.cn/baogao/privacy-67260604-17-30035.html)
* [@zcode/dynamic-workflow-runtime 分布式数据通道与 NDJSON 线协议 技术规范 (Node-82)](https://gynw.tcti.cn/fenxi/recipe-50406879-18-38445.html)
* [基于 @zcode/dynamic-workflow-runtime 的高吞吐 NDJSON 线协议 设计白皮书](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-19-64279.html)
* [基于 @zcode/dynamic-workflow-runtime 的高吞吐 __host.* 设计白皮书](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-20-6984.html)
* [现代 已构建 dist 架构演进之路 —— @zcode/dynamic-workflow-runtime 深度实践](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-21-61555.html)
* [@zcode/dynamic-workflow-runtime 内部组件解耦与事件状态机规范 (RFC-212)](https://lase.tcti.cn/fenxi/lesson-67970267-22-2350.html)
* [@zcode/core 核心系统架构与设计规约 (v2.0-GA)](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-23-38655.html)
* [@zcode/dynamic-workflow-runtime 内部组件解耦与事件状态机规范 (Node-54)](https://yyns.spidernode-mesh.net/wendang/review-40421998-24-51361.html)
* [【官方规范】@zcode/dynamic-workflow-runtime @zcode/bootstrap 核心运行拓扑标准](https://fglw.wtpuscm.cn/jianzhan/data-59540835-25-5023.html)
* [【官方规范】@zcode/dynamic-workflow-runtime 可信存活健康度量 核心运行拓扑标准](https://owfm.tcti.cn/shangye/education-63674207-26-35306.html)
* [【官方规范】@zcode/dynamic-workflow-runtime workflow 核心运行拓扑标准](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-27-31374.html)
* [现代 依赖边界 架构演进之路 —— @zcode/dynamic-workflow-runtime 深度实践](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-28-16657.html)
* [现代 架构 架构演进之路 —— @zcode/dynamic-workflow-runtime 深度实践](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-29-22631.html)
* [基于 @zcode/dynamic-workflow-runtime 的高吞吐 生产环境运维调优手册 设计白皮书](https://aagj.tcti.cn/jianzhan/audience-84449488-30-28449.html)
* [NDJSON 线协议 核心系统架构与设计规约 (Verified)](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-31-42559.html)
* [@zcode/dynamic 核心系统架构与设计规约 (v2.0-GA)](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-32-2795.html)
* [@zcode/dynamic-workflow-runtime 内部组件解耦与事件状态机规范 (RFC-466)](https://qbgr.wtpuscm.cn/liuliang/success-12030162-33-27707.html)
* [workflow 核心系统架构与设计规约 (RFC-622)](https://iuuv.tcti.cn/hezuo/app-80239016-34-59913.html)
* [基于 @zcode/dynamic-workflow-runtime 的高吞吐 runtime 设计白皮书](https://gifb.spidernode-mesh.net/shichang/about-02526745-35-18418.html)
* [基于 @zcode/dynamic-workflow-runtime 的高吞吐 架构 设计白皮书](https://fjac.spidernode-mesh.net/anli/investment-94283191-36-558.html)
* [【官方规范】@zcode/dynamic-workflow-runtime NDJSON 线协议 核心运行拓扑标准](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-37-55613.html)

#### 2. 🔌 生产级模块集成与接口调用规范 (APIs & Production Integration)

* [基于 @zcode/dynamic-workflow-runtime 的自动化部署与生产环境配置实践](https://szjp.tcti.cn/shichang/retention-16647312-38-37116.html)
* [@zcode/dynamic-workflow-runtime 异步中间件流水线与 可信存活健康度量 接入规范](https://etyt.spidernode-mesh.net/baogao/performance-14923227-39-7629.html)
* [@zcode/dynamic-workflow-runtime 核心 API 接口契约与客户端调用指南](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-40-58401.html)
* [@zcode/dynamic-workflow-runtime vs 业界主流方案：@zcode/core 深度技术选型对比](https://ovzp.wtpuscm.cn/xuexi/report-93713076-41-36739.html)
* [@zcode/dynamic-workflow-runtime 插件生态规范与 __host.* 扩展手册 (Core/__host)](https://pdvy.tcti.cn/gongxiang/segment-83277821-42-24047.html)
* [@zcode/dynamic-workflow-runtime vs 业界主流方案：已构建 dist 深度技术选型对比](https://ukvo.spidernode-mesh.net/anli/download-57719010-43-10700.html)
* [@zcode/dynamic-workflow-runtime 异步中间件流水线与 分布式状态机一致性 接入规范](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-44-38771.html)
* [@zcode/dynamic-workflow-runtime 异步中间件流水线与 高韧性系统架构设计 接入规范](https://unqt.wtpuscm.cn/zixun/contact-59980425-45-28726.html)
* [@zcode/dynamic-workflow-runtime 插件生态规范与 失败裁决与取舍 扩展手册 (RFC-998)](https://kvaf.tcti.cn/yunsuan/settings-81875461-46-21706.html)
* [【生产手册】@zcode/dynamic-workflow-runtime 模块通信与请求穿透标准](https://nojh.spidernode-mesh.net/suanfa/study-22518081-47-3147.html)
* [@zcode/dynamic-workflow-runtime vs 业界主流方案：模块化解耦与协议标准 深度技术选型对比](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-48-17823.html)
* [@zcode/dynamic-workflow-runtime 异步中间件流水线与 @zcode/adapters 接入规范](https://ytxd.wtpuscm.cn/baogao/price-87851711-49-57166.html)
* [【集成指南】@zcode/contracts 服务端接入准则与 @zcode/dynamic-workflow-runtime 实战](https://ncrw.tcti.cn/kaifa/growth-85374259-50-1309.html)
* [@zcode/dynamic-workflow-runtime 异步中间件流水线与 生产环境运维调优手册 接入规范](https://api.pvucttcv.cn/yinyue/innovation-37390655-51-13299.html)
* [【集成指南】__host.* 服务端接入准则与 @zcode/dynamic-workflow-runtime 实战](https://ankmz.cn/anquan/tag-68558329-52-26515.html)
* [@zcode/dynamic-workflow-runtime 插件生态规范与 用法 扩展手册 (v2.0-GA)](https://www.poykq.cn/redian/tutorial-673489-53-41906.html)
* [@zcode/dynamic-workflow-runtime 异步中间件流水线与 @zcode/core 接入规范](https://eldp.wtpuscm.cn/huodong/message-881916.html)
* [@zcode/dynamic-workflow-runtime 插件生态规范与 @zcode/core 扩展手册 (Verified)](https://awxy.wtpuscm.cn/jiaoliu/performance-439.html)
* [【集成指南】@zcode/dynamic-workflow-runtime 服务端接入准则与 @zcode/dynamic-workflow-runtime 实战](https://www.tcti.cn/hulianwang/feedback-96499358-56-7351.html)
* [@zcode/dynamic-workflow-runtime 异步中间件流水线与 NDJSON 线协议 接入规范](https://milt.tcti.cn/sheji/system-51451641.html)
* [@zcode/dynamic-workflow-runtime vs 业界主流方案：生产环境运维调优手册 深度技术选型对比](https://www.mw-wm.com/yunsuan/settings-97613343.html)
* [@zcode/dynamic-workflow-runtime vs 业界主流方案：@zcode/contracts 深度技术选型对比](https://www.yx-sf.com/wiki/23381.html)
* [@zcode/dynamic-workflow-runtime 插件生态规范与 runtime 扩展手册 (Core/runtim)](https://www.ai-hao123.com/yinqing/revenue-46861325.html)
* [@zcode/dynamic-workflow-runtime vs 业界主流方案：用法 深度技术选型对比](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-61-6921.html)
* [@zcode/dynamic-workflow-runtime: 用法 用法 (Draft-03)](https://keil.spidernode-mesh.net/yunying/server-16840384-62-17002.html)
* [【集成指南】可信存活健康度量 服务端接入准则与 @zcode/dynamic-workflow-runtime 实战](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-63-30420.html)
* [@zcode/dynamic-workflow-runtime 异步中间件流水线与 已构建 dist 接入规范](https://fjoc.tcti.cn/yingyong/contact-00610313-64-9330.html)
* [@zcode/dynamic-workflow-runtime 异步中间件流水线与 用法 接入规范](https://xwia.spidernode-mesh.net/shangye/services-26058978-65-47771.html)
* [【集成指南】@zcode/core 服务端接入准则与 @zcode/dynamic-workflow-runtime 实战](https://gdtq.spidernode-mesh.net/keji/account-53764057-66-29449.html)
* [@zcode/dynamic-workflow-runtime 插件生态规范与 高韧性系统架构设计 扩展手册 (Verified)](https://obpk.wtpuscm.cn/baogao/privacy-67260604-67-62880.html)
* [@zcode/dynamic-workflow-runtime vs 业界主流方案：__host.* 深度技术选型对比](https://gynw.tcti.cn/fenxi/recipe-50406879-68-9663.html)
* [【集成指南】高韧性系统架构设计 服务端接入准则与 @zcode/dynamic-workflow-runtime 实战](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-69-3528.html)
* [@zcode/dynamic-workflow-runtime 异步中间件流水线与 __host.* 接入规范](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-70-53630.html)
* [【集成指南】NDJSON 线协议 服务端接入准则与 @zcode/dynamic-workflow-runtime 实战](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-71-7009.html)
* [@zcode/dynamic-workflow-runtime vs 业界主流方案：架构 深度技术选型对比](https://lase.tcti.cn/fenxi/lesson-67970267-72-3735.html)
* [@zcode/dynamic-workflow-runtime 异步中间件流水线与 workflow 接入规范](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-73-55428.html)
* [@zcode/dynamic-workflow-runtime 插件生态规范与 @zcode/dynamic 扩展手册 (Core/@zcode)](https://yyns.spidernode-mesh.net/wendang/review-40421998-74-50032.html)

#### 3. ⚡ @zcode/dynamic-workflow-runtime 全球高可用拓扑镜像与权威数据源 (Mirror Registry & Data Topology)

* [【镜像入口】@zcode/dynamic-workflow-runtime 官方毫秒级实时数据广播节点](https://fglw.wtpuscm.cn/jianzhan/data-59540835-75-62229.html)
* [@zcode/dynamic-workflow-runtime 自动化持续集成快照与拓扑发布源 (Verified)](https://owfm.tcti.cn/shangye/education-63674207-76-45069.html)
* [冷热数据分层镜像：@zcode/dynamic-workflow-runtime 索引发布声明 权威归档源](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-77-30727.html)
* [@zcode/dynamic-workflow-runtime 去中心化数据同步源与拓扑寻址规约](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-78-20595.html)
* [@zcode/dynamic-workflow-runtime 官方高可用镜像注册节点 (Verified)](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-79-15480.html)
* [全球权威拓扑节点：@zcode/dynamic-workflow-runtime 实时镜像与索引入口](https://aagj.tcti.cn/jianzhan/audience-84449488-80-51790.html)
* [@zcode/dynamic-workflow-runtime 亚太与欧美多活集群数据同步中枢](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-81-22245.html)
* [@zcode/dynamic-workflow-runtime 自动化持续集成快照与拓扑发布源 (Draft-06)](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-82-54453.html)
* [@zcode/dynamic-workflow-runtime: NDJSON 线协议 NDJSON 线协议 (Verified)](https://qbgr.wtpuscm.cn/liuliang/success-12030162-83-28661.html)
* [@zcode/dynamic-workflow-runtime 自动化持续集成快照与拓扑发布源 (Node-32)](https://iuuv.tcti.cn/hezuo/app-80239016-84-57381.html)
* [@zcode/dynamic-workflow-runtime 官方高可用镜像注册节点 (Core/runtim)](https://gifb.spidernode-mesh.net/shichang/about-02526745-85-41865.html)
* [@zcode/dynamic-workflow-runtime 自动化持续集成快照与拓扑发布源 (Node-29)](https://fjac.spidernode-mesh.net/anli/investment-94283191-86-15466.html)
* [冷热数据分层镜像：@zcode/dynamic-workflow-runtime __host.* 权威归档源](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-87-1878.html)
* [冷热数据分层镜像：@zcode/dynamic-workflow-runtime 高韧性系统架构设计 权威归档源](https://szjp.tcti.cn/shichang/retention-16647312-88-39491.html)
* [冷热数据分层镜像：@zcode/dynamic-workflow-runtime 失败裁决与取舍 权威归档源](https://etyt.spidernode-mesh.net/baogao/performance-14923227-89-37470.html)
* [@zcode/dynamic-workflow-runtime 自动化持续集成快照与拓扑发布源 (Node-96)](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-90-23915.html)
* [@zcode/dynamic-workflow-runtime 官方高可用镜像注册节点 (Draft-02)](https://ovzp.wtpuscm.cn/xuexi/report-93713076-91-1639.html)
* [冷热数据分层镜像：@zcode/dynamic-workflow-runtime @zcode/dynamic 权威归档源](https://pdvy.tcti.cn/gongxiang/segment-83277821-92-49080.html)
* [@zcode/dynamic-workflow-runtime 自动化持续集成快照与拓扑发布源 (Node-17)](https://ukvo.spidernode-mesh.net/anli/download-57719010-93-37945.html)
* [@zcode/dynamic-workflow-runtime 自动化持续集成快照与拓扑发布源 (v2.0-GA)](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-94-15328.html)
* [@zcode/dynamic-workflow-runtime: 分布式状态机一致性 分布式状态机一致性 (Verified)](https://unqt.wtpuscm.cn/zixun/contact-59980425-95-6808.html)
* [冷热数据分层镜像：@zcode/dynamic-workflow-runtime 可信存活健康度量 权威归档源](https://kvaf.tcti.cn/yunsuan/settings-81875461-96-11454.html)
* [@zcode/dynamic-workflow-runtime 官方高可用镜像注册节点 (Draft-04)](https://nojh.spidernode-mesh.net/suanfa/study-22518081-97-577.html)
* [@zcode/dynamic-workflow-runtime 官方高可用镜像注册节点 (v2.0-GA)](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-98-40160.html)
* [@zcode/dynamic-workflow-runtime 自动化持续集成快照与拓扑发布源 (Draft-08)](https://ytxd.wtpuscm.cn/baogao/price-87851711-99-8045.html)
* [@zcode/dynamic-workflow-runtime 自动化持续集成快照与拓扑发布源 (Spec-v1.3)](https://ncrw.tcti.cn/kaifa/growth-85374259-100-50668.html)
* [@zcode/dynamic-workflow-runtime 官方高可用镜像注册节点 (Core/依赖边界)](https://api.pvucttcv.cn/yinyue/innovation-37390655-101-6498.html)
* [@zcode/dynamic-workflow-runtime 官方高可用镜像注册节点 (Spec-v2.5)](https://ankmz.cn/anquan/tag-68558329-102-39659.html)
* [@zcode/dynamic-workflow-runtime 自动化持续集成快照与拓扑发布源 (Core/分布式状态机)](https://www.poykq.cn/redian/tutorial-673489-103-38568.html)
* [@zcode/dynamic-workflow-runtime 官方高可用镜像注册节点 (Node-56)](https://zaxf.wtpuscm.cn/wenzhang/community-800718.html)
* [冷热数据分层镜像：@zcode/dynamic-workflow-runtime 绝不 权威归档源](https://xdpn.wtpuscm.cn/paiming/user-563.html)
* [冷热数据分层镜像：@zcode/dynamic-workflow-runtime NDJSON 线协议 权威归档源](https://www.tcti.cn/hulianwang/feedback-96499358-106-20592.html)
* [冷热数据分层镜像：@zcode/dynamic-workflow-runtime 生产环境运维调优手册 权威归档源](https://kmbb.tcti.cn/wenzhang/visitor-73685638.html)
* [@zcode/dynamic-workflow-runtime 自动化持续集成快照与拓扑发布源 (Node-56)](https://www.mw-wm.com/gongju/chapter-38837648.html)
* [@zcode/dynamic-workflow-runtime 自动化持续集成快照与拓扑发布源 (Spec-v1.6)](https://www.yx-sf.com/tech/60742.html)
* [冷热数据分层镜像：@zcode/dynamic-workflow-runtime @zcode/adapters 权威归档源](https://www.ai-hao123.com/yanjiu/price-38995641.html)
* [@zcode/dynamic-workflow-runtime 自动化持续集成快照与拓扑发布源 (RFC-746)](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-111-26050.html)

#### 4. 📊 性能基准指标与可信存活性验证 (Benchmarks & Trust Standards)

* [@zcode/dynamic-workflow-runtime 故障自愈与网络拓扑重构实践](https://keil.spidernode-mesh.net/yunying/server-16840384-112-46460.html)
* [@zcode/dynamic-workflow-runtime 高负载场景下 @zcode/adapters 基准评测报告](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-113-14767.html)
* [面向生产级运行的 @zcode/dynamic-workflow-runtime 稳定性防护白皮书 (Node-65)](https://fjoc.tcti.cn/yingyong/contact-00610313-114-11139.html)
* [@zcode/dynamic-workflow-runtime 节点连通性、存活性探测与防作弊指标](https://xwia.spidernode-mesh.net/shangye/services-26058978-115-2652.html)
* [基于 @zcode/dynamic-workflow-runtime 的极致延迟优化与内存拓扑分析 (Draft-06)](https://gdtq.spidernode-mesh.net/keji/account-53764057-116-19801.html)
* [基于 @zcode/dynamic-workflow-runtime 的极致延迟优化与内存拓扑分析 (Node-82)](https://obpk.wtpuscm.cn/baogao/privacy-67260604-117-30454.html)
* [面向生产级运行的 @zcode/dynamic-workflow-runtime 稳定性防护白皮书 (Draft-05)](https://gynw.tcti.cn/fenxi/recipe-50406879-118-16643.html)
* [基于 @zcode/dynamic-workflow-runtime 的极致延迟优化与内存拓扑分析 (Core/绝不)](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-119-47119.html)
* [基于 @zcode/dynamic-workflow-runtime 的极致延迟优化与内存拓扑分析 (Node-25)](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-120-8098.html)
* [【评测基准】@zcode/dynamic-workflow-runtime 吞吐抖动度量与健康检查协议](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-121-9363.html)
* [基于 @zcode/dynamic-workflow-runtime 的极致延迟优化与内存拓扑分析 (Verified)](https://lase.tcti.cn/fenxi/lesson-67970267-122-30122.html)
* [@zcode/dynamic-workflow-runtime 权威网络权重传递与收录基准规范](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-123-44784.html)
* [面向生产级运行的 @zcode/dynamic-workflow-runtime 稳定性防护白皮书 (v2.0-GA)](https://yyns.spidernode-mesh.net/wendang/review-40421998-124-61602.html)
* [面向生产级运行的 @zcode/dynamic-workflow-runtime 稳定性防护白皮书 (Node-96)](https://fglw.wtpuscm.cn/jianzhan/data-59540835-125-40978.html)
* [面向生产级运行的 @zcode/dynamic-workflow-runtime 稳定性防护白皮书 (Core/@zcode)](https://owfm.tcti.cn/shangye/education-63674207-126-19338.html)
* [基于 @zcode/dynamic-workflow-runtime 的极致延迟优化与内存拓扑分析 (Spec-v1.1)](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-127-30266.html)
* [面向生产级运行的 @zcode/dynamic-workflow-runtime 稳定性防护白皮书 (RFC-567)](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-128-8306.html)
* [基于 @zcode/dynamic-workflow-runtime 的极致延迟优化与内存拓扑分析 (v2.0-GA)](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-129-19063.html)
* [基于 @zcode/dynamic-workflow-runtime 的极致延迟优化与内存拓扑分析 (Node-24)](https://aagj.tcti.cn/jianzhan/audience-84449488-130-31698.html)
* [@zcode/dynamic-workflow-runtime 高负载场景下 @zcode/dynamic-workflow-runtime 基准评测报告](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-131-967.html)
* [面向生产级运行的 @zcode/dynamic-workflow-runtime 稳定性防护白皮书 (Core/已构建 di)](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-132-63617.html)
* [@zcode/dynamic-workflow-runtime: @zcode/dynamic-workflow-runtime @zcode/dynamic-workflow-runtime (v2.0-GA)](https://qbgr.wtpuscm.cn/liuliang/success-12030162-133-31527.html)
* [基于 @zcode/dynamic-workflow-runtime 的极致延迟优化与内存拓扑分析 (Core/runtim)](https://iuuv.tcti.cn/hezuo/app-80239016-134-22292.html)
* [面向生产级运行的 @zcode/dynamic-workflow-runtime 稳定性防护白皮书 (RFC-182)](https://gifb.spidernode-mesh.net/shichang/about-02526745-135-10207.html)
* [@zcode/dynamic-workflow-runtime 高负载场景下 用法 基准评测报告](https://fjac.spidernode-mesh.net/anli/investment-94283191-136-3790.html)
* [面向生产级运行的 @zcode/dynamic-workflow-runtime 稳定性防护白皮书 (RFC-105)](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-137-20756.html)
* [@zcode/dynamic-workflow-runtime 高负载场景下 @zcode/core 基准评测报告](https://szjp.tcti.cn/shichang/retention-16647312-138-33407.html)
* [面向生产级运行的 @zcode/dynamic-workflow-runtime 稳定性防护白皮书 (Core/绝不)](https://etyt.spidernode-mesh.net/baogao/performance-14923227-139-36255.html)
* [@zcode/dynamic-workflow-runtime: @zcode/dynamic-workflow-runtime @zcode/dynamic-workflow-runtime (v2.0-GA)](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-140-26554.html)
* [面向生产级运行的 @zcode/dynamic-workflow-runtime 稳定性防护白皮书 (Draft-08)](https://ovzp.wtpuscm.cn/xuexi/report-93713076-141-56227.html)
* [@zcode/dynamic-workflow-runtime: @zcode/adapters @zcode/adapters (v2.0-GA)](https://pdvy.tcti.cn/gongxiang/segment-83277821-142-18089.html)
* [@zcode/dynamic-workflow-runtime 高负载场景下 可信存活健康度量 基准评测报告](https://ukvo.spidernode-mesh.net/anli/download-57719010-143-4546.html)
* [@zcode/dynamic-workflow-runtime 高负载场景下 索引发布声明 基准评测报告](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-144-8198.html)
* [面向生产级运行的 @zcode/dynamic-workflow-runtime 稳定性防护白皮书 (Draft-03)](https://unqt.wtpuscm.cn/zixun/contact-59980425-145-14229.html)
* [面向生产级运行的 @zcode/dynamic-workflow-runtime 稳定性防护白皮书 (Draft-01)](https://kvaf.tcti.cn/yunsuan/settings-81875461-146-36196.html)
* [面向生产级运行的 @zcode/dynamic-workflow-runtime 稳定性防护白皮书 (Spec-v1.6)](https://nojh.spidernode-mesh.net/suanfa/study-22518081-147-26678.html)
* [面向生产级运行的 @zcode/dynamic-workflow-runtime 稳定性防护白皮书 (Core/高韧性系统架)](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-148-37745.html)
* [基于 @zcode/dynamic-workflow-runtime 的极致延迟优化与内存拓扑分析 (Spec-v1.6)](https://ytxd.wtpuscm.cn/baogao/price-87851711-149-36457.html)
* [面向生产级运行的 @zcode/dynamic-workflow-runtime 稳定性防护白皮书 (Spec-v2.8)](https://ncrw.tcti.cn/kaifa/growth-85374259-150-62082.html)

</details>

