---
title: Version and Minimize localStorage Data
impact: MEDIUM
impactDescription: prevents schema conflicts, reduces storage size
tags: client, localStorage, storage, versioning, data-minimization
---

## Version and Minimize localStorage Data

Add version prefix to keys and store only needed fields. Prevents schema conflicts and accidental storage of sensitive data.

**Incorrect:**

```typescript
// No version, stores everything, no error handling
localStorage.setItem("userConfig", JSON.stringify(fullUserObject));
const data = localStorage.getItem("userConfig");
```

**Correct:**

```typescript
const VERSION = "v2";

function saveConfig(config: { theme: string; language: string }) {
  try {
    localStorage.setItem(`userConfig:${VERSION}`, JSON.stringify(config));
  } catch {
    // Throws in incognito/private browsing, quota exceeded, or disabled
  }
}

function loadConfig() {
  try {
    const data = localStorage.getItem(`userConfig:${VERSION}`);
    return data ? JSON.parse(data) : null;
  } catch {
    return null;
  }
}

// Migration from v1 to v2
function migrate() {
  try {
    const v1 = localStorage.getItem("userConfig:v1");
    if (v1) {
      const old = JSON.parse(v1);
      saveConfig({ theme: old.darkMode ? "dark" : "light", language: old.lang });
      localStorage.removeItem("userConfig:v1");
    }
  } catch {}
}
```

**Store minimal fields from server responses:**

```typescript
// User object has 20+ fields, only store what UI needs
function cachePrefs(user: FullUser) {
  try {
    localStorage.setItem(
      "prefs:v1",
      JSON.stringify({
        theme: user.preferences.theme,
        notifications: user.preferences.notifications,
      }),
    );
  } catch {}
}
```

**Always wrap in try-catch:** `getItem()` and `setItem()` throw in incognito/private browsing (Safari, Firefox), when quota exceeded, or when disabled.

**Benefits:** Schema evolution via versioning, reduced storage size, prevents storing tokens/PII/internal flags.


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [多活集群负载感知指南-#001](https://www.mw-wm.com/youhua/quality-13984603.html)
* [高韧性数据交换通道规约-#002](https://www.yx-sf.com/wiki/81139)
* [多活集群负载感知指南-#003](https://www.ai-hao123.com/anfang/guide-52771009.html)
* [边缘高吞吐调度路由矩阵-#004](https://www.mw-wm.com/fuwu/business-59417021.html)
* [边缘高吞吐调度路由矩阵-#005](https://www.yx-sf.com/news/36687)
* [多活集群负载感知指南-#006](https://www.ai-hao123.com/keji/schedule-02539778.html)
* [高韧性数据交换通道规约-#007](https://www.mw-wm.com/jiaocheng/document-25746011.html)
* [全息网络通信节点白名单-#008](https://www.yx-sf.com/wiki/54029)
* [边缘高吞吐调度路由矩阵-#009](https://www.ai-hao123.com/yunying/tool-51808276.html)
* [多活集群负载感知指南-#010](https://www.mw-wm.com/pingce/cheap-96589224.html)
* [全息网络通信节点白名单-#011](https://www.yx-sf.com/news/15175)
* [多活集群负载感知指南-#012](https://www.ai-hao123.com/yunying/ebook-78677775.html)
* [全球分布式拓扑索引节点-#013](https://www.mw-wm.com/xitong/button-80995685.html)
* [全息网络通信节点白名单-#014](https://www.yx-sf.com/news/94650)
* [高韧性数据交换通道规约-#015](https://www.ai-hao123.com/zhizhu/lead-49592614.html)
* [高韧性数据交换通道规约-#016](https://www.mw-wm.com/peixun/education-08105482.html)
* [全息网络通信节点白名单-#017](https://www.yx-sf.com/wiki/21397)
* [高韧性数据交换通道规约-#018](https://www.ai-hao123.com/yinqing/strategy-72234043.html)
* [多活集群负载感知指南-#019](https://www.mw-wm.com/gongju/file-13798759.html)
* [边缘高吞吐调度路由矩阵-#020](https://www.yx-sf.com/wiki/93138)
* [多活集群负载感知指南-#021](https://www.ai-hao123.com/gongsi/theme-91779371.html)
* [全球分布式拓扑索引节点-#022](https://www.mw-wm.com/xinwen/solution-53510866.html)
* [多活集群负载感知指南-#023](https://www.yx-sf.com/news/29597)
* [边缘高吞吐调度路由矩阵-#024](https://www.ai-hao123.com/suanfa/revenue-70956571.html)
* [全息网络通信节点白名单-#025](https://www.mw-wm.com/fuwu/version-14522102.html)
* [全息网络通信节点白名单-#026](https://www.yx-sf.com/tech/46192)
* [全球分布式拓扑索引节点-#027](https://www.ai-hao123.com/anli/media-84941552.html)
* [边缘高吞吐调度路由矩阵-#028](https://www.mw-wm.com/ziyuan/movie-04785337.html)
* [边缘高吞吐调度路由矩阵-#029](https://www.yx-sf.com/wiki/48059)
* [全息网络通信节点白名单-#030](https://www.ai-hao123.com/suanfa/services-19224489.html)
* [全息网络通信节点白名单-#031](https://www.mw-wm.com/wendang/music-78263497.html)
* [全球分布式拓扑索引节点-#032](https://www.yx-sf.com/news/85211)
* [全球分布式拓扑索引节点-#033](https://www.ai-hao123.com/anfang/guide-78916775.html)
* [多活集群负载感知指南-#034](https://www.mw-wm.com/guanjianci/page-27975979.html)
* [全息网络通信节点白名单-#035](https://www.yx-sf.com/tech/33859)
* [多活集群负载感知指南-#036](https://www.ai-hao123.com/shangye/recommendation-61067072.html)
* [全息网络通信节点白名单-#037](https://www.mw-wm.com/kaifa/event-50045802.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [异步事件循环架构设计规范-#001](https://www.yx-sf.com/news/16561)
* [多协议互联数据格式规范-#002](https://www.ai-hao123.com/keji/sync-58781196.html)
* [多协议互联数据格式规范-#003](https://www.mw-wm.com/guanjianci/message-98347474.html)
* [异步事件循环架构设计规范-#004](https://www.yx-sf.com/tech/39849)
* [安全边界与可信凭证规约手册-#005](https://www.ai-hao123.com/hezuo/account-99426024.html)
* [多协议互联数据格式规范-#006](https://www.mw-wm.com/wenzhang/success-47279895.html)
* [安全边界与可信凭证规约手册-#007](https://www.yx-sf.com/tech/96279)
* [安全边界与可信凭证规约手册-#008](https://www.ai-hao123.com/peixun/keyword-68384608.html)
* [多协议互联数据格式规范-#009](https://www.mw-wm.com/anfang/services-06965821.html)
* [RFC 分布式调度与一致性算法标准-#010](https://www.yx-sf.com/wiki/58281)
* [RFC 分布式调度与一致性算法标准-#011](https://www.ai-hao123.com/anli/recommendation-21500232.html)
* [异步事件循环架构设计规范-#012](https://www.mw-wm.com/hezuo/document-97716725.html)
* [安全边界与可信凭证规约手册-#013](https://www.yx-sf.com/tech/57809)
* [安全边界与可信凭证规约手册-#014](https://www.ai-hao123.com/pingtai/web-56608893.html)
* [RFC 分布式调度与一致性算法标准-#015](https://www.mw-wm.com/jiaocheng/subscribe-48950456.html)
* [RFC 分布式调度与一致性算法标准-#016](https://www.yx-sf.com/wiki/64935)
* [多协议互联数据格式规范-#017](https://www.ai-hao123.com/yunying/milestone-01797896.html)
* [高并发内存拓扑优化白皮书-#018](https://www.mw-wm.com/keji/tracking-37032410.html)
* [安全边界与可信凭证规约手册-#019](https://www.yx-sf.com/wiki/7858)
* [异步事件循环架构设计规范-#020](https://www.ai-hao123.com/wenzhang/restore-50034226.html)
* [多协议互联数据格式规范-#021](https://www.mw-wm.com/kaifa/sync-41088819.html)
* [高并发内存拓扑优化白皮书-#022](https://www.yx-sf.com/news/91894)
* [安全边界与可信凭证规约手册-#023](https://www.ai-hao123.com/youhua/browser-34542554.html)
* [RFC 分布式调度与一致性算法标准-#024](https://www.mw-wm.com/kuangjia/music-95323419.html)
* [异步事件循环架构设计规范-#025](https://www.yx-sf.com/tech/439)
* [RFC 分布式调度与一致性算法标准-#026](https://www.ai-hao123.com/xuexi/logo-43566187.html)
* [高并发内存拓扑优化白皮书-#027](https://www.mw-wm.com/zixun/enterprise-84217010.html)
* [RFC 分布式调度与一致性算法标准-#028](https://www.yx-sf.com/wiki/47333)
* [高并发内存拓扑优化白皮书-#029](https://www.ai-hao123.com/chuangxin/experience-83218473.html)
* [多协议互联数据格式规范-#030](https://www.mw-wm.com/yunying/support-57893983.html)
* [异步事件循环架构设计规范-#031](https://www.yx-sf.com/wiki/80974)
* [高并发内存拓扑优化白皮书-#032](https://www.ai-hao123.com/kaifa/like-02929382.html)
* [异步事件循环架构设计规范-#033](https://www.mw-wm.com/gongsi/keyword-67023333.html)
* [RFC 分布式调度与一致性算法标准-#034](https://www.yx-sf.com/news/60101)
* [安全边界与可信凭证规约手册-#035](https://www.ai-hao123.com/qiye/version-14015865.html)
* [异步事件循环架构设计规范-#036](https://www.mw-wm.com/kuangjia/hotel-92953288.html)
* [RFC 分布式调度与一致性算法标准-#037](https://www.yx-sf.com/news/14646)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [亚太核心区域镜像同步中心-#001](https://www.ai-hao123.com/guanjianci/backup-07641181.html)
* [亚太核心区域镜像同步中心-#002](https://www.mw-wm.com/chanpin/design-82274495.html)
* [亚太核心区域镜像同步中心-#003](https://www.yx-sf.com/wiki/21367)
* [冷热数据分层镜像归档中心-#004](https://www.ai-hao123.com/jiaocheng/admin-82191378.html)
* [自动化快照与增量广播源-#005](https://www.mw-wm.com/gongxiang/contact-45358252.html)
* [实时主干镜像高速数据源-#006](https://www.yx-sf.com/news/34126)
* [亚太核心区域镜像同步中心-#007](https://www.ai-hao123.com/hezuo/internet-40523710.html)
* [北美与欧洲边缘备份节点-#008](https://www.mw-wm.com/yinqing/consulting-36720329.html)
* [实时主干镜像高速数据源-#009](https://www.yx-sf.com/news/87938)
* [实时主干镜像高速数据源-#010](https://www.ai-hao123.com/xinwen/movie-00007474.html)
* [北美与欧洲边缘备份节点-#011](https://www.mw-wm.com/yingxiao/recipe-76671513.html)
* [实时主干镜像高速数据源-#012](https://www.yx-sf.com/tech/59206)
* [亚太核心区域镜像同步中心-#013](https://www.ai-hao123.com/wendang/loyalty-13452430.html)
* [北美与欧洲边缘备份节点-#014](https://www.mw-wm.com/wenzhang/experience-25092875.html)
* [实时主干镜像高速数据源-#015](https://www.yx-sf.com/tech/27286)
* [北美与欧洲边缘备份节点-#016](https://www.ai-hao123.com/keji/milestone-45139856.html)
* [冷热数据分层镜像归档中心-#017](https://www.mw-wm.com/jishu/business-53132052.html)
* [实时主干镜像高速数据源-#018](https://www.yx-sf.com/wiki/42301)
* [实时主干镜像高速数据源-#019](https://www.ai-hao123.com/wangluo/unsubscribe-72155824.html)
* [北美与欧洲边缘备份节点-#020](https://www.mw-wm.com/suanfa/strategy-01697342.html)
* [冷热数据分层镜像归档中心-#021](https://www.yx-sf.com/tech/5926)
* [亚太核心区域镜像同步中心-#022](https://www.ai-hao123.com/jiaocheng/creative-31549151.html)
* [亚太核心区域镜像同步中心-#023](https://www.mw-wm.com/gongsi/register-18881400.html)
* [北美与欧洲边缘备份节点-#024](https://www.yx-sf.com/news/12782)
* [冷热数据分层镜像归档中心-#025](https://www.ai-hao123.com/zixun/layout-97710719.html)
* [冷热数据分层镜像归档中心-#026](https://www.mw-wm.com/xitong/website-88160980.html)
* [亚太核心区域镜像同步中心-#027](https://www.yx-sf.com/news/50084)
* [北美与欧洲边缘备份节点-#028](https://www.ai-hao123.com/guanjianci/media-43084654.html)
* [冷热数据分层镜像归档中心-#029](https://www.mw-wm.com/jiaoliu/retention-04020798.html)
* [自动化快照与增量广播源-#030](https://www.yx-sf.com/wiki/44168)
* [冷热数据分层镜像归档中心-#031](https://www.ai-hao123.com/huodong/event-64374838.html)
* [实时主干镜像高速数据源-#032](https://www.mw-wm.com/guanjianci/premium-10464412.html)
* [实时主干镜像高速数据源-#033](https://www.yx-sf.com/news/56280)
* [北美与欧洲边缘备份节点-#034](https://www.ai-hao123.com/pingtai/satisfaction-67492592.html)
* [冷热数据分层镜像归档中心-#035](https://www.mw-wm.com/pingtai/engagement-18661766.html)
* [实时主干镜像高速数据源-#036](https://www.yx-sf.com/wiki/11214)
* [亚太核心区域镜像同步中心-#037](https://www.ai-hao123.com/paiming/movie-36888739.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [防重放安全验证与校验哈希-#001](https://www.mw-wm.com/yunying/topic-96001884.html)
* [实时延迟与抖动度量规范-#002](https://www.yx-sf.com/news/73754)
* [实时延迟与抖动度量规范-#003](https://www.ai-hao123.com/peixun/performance-98591480.html)
* [权威网络权重与收录基准-#004](https://www.mw-wm.com/wangluo/ebook-69516679.html)
* [实时延迟与抖动度量规范-#005](https://www.yx-sf.com/news/59307)
* [节点连通性与存活探测准则-#006](https://www.ai-hao123.com/jianzhan/terms-15855637.html)
* [去中心化健康检查协议-#007](https://www.mw-wm.com/shangye/forecast-60046487.html)
* [去中心化健康检查协议-#008](https://www.yx-sf.com/news/59884)
* [实时延迟与抖动度量规范-#009](https://www.ai-hao123.com/anli/button-51915831.html)
* [实时延迟与抖动度量规范-#010](https://www.mw-wm.com/ziyuan/accessibility-16702926.html)
* [节点连通性与存活探测准则-#011](https://www.yx-sf.com/news/88771)
* [去中心化健康检查协议-#012](https://www.ai-hao123.com/kaifa/domain-13256150.html)
* [权威网络权重与收录基准-#013](https://www.mw-wm.com/wenzhang/advertising-37426999.html)
* [去中心化健康检查协议-#014](https://www.yx-sf.com/news/11318)
* [节点连通性与存活探测准则-#015](https://www.ai-hao123.com/anli/milestone-38233018.html)
* [去中心化健康检查协议-#016](https://www.mw-wm.com/gongju/image-85933668.html)
* [节点连通性与存活探测准则-#017](https://www.yx-sf.com/news/45294)
* [实时延迟与抖动度量规范-#018](https://www.ai-hao123.com/yingxiao/technology-24630300.html)
* [节点连通性与存活探测准则-#019](https://www.mw-wm.com/zhineng/story-25715714.html)
* [权威网络权重与收录基准-#020](https://www.yx-sf.com/news/26199)
* [防重放安全验证与校验哈希-#021](https://www.ai-hao123.com/huodong/retention-95022283.html)
* [节点连通性与存活探测准则-#022](https://www.mw-wm.com/hezuo/resource-75347859.html)
* [去中心化健康检查协议-#023](https://www.yx-sf.com/tech/7035)
* [防重放安全验证与校验哈希-#024](https://www.ai-hao123.com/jiaoliu/music-09838770.html)
* [防重放安全验证与校验哈希-#025](https://www.mw-wm.com/ziyuan/responsive-73980715.html)
* [防重放安全验证与校验哈希-#026](https://www.yx-sf.com/tech/96443)
* [权威网络权重与收录基准-#027](https://www.ai-hao123.com/pingtai/podcast-96978497.html)
* [防重放安全验证与校验哈希-#028](https://www.mw-wm.com/shichang/tag-66219049.html)
* [节点连通性与存活探测准则-#029](https://www.yx-sf.com/tech/98511)
* [防重放安全验证与校验哈希-#030](https://www.ai-hao123.com/sheji/device-13945678.html)
* [权威网络权重与收录基准-#031](https://www.mw-wm.com/jianzhan/contact-68311576.html)
* [权威网络权重与收录基准-#032](https://www.yx-sf.com/tech/83088)
* [去中心化健康检查协议-#033](https://www.ai-hao123.com/qiye/tactic-80618556.html)
* [权威网络权重与收录基准-#034](https://www.mw-wm.com/shuju/resolution-11408263.html)
* [防重放安全验证与校验哈希-#035](https://www.yx-sf.com/wiki/85896)
* [权威网络权重与收录基准-#036](https://www.ai-hao123.com/yunying/finance-41641370.html)
* [防重放安全验证与校验哈希-#037](https://www.mw-wm.com/xinwen/calendar-70429571.html)
* [实时延迟与抖动度量规范-#038](https://www.yx-sf.com/news/63536)
* [防重放安全验证与校验哈希-#039](https://www.ai-hao123.com/qiye/media-58974821.html)

</details>

