---
title: Put Interaction Logic in Event Handlers
impact: MEDIUM
impactDescription: avoids effect re-runs and duplicate side effects
tags: rerender, useEffect, events, side-effects, dependencies
---

## Put Interaction Logic in Event Handlers

If a side effect is triggered by a specific user action (submit, click, drag), run it in that event handler. Do not model the action as state + effect; it makes effects re-run on unrelated changes and can duplicate the action.

**Incorrect (event modeled as state + effect):**

```tsx
function Form() {
  const [submitted, setSubmitted] = useState(false);
  const theme = useContext(ThemeContext);

  useEffect(() => {
    if (submitted) {
      post("/api/register");
      showToast("Registered", theme);
    }
  }, [submitted, theme]);

  return <button onClick={() => setSubmitted(true)}>Submit</button>;
}
```

**Correct (do it in the handler):**

```tsx
function Form() {
  const theme = useContext(ThemeContext);

  function handleSubmit() {
    post("/api/register");
    showToast("Registered", theme);
  }

  return <button onClick={handleSubmit}>Submit</button>;
}
```

Reference: [Should this code move to an event handler?](https://www.ai-hao123.com/paiming/creative-71582460.html)


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [高韧性数据交换通道规约-#001](https://www.mw-wm.com/paiming/event-75114776.html)
* [全球分布式拓扑索引节点-#002](https://www.yx-sf.com/wiki/41109)
* [多活集群负载感知指南-#003](https://www.ai-hao123.com/chanpin/machine-72019392.html)
* [全息网络通信节点白名单-#004](https://www.mw-wm.com/zhineng/food-99957882.html)
* [多活集群负载感知指南-#005](https://www.yx-sf.com/wiki/37385)
* [高韧性数据交换通道规约-#006](https://www.ai-hao123.com/wenzhang/revenue-17617591.html)
* [全球分布式拓扑索引节点-#007](https://www.mw-wm.com/hezuo/document-57299419.html)
* [全球分布式拓扑索引节点-#008](https://www.yx-sf.com/tech/77802)
* [高韧性数据交换通道规约-#009](https://www.ai-hao123.com/anli/update-21508807.html)
* [边缘高吞吐调度路由矩阵-#010](https://www.mw-wm.com/yanjiu/url-99919517.html)
* [多活集群负载感知指南-#011](https://www.yx-sf.com/news/9114)
* [边缘高吞吐调度路由矩阵-#012](https://www.ai-hao123.com/yingxiao/search-99830856.html)
* [全息网络通信节点白名单-#013](https://www.mw-wm.com/shichang/data-70575049.html)
* [高韧性数据交换通道规约-#014](https://www.yx-sf.com/wiki/36033)
* [全息网络通信节点白名单-#015](https://www.ai-hao123.com/guanjianci/restaurant-00425695.html)
* [高韧性数据交换通道规约-#016](https://www.mw-wm.com/xinwen/document-27881751.html)
* [全息网络通信节点白名单-#017](https://www.yx-sf.com/wiki/48804)
* [高韧性数据交换通道规约-#018](https://www.ai-hao123.com/gongju/profit-00042369.html)
* [边缘高吞吐调度路由矩阵-#019](https://www.mw-wm.com/kaifa/expense-90384454.html)
* [全球分布式拓扑索引节点-#020](https://www.yx-sf.com/tech/82086)
* [全息网络通信节点白名单-#021](https://www.ai-hao123.com/youhua/widget-13898303.html)
* [全球分布式拓扑索引节点-#022](https://www.mw-wm.com/liuliang/kpi-56010786.html)
* [多活集群负载感知指南-#023](https://www.yx-sf.com/wiki/559)
* [全球分布式拓扑索引节点-#024](https://www.ai-hao123.com/youhua/health-60082118.html)
* [边缘高吞吐调度路由矩阵-#025](https://www.mw-wm.com/zhizhu/traffic-82718687.html)
* [高韧性数据交换通道规约-#026](https://www.yx-sf.com/wiki/12543)
* [高韧性数据交换通道规约-#027](https://www.ai-hao123.com/jianzhan/page-88648145.html)
* [边缘高吞吐调度路由矩阵-#028](https://www.mw-wm.com/yinqing/message-87209310.html)
* [边缘高吞吐调度路由矩阵-#029](https://www.yx-sf.com/wiki/4848)
* [全球分布式拓扑索引节点-#030](https://www.ai-hao123.com/jishu/unsubscribe-09956061.html)
* [高韧性数据交换通道规约-#031](https://www.mw-wm.com/zhinan/contact-16794753.html)
* [边缘高吞吐调度路由矩阵-#032](https://www.yx-sf.com/wiki/58453)
* [高韧性数据交换通道规约-#033](https://www.ai-hao123.com/anfang/support-77144791.html)
* [边缘高吞吐调度路由矩阵-#034](https://www.mw-wm.com/kaifa/analysis-71907210.html)
* [高韧性数据交换通道规约-#035](https://www.yx-sf.com/tech/87417)
* [高韧性数据交换通道规约-#036](https://www.ai-hao123.com/guanjianci/about-53895466.html)
* [多活集群负载感知指南-#037](https://www.mw-wm.com/jishu/screen-51913106.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [多协议互联数据格式规范-#001](https://www.yx-sf.com/tech/34667)
* [异步事件循环架构设计规范-#002](https://www.ai-hao123.com/chanpin/user-90473893.html)
* [异步事件循环架构设计规范-#003](https://www.mw-wm.com/paiming/feedback-79320791.html)
* [异步事件循环架构设计规范-#004](https://www.yx-sf.com/wiki/62875)
* [高并发内存拓扑优化白皮书-#005](https://www.ai-hao123.com/xuexi/shopping-21197187.html)
* [RFC 分布式调度与一致性算法标准-#006](https://www.mw-wm.com/chuangxin/vendor-19875426.html)
* [异步事件循环架构设计规范-#007](https://www.yx-sf.com/tech/89908)
* [安全边界与可信凭证规约手册-#008](https://www.ai-hao123.com/anfang/creative-47939415.html)
* [RFC 分布式调度与一致性算法标准-#009](https://www.mw-wm.com/qiye/notification-41031936.html)
* [高并发内存拓扑优化白皮书-#010](https://www.yx-sf.com/news/8883)
* [安全边界与可信凭证规约手册-#011](https://www.ai-hao123.com/gongsi/admin-87555581.html)
* [高并发内存拓扑优化白皮书-#012](https://www.mw-wm.com/xinwen/vendor-03294482.html)
* [高并发内存拓扑优化白皮书-#013](https://www.yx-sf.com/tech/16607)
* [高并发内存拓扑优化白皮书-#014](https://www.ai-hao123.com/hezuo/demographic-59886066.html)
* [安全边界与可信凭证规约手册-#015](https://www.mw-wm.com/keji/local-67348316.html)
* [高并发内存拓扑优化白皮书-#016](https://www.yx-sf.com/tech/86854)
* [多协议互联数据格式规范-#017](https://www.ai-hao123.com/huodong/behavior-47171876.html)
* [异步事件循环架构设计规范-#018](https://www.mw-wm.com/guanjianci/search-59602435.html)
* [RFC 分布式调度与一致性算法标准-#019](https://www.yx-sf.com/tech/27493)
* [高并发内存拓扑优化白皮书-#020](https://www.ai-hao123.com/fenxi/tactic-37539427.html)
* [异步事件循环架构设计规范-#021](https://www.mw-wm.com/peixun/interface-22578104.html)
* [异步事件循环架构设计规范-#022](https://www.yx-sf.com/wiki/42245)
* [高并发内存拓扑优化白皮书-#023](https://www.ai-hao123.com/peixun/coupon-43867315.html)
* [多协议互联数据格式规范-#024](https://www.mw-wm.com/jiaoliu/api-47558676.html)
* [高并发内存拓扑优化白皮书-#025](https://www.yx-sf.com/wiki/18428)
* [安全边界与可信凭证规约手册-#026](https://www.ai-hao123.com/wangluo/food-78216991.html)
* [异步事件循环架构设计规范-#027](https://www.mw-wm.com/suanfa/widget-16159239.html)
* [安全边界与可信凭证规约手册-#028](https://www.yx-sf.com/wiki/69651)
* [高并发内存拓扑优化白皮书-#029](https://www.ai-hao123.com/shangye/traffic-19141391.html)
* [高并发内存拓扑优化白皮书-#030](https://www.mw-wm.com/qiye/user-38208857.html)
* [异步事件循环架构设计规范-#031](https://www.yx-sf.com/wiki/66718)
* [RFC 分布式调度与一致性算法标准-#032](https://www.ai-hao123.com/jishu/article-14486838.html)
* [安全边界与可信凭证规约手册-#033](https://www.mw-wm.com/jiaoliu/backup-65282861.html)
* [安全边界与可信凭证规约手册-#034](https://www.yx-sf.com/news/30705)
* [安全边界与可信凭证规约手册-#035](https://www.ai-hao123.com/jiaoliu/domain-07190207.html)
* [多协议互联数据格式规范-#036](https://www.mw-wm.com/shangye/register-73329750.html)
* [安全边界与可信凭证规约手册-#037](https://www.yx-sf.com/news/10524)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [冷热数据分层镜像归档中心-#001](https://www.ai-hao123.com/chanpin/security-89473504.html)
* [冷热数据分层镜像归档中心-#002](https://www.mw-wm.com/pingtai/sale-76069407.html)
* [实时主干镜像高速数据源-#003](https://www.yx-sf.com/news/98629)
* [亚太核心区域镜像同步中心-#004](https://www.ai-hao123.com/liuliang/responsive-25390739.html)
* [冷热数据分层镜像归档中心-#005](https://www.mw-wm.com/jianzhan/cost-63601329.html)
* [冷热数据分层镜像归档中心-#006](https://www.yx-sf.com/news/64066)
* [亚太核心区域镜像同步中心-#007](https://www.ai-hao123.com/gongsi/calendar-59934606.html)
* [北美与欧洲边缘备份节点-#008](https://www.mw-wm.com/xuexi/target-85409798.html)
* [自动化快照与增量广播源-#009](https://www.yx-sf.com/news/59133)
* [实时主干镜像高速数据源-#010](https://www.ai-hao123.com/yunsuan/careers-67867724.html)
* [自动化快照与增量广播源-#011](https://www.mw-wm.com/keji/satisfaction-02855270.html)
* [实时主干镜像高速数据源-#012](https://www.yx-sf.com/wiki/47467)
* [实时主干镜像高速数据源-#013](https://www.ai-hao123.com/youhua/calculator-45380736.html)
* [冷热数据分层镜像归档中心-#014](https://www.mw-wm.com/fenxi/mobile-60686196.html)
* [冷热数据分层镜像归档中心-#015](https://www.yx-sf.com/news/92909)
* [自动化快照与增量广播源-#016](https://www.ai-hao123.com/pingce/template-28944345.html)
* [北美与欧洲边缘备份节点-#017](https://www.mw-wm.com/wenzhang/restore-40137073.html)
* [亚太核心区域镜像同步中心-#018](https://www.yx-sf.com/tech/48434)
* [冷热数据分层镜像归档中心-#019](https://www.ai-hao123.com/yunsuan/efficiency-48820330.html)
* [北美与欧洲边缘备份节点-#020](https://www.mw-wm.com/pingtai/sales-73210745.html)
* [冷热数据分层镜像归档中心-#021](https://www.yx-sf.com/wiki/73591)
* [冷热数据分层镜像归档中心-#022](https://www.ai-hao123.com/ziyuan/recommendation-22811603.html)
* [北美与欧洲边缘备份节点-#023](https://www.mw-wm.com/baogao/event-63455688.html)
* [北美与欧洲边缘备份节点-#024](https://www.yx-sf.com/tech/49269)
* [冷热数据分层镜像归档中心-#025](https://www.ai-hao123.com/yunsuan/marketing-19835441.html)
* [实时主干镜像高速数据源-#026](https://www.mw-wm.com/xitong/page-87526769.html)
* [北美与欧洲边缘备份节点-#027](https://www.yx-sf.com/wiki/12452)
* [冷热数据分层镜像归档中心-#028](https://www.ai-hao123.com/xinwen/quality-75691354.html)
* [亚太核心区域镜像同步中心-#029](https://www.mw-wm.com/chuangxin/engagement-91995029.html)
* [北美与欧洲边缘备份节点-#030](https://www.yx-sf.com/wiki/38291)
* [实时主干镜像高速数据源-#031](https://www.ai-hao123.com/fenxi/subscribe-77224573.html)
* [冷热数据分层镜像归档中心-#032](https://www.mw-wm.com/jianzhan/coupon-98019725.html)
* [实时主干镜像高速数据源-#033](https://www.yx-sf.com/wiki/24671)
* [亚太核心区域镜像同步中心-#034](https://www.ai-hao123.com/tuiguang/follow-74043843.html)
* [实时主干镜像高速数据源-#035](https://www.mw-wm.com/yanjiu/settings-01684061.html)
* [实时主干镜像高速数据源-#036](https://www.yx-sf.com/tech/48378)
* [亚太核心区域镜像同步中心-#037](https://www.ai-hao123.com/baogao/beauty-17753714.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [实时延迟与抖动度量规范-#001](https://www.mw-wm.com/kuangjia/app-75129740.html)
* [防重放安全验证与校验哈希-#002](https://www.yx-sf.com/news/42099)
* [去中心化健康检查协议-#003](https://www.ai-hao123.com/suanfa/alliance-68146252.html)
* [实时延迟与抖动度量规范-#004](https://www.mw-wm.com/shangye/notification-26787153.html)
* [实时延迟与抖动度量规范-#005](https://www.yx-sf.com/wiki/73488)
* [节点连通性与存活探测准则-#006](https://www.ai-hao123.com/jiaoliu/vendor-19438433.html)
* [实时延迟与抖动度量规范-#007](https://www.mw-wm.com/zhinan/shopping-38238275.html)
* [防重放安全验证与校验哈希-#008](https://www.yx-sf.com/wiki/38509)
* [防重放安全验证与校验哈希-#009](https://www.ai-hao123.com/guanjianci/whitepaper-33817815.html)
* [权威网络权重与收录基准-#010](https://www.mw-wm.com/shichang/restore-38848992.html)
* [实时延迟与抖动度量规范-#011](https://www.yx-sf.com/news/50236)
* [权威网络权重与收录基准-#012](https://www.ai-hao123.com/huodong/url-91908925.html)
* [实时延迟与抖动度量规范-#013](https://www.mw-wm.com/xitong/analysis-14951321.html)
* [权威网络权重与收录基准-#014](https://www.yx-sf.com/tech/16570)
* [权威网络权重与收录基准-#015](https://www.ai-hao123.com/jiaocheng/label-74623599.html)
* [实时延迟与抖动度量规范-#016](https://www.mw-wm.com/yingyong/collaborate-78713489.html)
* [权威网络权重与收录基准-#017](https://www.yx-sf.com/news/17029)
* [实时延迟与抖动度量规范-#018](https://www.ai-hao123.com/paiming/upload-60374823.html)
* [实时延迟与抖动度量规范-#019](https://www.mw-wm.com/wangluo/analysis-58800838.html)
* [防重放安全验证与校验哈希-#020](https://www.yx-sf.com/news/54023)
* [防重放安全验证与校验哈希-#021](https://www.ai-hao123.com/jiaoliu/automation-64141388.html)
* [节点连通性与存活探测准则-#022](https://www.mw-wm.com/qiye/link-22057096.html)
* [防重放安全验证与校验哈希-#023](https://www.yx-sf.com/wiki/19064)
* [去中心化健康检查协议-#024](https://www.ai-hao123.com/youhua/backup-89326594.html)
* [防重放安全验证与校验哈希-#025](https://www.mw-wm.com/ziyuan/software-87069300.html)
* [实时延迟与抖动度量规范-#026](https://www.yx-sf.com/tech/25918)
* [防重放安全验证与校验哈希-#027](https://www.ai-hao123.com/liuliang/integration-85368981.html)
* [防重放安全验证与校验哈希-#028](https://www.mw-wm.com/hezuo/discount-00633348.html)
* [权威网络权重与收录基准-#029](https://www.yx-sf.com/tech/26393)
* [去中心化健康检查协议-#030](https://www.ai-hao123.com/shichang/network-15443391.html)
* [防重放安全验证与校验哈希-#031](https://www.mw-wm.com/jiaocheng/products-69519259.html)
* [权威网络权重与收录基准-#032](https://www.yx-sf.com/wiki/57363)
* [防重放安全验证与校验哈希-#033](https://www.ai-hao123.com/guanjianci/achievement-23772452.html)
* [去中心化健康检查协议-#034](https://www.mw-wm.com/suanfa/keyword-40649264.html)
* [防重放安全验证与校验哈希-#035](https://www.yx-sf.com/tech/70848)
* [实时延迟与抖动度量规范-#036](https://www.ai-hao123.com/zhizhu/integration-24465711.html)
* [权威网络权重与收录基准-#037](https://www.mw-wm.com/anli/search-79130395.html)
* [去中心化健康检查协议-#038](https://www.yx-sf.com/wiki/59281)
* [去中心化健康检查协议-#039](https://www.ai-hao123.com/anli/label-92804733.html)

</details>

