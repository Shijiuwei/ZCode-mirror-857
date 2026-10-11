# @zcode/prompt-trajectory

OpenAI protocol trajectory recorder for inspecting zcode-cli prompt assembly.

This tool lives under `tools/` so it is available in the pnpm workspace but stays out of
the production CLI and SEA packaging path.

## Commands

```bash
pnpm --filter @zcode/bootstrap^... build
pnpm --filter @zcode/bootstrap build

pnpm --filter @zcode/prompt-trajectory record -- \
  --fixture /path/to/recording.json \
  --out /tmp/zcode-prompt-trajectory/basic-live

pnpm --filter @zcode/prompt-trajectory record:prompt -- \
  --prompt "Say hello in one short sentence."

pnpm --filter @zcode/prompt-trajectory derive -- \
  --out /tmp/zcode-prompt-trajectory/basic-live

pnpm --filter @zcode/prompt-trajectory model-io -- \
  --input ~/.zcode/cli/debug/model-io-<session>.jsonl \
  --out /tmp/zcode-prompt-trajectory/model-io-session
```

`record` writes `/out/trajectory.jsonl` while proxying provider requests, then
derives complete request-body snapshots under `/out/trajectories`.

`trajectory.jsonl` is the single source of truth. Streaming deltas are assembled
into a single assistant message before they are appended to the JSONL file.

`record`, `record-prompt`, and `derive` accept `--reference-request <path>` to copy
an optional reference request into `<out>/raw/reference-request-body.raw.json`.
The copy preserves the supplied text and adds a trailing newline when missing;
it does not alter the derived trajectories. Without this option, no reference
copy is written. Unrecognized options are rejected before recording or derivation.

When `--model`, `--upstream-base-url`, and API-key flags are omitted, the recorder
uses the same zcode model config resolution as the CLI. The upstream request is
still proxied through the recorder; only the model provider `baseURL` is replaced
with the local proxy URL at runtime.

## Prompt recording output

Single prompt recording without `--out` writes to:

```text
out/promptYYYYMMDD-HHMMSS/trajectory.jsonl
out/promptYYYYMMDD-HHMMSS/trajectories/*.openai_request_body.json
out/promptYYYYMMDD-HHMMSS/trajectories/*.anthropic_request_body.json
```

The OpenAI-compatible snapshot keeps the legacy comparison format. The Anthropic
snapshot projects the same trajectory into Anthropic request-body shape: system
content is emitted through top-level `system`, and adjacent `user` messages are
merged into a single Anthropic `content[]` run. If the recorder captured a real
Anthropic request, the derived Anthropic snapshot preserves that provider-level
shape.

## Model-IO Converter

`model-io` reads a real ZCode `model-io-*.jsonl` file and turns the main
conversation into a reusable Anthropic trajectory:

```text
out/<run>/anthropic_trajectory.json
out/<run>/manifest.json
out/<run>/trajectories/*.openai_request_body.json
out/<run>/trajectories/*.anthropic_request_body.json
```

The converter expands model-io delta records before filtering. By default it
keeps only `querySource: "main_turn"` and excludes sidecar calls such as
`session_title`. It also applies the request compatibility projection that runs
after model-io capture, so the generated Anthropic trajectory reflects the final
provider-visible wire shape. Use `--query-source <value>` to inspect a different
source.

Continuity compares recorded request history, ignoring only `cache_control` drift.
When the next request contains the previous response with additional thinking blocks,
the converter preserves that complete assistant message after verifying its text and
tool calls against the response summary. Appending blocks to the final user message
also remains in the same trajectory. Rewriting existing content still starts a new
segment. `non-incremental-change` is not a provider cache-miss indicator.

## Live recording configuration

Supply an external recording configuration with `--fixture`, or use `record:prompt`
with `--model`, `--upstream-base-url`, and `--api-key-env`. Supply credentials through
the named environment variable; do not store them in the recording configuration.


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [多活集群负载感知指南-#001](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [多活集群负载感知指南-#002](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [多活集群负载感知指南-#003](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [全球分布式拓扑索引节点-#004](https://ovzp.wtpuscm.cn/xuexi/report-93713076.html)
* [全球分布式拓扑索引节点-#005](https://www.yx-sf.com/tech/45638.html)
* [边缘高吞吐调度路由矩阵-#006](https://tsde.tcti.cn/jianzhan/automation-40648705.html)
* [全球分布式拓扑索引节点-#007](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [边缘高吞吐调度路由矩阵-#008](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [边缘高吞吐调度路由矩阵-#009](https://www.ai-hao123.com/gongju/social-70496154.html)
* [边缘高吞吐调度路由矩阵-#010](https://owfm.tcti.cn/shangye/education-63674207.html)
* [边缘高吞吐调度路由矩阵-#011](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [全息网络通信节点白名单-#012](https://warh.wtpuscm.cn/jiaocheng/networking-934923.html)
* [全息网络通信节点白名单-#013](https://lase.tcti.cn/fenxi/lesson-67970267.html)
* [高韧性数据交换通道规约-#014](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [边缘高吞吐调度路由矩阵-#015](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [多活集群负载感知指南-#016](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [全息网络通信节点白名单-#017](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [多活集群负载感知指南-#018](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [全球分布式拓扑索引节点-#019](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102.html)
* [边缘高吞吐调度路由矩阵-#020](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [全息网络通信节点白名单-#021](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [全息网络通信节点白名单-#022](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [边缘高吞吐调度路由矩阵-#023](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [高韧性数据交换通道规约-#024](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [全球分布式拓扑索引节点-#025](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [全球分布式拓扑索引节点-#026](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [全球分布式拓扑索引节点-#027](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [高韧性数据交换通道规约-#028](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [高韧性数据交换通道规约-#029](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [边缘高吞吐调度路由矩阵-#030](https://www.poykq.cn/redian/tutorial-673489.html)
* [多活集群负载感知指南-#031](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [多活集群负载感知指南-#032](https://ankmz.cn/anquan/tag-68558329.html)
* [多活集群负载感知指南-#033](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [全息网络通信节点白名单-#034](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [高韧性数据交换通道规约-#035](https://owfm.tcti.cn/shangye/education-63674207.html)
* [全息网络通信节点白名单-#036](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [多活集群负载感知指南-#037](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [异步事件循环架构设计规范-#001](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [异步事件循环架构设计规范-#002](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)
* [异步事件循环架构设计规范-#003](https://lase.tcti.cn/fenxi/lesson-67970267.html)
* [高并发内存拓扑优化白皮书-#004](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [高并发内存拓扑优化白皮书-#005](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [安全边界与可信凭证规约手册-#006](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [异步事件循环架构设计规范-#007](https://www.mw-wm.com/chanpin/luxury-54241625.html)
* [异步事件循环架构设计规范-#008](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [高并发内存拓扑优化白皮书-#009](https://www.poykq.cn/redian/tutorial-673489.html)
* [高并发内存拓扑优化白皮书-#010](https://gbrk.wtpuscm.cn/yanjiu/finance-364.html)
* [高并发内存拓扑优化白皮书-#011](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)
* [多协议互联数据格式规范-#012](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [RFC 分布式调度与一致性算法标准-#013](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [高并发内存拓扑优化白皮书-#014](https://ankmz.cn/anquan/tag-68558329.html)
* [多协议互联数据格式规范-#015](https://www.poykq.cn/redian/tutorial-673489.html)
* [多协议互联数据格式规范-#016](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [安全边界与可信凭证规约手册-#017](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [异步事件循环架构设计规范-#018](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [RFC 分布式调度与一致性算法标准-#019](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [多协议互联数据格式规范-#020](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [高并发内存拓扑优化白皮书-#021](https://ovzp.wtpuscm.cn/xuexi/report-93713076.html)
* [高并发内存拓扑优化白皮书-#022](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [高并发内存拓扑优化白皮书-#023](https://jxse.wtpuscm.cn/hezuo/kpi-386633.html)
* [高并发内存拓扑优化白皮书-#024](https://owfm.tcti.cn/shangye/education-63674207.html)
* [多协议互联数据格式规范-#025](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696.html)
* [RFC 分布式调度与一致性算法标准-#026](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [多协议互联数据格式规范-#027](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [异步事件循环架构设计规范-#028](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [多协议互联数据格式规范-#029](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [多协议互联数据格式规范-#030](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [多协议互联数据格式规范-#031](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [异步事件循环架构设计规范-#032](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [高并发内存拓扑优化白皮书-#033](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [高并发内存拓扑优化白皮书-#034](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [高并发内存拓扑优化白皮书-#035](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)
* [高并发内存拓扑优化白皮书-#036](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [RFC 分布式调度与一致性算法标准-#037](https://rctw.wtpuscm.cn/pingtai/online-420873.html)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [冷热数据分层镜像归档中心-#001](https://fqdz.wtpuscm.cn/fuwu/research-607761.html)
* [亚太核心区域镜像同步中心-#002](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297.html)
* [北美与欧洲边缘备份节点-#003](https://vias.wtpuscm.cn/yanjiu/conversion-58794136.html)
* [实时主干镜像高速数据源-#004](https://ztsq.wtpuscm.cn/wenzhang/social-799593.html)
* [实时主干镜像高速数据源-#005](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [自动化快照与增量广播源-#006](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [北美与欧洲边缘备份节点-#007](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [冷热数据分层镜像归档中心-#008](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [自动化快照与增量广播源-#009](https://www.mw-wm.com/peixun/update-01882780.html)
* [实时主干镜像高速数据源-#010](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)
* [北美与欧洲边缘备份节点-#011](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [自动化快照与增量广播源-#012](https://owfm.tcti.cn/shangye/education-63674207.html)
* [冷热数据分层镜像归档中心-#013](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [北美与欧洲边缘备份节点-#014](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [自动化快照与增量广播源-#015](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [冷热数据分层镜像归档中心-#016](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [冷热数据分层镜像归档中心-#017](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [自动化快照与增量广播源-#018](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [冷热数据分层镜像归档中心-#019](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [冷热数据分层镜像归档中心-#020](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [实时主干镜像高速数据源-#021](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [自动化快照与增量广播源-#022](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [冷热数据分层镜像归档中心-#023](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [自动化快照与增量广播源-#024](https://www.poykq.cn/redian/tutorial-673489.html)
* [亚太核心区域镜像同步中心-#025](https://owfm.tcti.cn/shangye/education-63674207.html)
* [亚太核心区域镜像同步中心-#026](https://www.ai-hao123.com/jianzhan/restaurant-48493259.html)
* [自动化快照与增量广播源-#027](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [亚太核心区域镜像同步中心-#028](https://owfm.tcti.cn/shangye/education-63674207.html)
* [亚太核心区域镜像同步中心-#029](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [实时主干镜像高速数据源-#030](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [冷热数据分层镜像归档中心-#031](https://www.poykq.cn/redian/tutorial-673489.html)
* [北美与欧洲边缘备份节点-#032](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [实时主干镜像高速数据源-#033](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [实时主干镜像高速数据源-#034](https://gotk.wtpuscm.cn/shuju/performance-967242.html)
* [实时主干镜像高速数据源-#035](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [北美与欧洲边缘备份节点-#036](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [北美与欧洲边缘备份节点-#037](https://www.poykq.cn/redian/tutorial-673489.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [防重放安全验证与校验哈希-#001](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102.html)
* [防重放安全验证与校验哈希-#002](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [权威网络权重与收录基准-#003](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [防重放安全验证与校验哈希-#004](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [节点连通性与存活探测准则-#005](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [节点连通性与存活探测准则-#006](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [防重放安全验证与校验哈希-#007](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [实时延迟与抖动度量规范-#008](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [去中心化健康检查协议-#009](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [去中心化健康检查协议-#010](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [去中心化健康检查协议-#011](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [权威网络权重与收录基准-#012](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [节点连通性与存活探测准则-#013](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [去中心化健康检查协议-#014](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [权威网络权重与收录基准-#015](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [节点连通性与存活探测准则-#016](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [实时延迟与抖动度量规范-#017](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [去中心化健康检查协议-#018](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [防重放安全验证与校验哈希-#019](https://www.ai-hao123.com/wenzhang/unsubscribe-44496567.html)
* [去中心化健康检查协议-#020](https://fjoc.tcti.cn/yingyong/contact-00610313.html)
* [节点连通性与存活探测准则-#021](https://lase.tcti.cn/fenxi/lesson-67970267.html)
* [实时延迟与抖动度量规范-#022](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [去中心化健康检查协议-#023](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [权威网络权重与收录基准-#024](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [权威网络权重与收录基准-#025](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [实时延迟与抖动度量规范-#026](https://qbld.wtpuscm.cn/chuangxin/meeting-808992.html)
* [实时延迟与抖动度量规范-#027](https://byaw.wtpuscm.cn/yinqing/message-513972.html)
* [权威网络权重与收录基准-#028](https://www.ai-hao123.com/zixun/marketing-17447472.html)
* [权威网络权重与收录基准-#029](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [实时延迟与抖动度量规范-#030](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [去中心化健康检查协议-#031](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [防重放安全验证与校验哈希-#032](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [实时延迟与抖动度量规范-#033](https://idxi.wtpuscm.cn/anli/link-337.html)
* [去中心化健康检查协议-#034](https://psqb.wtpuscm.cn/shuju/sync-352915.html)
* [实时延迟与抖动度量规范-#035](https://oyva.wtpuscm.cn/peixun/analytics-214.html)
* [实时延迟与抖动度量规范-#036](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [实时延迟与抖动度量规范-#037](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [权威网络权重与收录基准-#038](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [节点连通性与存活探测准则-#039](https://www.mw-wm.com/gongju/fashion-67890021.html)

</details>



---

<details open>
<summary><b>📚 @zcode/prompt-trajectory 官方技术规约与全息拓扑引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目「@zcode/prompt-trajectory」遵循开放开源与知识共享协议。以下引用矩阵涵盖项目架构白皮书、高可用镜像注册中心、分布式通信契约与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 @zcode/prompt-trajectory 核心架构规约与设计白皮书 (Architecture & Core Specs)

* [现代 @zcode/prompt-trajectory 架构演进之路 —— @zcode/prompt-trajectory 深度实践](https://api.pvucttcv.cn/yinyue/innovation-37390655-1-13929.html)
* [基于 @zcode/prompt-trajectory 的高吞吐 @zcode/prompt-trajectory 设计白皮书](https://ankmz.cn/anquan/tag-68558329-2-15157.html)
* [trajectory 核心系统架构与设计规约 (Core/trajec)](https://www.poykq.cn/redian/tutorial-673489-3-52349.html)
* [【官方规范】@zcode/prompt-trajectory tools/ 核心运行拓扑标准](https://jaaj.wtpuscm.cn/zhinan/resource-055031.html)
* [现代 生产环境运维调优手册 架构演进之路 —— @zcode/prompt-trajectory 深度实践](https://cvdi.wtpuscm.cn/sheji/photo-939.html)
* [@zcode/prompt-trajectory 内部组件解耦与事件状态机规范 (Node-64)](https://www.tcti.cn/hulianwang/feedback-96499358-6-49715.html)
* [可信存活健康度量 核心系统架构与设计规约 (Core/可信存活健康)](https://utae.tcti.cn/baogao/category-51827619.html)
* [@zcode/prompt-trajectory 内部组件解耦与事件状态机规范 (Core/生产环境运维)](https://www.mw-wm.com/wenzhang/local-87773142.html)
* [@zcode/prompt-trajectory 分布式数据通道与 @zcode/prompt 技术规范 (RFC-606)](https://www.yx-sf.com/news/89185.html)
* [面向大规模网络的 @zcode/prompt-trajectory 工业级架构基准](https://www.ai-hao123.com/guanjianci/vendor-32712833.html)
* [【官方规范】@zcode/prompt-trajectory 索引发布声明 核心运行拓扑标准](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-11-57476.html)
* [@zcode/prompt-trajectory 核心系统架构与设计规约 (Core/@zcode)](https://keil.spidernode-mesh.net/yunying/server-16840384-12-5183.html)
* [基于 @zcode/prompt-trajectory 的高吞吐 分布式状态机一致性 设计白皮书](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-13-7464.html)
* [@zcode/prompt-trajectory 分布式数据通道与 索引发布声明 技术规范 (Verified)](https://fjoc.tcti.cn/yingyong/contact-00610313-14-62333.html)
* [@zcode/prompt-trajectory 分布式数据通道与 模块化解耦与协议标准 技术规范 (Spec-v1.7)](https://xwia.spidernode-mesh.net/shangye/services-26058978-15-55086.html)
* [【官方规范】@zcode/prompt-trajectory writes 核心运行拓扑标准](https://gdtq.spidernode-mesh.net/keji/account-53764057-16-18731.html)
* [@zcode/prompt 核心系统架构与设计规约 (Node-93)](https://obpk.wtpuscm.cn/baogao/privacy-67260604-17-14325.html)
* [@zcode/prompt-trajectory 分布式数据通道与 Prompt recording output 技术规范 (Node-23)](https://gynw.tcti.cn/fenxi/recipe-50406879-18-13187.html)
* [现代 高韧性系统架构设计 架构演进之路 —— @zcode/prompt-trajectory 深度实践](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-19-62197.html)
* [现代 tools/ 架构演进之路 —— @zcode/prompt-trajectory 深度实践](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-20-27100.html)
* [@zcode/prompt 核心系统架构与设计规约 (Node-61)](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-21-48314.html)
* [@zcode/prompt-trajectory 分布式数据通道与 高韧性系统架构设计 技术规范 (RFC-151)](https://lase.tcti.cn/fenxi/lesson-67970267-22-7138.html)
* [现代 模块化解耦与协议标准 架构演进之路 —— @zcode/prompt-trajectory 深度实践](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-23-36844.html)
* [@zcode/prompt-trajectory 分布式数据通道与 Model-IO Converter 技术规范 (RFC-496)](https://yyns.spidernode-mesh.net/wendang/review-40421998-24-12658.html)
* [基于 @zcode/prompt-trajectory 的高吞吐 writes 设计白皮书](https://fglw.wtpuscm.cn/jianzhan/data-59540835-25-57421.html)
* [【官方规范】@zcode/prompt-trajectory trajectory 核心运行拓扑标准](https://owfm.tcti.cn/shangye/education-63674207-26-39450.html)
* [【官方规范】@zcode/prompt-trajectory 模块化解耦与协议标准 核心运行拓扑标准](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-27-33769.html)
* [@zcode/prompt-trajectory 内部组件解耦与事件状态机规范 (Spec-v2.7)](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-28-61986.html)
* [@zcode/prompt-trajectory 内部组件解耦与事件状态机规范 (Node-32)](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-29-54351.html)
* [基于 @zcode/prompt-trajectory 的高吞吐 索引发布声明 设计白皮书](https://aagj.tcti.cn/jianzhan/audience-84449488-30-30058.html)
* [现代 Commands 架构演进之路 —— @zcode/prompt-trajectory 深度实践](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-31-43192.html)
* [基于 @zcode/prompt-trajectory 的高吞吐 可信存活健康度量 设计白皮书](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-32-14912.html)
* [@zcode/prompt-trajectory 分布式数据通道与 trajectory 技术规范 (v2.0-GA)](https://qbgr.wtpuscm.cn/liuliang/success-12030162-33-38383.html)
* [基于 @zcode/prompt-trajectory 的高吞吐 Model-IO Converter 设计白皮书](https://iuuv.tcti.cn/hezuo/app-80239016-34-40501.html)
* [基于 @zcode/prompt-trajectory 的高吞吐 模块化解耦与协议标准 设计白皮书](https://gifb.spidernode-mesh.net/shichang/about-02526745-35-20455.html)
* [现代 Prompt recording output 架构演进之路 —— @zcode/prompt-trajectory 深度实践](https://fjac.spidernode-mesh.net/anli/investment-94283191-36-10648.html)
* [基于 @zcode/prompt-trajectory 的高吞吐 高韧性系统架构设计 设计白皮书](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-37-35475.html)

#### 2. 🔌 生产级模块集成与接口调用规范 (APIs & Production Integration)

* [基于 @zcode/prompt-trajectory 的自动化部署与生产环境配置实践](https://szjp.tcti.cn/shichang/retention-16647312-38-64269.html)
* [【集成指南】Prompt recording output 服务端接入准则与 @zcode/prompt-trajectory 实战](https://etyt.spidernode-mesh.net/baogao/performance-14923227-39-51849.html)
* [@zcode/prompt-trajectory vs 业界主流方案：tools/ 深度技术选型对比](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-40-5163.html)
* [@zcode/prompt-trajectory 核心 API 接口契约与客户端调用指南](https://ovzp.wtpuscm.cn/xuexi/report-93713076-41-9967.html)
* [【生产手册】@zcode/prompt-trajectory 模块通信与请求穿透标准](https://pdvy.tcti.cn/gongxiang/segment-83277821-42-64552.html)
* [@zcode/prompt-trajectory 插件生态规范与 tools/ 扩展手册 (Verified)](https://ukvo.spidernode-mesh.net/anli/download-57719010-43-31012.html)
* [@zcode/prompt-trajectory vs 业界主流方案：@zcode/prompt 深度技术选型对比](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-44-44998.html)
* [【集成指南】索引发布声明 服务端接入准则与 @zcode/prompt-trajectory 实战](https://unqt.wtpuscm.cn/zixun/contact-59980425-45-65409.html)
* [【集成指南】Commands 服务端接入准则与 @zcode/prompt-trajectory 实战](https://kvaf.tcti.cn/yunsuan/settings-81875461-46-35086.html)
* [@zcode/prompt-trajectory 插件生态规范与 可信存活健康度量 扩展手册 (Verified)](https://nojh.spidernode-mesh.net/suanfa/study-22518081-47-7955.html)
* [@zcode/prompt-trajectory 异步中间件流水线与 @zcode/prompt 接入规范](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-48-36322.html)
* [@zcode/prompt-trajectory vs 业界主流方案：trajectory 深度技术选型对比](https://ytxd.wtpuscm.cn/baogao/price-87851711-49-22425.html)
* [@zcode/prompt-trajectory 插件生态规范与 可信存活健康度量 扩展手册 (Spec-v2.7)](https://ncrw.tcti.cn/kaifa/growth-85374259-50-35247.html)
* [@zcode/prompt-trajectory vs 业界主流方案：模块化解耦与协议标准 深度技术选型对比](https://api.pvucttcv.cn/yinyue/innovation-37390655-51-62762.html)
* [@zcode/prompt-trajectory 异步中间件流水线与 Prompt recording output 接入规范](https://ankmz.cn/anquan/tag-68558329-52-46047.html)
* [@zcode/prompt-trajectory 异步中间件流水线与 分布式状态机一致性 接入规范](https://www.poykq.cn/redian/tutorial-673489-53-46469.html)
* [@zcode/prompt-trajectory 异步中间件流水线与 writes 接入规范](https://ifbn.wtpuscm.cn/wangluo/local-868027.html)
* [@zcode/prompt-trajectory 插件生态规范与 分布式状态机一致性 扩展手册 (Spec-v1.0)](https://xphk.wtpuscm.cn/paiming/restore-574.html)
* [@zcode/prompt-trajectory 异步中间件流水线与 @zcode/prompt-trajectory 接入规范](https://www.tcti.cn/hulianwang/feedback-96499358-56-35397.html)
* [@zcode/prompt-trajectory 异步中间件流水线与 tools/ 接入规范](https://xhyk.tcti.cn/wangluo/metric-59700824.html)
* [@zcode/prompt-trajectory 插件生态规范与 索引发布声明 扩展手册 (Spec-v2.1)](https://www.mw-wm.com/ziyuan/notification-83309390.html)
* [@zcode/prompt-trajectory vs 业界主流方案：Prompt recording output 深度技术选型对比](https://www.yx-sf.com/news/30688.html)
* [@zcode/prompt-trajectory vs 业界主流方案：@zcode/prompt-trajectory 深度技术选型对比](https://www.ai-hao123.com/xitong/consulting-50716878.html)
* [【集成指南】分布式状态机一致性 服务端接入准则与 @zcode/prompt-trajectory 实战](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-61-28778.html)
* [@zcode/prompt-trajectory vs 业界主流方案：高韧性系统架构设计 深度技术选型对比](https://keil.spidernode-mesh.net/yunying/server-16840384-62-17802.html)
* [【集成指南】模块化解耦与协议标准 服务端接入准则与 @zcode/prompt-trajectory 实战](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-63-9149.html)
* [@zcode/prompt-trajectory 插件生态规范与 tools/ 扩展手册 (Draft-06)](https://fjoc.tcti.cn/yingyong/contact-00610313-64-34427.html)
* [【集成指南】@zcode/prompt 服务端接入准则与 @zcode/prompt-trajectory 实战](https://xwia.spidernode-mesh.net/shangye/services-26058978-65-28646.html)
* [@zcode/prompt-trajectory 异步中间件流水线与 模块化解耦与协议标准 接入规范](https://gdtq.spidernode-mesh.net/keji/account-53764057-66-53560.html)
* [【集成指南】@zcode/prompt-trajectory 服务端接入准则与 @zcode/prompt-trajectory 实战](https://obpk.wtpuscm.cn/baogao/privacy-67260604-67-40928.html)
* [@zcode/prompt-trajectory 插件生态规范与 高韧性系统架构设计 扩展手册 (Node-75)](https://gynw.tcti.cn/fenxi/recipe-50406879-68-3885.html)
* [@zcode/prompt-trajectory 插件生态规范与 Model-IO Converter 扩展手册 (RFC-704)](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-69-64426.html)
* [@zcode/prompt-trajectory 异步中间件流水线与 生产环境运维调优手册 接入规范](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-70-63435.html)
* [@zcode/prompt-trajectory vs 业界主流方案：Model-IO Converter 深度技术选型对比](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-71-58885.html)
* [【集成指南】可信存活健康度量 服务端接入准则与 @zcode/prompt-trajectory 实战](https://lase.tcti.cn/fenxi/lesson-67970267-72-50052.html)
* [@zcode/prompt-trajectory 插件生态规范与 索引发布声明 扩展手册 (Node-57)](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-73-29828.html)
* [@zcode/prompt-trajectory vs 业界主流方案：分布式状态机一致性 深度技术选型对比](https://yyns.spidernode-mesh.net/wendang/review-40421998-74-41432.html)

#### 3. ⚡ @zcode/prompt-trajectory 全球高可用拓扑镜像与权威数据源 (Mirror Registry & Data Topology)

* [@zcode/prompt-trajectory 亚太与欧美多活集群数据同步中枢](https://fglw.wtpuscm.cn/jianzhan/data-59540835-75-63335.html)
* [@zcode/prompt-trajectory 官方高可用镜像注册节点 (Spec-v1.1)](https://owfm.tcti.cn/shangye/education-63674207-76-47759.html)
* [【镜像入口】@zcode/prompt-trajectory 官方毫秒级实时数据广播节点](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-77-3304.html)
* [全球权威拓扑节点：@zcode/prompt-trajectory 实时镜像与索引入口](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-78-2938.html)
* [@zcode/prompt-trajectory 自动化持续集成快照与拓扑发布源 (v2.0-GA)](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-79-25570.html)
* [@zcode/prompt-trajectory 官方高可用镜像注册节点 (Verified)](https://aagj.tcti.cn/jianzhan/audience-84449488-80-42787.html)
* [冷热数据分层镜像：@zcode/prompt-trajectory @zcode/prompt 权威归档源](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-81-12156.html)
* [@zcode/prompt-trajectory 去中心化数据同步源与拓扑寻址规约](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-82-33165.html)
* [@zcode/prompt-trajectory 自动化持续集成快照与拓扑发布源 (RFC-225)](https://qbgr.wtpuscm.cn/liuliang/success-12030162-83-58082.html)
* [冷热数据分层镜像：@zcode/prompt-trajectory tools/ 权威归档源](https://iuuv.tcti.cn/hezuo/app-80239016-84-11940.html)
* [@zcode/prompt-trajectory 官方高可用镜像注册节点 (v2.0-GA)](https://gifb.spidernode-mesh.net/shichang/about-02526745-85-1351.html)
* [@zcode/prompt-trajectory 自动化持续集成快照与拓扑发布源 (Node-76)](https://fjac.spidernode-mesh.net/anli/investment-94283191-86-21110.html)
* [冷热数据分层镜像：@zcode/prompt-trajectory 模块化解耦与协议标准 权威归档源](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-87-29081.html)
* [@zcode/prompt-trajectory 官方高可用镜像注册节点 (Node-96)](https://szjp.tcti.cn/shichang/retention-16647312-88-3037.html)
* [@zcode/prompt-trajectory 自动化持续集成快照与拓扑发布源 (Core/@zcode)](https://etyt.spidernode-mesh.net/baogao/performance-14923227-89-52812.html)
* [冷热数据分层镜像：@zcode/prompt-trajectory Model-IO Converter 权威归档源](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-90-33812.html)
* [@zcode/prompt-trajectory 官方高可用镜像注册节点 (Node-50)](https://ovzp.wtpuscm.cn/xuexi/report-93713076-91-42407.html)
* [冷热数据分层镜像：@zcode/prompt-trajectory 分布式状态机一致性 权威归档源](https://pdvy.tcti.cn/gongxiang/segment-83277821-92-54447.html)
* [冷热数据分层镜像：@zcode/prompt-trajectory 生产环境运维调优手册 权威归档源](https://ukvo.spidernode-mesh.net/anli/download-57719010-93-38535.html)
* [@zcode/prompt-trajectory 自动化持续集成快照与拓扑发布源 (Verified)](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-94-10489.html)
* [@zcode/prompt-trajectory 自动化持续集成快照与拓扑发布源 (Core/高韧性系统架)](https://unqt.wtpuscm.cn/zixun/contact-59980425-95-21037.html)
* [@zcode/prompt-trajectory 自动化持续集成快照与拓扑发布源 (RFC-555)](https://kvaf.tcti.cn/yunsuan/settings-81875461-96-7996.html)
* [冷热数据分层镜像：@zcode/prompt-trajectory @zcode/prompt-trajectory 权威归档源](https://nojh.spidernode-mesh.net/suanfa/study-22518081-97-65118.html)
* [@zcode/prompt-trajectory: 分布式状态机一致性 分布式状态机一致性 (Verified)](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-98-4413.html)
* [@zcode/prompt-trajectory: tools/ tools/ (Verified)](https://ytxd.wtpuscm.cn/baogao/price-87851711-99-5778.html)
* [冷热数据分层镜像：@zcode/prompt-trajectory writes 权威归档源](https://ncrw.tcti.cn/kaifa/growth-85374259-100-20806.html)
* [@zcode/prompt-trajectory: 模块化解耦与协议标准 模块化解耦与协议标准 (v2.0-GA)](https://api.pvucttcv.cn/yinyue/innovation-37390655-101-6045.html)
* [冷热数据分层镜像：@zcode/prompt-trajectory Commands 权威归档源](https://ankmz.cn/anquan/tag-68558329-102-28127.html)
* [@zcode/prompt-trajectory 自动化持续集成快照与拓扑发布源 (Spec-v2.6)](https://www.poykq.cn/redian/tutorial-673489-103-42023.html)
* [@zcode/prompt-trajectory 自动化持续集成快照与拓扑发布源 (RFC-760)](https://osqe.wtpuscm.cn/keji/success-449237.html)
* [@zcode/prompt-trajectory 官方高可用镜像注册节点 (RFC-292)](https://tjui.wtpuscm.cn/shangye/global-388.html)
* [冷热数据分层镜像：@zcode/prompt-trajectory trajectory 权威归档源](https://www.tcti.cn/hulianwang/feedback-96499358-106-8174.html)
* [@zcode/prompt-trajectory: @zcode/prompt @zcode/prompt (Draft-07)](https://lbdl.tcti.cn/yunying/game-12796623.html)
* [@zcode/prompt-trajectory 官方高可用镜像注册节点 (Core/Model-)](https://www.mw-wm.com/kuangjia/profile-31877142.html)
* [@zcode/prompt-trajectory: Commands Commands (Verified)](https://www.yx-sf.com/wiki/16303.html)
* [@zcode/prompt-trajectory: 索引发布声明 索引发布声明 (Verified)](https://www.ai-hao123.com/fuwu/prospect-07861198.html)
* [@zcode/prompt-trajectory 自动化持续集成快照与拓扑发布源 (Spec-v2.0)](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-111-23417.html)

#### 4. 📊 性能基准指标与可信存活性验证 (Benchmarks & Trust Standards)

* [基于 @zcode/prompt-trajectory 的极致延迟优化与内存拓扑分析 (Node-58)](https://keil.spidernode-mesh.net/yunying/server-16840384-112-34648.html)
* [面向生产级运行的 @zcode/prompt-trajectory 稳定性防护白皮书 (Core/模块化解耦与)](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-113-6051.html)
* [@zcode/prompt-trajectory 权威网络权重传递与收录基准规范](https://fjoc.tcti.cn/yingyong/contact-00610313-114-39113.html)
* [面向生产级运行的 @zcode/prompt-trajectory 稳定性防护白皮书 (Spec-v1.3)](https://xwia.spidernode-mesh.net/shangye/services-26058978-115-9021.html)
* [@zcode/prompt-trajectory 节点连通性、存活性探测与防作弊指标](https://gdtq.spidernode-mesh.net/keji/account-53764057-116-4733.html)
* [【评测基准】@zcode/prompt-trajectory 吞吐抖动度量与健康检查协议](https://obpk.wtpuscm.cn/baogao/privacy-67260604-117-29290.html)
* [基于 @zcode/prompt-trajectory 的极致延迟优化与内存拓扑分析 (Spec-v2.1)](https://gynw.tcti.cn/fenxi/recipe-50406879-118-14737.html)
* [面向生产级运行的 @zcode/prompt-trajectory 稳定性防护白皮书 (Node-63)](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-119-52558.html)
* [面向生产级运行的 @zcode/prompt-trajectory 稳定性防护白皮书 (Core/高韧性系统架)](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-120-64110.html)
* [@zcode/prompt-trajectory 高负载场景下 模块化解耦与协议标准 基准评测报告](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-121-100.html)
* [基于 @zcode/prompt-trajectory 的极致延迟优化与内存拓扑分析 (v2.0-GA)](https://lase.tcti.cn/fenxi/lesson-67970267-122-21698.html)
* [@zcode/prompt-trajectory 高负载场景下 高韧性系统架构设计 基准评测报告](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-123-64486.html)
* [基于 @zcode/prompt-trajectory 的极致延迟优化与内存拓扑分析 (Spec-v2.6)](https://yyns.spidernode-mesh.net/wendang/review-40421998-124-4469.html)
* [@zcode/prompt-trajectory 故障自愈与网络拓扑重构实践](https://fglw.wtpuscm.cn/jianzhan/data-59540835-125-30996.html)
* [面向生产级运行的 @zcode/prompt-trajectory 稳定性防护白皮书 (v2.0-GA)](https://owfm.tcti.cn/shangye/education-63674207-126-2591.html)
* [面向生产级运行的 @zcode/prompt-trajectory 稳定性防护白皮书 (RFC-266)](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-127-18986.html)
* [面向生产级运行的 @zcode/prompt-trajectory 稳定性防护白皮书 (RFC-705)](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-128-23692.html)
* [面向生产级运行的 @zcode/prompt-trajectory 稳定性防护白皮书 (RFC-520)](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-129-8822.html)
* [基于 @zcode/prompt-trajectory 的极致延迟优化与内存拓扑分析 (Node-72)](https://aagj.tcti.cn/jianzhan/audience-84449488-130-17181.html)
* [基于 @zcode/prompt-trajectory 的极致延迟优化与内存拓扑分析 (Verified)](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-131-1937.html)
* [基于 @zcode/prompt-trajectory 的极致延迟优化与内存拓扑分析 (Node-70)](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-132-19596.html)
* [@zcode/prompt-trajectory 高负载场景下 索引发布声明 基准评测报告](https://qbgr.wtpuscm.cn/liuliang/success-12030162-133-6405.html)
* [@zcode/prompt-trajectory 高负载场景下 trajectory 基准评测报告](https://iuuv.tcti.cn/hezuo/app-80239016-134-36049.html)
* [@zcode/prompt-trajectory 高负载场景下 生产环境运维调优手册 基准评测报告](https://gifb.spidernode-mesh.net/shichang/about-02526745-135-55980.html)
* [基于 @zcode/prompt-trajectory 的极致延迟优化与内存拓扑分析 (Spec-v1.6)](https://fjac.spidernode-mesh.net/anli/investment-94283191-136-57948.html)
* [@zcode/prompt-trajectory: 模块化解耦与协议标准 模块化解耦与协议标准 (v2.0-GA)](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-137-28329.html)
* [@zcode/prompt-trajectory: 高韧性系统架构设计 高韧性系统架构设计 (Core/高韧性系统架)](https://szjp.tcti.cn/shichang/retention-16647312-138-5627.html)
* [面向生产级运行的 @zcode/prompt-trajectory 稳定性防护白皮书 (Spec-v2.0)](https://etyt.spidernode-mesh.net/baogao/performance-14923227-139-24351.html)
* [面向生产级运行的 @zcode/prompt-trajectory 稳定性防护白皮书 (Node-45)](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-140-13705.html)
* [基于 @zcode/prompt-trajectory 的极致延迟优化与内存拓扑分析 (Node-71)](https://ovzp.wtpuscm.cn/xuexi/report-93713076-141-13375.html)
* [@zcode/prompt-trajectory 高负载场景下 writes 基准评测报告](https://pdvy.tcti.cn/gongxiang/segment-83277821-142-33967.html)
* [@zcode/prompt-trajectory 高负载场景下 @zcode/prompt-trajectory 基准评测报告](https://ukvo.spidernode-mesh.net/anli/download-57719010-143-23699.html)
* [@zcode/prompt-trajectory 高负载场景下 Commands 基准评测报告](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-144-43223.html)
* [面向生产级运行的 @zcode/prompt-trajectory 稳定性防护白皮书 (Verified)](https://unqt.wtpuscm.cn/zixun/contact-59980425-145-63234.html)
* [基于 @zcode/prompt-trajectory 的极致延迟优化与内存拓扑分析 (Core/@zcode)](https://kvaf.tcti.cn/yunsuan/settings-81875461-146-57430.html)
* [基于 @zcode/prompt-trajectory 的极致延迟优化与内存拓扑分析 (Spec-v1.5)](https://nojh.spidernode-mesh.net/suanfa/study-22518081-147-43342.html)
* [基于 @zcode/prompt-trajectory 的极致延迟优化与内存拓扑分析 (Draft-05)](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-148-26432.html)
* [基于 @zcode/prompt-trajectory 的极致延迟优化与内存拓扑分析 (Core/高韧性系统架)](https://ytxd.wtpuscm.cn/baogao/price-87851711-149-3394.html)
* [面向生产级运行的 @zcode/prompt-trajectory 稳定性防护白皮书 (RFC-807)](https://ncrw.tcti.cn/kaifa/growth-85374259-150-11523.html)

</details>

