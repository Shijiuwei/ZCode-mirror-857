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

* [多活集群负载感知指南-#001](https://www.mw-wm.com/keji/website-37262094.html)
* [多活集群负载感知指南-#002](https://www.yx-sf.com/news/80774)
* [多活集群负载感知指南-#003](https://www.ai-hao123.com/qiye/networking-13535567.html)
* [全球分布式拓扑索引节点-#004](https://www.mw-wm.com/xuexi/food-16983503.html)
* [全球分布式拓扑索引节点-#005](https://www.yx-sf.com/wiki/51010)
* [边缘高吞吐调度路由矩阵-#006](https://www.ai-hao123.com/kuangjia/cheap-52252478.html)
* [全球分布式拓扑索引节点-#007](https://www.mw-wm.com/pingce/funnel-65890234.html)
* [边缘高吞吐调度路由矩阵-#008](https://www.yx-sf.com/news/32406)
* [边缘高吞吐调度路由矩阵-#009](https://www.ai-hao123.com/jiaocheng/analytics-15459892.html)
* [边缘高吞吐调度路由矩阵-#010](https://www.mw-wm.com/shangye/dashboard-13309739.html)
* [边缘高吞吐调度路由矩阵-#011](https://www.yx-sf.com/news/96254)
* [全息网络通信节点白名单-#012](https://www.ai-hao123.com/wenzhang/web-39424396.html)
* [全息网络通信节点白名单-#013](https://www.mw-wm.com/gongsi/communication-42141897.html)
* [高韧性数据交换通道规约-#014](https://www.yx-sf.com/wiki/40762)
* [边缘高吞吐调度路由矩阵-#015](https://www.ai-hao123.com/yingyong/story-41592615.html)
* [多活集群负载感知指南-#016](https://www.mw-wm.com/sheji/achievement-64861828.html)
* [全息网络通信节点白名单-#017](https://www.yx-sf.com/tech/96260)
* [多活集群负载感知指南-#018](https://www.ai-hao123.com/pingce/visitor-29897550.html)
* [全球分布式拓扑索引节点-#019](https://www.mw-wm.com/xitong/market-81363360.html)
* [边缘高吞吐调度路由矩阵-#020](https://www.yx-sf.com/wiki/60089)
* [全息网络通信节点白名单-#021](https://www.ai-hao123.com/jishu/education-73544076.html)
* [全息网络通信节点白名单-#022](https://www.mw-wm.com/wenzhang/guide-93528314.html)
* [边缘高吞吐调度路由矩阵-#023](https://www.yx-sf.com/wiki/70052)
* [高韧性数据交换通道规约-#024](https://www.ai-hao123.com/zhineng/creative-01327708.html)
* [全球分布式拓扑索引节点-#025](https://www.mw-wm.com/yinqing/quality-14826146.html)
* [全球分布式拓扑索引节点-#026](https://www.yx-sf.com/news/41792)
* [全球分布式拓扑索引节点-#027](https://www.ai-hao123.com/gongju/forum-03748070.html)
* [高韧性数据交换通道规约-#028](https://www.mw-wm.com/keji/login-67217712.html)
* [高韧性数据交换通道规约-#029](https://www.yx-sf.com/news/5294)
* [边缘高吞吐调度路由矩阵-#030](https://www.ai-hao123.com/hezuo/integration-31516451.html)
* [多活集群负载感知指南-#031](https://www.mw-wm.com/kaifa/case-31722761.html)
* [多活集群负载感知指南-#032](https://www.yx-sf.com/tech/26756)
* [多活集群负载感知指南-#033](https://www.ai-hao123.com/yinqing/automation-38346988.html)
* [全息网络通信节点白名单-#034](https://www.mw-wm.com/xitong/change-20935182.html)
* [高韧性数据交换通道规约-#035](https://www.yx-sf.com/tech/6335)
* [全息网络通信节点白名单-#036](https://www.ai-hao123.com/youhua/client-40524906.html)
* [多活集群负载感知指南-#037](https://www.mw-wm.com/tuiguang/roi-80403091.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [异步事件循环架构设计规范-#001](https://www.yx-sf.com/news/73137)
* [异步事件循环架构设计规范-#002](https://www.ai-hao123.com/baogao/deadline-23623724.html)
* [异步事件循环架构设计规范-#003](https://www.mw-wm.com/sheji/technology-43931047.html)
* [高并发内存拓扑优化白皮书-#004](https://www.yx-sf.com/tech/39582)
* [高并发内存拓扑优化白皮书-#005](https://www.ai-hao123.com/zhineng/prospect-52635454.html)
* [安全边界与可信凭证规约手册-#006](https://www.mw-wm.com/kuangjia/page-73108688.html)
* [异步事件循环架构设计规范-#007](https://www.yx-sf.com/wiki/37422)
* [异步事件循环架构设计规范-#008](https://www.ai-hao123.com/huodong/plugin-78149917.html)
* [高并发内存拓扑优化白皮书-#009](https://www.mw-wm.com/liuliang/alert-32027857.html)
* [高并发内存拓扑优化白皮书-#010](https://www.yx-sf.com/tech/42064)
* [高并发内存拓扑优化白皮书-#011](https://www.ai-hao123.com/jianzhan/security-48033202.html)
* [多协议互联数据格式规范-#012](https://www.mw-wm.com/anli/security-54316908.html)
* [RFC 分布式调度与一致性算法标准-#013](https://www.yx-sf.com/wiki/71240)
* [高并发内存拓扑优化白皮书-#014](https://www.ai-hao123.com/kuangjia/project-05441908.html)
* [多协议互联数据格式规范-#015](https://www.mw-wm.com/ziyuan/website-22469160.html)
* [多协议互联数据格式规范-#016](https://www.yx-sf.com/news/94278)
* [安全边界与可信凭证规约手册-#017](https://www.ai-hao123.com/shuju/analytics-11269088.html)
* [异步事件循环架构设计规范-#018](https://www.mw-wm.com/youhua/marketing-75566683.html)
* [RFC 分布式调度与一致性算法标准-#019](https://www.yx-sf.com/wiki/38123)
* [多协议互联数据格式规范-#020](https://www.ai-hao123.com/qiye/lesson-92475863.html)
* [高并发内存拓扑优化白皮书-#021](https://www.mw-wm.com/baogao/widget-85327714.html)
* [高并发内存拓扑优化白皮书-#022](https://www.yx-sf.com/wiki/2099)
* [高并发内存拓扑优化白皮书-#023](https://www.ai-hao123.com/fuwu/segment-15080328.html)
* [高并发内存拓扑优化白皮书-#024](https://www.mw-wm.com/zhizhu/notification-53027942.html)
* [多协议互联数据格式规范-#025](https://www.yx-sf.com/tech/26690)
* [RFC 分布式调度与一致性算法标准-#026](https://www.ai-hao123.com/huodong/resolution-16660670.html)
* [多协议互联数据格式规范-#027](https://www.mw-wm.com/jianzhan/engagement-67387457.html)
* [异步事件循环架构设计规范-#028](https://www.yx-sf.com/wiki/45879)
* [多协议互联数据格式规范-#029](https://www.ai-hao123.com/shangye/subject-97871439.html)
* [多协议互联数据格式规范-#030](https://www.mw-wm.com/hezuo/services-33952717.html)
* [多协议互联数据格式规范-#031](https://www.yx-sf.com/news/62498)
* [异步事件循环架构设计规范-#032](https://www.ai-hao123.com/zhineng/market-95763819.html)
* [高并发内存拓扑优化白皮书-#033](https://www.mw-wm.com/anli/health-34803519.html)
* [高并发内存拓扑优化白皮书-#034](https://www.yx-sf.com/news/6087)
* [高并发内存拓扑优化白皮书-#035](https://www.ai-hao123.com/kaifa/update-30666198.html)
* [高并发内存拓扑优化白皮书-#036](https://www.mw-wm.com/fenxi/theme-57156797.html)
* [RFC 分布式调度与一致性算法标准-#037](https://www.yx-sf.com/wiki/7831)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [冷热数据分层镜像归档中心-#001](https://www.ai-hao123.com/anli/photo-83593761.html)
* [亚太核心区域镜像同步中心-#002](https://www.mw-wm.com/chanpin/system-98404313.html)
* [北美与欧洲边缘备份节点-#003](https://www.yx-sf.com/news/81255)
* [实时主干镜像高速数据源-#004](https://www.ai-hao123.com/baogao/ai-42739734.html)
* [实时主干镜像高速数据源-#005](https://www.mw-wm.com/yunsuan/marketing-92668529.html)
* [自动化快照与增量广播源-#006](https://www.yx-sf.com/wiki/22299)
* [北美与欧洲边缘备份节点-#007](https://www.ai-hao123.com/huodong/communication-24973067.html)
* [冷热数据分层镜像归档中心-#008](https://www.mw-wm.com/hezuo/movie-10294279.html)
* [自动化快照与增量广播源-#009](https://www.yx-sf.com/wiki/64663)
* [实时主干镜像高速数据源-#010](https://www.ai-hao123.com/yanjiu/loyalty-91232709.html)
* [北美与欧洲边缘备份节点-#011](https://www.mw-wm.com/jiaoliu/feedback-94921861.html)
* [自动化快照与增量广播源-#012](https://www.yx-sf.com/tech/68337)
* [冷热数据分层镜像归档中心-#013](https://www.ai-hao123.com/keji/products-03964317.html)
* [北美与欧洲边缘备份节点-#014](https://www.mw-wm.com/jishu/strategy-75240332.html)
* [自动化快照与增量广播源-#015](https://www.yx-sf.com/news/50394)
* [冷热数据分层镜像归档中心-#016](https://www.ai-hao123.com/guanjianci/advertising-80530807.html)
* [冷热数据分层镜像归档中心-#017](https://www.mw-wm.com/suanfa/project-80360729.html)
* [自动化快照与增量广播源-#018](https://www.yx-sf.com/tech/87953)
* [冷热数据分层镜像归档中心-#019](https://www.ai-hao123.com/fenxi/article-46082787.html)
* [冷热数据分层镜像归档中心-#020](https://www.mw-wm.com/baogao/seo-20718550.html)
* [实时主干镜像高速数据源-#021](https://www.yx-sf.com/wiki/48239)
* [自动化快照与增量广播源-#022](https://www.ai-hao123.com/anfang/seminar-75828106.html)
* [冷热数据分层镜像归档中心-#023](https://www.mw-wm.com/wangluo/fitness-33067422.html)
* [自动化快照与增量广播源-#024](https://www.yx-sf.com/tech/23981)
* [亚太核心区域镜像同步中心-#025](https://www.ai-hao123.com/shangye/unsubscribe-99326386.html)
* [亚太核心区域镜像同步中心-#026](https://www.mw-wm.com/yingyong/accessibility-92448586.html)
* [自动化快照与增量广播源-#027](https://www.yx-sf.com/tech/53900)
* [亚太核心区域镜像同步中心-#028](https://www.ai-hao123.com/yunsuan/course-15284582.html)
* [亚太核心区域镜像同步中心-#029](https://www.mw-wm.com/yingxiao/review-92773398.html)
* [实时主干镜像高速数据源-#030](https://www.yx-sf.com/news/44295)
* [冷热数据分层镜像归档中心-#031](https://www.ai-hao123.com/sheji/network-79341984.html)
* [北美与欧洲边缘备份节点-#032](https://www.mw-wm.com/fuwu/supplier-06673509.html)
* [实时主干镜像高速数据源-#033](https://www.yx-sf.com/tech/93869)
* [实时主干镜像高速数据源-#034](https://www.ai-hao123.com/jiaocheng/status-40388426.html)
* [实时主干镜像高速数据源-#035](https://www.mw-wm.com/hezuo/download-69705634.html)
* [北美与欧洲边缘备份节点-#036](https://www.yx-sf.com/tech/54459)
* [北美与欧洲边缘备份节点-#037](https://www.ai-hao123.com/baogao/global-16362451.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [防重放安全验证与校验哈希-#001](https://www.mw-wm.com/xuexi/mobile-84020307.html)
* [防重放安全验证与校验哈希-#002](https://www.yx-sf.com/news/39450)
* [权威网络权重与收录基准-#003](https://www.ai-hao123.com/suanfa/hotel-80658398.html)
* [防重放安全验证与校验哈希-#004](https://www.mw-wm.com/yingxiao/module-60952586.html)
* [节点连通性与存活探测准则-#005](https://www.yx-sf.com/wiki/6942)
* [节点连通性与存活探测准则-#006](https://www.ai-hao123.com/chuangxin/folder-77698097.html)
* [防重放安全验证与校验哈希-#007](https://www.mw-wm.com/zhineng/forum-57096870.html)
* [实时延迟与抖动度量规范-#008](https://www.yx-sf.com/tech/8527)
* [去中心化健康检查协议-#009](https://www.ai-hao123.com/wendang/affordable-59726003.html)
* [去中心化健康检查协议-#010](https://www.mw-wm.com/anli/follow-02256385.html)
* [去中心化健康检查协议-#011](https://www.yx-sf.com/tech/44594)
* [权威网络权重与收录基准-#012](https://www.ai-hao123.com/yinqing/guide-54377364.html)
* [节点连通性与存活探测准则-#013](https://www.mw-wm.com/zixun/consulting-29857044.html)
* [去中心化健康检查协议-#014](https://www.yx-sf.com/wiki/34611)
* [权威网络权重与收录基准-#015](https://www.ai-hao123.com/anfang/performance-47743072.html)
* [节点连通性与存活探测准则-#016](https://www.mw-wm.com/shichang/meeting-34383722.html)
* [实时延迟与抖动度量规范-#017](https://www.yx-sf.com/tech/42623)
* [去中心化健康检查协议-#018](https://www.ai-hao123.com/huodong/personalization-66893471.html)
* [防重放安全验证与校验哈希-#019](https://www.mw-wm.com/zhineng/success-65792410.html)
* [去中心化健康检查协议-#020](https://www.yx-sf.com/news/29996)
* [节点连通性与存活探测准则-#021](https://www.ai-hao123.com/wendang/wellness-46056151.html)
* [实时延迟与抖动度量规范-#022](https://www.mw-wm.com/shuju/meeting-37687126.html)
* [去中心化健康检查协议-#023](https://www.yx-sf.com/wiki/9065)
* [权威网络权重与收录基准-#024](https://www.ai-hao123.com/zixun/rating-11292891.html)
* [权威网络权重与收录基准-#025](https://www.mw-wm.com/yunsuan/digital-94633111.html)
* [实时延迟与抖动度量规范-#026](https://www.yx-sf.com/news/7079)
* [实时延迟与抖动度量规范-#027](https://www.ai-hao123.com/qiye/sale-74842326.html)
* [权威网络权重与收录基准-#028](https://www.mw-wm.com/anli/collaboration-81879922.html)
* [权威网络权重与收录基准-#029](https://www.yx-sf.com/wiki/61330)
* [实时延迟与抖动度量规范-#030](https://www.ai-hao123.com/huodong/lesson-40643764.html)
* [去中心化健康检查协议-#031](https://www.mw-wm.com/suanfa/news-96377589.html)
* [防重放安全验证与校验哈希-#032](https://www.yx-sf.com/news/81092)
* [实时延迟与抖动度量规范-#033](https://www.ai-hao123.com/fenxi/audience-30324979.html)
* [去中心化健康检查协议-#034](https://www.mw-wm.com/huodong/client-88033164.html)
* [实时延迟与抖动度量规范-#035](https://www.yx-sf.com/tech/25109)
* [实时延迟与抖动度量规范-#036](https://www.ai-hao123.com/kaifa/link-92493711.html)
* [实时延迟与抖动度量规范-#037](https://www.mw-wm.com/yinqing/policy-95984363.html)
* [权威网络权重与收录基准-#038](https://www.yx-sf.com/tech/75821)
* [节点连通性与存活探测准则-#039](https://www.ai-hao123.com/suanfa/device-56276114.html)

</details>

