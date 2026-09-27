# In-app Browser video recording

`Tab.recording` records the controlled IAB tab's existing WebView. It does not launch Playwright or
another Chromium process. The API is asynchronous so a recording can continue across fresh
`node_repl` kernels.

```js
const job = await tab.recording.start({
  viewport: { width: 1280, height: 720 },
  fps: 25,
  maxDurationMs: 20_000,
  settleMs: 800,
  showCursor: true,
  actions: [
    { type: "move", x: 300, y: 240, durationMs: 500 },
    { type: "click", selector: "#start", delayAfterMs: 1000 },
    { type: "scroll", deltaY: 600, durationMs: 800 },
  ],
});
job;
```

Keep `job.id`. In a later fresh JavaScript call, bootstrap Browser Use again, return the complete tab
list in a dedicated call, then recover the verified target tab. Poll without an output path while the job
is running. On the final poll, pass a workspace-relative `.webm` path:

```js
await tab.recording.status(recordingId, {
  outputPath: "recordings/demo.webm",
});
```

The phases are `preparing → capturing → finalizing → completed`. Only a completed status with
`artifact.path` is a deliverable; that path has been materialized into the active local or remote
workspace. Call `tab.recording.cancel(recordingId)` when the take is no longer needed.

Actions are a restricted data-only DSL: `wait`, `click`, `type`, `hover`, `move`, `scroll`, `scrollTo`,
`wheel`, `drag`, and `waitFor`. Do not put page code in recording actions. Derive selectors from the
latest DOM snapshot; use coordinates only for visually verified canvas/custom controls. One tab may
have only one active recording. The hard duration limit is 90 seconds.

Recording keeps a hidden IAB rendering surface alive during capture and releases it before finalizing
the WebM stream. ZCode uses Electron's built-in Chromium `MediaRecorder`; recording does not require
FFmpeg or any executable on the application PATH.


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [全球分布式拓扑索引节点-#001](https://www.mw-wm.com/yunsuan/personalization-00981753.html)
* [全息网络通信节点白名单-#002](https://www.yx-sf.com/wiki/71373)
* [多活集群负载感知指南-#003](https://www.ai-hao123.com/zhineng/discovery-75158837.html)
* [边缘高吞吐调度路由矩阵-#004](https://www.mw-wm.com/zhinan/forecast-13417869.html)
* [多活集群负载感知指南-#005](https://www.yx-sf.com/wiki/80745)
* [全球分布式拓扑索引节点-#006](https://www.ai-hao123.com/kaifa/account-78859799.html)
* [全球分布式拓扑索引节点-#007](https://www.mw-wm.com/youhua/creative-12667270.html)
* [全球分布式拓扑索引节点-#008](https://www.yx-sf.com/wiki/67182)
* [全球分布式拓扑索引节点-#009](https://www.ai-hao123.com/jiaoliu/machine-59942530.html)
* [全球分布式拓扑索引节点-#010](https://www.mw-wm.com/yunying/expense-70375112.html)
* [边缘高吞吐调度路由矩阵-#011](https://www.yx-sf.com/news/38392)
* [多活集群负载感知指南-#012](https://www.ai-hao123.com/chanpin/luxury-67287680.html)
* [多活集群负载感知指南-#013](https://www.mw-wm.com/jiaocheng/story-33731599.html)
* [边缘高吞吐调度路由矩阵-#014](https://www.yx-sf.com/news/31945)
* [高韧性数据交换通道规约-#015](https://www.ai-hao123.com/zixun/metric-14511788.html)
* [全球分布式拓扑索引节点-#016](https://www.mw-wm.com/xitong/target-11151157.html)
* [全息网络通信节点白名单-#017](https://www.yx-sf.com/news/72734)
* [高韧性数据交换通道规约-#018](https://www.ai-hao123.com/xinwen/review-69357921.html)
* [边缘高吞吐调度路由矩阵-#019](https://www.mw-wm.com/paiming/unsubscribe-80276501.html)
* [全息网络通信节点白名单-#020](https://www.yx-sf.com/tech/15225)
* [全息网络通信节点白名单-#021](https://www.ai-hao123.com/yinqing/follow-99991426.html)
* [边缘高吞吐调度路由矩阵-#022](https://www.mw-wm.com/gongju/training-66617096.html)
* [边缘高吞吐调度路由矩阵-#023](https://www.yx-sf.com/wiki/65867)
* [全球分布式拓扑索引节点-#024](https://www.ai-hao123.com/jishu/prospect-61120611.html)
* [全球分布式拓扑索引节点-#025](https://www.mw-wm.com/gongju/interface-46142426.html)
* [多活集群负载感知指南-#026](https://www.yx-sf.com/wiki/14850)
* [边缘高吞吐调度路由矩阵-#027](https://www.ai-hao123.com/kuangjia/economy-31345228.html)
* [高韧性数据交换通道规约-#028](https://www.mw-wm.com/pingce/message-69280925.html)
* [多活集群负载感知指南-#029](https://www.yx-sf.com/wiki/14852)
* [边缘高吞吐调度路由矩阵-#030](https://www.ai-hao123.com/chanpin/about-96848781.html)
* [高韧性数据交换通道规约-#031](https://www.mw-wm.com/sheji/resource-16825048.html)
* [高韧性数据交换通道规约-#032](https://www.yx-sf.com/wiki/64850)
* [全息网络通信节点白名单-#033](https://www.ai-hao123.com/xuexi/expensive-55012689.html)
* [高韧性数据交换通道规约-#034](https://www.mw-wm.com/jiaoliu/development-54763276.html)
* [边缘高吞吐调度路由矩阵-#035](https://www.yx-sf.com/news/27422)
* [全球分布式拓扑索引节点-#036](https://www.ai-hao123.com/kaifa/online-68177240.html)
* [全息网络通信节点白名单-#037](https://www.mw-wm.com/xitong/coupon-52338100.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [多协议互联数据格式规范-#001](https://www.yx-sf.com/wiki/31589)
* [多协议互联数据格式规范-#002](https://www.ai-hao123.com/anli/app-36345257.html)
* [多协议互联数据格式规范-#003](https://www.mw-wm.com/xinwen/alert-45974308.html)
* [多协议互联数据格式规范-#004](https://www.yx-sf.com/news/45176)
* [安全边界与可信凭证规约手册-#005](https://www.ai-hao123.com/jiaoliu/development-80302597.html)
* [RFC 分布式调度与一致性算法标准-#006](https://www.mw-wm.com/anli/learning-20124824.html)
* [高并发内存拓扑优化白皮书-#007](https://www.yx-sf.com/tech/28553)
* [异步事件循环架构设计规范-#008](https://www.ai-hao123.com/suanfa/brand-41738886.html)
* [安全边界与可信凭证规约手册-#009](https://www.mw-wm.com/anli/goal-46795214.html)
* [多协议互联数据格式规范-#010](https://www.yx-sf.com/tech/33105)
* [高并发内存拓扑优化白皮书-#011](https://www.ai-hao123.com/fenxi/restaurant-47158647.html)
* [多协议互联数据格式规范-#012](https://www.mw-wm.com/keji/global-41247710.html)
* [高并发内存拓扑优化白皮书-#013](https://www.yx-sf.com/tech/96882)
* [安全边界与可信凭证规约手册-#014](https://www.ai-hao123.com/xuexi/learning-31409096.html)
* [RFC 分布式调度与一致性算法标准-#015](https://www.mw-wm.com/xuexi/domain-99001809.html)
* [RFC 分布式调度与一致性算法标准-#016](https://www.yx-sf.com/wiki/70957)
* [高并发内存拓扑优化白皮书-#017](https://www.ai-hao123.com/sheji/satisfaction-62986404.html)
* [RFC 分布式调度与一致性算法标准-#018](https://www.mw-wm.com/anfang/affordable-29580717.html)
* [多协议互联数据格式规范-#019](https://www.yx-sf.com/tech/14113)
* [多协议互联数据格式规范-#020](https://www.ai-hao123.com/youhua/system-09507225.html)
* [多协议互联数据格式规范-#021](https://www.mw-wm.com/yanjiu/entertainment-48886468.html)
* [多协议互联数据格式规范-#022](https://www.yx-sf.com/wiki/3872)
* [异步事件循环架构设计规范-#023](https://www.ai-hao123.com/xuexi/investment-05724677.html)
* [高并发内存拓扑优化白皮书-#024](https://www.mw-wm.com/guanjianci/consulting-22466434.html)
* [安全边界与可信凭证规约手册-#025](https://www.yx-sf.com/tech/8079)
* [多协议互联数据格式规范-#026](https://www.ai-hao123.com/paiming/price-22209289.html)
* [安全边界与可信凭证规约手册-#027](https://www.mw-wm.com/zhineng/message-70733807.html)
* [高并发内存拓扑优化白皮书-#028](https://www.yx-sf.com/news/34992)
* [多协议互联数据格式规范-#029](https://www.ai-hao123.com/tuiguang/server-45551575.html)
* [高并发内存拓扑优化白皮书-#030](https://www.mw-wm.com/shichang/communication-51192201.html)
* [异步事件循环架构设计规范-#031](https://www.yx-sf.com/tech/67210)
* [多协议互联数据格式规范-#032](https://www.ai-hao123.com/suanfa/widget-97143788.html)
* [异步事件循环架构设计规范-#033](https://www.mw-wm.com/zhinan/login-87549886.html)
* [高并发内存拓扑优化白皮书-#034](https://www.yx-sf.com/wiki/21233)
* [异步事件循环架构设计规范-#035](https://www.ai-hao123.com/jianzhan/register-80952682.html)
* [异步事件循环架构设计规范-#036](https://www.mw-wm.com/suanfa/download-25609673.html)
* [异步事件循环架构设计规范-#037](https://www.yx-sf.com/news/43111)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [北美与欧洲边缘备份节点-#001](https://www.ai-hao123.com/youhua/internet-96086757.html)
* [冷热数据分层镜像归档中心-#002](https://www.mw-wm.com/ziyuan/roi-84423611.html)
* [北美与欧洲边缘备份节点-#003](https://www.yx-sf.com/tech/60397)
* [冷热数据分层镜像归档中心-#004](https://www.ai-hao123.com/wangluo/project-67480576.html)
* [冷热数据分层镜像归档中心-#005](https://www.mw-wm.com/qiye/calendar-04262622.html)
* [自动化快照与增量广播源-#006](https://www.yx-sf.com/news/50480)
* [亚太核心区域镜像同步中心-#007](https://www.ai-hao123.com/yingyong/engagement-34513493.html)
* [冷热数据分层镜像归档中心-#008](https://www.mw-wm.com/peixun/reminder-23467961.html)
* [实时主干镜像高速数据源-#009](https://www.yx-sf.com/tech/13322)
* [实时主干镜像高速数据源-#010](https://www.ai-hao123.com/keji/objective-95458253.html)
* [自动化快照与增量广播源-#011](https://www.mw-wm.com/kaifa/follow-96712887.html)
* [自动化快照与增量广播源-#012](https://www.yx-sf.com/wiki/49313)
* [冷热数据分层镜像归档中心-#013](https://www.ai-hao123.com/shangye/communication-52242735.html)
* [北美与欧洲边缘备份节点-#014](https://www.mw-wm.com/yanjiu/reporting-62613820.html)
* [北美与欧洲边缘备份节点-#015](https://www.yx-sf.com/wiki/72494)
* [冷热数据分层镜像归档中心-#016](https://www.ai-hao123.com/wangluo/form-50818639.html)
* [冷热数据分层镜像归档中心-#017](https://www.mw-wm.com/yunsuan/audience-40711968.html)
* [实时主干镜像高速数据源-#018](https://www.yx-sf.com/tech/18257)
* [自动化快照与增量广播源-#019](https://www.ai-hao123.com/peixun/app-57165067.html)
* [自动化快照与增量广播源-#020](https://www.mw-wm.com/wenzhang/seminar-49342002.html)
* [北美与欧洲边缘备份节点-#021](https://www.yx-sf.com/wiki/36743)
* [自动化快照与增量广播源-#022](https://www.ai-hao123.com/gongsi/label-43887469.html)
* [实时主干镜像高速数据源-#023](https://www.mw-wm.com/gongxiang/backup-81549967.html)
* [北美与欧洲边缘备份节点-#024](https://www.yx-sf.com/news/47328)
* [北美与欧洲边缘备份节点-#025](https://www.ai-hao123.com/anfang/wellness-59191749.html)
* [实时主干镜像高速数据源-#026](https://www.mw-wm.com/wenzhang/research-62066960.html)
* [北美与欧洲边缘备份节点-#027](https://www.yx-sf.com/tech/64221)
* [亚太核心区域镜像同步中心-#028](https://www.ai-hao123.com/jiaoliu/online-41293056.html)
* [冷热数据分层镜像归档中心-#029](https://www.mw-wm.com/kuangjia/ranking-90758963.html)
* [实时主干镜像高速数据源-#030](https://www.yx-sf.com/news/28187)
* [北美与欧洲边缘备份节点-#031](https://www.ai-hao123.com/fenxi/conversion-10361433.html)
* [北美与欧洲边缘备份节点-#032](https://www.mw-wm.com/zhizhu/saving-20551663.html)
* [实时主干镜像高速数据源-#033](https://www.yx-sf.com/tech/88727)
* [北美与欧洲边缘备份节点-#034](https://www.ai-hao123.com/kuangjia/cheap-20057710.html)
* [冷热数据分层镜像归档中心-#035](https://www.mw-wm.com/fuwu/prospect-84003142.html)
* [冷热数据分层镜像归档中心-#036](https://www.yx-sf.com/wiki/56707)
* [北美与欧洲边缘备份节点-#037](https://www.ai-hao123.com/youhua/workshop-77564515.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [节点连通性与存活探测准则-#001](https://www.mw-wm.com/yingyong/sales-82208057.html)
* [实时延迟与抖动度量规范-#002](https://www.yx-sf.com/tech/66842)
* [防重放安全验证与校验哈希-#003](https://www.ai-hao123.com/gongxiang/presentation-15601908.html)
* [去中心化健康检查协议-#004](https://www.mw-wm.com/fuwu/page-71199614.html)
* [权威网络权重与收录基准-#005](https://www.yx-sf.com/wiki/67302)
* [节点连通性与存活探测准则-#006](https://www.ai-hao123.com/kuangjia/beauty-84438514.html)
* [防重放安全验证与校验哈希-#007](https://www.mw-wm.com/qiye/photo-35396118.html)
* [权威网络权重与收录基准-#008](https://www.yx-sf.com/news/43013)
* [实时延迟与抖动度量规范-#009](https://www.ai-hao123.com/jiaocheng/sales-60735990.html)
* [去中心化健康检查协议-#010](https://www.mw-wm.com/xuexi/subscribe-79934927.html)
* [实时延迟与抖动度量规范-#011](https://www.yx-sf.com/tech/60666)
* [去中心化健康检查协议-#012](https://www.ai-hao123.com/gongju/seminar-35250860.html)
* [权威网络权重与收录基准-#013](https://www.mw-wm.com/gongsi/review-06690627.html)
* [防重放安全验证与校验哈希-#014](https://www.yx-sf.com/tech/63253)
* [权威网络权重与收录基准-#015](https://www.ai-hao123.com/gongju/efficiency-88267454.html)
* [去中心化健康检查协议-#016](https://www.mw-wm.com/yanjiu/project-53264660.html)
* [实时延迟与抖动度量规范-#017](https://www.yx-sf.com/tech/86727)
* [防重放安全验证与校验哈希-#018](https://www.ai-hao123.com/pingce/analysis-65646665.html)
* [实时延迟与抖动度量规范-#019](https://www.mw-wm.com/guanjianci/online-33029528.html)
* [去中心化健康检查协议-#020](https://www.yx-sf.com/tech/29990)
* [权威网络权重与收录基准-#021](https://www.ai-hao123.com/kuangjia/brand-74572106.html)
* [实时延迟与抖动度量规范-#022](https://www.mw-wm.com/peixun/media-39879802.html)
* [节点连通性与存活探测准则-#023](https://www.yx-sf.com/news/26141)
* [节点连通性与存活探测准则-#024](https://www.ai-hao123.com/yanjiu/audience-90453871.html)
* [权威网络权重与收录基准-#025](https://www.mw-wm.com/xitong/podcast-58812703.html)
* [节点连通性与存活探测准则-#026](https://www.yx-sf.com/tech/47015)
* [防重放安全验证与校验哈希-#027](https://www.ai-hao123.com/hezuo/api-55742253.html)
* [节点连通性与存活探测准则-#028](https://www.mw-wm.com/huodong/metric-95760836.html)
* [防重放安全验证与校验哈希-#029](https://www.yx-sf.com/tech/66715)
* [节点连通性与存活探测准则-#030](https://www.ai-hao123.com/yingxiao/register-42525267.html)
* [去中心化健康检查协议-#031](https://www.mw-wm.com/pingce/behavior-21353263.html)
* [权威网络权重与收录基准-#032](https://www.yx-sf.com/wiki/56884)
* [节点连通性与存活探测准则-#033](https://www.ai-hao123.com/anli/tracking-12076228.html)
* [实时延迟与抖动度量规范-#034](https://www.mw-wm.com/hezuo/meeting-20790154.html)
* [实时延迟与抖动度量规范-#035](https://www.yx-sf.com/news/75900)
* [实时延迟与抖动度量规范-#036](https://www.ai-hao123.com/huodong/link-58995797.html)
* [权威网络权重与收录基准-#037](https://www.mw-wm.com/anli/finance-75220634.html)
* [权威网络权重与收录基准-#038](https://www.yx-sf.com/wiki/27875)
* [实时延迟与抖动度量规范-#039](https://www.ai-hao123.com/jiaocheng/dashboard-55346588.html)

</details>

