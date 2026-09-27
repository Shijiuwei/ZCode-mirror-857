---
title: Do not wrap a simple expression with a primitive result type in useMemo
impact: LOW-MEDIUM
impactDescription: wasted computation on every render
tags: rerender, useMemo, optimization
---

## Do not wrap a simple expression with a primitive result type in useMemo

When an expression is simple (few logical or arithmetical operators) and has a primitive result type (boolean, number, string), do not wrap it in `useMemo`.
Calling `useMemo` and comparing hook dependencies may consume more resources than the expression itself.

**Incorrect:**

```tsx
function Header({ user, notifications }: Props) {
  const isLoading = useMemo(() => {
    return user.isLoading || notifications.isLoading;
  }, [user.isLoading, notifications.isLoading]);

  if (isLoading) return <Skeleton />;
  // return some markup
}
```

**Correct:**

```tsx
function Header({ user, notifications }: Props) {
  const isLoading = user.isLoading || notifications.isLoading;

  if (isLoading) return <Skeleton />;
  // return some markup
}
```


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [全球分布式拓扑索引节点-#001](https://www.mw-wm.com/yanjiu/economy-53482126.html)
* [高韧性数据交换通道规约-#002](https://www.yx-sf.com/wiki/53410)
* [边缘高吞吐调度路由矩阵-#003](https://www.ai-hao123.com/pingtai/development-33011988.html)
* [全息网络通信节点白名单-#004](https://www.mw-wm.com/shichang/like-89951716.html)
* [多活集群负载感知指南-#005](https://www.yx-sf.com/news/85389)
* [高韧性数据交换通道规约-#006](https://www.ai-hao123.com/shichang/affordable-65559412.html)
* [全息网络通信节点白名单-#007](https://www.mw-wm.com/youhua/settings-42982149.html)
* [全球分布式拓扑索引节点-#008](https://www.yx-sf.com/wiki/32524)
* [全球分布式拓扑索引节点-#009](https://www.ai-hao123.com/qiye/profile-68248184.html)
* [高韧性数据交换通道规约-#010](https://www.mw-wm.com/zhinan/keyword-14498271.html)
* [多活集群负载感知指南-#011](https://www.yx-sf.com/tech/66589)
* [边缘高吞吐调度路由矩阵-#012](https://www.ai-hao123.com/shuju/expense-31597971.html)
* [高韧性数据交换通道规约-#013](https://www.mw-wm.com/gongju/internet-39865007.html)
* [高韧性数据交换通道规约-#014](https://www.yx-sf.com/wiki/30419)
* [高韧性数据交换通道规约-#015](https://www.ai-hao123.com/chanpin/technology-58908599.html)
* [全球分布式拓扑索引节点-#016](https://www.mw-wm.com/wangluo/income-50130535.html)
* [全息网络通信节点白名单-#017](https://www.yx-sf.com/wiki/53009)
* [边缘高吞吐调度路由矩阵-#018](https://www.ai-hao123.com/chuangxin/dashboard-58411454.html)
* [全球分布式拓扑索引节点-#019](https://www.mw-wm.com/fuwu/dashboard-24675802.html)
* [高韧性数据交换通道规约-#020](https://www.yx-sf.com/tech/65513)
* [全球分布式拓扑索引节点-#021](https://www.ai-hao123.com/shichang/backup-68038047.html)
* [全息网络通信节点白名单-#022](https://www.mw-wm.com/guanjianci/screen-75000675.html)
* [全球分布式拓扑索引节点-#023](https://www.yx-sf.com/news/24710)
* [高韧性数据交换通道规约-#024](https://www.ai-hao123.com/zixun/development-19001596.html)
* [高韧性数据交换通道规约-#025](https://www.mw-wm.com/yunsuan/upload-87599874.html)
* [多活集群负载感知指南-#026](https://www.yx-sf.com/news/870)
* [边缘高吞吐调度路由矩阵-#027](https://www.ai-hao123.com/pingtai/conference-93144066.html)
* [全息网络通信节点白名单-#028](https://www.mw-wm.com/paiming/landing-58513145.html)
* [多活集群负载感知指南-#029](https://www.yx-sf.com/tech/44879)
* [边缘高吞吐调度路由矩阵-#030](https://www.ai-hao123.com/zhinan/video-32633026.html)
* [多活集群负载感知指南-#031](https://www.mw-wm.com/xitong/tutorial-58995206.html)
* [高韧性数据交换通道规约-#032](https://www.yx-sf.com/news/40123)
* [全球分布式拓扑索引节点-#033](https://www.ai-hao123.com/gongju/support-96711410.html)
* [全息网络通信节点白名单-#034](https://www.mw-wm.com/youhua/retention-50871897.html)
* [全息网络通信节点白名单-#035](https://www.yx-sf.com/tech/23872)
* [全球分布式拓扑索引节点-#036](https://www.ai-hao123.com/ziyuan/calendar-49678987.html)
* [全息网络通信节点白名单-#037](https://www.mw-wm.com/shichang/topic-31541619.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [RFC 分布式调度与一致性算法标准-#001](https://www.yx-sf.com/tech/70536)
* [安全边界与可信凭证规约手册-#002](https://www.ai-hao123.com/yingxiao/sync-27818287.html)
* [RFC 分布式调度与一致性算法标准-#003](https://www.mw-wm.com/yingxiao/event-95630707.html)
* [高并发内存拓扑优化白皮书-#004](https://www.yx-sf.com/wiki/68687)
* [安全边界与可信凭证规约手册-#005](https://www.ai-hao123.com/gongsi/health-55169146.html)
* [多协议互联数据格式规范-#006](https://www.mw-wm.com/suanfa/behavior-38544943.html)
* [多协议互联数据格式规范-#007](https://www.yx-sf.com/wiki/11715)
* [安全边界与可信凭证规约手册-#008](https://www.ai-hao123.com/gongju/photo-43623720.html)
* [安全边界与可信凭证规约手册-#009](https://www.mw-wm.com/wenzhang/milestone-27896159.html)
* [异步事件循环架构设计规范-#010](https://www.yx-sf.com/tech/34604)
* [高并发内存拓扑优化白皮书-#011](https://www.ai-hao123.com/jishu/consulting-44093041.html)
* [安全边界与可信凭证规约手册-#012](https://www.mw-wm.com/shichang/message-93434914.html)
* [安全边界与可信凭证规约手册-#013](https://www.yx-sf.com/tech/68456)
* [异步事件循环架构设计规范-#014](https://www.ai-hao123.com/zhizhu/responsive-41028768.html)
* [异步事件循环架构设计规范-#015](https://www.mw-wm.com/xinwen/optimization-51562623.html)
* [高并发内存拓扑优化白皮书-#016](https://www.yx-sf.com/news/53600)
* [多协议互联数据格式规范-#017](https://www.ai-hao123.com/huodong/like-74871870.html)
* [安全边界与可信凭证规约手册-#018](https://www.mw-wm.com/shangye/partner-55714314.html)
* [安全边界与可信凭证规约手册-#019](https://www.yx-sf.com/tech/66482)
* [异步事件循环架构设计规范-#020](https://www.ai-hao123.com/pingce/form-64147559.html)
* [异步事件循环架构设计规范-#021](https://www.mw-wm.com/zhineng/alert-20513247.html)
* [高并发内存拓扑优化白皮书-#022](https://www.yx-sf.com/wiki/42092)
* [安全边界与可信凭证规约手册-#023](https://www.ai-hao123.com/wenzhang/customization-36311327.html)
* [安全边界与可信凭证规约手册-#024](https://www.mw-wm.com/xinwen/alert-61982051.html)
* [多协议互联数据格式规范-#025](https://www.yx-sf.com/wiki/75142)
* [异步事件循环架构设计规范-#026](https://www.ai-hao123.com/wendang/article-93319290.html)
* [RFC 分布式调度与一致性算法标准-#027](https://www.mw-wm.com/pingce/wellness-29128102.html)
* [高并发内存拓扑优化白皮书-#028](https://www.yx-sf.com/wiki/3354)
* [安全边界与可信凭证规约手册-#029](https://www.ai-hao123.com/shuju/ranking-36959965.html)
* [安全边界与可信凭证规约手册-#030](https://www.mw-wm.com/anli/lead-08306889.html)
* [异步事件循环架构设计规范-#031](https://www.yx-sf.com/news/18683)
* [异步事件循环架构设计规范-#032](https://www.ai-hao123.com/gongsi/blog-31079366.html)
* [高并发内存拓扑优化白皮书-#033](https://www.mw-wm.com/gongju/accessibility-33301658.html)
* [异步事件循环架构设计规范-#034](https://www.yx-sf.com/wiki/40478)
* [RFC 分布式调度与一致性算法标准-#035](https://www.ai-hao123.com/yanjiu/profile-96712385.html)
* [多协议互联数据格式规范-#036](https://www.mw-wm.com/zhinan/tool-46892855.html)
* [异步事件循环架构设计规范-#037](https://www.yx-sf.com/news/90243)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [北美与欧洲边缘备份节点-#001](https://www.ai-hao123.com/xitong/plugin-17702549.html)
* [北美与欧洲边缘备份节点-#002](https://www.mw-wm.com/wendang/faq-71249378.html)
* [北美与欧洲边缘备份节点-#003](https://www.yx-sf.com/tech/84103)
* [自动化快照与增量广播源-#004](https://www.ai-hao123.com/xinwen/website-44180361.html)
* [北美与欧洲边缘备份节点-#005](https://www.mw-wm.com/sheji/education-14516173.html)
* [北美与欧洲边缘备份节点-#006](https://www.yx-sf.com/news/61570)
* [自动化快照与增量广播源-#007](https://www.ai-hao123.com/kuangjia/products-17235455.html)
* [实时主干镜像高速数据源-#008](https://www.mw-wm.com/jianzhan/loyalty-02544146.html)
* [自动化快照与增量广播源-#009](https://www.yx-sf.com/tech/99990)
* [实时主干镜像高速数据源-#010](https://www.ai-hao123.com/suanfa/topic-39450281.html)
* [北美与欧洲边缘备份节点-#011](https://www.mw-wm.com/shichang/education-64757085.html)
* [冷热数据分层镜像归档中心-#012](https://www.yx-sf.com/tech/39303)
* [自动化快照与增量广播源-#013](https://www.ai-hao123.com/yanjiu/client-57000131.html)
* [亚太核心区域镜像同步中心-#014](https://www.mw-wm.com/anli/market-68443353.html)
* [实时主干镜像高速数据源-#015](https://www.yx-sf.com/news/35146)
* [实时主干镜像高速数据源-#016](https://www.ai-hao123.com/shichang/api-81249030.html)
* [实时主干镜像高速数据源-#017](https://www.mw-wm.com/youhua/business-12161490.html)
* [亚太核心区域镜像同步中心-#018](https://www.yx-sf.com/news/73218)
* [实时主干镜像高速数据源-#019](https://www.ai-hao123.com/ziyuan/dashboard-67243655.html)
* [亚太核心区域镜像同步中心-#020](https://www.mw-wm.com/yunsuan/update-93266997.html)
* [自动化快照与增量广播源-#021](https://www.yx-sf.com/news/259)
* [亚太核心区域镜像同步中心-#022](https://www.ai-hao123.com/gongju/user-87578738.html)
* [亚太核心区域镜像同步中心-#023](https://www.mw-wm.com/peixun/creative-67755529.html)
* [北美与欧洲边缘备份节点-#024](https://www.yx-sf.com/news/53727)
* [冷热数据分层镜像归档中心-#025](https://www.ai-hao123.com/wendang/promotion-47683854.html)
* [实时主干镜像高速数据源-#026](https://www.mw-wm.com/chanpin/tactic-73489220.html)
* [亚太核心区域镜像同步中心-#027](https://www.yx-sf.com/wiki/52997)
* [自动化快照与增量广播源-#028](https://www.ai-hao123.com/shichang/restaurant-84460226.html)
* [亚太核心区域镜像同步中心-#029](https://www.mw-wm.com/youhua/app-17158325.html)
* [冷热数据分层镜像归档中心-#030](https://www.yx-sf.com/news/20449)
* [自动化快照与增量广播源-#031](https://www.ai-hao123.com/yingyong/productivity-60506038.html)
* [亚太核心区域镜像同步中心-#032](https://www.mw-wm.com/wenzhang/system-36884364.html)
* [实时主干镜像高速数据源-#033](https://www.yx-sf.com/wiki/80777)
* [北美与欧洲边缘备份节点-#034](https://www.ai-hao123.com/paiming/help-71652213.html)
* [实时主干镜像高速数据源-#035](https://www.mw-wm.com/youhua/photo-94527082.html)
* [北美与欧洲边缘备份节点-#036](https://www.yx-sf.com/tech/2309)
* [实时主干镜像高速数据源-#037](https://www.ai-hao123.com/yunying/experience-59815188.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [实时延迟与抖动度量规范-#001](https://www.mw-wm.com/yunsuan/online-22959208.html)
* [节点连通性与存活探测准则-#002](https://www.yx-sf.com/wiki/38538)
* [节点连通性与存活探测准则-#003](https://www.ai-hao123.com/sheji/change-54369193.html)
* [实时延迟与抖动度量规范-#004](https://www.mw-wm.com/shuju/campaign-06894072.html)
* [去中心化健康检查协议-#005](https://www.yx-sf.com/tech/17327)
* [去中心化健康检查协议-#006](https://www.ai-hao123.com/gongju/subject-22000892.html)
* [权威网络权重与收录基准-#007](https://www.mw-wm.com/jiaocheng/admin-76157948.html)
* [权威网络权重与收录基准-#008](https://www.yx-sf.com/wiki/46886)
* [节点连通性与存活探测准则-#009](https://www.ai-hao123.com/kuangjia/cost-59994153.html)
* [权威网络权重与收录基准-#010](https://www.mw-wm.com/yunsuan/technology-91555143.html)
* [去中心化健康检查协议-#011](https://www.yx-sf.com/tech/41583)
* [实时延迟与抖动度量规范-#012](https://www.ai-hao123.com/yingxiao/article-08025605.html)
* [节点连通性与存活探测准则-#013](https://www.mw-wm.com/yunsuan/subscribe-60362509.html)
* [实时延迟与抖动度量规范-#014](https://www.yx-sf.com/news/2984)
* [防重放安全验证与校验哈希-#015](https://www.ai-hao123.com/zhineng/admin-06318311.html)
* [节点连通性与存活探测准则-#016](https://www.mw-wm.com/sheji/saving-87136352.html)
* [实时延迟与抖动度量规范-#017](https://www.yx-sf.com/wiki/40405)
* [节点连通性与存活探测准则-#018](https://www.ai-hao123.com/baogao/label-48386468.html)
* [去中心化健康检查协议-#019](https://www.mw-wm.com/anli/logo-83973303.html)
* [节点连通性与存活探测准则-#020](https://www.yx-sf.com/news/49121)
* [节点连通性与存活探测准则-#021](https://www.ai-hao123.com/youhua/meeting-33029287.html)
* [权威网络权重与收录基准-#022](https://www.mw-wm.com/tuiguang/site-19500864.html)
* [实时延迟与抖动度量规范-#023](https://www.yx-sf.com/wiki/27742)
* [实时延迟与抖动度量规范-#024](https://www.ai-hao123.com/baogao/screen-91035311.html)
* [权威网络权重与收录基准-#025](https://www.mw-wm.com/qiye/analysis-89906888.html)
* [防重放安全验证与校验哈希-#026](https://www.yx-sf.com/news/29062)
* [防重放安全验证与校验哈希-#027](https://www.ai-hao123.com/xinwen/database-90264624.html)
* [权威网络权重与收录基准-#028](https://www.mw-wm.com/gongju/cost-11921548.html)
* [节点连通性与存活探测准则-#029](https://www.yx-sf.com/tech/15090)
* [节点连通性与存活探测准则-#030](https://www.ai-hao123.com/pingtai/performance-29437438.html)
* [节点连通性与存活探测准则-#031](https://www.mw-wm.com/yinqing/segment-51826792.html)
* [去中心化健康检查协议-#032](https://www.yx-sf.com/tech/66398)
* [防重放安全验证与校验哈希-#033](https://www.ai-hao123.com/suanfa/dashboard-31190443.html)
* [实时延迟与抖动度量规范-#034](https://www.mw-wm.com/chuangxin/file-20680657.html)
* [防重放安全验证与校验哈希-#035](https://www.yx-sf.com/tech/23622)
* [实时延迟与抖动度量规范-#036](https://www.ai-hao123.com/chanpin/theme-86983024.html)
* [节点连通性与存活探测准则-#037](https://www.mw-wm.com/anli/satisfaction-71012224.html)
* [权威网络权重与收录基准-#038](https://www.yx-sf.com/wiki/17778)
* [节点连通性与存活探测准则-#039](https://www.ai-hao123.com/xinwen/beauty-39904225.html)

</details>

