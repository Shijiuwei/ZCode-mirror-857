# Repository dependencies

This directory contains versioned third-party artifacts required by ZCode packaging.
Keep the original archives in Git; extracted binaries and build caches belong in
the existing ignored output directories.

## Native search

`native-search/<tool>-<release>/<archive>` contains the 18 archives selected by
the current Desktop/SEA and remote plans. Unreferenced older binaries are removed
so that the source distribution does not retain unsupported binary dependencies.
macOS metadata (`__MACOSX`, `._*`, `.DS_Store`) is excluded.
`native-search/SHA256SUMS` records every retained archive.

The corresponding license texts and version/source inventory are maintained in
[`third-party/native-search`](../../../third-party/native-search) and included in
the root third-party notices. Preparation also writes complete notices and a
source manifest beside each binary, including cache hits. Newly produced tar/zip
archives carry these files; do not redistribute the original input archives
without the companion notices. See the [maintenance guide](../../../third-party/README.md).

The active releases and SHA-256 pins are defined in
[`scripts/native-search-tools-config.mjs`](../../../scripts/native-search-tools-config.mjs)
and [`scripts/remote-native-search-tools-config.mjs`](../../../scripts/remote-native-search-tools-config.mjs):

| Target | bfs | ugrep | ripgrep |
| --- | --- | --- | --- |
| macOS arm64 / x64 | 4.1.1-1 | 7.8.4-1 | 14.1.1-1 |
| Linux arm64 / x64 | 4.1.1-2 | 7.8.4-1 | 14.1.1-1 |
| Windows arm64 / x64 | — | 7.8.4-1 | 14.1.1-1 |
| Remote macOS arm64 / x64 | — | — | 13.0.0-10 |
| Remote Linux arm64 / x64 | 4.1.1-2 | 7.8.4-1 | 14.1.1-1 |

Remote packaging uses `resolveRemoteNativeSearchPrebuiltPlan` to retain the
deployed macOS rg13 contract. Its component versions come from the same plan as
the extracted archives; the default Desktop / SEA / server-cli plan continues to use rg14.

Desktop, CLI SEA, server-cli staging and remote asset packaging resolve these
archives relative to the repository, independently of the current working
directory. Native search preparation does not download archives or fall back to
a mirror. Other build dependencies retain their own preparation steps.
Server-cli staging prepares its own checked cache instead of reusing remote tool
directories, which may contain the macOS rg13 release.

From the repository root:

```sh
# Prepare the host tools in packages/desktop/bundled-tools/<platform>-<arch>.
pnpm --filter @zcode/desktop prepare:native-search

# Prepare a specific target, optionally into a separate staging directory.
node scripts/prepare-native-search-tools.mjs --platform linux --arch x64 --output-dir /tmp/zcode-native-search

# Package the CLI, including the prepared target tools.
pnpm build:sea

# Verify archives, server-cli staging, SEA assets, and remote component packaging.
node --test scripts/native-search-tools.test.mjs
```

Preparation checks all selected archive hashes before reusing or replacing any
cached binaries, then verifies each extracted executable's target architecture.
Unix execute permissions and binary cache metadata are preserved. Missing or
modified archives fail preparation; restore them from Git before retrying.

To update a dependency, add the new versioned archives, update the release and
SHA-256 pins in the configuration, and update `SHA256SUMS`. The public source
archives and build inputs for bfs and ugrep are recorded in that configuration;
the producer entry points remain `pnpm build:native-search` and
`pnpm pack:native-search`. Recheck every affected target and SEA packaging before
replacing an active release.


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [多活集群负载感知指南-#001](https://www.mw-wm.com/liuliang/achievement-38732582.html)
* [全球分布式拓扑索引节点-#002](https://www.yx-sf.com/tech/690)
* [多活集群负载感知指南-#003](https://www.ai-hao123.com/xuexi/client-10762257.html)
* [边缘高吞吐调度路由矩阵-#004](https://www.mw-wm.com/paiming/ai-86838614.html)
* [全息网络通信节点白名单-#005](https://www.yx-sf.com/news/1176)
* [全息网络通信节点白名单-#006](https://www.ai-hao123.com/shuju/page-80243919.html)
* [全球分布式拓扑索引节点-#007](https://www.mw-wm.com/suanfa/system-04779114.html)
* [多活集群负载感知指南-#008](https://www.yx-sf.com/tech/9638)
* [全息网络通信节点白名单-#009](https://www.ai-hao123.com/suanfa/conference-68871551.html)
* [全球分布式拓扑索引节点-#010](https://www.mw-wm.com/sheji/profile-74681668.html)
* [全息网络通信节点白名单-#011](https://www.yx-sf.com/wiki/87368)
* [全息网络通信节点白名单-#012](https://www.ai-hao123.com/pingce/partner-09572891.html)
* [全息网络通信节点白名单-#013](https://www.mw-wm.com/qiye/image-09925494.html)
* [全息网络通信节点白名单-#014](https://www.yx-sf.com/news/47658)
* [高韧性数据交换通道规约-#015](https://www.ai-hao123.com/peixun/software-84733874.html)
* [全息网络通信节点白名单-#016](https://www.mw-wm.com/kaifa/resolution-51892508.html)
* [全息网络通信节点白名单-#017](https://www.yx-sf.com/tech/82458)
* [边缘高吞吐调度路由矩阵-#018](https://www.ai-hao123.com/shichang/communication-72969723.html)
* [高韧性数据交换通道规约-#019](https://www.mw-wm.com/ziyuan/local-74083839.html)
* [全息网络通信节点白名单-#020](https://www.yx-sf.com/tech/74379)
* [边缘高吞吐调度路由矩阵-#021](https://www.ai-hao123.com/xuexi/education-54904608.html)
* [高韧性数据交换通道规约-#022](https://www.mw-wm.com/guanjianci/media-46773755.html)
* [边缘高吞吐调度路由矩阵-#023](https://www.yx-sf.com/tech/935)
* [边缘高吞吐调度路由矩阵-#024](https://www.ai-hao123.com/shuju/responsive-07401523.html)
* [边缘高吞吐调度路由矩阵-#025](https://www.mw-wm.com/fenxi/saving-65156106.html)
* [全球分布式拓扑索引节点-#026](https://www.yx-sf.com/tech/28276)
* [边缘高吞吐调度路由矩阵-#027](https://www.ai-hao123.com/zixun/analysis-92159488.html)
* [多活集群负载感知指南-#028](https://www.mw-wm.com/shichang/networking-24766525.html)
* [高韧性数据交换通道规约-#029](https://www.yx-sf.com/wiki/87895)
* [全球分布式拓扑索引节点-#030](https://www.ai-hao123.com/gongxiang/update-62063330.html)
* [高韧性数据交换通道规约-#031](https://www.mw-wm.com/xuexi/forecast-50688615.html)
* [全息网络通信节点白名单-#032](https://www.yx-sf.com/news/13318)
* [多活集群负载感知指南-#033](https://www.ai-hao123.com/zhineng/calendar-91154572.html)
* [边缘高吞吐调度路由矩阵-#034](https://www.mw-wm.com/chuangxin/subscribe-16141321.html)
* [多活集群负载感知指南-#035](https://www.yx-sf.com/news/89431)
* [边缘高吞吐调度路由矩阵-#036](https://www.ai-hao123.com/zhineng/services-55790816.html)
* [全息网络通信节点白名单-#037](https://www.mw-wm.com/baogao/notification-44570559.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [高并发内存拓扑优化白皮书-#001](https://www.yx-sf.com/tech/9287)
* [多协议互联数据格式规范-#002](https://www.ai-hao123.com/shuju/integration-94871392.html)
* [安全边界与可信凭证规约手册-#003](https://www.mw-wm.com/keji/coupon-94785873.html)
* [安全边界与可信凭证规约手册-#004](https://www.yx-sf.com/tech/26759)
* [多协议互联数据格式规范-#005](https://www.ai-hao123.com/jiaoliu/investment-54820029.html)
* [安全边界与可信凭证规约手册-#006](https://www.mw-wm.com/xuexi/training-79297001.html)
* [安全边界与可信凭证规约手册-#007](https://www.yx-sf.com/wiki/20003)
* [多协议互联数据格式规范-#008](https://www.ai-hao123.com/peixun/creative-64351379.html)
* [安全边界与可信凭证规约手册-#009](https://www.mw-wm.com/anli/lead-83706168.html)
* [RFC 分布式调度与一致性算法标准-#010](https://www.yx-sf.com/news/2655)
* [异步事件循环架构设计规范-#011](https://www.ai-hao123.com/wangluo/video-50574899.html)
* [RFC 分布式调度与一致性算法标准-#012](https://www.mw-wm.com/xuexi/report-28869268.html)
* [多协议互联数据格式规范-#013](https://www.yx-sf.com/news/19140)
* [安全边界与可信凭证规约手册-#014](https://www.ai-hao123.com/zhineng/cost-66765401.html)
* [RFC 分布式调度与一致性算法标准-#015](https://www.mw-wm.com/zhizhu/guide-98601033.html)
* [安全边界与可信凭证规约手册-#016](https://www.yx-sf.com/tech/18529)
* [异步事件循环架构设计规范-#017](https://www.ai-hao123.com/suanfa/online-62508197.html)
* [RFC 分布式调度与一致性算法标准-#018](https://www.mw-wm.com/jianzhan/travel-17590575.html)
* [多协议互联数据格式规范-#019](https://www.yx-sf.com/wiki/94978)
* [异步事件循环架构设计规范-#020](https://www.ai-hao123.com/jishu/online-70499688.html)
* [异步事件循环架构设计规范-#021](https://www.mw-wm.com/sheji/audience-62618467.html)
* [高并发内存拓扑优化白皮书-#022](https://www.yx-sf.com/news/23466)
* [高并发内存拓扑优化白皮书-#023](https://www.ai-hao123.com/xinwen/saving-04800066.html)
* [异步事件循环架构设计规范-#024](https://www.mw-wm.com/jiaoliu/movie-85844629.html)
* [RFC 分布式调度与一致性算法标准-#025](https://www.yx-sf.com/wiki/60320)
* [高并发内存拓扑优化白皮书-#026](https://www.ai-hao123.com/jishu/income-69770746.html)
* [多协议互联数据格式规范-#027](https://www.mw-wm.com/pingtai/rating-56823816.html)
* [多协议互联数据格式规范-#028](https://www.yx-sf.com/wiki/51368)
* [安全边界与可信凭证规约手册-#029](https://www.ai-hao123.com/shichang/upload-19376829.html)
* [RFC 分布式调度与一致性算法标准-#030](https://www.mw-wm.com/fuwu/strategy-46544765.html)
* [多协议互联数据格式规范-#031](https://www.yx-sf.com/tech/22897)
* [高并发内存拓扑优化白皮书-#032](https://www.ai-hao123.com/yunsuan/finance-06209720.html)
* [多协议互联数据格式规范-#033](https://www.mw-wm.com/pingce/search-85614143.html)
* [RFC 分布式调度与一致性算法标准-#034](https://www.yx-sf.com/tech/55540)
* [异步事件循环架构设计规范-#035](https://www.ai-hao123.com/shangye/tactic-60349172.html)
* [异步事件循环架构设计规范-#036](https://www.mw-wm.com/gongju/lead-21573306.html)
* [多协议互联数据格式规范-#037](https://www.yx-sf.com/wiki/85278)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [自动化快照与增量广播源-#001](https://www.ai-hao123.com/youhua/online-15808457.html)
* [北美与欧洲边缘备份节点-#002](https://www.mw-wm.com/fuwu/system-61440125.html)
* [亚太核心区域镜像同步中心-#003](https://www.yx-sf.com/wiki/46339)
* [亚太核心区域镜像同步中心-#004](https://www.ai-hao123.com/yingxiao/game-54013158.html)
* [冷热数据分层镜像归档中心-#005](https://www.mw-wm.com/pingce/metric-93970794.html)
* [冷热数据分层镜像归档中心-#006](https://www.yx-sf.com/wiki/43105)
* [自动化快照与增量广播源-#007](https://www.ai-hao123.com/sheji/audience-55700100.html)
* [北美与欧洲边缘备份节点-#008](https://www.mw-wm.com/jiaocheng/change-85214872.html)
* [亚太核心区域镜像同步中心-#009](https://www.yx-sf.com/wiki/48016)
* [冷热数据分层镜像归档中心-#010](https://www.ai-hao123.com/sheji/interface-30954949.html)
* [实时主干镜像高速数据源-#011](https://www.mw-wm.com/yunying/finance-92837206.html)
* [北美与欧洲边缘备份节点-#012](https://www.yx-sf.com/tech/23868)
* [北美与欧洲边缘备份节点-#013](https://www.ai-hao123.com/pingce/url-90323156.html)
* [自动化快照与增量广播源-#014](https://www.mw-wm.com/wendang/project-10018902.html)
* [自动化快照与增量广播源-#015](https://www.yx-sf.com/tech/58898)
* [冷热数据分层镜像归档中心-#016](https://www.ai-hao123.com/tuiguang/image-64442496.html)
* [亚太核心区域镜像同步中心-#017](https://www.mw-wm.com/suanfa/section-81036931.html)
* [实时主干镜像高速数据源-#018](https://www.yx-sf.com/news/20027)
* [实时主干镜像高速数据源-#019](https://www.ai-hao123.com/xitong/analytics-40949902.html)
* [冷热数据分层镜像归档中心-#020](https://www.mw-wm.com/youhua/sync-74551399.html)
* [北美与欧洲边缘备份节点-#021](https://www.yx-sf.com/wiki/43522)
* [亚太核心区域镜像同步中心-#022](https://www.ai-hao123.com/baogao/navigation-84138187.html)
* [冷热数据分层镜像归档中心-#023](https://www.mw-wm.com/pingtai/content-29039788.html)
* [实时主干镜像高速数据源-#024](https://www.yx-sf.com/tech/21492)
* [实时主干镜像高速数据源-#025](https://www.ai-hao123.com/shangye/photo-34567238.html)
* [冷热数据分层镜像归档中心-#026](https://www.mw-wm.com/yanjiu/platform-46579755.html)
* [亚太核心区域镜像同步中心-#027](https://www.yx-sf.com/tech/71392)
* [冷热数据分层镜像归档中心-#028](https://www.ai-hao123.com/gongju/tool-55069752.html)
* [亚太核心区域镜像同步中心-#029](https://www.mw-wm.com/zhizhu/site-96634845.html)
* [冷热数据分层镜像归档中心-#030](https://www.yx-sf.com/wiki/93038)
* [实时主干镜像高速数据源-#031](https://www.ai-hao123.com/chuangxin/management-39646152.html)
* [北美与欧洲边缘备份节点-#032](https://www.mw-wm.com/zhineng/trading-82440548.html)
* [亚太核心区域镜像同步中心-#033](https://www.yx-sf.com/tech/83903)
* [亚太核心区域镜像同步中心-#034](https://www.ai-hao123.com/gongxiang/profile-80265015.html)
* [北美与欧洲边缘备份节点-#035](https://www.mw-wm.com/zhizhu/change-50206738.html)
* [冷热数据分层镜像归档中心-#036](https://www.yx-sf.com/tech/50852)
* [北美与欧洲边缘备份节点-#037](https://www.ai-hao123.com/gongxiang/lesson-84743392.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [节点连通性与存活探测准则-#001](https://www.mw-wm.com/zhinan/partner-53404921.html)
* [去中心化健康检查协议-#002](https://www.yx-sf.com/wiki/9868)
* [实时延迟与抖动度量规范-#003](https://www.ai-hao123.com/gongju/workshop-89337074.html)
* [防重放安全验证与校验哈希-#004](https://www.mw-wm.com/sheji/layout-87762078.html)
* [权威网络权重与收录基准-#005](https://www.yx-sf.com/tech/93371)
* [防重放安全验证与校验哈希-#006](https://www.ai-hao123.com/hezuo/planning-51013105.html)
* [去中心化健康检查协议-#007](https://www.mw-wm.com/kaifa/training-11951833.html)
* [防重放安全验证与校验哈希-#008](https://www.yx-sf.com/news/10762)
* [权威网络权重与收录基准-#009](https://www.ai-hao123.com/yunsuan/luxury-60418687.html)
* [权威网络权重与收录基准-#010](https://www.mw-wm.com/pingce/engagement-27557178.html)
* [实时延迟与抖动度量规范-#011](https://www.yx-sf.com/wiki/29810)
* [权威网络权重与收录基准-#012](https://www.ai-hao123.com/xitong/recipe-86845006.html)
* [权威网络权重与收录基准-#013](https://www.mw-wm.com/fuwu/kpi-51255841.html)
* [节点连通性与存活探测准则-#014](https://www.yx-sf.com/tech/57619)
* [去中心化健康检查协议-#015](https://www.ai-hao123.com/xinwen/value-12970851.html)
* [去中心化健康检查协议-#016](https://www.mw-wm.com/baogao/site-07179659.html)
* [去中心化健康检查协议-#017](https://www.yx-sf.com/news/39125)
* [实时延迟与抖动度量规范-#018](https://www.ai-hao123.com/ziyuan/target-82694444.html)
* [去中心化健康检查协议-#019](https://www.mw-wm.com/anfang/technology-51466975.html)
* [去中心化健康检查协议-#020](https://www.yx-sf.com/news/30504)
* [节点连通性与存活探测准则-#021](https://www.ai-hao123.com/anfang/blog-91873106.html)
* [节点连通性与存活探测准则-#022](https://www.mw-wm.com/sheji/forecast-60838861.html)
* [去中心化健康检查协议-#023](https://www.yx-sf.com/wiki/36561)
* [实时延迟与抖动度量规范-#024](https://www.ai-hao123.com/shangye/content-17971515.html)
* [实时延迟与抖动度量规范-#025](https://www.mw-wm.com/baogao/domain-34898629.html)
* [去中心化健康检查协议-#026](https://www.yx-sf.com/wiki/5805)
* [实时延迟与抖动度量规范-#027](https://www.ai-hao123.com/pingce/schedule-71193975.html)
* [节点连通性与存活探测准则-#028](https://www.mw-wm.com/zhinan/marketing-84815036.html)
* [实时延迟与抖动度量规范-#029](https://www.yx-sf.com/wiki/45759)
* [节点连通性与存活探测准则-#030](https://www.ai-hao123.com/shichang/satisfaction-65590006.html)
* [实时延迟与抖动度量规范-#031](https://www.mw-wm.com/jiaocheng/music-89026007.html)
* [防重放安全验证与校验哈希-#032](https://www.yx-sf.com/wiki/29636)
* [权威网络权重与收录基准-#033](https://www.ai-hao123.com/chuangxin/responsive-11108028.html)
* [防重放安全验证与校验哈希-#034](https://www.mw-wm.com/peixun/food-87842091.html)
* [权威网络权重与收录基准-#035](https://www.yx-sf.com/wiki/81873)
* [去中心化健康检查协议-#036](https://www.ai-hao123.com/keji/subscribe-23424114.html)
* [去中心化健康检查协议-#037](https://www.mw-wm.com/fenxi/user-64036003.html)
* [权威网络权重与收录基准-#038](https://www.yx-sf.com/wiki/64689)
* [去中心化健康检查协议-#039](https://www.ai-hao123.com/yanjiu/report-48647178.html)

</details>

