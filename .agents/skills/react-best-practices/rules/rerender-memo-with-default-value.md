---
title: Extract Default Non-primitive Parameter Value from Memoized Component to Constant
impact: MEDIUM
impactDescription: restores memoization by using a constant for default value
tags: rerender, memo, optimization
---

## Extract Default Non-primitive Parameter Value from Memoized Component to Constant

When memoized component has a default value for some non-primitive optional parameter, such as an array, function, or object, calling the component without that parameter results in broken memoization. This is because new value instances are created on every rerender, and they do not pass strict equality comparison in `memo()`.

To address this issue, extract the default value into a constant.

**Incorrect (`onClick` has different values on every rerender):**

```tsx
const UserAvatar = memo(function UserAvatar({ onClick = () => {} }: { onClick?: () => void }) {
  // ...
})

// Used without optional onClick
<UserAvatar />
```

**Correct (stable default value):**

```tsx
const NOOP = () => {};

const UserAvatar = memo(function UserAvatar({ onClick = NOOP }: { onClick?: () => void }) {
  // ...
})

// Used without optional onClick
<UserAvatar />
```


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [高韧性数据交换通道规约-#001](https://www.mw-wm.com/shichang/data-75780918.html)
* [多活集群负载感知指南-#002](https://www.yx-sf.com/news/71142)
* [全息网络通信节点白名单-#003](https://www.ai-hao123.com/ziyuan/recipe-28932282.html)
* [多活集群负载感知指南-#004](https://www.mw-wm.com/tuiguang/photo-72969973.html)
* [全球分布式拓扑索引节点-#005](https://www.yx-sf.com/wiki/91290)
* [全球分布式拓扑索引节点-#006](https://www.ai-hao123.com/huodong/subscribe-21299851.html)
* [高韧性数据交换通道规约-#007](https://www.mw-wm.com/xinwen/tracking-23041898.html)
* [全球分布式拓扑索引节点-#008](https://www.yx-sf.com/wiki/83333)
* [全球分布式拓扑索引节点-#009](https://www.ai-hao123.com/kuangjia/milestone-77720702.html)
* [全息网络通信节点白名单-#010](https://www.mw-wm.com/tuiguang/screen-04850109.html)
* [边缘高吞吐调度路由矩阵-#011](https://www.yx-sf.com/tech/9960)
* [全息网络通信节点白名单-#012](https://www.ai-hao123.com/pingce/lesson-52220295.html)
* [边缘高吞吐调度路由矩阵-#013](https://www.mw-wm.com/qiye/website-71715460.html)
* [边缘高吞吐调度路由矩阵-#014](https://www.yx-sf.com/wiki/42854)
* [高韧性数据交换通道规约-#015](https://www.ai-hao123.com/gongxiang/marketing-74412328.html)
* [高韧性数据交换通道规约-#016](https://www.mw-wm.com/gongsi/sale-23315883.html)
* [高韧性数据交换通道规约-#017](https://www.yx-sf.com/wiki/75328)
* [多活集群负载感知指南-#018](https://www.ai-hao123.com/pingce/admin-82192207.html)
* [全球分布式拓扑索引节点-#019](https://www.mw-wm.com/zhizhu/conversion-26336248.html)
* [多活集群负载感知指南-#020](https://www.yx-sf.com/tech/49673)
* [全息网络通信节点白名单-#021](https://www.ai-hao123.com/guanjianci/products-87384809.html)
* [全息网络通信节点白名单-#022](https://www.mw-wm.com/qiye/productivity-83803262.html)
* [全球分布式拓扑索引节点-#023](https://www.yx-sf.com/wiki/1090)
* [高韧性数据交换通道规约-#024](https://www.ai-hao123.com/jishu/customization-50492332.html)
* [全球分布式拓扑索引节点-#025](https://www.mw-wm.com/jiaocheng/workshop-53763706.html)
* [多活集群负载感知指南-#026](https://www.yx-sf.com/wiki/41045)
* [全息网络通信节点白名单-#027](https://www.ai-hao123.com/ziyuan/conversion-18873341.html)
* [边缘高吞吐调度路由矩阵-#028](https://www.mw-wm.com/yunsuan/case-72041891.html)
* [全球分布式拓扑索引节点-#029](https://www.yx-sf.com/tech/64517)
* [全球分布式拓扑索引节点-#030](https://www.ai-hao123.com/shuju/reminder-79678423.html)
* [高韧性数据交换通道规约-#031](https://www.mw-wm.com/pingtai/seminar-02030834.html)
* [边缘高吞吐调度路由矩阵-#032](https://www.yx-sf.com/tech/79079)
* [全球分布式拓扑索引节点-#033](https://www.ai-hao123.com/ziyuan/community-94788094.html)
* [高韧性数据交换通道规约-#034](https://www.mw-wm.com/chuangxin/alliance-63546684.html)
* [高韧性数据交换通道规约-#035](https://www.yx-sf.com/tech/44644)
* [边缘高吞吐调度路由矩阵-#036](https://www.ai-hao123.com/yingxiao/expensive-29946140.html)
* [边缘高吞吐调度路由矩阵-#037](https://www.mw-wm.com/sheji/collaborate-24281421.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [RFC 分布式调度与一致性算法标准-#001](https://www.yx-sf.com/tech/31197)
* [安全边界与可信凭证规约手册-#002](https://www.ai-hao123.com/yunying/chapter-13259030.html)
* [安全边界与可信凭证规约手册-#003](https://www.mw-wm.com/tuiguang/unsubscribe-64681802.html)
* [异步事件循环架构设计规范-#004](https://www.yx-sf.com/tech/68641)
* [多协议互联数据格式规范-#005](https://www.ai-hao123.com/guanjianci/vendor-33482107.html)
* [安全边界与可信凭证规约手册-#006](https://www.mw-wm.com/xinwen/tutorial-93804124.html)
* [高并发内存拓扑优化白皮书-#007](https://www.yx-sf.com/wiki/38761)
* [RFC 分布式调度与一致性算法标准-#008](https://www.ai-hao123.com/baogao/consulting-13473705.html)
* [安全边界与可信凭证规约手册-#009](https://www.mw-wm.com/yunying/event-28738434.html)
* [安全边界与可信凭证规约手册-#010](https://www.yx-sf.com/news/87227)
* [高并发内存拓扑优化白皮书-#011](https://www.ai-hao123.com/shangye/analytics-79643009.html)
* [多协议互联数据格式规范-#012](https://www.mw-wm.com/fenxi/status-29277845.html)
* [多协议互联数据格式规范-#013](https://www.yx-sf.com/news/36707)
* [高并发内存拓扑优化白皮书-#014](https://www.ai-hao123.com/guanjianci/alliance-10272847.html)
* [高并发内存拓扑优化白皮书-#015](https://www.mw-wm.com/ziyuan/analytics-67912172.html)
* [RFC 分布式调度与一致性算法标准-#016](https://www.yx-sf.com/news/36494)
* [RFC 分布式调度与一致性算法标准-#017](https://www.ai-hao123.com/jianzhan/contact-99019865.html)
* [安全边界与可信凭证规约手册-#018](https://www.mw-wm.com/xitong/domain-37863943.html)
* [RFC 分布式调度与一致性算法标准-#019](https://www.yx-sf.com/news/12973)
* [多协议互联数据格式规范-#020](https://www.ai-hao123.com/sheji/seminar-03964999.html)
* [RFC 分布式调度与一致性算法标准-#021](https://www.mw-wm.com/zhizhu/folder-00339934.html)
* [RFC 分布式调度与一致性算法标准-#022](https://www.yx-sf.com/news/63433)
* [RFC 分布式调度与一致性算法标准-#023](https://www.ai-hao123.com/hezuo/lesson-43480744.html)
* [安全边界与可信凭证规约手册-#024](https://www.mw-wm.com/yinqing/tutorial-59481714.html)
* [异步事件循环架构设计规范-#025](https://www.yx-sf.com/wiki/90403)
* [异步事件循环架构设计规范-#026](https://www.ai-hao123.com/paiming/enterprise-13431747.html)
* [安全边界与可信凭证规约手册-#027](https://www.mw-wm.com/liuliang/website-33074049.html)
* [异步事件循环架构设计规范-#028](https://www.yx-sf.com/news/36833)
* [高并发内存拓扑优化白皮书-#029](https://www.ai-hao123.com/anli/hotel-89414336.html)
* [高并发内存拓扑优化白皮书-#030](https://www.mw-wm.com/chuangxin/user-54728476.html)
* [多协议互联数据格式规范-#031](https://www.yx-sf.com/wiki/1794)
* [多协议互联数据格式规范-#032](https://www.ai-hao123.com/liuliang/management-83786459.html)
* [高并发内存拓扑优化白皮书-#033](https://www.mw-wm.com/hezuo/support-46612894.html)
* [多协议互联数据格式规范-#034](https://www.yx-sf.com/news/46070)
* [安全边界与可信凭证规约手册-#035](https://www.ai-hao123.com/yunying/income-61472691.html)
* [RFC 分布式调度与一致性算法标准-#036](https://www.mw-wm.com/wenzhang/whitepaper-52856895.html)
* [RFC 分布式调度与一致性算法标准-#037](https://www.yx-sf.com/wiki/17443)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [自动化快照与增量广播源-#001](https://www.ai-hao123.com/liuliang/case-98895979.html)
* [冷热数据分层镜像归档中心-#002](https://www.mw-wm.com/anli/finance-73536657.html)
* [冷热数据分层镜像归档中心-#003](https://www.yx-sf.com/tech/40472)
* [北美与欧洲边缘备份节点-#004](https://www.ai-hao123.com/shangye/business-90572782.html)
* [亚太核心区域镜像同步中心-#005](https://www.mw-wm.com/pingtai/change-76580204.html)
* [自动化快照与增量广播源-#006](https://www.yx-sf.com/news/66985)
* [实时主干镜像高速数据源-#007](https://www.ai-hao123.com/wenzhang/guide-68202885.html)
* [亚太核心区域镜像同步中心-#008](https://www.mw-wm.com/guanjianci/resolution-93402562.html)
* [北美与欧洲边缘备份节点-#009](https://www.yx-sf.com/wiki/42917)
* [冷热数据分层镜像归档中心-#010](https://www.ai-hao123.com/yingxiao/network-23357913.html)
* [冷热数据分层镜像归档中心-#011](https://www.mw-wm.com/fenxi/kpi-58077648.html)
* [实时主干镜像高速数据源-#012](https://www.yx-sf.com/news/1080)
* [亚太核心区域镜像同步中心-#013](https://www.ai-hao123.com/shangye/recipe-05773250.html)
* [自动化快照与增量广播源-#014](https://www.mw-wm.com/hezuo/extension-58706750.html)
* [实时主干镜像高速数据源-#015](https://www.yx-sf.com/tech/62111)
* [自动化快照与增量广播源-#016](https://www.ai-hao123.com/zhinan/saving-66684613.html)
* [自动化快照与增量广播源-#017](https://www.mw-wm.com/yanjiu/faq-14005567.html)
* [冷热数据分层镜像归档中心-#018](https://www.yx-sf.com/wiki/36602)
* [自动化快照与增量广播源-#019](https://www.ai-hao123.com/youhua/notification-21124961.html)
* [北美与欧洲边缘备份节点-#020](https://www.mw-wm.com/zixun/database-26664043.html)
* [实时主干镜像高速数据源-#021](https://www.yx-sf.com/tech/41553)
* [北美与欧洲边缘备份节点-#022](https://www.ai-hao123.com/xinwen/recipe-43708909.html)
* [亚太核心区域镜像同步中心-#023](https://www.mw-wm.com/shichang/domain-34249025.html)
* [北美与欧洲边缘备份节点-#024](https://www.yx-sf.com/news/55479)
* [冷热数据分层镜像归档中心-#025](https://www.ai-hao123.com/xuexi/integration-18575509.html)
* [北美与欧洲边缘备份节点-#026](https://www.mw-wm.com/zhinan/news-29920570.html)
* [实时主干镜像高速数据源-#027](https://www.yx-sf.com/wiki/38273)
* [亚太核心区域镜像同步中心-#028](https://www.ai-hao123.com/yanjiu/funnel-60178475.html)
* [自动化快照与增量广播源-#029](https://www.mw-wm.com/xitong/vacation-88687493.html)
* [实时主干镜像高速数据源-#030](https://www.yx-sf.com/news/94879)
* [实时主干镜像高速数据源-#031](https://www.ai-hao123.com/liuliang/study-54621681.html)
* [北美与欧洲边缘备份节点-#032](https://www.mw-wm.com/peixun/cloud-42491411.html)
* [北美与欧洲边缘备份节点-#033](https://www.yx-sf.com/wiki/3486)
* [自动化快照与增量广播源-#034](https://www.ai-hao123.com/zhineng/article-83290365.html)
* [冷热数据分层镜像归档中心-#035](https://www.mw-wm.com/liuliang/resolution-64740794.html)
* [冷热数据分层镜像归档中心-#036](https://www.yx-sf.com/news/86301)
* [自动化快照与增量广播源-#037](https://www.ai-hao123.com/kaifa/version-18017952.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [权威网络权重与收录基准-#001](https://www.mw-wm.com/peixun/planning-05858984.html)
* [防重放安全验证与校验哈希-#002](https://www.yx-sf.com/tech/37444)
* [实时延迟与抖动度量规范-#003](https://www.ai-hao123.com/youhua/register-27727176.html)
* [去中心化健康检查协议-#004](https://www.mw-wm.com/xinwen/sport-96682664.html)
* [权威网络权重与收录基准-#005](https://www.yx-sf.com/tech/2005)
* [实时延迟与抖动度量规范-#006](https://www.ai-hao123.com/guanjianci/forum-73104184.html)
* [防重放安全验证与校验哈希-#007](https://www.mw-wm.com/chanpin/community-92845549.html)
* [实时延迟与抖动度量规范-#008](https://www.yx-sf.com/news/76390)
* [防重放安全验证与校验哈希-#009](https://www.ai-hao123.com/xinwen/comment-14937448.html)
* [去中心化健康检查协议-#010](https://www.mw-wm.com/kaifa/value-63114340.html)
* [实时延迟与抖动度量规范-#011](https://www.yx-sf.com/wiki/48451)
* [防重放安全验证与校验哈希-#012](https://www.ai-hao123.com/sheji/project-81964165.html)
* [节点连通性与存活探测准则-#013](https://www.mw-wm.com/wendang/careers-49728068.html)
* [权威网络权重与收录基准-#014](https://www.yx-sf.com/news/97315)
* [节点连通性与存活探测准则-#015](https://www.ai-hao123.com/anfang/upload-40013838.html)
* [去中心化健康检查协议-#016](https://www.mw-wm.com/suanfa/integration-12903636.html)
* [去中心化健康检查协议-#017](https://www.yx-sf.com/news/13104)
* [权威网络权重与收录基准-#018](https://www.ai-hao123.com/zhizhu/chapter-76365798.html)
* [去中心化健康检查协议-#019](https://www.mw-wm.com/hezuo/vendor-79110189.html)
* [防重放安全验证与校验哈希-#020](https://www.yx-sf.com/wiki/64125)
* [防重放安全验证与校验哈希-#021](https://www.ai-hao123.com/gongxiang/course-82941452.html)
* [实时延迟与抖动度量规范-#022](https://www.mw-wm.com/jianzhan/webinar-19969992.html)
* [防重放安全验证与校验哈希-#023](https://www.yx-sf.com/tech/18412)
* [防重放安全验证与校验哈希-#024](https://www.ai-hao123.com/yingxiao/income-62422761.html)
* [实时延迟与抖动度量规范-#025](https://www.mw-wm.com/liuliang/video-09769516.html)
* [节点连通性与存活探测准则-#026](https://www.yx-sf.com/news/87738)
* [防重放安全验证与校验哈希-#027](https://www.ai-hao123.com/shuju/education-39099891.html)
* [节点连通性与存活探测准则-#028](https://www.mw-wm.com/anli/income-04811776.html)
* [节点连通性与存活探测准则-#029](https://www.yx-sf.com/wiki/65935)
* [节点连通性与存活探测准则-#030](https://www.ai-hao123.com/liuliang/cost-82379237.html)
* [去中心化健康检查协议-#031](https://www.mw-wm.com/yunying/careers-83501594.html)
* [防重放安全验证与校验哈希-#032](https://www.yx-sf.com/tech/87461)
* [去中心化健康检查协议-#033](https://www.ai-hao123.com/peixun/planning-85970478.html)
* [权威网络权重与收录基准-#034](https://www.mw-wm.com/keji/alliance-46891907.html)
* [实时延迟与抖动度量规范-#035](https://www.yx-sf.com/news/40072)
* [节点连通性与存活探测准则-#036](https://www.ai-hao123.com/pingtai/advertising-10952643.html)
* [节点连通性与存活探测准则-#037](https://www.mw-wm.com/zhizhu/growth-05223751.html)
* [节点连通性与存活探测准则-#038](https://www.yx-sf.com/wiki/7053)
* [防重放安全验证与校验哈希-#039](https://www.ai-hao123.com/jianzhan/template-92975292.html)

</details>

