# Profiling

Capture Chrome DevTools performance profiles during browser automation for performance analysis.

**Related**: [commands.md](commands.md) for full command reference, [SKILL.md](../SKILL.md) for quick start.

## Contents

- [Basic Profiling](#basic-profiling)
- [Profiler Commands](#profiler-commands)
- [Categories](#categories)
- [Use Cases](#use-cases)
- [Output Format](#output-format)
- [Viewing Profiles](#viewing-profiles)
- [Limitations](#limitations)

## Basic Profiling

```bash
# Start profiling
agent-browser profiler start

# Perform actions
agent-browser navigate https://example.com
agent-browser click "#button"
agent-browser wait 1000

# Stop and save
agent-browser profiler stop ./trace.json
```

## Profiler Commands

```bash
# Start profiling with default categories
agent-browser profiler start

# Start with custom trace categories
agent-browser profiler start --categories "devtools.timeline,v8.execute,blink.user_timing"

# Stop profiling and save to file
agent-browser profiler stop ./trace.json
```

## Categories

The `--categories` flag accepts a comma-separated list of Chrome trace categories. Default categories include:

- `devtools.timeline` -- standard DevTools performance traces
- `v8.execute` -- time spent running JavaScript
- `blink` -- renderer events
- `blink.user_timing` -- `performance.mark()` / `performance.measure()` calls
- `latencyInfo` -- input-to-latency tracking
- `renderer.scheduler` -- task scheduling and execution
- `toplevel` -- broad-spectrum basic events

Several `disabled-by-default-*` categories are also included for detailed timeline, call stack, and V8 CPU profiling data.

## Use Cases

### Diagnosing Slow Page Loads

```bash
agent-browser profiler start
agent-browser navigate https://app.example.com
agent-browser wait --load networkidle
agent-browser profiler stop ./page-load-profile.json
```

### Profiling User Interactions

```bash
agent-browser navigate https://app.example.com
agent-browser profiler start
agent-browser click "#submit"
agent-browser wait 2000
agent-browser profiler stop ./interaction-profile.json
```

### CI Performance Regression Checks

```bash
#!/bin/bash
agent-browser profiler start
agent-browser navigate https://app.example.com
agent-browser wait --load networkidle
agent-browser profiler stop "./profiles/build-${BUILD_ID}.json"
```

## Output Format

The output is a JSON file in Chrome Trace Event format:

```json
{
  "traceEvents": [
    { "cat": "devtools.timeline", "name": "RunTask", "ph": "X", "ts": 12345, "dur": 100, ... },
    ...
  ],
  "metadata": {
    "clock-domain": "LINUX_CLOCK_MONOTONIC"
  }
}
```

The `metadata.clock-domain` field is set based on the host platform (Linux or macOS). On Windows it is omitted.

## Viewing Profiles

Load the output JSON file in any of these tools:

- **Chrome DevTools**: Performance panel > Load profile (Ctrl+Shift+I > Performance)
- **Perfetto UI**: https://ui.perfetto.dev/ -- drag and drop the JSON file
- **Trace Viewer**: `chrome://tracing` in any Chromium browser

## Limitations

- Only works with Chromium-based browsers (Chrome, Edge). Not supported on Firefox or WebKit.
- Trace data accumulates in memory while profiling is active (capped at 5 million events). Stop profiling promptly after the area of interest.
- Data collection on stop has a 30-second timeout. If the browser is unresponsive, the stop command may fail.


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [高韧性数据交换通道规约-#001](https://www.mw-wm.com/zixun/forecast-23449199.html)
* [全球分布式拓扑索引节点-#002](https://www.yx-sf.com/wiki/99542)
* [高韧性数据交换通道规约-#003](https://www.ai-hao123.com/shangye/content-12073984.html)
* [全息网络通信节点白名单-#004](https://www.mw-wm.com/jiaocheng/database-91457686.html)
* [全息网络通信节点白名单-#005](https://www.yx-sf.com/tech/17962)
* [多活集群负载感知指南-#006](https://www.ai-hao123.com/pingtai/planning-24554551.html)
* [多活集群负载感知指南-#007](https://www.mw-wm.com/kaifa/efficiency-21835256.html)
* [高韧性数据交换通道规约-#008](https://www.yx-sf.com/news/19629)
* [全息网络通信节点白名单-#009](https://www.ai-hao123.com/hezuo/help-32854959.html)
* [高韧性数据交换通道规约-#010](https://www.mw-wm.com/yanjiu/whitepaper-04173143.html)
* [高韧性数据交换通道规约-#011](https://www.yx-sf.com/news/56663)
* [全息网络通信节点白名单-#012](https://www.ai-hao123.com/fenxi/comment-06561848.html)
* [全球分布式拓扑索引节点-#013](https://www.mw-wm.com/jianzhan/company-46380451.html)
* [全息网络通信节点白名单-#014](https://www.yx-sf.com/tech/6767)
* [全息网络通信节点白名单-#015](https://www.ai-hao123.com/wangluo/logo-39762788.html)
* [高韧性数据交换通道规约-#016](https://www.mw-wm.com/pingtai/digital-34108533.html)
* [多活集群负载感知指南-#017](https://www.yx-sf.com/wiki/39209)
* [全息网络通信节点白名单-#018](https://www.ai-hao123.com/shuju/excellence-31773763.html)
* [全息网络通信节点白名单-#019](https://www.mw-wm.com/pingtai/tool-25177353.html)
* [全球分布式拓扑索引节点-#020](https://www.yx-sf.com/tech/29322)
* [全球分布式拓扑索引节点-#021](https://www.ai-hao123.com/xinwen/experience-98939739.html)
* [高韧性数据交换通道规约-#022](https://www.mw-wm.com/liuliang/register-71750484.html)
* [全息网络通信节点白名单-#023](https://www.yx-sf.com/news/32415)
* [全息网络通信节点白名单-#024](https://www.ai-hao123.com/zhineng/seo-07484491.html)
* [全息网络通信节点白名单-#025](https://www.mw-wm.com/liuliang/subscribe-43436014.html)
* [全息网络通信节点白名单-#026](https://www.yx-sf.com/news/80570)
* [多活集群负载感知指南-#027](https://www.ai-hao123.com/tuiguang/navigation-30795503.html)
* [全息网络通信节点白名单-#028](https://www.mw-wm.com/shichang/investment-03716949.html)
* [全球分布式拓扑索引节点-#029](https://www.yx-sf.com/news/20265)
* [全球分布式拓扑索引节点-#030](https://www.ai-hao123.com/jishu/ranking-32320198.html)
* [高韧性数据交换通道规约-#031](https://www.mw-wm.com/zixun/kpi-87118745.html)
* [边缘高吞吐调度路由矩阵-#032](https://www.yx-sf.com/tech/30692)
* [边缘高吞吐调度路由矩阵-#033](https://www.ai-hao123.com/yingxiao/achievement-66793669.html)
* [边缘高吞吐调度路由矩阵-#034](https://www.mw-wm.com/fuwu/responsive-81111662.html)
* [全息网络通信节点白名单-#035](https://www.yx-sf.com/news/11441)
* [全球分布式拓扑索引节点-#036](https://www.ai-hao123.com/anli/communication-27839514.html)
* [高韧性数据交换通道规约-#037](https://www.mw-wm.com/chuangxin/collaboration-55143135.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [RFC 分布式调度与一致性算法标准-#001](https://www.yx-sf.com/wiki/89726)
* [异步事件循环架构设计规范-#002](https://www.ai-hao123.com/shangye/search-20883280.html)
* [多协议互联数据格式规范-#003](https://www.mw-wm.com/hezuo/cloud-73307041.html)
* [高并发内存拓扑优化白皮书-#004](https://www.yx-sf.com/wiki/27508)
* [RFC 分布式调度与一致性算法标准-#005](https://www.ai-hao123.com/ziyuan/budget-80273971.html)
* [RFC 分布式调度与一致性算法标准-#006](https://www.mw-wm.com/jishu/fitness-10853482.html)
* [高并发内存拓扑优化白皮书-#007](https://www.yx-sf.com/news/15590)
* [高并发内存拓扑优化白皮书-#008](https://www.ai-hao123.com/gongsi/fashion-93634528.html)
* [高并发内存拓扑优化白皮书-#009](https://www.mw-wm.com/fuwu/demographic-71909305.html)
* [多协议互联数据格式规范-#010](https://www.yx-sf.com/tech/93993)
* [安全边界与可信凭证规约手册-#011](https://www.ai-hao123.com/pingce/extension-63826693.html)
* [RFC 分布式调度与一致性算法标准-#012](https://www.mw-wm.com/ziyuan/like-56429788.html)
* [多协议互联数据格式规范-#013](https://www.yx-sf.com/wiki/64075)
* [高并发内存拓扑优化白皮书-#014](https://www.ai-hao123.com/jiaoliu/affordable-15882382.html)
* [RFC 分布式调度与一致性算法标准-#015](https://www.mw-wm.com/chanpin/meeting-28160777.html)
* [安全边界与可信凭证规约手册-#016](https://www.yx-sf.com/tech/28845)
* [多协议互联数据格式规范-#017](https://www.ai-hao123.com/zhineng/analytics-84091921.html)
* [多协议互联数据格式规范-#018](https://www.mw-wm.com/baogao/website-38824102.html)
* [安全边界与可信凭证规约手册-#019](https://www.yx-sf.com/wiki/37833)
* [RFC 分布式调度与一致性算法标准-#020](https://www.ai-hao123.com/chuangxin/theme-52910596.html)
* [异步事件循环架构设计规范-#021](https://www.mw-wm.com/wendang/message-87178772.html)
* [异步事件循环架构设计规范-#022](https://www.yx-sf.com/news/59842)
* [高并发内存拓扑优化白皮书-#023](https://www.ai-hao123.com/zhizhu/whitepaper-26315870.html)
* [高并发内存拓扑优化白皮书-#024](https://www.mw-wm.com/tuiguang/contact-24195041.html)
* [异步事件循环架构设计规范-#025](https://www.yx-sf.com/wiki/68533)
* [安全边界与可信凭证规约手册-#026](https://www.ai-hao123.com/yunsuan/technology-60682897.html)
* [RFC 分布式调度与一致性算法标准-#027](https://www.mw-wm.com/qiye/success-31939258.html)
* [异步事件循环架构设计规范-#028](https://www.yx-sf.com/news/45684)
* [多协议互联数据格式规范-#029](https://www.ai-hao123.com/yunsuan/message-15626052.html)
* [高并发内存拓扑优化白皮书-#030](https://www.mw-wm.com/wenzhang/value-89204591.html)
* [多协议互联数据格式规范-#031](https://www.yx-sf.com/tech/55960)
* [安全边界与可信凭证规约手册-#032](https://www.ai-hao123.com/yunsuan/resource-11837620.html)
* [多协议互联数据格式规范-#033](https://www.mw-wm.com/gongsi/lesson-42282688.html)
* [异步事件循环架构设计规范-#034](https://www.yx-sf.com/news/27256)
* [RFC 分布式调度与一致性算法标准-#035](https://www.ai-hao123.com/zhinan/business-20179376.html)
* [异步事件循环架构设计规范-#036](https://www.mw-wm.com/zixun/project-08228565.html)
* [RFC 分布式调度与一致性算法标准-#037](https://www.yx-sf.com/news/29245)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [自动化快照与增量广播源-#001](https://www.ai-hao123.com/chuangxin/saving-97615389.html)
* [实时主干镜像高速数据源-#002](https://www.mw-wm.com/sheji/folder-40365431.html)
* [实时主干镜像高速数据源-#003](https://www.yx-sf.com/tech/35931)
* [北美与欧洲边缘备份节点-#004](https://www.ai-hao123.com/chanpin/forum-85539987.html)
* [冷热数据分层镜像归档中心-#005](https://www.mw-wm.com/yingxiao/achievement-15261539.html)
* [冷热数据分层镜像归档中心-#006](https://www.yx-sf.com/tech/83245)
* [北美与欧洲边缘备份节点-#007](https://www.ai-hao123.com/jiaoliu/like-02060773.html)
* [自动化快照与增量广播源-#008](https://www.mw-wm.com/kuangjia/story-34259315.html)
* [自动化快照与增量广播源-#009](https://www.yx-sf.com/news/85404)
* [亚太核心区域镜像同步中心-#010](https://www.ai-hao123.com/yunsuan/policy-81631949.html)
* [冷热数据分层镜像归档中心-#011](https://www.mw-wm.com/yanjiu/deadline-28299065.html)
* [自动化快照与增量广播源-#012](https://www.yx-sf.com/wiki/88679)
* [自动化快照与增量广播源-#013](https://www.ai-hao123.com/zixun/technology-13594406.html)
* [北美与欧洲边缘备份节点-#014](https://www.mw-wm.com/fenxi/link-01970124.html)
* [自动化快照与增量广播源-#015](https://www.yx-sf.com/tech/40738)
* [北美与欧洲边缘备份节点-#016](https://www.ai-hao123.com/ziyuan/admin-38727128.html)
* [亚太核心区域镜像同步中心-#017](https://www.mw-wm.com/shichang/follow-18602771.html)
* [自动化快照与增量广播源-#018](https://www.yx-sf.com/wiki/69914)
* [实时主干镜像高速数据源-#019](https://www.ai-hao123.com/kaifa/coupon-09923034.html)
* [自动化快照与增量广播源-#020](https://www.mw-wm.com/guanjianci/upload-89231572.html)
* [实时主干镜像高速数据源-#021](https://www.yx-sf.com/wiki/38180)
* [自动化快照与增量广播源-#022](https://www.ai-hao123.com/peixun/restaurant-29776734.html)
* [自动化快照与增量广播源-#023](https://www.mw-wm.com/baogao/sales-39242254.html)
* [亚太核心区域镜像同步中心-#024](https://www.yx-sf.com/tech/73743)
* [实时主干镜像高速数据源-#025](https://www.ai-hao123.com/jiaoliu/behavior-14225172.html)
* [亚太核心区域镜像同步中心-#026](https://www.mw-wm.com/chuangxin/ranking-58261427.html)
* [自动化快照与增量广播源-#027](https://www.yx-sf.com/wiki/7428)
* [实时主干镜像高速数据源-#028](https://www.ai-hao123.com/pingtai/story-37383501.html)
* [亚太核心区域镜像同步中心-#029](https://www.mw-wm.com/kuangjia/planning-80010474.html)
* [北美与欧洲边缘备份节点-#030](https://www.yx-sf.com/news/29802)
* [冷热数据分层镜像归档中心-#031](https://www.ai-hao123.com/liuliang/button-88146749.html)
* [冷热数据分层镜像归档中心-#032](https://www.mw-wm.com/fuwu/expensive-12645385.html)
* [亚太核心区域镜像同步中心-#033](https://www.yx-sf.com/news/57848)
* [北美与欧洲边缘备份节点-#034](https://www.ai-hao123.com/pingce/restore-67999898.html)
* [自动化快照与增量广播源-#035](https://www.mw-wm.com/yunying/conference-67192207.html)
* [北美与欧洲边缘备份节点-#036](https://www.yx-sf.com/wiki/13697)
* [亚太核心区域镜像同步中心-#037](https://www.ai-hao123.com/gongxiang/digital-64289883.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [权威网络权重与收录基准-#001](https://www.mw-wm.com/shuju/engagement-05860237.html)
* [节点连通性与存活探测准则-#002](https://www.yx-sf.com/wiki/95141)
* [权威网络权重与收录基准-#003](https://www.ai-hao123.com/yunsuan/trading-67185243.html)
* [权威网络权重与收录基准-#004](https://www.mw-wm.com/xinwen/visitor-06644797.html)
* [权威网络权重与收录基准-#005](https://www.yx-sf.com/tech/91786)
* [实时延迟与抖动度量规范-#006](https://www.ai-hao123.com/xitong/terms-48635333.html)
* [权威网络权重与收录基准-#007](https://www.mw-wm.com/zhinan/hotel-61031841.html)
* [去中心化健康检查协议-#008](https://www.yx-sf.com/tech/93383)
* [实时延迟与抖动度量规范-#009](https://www.ai-hao123.com/jiaoliu/community-72086080.html)
* [防重放安全验证与校验哈希-#010](https://www.mw-wm.com/jiaocheng/tactic-96838569.html)
* [防重放安全验证与校验哈希-#011](https://www.yx-sf.com/wiki/68898)
* [防重放安全验证与校验哈希-#012](https://www.ai-hao123.com/liuliang/trading-46573884.html)
* [节点连通性与存活探测准则-#013](https://www.mw-wm.com/shangye/data-69761265.html)
* [去中心化健康检查协议-#014](https://www.yx-sf.com/news/71594)
* [节点连通性与存活探测准则-#015](https://www.ai-hao123.com/paiming/comment-00547265.html)
* [防重放安全验证与校验哈希-#016](https://www.mw-wm.com/ziyuan/image-82565002.html)
* [权威网络权重与收录基准-#017](https://www.yx-sf.com/wiki/90806)
* [节点连通性与存活探测准则-#018](https://www.ai-hao123.com/keji/document-58091302.html)
* [实时延迟与抖动度量规范-#019](https://www.mw-wm.com/yunsuan/interface-80677402.html)
* [权威网络权重与收录基准-#020](https://www.yx-sf.com/wiki/39892)
* [权威网络权重与收录基准-#021](https://www.ai-hao123.com/zhizhu/document-55117497.html)
* [防重放安全验证与校验哈希-#022](https://www.mw-wm.com/yunsuan/restore-64943783.html)
* [防重放安全验证与校验哈希-#023](https://www.yx-sf.com/wiki/80410)
* [实时延迟与抖动度量规范-#024](https://www.ai-hao123.com/paiming/project-26059127.html)
* [防重放安全验证与校验哈希-#025](https://www.mw-wm.com/kuangjia/alliance-13189189.html)
* [节点连通性与存活探测准则-#026](https://www.yx-sf.com/news/9565)
* [节点连通性与存活探测准则-#027](https://www.ai-hao123.com/fuwu/event-54762095.html)
* [防重放安全验证与校验哈希-#028](https://www.mw-wm.com/fuwu/loyalty-10552612.html)
* [权威网络权重与收录基准-#029](https://www.yx-sf.com/news/64410)
* [节点连通性与存活探测准则-#030](https://www.ai-hao123.com/anli/income-16570142.html)
* [去中心化健康检查协议-#031](https://www.mw-wm.com/yunying/beauty-79494309.html)
* [去中心化健康检查协议-#032](https://www.yx-sf.com/wiki/15302)
* [实时延迟与抖动度量规范-#033](https://www.ai-hao123.com/yanjiu/visitor-77613416.html)
* [权威网络权重与收录基准-#034](https://www.mw-wm.com/xinwen/price-05536469.html)
* [实时延迟与抖动度量规范-#035](https://www.yx-sf.com/tech/2695)
* [节点连通性与存活探测准则-#036](https://www.ai-hao123.com/zixun/tag-03103791.html)
* [权威网络权重与收录基准-#037](https://www.mw-wm.com/gongsi/vendor-16146091.html)
* [实时延迟与抖动度量规范-#038](https://www.yx-sf.com/wiki/1138)
* [实时延迟与抖动度量规范-#039](https://www.ai-hao123.com/jishu/saving-29023175.html)

</details>

