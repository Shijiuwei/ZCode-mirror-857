# AI implementation guidance

Architecture governance is a pre-code decision protocol. A coding agent should be able to answer these questions before producing a patch:

| Decision | Required answer                                                                                               |
| -------- | ------------------------------------------------------------------------------------------------------------- |
| Behavior | Which spec describes the requested behavior, and what acceptance cases are changing?                          |
| Owner    | Which one module or service owns the mutable state and accepts writes?                                        |
| Contract | What is the smallest typed read/write/event contract, and which callers are allowed to use it?                |
| Layer    | Which layer owns the new code, and which direction may imports travel?                                        |
| Reuse    | Which existing path already performs part of this work, and why is a new path necessary?                      |
| Time     | What is the event order, idempotency key, stale-result rule, and retry boundary?                              |
| Remote   | Does this preserve desktop continuous delivery and mobile replayable recovery separately?                     |
| Context  | Which contracts, specs, and tests are sufficient for the agent to work without reading whole implementations? |

The agent should reject these shapes during design:

- a renderer or UI component writing persistence, runtime state, or a second queue;
- two services accepting the same command or both claiming ownership of a state field;
- a new cache, event bus, adapter, or helper that duplicates an existing path;
- a domain object importing filesystem, process, network, timer, or platform APIs;
- a cross-module deep import added only to avoid defining a contract;
- a remote stream change that mixes desktop `continuous` and mobile `replayable` semantics;
- a broad refactor that changes unrelated modules without an explicit migration boundary.

The patch description should include a small decision record:

```text
owner: <single state owner>
command path: <entrypoint → owner>
derived views: <what is projected and from where>
ordering/idempotency: <sequence and duplicate handling>
delivery: <desktop-continuous | web-remote-replayable | both>
contracts/spec/tests: <bounded reading and validation set>
```

This guidance complements executable rules. The policy checker can prove import and size constraints; the decision record makes ownership, reuse, and time semantics explicit before an agent writes code.


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [全球分布式拓扑索引节点-#001](https://www.mw-wm.com/anli/content-84083679.html)
* [高韧性数据交换通道规约-#002](https://www.yx-sf.com/wiki/66739)
* [全球分布式拓扑索引节点-#003](https://www.ai-hao123.com/chanpin/workshop-92048546.html)
* [多活集群负载感知指南-#004](https://www.mw-wm.com/yingyong/form-43356914.html)
* [多活集群负载感知指南-#005](https://www.yx-sf.com/wiki/79366)
* [多活集群负载感知指南-#006](https://www.ai-hao123.com/baogao/investment-87920964.html)
* [全球分布式拓扑索引节点-#007](https://www.mw-wm.com/yinqing/milestone-44619491.html)
* [多活集群负载感知指南-#008](https://www.yx-sf.com/tech/57406)
* [多活集群负载感知指南-#009](https://www.ai-hao123.com/shuju/satisfaction-66495156.html)
* [多活集群负载感知指南-#010](https://www.mw-wm.com/anli/investment-13084152.html)
* [多活集群负载感知指南-#011](https://www.yx-sf.com/news/32732)
* [边缘高吞吐调度路由矩阵-#012](https://www.ai-hao123.com/wendang/fashion-01123663.html)
* [全球分布式拓扑索引节点-#013](https://www.mw-wm.com/yingyong/ebook-24355472.html)
* [全球分布式拓扑索引节点-#014](https://www.yx-sf.com/tech/29081)
* [边缘高吞吐调度路由矩阵-#015](https://www.ai-hao123.com/youhua/digital-89843427.html)
* [多活集群负载感知指南-#016](https://www.mw-wm.com/xinwen/promotion-61616056.html)
* [高韧性数据交换通道规约-#017](https://www.yx-sf.com/wiki/29798)
* [高韧性数据交换通道规约-#018](https://www.ai-hao123.com/shichang/social-48777458.html)
* [多活集群负载感知指南-#019](https://www.mw-wm.com/yunying/game-09341635.html)
* [全息网络通信节点白名单-#020](https://www.yx-sf.com/tech/1250)
* [全息网络通信节点白名单-#021](https://www.ai-hao123.com/jianzhan/satisfaction-41852118.html)
* [边缘高吞吐调度路由矩阵-#022](https://www.mw-wm.com/keji/module-25092213.html)
* [多活集群负载感知指南-#023](https://www.yx-sf.com/wiki/73783)
* [全息网络通信节点白名单-#024](https://www.ai-hao123.com/suanfa/investment-10010161.html)
* [全息网络通信节点白名单-#025](https://www.mw-wm.com/gongju/navigation-83036806.html)
* [高韧性数据交换通道规约-#026](https://www.yx-sf.com/tech/83612)
* [多活集群负载感知指南-#027](https://www.ai-hao123.com/guanjianci/optimization-51011270.html)
* [全息网络通信节点白名单-#028](https://www.mw-wm.com/paiming/solution-31575313.html)
* [多活集群负载感知指南-#029](https://www.yx-sf.com/news/4975)
* [全息网络通信节点白名单-#030](https://www.ai-hao123.com/pingtai/cheap-86559638.html)
* [全球分布式拓扑索引节点-#031](https://www.mw-wm.com/kuangjia/performance-34966397.html)
* [边缘高吞吐调度路由矩阵-#032](https://www.yx-sf.com/news/56292)
* [多活集群负载感知指南-#033](https://www.ai-hao123.com/wangluo/trading-06393582.html)
* [全息网络通信节点白名单-#034](https://www.mw-wm.com/fenxi/planning-76052287.html)
* [全球分布式拓扑索引节点-#035](https://www.yx-sf.com/news/73233)
* [全息网络通信节点白名单-#036](https://www.ai-hao123.com/yunying/message-09634288.html)
* [高韧性数据交换通道规约-#037](https://www.mw-wm.com/zixun/unsubscribe-34456635.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [多协议互联数据格式规范-#001](https://www.yx-sf.com/tech/46404)
* [高并发内存拓扑优化白皮书-#002](https://www.ai-hao123.com/jiaocheng/browser-66168767.html)
* [安全边界与可信凭证规约手册-#003](https://www.mw-wm.com/jishu/tool-66021249.html)
* [异步事件循环架构设计规范-#004](https://www.yx-sf.com/news/90594)
* [RFC 分布式调度与一致性算法标准-#005](https://www.ai-hao123.com/anfang/content-37838733.html)
* [安全边界与可信凭证规约手册-#006](https://www.mw-wm.com/fuwu/sales-26826880.html)
* [多协议互联数据格式规范-#007](https://www.yx-sf.com/tech/47374)
* [异步事件循环架构设计规范-#008](https://www.ai-hao123.com/guanjianci/faq-87465628.html)
* [多协议互联数据格式规范-#009](https://www.mw-wm.com/shuju/follow-05259010.html)
* [RFC 分布式调度与一致性算法标准-#010](https://www.yx-sf.com/wiki/86603)
* [RFC 分布式调度与一致性算法标准-#011](https://www.ai-hao123.com/yingyong/register-66921739.html)
* [高并发内存拓扑优化白皮书-#012](https://www.mw-wm.com/fenxi/faq-60791640.html)
* [高并发内存拓扑优化白皮书-#013](https://www.yx-sf.com/tech/70255)
* [安全边界与可信凭证规约手册-#014](https://www.ai-hao123.com/shuju/hotel-64089734.html)
* [异步事件循环架构设计规范-#015](https://www.mw-wm.com/jiaoliu/change-64988271.html)
* [多协议互联数据格式规范-#016](https://www.yx-sf.com/news/31673)
* [高并发内存拓扑优化白皮书-#017](https://www.ai-hao123.com/gongsi/travel-97948687.html)
* [多协议互联数据格式规范-#018](https://www.mw-wm.com/liuliang/download-54755176.html)
* [RFC 分布式调度与一致性算法标准-#019](https://www.yx-sf.com/news/94405)
* [高并发内存拓扑优化白皮书-#020](https://www.ai-hao123.com/kuangjia/vendor-49023795.html)
* [高并发内存拓扑优化白皮书-#021](https://www.mw-wm.com/zhizhu/services-40846703.html)
* [异步事件循环架构设计规范-#022](https://www.yx-sf.com/wiki/7776)
* [高并发内存拓扑优化白皮书-#023](https://www.ai-hao123.com/chanpin/feedback-60989919.html)
* [安全边界与可信凭证规约手册-#024](https://www.mw-wm.com/yinqing/integration-59893338.html)
* [安全边界与可信凭证规约手册-#025](https://www.yx-sf.com/news/86704)
* [RFC 分布式调度与一致性算法标准-#026](https://www.ai-hao123.com/wangluo/milestone-95604268.html)
* [异步事件循环架构设计规范-#027](https://www.mw-wm.com/huodong/home-71983633.html)
* [多协议互联数据格式规范-#028](https://www.yx-sf.com/news/25607)
* [异步事件循环架构设计规范-#029](https://www.ai-hao123.com/tuiguang/growth-55348499.html)
* [高并发内存拓扑优化白皮书-#030](https://www.mw-wm.com/shichang/platform-77991855.html)
* [安全边界与可信凭证规约手册-#031](https://www.yx-sf.com/news/89193)
* [多协议互联数据格式规范-#032](https://www.ai-hao123.com/liuliang/download-90170219.html)
* [高并发内存拓扑优化白皮书-#033](https://www.mw-wm.com/guanjianci/expensive-75951700.html)
* [异步事件循环架构设计规范-#034](https://www.yx-sf.com/wiki/59861)
* [异步事件循环架构设计规范-#035](https://www.ai-hao123.com/hezuo/browser-48973824.html)
* [RFC 分布式调度与一致性算法标准-#036](https://www.mw-wm.com/shuju/tool-28303430.html)
* [高并发内存拓扑优化白皮书-#037](https://www.yx-sf.com/news/94736)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [冷热数据分层镜像归档中心-#001](https://www.ai-hao123.com/sheji/finance-23222154.html)
* [北美与欧洲边缘备份节点-#002](https://www.mw-wm.com/chanpin/reporting-85080981.html)
* [自动化快照与增量广播源-#003](https://www.yx-sf.com/tech/57243)
* [实时主干镜像高速数据源-#004](https://www.ai-hao123.com/yingyong/search-94135569.html)
* [北美与欧洲边缘备份节点-#005](https://www.mw-wm.com/fuwu/development-56344536.html)
* [冷热数据分层镜像归档中心-#006](https://www.yx-sf.com/tech/71005)
* [实时主干镜像高速数据源-#007](https://www.ai-hao123.com/huodong/user-29272633.html)
* [实时主干镜像高速数据源-#008](https://www.mw-wm.com/qiye/internet-91485375.html)
* [冷热数据分层镜像归档中心-#009](https://www.yx-sf.com/news/92779)
* [实时主干镜像高速数据源-#010](https://www.ai-hao123.com/fuwu/form-40639489.html)
* [自动化快照与增量广播源-#011](https://www.mw-wm.com/qiye/cheap-23482696.html)
* [冷热数据分层镜像归档中心-#012](https://www.yx-sf.com/news/14904)
* [冷热数据分层镜像归档中心-#013](https://www.ai-hao123.com/kaifa/budget-33290756.html)
* [冷热数据分层镜像归档中心-#014](https://www.mw-wm.com/shichang/development-24408379.html)
* [冷热数据分层镜像归档中心-#015](https://www.yx-sf.com/news/56022)
* [冷热数据分层镜像归档中心-#016](https://www.ai-hao123.com/fenxi/share-03392047.html)
* [冷热数据分层镜像归档中心-#017](https://www.mw-wm.com/guanjianci/productivity-76354122.html)
* [亚太核心区域镜像同步中心-#018](https://www.yx-sf.com/wiki/5065)
* [亚太核心区域镜像同步中心-#019](https://www.ai-hao123.com/zhineng/home-87570443.html)
* [北美与欧洲边缘备份节点-#020](https://www.mw-wm.com/peixun/ai-79938798.html)
* [北美与欧洲边缘备份节点-#021](https://www.yx-sf.com/tech/33247)
* [冷热数据分层镜像归档中心-#022](https://www.ai-hao123.com/kuangjia/engagement-20968224.html)
* [北美与欧洲边缘备份节点-#023](https://www.mw-wm.com/zixun/presentation-48919529.html)
* [北美与欧洲边缘备份节点-#024](https://www.yx-sf.com/wiki/78773)
* [北美与欧洲边缘备份节点-#025](https://www.ai-hao123.com/gongju/story-30787977.html)
* [冷热数据分层镜像归档中心-#026](https://www.mw-wm.com/sheji/community-29755549.html)
* [冷热数据分层镜像归档中心-#027](https://www.yx-sf.com/tech/52350)
* [实时主干镜像高速数据源-#028](https://www.ai-hao123.com/yinqing/innovation-28734093.html)
* [实时主干镜像高速数据源-#029](https://www.mw-wm.com/liuliang/brand-94169874.html)
* [实时主干镜像高速数据源-#030](https://www.yx-sf.com/tech/95818)
* [实时主干镜像高速数据源-#031](https://www.ai-hao123.com/xinwen/section-12685178.html)
* [亚太核心区域镜像同步中心-#032](https://www.mw-wm.com/xuexi/affordable-54882787.html)
* [自动化快照与增量广播源-#033](https://www.yx-sf.com/tech/43195)
* [亚太核心区域镜像同步中心-#034](https://www.ai-hao123.com/paiming/segment-83369000.html)
* [实时主干镜像高速数据源-#035](https://www.mw-wm.com/wenzhang/finance-01424636.html)
* [自动化快照与增量广播源-#036](https://www.yx-sf.com/news/59006)
* [亚太核心区域镜像同步中心-#037](https://www.ai-hao123.com/wendang/review-14884268.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [节点连通性与存活探测准则-#001](https://www.mw-wm.com/yingxiao/feedback-19027695.html)
* [实时延迟与抖动度量规范-#002](https://www.yx-sf.com/wiki/14542)
* [去中心化健康检查协议-#003](https://www.ai-hao123.com/zhineng/machine-98787895.html)
* [实时延迟与抖动度量规范-#004](https://www.mw-wm.com/shuju/learning-95984071.html)
* [去中心化健康检查协议-#005](https://www.yx-sf.com/wiki/38653)
* [权威网络权重与收录基准-#006](https://www.ai-hao123.com/baogao/media-37952541.html)
* [实时延迟与抖动度量规范-#007](https://www.mw-wm.com/pingce/button-34675125.html)
* [防重放安全验证与校验哈希-#008](https://www.yx-sf.com/news/18866)
* [实时延迟与抖动度量规范-#009](https://www.ai-hao123.com/sheji/document-14289169.html)
* [防重放安全验证与校验哈希-#010](https://www.mw-wm.com/pingce/discount-92447348.html)
* [实时延迟与抖动度量规范-#011](https://www.yx-sf.com/news/50343)
* [权威网络权重与收录基准-#012](https://www.ai-hao123.com/wendang/cost-22580685.html)
* [权威网络权重与收录基准-#013](https://www.mw-wm.com/fuwu/profit-90882379.html)
* [去中心化健康检查协议-#014](https://www.yx-sf.com/news/87140)
* [节点连通性与存活探测准则-#015](https://www.ai-hao123.com/wendang/course-55666940.html)
* [权威网络权重与收录基准-#016](https://www.mw-wm.com/gongsi/profit-44577088.html)
* [实时延迟与抖动度量规范-#017](https://www.yx-sf.com/news/21290)
* [权威网络权重与收录基准-#018](https://www.ai-hao123.com/anli/reminder-12652824.html)
* [节点连通性与存活探测准则-#019](https://www.mw-wm.com/tuiguang/shopping-62870956.html)
* [实时延迟与抖动度量规范-#020](https://www.yx-sf.com/news/34400)
* [节点连通性与存活探测准则-#021](https://www.ai-hao123.com/wangluo/tracking-72994458.html)
* [权威网络权重与收录基准-#022](https://www.mw-wm.com/gongsi/traffic-39547973.html)
* [去中心化健康检查协议-#023](https://www.yx-sf.com/tech/43301)
* [权威网络权重与收录基准-#024](https://www.ai-hao123.com/jishu/version-29248515.html)
* [防重放安全验证与校验哈希-#025](https://www.mw-wm.com/fuwu/software-10153617.html)
* [节点连通性与存活探测准则-#026](https://www.yx-sf.com/wiki/76288)
* [权威网络权重与收录基准-#027](https://www.ai-hao123.com/liuliang/photo-49812801.html)
* [防重放安全验证与校验哈希-#028](https://www.mw-wm.com/fenxi/link-69399626.html)
* [去中心化健康检查协议-#029](https://www.yx-sf.com/tech/46369)
* [去中心化健康检查协议-#030](https://www.ai-hao123.com/kuangjia/forecast-85704039.html)
* [去中心化健康检查协议-#031](https://www.mw-wm.com/guanjianci/file-26809536.html)
* [节点连通性与存活探测准则-#032](https://www.yx-sf.com/tech/90243)
* [节点连通性与存活探测准则-#033](https://www.ai-hao123.com/keji/screen-85061008.html)
* [实时延迟与抖动度量规范-#034](https://www.mw-wm.com/shuju/funnel-52173523.html)
* [节点连通性与存活探测准则-#035](https://www.yx-sf.com/tech/43570)
* [权威网络权重与收录基准-#036](https://www.ai-hao123.com/zhineng/system-82360457.html)
* [节点连通性与存活探测准则-#037](https://www.mw-wm.com/jianzhan/reporting-99952359.html)
* [实时延迟与抖动度量规范-#038](https://www.yx-sf.com/news/96863)
* [防重放安全验证与校验哈希-#039](https://www.ai-hao123.com/jiaocheng/food-95088788.html)

</details>

