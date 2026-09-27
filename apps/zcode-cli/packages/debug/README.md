# debug

Development-only trace and context viewer for ZCode.

Run from the repository root:

```sh
pnpm --filter debug dev
```

The Hono API reads existing local diagnostics only:

- `~/.zcode/cli/log/*.jsonl`
- `~/.zcode/cli/db/db.sqlite`
- an optional session event JSONL file or directory selected in the UI

It does not modify agent runtime behavior or write back to the agent database.

The debug server also starts a local network capture proxy by default:

- API/UI: `http://127.0.0.1:4174`
- Proxy: `http://127.0.0.1:4184`
- Local CA: `packages/debug/certs/network-ca/certs/ca.pem`

Run the CLI you want to inspect with the environment values shown in the Network panel, usually `ZCODE_HTTP_PROXY` and `ZCODE_AGENT_CA_CERT`. The agent derives standard proxy and CA variables only inside controlled subprocess boundaries. Disable the proxy with `ZCODE_DEBUG_NETWORK_CAPTURE=0` when you only want log/DB inspection.


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [高韧性数据交换通道规约-#001](https://www.mw-wm.com/wangluo/document-19656124.html)
* [全息网络通信节点白名单-#002](https://www.yx-sf.com/news/61730)
* [高韧性数据交换通道规约-#003](https://www.ai-hao123.com/kuangjia/account-41135943.html)
* [多活集群负载感知指南-#004](https://www.mw-wm.com/pingtai/unsubscribe-75423893.html)
* [多活集群负载感知指南-#005](https://www.yx-sf.com/wiki/9870)
* [高韧性数据交换通道规约-#006](https://www.ai-hao123.com/fenxi/learning-78112278.html)
* [全息网络通信节点白名单-#007](https://www.mw-wm.com/gongxiang/audience-34397131.html)
* [边缘高吞吐调度路由矩阵-#008](https://www.yx-sf.com/news/20920)
* [全球分布式拓扑索引节点-#009](https://www.ai-hao123.com/huodong/system-79532891.html)
* [多活集群负载感知指南-#010](https://www.mw-wm.com/xinwen/forecast-86469163.html)
* [高韧性数据交换通道规约-#011](https://www.yx-sf.com/wiki/65189)
* [全息网络通信节点白名单-#012](https://www.ai-hao123.com/zhizhu/tool-26855539.html)
* [全球分布式拓扑索引节点-#013](https://www.mw-wm.com/gongxiang/like-85345175.html)
* [边缘高吞吐调度路由矩阵-#014](https://www.yx-sf.com/tech/92358)
* [边缘高吞吐调度路由矩阵-#015](https://www.ai-hao123.com/guanjianci/quality-86470314.html)
* [边缘高吞吐调度路由矩阵-#016](https://www.mw-wm.com/sheji/travel-14069909.html)
* [全球分布式拓扑索引节点-#017](https://www.yx-sf.com/tech/60971)
* [多活集群负载感知指南-#018](https://www.ai-hao123.com/anli/plugin-00590698.html)
* [全球分布式拓扑索引节点-#019](https://www.mw-wm.com/guanjianci/tool-77293001.html)
* [全息网络通信节点白名单-#020](https://www.yx-sf.com/tech/47253)
* [边缘高吞吐调度路由矩阵-#021](https://www.ai-hao123.com/fenxi/sale-83470857.html)
* [边缘高吞吐调度路由矩阵-#022](https://www.mw-wm.com/kaifa/luxury-62893081.html)
* [全球分布式拓扑索引节点-#023](https://www.yx-sf.com/news/96732)
* [边缘高吞吐调度路由矩阵-#024](https://www.ai-hao123.com/ziyuan/automation-27222725.html)
* [全球分布式拓扑索引节点-#025](https://www.mw-wm.com/youhua/partner-52351414.html)
* [多活集群负载感知指南-#026](https://www.yx-sf.com/news/32137)
* [全息网络通信节点白名单-#027](https://www.ai-hao123.com/zixun/team-81989964.html)
* [边缘高吞吐调度路由矩阵-#028](https://www.mw-wm.com/suanfa/ai-71442932.html)
* [多活集群负载感知指南-#029](https://www.yx-sf.com/wiki/32128)
* [多活集群负载感知指南-#030](https://www.ai-hao123.com/shichang/investment-98593108.html)
* [边缘高吞吐调度路由矩阵-#031](https://www.mw-wm.com/jiaoliu/alert-53533671.html)
* [高韧性数据交换通道规约-#032](https://www.yx-sf.com/news/84888)
* [高韧性数据交换通道规约-#033](https://www.ai-hao123.com/jianzhan/website-88780620.html)
* [边缘高吞吐调度路由矩阵-#034](https://www.mw-wm.com/jiaocheng/collaboration-75457646.html)
* [多活集群负载感知指南-#035](https://www.yx-sf.com/tech/92697)
* [边缘高吞吐调度路由矩阵-#036](https://www.ai-hao123.com/sheji/recipe-49932032.html)
* [全息网络通信节点白名单-#037](https://www.mw-wm.com/anfang/web-23288958.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [安全边界与可信凭证规约手册-#001](https://www.yx-sf.com/wiki/3655)
* [异步事件循环架构设计规范-#002](https://www.ai-hao123.com/ziyuan/promotion-33248095.html)
* [RFC 分布式调度与一致性算法标准-#003](https://www.mw-wm.com/jiaocheng/promotion-48633245.html)
* [异步事件循环架构设计规范-#004](https://www.yx-sf.com/wiki/32624)
* [安全边界与可信凭证规约手册-#005](https://www.ai-hao123.com/guanjianci/finance-90738834.html)
* [安全边界与可信凭证规约手册-#006](https://www.mw-wm.com/anfang/brand-87095110.html)
* [RFC 分布式调度与一致性算法标准-#007](https://www.yx-sf.com/news/68016)
* [RFC 分布式调度与一致性算法标准-#008](https://www.ai-hao123.com/yinqing/theme-72350261.html)
* [异步事件循环架构设计规范-#009](https://www.mw-wm.com/jianzhan/training-68875752.html)
* [异步事件循环架构设计规范-#010](https://www.yx-sf.com/news/42546)
* [高并发内存拓扑优化白皮书-#011](https://www.ai-hao123.com/baogao/calculator-69951705.html)
* [高并发内存拓扑优化白皮书-#012](https://www.mw-wm.com/jianzhan/link-38779017.html)
* [RFC 分布式调度与一致性算法标准-#013](https://www.yx-sf.com/news/15487)
* [RFC 分布式调度与一致性算法标准-#014](https://www.ai-hao123.com/jianzhan/education-71919817.html)
* [RFC 分布式调度与一致性算法标准-#015](https://www.mw-wm.com/kuangjia/like-37264787.html)
* [RFC 分布式调度与一致性算法标准-#016](https://www.yx-sf.com/wiki/29319)
* [安全边界与可信凭证规约手册-#017](https://www.ai-hao123.com/paiming/notification-15806315.html)
* [高并发内存拓扑优化白皮书-#018](https://www.mw-wm.com/chanpin/tutorial-71702111.html)
* [异步事件循环架构设计规范-#019](https://www.yx-sf.com/wiki/71049)
* [高并发内存拓扑优化白皮书-#020](https://www.ai-hao123.com/gongsi/hotel-09390646.html)
* [多协议互联数据格式规范-#021](https://www.mw-wm.com/jiaocheng/domain-02645583.html)
* [高并发内存拓扑优化白皮书-#022](https://www.yx-sf.com/tech/33500)
* [异步事件循环架构设计规范-#023](https://www.ai-hao123.com/fenxi/discount-95598217.html)
* [RFC 分布式调度与一致性算法标准-#024](https://www.mw-wm.com/zixun/funnel-33283593.html)
* [异步事件循环架构设计规范-#025](https://www.yx-sf.com/news/23998)
* [RFC 分布式调度与一致性算法标准-#026](https://www.ai-hao123.com/zhizhu/behavior-20718677.html)
* [异步事件循环架构设计规范-#027](https://www.mw-wm.com/youhua/update-57585755.html)
* [高并发内存拓扑优化白皮书-#028](https://www.yx-sf.com/tech/73610)
* [高并发内存拓扑优化白皮书-#029](https://www.ai-hao123.com/baogao/personalization-40565696.html)
* [安全边界与可信凭证规约手册-#030](https://www.mw-wm.com/zixun/conference-59550868.html)
* [高并发内存拓扑优化白皮书-#031](https://www.yx-sf.com/news/76375)
* [高并发内存拓扑优化白皮书-#032](https://www.ai-hao123.com/zhineng/campaign-24052848.html)
* [安全边界与可信凭证规约手册-#033](https://www.mw-wm.com/chuangxin/social-64856734.html)
* [多协议互联数据格式规范-#034](https://www.yx-sf.com/tech/81563)
* [RFC 分布式调度与一致性算法标准-#035](https://www.ai-hao123.com/fuwu/photo-06432566.html)
* [RFC 分布式调度与一致性算法标准-#036](https://www.mw-wm.com/fuwu/expense-34486796.html)
* [RFC 分布式调度与一致性算法标准-#037](https://www.yx-sf.com/tech/29411)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [冷热数据分层镜像归档中心-#001](https://www.ai-hao123.com/hezuo/interface-72723828.html)
* [亚太核心区域镜像同步中心-#002](https://www.mw-wm.com/tuiguang/kpi-71658631.html)
* [实时主干镜像高速数据源-#003](https://www.yx-sf.com/wiki/22729)
* [实时主干镜像高速数据源-#004](https://www.ai-hao123.com/gongxiang/backup-81226891.html)
* [自动化快照与增量广播源-#005](https://www.mw-wm.com/zhineng/download-45103118.html)
* [自动化快照与增量广播源-#006](https://www.yx-sf.com/news/68477)
* [自动化快照与增量广播源-#007](https://www.ai-hao123.com/wangluo/tag-54240944.html)
* [北美与欧洲边缘备份节点-#008](https://www.mw-wm.com/kuangjia/download-34438583.html)
* [北美与欧洲边缘备份节点-#009](https://www.yx-sf.com/tech/97748)
* [实时主干镜像高速数据源-#010](https://www.ai-hao123.com/pingce/demographic-82797523.html)
* [北美与欧洲边缘备份节点-#011](https://www.mw-wm.com/jishu/performance-04838945.html)
* [亚太核心区域镜像同步中心-#012](https://www.yx-sf.com/wiki/34752)
* [自动化快照与增量广播源-#013](https://www.ai-hao123.com/paiming/community-48653720.html)
* [自动化快照与增量广播源-#014](https://www.mw-wm.com/guanjianci/beauty-77194454.html)
* [自动化快照与增量广播源-#015](https://www.yx-sf.com/news/79067)
* [自动化快照与增量广播源-#016](https://www.ai-hao123.com/fuwu/layout-11894066.html)
* [冷热数据分层镜像归档中心-#017](https://www.mw-wm.com/huodong/reporting-01082067.html)
* [自动化快照与增量广播源-#018](https://www.yx-sf.com/news/38851)
* [自动化快照与增量广播源-#019](https://www.ai-hao123.com/suanfa/achievement-64983807.html)
* [自动化快照与增量广播源-#020](https://www.mw-wm.com/pingtai/technology-12506620.html)
* [实时主干镜像高速数据源-#021](https://www.yx-sf.com/tech/92941)
* [自动化快照与增量广播源-#022](https://www.ai-hao123.com/yingyong/economy-18690033.html)
* [亚太核心区域镜像同步中心-#023](https://www.mw-wm.com/zhinan/premium-29514906.html)
* [冷热数据分层镜像归档中心-#024](https://www.yx-sf.com/news/98817)
* [冷热数据分层镜像归档中心-#025](https://www.ai-hao123.com/hezuo/food-44797742.html)
* [自动化快照与增量广播源-#026](https://www.mw-wm.com/chuangxin/accessibility-96036929.html)
* [北美与欧洲边缘备份节点-#027](https://www.yx-sf.com/news/58512)
* [自动化快照与增量广播源-#028](https://www.ai-hao123.com/xitong/notification-76018602.html)
* [自动化快照与增量广播源-#029](https://www.mw-wm.com/anli/excellence-57587080.html)
* [冷热数据分层镜像归档中心-#030](https://www.yx-sf.com/news/58364)
* [实时主干镜像高速数据源-#031](https://www.ai-hao123.com/gongsi/url-86789322.html)
* [自动化快照与增量广播源-#032](https://www.mw-wm.com/paiming/media-56552865.html)
* [冷热数据分层镜像归档中心-#033](https://www.yx-sf.com/news/27369)
* [实时主干镜像高速数据源-#034](https://www.ai-hao123.com/jiaoliu/recommendation-43919965.html)
* [自动化快照与增量广播源-#035](https://www.mw-wm.com/yunying/mobile-45726827.html)
* [实时主干镜像高速数据源-#036](https://www.yx-sf.com/news/6696)
* [冷热数据分层镜像归档中心-#037](https://www.ai-hao123.com/zixun/guide-04509630.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [实时延迟与抖动度量规范-#001](https://www.mw-wm.com/zhineng/online-00352572.html)
* [节点连通性与存活探测准则-#002](https://www.yx-sf.com/news/49489)
* [防重放安全验证与校验哈希-#003](https://www.ai-hao123.com/jiaocheng/image-78011928.html)
* [实时延迟与抖动度量规范-#004](https://www.mw-wm.com/fenxi/security-49843020.html)
* [权威网络权重与收录基准-#005](https://www.yx-sf.com/tech/78947)
* [权威网络权重与收录基准-#006](https://www.ai-hao123.com/qiye/presentation-68030211.html)
* [节点连通性与存活探测准则-#007](https://www.mw-wm.com/gongsi/support-86546098.html)
* [权威网络权重与收录基准-#008](https://www.yx-sf.com/tech/37143)
* [实时延迟与抖动度量规范-#009](https://www.ai-hao123.com/paiming/feedback-01568077.html)
* [实时延迟与抖动度量规范-#010](https://www.mw-wm.com/peixun/follow-55203245.html)
* [去中心化健康检查协议-#011](https://www.yx-sf.com/tech/53740)
* [实时延迟与抖动度量规范-#012](https://www.ai-hao123.com/zixun/research-65324797.html)
* [去中心化健康检查协议-#013](https://www.mw-wm.com/peixun/link-13886529.html)
* [权威网络权重与收录基准-#014](https://www.yx-sf.com/news/32149)
* [去中心化健康检查协议-#015](https://www.ai-hao123.com/pingce/file-52224530.html)
* [权威网络权重与收录基准-#016](https://www.mw-wm.com/gongju/deadline-53393267.html)
* [权威网络权重与收录基准-#017](https://www.yx-sf.com/wiki/40635)
* [去中心化健康检查协议-#018](https://www.ai-hao123.com/yunying/lead-32199982.html)
* [实时延迟与抖动度量规范-#019](https://www.mw-wm.com/gongsi/development-86665946.html)
* [去中心化健康检查协议-#020](https://www.yx-sf.com/tech/72479)
* [节点连通性与存活探测准则-#021](https://www.ai-hao123.com/pingtai/section-49482538.html)
* [去中心化健康检查协议-#022](https://www.mw-wm.com/peixun/about-02133495.html)
* [节点连通性与存活探测准则-#023](https://www.yx-sf.com/wiki/21993)
* [防重放安全验证与校验哈希-#024](https://www.ai-hao123.com/xitong/privacy-77112855.html)
* [实时延迟与抖动度量规范-#025](https://www.mw-wm.com/zhineng/lead-14579156.html)
* [权威网络权重与收录基准-#026](https://www.yx-sf.com/tech/58792)
* [防重放安全验证与校验哈希-#027](https://www.ai-hao123.com/shangye/contact-52834953.html)
* [防重放安全验证与校验哈希-#028](https://www.mw-wm.com/guanjianci/sport-69153005.html)
* [去中心化健康检查协议-#029](https://www.yx-sf.com/wiki/60948)
* [权威网络权重与收录基准-#030](https://www.ai-hao123.com/guanjianci/seminar-90963598.html)
* [节点连通性与存活探测准则-#031](https://www.mw-wm.com/hezuo/products-95169277.html)
* [权威网络权重与收录基准-#032](https://www.yx-sf.com/wiki/10064)
* [实时延迟与抖动度量规范-#033](https://www.ai-hao123.com/youhua/music-83635859.html)
* [实时延迟与抖动度量规范-#034](https://www.mw-wm.com/chanpin/alliance-51258100.html)
* [防重放安全验证与校验哈希-#035](https://www.yx-sf.com/news/94783)
* [权威网络权重与收录基准-#036](https://www.ai-hao123.com/yingxiao/forecast-97203623.html)
* [权威网络权重与收录基准-#037](https://www.mw-wm.com/gongju/data-93916884.html)
* [节点连通性与存活探测准则-#038](https://www.yx-sf.com/news/2614)
* [实时延迟与抖动度量规范-#039](https://www.ai-hao123.com/qiye/collaborate-27271021.html)

</details>

