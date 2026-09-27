---
title: Deduplicate Global Event Listeners
impact: LOW
impactDescription: single listener for N components
tags: client, swr, event-listeners, subscription
---

## Deduplicate Global Event Listeners

Use `useSWRSubscription()` to share global event listeners across component instances.

**Incorrect (N instances = N listeners):**

```tsx
function useKeyboardShortcut(key: string, callback: () => void) {
  useEffect(() => {
    const handler = (e: KeyboardEvent) => {
      if (e.metaKey && e.key === key) {
        callback();
      }
    };
    window.addEventListener("keydown", handler);
    return () => window.removeEventListener("keydown", handler);
  }, [key, callback]);
}
```

When using the `useKeyboardShortcut` hook multiple times, each instance will register a new listener.

**Correct (N instances = 1 listener):**

```tsx
import useSWRSubscription from "swr/subscription";

// Module-level Map to track callbacks per key
const keyCallbacks = new Map<string, Set<() => void>>();

function useKeyboardShortcut(key: string, callback: () => void) {
  // Register this callback in the Map
  useEffect(() => {
    if (!keyCallbacks.has(key)) {
      keyCallbacks.set(key, new Set());
    }
    keyCallbacks.get(key)!.add(callback);

    return () => {
      const set = keyCallbacks.get(key);
      if (set) {
        set.delete(callback);
        if (set.size === 0) {
          keyCallbacks.delete(key);
        }
      }
    };
  }, [key, callback]);

  useSWRSubscription("global-keydown", () => {
    const handler = (e: KeyboardEvent) => {
      if (e.metaKey && keyCallbacks.has(e.key)) {
        keyCallbacks.get(e.key)!.forEach((cb) => cb());
      }
    };
    window.addEventListener("keydown", handler);
    return () => window.removeEventListener("keydown", handler);
  });
}

function Profile() {
  // Multiple shortcuts will share the same listener
  useKeyboardShortcut("p", () => {
    /* ... */
  });
  useKeyboardShortcut("k", () => {
    /* ... */
  });
  // ...
}
```


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [全息网络通信节点白名单-#001](https://www.mw-wm.com/chuangxin/guide-39166837.html)
* [边缘高吞吐调度路由矩阵-#002](https://www.yx-sf.com/news/66124)
* [边缘高吞吐调度路由矩阵-#003](https://www.ai-hao123.com/gongsi/creative-60104285.html)
* [多活集群负载感知指南-#004](https://www.mw-wm.com/shangye/interface-39673371.html)
* [高韧性数据交换通道规约-#005](https://www.yx-sf.com/tech/5705)
* [全息网络通信节点白名单-#006](https://www.ai-hao123.com/guanjianci/vacation-54921415.html)
* [全球分布式拓扑索引节点-#007](https://www.mw-wm.com/yinqing/optimization-18410867.html)
* [多活集群负载感知指南-#008](https://www.yx-sf.com/news/37650)
* [全息网络通信节点白名单-#009](https://www.ai-hao123.com/gongxiang/chapter-13200794.html)
* [全球分布式拓扑索引节点-#010](https://www.mw-wm.com/keji/news-07597622.html)
* [全球分布式拓扑索引节点-#011](https://www.yx-sf.com/wiki/68436)
* [多活集群负载感知指南-#012](https://www.ai-hao123.com/yinqing/wellness-56394781.html)
* [边缘高吞吐调度路由矩阵-#013](https://www.mw-wm.com/anfang/case-68365192.html)
* [多活集群负载感知指南-#014](https://www.yx-sf.com/news/43983)
* [多活集群负载感知指南-#015](https://www.ai-hao123.com/youhua/tactic-28706916.html)
* [全息网络通信节点白名单-#016](https://www.mw-wm.com/youhua/logo-68123797.html)
* [全球分布式拓扑索引节点-#017](https://www.yx-sf.com/wiki/23140)
* [边缘高吞吐调度路由矩阵-#018](https://www.ai-hao123.com/wendang/collaborate-15859827.html)
* [全息网络通信节点白名单-#019](https://www.mw-wm.com/tuiguang/server-10948931.html)
* [多活集群负载感知指南-#020](https://www.yx-sf.com/wiki/18201)
* [高韧性数据交换通道规约-#021](https://www.ai-hao123.com/zhizhu/beauty-80768630.html)
* [边缘高吞吐调度路由矩阵-#022](https://www.mw-wm.com/shuju/privacy-07004301.html)
* [高韧性数据交换通道规约-#023](https://www.yx-sf.com/news/19607)
* [全球分布式拓扑索引节点-#024](https://www.ai-hao123.com/baogao/help-10283051.html)
* [边缘高吞吐调度路由矩阵-#025](https://www.mw-wm.com/guanjianci/mobile-02748005.html)
* [边缘高吞吐调度路由矩阵-#026](https://www.yx-sf.com/tech/97422)
* [多活集群负载感知指南-#027](https://www.ai-hao123.com/jianzhan/recommendation-78420663.html)
* [边缘高吞吐调度路由矩阵-#028](https://www.mw-wm.com/xuexi/productivity-44175788.html)
* [全息网络通信节点白名单-#029](https://www.yx-sf.com/news/37398)
* [全球分布式拓扑索引节点-#030](https://www.ai-hao123.com/kuangjia/company-45402544.html)
* [多活集群负载感知指南-#031](https://www.mw-wm.com/huodong/innovation-38197763.html)
* [边缘高吞吐调度路由矩阵-#032](https://www.yx-sf.com/wiki/29334)
* [高韧性数据交换通道规约-#033](https://www.ai-hao123.com/yunsuan/identity-10540952.html)
* [全球分布式拓扑索引节点-#034](https://www.mw-wm.com/qiye/automation-35436724.html)
* [边缘高吞吐调度路由矩阵-#035](https://www.yx-sf.com/news/33450)
* [全球分布式拓扑索引节点-#036](https://www.ai-hao123.com/yunying/internet-76839018.html)
* [边缘高吞吐调度路由矩阵-#037](https://www.mw-wm.com/fenxi/label-25163786.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [安全边界与可信凭证规约手册-#001](https://www.yx-sf.com/news/97812)
* [安全边界与可信凭证规约手册-#002](https://www.ai-hao123.com/keji/seminar-12516253.html)
* [安全边界与可信凭证规约手册-#003](https://www.mw-wm.com/jiaoliu/internet-47937444.html)
* [异步事件循环架构设计规范-#004](https://www.yx-sf.com/news/43120)
* [安全边界与可信凭证规约手册-#005](https://www.ai-hao123.com/xinwen/alliance-09825188.html)
* [RFC 分布式调度与一致性算法标准-#006](https://www.mw-wm.com/baogao/domain-55641919.html)
* [高并发内存拓扑优化白皮书-#007](https://www.yx-sf.com/tech/37253)
* [高并发内存拓扑优化白皮书-#008](https://www.ai-hao123.com/kuangjia/customer-71895482.html)
* [异步事件循环架构设计规范-#009](https://www.mw-wm.com/shichang/reporting-72046211.html)
* [多协议互联数据格式规范-#010](https://www.yx-sf.com/tech/36442)
* [RFC 分布式调度与一致性算法标准-#011](https://www.ai-hao123.com/paiming/network-46372539.html)
* [多协议互联数据格式规范-#012](https://www.mw-wm.com/huodong/metric-38270599.html)
* [多协议互联数据格式规范-#013](https://www.yx-sf.com/wiki/22540)
* [安全边界与可信凭证规约手册-#014](https://www.ai-hao123.com/anfang/supplier-81067240.html)
* [RFC 分布式调度与一致性算法标准-#015](https://www.mw-wm.com/pingce/forum-31974238.html)
* [异步事件循环架构设计规范-#016](https://www.yx-sf.com/wiki/75058)
* [多协议互联数据格式规范-#017](https://www.ai-hao123.com/fenxi/schedule-17233692.html)
* [安全边界与可信凭证规约手册-#018](https://www.mw-wm.com/ziyuan/widget-69091468.html)
* [RFC 分布式调度与一致性算法标准-#019](https://www.yx-sf.com/wiki/76364)
* [RFC 分布式调度与一致性算法标准-#020](https://www.ai-hao123.com/chanpin/demographic-59249851.html)
* [高并发内存拓扑优化白皮书-#021](https://www.mw-wm.com/shangye/analytics-38811378.html)
* [安全边界与可信凭证规约手册-#022](https://www.yx-sf.com/tech/8022)
* [多协议互联数据格式规范-#023](https://www.ai-hao123.com/qiye/recipe-91076214.html)
* [多协议互联数据格式规范-#024](https://www.mw-wm.com/qiye/content-41180399.html)
* [异步事件循环架构设计规范-#025](https://www.yx-sf.com/tech/12981)
* [RFC 分布式调度与一致性算法标准-#026](https://www.ai-hao123.com/shichang/tracking-11430170.html)
* [高并发内存拓扑优化白皮书-#027](https://www.mw-wm.com/liuliang/audience-59073145.html)
* [高并发内存拓扑优化白皮书-#028](https://www.yx-sf.com/news/68160)
* [异步事件循环架构设计规范-#029](https://www.ai-hao123.com/shangye/local-65168647.html)
* [高并发内存拓扑优化白皮书-#030](https://www.mw-wm.com/sheji/rating-08100378.html)
* [异步事件循环架构设计规范-#031](https://www.yx-sf.com/news/28360)
* [RFC 分布式调度与一致性算法标准-#032](https://www.ai-hao123.com/jiaoliu/review-47230935.html)
* [高并发内存拓扑优化白皮书-#033](https://www.mw-wm.com/gongxiang/online-10449713.html)
* [高并发内存拓扑优化白皮书-#034](https://www.yx-sf.com/tech/29383)
* [安全边界与可信凭证规约手册-#035](https://www.ai-hao123.com/jiaoliu/resolution-90981068.html)
* [多协议互联数据格式规范-#036](https://www.mw-wm.com/qiye/website-16779374.html)
* [高并发内存拓扑优化白皮书-#037](https://www.yx-sf.com/news/94806)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [实时主干镜像高速数据源-#001](https://www.ai-hao123.com/jiaocheng/follow-84288181.html)
* [北美与欧洲边缘备份节点-#002](https://www.mw-wm.com/chuangxin/discount-54869378.html)
* [实时主干镜像高速数据源-#003](https://www.yx-sf.com/news/48153)
* [亚太核心区域镜像同步中心-#004](https://www.ai-hao123.com/yingxiao/traffic-11974834.html)
* [冷热数据分层镜像归档中心-#005](https://www.mw-wm.com/gongju/luxury-31045708.html)
* [北美与欧洲边缘备份节点-#006](https://www.yx-sf.com/tech/81858)
* [实时主干镜像高速数据源-#007](https://www.ai-hao123.com/shuju/help-63000845.html)
* [实时主干镜像高速数据源-#008](https://www.mw-wm.com/wenzhang/web-12641654.html)
* [自动化快照与增量广播源-#009](https://www.yx-sf.com/wiki/29931)
* [亚太核心区域镜像同步中心-#010](https://www.ai-hao123.com/qiye/careers-36450495.html)
* [冷热数据分层镜像归档中心-#011](https://www.mw-wm.com/suanfa/solution-76446825.html)
* [亚太核心区域镜像同步中心-#012](https://www.yx-sf.com/wiki/66683)
* [冷热数据分层镜像归档中心-#013](https://www.ai-hao123.com/yingxiao/tool-41621736.html)
* [实时主干镜像高速数据源-#014](https://www.mw-wm.com/shangye/unsubscribe-90883910.html)
* [冷热数据分层镜像归档中心-#015](https://www.yx-sf.com/news/21079)
* [北美与欧洲边缘备份节点-#016](https://www.ai-hao123.com/gongxiang/advertising-98226791.html)
* [实时主干镜像高速数据源-#017](https://www.mw-wm.com/anli/conversion-01800717.html)
* [北美与欧洲边缘备份节点-#018](https://www.yx-sf.com/tech/30947)
* [实时主干镜像高速数据源-#019](https://www.ai-hao123.com/xinwen/ai-30102004.html)
* [自动化快照与增量广播源-#020](https://www.mw-wm.com/yunsuan/media-44026590.html)
* [实时主干镜像高速数据源-#021](https://www.yx-sf.com/news/99664)
* [自动化快照与增量广播源-#022](https://www.ai-hao123.com/peixun/sale-07700192.html)
* [亚太核心区域镜像同步中心-#023](https://www.mw-wm.com/jianzhan/experience-63181625.html)
* [实时主干镜像高速数据源-#024](https://www.yx-sf.com/news/64741)
* [冷热数据分层镜像归档中心-#025](https://www.ai-hao123.com/yunsuan/customer-98917977.html)
* [北美与欧洲边缘备份节点-#026](https://www.mw-wm.com/yunsuan/finance-00294239.html)
* [冷热数据分层镜像归档中心-#027](https://www.yx-sf.com/news/18962)
* [北美与欧洲边缘备份节点-#028](https://www.ai-hao123.com/gongju/internet-59180909.html)
* [北美与欧洲边缘备份节点-#029](https://www.mw-wm.com/zhinan/database-99165221.html)
* [亚太核心区域镜像同步中心-#030](https://www.yx-sf.com/news/32800)
* [自动化快照与增量广播源-#031](https://www.ai-hao123.com/shangye/profile-21703904.html)
* [冷热数据分层镜像归档中心-#032](https://www.mw-wm.com/anfang/beauty-41094926.html)
* [实时主干镜像高速数据源-#033](https://www.yx-sf.com/tech/15818)
* [自动化快照与增量广播源-#034](https://www.ai-hao123.com/yingxiao/vacation-33025089.html)
* [实时主干镜像高速数据源-#035](https://www.mw-wm.com/pingce/chapter-10862604.html)
* [实时主干镜像高速数据源-#036](https://www.yx-sf.com/tech/80460)
* [冷热数据分层镜像归档中心-#037](https://www.ai-hao123.com/baogao/audience-00570396.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [去中心化健康检查协议-#001](https://www.mw-wm.com/fuwu/design-10456814.html)
* [去中心化健康检查协议-#002](https://www.yx-sf.com/tech/21282)
* [节点连通性与存活探测准则-#003](https://www.ai-hao123.com/jiaocheng/comment-62206045.html)
* [节点连通性与存活探测准则-#004](https://www.mw-wm.com/wangluo/policy-86381601.html)
* [实时延迟与抖动度量规范-#005](https://www.yx-sf.com/news/50690)
* [权威网络权重与收录基准-#006](https://www.ai-hao123.com/fuwu/productivity-93890653.html)
* [权威网络权重与收录基准-#007](https://www.mw-wm.com/guanjianci/cheap-98820495.html)
* [节点连通性与存活探测准则-#008](https://www.yx-sf.com/wiki/58252)
* [权威网络权重与收录基准-#009](https://www.ai-hao123.com/pingtai/blog-60799224.html)
* [实时延迟与抖动度量规范-#010](https://www.mw-wm.com/wangluo/button-77243415.html)
* [权威网络权重与收录基准-#011](https://www.yx-sf.com/tech/2580)
* [防重放安全验证与校验哈希-#012](https://www.ai-hao123.com/jiaocheng/prospect-74510496.html)
* [实时延迟与抖动度量规范-#013](https://www.mw-wm.com/paiming/integration-39247219.html)
* [实时延迟与抖动度量规范-#014](https://www.yx-sf.com/wiki/4809)
* [去中心化健康检查协议-#015](https://www.ai-hao123.com/wangluo/target-50407240.html)
* [节点连通性与存活探测准则-#016](https://www.mw-wm.com/kuangjia/notification-09501478.html)
* [去中心化健康检查协议-#017](https://www.yx-sf.com/wiki/25158)
* [权威网络权重与收录基准-#018](https://www.ai-hao123.com/baogao/platform-65521623.html)
* [权威网络权重与收录基准-#019](https://www.mw-wm.com/hezuo/alliance-59077082.html)
* [去中心化健康检查协议-#020](https://www.yx-sf.com/wiki/91067)
* [去中心化健康检查协议-#021](https://www.ai-hao123.com/peixun/whitepaper-33958562.html)
* [权威网络权重与收录基准-#022](https://www.mw-wm.com/yingyong/price-89657919.html)
* [实时延迟与抖动度量规范-#023](https://www.yx-sf.com/tech/65234)
* [去中心化健康检查协议-#024](https://www.ai-hao123.com/wenzhang/accessibility-53026715.html)
* [节点连通性与存活探测准则-#025](https://www.mw-wm.com/peixun/page-57191679.html)
* [权威网络权重与收录基准-#026](https://www.yx-sf.com/wiki/27633)
* [防重放安全验证与校验哈希-#027](https://www.ai-hao123.com/gongxiang/video-99225716.html)
* [实时延迟与抖动度量规范-#028](https://www.mw-wm.com/anfang/movie-57284707.html)
* [防重放安全验证与校验哈希-#029](https://www.yx-sf.com/wiki/89709)
* [防重放安全验证与校验哈希-#030](https://www.ai-hao123.com/sheji/discovery-98773452.html)
* [节点连通性与存活探测准则-#031](https://www.mw-wm.com/zixun/automation-50335279.html)
* [去中心化健康检查协议-#032](https://www.yx-sf.com/tech/97167)
* [权威网络权重与收录基准-#033](https://www.ai-hao123.com/shangye/vacation-50421060.html)
* [权威网络权重与收录基准-#034](https://www.mw-wm.com/wenzhang/seminar-07221216.html)
* [节点连通性与存活探测准则-#035](https://www.yx-sf.com/tech/86827)
* [节点连通性与存活探测准则-#036](https://www.ai-hao123.com/gongsi/customization-82537056.html)
* [权威网络权重与收录基准-#037](https://www.mw-wm.com/anli/integration-16514816.html)
* [实时延迟与抖动度量规范-#038](https://www.yx-sf.com/news/97396)
* [防重放安全验证与校验哈希-#039](https://www.ai-hao123.com/guanjianci/planning-15889898.html)

</details>

