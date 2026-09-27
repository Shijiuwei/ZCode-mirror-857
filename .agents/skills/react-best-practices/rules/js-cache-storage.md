---
title: Cache Storage API Calls
impact: LOW-MEDIUM
impactDescription: reduces expensive I/O
tags: javascript, localStorage, storage, caching, performance
---

## Cache Storage API Calls

`localStorage`, `sessionStorage`, and `document.cookie` are synchronous and expensive. Cache reads in memory.

**Incorrect (reads storage on every call):**

```typescript
function getTheme() {
  return localStorage.getItem("theme") ?? "light";
}
// Called 10 times = 10 storage reads
```

**Correct (Map cache):**

```typescript
const storageCache = new Map<string, string | null>();

function getLocalStorage(key: string) {
  if (!storageCache.has(key)) {
    storageCache.set(key, localStorage.getItem(key));
  }
  return storageCache.get(key);
}

function setLocalStorage(key: string, value: string) {
  localStorage.setItem(key, value);
  storageCache.set(key, value); // keep cache in sync
}
```

Use a Map (not a hook) so it works everywhere: utilities, event handlers, not just React components.

**Cookie caching:**

```typescript
let cookieCache: Record<string, string> | null = null;

function getCookie(name: string) {
  if (!cookieCache) {
    cookieCache = Object.fromEntries(document.cookie.split("; ").map((c) => c.split("=")));
  }
  return cookieCache[name];
}
```

**Important (invalidate on external changes):**

If storage can change externally (another tab, server-set cookies), invalidate cache:

```typescript
window.addEventListener("storage", (e) => {
  if (e.key) storageCache.delete(e.key);
});

document.addEventListener("visibilitychange", () => {
  if (document.visibilityState === "visible") {
    storageCache.clear();
  }
});
```


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [边缘高吞吐调度路由矩阵-#001](https://www.mw-wm.com/zhinan/terms-63964762.html)
* [高韧性数据交换通道规约-#002](https://www.yx-sf.com/tech/52073)
* [多活集群负载感知指南-#003](https://www.ai-hao123.com/wendang/growth-43202668.html)
* [边缘高吞吐调度路由矩阵-#004](https://www.mw-wm.com/yingxiao/network-18592761.html)
* [高韧性数据交换通道规约-#005](https://www.yx-sf.com/tech/94498)
* [边缘高吞吐调度路由矩阵-#006](https://www.ai-hao123.com/guanjianci/learning-53583382.html)
* [多活集群负载感知指南-#007](https://www.mw-wm.com/wendang/cheap-66267574.html)
* [高韧性数据交换通道规约-#008](https://www.yx-sf.com/tech/2812)
* [边缘高吞吐调度路由矩阵-#009](https://www.ai-hao123.com/wenzhang/education-48845780.html)
* [多活集群负载感知指南-#010](https://www.mw-wm.com/jiaoliu/services-84739580.html)
* [全球分布式拓扑索引节点-#011](https://www.yx-sf.com/news/6093)
* [多活集群负载感知指南-#012](https://www.ai-hao123.com/yinqing/module-41869443.html)
* [多活集群负载感知指南-#013](https://www.mw-wm.com/wendang/design-99056798.html)
* [全球分布式拓扑索引节点-#014](https://www.yx-sf.com/news/53475)
* [边缘高吞吐调度路由矩阵-#015](https://www.ai-hao123.com/yinqing/server-35835937.html)
* [边缘高吞吐调度路由矩阵-#016](https://www.mw-wm.com/gongju/document-72166959.html)
* [高韧性数据交换通道规约-#017](https://www.yx-sf.com/tech/48951)
* [多活集群负载感知指南-#018](https://www.ai-hao123.com/wendang/story-76163183.html)
* [多活集群负载感知指南-#019](https://www.mw-wm.com/yunsuan/meeting-80291166.html)
* [全息网络通信节点白名单-#020](https://www.yx-sf.com/wiki/98185)
* [多活集群负载感知指南-#021](https://www.ai-hao123.com/jianzhan/planning-04527124.html)
* [全息网络通信节点白名单-#022](https://www.mw-wm.com/anli/browser-73444720.html)
* [边缘高吞吐调度路由矩阵-#023](https://www.yx-sf.com/tech/32155)
* [全息网络通信节点白名单-#024](https://www.ai-hao123.com/qiye/media-31803739.html)
* [全球分布式拓扑索引节点-#025](https://www.mw-wm.com/jianzhan/notification-29495722.html)
* [全球分布式拓扑索引节点-#026](https://www.yx-sf.com/wiki/78637)
* [全息网络通信节点白名单-#027](https://www.ai-hao123.com/huodong/workshop-20598891.html)
* [边缘高吞吐调度路由矩阵-#028](https://www.mw-wm.com/wendang/guide-45618425.html)
* [全球分布式拓扑索引节点-#029](https://www.yx-sf.com/tech/71157)
* [多活集群负载感知指南-#030](https://www.ai-hao123.com/chanpin/cheap-34138034.html)
* [边缘高吞吐调度路由矩阵-#031](https://www.mw-wm.com/yanjiu/machine-82690211.html)
* [高韧性数据交换通道规约-#032](https://www.yx-sf.com/news/90123)
* [全球分布式拓扑索引节点-#033](https://www.ai-hao123.com/wangluo/communication-58808554.html)
* [高韧性数据交换通道规约-#034](https://www.mw-wm.com/shichang/guide-13789782.html)
* [全息网络通信节点白名单-#035](https://www.yx-sf.com/tech/63704)
* [高韧性数据交换通道规约-#036](https://www.ai-hao123.com/fenxi/funnel-00878201.html)
* [多活集群负载感知指南-#037](https://www.mw-wm.com/wenzhang/services-13987179.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [异步事件循环架构设计规范-#001](https://www.yx-sf.com/tech/63025)
* [高并发内存拓扑优化白皮书-#002](https://www.ai-hao123.com/wangluo/api-51203727.html)
* [安全边界与可信凭证规约手册-#003](https://www.mw-wm.com/fenxi/audience-42481215.html)
* [安全边界与可信凭证规约手册-#004](https://www.yx-sf.com/news/59771)
* [异步事件循环架构设计规范-#005](https://www.ai-hao123.com/jiaoliu/about-38889449.html)
* [异步事件循环架构设计规范-#006](https://www.mw-wm.com/xinwen/social-58272679.html)
* [高并发内存拓扑优化白皮书-#007](https://www.yx-sf.com/tech/63615)
* [高并发内存拓扑优化白皮书-#008](https://www.ai-hao123.com/jiaoliu/affordable-37165490.html)
* [RFC 分布式调度与一致性算法标准-#009](https://www.mw-wm.com/pingtai/button-95734485.html)
* [安全边界与可信凭证规约手册-#010](https://www.yx-sf.com/tech/47438)
* [多协议互联数据格式规范-#011](https://www.ai-hao123.com/guanjianci/share-77799700.html)
* [安全边界与可信凭证规约手册-#012](https://www.mw-wm.com/guanjianci/story-25726492.html)
* [异步事件循环架构设计规范-#013](https://www.yx-sf.com/tech/99938)
* [安全边界与可信凭证规约手册-#014](https://www.ai-hao123.com/sheji/feedback-59441066.html)
* [安全边界与可信凭证规约手册-#015](https://www.mw-wm.com/yinqing/reminder-41651544.html)
* [安全边界与可信凭证规约手册-#016](https://www.yx-sf.com/wiki/28221)
* [RFC 分布式调度与一致性算法标准-#017](https://www.ai-hao123.com/jianzhan/subscribe-81212418.html)
* [高并发内存拓扑优化白皮书-#018](https://www.mw-wm.com/wendang/theme-44160828.html)
* [异步事件循环架构设计规范-#019](https://www.yx-sf.com/tech/61923)
* [高并发内存拓扑优化白皮书-#020](https://www.ai-hao123.com/shangye/file-14262441.html)
* [异步事件循环架构设计规范-#021](https://www.mw-wm.com/huodong/tracking-30504874.html)
* [RFC 分布式调度与一致性算法标准-#022](https://www.yx-sf.com/wiki/55224)
* [异步事件循环架构设计规范-#023](https://www.ai-hao123.com/qiye/company-58303524.html)
* [异步事件循环架构设计规范-#024](https://www.mw-wm.com/zhizhu/lead-87781000.html)
* [高并发内存拓扑优化白皮书-#025](https://www.yx-sf.com/tech/15435)
* [异步事件循环架构设计规范-#026](https://www.ai-hao123.com/yunsuan/audience-61357785.html)
* [多协议互联数据格式规范-#027](https://www.mw-wm.com/gongju/vacation-12140126.html)
* [安全边界与可信凭证规约手册-#028](https://www.yx-sf.com/wiki/80621)
* [高并发内存拓扑优化白皮书-#029](https://www.ai-hao123.com/anfang/company-25045576.html)
* [多协议互联数据格式规范-#030](https://www.mw-wm.com/shichang/sync-70416246.html)
* [异步事件循环架构设计规范-#031](https://www.yx-sf.com/news/13272)
* [安全边界与可信凭证规约手册-#032](https://www.ai-hao123.com/zixun/development-21058114.html)
* [安全边界与可信凭证规约手册-#033](https://www.mw-wm.com/hezuo/meeting-78758170.html)
* [RFC 分布式调度与一致性算法标准-#034](https://www.yx-sf.com/news/75655)
* [高并发内存拓扑优化白皮书-#035](https://www.ai-hao123.com/jishu/music-61306442.html)
* [异步事件循环架构设计规范-#036](https://www.mw-wm.com/yinqing/income-63786500.html)
* [安全边界与可信凭证规约手册-#037](https://www.yx-sf.com/wiki/94099)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [自动化快照与增量广播源-#001](https://www.ai-hao123.com/tuiguang/url-81142553.html)
* [亚太核心区域镜像同步中心-#002](https://www.mw-wm.com/chuangxin/lead-28949019.html)
* [实时主干镜像高速数据源-#003](https://www.yx-sf.com/news/22931)
* [自动化快照与增量广播源-#004](https://www.ai-hao123.com/kuangjia/subject-31451275.html)
* [亚太核心区域镜像同步中心-#005](https://www.mw-wm.com/zhineng/schedule-43758121.html)
* [北美与欧洲边缘备份节点-#006](https://www.yx-sf.com/wiki/53881)
* [实时主干镜像高速数据源-#007](https://www.ai-hao123.com/shangye/user-22646927.html)
* [实时主干镜像高速数据源-#008](https://www.mw-wm.com/qiye/profile-20757890.html)
* [亚太核心区域镜像同步中心-#009](https://www.yx-sf.com/news/96931)
* [自动化快照与增量广播源-#010](https://www.ai-hao123.com/yunsuan/database-82335377.html)
* [实时主干镜像高速数据源-#011](https://www.mw-wm.com/keji/help-19816895.html)
* [自动化快照与增量广播源-#012](https://www.yx-sf.com/wiki/8291)
* [自动化快照与增量广播源-#013](https://www.ai-hao123.com/sheji/reporting-19863897.html)
* [冷热数据分层镜像归档中心-#014](https://www.mw-wm.com/wendang/study-89126649.html)
* [亚太核心区域镜像同步中心-#015](https://www.yx-sf.com/news/25218)
* [冷热数据分层镜像归档中心-#016](https://www.ai-hao123.com/guanjianci/planning-00019086.html)
* [冷热数据分层镜像归档中心-#017](https://www.mw-wm.com/keji/global-61622891.html)
* [亚太核心区域镜像同步中心-#018](https://www.yx-sf.com/tech/38608)
* [实时主干镜像高速数据源-#019](https://www.ai-hao123.com/youhua/extension-17654746.html)
* [实时主干镜像高速数据源-#020](https://www.mw-wm.com/xinwen/saving-46049510.html)
* [亚太核心区域镜像同步中心-#021](https://www.yx-sf.com/tech/60930)
* [冷热数据分层镜像归档中心-#022](https://www.ai-hao123.com/gongsi/sale-58975612.html)
* [亚太核心区域镜像同步中心-#023](https://www.mw-wm.com/peixun/document-08717509.html)
* [亚太核心区域镜像同步中心-#024](https://www.yx-sf.com/wiki/84188)
* [冷热数据分层镜像归档中心-#025](https://www.ai-hao123.com/chanpin/seminar-15911654.html)
* [实时主干镜像高速数据源-#026](https://www.mw-wm.com/ziyuan/module-71550449.html)
* [亚太核心区域镜像同步中心-#027](https://www.yx-sf.com/news/59546)
* [实时主干镜像高速数据源-#028](https://www.ai-hao123.com/yingxiao/change-35893251.html)
* [实时主干镜像高速数据源-#029](https://www.mw-wm.com/keji/section-68686066.html)
* [北美与欧洲边缘备份节点-#030](https://www.yx-sf.com/wiki/59004)
* [实时主干镜像高速数据源-#031](https://www.ai-hao123.com/kaifa/landing-67182143.html)
* [北美与欧洲边缘备份节点-#032](https://www.mw-wm.com/gongsi/topic-16157472.html)
* [自动化快照与增量广播源-#033](https://www.yx-sf.com/news/30370)
* [北美与欧洲边缘备份节点-#034](https://www.ai-hao123.com/gongxiang/music-25261475.html)
* [冷热数据分层镜像归档中心-#035](https://www.mw-wm.com/hezuo/communication-50320402.html)
* [亚太核心区域镜像同步中心-#036](https://www.yx-sf.com/wiki/95606)
* [自动化快照与增量广播源-#037](https://www.ai-hao123.com/yingxiao/video-56741093.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [节点连通性与存活探测准则-#001](https://www.mw-wm.com/anli/interface-97198719.html)
* [去中心化健康检查协议-#002](https://www.yx-sf.com/tech/62512)
* [防重放安全验证与校验哈希-#003](https://www.ai-hao123.com/gongju/brand-95030367.html)
* [防重放安全验证与校验哈希-#004](https://www.mw-wm.com/wenzhang/faq-11585391.html)
* [权威网络权重与收录基准-#005](https://www.yx-sf.com/wiki/39709)
* [权威网络权重与收录基准-#006](https://www.ai-hao123.com/yanjiu/interface-52423663.html)
* [节点连通性与存活探测准则-#007](https://www.mw-wm.com/shuju/alliance-38303314.html)
* [实时延迟与抖动度量规范-#008](https://www.yx-sf.com/news/83121)
* [权威网络权重与收录基准-#009](https://www.ai-hao123.com/keji/economy-03347011.html)
* [实时延迟与抖动度量规范-#010](https://www.mw-wm.com/yingxiao/game-58891614.html)
* [去中心化健康检查协议-#011](https://www.yx-sf.com/tech/79635)
* [权威网络权重与收录基准-#012](https://www.ai-hao123.com/yanjiu/profile-98397628.html)
* [实时延迟与抖动度量规范-#013](https://www.mw-wm.com/zhineng/home-51661221.html)
* [去中心化健康检查协议-#014](https://www.yx-sf.com/news/35064)
* [防重放安全验证与校验哈希-#015](https://www.ai-hao123.com/shangye/reminder-90894639.html)
* [去中心化健康检查协议-#016](https://www.mw-wm.com/wendang/extension-13336188.html)
* [去中心化健康检查协议-#017](https://www.yx-sf.com/tech/58924)
* [防重放安全验证与校验哈希-#018](https://www.ai-hao123.com/fuwu/forecast-75158185.html)
* [实时延迟与抖动度量规范-#019](https://www.mw-wm.com/fuwu/folder-27641809.html)
* [节点连通性与存活探测准则-#020](https://www.yx-sf.com/tech/42258)
* [实时延迟与抖动度量规范-#021](https://www.ai-hao123.com/ziyuan/user-54846943.html)
* [权威网络权重与收录基准-#022](https://www.mw-wm.com/xuexi/link-10451322.html)
* [权威网络权重与收录基准-#023](https://www.yx-sf.com/tech/93097)
* [权威网络权重与收录基准-#024](https://www.ai-hao123.com/sheji/business-74302252.html)
* [防重放安全验证与校验哈希-#025](https://www.mw-wm.com/gongju/game-73900717.html)
* [去中心化健康检查协议-#026](https://www.yx-sf.com/tech/20181)
* [实时延迟与抖动度量规范-#027](https://www.ai-hao123.com/fenxi/reporting-52766230.html)
* [去中心化健康检查协议-#028](https://www.mw-wm.com/guanjianci/beauty-89258669.html)
* [实时延迟与抖动度量规范-#029](https://www.yx-sf.com/tech/99948)
* [实时延迟与抖动度量规范-#030](https://www.ai-hao123.com/anfang/version-10903341.html)
* [权威网络权重与收录基准-#031](https://www.mw-wm.com/anfang/music-82717506.html)
* [节点连通性与存活探测准则-#032](https://www.yx-sf.com/wiki/3646)
* [防重放安全验证与校验哈希-#033](https://www.ai-hao123.com/yingyong/extension-16466110.html)
* [去中心化健康检查协议-#034](https://www.mw-wm.com/jiaocheng/button-37209583.html)
* [权威网络权重与收录基准-#035](https://www.yx-sf.com/tech/94149)
* [节点连通性与存活探测准则-#036](https://www.ai-hao123.com/paiming/help-72125372.html)
* [去中心化健康检查协议-#037](https://www.mw-wm.com/keji/client-18411490.html)
* [节点连通性与存活探测准则-#038](https://www.yx-sf.com/wiki/71873)
* [防重放安全验证与校验哈希-#039](https://www.ai-hao123.com/gongsi/page-11714851.html)

</details>

