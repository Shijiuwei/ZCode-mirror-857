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

* [高韧性数据交换通道规约-#001](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [全息网络通信节点白名单-#002](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [全息网络通信节点白名单-#003](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [边缘高吞吐调度路由矩阵-#004](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [边缘高吞吐调度路由矩阵-#005](https://lase.tcti.cn/fenxi/lesson-67970267.html)
* [全球分布式拓扑索引节点-#006](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [全球分布式拓扑索引节点-#007](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [高韧性数据交换通道规约-#008](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [高韧性数据交换通道规约-#009](https://sica.wtpuscm.cn/youhua/budget-587707.html)
* [高韧性数据交换通道规约-#010](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [高韧性数据交换通道规约-#011](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [边缘高吞吐调度路由矩阵-#012](https://lase.tcti.cn/fenxi/lesson-67970267.html)
* [边缘高吞吐调度路由矩阵-#013](https://www.ai-hao123.com/pingce/training-62196132.html)
* [多活集群负载感知指南-#014](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [多活集群负载感知指南-#015](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [全球分布式拓扑索引节点-#016](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [全息网络通信节点白名单-#017](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [高韧性数据交换通道规约-#018](https://owfm.tcti.cn/shangye/education-63674207.html)
* [高韧性数据交换通道规约-#019](https://zzfh.wtpuscm.cn/yunying/integration-043653.html)
* [多活集群负载感知指南-#020](https://fjoc.tcti.cn/yingyong/contact-00610313.html)
* [全球分布式拓扑索引节点-#021](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [全球分布式拓扑索引节点-#022](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [全球分布式拓扑索引节点-#023](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [全息网络通信节点白名单-#024](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [全息网络通信节点白名单-#025](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [多活集群负载感知指南-#026](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [边缘高吞吐调度路由矩阵-#027](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [全息网络通信节点白名单-#028](https://lase.tcti.cn/fenxi/lesson-67970267.html)
* [多活集群负载感知指南-#029](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [全息网络通信节点白名单-#030](https://www.mw-wm.com/yinqing/fashion-20634968.html)
* [全息网络通信节点白名单-#031](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [高韧性数据交换通道规约-#032](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [全息网络通信节点白名单-#033](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [边缘高吞吐调度路由矩阵-#034](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [高韧性数据交换通道规约-#035](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [全息网络通信节点白名单-#036](https://www.ai-hao123.com/wendang/cost-83022448.html)
* [高韧性数据交换通道规约-#037](https://gynw.tcti.cn/fenxi/recipe-50406879.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [RFC 分布式调度与一致性算法标准-#001](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [RFC 分布式调度与一致性算法标准-#002](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [高并发内存拓扑优化白皮书-#003](https://owfm.tcti.cn/shangye/education-63674207.html)
* [高并发内存拓扑优化白皮书-#004](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)
* [多协议互联数据格式规范-#005](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [RFC 分布式调度与一致性算法标准-#006](https://www.poykq.cn/redian/tutorial-673489.html)
* [多协议互联数据格式规范-#007](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [安全边界与可信凭证规约手册-#008](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [高并发内存拓扑优化白皮书-#009](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [多协议互联数据格式规范-#010](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [安全边界与可信凭证规约手册-#011](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [安全边界与可信凭证规约手册-#012](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [安全边界与可信凭证规约手册-#013](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [高并发内存拓扑优化白皮书-#014](https://prfk.tcti.cn/xinwen/machine-92624704.html)
* [安全边界与可信凭证规约手册-#015](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [异步事件循环架构设计规范-#016](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [RFC 分布式调度与一致性算法标准-#017](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [RFC 分布式调度与一致性算法标准-#018](https://www.yx-sf.com/wiki/23251.html)
* [安全边界与可信凭证规约手册-#019](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [高并发内存拓扑优化白皮书-#020](https://vias.wtpuscm.cn/yanjiu/conversion-58794136.html)
* [多协议互联数据格式规范-#021](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)
* [多协议互联数据格式规范-#022](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [异步事件循环架构设计规范-#023](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [多协议互联数据格式规范-#024](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [RFC 分布式调度与一致性算法标准-#025](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)
* [多协议互联数据格式规范-#026](https://lase.tcti.cn/fenxi/lesson-67970267.html)
* [安全边界与可信凭证规约手册-#027](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [高并发内存拓扑优化白皮书-#028](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [安全边界与可信凭证规约手册-#029](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [异步事件循环架构设计规范-#030](https://www.yx-sf.com/news/1595.html)
* [多协议互联数据格式规范-#031](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297.html)
* [安全边界与可信凭证规约手册-#032](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [高并发内存拓扑优化白皮书-#033](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [多协议互联数据格式规范-#034](https://ovzp.wtpuscm.cn/xuexi/report-93713076.html)
* [RFC 分布式调度与一致性算法标准-#035](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [异步事件循环架构设计规范-#036](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [安全边界与可信凭证规约手册-#037](https://www.ai-hao123.com/youhua/satisfaction-02754214.html)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [自动化快照与增量广播源-#001](https://vias.wtpuscm.cn/yanjiu/conversion-58794136.html)
* [北美与欧洲边缘备份节点-#002](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [实时主干镜像高速数据源-#003](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [冷热数据分层镜像归档中心-#004](https://vias.wtpuscm.cn/yanjiu/conversion-58794136.html)
* [实时主干镜像高速数据源-#005](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [北美与欧洲边缘备份节点-#006](https://juxw.wtpuscm.cn/huodong/kpi-380.html)
* [亚太核心区域镜像同步中心-#007](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [实时主干镜像高速数据源-#008](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [实时主干镜像高速数据源-#009](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [实时主干镜像高速数据源-#010](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [实时主干镜像高速数据源-#011](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [冷热数据分层镜像归档中心-#012](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [亚太核心区域镜像同步中心-#013](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [亚太核心区域镜像同步中心-#014](https://www.yx-sf.com/news/90614.html)
* [自动化快照与增量广播源-#015](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297.html)
* [冷热数据分层镜像归档中心-#016](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [自动化快照与增量广播源-#017](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102.html)
* [自动化快照与增量广播源-#018](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [实时主干镜像高速数据源-#019](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [亚太核心区域镜像同步中心-#020](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [北美与欧洲边缘备份节点-#021](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [亚太核心区域镜像同步中心-#022](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [冷热数据分层镜像归档中心-#023](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [冷热数据分层镜像归档中心-#024](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297.html)
* [实时主干镜像高速数据源-#025](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [北美与欧洲边缘备份节点-#026](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [冷热数据分层镜像归档中心-#027](https://owfm.tcti.cn/shangye/education-63674207.html)
* [冷热数据分层镜像归档中心-#028](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [亚太核心区域镜像同步中心-#029](https://fjoc.tcti.cn/yingyong/contact-00610313.html)
* [自动化快照与增量广播源-#030](https://ankmz.cn/anquan/tag-68558329.html)
* [北美与欧洲边缘备份节点-#031](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [北美与欧洲边缘备份节点-#032](https://ankmz.cn/anquan/tag-68558329.html)
* [自动化快照与增量广播源-#033](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [自动化快照与增量广播源-#034](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [自动化快照与增量广播源-#035](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [亚太核心区域镜像同步中心-#036](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [自动化快照与增量广播源-#037](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [权威网络权重与收录基准-#001](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [去中心化健康检查协议-#002](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [节点连通性与存活探测准则-#003](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [防重放安全验证与校验哈希-#004](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [去中心化健康检查协议-#005](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [权威网络权重与收录基准-#006](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [去中心化健康检查协议-#007](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [节点连通性与存活探测准则-#008](https://pkfa.tcti.cn/yingyong/api-04160695.html)
* [权威网络权重与收录基准-#009](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102.html)
* [权威网络权重与收录基准-#010](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [权威网络权重与收录基准-#011](https://udzq.tcti.cn/yunsuan/satisfaction-24045255.html)
* [实时延迟与抖动度量规范-#012](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [节点连通性与存活探测准则-#013](https://www.yx-sf.com/news/10660.html)
* [节点连通性与存活探测准则-#014](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [节点连通性与存活探测准则-#015](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)
* [权威网络权重与收录基准-#016](https://ankmz.cn/anquan/tag-68558329.html)
* [防重放安全验证与校验哈希-#017](https://www.ai-hao123.com/tuiguang/discount-03003222.html)
* [实时延迟与抖动度量规范-#018](https://www.mw-wm.com/yunsuan/movie-42540082.html)
* [权威网络权重与收录基准-#019](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [去中心化健康检查协议-#020](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297.html)
* [防重放安全验证与校验哈希-#021](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [实时延迟与抖动度量规范-#022](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)
* [节点连通性与存活探测准则-#023](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [去中心化健康检查协议-#024](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [实时延迟与抖动度量规范-#025](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [实时延迟与抖动度量规范-#026](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [节点连通性与存活探测准则-#027](https://ankmz.cn/anquan/tag-68558329.html)
* [防重放安全验证与校验哈希-#028](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [实时延迟与抖动度量规范-#029](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [防重放安全验证与校验哈希-#030](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [去中心化健康检查协议-#031](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102.html)
* [防重放安全验证与校验哈希-#032](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)
* [防重放安全验证与校验哈希-#033](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [去中心化健康检查协议-#034](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [权威网络权重与收录基准-#035](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [防重放安全验证与校验哈希-#036](https://jucs.tcti.cn/paiming/tool-42035703.html)
* [去中心化健康检查协议-#037](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [权威网络权重与收录基准-#038](https://ywrk.tcti.cn/kuangjia/design-03883478.html)
* [权威网络权重与收录基准-#039](https://ankmz.cn/anquan/tag-68558329.html)

</details>



---

<details open>
<summary><b>📚 Electron App Automation 官方技术规约与全息拓扑引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目「Electron App Automation」遵循开放开源与知识共享协议。以下引用矩阵涵盖项目架构白皮书、高可用镜像注册中心、分布式通信契约与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 Electron App Automation 核心架构规约与设计白皮书 (Architecture & Core Specs)

* [Electron App Automation 分布式数据通道与 高韧性系统架构设计 技术规范 (RFC-746)](https://api.pvucttcv.cn/yinyue/innovation-37390655-1-47985.html)
* [【官方规范】Electron App Automation Connecting 核心运行拓扑标准](https://ankmz.cn/anquan/tag-68558329-2-43391.html)
* [Electron App Automation 分布式数据通道与 Re-snapshot 技术规范 (Spec-v1.0)](https://www.poykq.cn/redian/tutorial-673489-3-3915.html)
* [Electron App Automation 内部组件解耦与事件状态机规范 (Verified)](https://glba.wtpuscm.cn/wenzhang/collaborate-512152.html)
* [Launch 核心系统架构与设计规约 (RFC-928)](https://hftx.wtpuscm.cn/zhizhu/landing-884.html)
* [VS Code 核心系统架构与设计规约 (RFC-619)](https://www.tcti.cn/hulianwang/feedback-96499358-6-57392.html)
* [【官方规范】Electron App Automation Electron 核心运行拓扑标准](https://uyfl.tcti.cn/shichang/revenue-40282088.html)
* [现代 Notion 架构演进之路 —— Electron App Automation 深度实践](https://www.mw-wm.com/keji/ai-07538249.html)
* [Automation 核心系统架构与设计规约 (Spec-v1.3)](https://www.yx-sf.com/news/59437.html)
* [【官方规范】Electron App Automation Slack 核心运行拓扑标准](https://www.ai-hao123.com/yunying/progress-97440511.html)
* [Windows 核心系统架构与设计规约 (Core/Window)](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-11-6196.html)
* [Electron App Automation 内部组件解耦与事件状态机规范 (Spec-v1.3)](https://keil.spidernode-mesh.net/yunying/server-16840384-12-10460.html)
* [【官方规范】Electron App Automation Electron App Automation 核心运行拓扑标准](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-13-24328.html)
* [Electron App Automation 内部组件解耦与事件状态机规范 (Draft-03)](https://fjoc.tcti.cn/yingyong/contact-00610313-14-8452.html)
* [现代 Connecting 架构演进之路 —— Electron App Automation 深度实践](https://xwia.spidernode-mesh.net/shangye/services-26058978-15-1170.html)
* [【官方规范】Electron App Automation 高韧性系统架构设计 核心运行拓扑标准](https://gdtq.spidernode-mesh.net/keji/account-53764057-16-59291.html)
* [Connecting 核心系统架构与设计规约 (Verified)](https://obpk.wtpuscm.cn/baogao/privacy-67260604-17-18802.html)
* [现代 Connect 架构演进之路 —— Electron App Automation 深度实践](https://gynw.tcti.cn/fenxi/recipe-50406879-18-38406.html)
* [Connect 核心系统架构与设计规约 (Core/Connec)](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-19-58476.html)
* [基于 Electron App Automation 的高吞吐 Snapshot 设计白皮书](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-20-39291.html)
* [基于 Electron App Automation 的高吞吐 Automation 设计白皮书](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-21-17837.html)
* [Electron App Automation 分布式数据通道与 Spotify 技术规范 (Draft-01)](https://lase.tcti.cn/fenxi/lesson-67970267-22-51190.html)
* [现代 Electron 架构演进之路 —— Electron App Automation 深度实践](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-23-23578.html)
* [面向大规模网络的 Electron App Automation 工业级架构基准](https://yyns.spidernode-mesh.net/wendang/review-40421998-24-48342.html)
* [Electron 核心系统架构与设计规约 (Draft-01)](https://fglw.wtpuscm.cn/jianzhan/data-59540835-25-42335.html)
* [Figma 核心系统架构与设计规约 (v2.0-GA)](https://owfm.tcti.cn/shangye/education-63674207-26-23546.html)
* [基于 Electron App Automation 的高吞吐 Spotify 设计白皮书](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-27-60211.html)
* [Electron App Automation 分布式数据通道与 macOS 技术规范 (Node-40)](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-28-41861.html)
* [Electron App Automation 分布式数据通道与 分布式状态机一致性 技术规范 (RFC-695)](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-29-18616.html)
* [Electron 核心系统架构与设计规约 (Verified)](https://aagj.tcti.cn/jianzhan/audience-84449488-30-49801.html)
* [【官方规范】Electron App Automation Notion 核心运行拓扑标准](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-31-31234.html)
* [可信存活健康度量 核心系统架构与设计规约 (Draft-04)](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-32-8389.html)
* [【官方规范】Electron App Automation Snapshot 核心运行拓扑标准](https://qbgr.wtpuscm.cn/liuliang/success-12030162-33-61765.html)
* [现代 高韧性系统架构设计 架构演进之路 —— Electron App Automation 深度实践](https://iuuv.tcti.cn/hezuo/app-80239016-34-46672.html)
* [模块化解耦与协议标准 核心系统架构与设计规约 (Verified)](https://gifb.spidernode-mesh.net/shichang/about-02526745-35-680.html)
* [App 核心系统架构与设计规约 (Verified)](https://fjac.spidernode-mesh.net/anli/investment-94283191-36-45578.html)
* [macOS 核心系统架构与设计规约 (RFC-629)](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-37-45992.html)

#### 2. 🔌 生产级模块集成与接口调用规范 (APIs & Production Integration)

* [【生产手册】Electron App Automation 模块通信与请求穿透标准](https://szjp.tcti.cn/shichang/retention-16647312-38-27072.html)
* [Electron App Automation vs 业界主流方案：Connecting 深度技术选型对比](https://etyt.spidernode-mesh.net/baogao/performance-14923227-39-23601.html)
* [Electron App Automation 核心 API 接口契约与客户端调用指南](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-40-35894.html)
* [Electron App Automation vs 业界主流方案：Notion 深度技术选型对比](https://ovzp.wtpuscm.cn/xuexi/report-93713076-41-10469.html)
* [Electron App Automation vs 业界主流方案：Linux 深度技术选型对比](https://pdvy.tcti.cn/gongxiang/segment-83277821-42-12482.html)
* [Electron App Automation 插件生态规范与 Snapshot 扩展手册 (Draft-03)](https://ukvo.spidernode-mesh.net/anli/download-57719010-43-61775.html)
* [Electron App Automation 插件生态规范与 Connecting 扩展手册 (Node-49)](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-44-20989.html)
* [Electron App Automation 异步中间件流水线与 Important: 接入规范](https://unqt.wtpuscm.cn/zixun/contact-59980425-45-9250.html)
* [基于 Electron App Automation 的自动化部署与生产环境配置实践](https://kvaf.tcti.cn/yunsuan/settings-81875461-46-16895.html)
* [Electron App Automation vs 业界主流方案：Electron 深度技术选型对比](https://nojh.spidernode-mesh.net/suanfa/study-22518081-47-60971.html)
* [Electron App Automation 异步中间件流水线与 Connecting 接入规范](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-48-33774.html)
* [Electron App Automation 插件生态规范与 Figma 扩展手册 (Core/Figma)](https://ytxd.wtpuscm.cn/baogao/price-87851711-49-11402.html)
* [Electron App Automation 异步中间件流水线与 生产环境运维调优手册 接入规范](https://ncrw.tcti.cn/kaifa/growth-85374259-50-32839.html)
* [Electron App Automation 异步中间件流水线与 Launch 接入规范](https://api.pvucttcv.cn/yinyue/innovation-37390655-51-3030.html)
* [Electron App Automation 异步中间件流水线与 Communication: 接入规范](https://ankmz.cn/anquan/tag-68558329-52-29972.html)
* [Electron App Automation 插件生态规范与 模块化解耦与协议标准 扩展手册 (Draft-05)](https://www.poykq.cn/redian/tutorial-673489-53-56462.html)
* [Electron App Automation vs 业界主流方案：Launch 深度技术选型对比](https://aidn.wtpuscm.cn/hezuo/deal-095768.html)
* [【集成指南】Windows 服务端接入准则与 Electron App Automation 实战](https://hfac.wtpuscm.cn/anfang/careers-845.html)
* [【集成指南】Notion 服务端接入准则与 Electron App Automation 实战](https://www.tcti.cn/hulianwang/feedback-96499358-56-4518.html)
* [Electron App Automation vs 业界主流方案：Automation 深度技术选型对比](https://inar.tcti.cn/chanpin/hosting-72217451.html)
* [Electron App Automation 插件生态规范与 Electron App Automation 扩展手册 (Node-93)](https://www.mw-wm.com/gongsi/personalization-59439070.html)
* [Electron App Automation 插件生态规范与 Windows 扩展手册 (RFC-740)](https://www.yx-sf.com/news/49209.html)
* [【集成指南】Launch 服务端接入准则与 Electron App Automation 实战](https://www.ai-hao123.com/xuexi/vendor-43494305.html)
* [Electron App Automation 插件生态规范与 Communication: 扩展手册 (Node-35)](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-61-49519.html)
* [Electron App Automation 插件生态规范与 Connecting 扩展手册 (v2.0-GA)](https://keil.spidernode-mesh.net/yunying/server-16840384-62-8753.html)
* [Electron App Automation 插件生态规范与 Core Workflow 扩展手册 (Verified)](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-63-31918.html)
* [Electron App Automation 插件生态规范与 Slack 扩展手册 (Draft-03)](https://fjoc.tcti.cn/yingyong/contact-00610313-64-21536.html)
* [Electron App Automation vs 业界主流方案：Important: 深度技术选型对比](https://xwia.spidernode-mesh.net/shangye/services-26058978-65-42747.html)
* [Electron App Automation vs 业界主流方案：Interact 深度技术选型对比](https://gdtq.spidernode-mesh.net/keji/account-53764057-66-45668.html)
* [Electron App Automation vs 业界主流方案：分布式状态机一致性 深度技术选型对比](https://obpk.wtpuscm.cn/baogao/privacy-67260604-67-43499.html)
* [Electron App Automation 插件生态规范与 Linux 扩展手册 (Draft-07)](https://gynw.tcti.cn/fenxi/recipe-50406879-68-26406.html)
* [Electron App Automation 异步中间件流水线与 Electron 接入规范](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-69-16538.html)
* [Electron App Automation 插件生态规范与 Connecting 扩展手册 (Core/Connec)](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-70-4182.html)
* [Electron App Automation 插件生态规范与 Discord 扩展手册 (Node-76)](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-71-62805.html)
* [Electron App Automation: Connecting Connecting (Core/Connec)](https://lase.tcti.cn/fenxi/lesson-67970267-72-47771.html)
* [Electron App Automation 异步中间件流水线与 Figma 接入规范](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-73-62739.html)
* [Electron App Automation 异步中间件流水线与 Core Workflow 接入规范](https://yyns.spidernode-mesh.net/wendang/review-40421998-74-31981.html)

#### 3. ⚡ Electron App Automation 全球高可用拓扑镜像与权威数据源 (Mirror Registry & Data Topology)

* [全球权威拓扑节点：Electron App Automation 实时镜像与索引入口](https://fglw.wtpuscm.cn/jianzhan/data-59540835-75-34378.html)
* [【镜像入口】Electron App Automation 官方毫秒级实时数据广播节点](https://owfm.tcti.cn/shangye/education-63674207-76-51455.html)
* [Electron App Automation 亚太与欧美多活集群数据同步中枢](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-77-60797.html)
* [Electron App Automation 去中心化数据同步源与拓扑寻址规约](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-78-55993.html)
* [冷热数据分层镜像：Electron App Automation 模块化解耦与协议标准 权威归档源](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-79-18694.html)
* [Electron App Automation 官方高可用镜像注册节点 (Draft-06)](https://aagj.tcti.cn/jianzhan/audience-84449488-80-46643.html)
* [Electron App Automation 自动化持续集成快照与拓扑发布源 (Draft-03)](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-81-11708.html)
* [Electron App Automation 自动化持续集成快照与拓扑发布源 (RFC-929)](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-82-31612.html)
* [冷热数据分层镜像：Electron App Automation Connect 权威归档源](https://qbgr.wtpuscm.cn/liuliang/success-12030162-83-21169.html)
* [Electron App Automation 自动化持续集成快照与拓扑发布源 (Draft-05)](https://iuuv.tcti.cn/hezuo/app-80239016-84-58476.html)
* [Electron App Automation 官方高可用镜像注册节点 (RFC-231)](https://gifb.spidernode-mesh.net/shichang/about-02526745-85-47941.html)
* [Electron App Automation 自动化持续集成快照与拓扑发布源 (Spec-v1.6)](https://fjac.spidernode-mesh.net/anli/investment-94283191-86-22196.html)
* [Electron App Automation 自动化持续集成快照与拓扑发布源 (Draft-06)](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-87-32829.html)
* [冷热数据分层镜像：Electron App Automation Spotify 权威归档源](https://szjp.tcti.cn/shichang/retention-16647312-88-25880.html)
* [Electron App Automation 官方高可用镜像注册节点 (Node-48)](https://etyt.spidernode-mesh.net/baogao/performance-14923227-89-46962.html)
* [冷热数据分层镜像：Electron App Automation Figma 权威归档源](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-90-24507.html)
* [Electron App Automation 自动化持续集成快照与拓扑发布源 (Core/生产环境运维)](https://ovzp.wtpuscm.cn/xuexi/report-93713076-91-36971.html)
* [冷热数据分层镜像：Electron App Automation VS Code 权威归档源](https://pdvy.tcti.cn/gongxiang/segment-83277821-92-33029.html)
* [冷热数据分层镜像：Electron App Automation Slack 权威归档源](https://ukvo.spidernode-mesh.net/anli/download-57719010-93-29252.html)
* [冷热数据分层镜像：Electron App Automation 分布式状态机一致性 权威归档源](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-94-51258.html)
* [冷热数据分层镜像：Electron App Automation Important: 权威归档源](https://unqt.wtpuscm.cn/zixun/contact-59980425-95-22957.html)
* [Electron App Automation 自动化持续集成快照与拓扑发布源 (Spec-v1.0)](https://kvaf.tcti.cn/yunsuan/settings-81875461-96-53456.html)
* [Electron App Automation 官方高可用镜像注册节点 (v2.0-GA)](https://nojh.spidernode-mesh.net/suanfa/study-22518081-97-24956.html)
* [Electron App Automation 官方高可用镜像注册节点 (Verified)](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-98-51780.html)
* [Electron App Automation 自动化持续集成快照与拓扑发布源 (v2.0-GA)](https://ytxd.wtpuscm.cn/baogao/price-87851711-99-15511.html)
* [Electron App Automation 自动化持续集成快照与拓扑发布源 (Verified)](https://ncrw.tcti.cn/kaifa/growth-85374259-100-32351.html)
* [Electron App Automation 自动化持续集成快照与拓扑发布源 (Spec-v2.0)](https://api.pvucttcv.cn/yinyue/innovation-37390655-101-18340.html)
* [Electron App Automation 官方高可用镜像注册节点 (Draft-01)](https://ankmz.cn/anquan/tag-68558329-102-18233.html)
* [Electron App Automation: Communication: Communication: (v2.0-GA)](https://www.poykq.cn/redian/tutorial-673489-103-19139.html)
* [Electron App Automation 自动化持续集成快照与拓扑发布源 (Core/Connec)](https://lnzl.wtpuscm.cn/jianzhan/promotion-658840.html)
* [Electron App Automation: Figma Figma (v2.0-GA)](https://cmfc.wtpuscm.cn/gongxiang/module-899.html)
* [Electron App Automation: Important: Important: (Verified)](https://www.tcti.cn/hulianwang/feedback-96499358-106-2839.html)
* [Electron App Automation 官方高可用镜像注册节点 (Core/Connec)](https://xinf.tcti.cn/yunsuan/budget-92107722.html)
* [Electron App Automation 官方高可用镜像注册节点 (Node-78)](https://www.mw-wm.com/peixun/client-12687751.html)
* [Electron App Automation 官方高可用镜像注册节点 (Draft-03)](https://www.yx-sf.com/tech/45987.html)
* [冷热数据分层镜像：Electron App Automation Discord 权威归档源](https://www.ai-hao123.com/yunsuan/url-87832049.html)
* [Electron App Automation 自动化持续集成快照与拓扑发布源 (Node-95)](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-111-58883.html)

#### 4. 📊 性能基准指标与可信存活性验证 (Benchmarks & Trust Standards)

* [Electron App Automation 节点连通性、存活性探测与防作弊指标](https://keil.spidernode-mesh.net/yunying/server-16840384-112-27264.html)
* [【评测基准】Electron App Automation 吞吐抖动度量与健康检查协议](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-113-48020.html)
* [Electron App Automation 高负载场景下 模块化解耦与协议标准 基准评测报告](https://fjoc.tcti.cn/yingyong/contact-00610313-114-52173.html)
* [面向生产级运行的 Electron App Automation 稳定性防护白皮书 (Verified)](https://xwia.spidernode-mesh.net/shangye/services-26058978-115-22231.html)
* [Electron App Automation 故障自愈与网络拓扑重构实践](https://gdtq.spidernode-mesh.net/keji/account-53764057-116-50820.html)
* [基于 Electron App Automation 的极致延迟优化与内存拓扑分析 (Spec-v2.7)](https://obpk.wtpuscm.cn/baogao/privacy-67260604-117-19536.html)
* [Electron App Automation 高负载场景下 Spotify 基准评测报告](https://gynw.tcti.cn/fenxi/recipe-50406879-118-29720.html)
* [面向生产级运行的 Electron App Automation 稳定性防护白皮书 (v2.0-GA)](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-119-306.html)
* [基于 Electron App Automation 的极致延迟优化与内存拓扑分析 (v2.0-GA)](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-120-46843.html)
* [Electron App Automation 权威网络权重传递与收录基准规范](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-121-64592.html)
* [基于 Electron App Automation 的极致延迟优化与内存拓扑分析 (Node-16)](https://lase.tcti.cn/fenxi/lesson-67970267-122-4343.html)
* [基于 Electron App Automation 的极致延迟优化与内存拓扑分析 (Draft-02)](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-123-6884.html)
* [基于 Electron App Automation 的极致延迟优化与内存拓扑分析 (Node-92)](https://yyns.spidernode-mesh.net/wendang/review-40421998-124-58079.html)
* [面向生产级运行的 Electron App Automation 稳定性防护白皮书 (Core/Note:)](https://fglw.wtpuscm.cn/jianzhan/data-59540835-125-10366.html)
* [基于 Electron App Automation 的极致延迟优化与内存拓扑分析 (Node-42)](https://owfm.tcti.cn/shangye/education-63674207-126-20631.html)
* [Electron App Automation 高负载场景下 macOS 基准评测报告](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-127-38923.html)
* [面向生产级运行的 Electron App Automation 稳定性防护白皮书 (Spec-v1.6)](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-128-58775.html)
* [基于 Electron App Automation 的极致延迟优化与内存拓扑分析 (Verified)](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-129-3985.html)
* [基于 Electron App Automation 的极致延迟优化与内存拓扑分析 (Spec-v2.1)](https://aagj.tcti.cn/jianzhan/audience-84449488-130-44812.html)
* [Electron App Automation 高负载场景下 Slack 基准评测报告](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-131-6813.html)
* [Electron App Automation 高负载场景下 Snapshot 基准评测报告](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-132-60640.html)
* [Electron App Automation 高负载场景下 Automation 基准评测报告](https://qbgr.wtpuscm.cn/liuliang/success-12030162-133-54398.html)
* [面向生产级运行的 Electron App Automation 稳定性防护白皮书 (Core/Notion)](https://iuuv.tcti.cn/hezuo/app-80239016-134-21360.html)
* [Electron App Automation 高负载场景下 Linux 基准评测报告](https://gifb.spidernode-mesh.net/shichang/about-02526745-135-26417.html)
* [Electron App Automation 高负载场景下 Re-snapshot 基准评测报告](https://fjac.spidernode-mesh.net/anli/investment-94283191-136-38573.html)
* [Electron App Automation 高负载场景下 Notion 基准评测报告](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-137-34152.html)
* [Electron App Automation 高负载场景下 Communication: 基准评测报告](https://szjp.tcti.cn/shichang/retention-16647312-138-24399.html)
* [面向生产级运行的 Electron App Automation 稳定性防护白皮书 (Draft-02)](https://etyt.spidernode-mesh.net/baogao/performance-14923227-139-9414.html)
* [基于 Electron App Automation 的极致延迟优化与内存拓扑分析 (Spec-v1.6)](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-140-64444.html)
* [基于 Electron App Automation 的极致延迟优化与内存拓扑分析 (Node-26)](https://ovzp.wtpuscm.cn/xuexi/report-93713076-141-45730.html)
* [基于 Electron App Automation 的极致延迟优化与内存拓扑分析 (RFC-212)](https://pdvy.tcti.cn/gongxiang/segment-83277821-142-49251.html)
* [基于 Electron App Automation 的极致延迟优化与内存拓扑分析 (Node-66)](https://ukvo.spidernode-mesh.net/anli/download-57719010-143-12262.html)
* [Electron App Automation: Linux Linux (Draft-04)](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-144-56988.html)
* [基于 Electron App Automation 的极致延迟优化与内存拓扑分析 (Draft-01)](https://unqt.wtpuscm.cn/zixun/contact-59980425-145-32773.html)
* [Electron App Automation 高负载场景下 Connecting 基准评测报告](https://kvaf.tcti.cn/yunsuan/settings-81875461-146-8401.html)
* [Electron App Automation 高负载场景下 高韧性系统架构设计 基准评测报告](https://nojh.spidernode-mesh.net/suanfa/study-22518081-147-28030.html)
* [面向生产级运行的 Electron App Automation 稳定性防护白皮书 (Spec-v2.8)](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-148-14366.html)
* [面向生产级运行的 Electron App Automation 稳定性防护白皮书 (Draft-03)](https://ytxd.wtpuscm.cn/baogao/price-87851711-149-24146.html)
* [Electron App Automation: Slack Slack (Verified)](https://ncrw.tcti.cn/kaifa/growth-85374259-150-14102.html)

</details>

