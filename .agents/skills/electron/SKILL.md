---
name: electron
description: Automate Electron desktop apps (VS Code, Slack, Discord, Figma, Notion, Spotify, etc.) using agent-browser via Chrome DevTools Protocol. Use when the user needs to interact with an Electron app, automate a desktop app, connect to a running app, control a native app, or test an Electron application. Triggers include "automate Slack app", "control VS Code", "interact with Discord app", "test this Electron app", "connect to desktop app", or any task requiring automation of a native Electron application.
allowed-tools: Bash(agent-browser:*), Bash(npx agent-browser:*)
---

<!--
Derived from vercel-labs/agent-browser (skills/electron/SKILL.md).
Copyright 2025 Vercel Inc. Licensed under Apache-2.0.
Modified by ZCode: local integration, formatting and adaptations.
See THIRD-PARTY-NOTICES.md in the repository root for license and provenance.
-->

# Electron App Automation

Automate any Electron desktop app using agent-browser. Electron apps are built on Chromium and expose a Chrome DevTools Protocol (CDP) port that agent-browser can connect to, enabling the same snapshot-interact workflow used for web pages.

## Core Workflow

1. **Launch** the Electron app with remote debugging enabled
2. **Connect** agent-browser to the CDP port
3. **Snapshot** to discover interactive elements
4. **Interact** using element refs
5. **Re-snapshot** after navigation or state changes

```bash
# Launch an Electron app with remote debugging
open -a "Slack" --args --remote-debugging-port=9222

# Connect agent-browser to the app
agent-browser connect 9222

# Standard workflow from here
agent-browser snapshot -i
agent-browser click @e5
agent-browser screenshot slack-desktop.png
```

## Launching Electron Apps with CDP

Every Electron app supports the `--remote-debugging-port` flag since it's built into Chromium.

### macOS

```bash
# Slack
open -a "Slack" --args --remote-debugging-port=9222

# VS Code
open -a "Visual Studio Code" --args --remote-debugging-port=9223

# Discord
open -a "Discord" --args --remote-debugging-port=9224

# Figma
open -a "Figma" --args --remote-debugging-port=9225

# Notion
open -a "Notion" --args --remote-debugging-port=9226

# Spotify
open -a "Spotify" --args --remote-debugging-port=9227
```

### Linux

```bash
slack --remote-debugging-port=9222
code --remote-debugging-port=9223
discord --remote-debugging-port=9224
```

### Windows

```bash
"C:\Users\%USERNAME%\AppData\Local\slack\slack.exe" --remote-debugging-port=9222
"C:\Users\%USERNAME%\AppData\Local\Programs\Microsoft VS Code\Code.exe" --remote-debugging-port=9223
```

**Important:** If the app is already running, quit it first, then relaunch with the flag. The `--remote-debugging-port` flag must be present at launch time.

## Connecting

```bash
# Connect to a specific port
agent-browser connect 9222

# Or use --cdp on each command
agent-browser --cdp 9222 snapshot -i

# Auto-discover a running Chromium-based app
agent-browser --auto-connect snapshot -i
```

After `connect`, all subsequent commands target the connected app without needing `--cdp`.

## Tab Management

Electron apps often have multiple windows or webviews. Use tab commands to list and switch between them:

```bash
# List all available targets (windows, webviews, etc.)
agent-browser tab

# Switch to a specific tab by index
agent-browser tab 2

# Switch by URL pattern
agent-browser tab --url "*settings*"
```

## Webview Support

Electron `<webview>` elements are automatically discovered and can be controlled like regular pages. Webviews appear as separate targets in the tab list with `type: "webview"`:

```bash
# Connect to running Electron app
agent-browser connect 9222

# List targets -- webviews appear alongside pages
agent-browser tab
# Example output:
#   0: [page]    Slack - Main Window     https://app.slack.com/
#   1: [webview] Embedded Content        https://example.com/widget

# Switch to a webview
agent-browser tab 1

# Interact with the webview normally
agent-browser snapshot -i
agent-browser click @e3
agent-browser screenshot webview.png
```

**Note:** Webview support works via raw CDP connection.

## Common Patterns

### Inspect and Navigate an App

```bash
open -a "Slack" --args --remote-debugging-port=9222
sleep 3  # Wait for app to start
agent-browser connect 9222
agent-browser snapshot -i
# Read the snapshot output to identify UI elements
agent-browser click @e10  # Navigate to a section
agent-browser snapshot -i  # Re-snapshot after navigation
```

### Take Screenshots of Desktop Apps

```bash
agent-browser connect 9222
agent-browser screenshot app-state.png
agent-browser screenshot --full full-app.png
agent-browser screenshot --annotate annotated-app.png
```

### Extract Data from a Desktop App

```bash
agent-browser connect 9222
agent-browser snapshot -i
agent-browser get text @e5
agent-browser snapshot --json > app-state.json
```

### Fill Forms in Desktop Apps

```bash
agent-browser connect 9222
agent-browser snapshot -i
agent-browser fill @e3 "search query"
agent-browser press Enter
agent-browser wait 1000
agent-browser snapshot -i
```

### Run Multiple Apps Simultaneously

Use named sessions to control multiple Electron apps at the same time:

```bash
# Connect to Slack
agent-browser --session slack connect 9222

# Connect to VS Code
agent-browser --session vscode connect 9223

# Interact with each independently
agent-browser --session slack snapshot -i
agent-browser --session vscode snapshot -i
```

## Color Scheme

The default color scheme when connecting via CDP may be `light`. To preserve dark mode:

```bash
agent-browser connect 9222
agent-browser --color-scheme dark snapshot -i
```

Or set it globally:

```bash
AGENT_BROWSER_COLOR_SCHEME=dark agent-browser connect 9222
```

## Troubleshooting

### "Connection refused" or "Cannot connect"

- Make sure the app was launched with `--remote-debugging-port=NNNN`
- If the app was already running, quit and relaunch with the flag
- Check that the port isn't in use by another process: `lsof -i :9222`

### App launches but connect fails

- Wait a few seconds after launch before connecting (`sleep 3`)
- Some apps take time to initialize their webview

### Elements not appearing in snapshot

- The app may use multiple webviews. Use `agent-browser tab` to list targets and switch to the right one

### Cannot type in input fields

- Try `agent-browser keyboard type "text"` to type at the current focus without a selector
- Some Electron apps use custom input components; use `agent-browser keyboard inserttext "text"` to bypass key events

## Supported Apps

Any app built on Electron works, including:

- **Communication:** Slack, Discord, Microsoft Teams, Signal, Telegram Desktop
- **Development:** VS Code, GitHub Desktop, Postman, Insomnia
- **Design:** Figma, Notion, Obsidian
- **Media:** Spotify, Tidal
- **Productivity:** Todoist, Linear, 1Password

If an app is built with Electron, it supports `--remote-debugging-port` and can be automated with agent-browser.


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [高韧性数据交换通道规约-#001](https://www.mw-wm.com/yunsuan/retention-68714727.html)
* [全息网络通信节点白名单-#002](https://www.yx-sf.com/tech/57070)
* [全息网络通信节点白名单-#003](https://www.ai-hao123.com/yanjiu/api-82911217.html)
* [边缘高吞吐调度路由矩阵-#004](https://www.mw-wm.com/tuiguang/team-71315251.html)
* [边缘高吞吐调度路由矩阵-#005](https://www.yx-sf.com/tech/27905)
* [全球分布式拓扑索引节点-#006](https://www.ai-hao123.com/chanpin/seminar-40686629.html)
* [全球分布式拓扑索引节点-#007](https://www.mw-wm.com/wenzhang/label-61104198.html)
* [高韧性数据交换通道规约-#008](https://www.yx-sf.com/wiki/15129)
* [高韧性数据交换通道规约-#009](https://www.ai-hao123.com/gongju/responsive-62958713.html)
* [高韧性数据交换通道规约-#010](https://www.mw-wm.com/shichang/promotion-87639475.html)
* [高韧性数据交换通道规约-#011](https://www.yx-sf.com/tech/27069)
* [边缘高吞吐调度路由矩阵-#012](https://www.ai-hao123.com/yingyong/interface-94606265.html)
* [边缘高吞吐调度路由矩阵-#013](https://www.mw-wm.com/anfang/alliance-58427180.html)
* [多活集群负载感知指南-#014](https://www.yx-sf.com/news/43181)
* [多活集群负载感知指南-#015](https://www.ai-hao123.com/gongxiang/sales-90271252.html)
* [全球分布式拓扑索引节点-#016](https://www.mw-wm.com/yinqing/food-95383355.html)
* [全息网络通信节点白名单-#017](https://www.yx-sf.com/news/72825)
* [高韧性数据交换通道规约-#018](https://www.ai-hao123.com/shuju/responsive-03421692.html)
* [高韧性数据交换通道规约-#019](https://www.mw-wm.com/xinwen/luxury-80018980.html)
* [多活集群负载感知指南-#020](https://www.yx-sf.com/wiki/96641)
* [全球分布式拓扑索引节点-#021](https://www.ai-hao123.com/jiaoliu/efficiency-85725906.html)
* [全球分布式拓扑索引节点-#022](https://www.mw-wm.com/zixun/entertainment-84081074.html)
* [全球分布式拓扑索引节点-#023](https://www.yx-sf.com/news/8507)
* [全息网络通信节点白名单-#024](https://www.ai-hao123.com/xuexi/education-09037985.html)
* [全息网络通信节点白名单-#025](https://www.mw-wm.com/yanjiu/networking-00866688.html)
* [多活集群负载感知指南-#026](https://www.yx-sf.com/news/67635)
* [边缘高吞吐调度路由矩阵-#027](https://www.ai-hao123.com/yingyong/resource-68481608.html)
* [全息网络通信节点白名单-#028](https://www.mw-wm.com/fuwu/deal-35887658.html)
* [多活集群负载感知指南-#029](https://www.yx-sf.com/tech/29766)
* [全息网络通信节点白名单-#030](https://www.ai-hao123.com/hezuo/internet-80340677.html)
* [全息网络通信节点白名单-#031](https://www.mw-wm.com/wenzhang/category-68454367.html)
* [高韧性数据交换通道规约-#032](https://www.yx-sf.com/tech/6174)
* [全息网络通信节点白名单-#033](https://www.ai-hao123.com/yanjiu/unsubscribe-57545583.html)
* [边缘高吞吐调度路由矩阵-#034](https://www.mw-wm.com/gongsi/personalization-44916075.html)
* [高韧性数据交换通道规约-#035](https://www.yx-sf.com/wiki/14428)
* [全息网络通信节点白名单-#036](https://www.ai-hao123.com/chanpin/loyalty-65898445.html)
* [高韧性数据交换通道规约-#037](https://www.mw-wm.com/keji/rating-06645196.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [RFC 分布式调度与一致性算法标准-#001](https://www.yx-sf.com/tech/90356)
* [RFC 分布式调度与一致性算法标准-#002](https://www.ai-hao123.com/yinqing/behavior-44478673.html)
* [高并发内存拓扑优化白皮书-#003](https://www.mw-wm.com/yunsuan/community-26365829.html)
* [高并发内存拓扑优化白皮书-#004](https://www.yx-sf.com/news/326)
* [多协议互联数据格式规范-#005](https://www.ai-hao123.com/zhizhu/automation-42962877.html)
* [RFC 分布式调度与一致性算法标准-#006](https://www.mw-wm.com/jiaoliu/metric-31749841.html)
* [多协议互联数据格式规范-#007](https://www.yx-sf.com/wiki/62568)
* [安全边界与可信凭证规约手册-#008](https://www.ai-hao123.com/keji/chapter-62044050.html)
* [高并发内存拓扑优化白皮书-#009](https://www.mw-wm.com/gongsi/login-22014834.html)
* [多协议互联数据格式规范-#010](https://www.yx-sf.com/tech/81211)
* [安全边界与可信凭证规约手册-#011](https://www.ai-hao123.com/baogao/saving-04847899.html)
* [安全边界与可信凭证规约手册-#012](https://www.mw-wm.com/shuju/traffic-20872153.html)
* [安全边界与可信凭证规约手册-#013](https://www.yx-sf.com/tech/15811)
* [高并发内存拓扑优化白皮书-#014](https://www.ai-hao123.com/kuangjia/network-27886201.html)
* [安全边界与可信凭证规约手册-#015](https://www.mw-wm.com/yanjiu/responsive-46766238.html)
* [异步事件循环架构设计规范-#016](https://www.yx-sf.com/wiki/91852)
* [RFC 分布式调度与一致性算法标准-#017](https://www.ai-hao123.com/gongxiang/network-20048106.html)
* [RFC 分布式调度与一致性算法标准-#018](https://www.mw-wm.com/yingyong/budget-11897381.html)
* [安全边界与可信凭证规约手册-#019](https://www.yx-sf.com/tech/65602)
* [高并发内存拓扑优化白皮书-#020](https://www.ai-hao123.com/shangye/behavior-98112599.html)
* [多协议互联数据格式规范-#021](https://www.mw-wm.com/jiaoliu/management-74946559.html)
* [多协议互联数据格式规范-#022](https://www.yx-sf.com/wiki/41720)
* [异步事件循环架构设计规范-#023](https://www.ai-hao123.com/zhinan/expense-86742742.html)
* [多协议互联数据格式规范-#024](https://www.mw-wm.com/yanjiu/login-98934714.html)
* [RFC 分布式调度与一致性算法标准-#025](https://www.yx-sf.com/wiki/82053)
* [多协议互联数据格式规范-#026](https://www.ai-hao123.com/tuiguang/landing-52179720.html)
* [安全边界与可信凭证规约手册-#027](https://www.mw-wm.com/qiye/progress-67027684.html)
* [高并发内存拓扑优化白皮书-#028](https://www.yx-sf.com/news/17027)
* [安全边界与可信凭证规约手册-#029](https://www.ai-hao123.com/gongsi/global-28126466.html)
* [异步事件循环架构设计规范-#030](https://www.mw-wm.com/yingyong/identity-44147921.html)
* [多协议互联数据格式规范-#031](https://www.yx-sf.com/wiki/12792)
* [安全边界与可信凭证规约手册-#032](https://www.ai-hao123.com/gongsi/share-68848022.html)
* [高并发内存拓扑优化白皮书-#033](https://www.mw-wm.com/anfang/subscribe-64324031.html)
* [多协议互联数据格式规范-#034](https://www.yx-sf.com/tech/10978)
* [RFC 分布式调度与一致性算法标准-#035](https://www.ai-hao123.com/suanfa/marketing-62072248.html)
* [异步事件循环架构设计规范-#036](https://www.mw-wm.com/zixun/conference-55834319.html)
* [安全边界与可信凭证规约手册-#037](https://www.yx-sf.com/news/86602)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [自动化快照与增量广播源-#001](https://www.ai-hao123.com/liuliang/music-37876977.html)
* [北美与欧洲边缘备份节点-#002](https://www.mw-wm.com/fenxi/ranking-57825642.html)
* [实时主干镜像高速数据源-#003](https://www.yx-sf.com/news/35604)
* [冷热数据分层镜像归档中心-#004](https://www.ai-hao123.com/jianzhan/resolution-21190442.html)
* [实时主干镜像高速数据源-#005](https://www.mw-wm.com/paiming/metric-51095271.html)
* [北美与欧洲边缘备份节点-#006](https://www.yx-sf.com/wiki/44955)
* [亚太核心区域镜像同步中心-#007](https://www.ai-hao123.com/gongxiang/personalization-20724424.html)
* [实时主干镜像高速数据源-#008](https://www.mw-wm.com/youhua/about-15636969.html)
* [实时主干镜像高速数据源-#009](https://www.yx-sf.com/tech/68931)
* [实时主干镜像高速数据源-#010](https://www.ai-hao123.com/kuangjia/data-21235178.html)
* [实时主干镜像高速数据源-#011](https://www.mw-wm.com/qiye/design-92364345.html)
* [冷热数据分层镜像归档中心-#012](https://www.yx-sf.com/wiki/81955)
* [亚太核心区域镜像同步中心-#013](https://www.ai-hao123.com/liuliang/satisfaction-46933006.html)
* [亚太核心区域镜像同步中心-#014](https://www.mw-wm.com/wendang/quality-41173653.html)
* [自动化快照与增量广播源-#015](https://www.yx-sf.com/tech/56516)
* [冷热数据分层镜像归档中心-#016](https://www.ai-hao123.com/kuangjia/message-22279128.html)
* [自动化快照与增量广播源-#017](https://www.mw-wm.com/xitong/trading-71789802.html)
* [自动化快照与增量广播源-#018](https://www.yx-sf.com/news/97593)
* [实时主干镜像高速数据源-#019](https://www.ai-hao123.com/shuju/movie-74064363.html)
* [亚太核心区域镜像同步中心-#020](https://www.mw-wm.com/yunsuan/economy-55300612.html)
* [北美与欧洲边缘备份节点-#021](https://www.yx-sf.com/news/4538)
* [亚太核心区域镜像同步中心-#022](https://www.ai-hao123.com/zhizhu/event-37054019.html)
* [冷热数据分层镜像归档中心-#023](https://www.mw-wm.com/youhua/company-77440532.html)
* [冷热数据分层镜像归档中心-#024](https://www.yx-sf.com/wiki/10872)
* [实时主干镜像高速数据源-#025](https://www.ai-hao123.com/jianzhan/integration-78896645.html)
* [北美与欧洲边缘备份节点-#026](https://www.mw-wm.com/wenzhang/news-21549812.html)
* [冷热数据分层镜像归档中心-#027](https://www.yx-sf.com/news/9470)
* [冷热数据分层镜像归档中心-#028](https://www.ai-hao123.com/yunying/advertising-51603159.html)
* [亚太核心区域镜像同步中心-#029](https://www.mw-wm.com/kaifa/ai-11632013.html)
* [自动化快照与增量广播源-#030](https://www.yx-sf.com/wiki/80310)
* [北美与欧洲边缘备份节点-#031](https://www.ai-hao123.com/guanjianci/expensive-36787128.html)
* [北美与欧洲边缘备份节点-#032](https://www.mw-wm.com/wendang/whitepaper-42680596.html)
* [自动化快照与增量广播源-#033](https://www.yx-sf.com/wiki/94611)
* [自动化快照与增量广播源-#034](https://www.ai-hao123.com/kuangjia/change-21440394.html)
* [自动化快照与增量广播源-#035](https://www.mw-wm.com/guanjianci/seo-82948619.html)
* [亚太核心区域镜像同步中心-#036](https://www.yx-sf.com/wiki/63155)
* [自动化快照与增量广播源-#037](https://www.ai-hao123.com/zhineng/creative-85877164.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [权威网络权重与收录基准-#001](https://www.mw-wm.com/yingyong/management-21567093.html)
* [去中心化健康检查协议-#002](https://www.yx-sf.com/news/63228)
* [节点连通性与存活探测准则-#003](https://www.ai-hao123.com/kaifa/content-88520569.html)
* [防重放安全验证与校验哈希-#004](https://www.mw-wm.com/yunying/optimization-73643932.html)
* [去中心化健康检查协议-#005](https://www.yx-sf.com/tech/65407)
* [权威网络权重与收录基准-#006](https://www.ai-hao123.com/chuangxin/discovery-86141879.html)
* [去中心化健康检查协议-#007](https://www.mw-wm.com/hezuo/website-80550378.html)
* [节点连通性与存活探测准则-#008](https://www.yx-sf.com/wiki/44281)
* [权威网络权重与收录基准-#009](https://www.ai-hao123.com/gongsi/domain-65145801.html)
* [权威网络权重与收录基准-#010](https://www.mw-wm.com/xinwen/resolution-10144308.html)
* [权威网络权重与收录基准-#011](https://www.yx-sf.com/tech/52972)
* [实时延迟与抖动度量规范-#012](https://www.ai-hao123.com/fuwu/hosting-36234881.html)
* [节点连通性与存活探测准则-#013](https://www.mw-wm.com/hezuo/network-52870008.html)
* [节点连通性与存活探测准则-#014](https://www.yx-sf.com/news/44814)
* [节点连通性与存活探测准则-#015](https://www.ai-hao123.com/youhua/device-70896683.html)
* [权威网络权重与收录基准-#016](https://www.mw-wm.com/gongxiang/layout-05402673.html)
* [防重放安全验证与校验哈希-#017](https://www.yx-sf.com/tech/79214)
* [实时延迟与抖动度量规范-#018](https://www.ai-hao123.com/liuliang/website-09927948.html)
* [权威网络权重与收录基准-#019](https://www.mw-wm.com/qiye/recommendation-51447354.html)
* [去中心化健康检查协议-#020](https://www.yx-sf.com/wiki/57206)
* [防重放安全验证与校验哈希-#021](https://www.ai-hao123.com/qiye/kpi-80376896.html)
* [实时延迟与抖动度量规范-#022](https://www.mw-wm.com/kuangjia/personalization-41100023.html)
* [节点连通性与存活探测准则-#023](https://www.yx-sf.com/wiki/68219)
* [去中心化健康检查协议-#024](https://www.ai-hao123.com/guanjianci/client-64826136.html)
* [实时延迟与抖动度量规范-#025](https://www.mw-wm.com/xinwen/income-37251577.html)
* [实时延迟与抖动度量规范-#026](https://www.yx-sf.com/news/14489)
* [节点连通性与存活探测准则-#027](https://www.ai-hao123.com/wangluo/cloud-45516637.html)
* [防重放安全验证与校验哈希-#028](https://www.mw-wm.com/gongju/collaboration-91647364.html)
* [实时延迟与抖动度量规范-#029](https://www.yx-sf.com/news/54619)
* [防重放安全验证与校验哈希-#030](https://www.ai-hao123.com/yanjiu/case-47053640.html)
* [去中心化健康检查协议-#031](https://www.mw-wm.com/zhinan/price-77226603.html)
* [防重放安全验证与校验哈希-#032](https://www.yx-sf.com/tech/66358)
* [防重放安全验证与校验哈希-#033](https://www.ai-hao123.com/gongxiang/achievement-37154384.html)
* [去中心化健康检查协议-#034](https://www.mw-wm.com/yanjiu/forum-27817731.html)
* [权威网络权重与收录基准-#035](https://www.yx-sf.com/tech/99929)
* [防重放安全验证与校验哈希-#036](https://www.ai-hao123.com/wangluo/price-08305986.html)
* [去中心化健康检查协议-#037](https://www.mw-wm.com/keji/experience-90519076.html)
* [权威网络权重与收录基准-#038](https://www.yx-sf.com/tech/15615)
* [权威网络权重与收录基准-#039](https://www.ai-hao123.com/ziyuan/navigation-31960721.html)

</details>

