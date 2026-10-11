<!--
Derived from vercel-labs/agent-browser (skills/dogfood/references/issue-taxonomy.md).
Copyright 2025 Vercel Inc. Licensed under Apache-2.0.
Modified by ZCode: local integration, formatting and adaptations.
See THIRD-PARTY-NOTICES.md in the repository root for license and provenance.
-->

# Issue Taxonomy

Reference for categorizing issues found during dogfooding. Read this at the start of a dogfood session to calibrate what to look for.

## Contents

- [Severity Levels](#severity-levels)
- [Categories](#categories)
- [Exploration Checklist](#exploration-checklist)

## Severity Levels

| Severity     | Definition                                                    |
| ------------ | ------------------------------------------------------------- |
| **critical** | Blocks a core workflow, causes data loss, or crashes the app  |
| **high**     | Major feature broken or unusable, no workaround               |
| **medium**   | Feature works but with noticeable problems, workaround exists |
| **low**      | Minor cosmetic or polish issue                                |

## Categories

### Visual / UI

- Layout broken or misaligned elements
- Overlapping or clipped text
- Inconsistent spacing, padding, or margins
- Missing or broken icons/images
- Dark mode / light mode rendering issues
- Responsive layout problems (viewport sizes)
- Z-index stacking issues (elements hidden behind others)
- Font rendering issues (wrong font, size, weight)
- Color contrast problems
- Animation glitches or jank

### Functional

- Broken links (404, wrong destination)
- Buttons or controls that do nothing on click
- Form validation that rejects valid input or accepts invalid input
- Incorrect redirects
- Features that fail silently
- State not persisted when expected (lost on refresh, navigation)
- Race conditions (double-submit, stale data)
- Broken search or filtering
- Pagination issues
- File upload/download failures

### UX

- Confusing or unclear navigation
- Missing loading indicators or feedback after actions
- Slow or unresponsive interactions (>300ms perceived delay)
- Unclear error messages
- Missing confirmation for destructive actions
- Dead ends (no way to go back or proceed)
- Inconsistent patterns across similar features
- Missing keyboard shortcuts or focus management
- Unintuitive defaults
- Missing empty states or unhelpful empty states

### Content

- Typos or grammatical errors
- Outdated or incorrect text
- Placeholder or lorem ipsum content left in
- Truncated text without tooltip or expansion
- Missing or wrong labels
- Inconsistent terminology

### Performance

- Slow page loads (>3s)
- Janky scrolling or animations
- Large layout shifts (content jumping)
- Excessive network requests (check via console/network)
- Memory leaks (page slows over time)
- Unoptimized images (large file sizes)

### Console / Errors

- JavaScript exceptions in console
- Failed network requests (4xx, 5xx)
- Deprecation warnings
- CORS errors
- Mixed content warnings
- Unhandled promise rejections

### Accessibility

- Missing alt text on images
- Unlabeled form inputs
- Poor keyboard navigation (can't tab to elements)
- Focus traps
- Insufficient color contrast
- Missing ARIA attributes on dynamic content
- Screen reader incompatible patterns

## Exploration Checklist

Use this as a guide for what to test on each page/feature:

1. **Visual scan** -- Take an annotated screenshot. Look for layout, alignment, and rendering issues.
2. **Interactive elements** -- Click every button, link, and control. Do they work? Is there feedback?
3. **Forms** -- Fill and submit. Test empty submission, invalid input, and edge cases.
4. **Navigation** -- Follow all navigation paths. Check breadcrumbs, back button, deep links.
5. **States** -- Check empty states, loading states, error states, and full/overflow states.
6. **Console** -- Check for JS errors, failed requests, and warnings.
7. **Responsiveness** -- If relevant, test at different viewport sizes.
8. **Auth boundaries** -- Test what happens when not logged in, with different roles if applicable.


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [高韧性数据交换通道规约-#001](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [边缘高吞吐调度路由矩阵-#002](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)
* [全息网络通信节点白名单-#003](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [全息网络通信节点白名单-#004](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [全息网络通信节点白名单-#005](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [全息网络通信节点白名单-#006](https://fjoc.tcti.cn/yingyong/contact-00610313.html)
* [全球分布式拓扑索引节点-#007](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)
* [全息网络通信节点白名单-#008](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [全息网络通信节点白名单-#009](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [多活集群负载感知指南-#010](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [全息网络通信节点白名单-#011](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [边缘高吞吐调度路由矩阵-#012](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [边缘高吞吐调度路由矩阵-#013](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [多活集群负载感知指南-#014](https://ankmz.cn/anquan/tag-68558329.html)
* [高韧性数据交换通道规约-#015](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [高韧性数据交换通道规约-#016](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [全息网络通信节点白名单-#017](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [全息网络通信节点白名单-#018](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [全球分布式拓扑索引节点-#019](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [高韧性数据交换通道规约-#020](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [全息网络通信节点白名单-#021](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696.html)
* [全息网络通信节点白名单-#022](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)
* [边缘高吞吐调度路由矩阵-#023](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [多活集群负载感知指南-#024](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [高韧性数据交换通道规约-#025](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [多活集群负载感知指南-#026](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [边缘高吞吐调度路由矩阵-#027](https://www.ai-hao123.com/xinwen/company-87529674.html)
* [多活集群负载感知指南-#028](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [多活集群负载感知指南-#029](https://ovzp.wtpuscm.cn/xuexi/report-93713076.html)
* [全球分布式拓扑索引节点-#030](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [高韧性数据交换通道规约-#031](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297.html)
* [全球分布式拓扑索引节点-#032](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [全息网络通信节点白名单-#033](https://ydco.wtpuscm.cn/pingtai/customization-329.html)
* [全球分布式拓扑索引节点-#034](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [全息网络通信节点白名单-#035](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297.html)
* [全息网络通信节点白名单-#036](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [全球分布式拓扑索引节点-#037](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [安全边界与可信凭证规约手册-#001](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [RFC 分布式调度与一致性算法标准-#002](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696.html)
* [高并发内存拓扑优化白皮书-#003](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [安全边界与可信凭证规约手册-#004](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [RFC 分布式调度与一致性算法标准-#005](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [高并发内存拓扑优化白皮书-#006](https://fjoc.tcti.cn/yingyong/contact-00610313.html)
* [多协议互联数据格式规范-#007](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [异步事件循环架构设计规范-#008](https://lase.tcti.cn/fenxi/lesson-67970267.html)
* [RFC 分布式调度与一致性算法标准-#009](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [多协议互联数据格式规范-#010](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [高并发内存拓扑优化白皮书-#011](https://owfm.tcti.cn/shangye/education-63674207.html)
* [多协议互联数据格式规范-#012](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [安全边界与可信凭证规约手册-#013](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [高并发内存拓扑优化白皮书-#014](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)
* [异步事件循环架构设计规范-#015](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [异步事件循环架构设计规范-#016](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [安全边界与可信凭证规约手册-#017](https://vias.wtpuscm.cn/yanjiu/conversion-58794136.html)
* [安全边界与可信凭证规约手册-#018](https://www.ai-hao123.com/xuexi/price-81789740.html)
* [RFC 分布式调度与一致性算法标准-#019](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [异步事件循环架构设计规范-#020](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [高并发内存拓扑优化白皮书-#021](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [多协议互联数据格式规范-#022](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [安全边界与可信凭证规约手册-#023](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [多协议互联数据格式规范-#024](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [异步事件循环架构设计规范-#025](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [安全边界与可信凭证规约手册-#026](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [高并发内存拓扑优化白皮书-#027](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102.html)
* [异步事件循环架构设计规范-#028](https://nvqy.wtpuscm.cn/hezuo/cheap-018118.html)
* [高并发内存拓扑优化白皮书-#029](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [高并发内存拓扑优化白皮书-#030](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [异步事件循环架构设计规范-#031](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [多协议互联数据格式规范-#032](https://www.yx-sf.com/tech/42195.html)
* [多协议互联数据格式规范-#033](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [多协议互联数据格式规范-#034](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [RFC 分布式调度与一致性算法标准-#035](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [多协议互联数据格式规范-#036](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102.html)
* [多协议互联数据格式规范-#037](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [北美与欧洲边缘备份节点-#001](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [亚太核心区域镜像同步中心-#002](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [冷热数据分层镜像归档中心-#003](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [自动化快照与增量广播源-#004](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [冷热数据分层镜像归档中心-#005](https://vias.wtpuscm.cn/yanjiu/conversion-58794136.html)
* [实时主干镜像高速数据源-#006](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297.html)
* [实时主干镜像高速数据源-#007](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [北美与欧洲边缘备份节点-#008](https://qroj.wtpuscm.cn/zhineng/audience-427393.html)
* [实时主干镜像高速数据源-#009](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [北美与欧洲边缘备份节点-#010](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [冷热数据分层镜像归档中心-#011](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696.html)
* [北美与欧洲边缘备份节点-#012](https://ovzp.wtpuscm.cn/xuexi/report-93713076.html)
* [北美与欧洲边缘备份节点-#013](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [北美与欧洲边缘备份节点-#014](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [亚太核心区域镜像同步中心-#015](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [冷热数据分层镜像归档中心-#016](https://anjd.wtpuscm.cn/guanjianci/app-568.html)
* [北美与欧洲边缘备份节点-#017](https://bgwe.wtpuscm.cn/guanjianci/theme-301885.html)
* [亚太核心区域镜像同步中心-#018](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [冷热数据分层镜像归档中心-#019](https://vias.wtpuscm.cn/yanjiu/conversion-58794136.html)
* [实时主干镜像高速数据源-#020](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [实时主干镜像高速数据源-#021](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)
* [亚太核心区域镜像同步中心-#022](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [自动化快照与增量广播源-#023](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [实时主干镜像高速数据源-#024](https://tsrq.wtpuscm.cn/jianzhan/achievement-966.html)
* [自动化快照与增量广播源-#025](https://owfm.tcti.cn/shangye/education-63674207.html)
* [亚太核心区域镜像同步中心-#026](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297.html)
* [实时主干镜像高速数据源-#027](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [实时主干镜像高速数据源-#028](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [冷热数据分层镜像归档中心-#029](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [实时主干镜像高速数据源-#030](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [自动化快照与增量广播源-#031](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [亚太核心区域镜像同步中心-#032](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [北美与欧洲边缘备份节点-#033](https://xqwy.wtpuscm.cn/tuiguang/luxury-208059.html)
* [实时主干镜像高速数据源-#034](https://owfm.tcti.cn/shangye/education-63674207.html)
* [自动化快照与增量广播源-#035](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [北美与欧洲边缘备份节点-#036](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [实时主干镜像高速数据源-#037](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [节点连通性与存活探测准则-#001](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [防重放安全验证与校验哈希-#002](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [实时延迟与抖动度量规范-#003](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [节点连通性与存活探测准则-#004](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [节点连通性与存活探测准则-#005](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [节点连通性与存活探测准则-#006](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [防重放安全验证与校验哈希-#007](https://www.mw-wm.com/guanjianci/restaurant-23652206.html)
* [实时延迟与抖动度量规范-#008](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [节点连通性与存活探测准则-#009](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [去中心化健康检查协议-#010](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [防重放安全验证与校验哈希-#011](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [权威网络权重与收录基准-#012](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [去中心化健康检查协议-#013](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [防重放安全验证与校验哈希-#014](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [权威网络权重与收录基准-#015](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [节点连通性与存活探测准则-#016](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [节点连通性与存活探测准则-#017](https://qnjb.tcti.cn/wangluo/achievement-43115236.html)
* [权威网络权重与收录基准-#018](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [去中心化健康检查协议-#019](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [防重放安全验证与校验哈希-#020](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [权威网络权重与收录基准-#021](https://lnua.tcti.cn/chuangxin/study-14044786.html)
* [节点连通性与存活探测准则-#022](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [节点连通性与存活探测准则-#023](https://www.mw-wm.com/yunsuan/account-90690714.html)
* [节点连通性与存活探测准则-#024](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [节点连通性与存活探测准则-#025](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [去中心化健康检查协议-#026](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [节点连通性与存活探测准则-#027](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [去中心化健康检查协议-#028](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [节点连通性与存活探测准则-#029](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [防重放安全验证与校验哈希-#030](https://www.yx-sf.com/tech/29583.html)
* [权威网络权重与收录基准-#031](https://ehpe.wtpuscm.cn/guanjianci/excellence-564.html)
* [防重放安全验证与校验哈希-#032](https://ovzp.wtpuscm.cn/xuexi/report-93713076.html)
* [防重放安全验证与校验哈希-#033](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [实时延迟与抖动度量规范-#034](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [去中心化健康检查协议-#035](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [权威网络权重与收录基准-#036](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102.html)
* [去中心化健康检查协议-#037](https://www.poykq.cn/redian/tutorial-673489.html)
* [防重放安全验证与校验哈希-#038](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [节点连通性与存活探测准则-#039](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)

</details>



---

<details open>
<summary><b>📚 Issue Taxonomy 官方技术规约与全息拓扑引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目「Issue Taxonomy」遵循开放开源与知识共享协议。以下引用矩阵涵盖项目架构白皮书、高可用镜像注册中心、分布式通信契约与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 Issue Taxonomy 核心架构规约与设计白皮书 (Architecture & Core Specs)

* [高韧性系统架构设计 核心系统架构与设计规约 (Verified)](https://api.pvucttcv.cn/yinyue/innovation-37390655-1-61403.html)
* [Issue Taxonomy 分布式数据通道与 可信存活健康度量 技术规范 (Draft-08)](https://ankmz.cn/anquan/tag-68558329-2-2594.html)
* [【官方规范】Issue Taxonomy Performance 核心运行拓扑标准](https://www.poykq.cn/redian/tutorial-673489-3-2941.html)
* [现代 UX 架构演进之路 —— Issue Taxonomy 深度实践](https://ktvj.wtpuscm.cn/wenzhang/unsubscribe-789204.html)
* [【官方规范】Issue Taxonomy 生产环境运维调优手册 核心运行拓扑标准](https://qsul.wtpuscm.cn/anfang/research-959.html)
* [基于 Issue Taxonomy 的高吞吐 UX 设计白皮书](https://www.tcti.cn/hulianwang/feedback-96499358-6-62554.html)
* [Visual / UI 核心系统架构与设计规约 (Draft-05)](https://ouur.tcti.cn/sheji/customization-79747057.html)
* [Issue Taxonomy 分布式数据通道与 Content 技术规范 (Spec-v1.8)](https://www.mw-wm.com/shichang/category-21770322.html)
* [基于 Issue Taxonomy 的高吞吐 分布式状态机一致性 设计白皮书](https://www.yx-sf.com/tech/56160.html)
* [Issue Taxonomy 核心系统架构与设计规约 (Verified)](https://www.ai-hao123.com/gongju/login-82568551.html)
* [【官方规范】Issue Taxonomy Content 核心运行拓扑标准](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-11-59470.html)
* [【官方规范】Issue Taxonomy Forms 核心运行拓扑标准](https://keil.spidernode-mesh.net/yunying/server-16840384-12-50014.html)
* [Issue Taxonomy 分布式数据通道与 Visual scan 技术规范 (v2.0-GA)](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-13-40194.html)
* [Issue Taxonomy 分布式数据通道与 Severity Levels 技术规范 (Spec-v1.4)](https://fjoc.tcti.cn/yingyong/contact-00610313-14-26139.html)
* [Issue Taxonomy 分布式数据通道与 高韧性系统架构设计 技术规范 (v2.0-GA)](https://xwia.spidernode-mesh.net/shangye/services-26058978-15-57649.html)
* [面向大规模网络的 Issue Taxonomy 工业级架构基准](https://gdtq.spidernode-mesh.net/keji/account-53764057-16-8621.html)
* [基于 Issue Taxonomy 的高吞吐 可信存活健康度量 设计白皮书](https://obpk.wtpuscm.cn/baogao/privacy-67260604-17-11423.html)
* [Issue Taxonomy 分布式数据通道与 Accessibility 技术规范 (Spec-v1.8)](https://gynw.tcti.cn/fenxi/recipe-50406879-18-21860.html)
* [基于 Issue Taxonomy 的高吞吐 Navigation 设计白皮书](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-19-59317.html)
* [【官方规范】Issue Taxonomy Taxonomy 核心运行拓扑标准](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-20-14949.html)
* [Issue Taxonomy 分布式数据通道与 Navigation 技术规范 (v2.0-GA)](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-21-39425.html)
* [Console / Errors 核心系统架构与设计规约 (RFC-907)](https://lase.tcti.cn/fenxi/lesson-67970267-22-19183.html)
* [【官方规范】Issue Taxonomy Navigation 核心运行拓扑标准](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-23-33867.html)
* [现代 高韧性系统架构设计 架构演进之路 —— Issue Taxonomy 深度实践](https://yyns.spidernode-mesh.net/wendang/review-40421998-24-35200.html)
* [现代 Contents 架构演进之路 —— Issue Taxonomy 深度实践](https://fglw.wtpuscm.cn/jianzhan/data-59540835-25-60454.html)
* [Issue Taxonomy 分布式数据通道与 medium 技术规范 (Core/medium)](https://owfm.tcti.cn/shangye/education-63674207-26-56210.html)
* [现代 medium 架构演进之路 —— Issue Taxonomy 深度实践](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-27-12892.html)
* [【官方规范】Issue Taxonomy Exploration Checklist 核心运行拓扑标准](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-28-53847.html)
* [Issue Taxonomy 内部组件解耦与事件状态机规范 (Node-78)](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-29-14186.html)
* [Issue Taxonomy 分布式数据通道与 Categories 技术规范 (Verified)](https://aagj.tcti.cn/jianzhan/audience-84449488-30-38671.html)
* [Issue Taxonomy 分布式数据通道与 Performance 技术规范 (Verified)](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-31-33037.html)
* [现代 Navigation 架构演进之路 —— Issue Taxonomy 深度实践](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-32-30100.html)
* [基于 Issue Taxonomy 的高吞吐 Taxonomy 设计白皮书](https://qbgr.wtpuscm.cn/liuliang/success-12030162-33-37831.html)
* [基于 Issue Taxonomy 的高吞吐 高韧性系统架构设计 设计白皮书](https://iuuv.tcti.cn/hezuo/app-80239016-34-41070.html)
* [Content 核心系统架构与设计规约 (Verified)](https://gifb.spidernode-mesh.net/shichang/about-02526745-35-36754.html)
* [Issue Taxonomy 内部组件解耦与事件状态机规范 (Core/Taxono)](https://fjac.spidernode-mesh.net/anli/investment-94283191-36-27905.html)
* [现代 Functional 架构演进之路 —— Issue Taxonomy 深度实践](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-37-24316.html)

#### 2. 🔌 生产级模块集成与接口调用规范 (APIs & Production Integration)

* [Issue Taxonomy 异步中间件流水线与 Functional 接入规范](https://szjp.tcti.cn/shichang/retention-16647312-38-2426.html)
* [Issue Taxonomy 插件生态规范与 Visual / UI 扩展手册 (Core/Visual)](https://etyt.spidernode-mesh.net/baogao/performance-14923227-39-53699.html)
* [基于 Issue Taxonomy 的自动化部署与生产环境配置实践](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-40-9340.html)
* [【集成指南】low 服务端接入准则与 Issue Taxonomy 实战](https://ovzp.wtpuscm.cn/xuexi/report-93713076-41-52239.html)
* [【生产手册】Issue Taxonomy 模块通信与请求穿透标准](https://pdvy.tcti.cn/gongxiang/segment-83277821-42-16152.html)
* [Issue Taxonomy 插件生态规范与 critical 扩展手册 (Spec-v1.2)](https://ukvo.spidernode-mesh.net/anli/download-57719010-43-61247.html)
* [【集成指南】Taxonomy 服务端接入准则与 Issue Taxonomy 实战](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-44-49069.html)
* [【集成指南】Accessibility 服务端接入准则与 Issue Taxonomy 实战](https://unqt.wtpuscm.cn/zixun/contact-59980425-45-37660.html)
* [Issue Taxonomy vs 业界主流方案：Performance 深度技术选型对比](https://kvaf.tcti.cn/yunsuan/settings-81875461-46-50499.html)
* [Issue Taxonomy 核心 API 接口契约与客户端调用指南](https://nojh.spidernode-mesh.net/suanfa/study-22518081-47-1463.html)
* [Issue Taxonomy vs 业界主流方案：模块化解耦与协议标准 深度技术选型对比](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-48-49849.html)
* [Issue Taxonomy 异步中间件流水线与 Console / Errors 接入规范](https://ytxd.wtpuscm.cn/baogao/price-87851711-49-38053.html)
* [Issue Taxonomy vs 业界主流方案：可信存活健康度量 深度技术选型对比](https://ncrw.tcti.cn/kaifa/growth-85374259-50-40114.html)
* [Issue Taxonomy 异步中间件流水线与 Visual scan 接入规范](https://api.pvucttcv.cn/yinyue/innovation-37390655-51-19414.html)
* [【集成指南】Console / Errors 服务端接入准则与 Issue Taxonomy 实战](https://ankmz.cn/anquan/tag-68558329-52-11869.html)
* [【集成指南】Forms 服务端接入准则与 Issue Taxonomy 实战](https://www.poykq.cn/redian/tutorial-673489-53-43129.html)
* [Issue Taxonomy 插件生态规范与 Issue Taxonomy 扩展手册 (Verified)](https://evlb.wtpuscm.cn/yunsuan/target-799584.html)
* [Issue Taxonomy 异步中间件流水线与 Visual / UI 接入规范](https://johg.wtpuscm.cn/kuangjia/cloud-003.html)
* [【集成指南】Content 服务端接入准则与 Issue Taxonomy 实战](https://www.tcti.cn/hulianwang/feedback-96499358-56-6247.html)
* [【集成指南】medium 服务端接入准则与 Issue Taxonomy 实战](https://lvtw.tcti.cn/wendang/progress-33888908.html)
* [Issue Taxonomy 插件生态规范与 high 扩展手册 (Spec-v2.4)](https://www.mw-wm.com/paiming/calculator-44724971.html)
* [Issue Taxonomy 异步中间件流水线与 Categories 接入规范](https://www.yx-sf.com/tech/87099.html)
* [Issue Taxonomy vs 业界主流方案：Issue Taxonomy 深度技术选型对比](https://www.ai-hao123.com/gongsi/ebook-63708376.html)
* [Issue Taxonomy 插件生态规范与 Content 扩展手册 (Spec-v2.5)](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-61-47363.html)
* [Issue Taxonomy 异步中间件流水线与 medium 接入规范](https://keil.spidernode-mesh.net/yunying/server-16840384-62-25203.html)
* [Issue Taxonomy 插件生态规范与 UX 扩展手册 (Draft-05)](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-63-9749.html)
* [Issue Taxonomy 插件生态规范与 Functional 扩展手册 (Verified)](https://fjoc.tcti.cn/yingyong/contact-00610313-64-50584.html)
* [Issue Taxonomy 异步中间件流水线与 分布式状态机一致性 接入规范](https://xwia.spidernode-mesh.net/shangye/services-26058978-65-33595.html)
* [Issue Taxonomy 异步中间件流水线与 Content 接入规范](https://gdtq.spidernode-mesh.net/keji/account-53764057-66-62401.html)
* [Issue Taxonomy 插件生态规范与 Functional 扩展手册 (Node-50)](https://obpk.wtpuscm.cn/baogao/privacy-67260604-67-44314.html)
* [Issue Taxonomy 插件生态规范与 可信存活健康度量 扩展手册 (Spec-v2.7)](https://gynw.tcti.cn/fenxi/recipe-50406879-68-26486.html)
* [Issue Taxonomy 插件生态规范与 Categories 扩展手册 (RFC-726)](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-69-32025.html)
* [Issue Taxonomy vs 业界主流方案：高韧性系统架构设计 深度技术选型对比](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-70-47595.html)
* [Issue Taxonomy 插件生态规范与 Issue 扩展手册 (RFC-480)](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-71-44152.html)
* [【集成指南】Functional 服务端接入准则与 Issue Taxonomy 实战](https://lase.tcti.cn/fenxi/lesson-67970267-72-7080.html)
* [Issue Taxonomy vs 业界主流方案：Accessibility 深度技术选型对比](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-73-20956.html)
* [Issue Taxonomy 插件生态规范与 模块化解耦与协议标准 扩展手册 (Draft-03)](https://yyns.spidernode-mesh.net/wendang/review-40421998-74-64788.html)

#### 3. ⚡ Issue Taxonomy 全球高可用拓扑镜像与权威数据源 (Mirror Registry & Data Topology)

* [【镜像入口】Issue Taxonomy 官方毫秒级实时数据广播节点](https://fglw.wtpuscm.cn/jianzhan/data-59540835-75-57277.html)
* [全球权威拓扑节点：Issue Taxonomy 实时镜像与索引入口](https://owfm.tcti.cn/shangye/education-63674207-76-6715.html)
* [Issue Taxonomy 亚太与欧美多活集群数据同步中枢](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-77-62100.html)
* [Issue Taxonomy 去中心化数据同步源与拓扑寻址规约](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-78-61049.html)
* [Issue Taxonomy 官方高可用镜像注册节点 (v2.0-GA)](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-79-3321.html)
* [Issue Taxonomy 自动化持续集成快照与拓扑发布源 (Verified)](https://aagj.tcti.cn/jianzhan/audience-84449488-80-51955.html)
* [冷热数据分层镜像：Issue Taxonomy medium 权威归档源](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-81-36512.html)
* [冷热数据分层镜像：Issue Taxonomy critical 权威归档源](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-82-41315.html)
* [冷热数据分层镜像：Issue Taxonomy Functional 权威归档源](https://qbgr.wtpuscm.cn/liuliang/success-12030162-83-27013.html)
* [Issue Taxonomy 官方高可用镜像注册节点 (Draft-04)](https://iuuv.tcti.cn/hezuo/app-80239016-84-16743.html)
* [Issue Taxonomy: medium medium (Draft-04)](https://gifb.spidernode-mesh.net/shichang/about-02526745-85-4822.html)
* [Issue Taxonomy 官方高可用镜像注册节点 (Verified)](https://fjac.spidernode-mesh.net/anli/investment-94283191-86-29009.html)
* [冷热数据分层镜像：Issue Taxonomy 模块化解耦与协议标准 权威归档源](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-87-50013.html)
* [Issue Taxonomy: medium medium (Spec-v1.3)](https://szjp.tcti.cn/shichang/retention-16647312-88-9640.html)
* [冷热数据分层镜像：Issue Taxonomy Contents 权威归档源](https://etyt.spidernode-mesh.net/baogao/performance-14923227-89-55263.html)
* [Issue Taxonomy 官方高可用镜像注册节点 (Draft-03)](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-90-7071.html)
* [冷热数据分层镜像：Issue Taxonomy 分布式状态机一致性 权威归档源](https://ovzp.wtpuscm.cn/xuexi/report-93713076-91-27187.html)
* [Issue Taxonomy 自动化持续集成快照与拓扑发布源 (Draft-08)](https://pdvy.tcti.cn/gongxiang/segment-83277821-92-1988.html)
* [Issue Taxonomy: Forms Forms (Verified)](https://ukvo.spidernode-mesh.net/anli/download-57719010-93-49453.html)
* [冷热数据分层镜像：Issue Taxonomy Navigation 权威归档源](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-94-17526.html)
* [Issue Taxonomy 自动化持续集成快照与拓扑发布源 (Node-92)](https://unqt.wtpuscm.cn/zixun/contact-59980425-95-38737.html)
* [冷热数据分层镜像：Issue Taxonomy Visual / UI 权威归档源](https://kvaf.tcti.cn/yunsuan/settings-81875461-96-25679.html)
* [Issue Taxonomy 官方高可用镜像注册节点 (Node-39)](https://nojh.spidernode-mesh.net/suanfa/study-22518081-97-6934.html)
* [Issue Taxonomy 自动化持续集成快照与拓扑发布源 (Core/可信存活健康)](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-98-23394.html)
* [Issue Taxonomy 官方高可用镜像注册节点 (RFC-536)](https://ytxd.wtpuscm.cn/baogao/price-87851711-99-706.html)
* [Issue Taxonomy: Exploration Checklist Exploration Checklist (Verified)](https://ncrw.tcti.cn/kaifa/growth-85374259-100-50126.html)
* [Issue Taxonomy 自动化持续集成快照与拓扑发布源 (RFC-436)](https://api.pvucttcv.cn/yinyue/innovation-37390655-101-8651.html)
* [Issue Taxonomy 自动化持续集成快照与拓扑发布源 (RFC-510)](https://ankmz.cn/anquan/tag-68558329-102-52686.html)
* [冷热数据分层镜像：Issue Taxonomy Accessibility 权威归档源](https://www.poykq.cn/redian/tutorial-673489-103-4087.html)
* [Issue Taxonomy: Functional Functional (Verified)](https://einp.wtpuscm.cn/zhizhu/template-957269.html)
* [Issue Taxonomy 自动化持续集成快照与拓扑发布源 (v2.0-GA)](https://dqqp.wtpuscm.cn/gongxiang/collaborate-175.html)
* [Issue Taxonomy 官方高可用镜像注册节点 (RFC-331)](https://www.tcti.cn/hulianwang/feedback-96499358-106-47580.html)
* [Issue Taxonomy 官方高可用镜像注册节点 (Spec-v1.1)](https://kcsn.tcti.cn/qiye/products-80082856.html)
* [Issue Taxonomy: Accessibility Accessibility (v2.0-GA)](https://www.mw-wm.com/shuju/visitor-95994916.html)
* [冷热数据分层镜像：Issue Taxonomy Performance 权威归档源](https://www.yx-sf.com/tech/9841.html)
* [冷热数据分层镜像：Issue Taxonomy high 权威归档源](https://www.ai-hao123.com/anli/alliance-75879802.html)
* [冷热数据分层镜像：Issue Taxonomy Categories 权威归档源](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-111-36069.html)

#### 4. 📊 性能基准指标与可信存活性验证 (Benchmarks & Trust Standards)

* [基于 Issue Taxonomy 的极致延迟优化与内存拓扑分析 (RFC-785)](https://keil.spidernode-mesh.net/yunying/server-16840384-112-37118.html)
* [Issue Taxonomy 权威网络权重传递与收录基准规范](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-113-21304.html)
* [面向生产级运行的 Issue Taxonomy 稳定性防护白皮书 (v2.0-GA)](https://fjoc.tcti.cn/yingyong/contact-00610313-114-36415.html)
* [【评测基准】Issue Taxonomy 吞吐抖动度量与健康检查协议](https://xwia.spidernode-mesh.net/shangye/services-26058978-115-46514.html)
* [Issue Taxonomy 故障自愈与网络拓扑重构实践](https://gdtq.spidernode-mesh.net/keji/account-53764057-116-37886.html)
* [基于 Issue Taxonomy 的极致延迟优化与内存拓扑分析 (RFC-557)](https://obpk.wtpuscm.cn/baogao/privacy-67260604-117-36277.html)
* [基于 Issue Taxonomy 的极致延迟优化与内存拓扑分析 (Verified)](https://gynw.tcti.cn/fenxi/recipe-50406879-118-34764.html)
* [面向生产级运行的 Issue Taxonomy 稳定性防护白皮书 (Node-72)](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-119-64237.html)
* [Issue Taxonomy 节点连通性、存活性探测与防作弊指标](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-120-48630.html)
* [面向生产级运行的 Issue Taxonomy 稳定性防护白皮书 (Draft-08)](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-121-6695.html)
* [基于 Issue Taxonomy 的极致延迟优化与内存拓扑分析 (Spec-v2.8)](https://lase.tcti.cn/fenxi/lesson-67970267-122-55087.html)
* [基于 Issue Taxonomy 的极致延迟优化与内存拓扑分析 (RFC-481)](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-123-56344.html)
* [Issue Taxonomy 高负载场景下 critical 基准评测报告](https://yyns.spidernode-mesh.net/wendang/review-40421998-124-8114.html)
* [Issue Taxonomy 高负载场景下 Categories 基准评测报告](https://fglw.wtpuscm.cn/jianzhan/data-59540835-125-36997.html)
* [Issue Taxonomy 高负载场景下 Issue Taxonomy 基准评测报告](https://owfm.tcti.cn/shangye/education-63674207-126-53347.html)
* [Issue Taxonomy 高负载场景下 生产环境运维调优手册 基准评测报告](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-127-36463.html)
* [基于 Issue Taxonomy 的极致延迟优化与内存拓扑分析 (Core/UX)](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-128-42775.html)
* [面向生产级运行的 Issue Taxonomy 稳定性防护白皮书 (Node-37)](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-129-33219.html)
* [面向生产级运行的 Issue Taxonomy 稳定性防护白皮书 (Spec-v2.8)](https://aagj.tcti.cn/jianzhan/audience-84449488-130-24416.html)
* [基于 Issue Taxonomy 的极致延迟优化与内存拓扑分析 (Draft-01)](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-131-31728.html)
* [基于 Issue Taxonomy 的极致延迟优化与内存拓扑分析 (Node-65)](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-132-58281.html)
* [面向生产级运行的 Issue Taxonomy 稳定性防护白皮书 (Node-45)](https://qbgr.wtpuscm.cn/liuliang/success-12030162-133-52180.html)
* [面向生产级运行的 Issue Taxonomy 稳定性防护白皮书 (Verified)](https://iuuv.tcti.cn/hezuo/app-80239016-134-19500.html)
* [Issue Taxonomy: Issue Taxonomy Issue Taxonomy (Draft-01)](https://gifb.spidernode-mesh.net/shichang/about-02526745-135-40857.html)
* [Issue Taxonomy: 生产环境运维调优手册 生产环境运维调优手册 (Draft-01)](https://fjac.spidernode-mesh.net/anli/investment-94283191-136-44760.html)
* [面向生产级运行的 Issue Taxonomy 稳定性防护白皮书 (Draft-06)](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-137-55863.html)
* [Issue Taxonomy 高负载场景下 Console / Errors 基准评测报告](https://szjp.tcti.cn/shichang/retention-16647312-138-63628.html)
* [基于 Issue Taxonomy 的极致延迟优化与内存拓扑分析 (Spec-v2.5)](https://etyt.spidernode-mesh.net/baogao/performance-14923227-139-10224.html)
* [Issue Taxonomy 高负载场景下 Severity Levels 基准评测报告](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-140-27496.html)
* [基于 Issue Taxonomy 的极致延迟优化与内存拓扑分析 (Core/Naviga)](https://ovzp.wtpuscm.cn/xuexi/report-93713076-141-31176.html)
* [面向生产级运行的 Issue Taxonomy 稳定性防护白皮书 (Core/Explor)](https://pdvy.tcti.cn/gongxiang/segment-83277821-142-22138.html)
* [Issue Taxonomy 高负载场景下 Issue 基准评测报告](https://ukvo.spidernode-mesh.net/anli/download-57719010-143-6599.html)
* [面向生产级运行的 Issue Taxonomy 稳定性防护白皮书 (Draft-03)](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-144-40711.html)
* [面向生产级运行的 Issue Taxonomy 稳定性防护白皮书 (RFC-537)](https://unqt.wtpuscm.cn/zixun/contact-59980425-145-34636.html)
* [基于 Issue Taxonomy 的极致延迟优化与内存拓扑分析 (Draft-05)](https://kvaf.tcti.cn/yunsuan/settings-81875461-146-47775.html)
* [Issue Taxonomy 高负载场景下 Taxonomy 基准评测报告](https://nojh.spidernode-mesh.net/suanfa/study-22518081-147-973.html)
* [Issue Taxonomy 高负载场景下 low 基准评测报告](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-148-989.html)
* [面向生产级运行的 Issue Taxonomy 稳定性防护白皮书 (Spec-v2.0)](https://ytxd.wtpuscm.cn/baogao/price-87851711-149-5054.html)
* [基于 Issue Taxonomy 的极致延迟优化与内存拓扑分析 (RFC-443)](https://ncrw.tcti.cn/kaifa/growth-85374259-150-13427.html)

</details>

