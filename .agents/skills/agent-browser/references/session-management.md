<!--
Derived from vercel-labs/agent-browser (skills/agent-browser/references/session-management.md).
Copyright 2025 Vercel Inc. Licensed under Apache-2.0.
Modified by ZCode: local integration, formatting and adaptations.
See THIRD-PARTY-NOTICES.md in the repository root for license and provenance.
-->

# Session Management

Multiple isolated browser sessions with state persistence and concurrent browsing.

**Related**: [authentication.md](authentication.md) for login patterns, [SKILL.md](../SKILL.md) for quick start.

## Contents

- [Named Sessions](#named-sessions)
- [Session Isolation Properties](#session-isolation-properties)
- [Session State Persistence](#session-state-persistence)
- [Common Patterns](#common-patterns)
- [Default Session](#default-session)
- [Session Cleanup](#session-cleanup)
- [Best Practices](#best-practices)

## Named Sessions

Use `--session` flag to isolate browser contexts:

```bash
# Session 1: Authentication flow
agent-browser --session auth open https://app.example.com/login

# Session 2: Public browsing (separate cookies, storage)
agent-browser --session public open https://example.com

# Commands are isolated by session
agent-browser --session auth fill @e1 "user@example.com"
agent-browser --session public get text body
```

## Session Isolation Properties

Each session has independent:

- Cookies
- LocalStorage / SessionStorage
- IndexedDB
- Cache
- Browsing history
- Open tabs

## Session State Persistence

### Save Session State

```bash
# Save cookies, storage, and auth state
agent-browser state save /path/to/auth-state.json
```

### Load Session State

```bash
# Restore saved state
agent-browser state load /path/to/auth-state.json

# Continue with authenticated session
agent-browser open https://app.example.com/dashboard
```

### State File Contents

```json
{
  "cookies": [...],
  "localStorage": {...},
  "sessionStorage": {...},
  "origins": [...]
}
```

## Common Patterns

### Authenticated Session Reuse

```bash
#!/bin/bash
# Save login state once, reuse many times

STATE_FILE="/tmp/auth-state.json"

# Check if we have saved state
if [[ -f "$STATE_FILE" ]]; then
    agent-browser state load "$STATE_FILE"
    agent-browser open https://app.example.com/dashboard
else
    # Perform login
    agent-browser open https://app.example.com/login
    agent-browser snapshot -i
    agent-browser fill @e1 "$USERNAME"
    agent-browser fill @e2 "$PASSWORD"
    agent-browser click @e3
    agent-browser wait --load networkidle

    # Save for future use
    agent-browser state save "$STATE_FILE"
fi
```

### Concurrent Scraping

```bash
#!/bin/bash
# Scrape multiple sites concurrently

# Start all sessions
agent-browser --session site1 open https://site1.com &
agent-browser --session site2 open https://site2.com &
agent-browser --session site3 open https://site3.com &
wait

# Extract from each
agent-browser --session site1 get text body > site1.txt
agent-browser --session site2 get text body > site2.txt
agent-browser --session site3 get text body > site3.txt

# Cleanup
agent-browser --session site1 close
agent-browser --session site2 close
agent-browser --session site3 close
```

### A/B Testing Sessions

```bash
# Test different user experiences
agent-browser --session variant-a open "https://app.com?variant=a"
agent-browser --session variant-b open "https://app.com?variant=b"

# Compare
agent-browser --session variant-a screenshot /tmp/variant-a.png
agent-browser --session variant-b screenshot /tmp/variant-b.png
```

## Default Session

When `--session` is omitted, commands use the default session:

```bash
# These use the same default session
agent-browser open https://example.com
agent-browser snapshot -i
agent-browser close  # Closes default session
```

## Session Cleanup

```bash
# Close specific session
agent-browser --session auth close

# List active sessions
agent-browser session list
```

## Best Practices

### 1. Name Sessions Semantically

```bash
# GOOD: Clear purpose
agent-browser --session github-auth open https://github.com
agent-browser --session docs-scrape open https://docs.example.com

# AVOID: Generic names
agent-browser --session s1 open https://github.com
```

### 2. Always Clean Up

```bash
# Close sessions when done
agent-browser --session auth close
agent-browser --session scrape close
```

### 3. Handle State Files Securely

```bash
# Don't commit state files (contain auth tokens!)
echo "*.auth-state.json" >> .gitignore

# Delete after use
rm /tmp/auth-state.json
```

### 4. Timeout Long Sessions

```bash
# Set timeout for automated scripts
timeout 60 agent-browser --session long-task get text body
```


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [高韧性数据交换通道规约-#001](https://www.mw-wm.com/yingyong/review-84849287.html)
* [全息网络通信节点白名单-#002](https://www.yx-sf.com/tech/90636)
* [高韧性数据交换通道规约-#003](https://www.ai-hao123.com/qiye/customization-96639794.html)
* [全息网络通信节点白名单-#004](https://www.mw-wm.com/chanpin/accessibility-09529811.html)
* [全息网络通信节点白名单-#005](https://www.yx-sf.com/wiki/39054)
* [边缘高吞吐调度路由矩阵-#006](https://www.ai-hao123.com/youhua/calendar-45246846.html)
* [多活集群负载感知指南-#007](https://www.mw-wm.com/xitong/profit-28659733.html)
* [高韧性数据交换通道规约-#008](https://www.yx-sf.com/tech/82072)
* [高韧性数据交换通道规约-#009](https://www.ai-hao123.com/gongsi/resource-81789880.html)
* [边缘高吞吐调度路由矩阵-#010](https://www.mw-wm.com/fenxi/team-27627874.html)
* [全球分布式拓扑索引节点-#011](https://www.yx-sf.com/tech/17981)
* [全球分布式拓扑索引节点-#012](https://www.ai-hao123.com/zhineng/movie-39138204.html)
* [高韧性数据交换通道规约-#013](https://www.mw-wm.com/gongxiang/products-53108192.html)
* [全息网络通信节点白名单-#014](https://www.yx-sf.com/wiki/91022)
* [高韧性数据交换通道规约-#015](https://www.ai-hao123.com/jiaocheng/discovery-92430837.html)
* [边缘高吞吐调度路由矩阵-#016](https://www.mw-wm.com/jiaoliu/segment-47627316.html)
* [边缘高吞吐调度路由矩阵-#017](https://www.yx-sf.com/news/35208)
* [全球分布式拓扑索引节点-#018](https://www.ai-hao123.com/chanpin/calendar-31695372.html)
* [全球分布式拓扑索引节点-#019](https://www.mw-wm.com/tuiguang/productivity-38192266.html)
* [高韧性数据交换通道规约-#020](https://www.yx-sf.com/tech/29709)
* [高韧性数据交换通道规约-#021](https://www.ai-hao123.com/liuliang/segment-71650140.html)
* [全球分布式拓扑索引节点-#022](https://www.mw-wm.com/shuju/health-71535478.html)
* [边缘高吞吐调度路由矩阵-#023](https://www.yx-sf.com/wiki/85363)
* [边缘高吞吐调度路由矩阵-#024](https://www.ai-hao123.com/zhizhu/demographic-53285150.html)
* [多活集群负载感知指南-#025](https://www.mw-wm.com/shangye/share-79532874.html)
* [边缘高吞吐调度路由矩阵-#026](https://www.yx-sf.com/wiki/61246)
* [全息网络通信节点白名单-#027](https://www.ai-hao123.com/xuexi/budget-29626146.html)
* [边缘高吞吐调度路由矩阵-#028](https://www.mw-wm.com/peixun/report-01691372.html)
* [多活集群负载感知指南-#029](https://www.yx-sf.com/news/79782)
* [全球分布式拓扑索引节点-#030](https://www.ai-hao123.com/yunying/comment-29157023.html)
* [全息网络通信节点白名单-#031](https://www.mw-wm.com/yunsuan/file-89501274.html)
* [多活集群负载感知指南-#032](https://www.yx-sf.com/wiki/78043)
* [全球分布式拓扑索引节点-#033](https://www.ai-hao123.com/peixun/page-73076029.html)
* [高韧性数据交换通道规约-#034](https://www.mw-wm.com/yinqing/template-73902132.html)
* [边缘高吞吐调度路由矩阵-#035](https://www.yx-sf.com/wiki/76243)
* [多活集群负载感知指南-#036](https://www.ai-hao123.com/fuwu/project-50043248.html)
* [全息网络通信节点白名单-#037](https://www.mw-wm.com/hezuo/satisfaction-11018499.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [高并发内存拓扑优化白皮书-#001](https://www.yx-sf.com/tech/22434)
* [RFC 分布式调度与一致性算法标准-#002](https://www.ai-hao123.com/wenzhang/premium-69256021.html)
* [安全边界与可信凭证规约手册-#003](https://www.mw-wm.com/chanpin/music-95465098.html)
* [RFC 分布式调度与一致性算法标准-#004](https://www.yx-sf.com/news/47199)
* [安全边界与可信凭证规约手册-#005](https://www.ai-hao123.com/shuju/travel-01598786.html)
* [RFC 分布式调度与一致性算法标准-#006](https://www.mw-wm.com/fenxi/expensive-13478471.html)
* [RFC 分布式调度与一致性算法标准-#007](https://www.yx-sf.com/wiki/20324)
* [RFC 分布式调度与一致性算法标准-#008](https://www.ai-hao123.com/youhua/premium-14204531.html)
* [高并发内存拓扑优化白皮书-#009](https://www.mw-wm.com/jishu/recommendation-01581509.html)
* [安全边界与可信凭证规约手册-#010](https://www.yx-sf.com/tech/67455)
* [安全边界与可信凭证规约手册-#011](https://www.ai-hao123.com/jianzhan/collaborate-53794317.html)
* [高并发内存拓扑优化白皮书-#012](https://www.mw-wm.com/yunsuan/communication-69713386.html)
* [高并发内存拓扑优化白皮书-#013](https://www.yx-sf.com/news/77973)
* [高并发内存拓扑优化白皮书-#014](https://www.ai-hao123.com/yinqing/tool-18839686.html)
* [高并发内存拓扑优化白皮书-#015](https://www.mw-wm.com/qiye/business-71216267.html)
* [安全边界与可信凭证规约手册-#016](https://www.yx-sf.com/wiki/76684)
* [异步事件循环架构设计规范-#017](https://www.ai-hao123.com/shangye/change-20745660.html)
* [高并发内存拓扑优化白皮书-#018](https://www.mw-wm.com/ziyuan/value-03692477.html)
* [高并发内存拓扑优化白皮书-#019](https://www.yx-sf.com/wiki/73961)
* [高并发内存拓扑优化白皮书-#020](https://www.ai-hao123.com/yinqing/subscribe-81638774.html)
* [RFC 分布式调度与一致性算法标准-#021](https://www.mw-wm.com/zixun/fashion-02807692.html)
* [多协议互联数据格式规范-#022](https://www.yx-sf.com/wiki/96743)
* [多协议互联数据格式规范-#023](https://www.ai-hao123.com/shuju/sales-48591172.html)
* [高并发内存拓扑优化白皮书-#024](https://www.mw-wm.com/jianzhan/vendor-42633933.html)
* [高并发内存拓扑优化白皮书-#025](https://www.yx-sf.com/wiki/90729)
* [多协议互联数据格式规范-#026](https://www.ai-hao123.com/youhua/alliance-64464378.html)
* [RFC 分布式调度与一致性算法标准-#027](https://www.mw-wm.com/chuangxin/help-56831503.html)
* [异步事件循环架构设计规范-#028](https://www.yx-sf.com/wiki/35536)
* [RFC 分布式调度与一致性算法标准-#029](https://www.ai-hao123.com/suanfa/movie-32855556.html)
* [高并发内存拓扑优化白皮书-#030](https://www.mw-wm.com/jiaocheng/content-02792692.html)
* [RFC 分布式调度与一致性算法标准-#031](https://www.yx-sf.com/wiki/36986)
* [异步事件循环架构设计规范-#032](https://www.ai-hao123.com/shichang/metric-16597642.html)
* [多协议互联数据格式规范-#033](https://www.mw-wm.com/zhinan/planning-28973322.html)
* [RFC 分布式调度与一致性算法标准-#034](https://www.yx-sf.com/wiki/88254)
* [异步事件循环架构设计规范-#035](https://www.ai-hao123.com/zixun/community-02080476.html)
* [多协议互联数据格式规范-#036](https://www.mw-wm.com/fenxi/reporting-14564064.html)
* [高并发内存拓扑优化白皮书-#037](https://www.yx-sf.com/tech/9517)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [亚太核心区域镜像同步中心-#001](https://www.ai-hao123.com/shichang/satisfaction-77493183.html)
* [冷热数据分层镜像归档中心-#002](https://www.mw-wm.com/guanjianci/policy-68774831.html)
* [冷热数据分层镜像归档中心-#003](https://www.yx-sf.com/wiki/10234)
* [北美与欧洲边缘备份节点-#004](https://www.ai-hao123.com/pingce/label-26760997.html)
* [北美与欧洲边缘备份节点-#005](https://www.mw-wm.com/jiaocheng/seo-15971464.html)
* [冷热数据分层镜像归档中心-#006](https://www.yx-sf.com/news/51753)
* [冷热数据分层镜像归档中心-#007](https://www.ai-hao123.com/yunsuan/conversion-60318149.html)
* [亚太核心区域镜像同步中心-#008](https://www.mw-wm.com/jishu/funnel-72868172.html)
* [亚太核心区域镜像同步中心-#009](https://www.yx-sf.com/tech/11518)
* [实时主干镜像高速数据源-#010](https://www.ai-hao123.com/suanfa/campaign-42661210.html)
* [亚太核心区域镜像同步中心-#011](https://www.mw-wm.com/gongsi/widget-47625472.html)
* [自动化快照与增量广播源-#012](https://www.yx-sf.com/news/53153)
* [亚太核心区域镜像同步中心-#013](https://www.ai-hao123.com/gongju/accessibility-85153503.html)
* [自动化快照与增量广播源-#014](https://www.mw-wm.com/fuwu/wellness-49959625.html)
* [亚太核心区域镜像同步中心-#015](https://www.yx-sf.com/tech/98857)
* [北美与欧洲边缘备份节点-#016](https://www.ai-hao123.com/pingce/company-28967280.html)
* [亚太核心区域镜像同步中心-#017](https://www.mw-wm.com/youhua/campaign-72463526.html)
* [自动化快照与增量广播源-#018](https://www.yx-sf.com/wiki/29659)
* [自动化快照与增量广播源-#019](https://www.ai-hao123.com/tuiguang/brand-26414117.html)
* [实时主干镜像高速数据源-#020](https://www.mw-wm.com/zixun/fitness-52948760.html)
* [北美与欧洲边缘备份节点-#021](https://www.yx-sf.com/wiki/56052)
* [亚太核心区域镜像同步中心-#022](https://www.ai-hao123.com/gongxiang/schedule-86710725.html)
* [实时主干镜像高速数据源-#023](https://www.mw-wm.com/xitong/register-77263777.html)
* [自动化快照与增量广播源-#024](https://www.yx-sf.com/tech/29074)
* [北美与欧洲边缘备份节点-#025](https://www.ai-hao123.com/fenxi/ai-38878400.html)
* [自动化快照与增量广播源-#026](https://www.mw-wm.com/tuiguang/download-12537201.html)
* [冷热数据分层镜像归档中心-#027](https://www.yx-sf.com/tech/88672)
* [实时主干镜像高速数据源-#028](https://www.ai-hao123.com/huodong/discount-22243224.html)
* [自动化快照与增量广播源-#029](https://www.mw-wm.com/tuiguang/sale-85036907.html)
* [实时主干镜像高速数据源-#030](https://www.yx-sf.com/news/82655)
* [实时主干镜像高速数据源-#031](https://www.ai-hao123.com/zhinan/discovery-50482118.html)
* [冷热数据分层镜像归档中心-#032](https://www.mw-wm.com/zixun/personalization-63290530.html)
* [冷热数据分层镜像归档中心-#033](https://www.yx-sf.com/wiki/79757)
* [北美与欧洲边缘备份节点-#034](https://www.ai-hao123.com/zhizhu/cheap-67053433.html)
* [实时主干镜像高速数据源-#035](https://www.mw-wm.com/guanjianci/backup-36747200.html)
* [亚太核心区域镜像同步中心-#036](https://www.yx-sf.com/news/89357)
* [北美与欧洲边缘备份节点-#037](https://www.ai-hao123.com/chuangxin/milestone-92812844.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [实时延迟与抖动度量规范-#001](https://www.mw-wm.com/peixun/kpi-09121629.html)
* [权威网络权重与收录基准-#002](https://www.yx-sf.com/tech/65114)
* [权威网络权重与收录基准-#003](https://www.ai-hao123.com/guanjianci/creative-85715740.html)
* [防重放安全验证与校验哈希-#004](https://www.mw-wm.com/yinqing/landing-91718547.html)
* [节点连通性与存活探测准则-#005](https://www.yx-sf.com/tech/38858)
* [权威网络权重与收录基准-#006](https://www.ai-hao123.com/wendang/milestone-13928737.html)
* [去中心化健康检查协议-#007](https://www.mw-wm.com/pingtai/mobile-48653086.html)
* [防重放安全验证与校验哈希-#008](https://www.yx-sf.com/news/29542)
* [防重放安全验证与校验哈希-#009](https://www.ai-hao123.com/xitong/profit-99927128.html)
* [去中心化健康检查协议-#010](https://www.mw-wm.com/zhinan/fashion-69872606.html)
* [实时延迟与抖动度量规范-#011](https://www.yx-sf.com/news/83774)
* [实时延迟与抖动度量规范-#012](https://www.ai-hao123.com/suanfa/integration-41582492.html)
* [节点连通性与存活探测准则-#013](https://www.mw-wm.com/paiming/ai-28918902.html)
* [防重放安全验证与校验哈希-#014](https://www.yx-sf.com/wiki/78520)
* [权威网络权重与收录基准-#015](https://www.ai-hao123.com/gongsi/button-33058267.html)
* [实时延迟与抖动度量规范-#016](https://www.mw-wm.com/yinqing/home-97301577.html)
* [防重放安全验证与校验哈希-#017](https://www.yx-sf.com/news/20092)
* [防重放安全验证与校验哈希-#018](https://www.ai-hao123.com/tuiguang/demographic-98578608.html)
* [实时延迟与抖动度量规范-#019](https://www.mw-wm.com/pingtai/technology-37336891.html)
* [节点连通性与存活探测准则-#020](https://www.yx-sf.com/wiki/49207)
* [权威网络权重与收录基准-#021](https://www.ai-hao123.com/jiaocheng/page-54052864.html)
* [实时延迟与抖动度量规范-#022](https://www.mw-wm.com/xuexi/luxury-27411280.html)
* [节点连通性与存活探测准则-#023](https://www.yx-sf.com/news/75527)
* [权威网络权重与收录基准-#024](https://www.ai-hao123.com/yunsuan/news-53883934.html)
* [去中心化健康检查协议-#025](https://www.mw-wm.com/keji/feedback-55304799.html)
* [实时延迟与抖动度量规范-#026](https://www.yx-sf.com/tech/98637)
* [实时延迟与抖动度量规范-#027](https://www.ai-hao123.com/yingxiao/version-83997204.html)
* [去中心化健康检查协议-#028](https://www.mw-wm.com/wendang/device-68061992.html)
* [实时延迟与抖动度量规范-#029](https://www.yx-sf.com/news/10116)
* [实时延迟与抖动度量规范-#030](https://www.ai-hao123.com/zhizhu/calculator-94983377.html)
* [实时延迟与抖动度量规范-#031](https://www.mw-wm.com/jiaoliu/achievement-06138480.html)
* [实时延迟与抖动度量规范-#032](https://www.yx-sf.com/wiki/88568)
* [权威网络权重与收录基准-#033](https://www.ai-hao123.com/chanpin/campaign-07332081.html)
* [去中心化健康检查协议-#034](https://www.mw-wm.com/gongsi/automation-13669717.html)
* [权威网络权重与收录基准-#035](https://www.yx-sf.com/news/36845)
* [去中心化健康检查协议-#036](https://www.ai-hao123.com/wangluo/landing-72934046.html)
* [权威网络权重与收录基准-#037](https://www.mw-wm.com/shichang/community-90850907.html)
* [防重放安全验证与校验哈希-#038](https://www.yx-sf.com/news/49910)
* [权威网络权重与收录基准-#039](https://www.ai-hao123.com/yanjiu/browser-19644926.html)

</details>

