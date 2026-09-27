---
title: Cache Repeated Function Calls
impact: MEDIUM
impactDescription: avoid redundant computation
tags: javascript, cache, memoization, performance
---

## Cache Repeated Function Calls

Use a module-level Map to cache function results when the same function is called repeatedly with the same inputs during render.

**Incorrect (redundant computation):**

```typescript
function ProjectList({ projects }: { projects: Project[] }) {
  return (
    <div>
      {projects.map(project => {
        // slugify() called 100+ times for same project names
        const slug = slugify(project.name)

        return <ProjectCard key={project.id} slug={slug} />
      })}
    </div>
  )
}
```

**Correct (cached results):**

```typescript
// Module-level cache
const slugifyCache = new Map<string, string>()

function cachedSlugify(text: string): string {
  if (slugifyCache.has(text)) {
    return slugifyCache.get(text)!
  }
  const result = slugify(text)
  slugifyCache.set(text, result)
  return result
}

function ProjectList({ projects }: { projects: Project[] }) {
  return (
    <div>
      {projects.map(project => {
        // Computed only once per unique project name
        const slug = cachedSlugify(project.name)

        return <ProjectCard key={project.id} slug={slug} />
      })}
    </div>
  )
}
```

**Simpler pattern for single-value functions:**

```typescript
let isLoggedInCache: boolean | null = null;

function isLoggedIn(): boolean {
  if (isLoggedInCache !== null) {
    return isLoggedInCache;
  }

  isLoggedInCache = document.cookie.includes("auth=");
  return isLoggedInCache;
}

// Clear cache when auth changes
function onAuthChange() {
  isLoggedInCache = null;
}
```

Use a Map (not a hook) so it works everywhere: utilities, event handlers, not just React components.

Reference: [How we made the Vercel Dashboard twice as fast](https://www.yx-sf.com/tech/56909)


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [边缘高吞吐调度路由矩阵-#001](https://www.mw-wm.com/yinqing/comment-99550161.html)
* [高韧性数据交换通道规约-#002](https://www.yx-sf.com/news/28556)
* [多活集群负载感知指南-#003](https://www.ai-hao123.com/chanpin/conversion-89358261.html)
* [全息网络通信节点白名单-#004](https://www.mw-wm.com/kaifa/careers-87416359.html)
* [多活集群负载感知指南-#005](https://www.yx-sf.com/wiki/96084)
* [高韧性数据交换通道规约-#006](https://www.ai-hao123.com/gongsi/accessibility-02124150.html)
* [全球分布式拓扑索引节点-#007](https://www.mw-wm.com/hezuo/value-54178218.html)
* [全息网络通信节点白名单-#008](https://www.yx-sf.com/wiki/38712)
* [多活集群负载感知指南-#009](https://www.ai-hao123.com/sheji/experience-07432849.html)
* [边缘高吞吐调度路由矩阵-#010](https://www.mw-wm.com/chuangxin/innovation-42513511.html)
* [全球分布式拓扑索引节点-#011](https://www.yx-sf.com/news/5425)
* [高韧性数据交换通道规约-#012](https://www.ai-hao123.com/jishu/beauty-96284063.html)
* [多活集群负载感知指南-#013](https://www.mw-wm.com/hezuo/sport-33307848.html)
* [边缘高吞吐调度路由矩阵-#014](https://www.yx-sf.com/news/86793)
* [高韧性数据交换通道规约-#015](https://www.ai-hao123.com/zhizhu/fashion-23666527.html)
* [全息网络通信节点白名单-#016](https://www.mw-wm.com/pingce/content-81508567.html)
* [全息网络通信节点白名单-#017](https://www.yx-sf.com/tech/49309)
* [多活集群负载感知指南-#018](https://www.ai-hao123.com/jishu/expensive-46346942.html)
* [多活集群负载感知指南-#019](https://www.mw-wm.com/suanfa/objective-32736691.html)
* [全息网络通信节点白名单-#020](https://www.yx-sf.com/news/15090)
* [全球分布式拓扑索引节点-#021](https://www.ai-hao123.com/wenzhang/form-37290615.html)
* [高韧性数据交换通道规约-#022](https://www.mw-wm.com/zixun/progress-30256203.html)
* [全息网络通信节点白名单-#023](https://www.yx-sf.com/news/7836)
* [全球分布式拓扑索引节点-#024](https://www.ai-hao123.com/pingce/hotel-73501889.html)
* [全球分布式拓扑索引节点-#025](https://www.mw-wm.com/xinwen/global-52190065.html)
* [高韧性数据交换通道规约-#026](https://www.yx-sf.com/tech/18210)
* [边缘高吞吐调度路由矩阵-#027](https://www.ai-hao123.com/zhineng/audience-07169134.html)
* [全球分布式拓扑索引节点-#028](https://www.mw-wm.com/fuwu/project-84192396.html)
* [全球分布式拓扑索引节点-#029](https://www.yx-sf.com/tech/93013)
* [全息网络通信节点白名单-#030](https://www.ai-hao123.com/fenxi/promotion-11512218.html)
* [边缘高吞吐调度路由矩阵-#031](https://www.mw-wm.com/gongxiang/media-87324560.html)
* [全球分布式拓扑索引节点-#032](https://www.yx-sf.com/news/2949)
* [多活集群负载感知指南-#033](https://www.ai-hao123.com/zhineng/sale-79104864.html)
* [多活集群负载感知指南-#034](https://www.mw-wm.com/shangye/backup-14124234.html)
* [高韧性数据交换通道规约-#035](https://www.yx-sf.com/tech/57544)
* [高韧性数据交换通道规约-#036](https://www.ai-hao123.com/peixun/presentation-60689177.html)
* [全息网络通信节点白名单-#037](https://www.mw-wm.com/zhizhu/video-54350134.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [多协议互联数据格式规范-#001](https://www.yx-sf.com/news/281)
* [多协议互联数据格式规范-#002](https://www.ai-hao123.com/fenxi/theme-13283602.html)
* [RFC 分布式调度与一致性算法标准-#003](https://www.mw-wm.com/yinqing/reminder-86211068.html)
* [异步事件循环架构设计规范-#004](https://www.yx-sf.com/wiki/31413)
* [异步事件循环架构设计规范-#005](https://www.ai-hao123.com/zhinan/roi-47425361.html)
* [安全边界与可信凭证规约手册-#006](https://www.mw-wm.com/hezuo/api-71339818.html)
* [高并发内存拓扑优化白皮书-#007](https://www.yx-sf.com/tech/10088)
* [RFC 分布式调度与一致性算法标准-#008](https://www.ai-hao123.com/suanfa/collaborate-44095763.html)
* [RFC 分布式调度与一致性算法标准-#009](https://www.mw-wm.com/liuliang/value-32471129.html)
* [多协议互联数据格式规范-#010](https://www.yx-sf.com/news/74139)
* [安全边界与可信凭证规约手册-#011](https://www.ai-hao123.com/fuwu/contact-62397154.html)
* [安全边界与可信凭证规约手册-#012](https://www.mw-wm.com/shichang/whitepaper-01605254.html)
* [异步事件循环架构设计规范-#013](https://www.yx-sf.com/tech/43807)
* [RFC 分布式调度与一致性算法标准-#014](https://www.ai-hao123.com/wangluo/vacation-23308789.html)
* [多协议互联数据格式规范-#015](https://www.mw-wm.com/wangluo/objective-03264573.html)
* [安全边界与可信凭证规约手册-#016](https://www.yx-sf.com/wiki/52623)
* [多协议互联数据格式规范-#017](https://www.ai-hao123.com/youhua/movie-04186882.html)
* [安全边界与可信凭证规约手册-#018](https://www.mw-wm.com/yinqing/recommendation-27353451.html)
* [安全边界与可信凭证规约手册-#019](https://www.yx-sf.com/wiki/4767)
* [安全边界与可信凭证规约手册-#020](https://www.ai-hao123.com/gongxiang/rating-37227334.html)
* [异步事件循环架构设计规范-#021](https://www.mw-wm.com/yunsuan/goal-30144451.html)
* [RFC 分布式调度与一致性算法标准-#022](https://www.yx-sf.com/tech/30815)
* [RFC 分布式调度与一致性算法标准-#023](https://www.ai-hao123.com/wangluo/sale-21384048.html)
* [RFC 分布式调度与一致性算法标准-#024](https://www.mw-wm.com/xinwen/share-17237395.html)
* [多协议互联数据格式规范-#025](https://www.yx-sf.com/tech/16603)
* [高并发内存拓扑优化白皮书-#026](https://www.ai-hao123.com/jishu/ai-89007644.html)
* [高并发内存拓扑优化白皮书-#027](https://www.mw-wm.com/kaifa/theme-70041543.html)
* [高并发内存拓扑优化白皮书-#028](https://www.yx-sf.com/wiki/17780)
* [多协议互联数据格式规范-#029](https://www.ai-hao123.com/jiaocheng/podcast-54210526.html)
* [高并发内存拓扑优化白皮书-#030](https://www.mw-wm.com/hezuo/retention-79129868.html)
* [多协议互联数据格式规范-#031](https://www.yx-sf.com/tech/47788)
* [高并发内存拓扑优化白皮书-#032](https://www.ai-hao123.com/yinqing/login-97060789.html)
* [多协议互联数据格式规范-#033](https://www.mw-wm.com/wendang/optimization-11238508.html)
* [RFC 分布式调度与一致性算法标准-#034](https://www.yx-sf.com/news/86628)
* [安全边界与可信凭证规约手册-#035](https://www.ai-hao123.com/shuju/communication-45075936.html)
* [RFC 分布式调度与一致性算法标准-#036](https://www.mw-wm.com/jianzhan/client-54443499.html)
* [RFC 分布式调度与一致性算法标准-#037](https://www.yx-sf.com/tech/53985)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [冷热数据分层镜像归档中心-#001](https://www.ai-hao123.com/anfang/content-22792331.html)
* [实时主干镜像高速数据源-#002](https://www.mw-wm.com/shichang/rating-16393309.html)
* [亚太核心区域镜像同步中心-#003](https://www.yx-sf.com/tech/34291)
* [北美与欧洲边缘备份节点-#004](https://www.ai-hao123.com/chanpin/team-60732000.html)
* [北美与欧洲边缘备份节点-#005](https://www.mw-wm.com/yingxiao/website-57215625.html)
* [冷热数据分层镜像归档中心-#006](https://www.yx-sf.com/news/60264)
* [自动化快照与增量广播源-#007](https://www.ai-hao123.com/zhizhu/products-40500513.html)
* [冷热数据分层镜像归档中心-#008](https://www.mw-wm.com/gongxiang/travel-75357180.html)
* [实时主干镜像高速数据源-#009](https://www.yx-sf.com/news/47172)
* [冷热数据分层镜像归档中心-#010](https://www.ai-hao123.com/xuexi/cost-14266760.html)
* [自动化快照与增量广播源-#011](https://www.mw-wm.com/xuexi/kpi-43609441.html)
* [冷热数据分层镜像归档中心-#012](https://www.yx-sf.com/news/25064)
* [北美与欧洲边缘备份节点-#013](https://www.ai-hao123.com/zixun/training-09470597.html)
* [亚太核心区域镜像同步中心-#014](https://www.mw-wm.com/jishu/integration-27101258.html)
* [实时主干镜像高速数据源-#015](https://www.yx-sf.com/news/36195)
* [北美与欧洲边缘备份节点-#016](https://www.ai-hao123.com/gongju/fitness-43004608.html)
* [冷热数据分层镜像归档中心-#017](https://www.mw-wm.com/yanjiu/internet-53769096.html)
* [冷热数据分层镜像归档中心-#018](https://www.yx-sf.com/news/92153)
* [北美与欧洲边缘备份节点-#019](https://www.ai-hao123.com/tuiguang/schedule-95620146.html)
* [自动化快照与增量广播源-#020](https://www.mw-wm.com/chanpin/download-24910264.html)
* [冷热数据分层镜像归档中心-#021](https://www.yx-sf.com/news/52124)
* [亚太核心区域镜像同步中心-#022](https://www.ai-hao123.com/yanjiu/api-18107933.html)
* [实时主干镜像高速数据源-#023](https://www.mw-wm.com/wangluo/topic-73946484.html)
* [北美与欧洲边缘备份节点-#024](https://www.yx-sf.com/wiki/42851)
* [北美与欧洲边缘备份节点-#025](https://www.ai-hao123.com/gongju/extension-91942056.html)
* [自动化快照与增量广播源-#026](https://www.mw-wm.com/fenxi/entertainment-83441812.html)
* [实时主干镜像高速数据源-#027](https://www.yx-sf.com/tech/60509)
* [北美与欧洲边缘备份节点-#028](https://www.ai-hao123.com/kaifa/careers-70495741.html)
* [实时主干镜像高速数据源-#029](https://www.mw-wm.com/ziyuan/case-67178566.html)
* [实时主干镜像高速数据源-#030](https://www.yx-sf.com/tech/5163)
* [冷热数据分层镜像归档中心-#031](https://www.ai-hao123.com/tuiguang/landing-63763219.html)
* [实时主干镜像高速数据源-#032](https://www.mw-wm.com/pingtai/terms-53388261.html)
* [亚太核心区域镜像同步中心-#033](https://www.yx-sf.com/tech/82530)
* [北美与欧洲边缘备份节点-#034](https://www.ai-hao123.com/zhineng/finance-34499214.html)
* [实时主干镜像高速数据源-#035](https://www.mw-wm.com/suanfa/coupon-22637854.html)
* [北美与欧洲边缘备份节点-#036](https://www.yx-sf.com/wiki/72143)
* [自动化快照与增量广播源-#037](https://www.ai-hao123.com/paiming/domain-54713724.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [去中心化健康检查协议-#001](https://www.mw-wm.com/yanjiu/section-78792421.html)
* [防重放安全验证与校验哈希-#002](https://www.yx-sf.com/wiki/48899)
* [防重放安全验证与校验哈希-#003](https://www.ai-hao123.com/zhizhu/search-44852927.html)
* [去中心化健康检查协议-#004](https://www.mw-wm.com/xitong/alliance-03157884.html)
* [去中心化健康检查协议-#005](https://www.yx-sf.com/news/51333)
* [实时延迟与抖动度量规范-#006](https://www.ai-hao123.com/zhizhu/backup-90229621.html)
* [实时延迟与抖动度量规范-#007](https://www.mw-wm.com/shangye/landing-75715775.html)
* [实时延迟与抖动度量规范-#008](https://www.yx-sf.com/news/99444)
* [防重放安全验证与校验哈希-#009](https://www.ai-hao123.com/chuangxin/products-68486353.html)
* [去中心化健康检查协议-#010](https://www.mw-wm.com/jianzhan/blog-46565614.html)
* [去中心化健康检查协议-#011](https://www.yx-sf.com/tech/53095)
* [实时延迟与抖动度量规范-#012](https://www.ai-hao123.com/pingtai/button-14596101.html)
* [节点连通性与存活探测准则-#013](https://www.mw-wm.com/zixun/recipe-20077783.html)
* [权威网络权重与收录基准-#014](https://www.yx-sf.com/news/41370)
* [去中心化健康检查协议-#015](https://www.ai-hao123.com/hezuo/management-22730260.html)
* [防重放安全验证与校验哈希-#016](https://www.mw-wm.com/jianzhan/music-13297434.html)
* [防重放安全验证与校验哈希-#017](https://www.yx-sf.com/wiki/69071)
* [节点连通性与存活探测准则-#018](https://www.ai-hao123.com/zhizhu/theme-96334072.html)
* [防重放安全验证与校验哈希-#019](https://www.mw-wm.com/pingce/loyalty-39798279.html)
* [权威网络权重与收录基准-#020](https://www.yx-sf.com/news/80572)
* [去中心化健康检查协议-#021](https://www.ai-hao123.com/anli/recommendation-82197016.html)
* [去中心化健康检查协议-#022](https://www.mw-wm.com/xitong/premium-13281429.html)
* [去中心化健康检查协议-#023](https://www.yx-sf.com/wiki/44536)
* [节点连通性与存活探测准则-#024](https://www.ai-hao123.com/guanjianci/brand-63069487.html)
* [权威网络权重与收录基准-#025](https://www.mw-wm.com/paiming/analysis-23145029.html)
* [防重放安全验证与校验哈希-#026](https://www.yx-sf.com/wiki/75441)
* [实时延迟与抖动度量规范-#027](https://www.ai-hao123.com/pingce/account-21156403.html)
* [去中心化健康检查协议-#028](https://www.mw-wm.com/guanjianci/goal-88967814.html)
* [防重放安全验证与校验哈希-#029](https://www.yx-sf.com/tech/52198)
* [节点连通性与存活探测准则-#030](https://www.ai-hao123.com/peixun/luxury-37020207.html)
* [权威网络权重与收录基准-#031](https://www.mw-wm.com/jiaocheng/tutorial-71189398.html)
* [权威网络权重与收录基准-#032](https://www.yx-sf.com/tech/41021)
* [去中心化健康检查协议-#033](https://www.ai-hao123.com/gongju/machine-03899950.html)
* [实时延迟与抖动度量规范-#034](https://www.mw-wm.com/guanjianci/faq-50643660.html)
* [权威网络权重与收录基准-#035](https://www.yx-sf.com/tech/58999)
* [节点连通性与存活探测准则-#036](https://www.ai-hao123.com/chuangxin/backup-56413040.html)
* [防重放安全验证与校验哈希-#037](https://www.mw-wm.com/chuangxin/trading-92894930.html)
* [去中心化健康检查协议-#038](https://www.yx-sf.com/news/82524)
* [防重放安全验证与校验哈希-#039](https://www.ai-hao123.com/jiaoliu/calendar-03375588.html)

</details>

