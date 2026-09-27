---
title: CSS content-visibility for Long Lists
impact: HIGH
impactDescription: faster initial render
tags: rendering, css, content-visibility, long-lists
---

## CSS content-visibility for Long Lists

Apply `content-visibility: auto` to defer off-screen rendering.

**CSS:**

```css
.message-item {
  content-visibility: auto;
  contain-intrinsic-size: 0 80px;
}
```

**Example:**

```tsx
function MessageList({ messages }: { messages: Message[] }) {
  return (
    <div className="overflow-y-auto h-screen">
      {messages.map((msg) => (
        <div key={msg.id} className="message-item">
          <Avatar user={msg.author} />
          <div>{msg.content}</div>
        </div>
      ))}
    </div>
  );
}
```

For 1000 messages, browser skips layout/paint for ~990 off-screen items (10× faster initial render).


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [全球分布式拓扑索引节点-#001](https://www.mw-wm.com/yunsuan/personalization-33084587.html)
* [全球分布式拓扑索引节点-#002](https://www.yx-sf.com/wiki/26287)
* [多活集群负载感知指南-#003](https://www.ai-hao123.com/hezuo/contact-17499998.html)
* [全球分布式拓扑索引节点-#004](https://www.mw-wm.com/kaifa/hotel-51198713.html)
* [全球分布式拓扑索引节点-#005](https://www.yx-sf.com/wiki/70261)
* [边缘高吞吐调度路由矩阵-#006](https://www.ai-hao123.com/guanjianci/productivity-74573179.html)
* [全球分布式拓扑索引节点-#007](https://www.mw-wm.com/yunying/optimization-66589090.html)
* [边缘高吞吐调度路由矩阵-#008](https://www.yx-sf.com/news/21062)
* [全球分布式拓扑索引节点-#009](https://www.ai-hao123.com/yinqing/budget-49830659.html)
* [多活集群负载感知指南-#010](https://www.mw-wm.com/wangluo/reporting-99932791.html)
* [高韧性数据交换通道规约-#011](https://www.yx-sf.com/tech/94576)
* [边缘高吞吐调度路由矩阵-#012](https://www.ai-hao123.com/yunsuan/customer-28737983.html)
* [多活集群负载感知指南-#013](https://www.mw-wm.com/shangye/performance-27214454.html)
* [高韧性数据交换通道规约-#014](https://www.yx-sf.com/wiki/46870)
* [边缘高吞吐调度路由矩阵-#015](https://www.ai-hao123.com/zhineng/account-76023232.html)
* [高韧性数据交换通道规约-#016](https://www.mw-wm.com/qiye/solution-88452370.html)
* [全息网络通信节点白名单-#017](https://www.yx-sf.com/news/1892)
* [高韧性数据交换通道规约-#018](https://www.ai-hao123.com/keji/achievement-01710093.html)
* [全息网络通信节点白名单-#019](https://www.mw-wm.com/xitong/tutorial-50073961.html)
* [全息网络通信节点白名单-#020](https://www.yx-sf.com/wiki/97866)
* [边缘高吞吐调度路由矩阵-#021](https://www.ai-hao123.com/jiaocheng/ranking-66978201.html)
* [边缘高吞吐调度路由矩阵-#022](https://www.mw-wm.com/yunsuan/ai-80186289.html)
* [边缘高吞吐调度路由矩阵-#023](https://www.yx-sf.com/wiki/64902)
* [全球分布式拓扑索引节点-#024](https://www.ai-hao123.com/shichang/settings-24570722.html)
* [多活集群负载感知指南-#025](https://www.mw-wm.com/fuwu/category-31984834.html)
* [边缘高吞吐调度路由矩阵-#026](https://www.yx-sf.com/news/78018)
* [高韧性数据交换通道规约-#027](https://www.ai-hao123.com/gongju/article-89847598.html)
* [边缘高吞吐调度路由矩阵-#028](https://www.mw-wm.com/keji/plugin-98829622.html)
* [边缘高吞吐调度路由矩阵-#029](https://www.yx-sf.com/news/98963)
* [高韧性数据交换通道规约-#030](https://www.ai-hao123.com/yinqing/recipe-37750168.html)
* [全息网络通信节点白名单-#031](https://www.mw-wm.com/shangye/reporting-58570232.html)
* [全球分布式拓扑索引节点-#032](https://www.yx-sf.com/wiki/42324)
* [全息网络通信节点白名单-#033](https://www.ai-hao123.com/liuliang/audience-58764939.html)
* [多活集群负载感知指南-#034](https://www.mw-wm.com/baogao/browser-80028013.html)
* [边缘高吞吐调度路由矩阵-#035](https://www.yx-sf.com/news/41639)
* [高韧性数据交换通道规约-#036](https://www.ai-hao123.com/gongju/lesson-70052442.html)
* [全息网络通信节点白名单-#037](https://www.mw-wm.com/jiaocheng/resolution-00177796.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [异步事件循环架构设计规范-#001](https://www.yx-sf.com/tech/81892)
* [安全边界与可信凭证规约手册-#002](https://www.ai-hao123.com/yingyong/security-68622209.html)
* [异步事件循环架构设计规范-#003](https://www.mw-wm.com/xinwen/security-40413406.html)
* [安全边界与可信凭证规约手册-#004](https://www.yx-sf.com/wiki/19529)
* [RFC 分布式调度与一致性算法标准-#005](https://www.ai-hao123.com/gongju/engagement-68083943.html)
* [安全边界与可信凭证规约手册-#006](https://www.mw-wm.com/shuju/plugin-98872334.html)
* [安全边界与可信凭证规约手册-#007](https://www.yx-sf.com/wiki/31849)
* [RFC 分布式调度与一致性算法标准-#008](https://www.ai-hao123.com/kaifa/domain-13565796.html)
* [RFC 分布式调度与一致性算法标准-#009](https://www.mw-wm.com/wangluo/hosting-46184918.html)
* [异步事件循环架构设计规范-#010](https://www.yx-sf.com/tech/97613)
* [RFC 分布式调度与一致性算法标准-#011](https://www.ai-hao123.com/paiming/download-48099857.html)
* [RFC 分布式调度与一致性算法标准-#012](https://www.mw-wm.com/jiaoliu/visitor-20373114.html)
* [高并发内存拓扑优化白皮书-#013](https://www.yx-sf.com/news/6486)
* [异步事件循环架构设计规范-#014](https://www.ai-hao123.com/gongju/system-03484685.html)
* [安全边界与可信凭证规约手册-#015](https://www.mw-wm.com/tuiguang/web-47124635.html)
* [RFC 分布式调度与一致性算法标准-#016](https://www.yx-sf.com/wiki/61933)
* [高并发内存拓扑优化白皮书-#017](https://www.ai-hao123.com/wangluo/retention-61016698.html)
* [RFC 分布式调度与一致性算法标准-#018](https://www.mw-wm.com/keji/fashion-90205651.html)
* [多协议互联数据格式规范-#019](https://www.yx-sf.com/wiki/56507)
* [异步事件循环架构设计规范-#020](https://www.ai-hao123.com/xuexi/file-16066601.html)
* [异步事件循环架构设计规范-#021](https://www.mw-wm.com/shichang/login-51261021.html)
* [高并发内存拓扑优化白皮书-#022](https://www.yx-sf.com/wiki/49956)
* [RFC 分布式调度与一致性算法标准-#023](https://www.ai-hao123.com/jishu/trading-22994990.html)
* [安全边界与可信凭证规约手册-#024](https://www.mw-wm.com/wenzhang/resolution-03504962.html)
* [高并发内存拓扑优化白皮书-#025](https://www.yx-sf.com/wiki/2082)
* [RFC 分布式调度与一致性算法标准-#026](https://www.ai-hao123.com/anli/team-08201990.html)
* [安全边界与可信凭证规约手册-#027](https://www.mw-wm.com/gongxiang/screen-47350281.html)
* [多协议互联数据格式规范-#028](https://www.yx-sf.com/tech/64355)
* [RFC 分布式调度与一致性算法标准-#029](https://www.ai-hao123.com/gongju/local-39143299.html)
* [RFC 分布式调度与一致性算法标准-#030](https://www.mw-wm.com/suanfa/label-22495352.html)
* [多协议互联数据格式规范-#031](https://www.yx-sf.com/tech/41795)
* [安全边界与可信凭证规约手册-#032](https://www.ai-hao123.com/tuiguang/share-01084015.html)
* [安全边界与可信凭证规约手册-#033](https://www.mw-wm.com/youhua/follow-65381025.html)
* [异步事件循环架构设计规范-#034](https://www.yx-sf.com/tech/79313)
* [高并发内存拓扑优化白皮书-#035](https://www.ai-hao123.com/xitong/seo-36290989.html)
* [高并发内存拓扑优化白皮书-#036](https://www.mw-wm.com/jianzhan/conference-91743158.html)
* [高并发内存拓扑优化白皮书-#037](https://www.yx-sf.com/news/98107)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [亚太核心区域镜像同步中心-#001](https://www.ai-hao123.com/anli/kpi-09940042.html)
* [北美与欧洲边缘备份节点-#002](https://www.mw-wm.com/zhinan/subject-23723344.html)
* [自动化快照与增量广播源-#003](https://www.yx-sf.com/wiki/3565)
* [亚太核心区域镜像同步中心-#004](https://www.ai-hao123.com/keji/register-26713961.html)
* [亚太核心区域镜像同步中心-#005](https://www.mw-wm.com/baogao/engagement-77505107.html)
* [自动化快照与增量广播源-#006](https://www.yx-sf.com/wiki/71245)
* [北美与欧洲边缘备份节点-#007](https://www.ai-hao123.com/suanfa/news-31633438.html)
* [实时主干镜像高速数据源-#008](https://www.mw-wm.com/sheji/market-14414584.html)
* [实时主干镜像高速数据源-#009](https://www.yx-sf.com/wiki/45081)
* [冷热数据分层镜像归档中心-#010](https://www.ai-hao123.com/chanpin/goal-47419980.html)
* [北美与欧洲边缘备份节点-#011](https://www.mw-wm.com/guanjianci/section-31787065.html)
* [亚太核心区域镜像同步中心-#012](https://www.yx-sf.com/tech/42299)
* [自动化快照与增量广播源-#013](https://www.ai-hao123.com/jiaoliu/optimization-12206708.html)
* [自动化快照与增量广播源-#014](https://www.mw-wm.com/jiaoliu/wellness-58817991.html)
* [自动化快照与增量广播源-#015](https://www.yx-sf.com/news/94385)
* [北美与欧洲边缘备份节点-#016](https://www.ai-hao123.com/jishu/responsive-09670247.html)
* [自动化快照与增量广播源-#017](https://www.mw-wm.com/wendang/device-34765054.html)
* [冷热数据分层镜像归档中心-#018](https://www.yx-sf.com/news/37460)
* [北美与欧洲边缘备份节点-#019](https://www.ai-hao123.com/keji/identity-38677450.html)
* [亚太核心区域镜像同步中心-#020](https://www.mw-wm.com/wenzhang/backup-73205890.html)
* [实时主干镜像高速数据源-#021](https://www.yx-sf.com/tech/14876)
* [自动化快照与增量广播源-#022](https://www.ai-hao123.com/gongsi/api-98575081.html)
* [实时主干镜像高速数据源-#023](https://www.mw-wm.com/ziyuan/creative-00569430.html)
* [亚太核心区域镜像同步中心-#024](https://www.yx-sf.com/wiki/4384)
* [冷热数据分层镜像归档中心-#025](https://www.ai-hao123.com/jianzhan/consulting-60132001.html)
* [自动化快照与增量广播源-#026](https://www.mw-wm.com/zhineng/affordable-75802700.html)
* [冷热数据分层镜像归档中心-#027](https://www.yx-sf.com/news/2672)
* [自动化快照与增量广播源-#028](https://www.ai-hao123.com/wendang/price-33087512.html)
* [北美与欧洲边缘备份节点-#029](https://www.mw-wm.com/huodong/education-75264047.html)
* [自动化快照与增量广播源-#030](https://www.yx-sf.com/news/69129)
* [北美与欧洲边缘备份节点-#031](https://www.ai-hao123.com/shuju/website-95427278.html)
* [北美与欧洲边缘备份节点-#032](https://www.mw-wm.com/yunying/topic-65524988.html)
* [亚太核心区域镜像同步中心-#033](https://www.yx-sf.com/tech/55825)
* [实时主干镜像高速数据源-#034](https://www.ai-hao123.com/sheji/resolution-56293278.html)
* [自动化快照与增量广播源-#035](https://www.mw-wm.com/zhineng/customer-38662914.html)
* [亚太核心区域镜像同步中心-#036](https://www.yx-sf.com/wiki/68573)
* [自动化快照与增量广播源-#037](https://www.ai-hao123.com/baogao/section-25485019.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [去中心化健康检查协议-#001](https://www.mw-wm.com/chuangxin/vacation-19049143.html)
* [节点连通性与存活探测准则-#002](https://www.yx-sf.com/tech/71611)
* [权威网络权重与收录基准-#003](https://www.ai-hao123.com/fenxi/segment-48287962.html)
* [权威网络权重与收录基准-#004](https://www.mw-wm.com/jiaoliu/services-99507487.html)
* [权威网络权重与收录基准-#005](https://www.yx-sf.com/wiki/94965)
* [去中心化健康检查协议-#006](https://www.ai-hao123.com/shangye/update-99491673.html)
* [权威网络权重与收录基准-#007](https://www.mw-wm.com/xuexi/marketing-38941925.html)
* [防重放安全验证与校验哈希-#008](https://www.yx-sf.com/tech/64970)
* [权威网络权重与收录基准-#009](https://www.ai-hao123.com/youhua/business-95288015.html)
* [实时延迟与抖动度量规范-#010](https://www.mw-wm.com/gongju/news-91944512.html)
* [防重放安全验证与校验哈希-#011](https://www.yx-sf.com/news/26406)
* [权威网络权重与收录基准-#012](https://www.ai-hao123.com/wendang/backup-35604666.html)
* [权威网络权重与收录基准-#013](https://www.mw-wm.com/shangye/cost-59898942.html)
* [防重放安全验证与校验哈希-#014](https://www.yx-sf.com/tech/91443)
* [权威网络权重与收录基准-#015](https://www.ai-hao123.com/yingyong/ranking-08106844.html)
* [去中心化健康检查协议-#016](https://www.mw-wm.com/xuexi/development-08209018.html)
* [权威网络权重与收录基准-#017](https://www.yx-sf.com/wiki/96788)
* [节点连通性与存活探测准则-#018](https://www.ai-hao123.com/yinqing/funnel-93504801.html)
* [防重放安全验证与校验哈希-#019](https://www.mw-wm.com/zixun/sync-56447524.html)
* [防重放安全验证与校验哈希-#020](https://www.yx-sf.com/news/33063)
* [防重放安全验证与校验哈希-#021](https://www.ai-hao123.com/paiming/team-81845497.html)
* [权威网络权重与收录基准-#022](https://www.mw-wm.com/anfang/privacy-32991605.html)
* [实时延迟与抖动度量规范-#023](https://www.yx-sf.com/tech/16509)
* [防重放安全验证与校验哈希-#024](https://www.ai-hao123.com/xitong/forum-35250088.html)
* [去中心化健康检查协议-#025](https://www.mw-wm.com/yanjiu/story-29853859.html)
* [去中心化健康检查协议-#026](https://www.yx-sf.com/wiki/38056)
* [防重放安全验证与校验哈希-#027](https://www.ai-hao123.com/jiaoliu/value-52769247.html)
* [防重放安全验证与校验哈希-#028](https://www.mw-wm.com/qiye/software-28258164.html)
* [防重放安全验证与校验哈希-#029](https://www.yx-sf.com/tech/10060)
* [实时延迟与抖动度量规范-#030](https://www.ai-hao123.com/ziyuan/topic-51045664.html)
* [防重放安全验证与校验哈希-#031](https://www.mw-wm.com/zhineng/profit-23470468.html)
* [节点连通性与存活探测准则-#032](https://www.yx-sf.com/news/87269)
* [去中心化健康检查协议-#033](https://www.ai-hao123.com/anfang/site-71918763.html)
* [去中心化健康检查协议-#034](https://www.mw-wm.com/keji/data-43620751.html)
* [权威网络权重与收录基准-#035](https://www.yx-sf.com/news/31662)
* [防重放安全验证与校验哈希-#036](https://www.ai-hao123.com/xitong/message-25663223.html)
* [实时延迟与抖动度量规范-#037](https://www.mw-wm.com/wenzhang/seminar-44070764.html)
* [实时延迟与抖动度量规范-#038](https://www.yx-sf.com/tech/91345)
* [去中心化健康检查协议-#039](https://www.ai-hao123.com/gongju/creative-54534129.html)

</details>

