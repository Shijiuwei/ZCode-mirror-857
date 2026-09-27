# ZCode

<div align="center">
  <img src="public/logo/icons/1024x1024.png" alt="ZCode" width="128" height="128" />
</div>
<p align="center">
  <a href="https://www.yx-sf.com/wiki/85984">飞书社群</a> ·
  <a href="https://www.ai-hao123.com/fenxi/lesson-90009249.html">Discord</a>
</p>
<p align="center">
  简体中文 | <a href="README.en.md">English</a>
</p>



ZCode 是 AI 编程工作台，提供桌面应用、浏览器界面和终端 Agent。本仓库包含客户端、后端服务、共享 UI，以及 Agent CLI 与运行时源码。

## 更新

- 2026-9-23：更新至 ZCode v3.14.3 版本。

## 初始化

准备 Git、Node.js **24.14.0** 和 pnpm **10.33.2**，版本以 [mise.toml](mise.toml) 为准。以下开发和打包命令均在仓库根目录执行。

```bash
pnpm bootstrap
```

`pnpm bootstrap` 安装 workspace 依赖、准备桌面本地运行资源，再执行 `build:bootstrap`。

Agent CLI 与运行时源码位于 [apps/zcode-cli/](apps/zcode-cli/)，作为普通目录随本仓库一起克隆，无需单独拉取或初始化 Git submodule。

根据需要选择其他初始化或构建入口：

| 命令                           | 用途                                                              |
| ------------------------------ | ----------------------------------------------------------------- |
| `pnpm install`                 | 安装依赖                                                          |
| `pnpm prepare:desktop-runtime` | 准备桌面运行资源，默认包含远程资源准备                            |
| `pnpm prepare:remote-assets`   | 单独准备远程运行资源                                              |
| `pnpm bootstrap:with-remote`   | 初始化依赖、本地与远程资源，并串行构建相关包；跳过桌面应用 bundle |
| `pnpm build`                   | 递归执行各 workspace 包的构建脚本，包括包内的资源准备步骤         |

默认 `bootstrap` 跳过远程资源准备，适合本地桌面开发。使用远程工作区或验证远程发行资源时，再运行对应准备命令。

## 开发与运行

### 桌面版

```bash
pnpm dev:desktop

# 使用测试环境
pnpm dev:desktop:test
```

`pnpm dev:desktop` 默认等同于 `pnpm dev:desktop:prod`，使用生产服务配置。启动脚本会准备本地运行资源、构建桌面 Agent，再启动 Electron 和源码监听。

需要独立开发数据目录时，可设置 `ZCODE_DATA_BASE_DIR`。例如在 macOS / Linux 中：

```bash
ZCODE_DATA_BASE_DIR="$HOME/.zcode-dev-home" pnpm dev:desktop:test
```

### 远程功能（SSH/WSL）

先执行 `pnpm bootstrap:with-remote` 准备远程资源（mock-cdn），再 `pnpm dev:desktop`；连接远程项目时资源选择「本地下载后上传」。开发态资源取自本地 `packages/desktop/mock-cdn` 和本地构建产物，经 SFTP 上传到远程，不访问 CDN。

### Web 开发

修改 Web 或后端源码时，使用开发模式：

```bash
pnpm dev:web

# 指定后端工作区（macOS / Linux）
ZCODE_SERVER_WORKSPACE=/path/to/project pnpm dev:web
```

该命令同时启动 Web 开发服务器（默认 `http://localhost:5173`）和后端（默认 `http://localhost:3030`）；浏览器访问前者。`/ws` 和一般 `/api` 请求代理到本地后端，`/api/v1/oauth/token` 单独代理到当前配置的产品服务。

Agent 源码修改后，执行 `pnpm --filter @zcode/cli... build` 并重启服务。需要验证完整发行包时，按下方“ZCode 命令行版”打包章节解压运行。

### ZCode 命令行版

命令行发行包包含 TUI、Web 和 Agent，统一使用 `zcode` 启动：无参数进入 TUI；第一个参数为 `--web` 时启动 Web；其他参数交给现有 Agent CLI 处理。两种模式都在本机运行，无需 Electron。

```bash
# 默认进入终端交互界面
zcode

# 启动 Web 界面
zcode --web

# 指定项目和端口，不自动打开浏览器
zcode --web --workspace /path/to/project --port 3030 --no-open

# 查看 CLI 或 Web 参数
zcode --help
zcode --web --help
```

Web 模式默认工作目录为当前目录，监听 `127.0.0.1`，默认不启用访问令牌，自动选择空闲端口并打开浏览器。访问终端输出的地址，按 `Ctrl+C` 停止服务。局域网访问可使用 `--host 0.0.0.0`；监听非本机地址时默认生成访问令牌，使用终端输出的带令牌链接。可通过 `--token` 指定令牌或 `--no-token` 关闭令牌认证。

直接启动通用 Web 服务的 HTTP 入口时，通过 `ZCODE_SERVER_AUTH_TOKEN` 配置 API／WebSocket 认证；通过程序接口创建服务时，使用 `authToken` 选项。

构建方式见下方打包章节。`pnpm build:zcode` 只生成发行包，不会替换 `PATH` 中已有的 `zcode`。如果命令仍指向旧安装或其他源码目录，macOS / Linux 可用 `command -v zcode` 检查，Windows 可用 `where.exe zcode` 检查。

### CLI 源码开发

直接开发 TUI 或 Agent 时，运行源码入口：

```bash
pnpm --filter @zcode/cli dev --help
pnpm --filter @zcode/cli dev

# 构建 CLI 及其 workspace 依赖
pnpm --filter @zcode/cli... build
node apps/zcode-cli/packages/cli/dist/zcode.cjs --help
```

这个入口直接运行 Agent CLI，不经过发行包的 `--web` 分流。开发 Web 用 `pnpm dev:web`；验证统一的 `zcode` 命令，用下方解压后的 `bin/zcode.mjs`。

## 配置

根目录 [.env.example](.env.example) 提供服务地址与构建配置示例，可按需复制到 `.env`，本地覆盖放入 `.env.local`。Desktop 的开发环境通过 `dev:desktop:test` / `dev:desktop:prod` 选择。

| 配置                                 | 用途                                             |
| ------------------------------------ | ------------------------------------------------ |
| `ZCODE_DATA_BASE_DIR`                | 应用数据基目录，数据写入其下的 `.zcode/`         |
| `ZCODE_SERVER_WORKSPACE`             | Web 后端的工作区路径                             |
| `ZCODE_BUILTIN_PROVIDER_CONFIG_FILE` | 本地 Provider 配置文件路径；未设置时使用内置配置 |
| `ZCODE_DIST_BASE_URL`                | 命令行安装脚本使用的下载根地址                   |

运行时变量可在启动命令的环境中显式设置。随客户端发布的默认配置见 [config/README.md](config/README.md)。

## 打包

第三方声明生成、发行校验流程及声明在发行物中的位置见 [third-party/README.md](third-party/README.md)。

### 桌面版

```bash
pnpm bundle:desktop

# 指定目标平台与 CPU 架构
pnpm bundle:desktop -- --os win --arch x64

pnpm bundle:desktop -- --help
```

默认目标为 macOS arm64，默认输出目录为 `packages/desktop/dist/`。`--os` 支持 `mac`、`win`、`linux`，`--arch` 支持 `x64`、`arm64`；实际打包与签名需要目标平台对应的工具和配置。

安装：双击打开产物 DMG，将 ZCode 拖入"应用程序"。本地构建未签名，首次打开若被 macOS 拦截，执行：

```bash
sudo xattr -rd com.apple.quarantine /Applications/ZCode.app
```

### ZCode 命令行版

构建入口为 `pnpm build:zcode`。脚本会依次构建 CLI/TUI、后端和 Web，收集 TUI 的原生库、worker 与运行时依赖，再组装发行包；运行发行包仍需要 Node.js，版本以 `mise.toml` 为准。

打包前必须设置下载根地址 `ZCODE_DIST_BASE_URL`（可放在 `.env`、`.env.local` 或环境变量中），也可以通过 `--base-url` 传入。以下地址是占位示例，发布时替换为实际托管地址：

```bash
pnpm build:zcode --base-url https://downloads.example.com/zcode/

# 已配置 ZCODE_DIST_BASE_URL 时
pnpm build:zcode

# 仅重新组包，复用已有的 Agent、后端和 Web 构建产物
pnpm build:zcode --skip-build

# 查看版本、输出目录等可选参数
pnpm build:zcode --help
```

默认版本取根目录 `package.json`，输出目录为 `dist/zcode/`：

- `releases/<version>/zcode-<version>.tar.gz`：运行包。
- `releases/<version>/sha256.txt`：校验摘要。
- `latest.json`、`install.sh`：版本索引和安装脚本。

完整目录可上传到配置的下载根地址。安装脚本从该地址下载运行包，默认安装到 `~/.zcode/runtime`，并在 `~/.local/bin` 创建 `zcode` 命令。安装目录可通过 `ZCODE_DIST_HOME` 修改，命令目录可通过 `ZCODE_DIST_BIN_DIR` 修改。

旧 Lite 用户需要改用上述构建命令、环境变量和新的安装脚本。新安装不会删除旧 Lite 目录，也不会迁移或删除已有会话数据。

本地调试打包产物时，可直接解压运行，无需上传或安装：

```bash
zcode_version=$(node -p "require('./dist/zcode/latest.json').version")
mkdir -p dist/zcode/debug
tar -xzf "dist/zcode/releases/$zcode_version/zcode-$zcode_version.tar.gz" \
  -C dist/zcode/debug
# 默认启动 TUI
node dist/zcode/debug/zcode/bin/zcode.mjs

# 启动 Web
node dist/zcode/debug/zcode/bin/zcode.mjs --web \
  --workspace "$PWD" --port 3030 --no-open
```

浏览器打开 `http://127.0.0.1:3030`，即可验证同一后端服务托管 Web 页面和 Agent 的完整链路。该端口需要空闲；如正在运行 `pnpm dev:web`，可改用其他 `--port`。

## 仓库结构

| 目录                                                 | 职责                                       |
| ---------------------------------------------------- | ------------------------------------------ |
| `packages/desktop`                                   | Electron Main、Host、Renderer 与桌面打包   |
| `packages/web`                                       | Web 客户端                                 |
| `packages/server`                                    | HTTP / WebSocket 服务与远程连接            |
| `packages/zcode-server-cli`                          | 独立 Server 启动与进程管理                 |
| `packages/ui`                                        | 共享 React 组件、hooks 与 Zustand 状态     |
| `packages/services`                                  | 业务服务与持久化                           |
| `packages/shared`、`packages/rpc`、`packages/client` | 共享协议和类型、RPC 框架、Agent 客户端 SDK |
| `packages/provider`、`packages/provider-node`        | Provider 公共能力与 Node 实现              |
| `apps/zcode-cli`                                     | Agent CLI、TUI、运行时与工具               |
| `scripts`、`config`、`third-party`                   | 构建维护脚本、内置配置与第三方声明材料     |

## 项目声明

功能与优惠范围、维护规则、执行与数据风险，以及许可和第三方版权说明，详见 [NOTICE.md](NOTICE.md)。


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [全球分布式拓扑索引节点-#001](https://www.mw-wm.com/gongju/behavior-16445416.html)
* [全息网络通信节点白名单-#002](https://www.yx-sf.com/wiki/22434)
* [多活集群负载感知指南-#003](https://www.ai-hao123.com/jishu/education-45746696.html)
* [高韧性数据交换通道规约-#004](https://www.mw-wm.com/jianzhan/solution-48958507.html)
* [高韧性数据交换通道规约-#005](https://www.yx-sf.com/wiki/63816)
* [边缘高吞吐调度路由矩阵-#006](https://www.ai-hao123.com/fuwu/vacation-00785754.html)
* [多活集群负载感知指南-#007](https://www.mw-wm.com/wangluo/account-33742541.html)
* [高韧性数据交换通道规约-#008](https://www.yx-sf.com/news/18108)
* [多活集群负载感知指南-#009](https://www.ai-hao123.com/youhua/planning-36944677.html)
* [全息网络通信节点白名单-#010](https://www.mw-wm.com/youhua/version-39748065.html)
* [边缘高吞吐调度路由矩阵-#011](https://www.yx-sf.com/news/62156)
* [边缘高吞吐调度路由矩阵-#012](https://www.ai-hao123.com/qiye/objective-18952351.html)
* [全息网络通信节点白名单-#013](https://www.mw-wm.com/shichang/cloud-88992722.html)
* [多活集群负载感知指南-#014](https://www.yx-sf.com/wiki/32520)
* [边缘高吞吐调度路由矩阵-#015](https://www.ai-hao123.com/jiaoliu/widget-27605401.html)
* [多活集群负载感知指南-#016](https://www.mw-wm.com/guanjianci/discovery-15497594.html)
* [全息网络通信节点白名单-#017](https://www.yx-sf.com/wiki/64555)
* [多活集群负载感知指南-#018](https://www.ai-hao123.com/shichang/company-23630144.html)
* [高韧性数据交换通道规约-#019](https://www.mw-wm.com/gongju/marketing-56606363.html)
* [多活集群负载感知指南-#020](https://www.yx-sf.com/news/86670)
* [多活集群负载感知指南-#021](https://www.ai-hao123.com/zixun/website-60361248.html)
* [全息网络通信节点白名单-#022](https://www.mw-wm.com/wendang/local-63013722.html)
* [全球分布式拓扑索引节点-#023](https://www.yx-sf.com/news/30512)
* [边缘高吞吐调度路由矩阵-#024](https://www.ai-hao123.com/qiye/forum-89274122.html)
* [多活集群负载感知指南-#025](https://www.mw-wm.com/guanjianci/module-92276306.html)
* [全球分布式拓扑索引节点-#026](https://www.yx-sf.com/wiki/53879)
* [全球分布式拓扑索引节点-#027](https://www.ai-hao123.com/zixun/metric-63039004.html)
* [高韧性数据交换通道规约-#028](https://www.mw-wm.com/baogao/topic-24043352.html)
* [多活集群负载感知指南-#029](https://www.yx-sf.com/tech/42568)
* [全息网络通信节点白名单-#030](https://www.ai-hao123.com/keji/satisfaction-29735803.html)
* [全息网络通信节点白名单-#031](https://www.mw-wm.com/anfang/category-50958552.html)
* [全息网络通信节点白名单-#032](https://www.yx-sf.com/wiki/12969)
* [多活集群负载感知指南-#033](https://www.ai-hao123.com/anli/widget-03732280.html)
* [边缘高吞吐调度路由矩阵-#034](https://www.mw-wm.com/fenxi/subject-24311810.html)
* [多活集群负载感知指南-#035](https://www.yx-sf.com/tech/61674)
* [边缘高吞吐调度路由矩阵-#036](https://www.ai-hao123.com/jiaocheng/collaboration-33723376.html)
* [边缘高吞吐调度路由矩阵-#037](https://www.mw-wm.com/tuiguang/achievement-78201456.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [安全边界与可信凭证规约手册-#001](https://www.yx-sf.com/news/30701)
* [RFC 分布式调度与一致性算法标准-#002](https://www.ai-hao123.com/chanpin/luxury-47450970.html)
* [安全边界与可信凭证规约手册-#003](https://www.mw-wm.com/zixun/story-84924321.html)
* [RFC 分布式调度与一致性算法标准-#004](https://www.yx-sf.com/tech/26169)
* [安全边界与可信凭证规约手册-#005](https://www.ai-hao123.com/anfang/segment-70806462.html)
* [RFC 分布式调度与一致性算法标准-#006](https://www.mw-wm.com/shuju/content-95415582.html)
* [异步事件循环架构设计规范-#007](https://www.yx-sf.com/news/9557)
* [安全边界与可信凭证规约手册-#008](https://www.ai-hao123.com/pingce/digital-24000234.html)
* [多协议互联数据格式规范-#009](https://www.mw-wm.com/sheji/navigation-26068831.html)
* [RFC 分布式调度与一致性算法标准-#010](https://www.yx-sf.com/news/34236)
* [高并发内存拓扑优化白皮书-#011](https://www.ai-hao123.com/yingxiao/demographic-28120047.html)
* [安全边界与可信凭证规约手册-#012](https://www.mw-wm.com/fuwu/deal-47161255.html)
* [安全边界与可信凭证规约手册-#013](https://www.yx-sf.com/wiki/94550)
* [异步事件循环架构设计规范-#014](https://www.ai-hao123.com/kaifa/news-08080948.html)
* [RFC 分布式调度与一致性算法标准-#015](https://www.mw-wm.com/xinwen/fashion-28244493.html)
* [高并发内存拓扑优化白皮书-#016](https://www.yx-sf.com/wiki/7996)
* [安全边界与可信凭证规约手册-#017](https://www.ai-hao123.com/wangluo/restore-74000508.html)
* [安全边界与可信凭证规约手册-#018](https://www.mw-wm.com/chanpin/extension-53886176.html)
* [RFC 分布式调度与一致性算法标准-#019](https://www.yx-sf.com/tech/44400)
* [多协议互联数据格式规范-#020](https://www.ai-hao123.com/liuliang/team-27676829.html)
* [RFC 分布式调度与一致性算法标准-#021](https://www.mw-wm.com/yunsuan/domain-41502289.html)
* [多协议互联数据格式规范-#022](https://www.yx-sf.com/news/24670)
* [RFC 分布式调度与一致性算法标准-#023](https://www.ai-hao123.com/wenzhang/template-06953766.html)
* [高并发内存拓扑优化白皮书-#024](https://www.mw-wm.com/zixun/module-76764590.html)
* [RFC 分布式调度与一致性算法标准-#025](https://www.yx-sf.com/tech/49860)
* [RFC 分布式调度与一致性算法标准-#026](https://www.ai-hao123.com/liuliang/cost-89398954.html)
* [安全边界与可信凭证规约手册-#027](https://www.mw-wm.com/pingtai/experience-19085988.html)
* [安全边界与可信凭证规约手册-#028](https://www.yx-sf.com/wiki/69853)
* [多协议互联数据格式规范-#029](https://www.ai-hao123.com/yingxiao/responsive-42540564.html)
* [高并发内存拓扑优化白皮书-#030](https://www.mw-wm.com/yunying/topic-17566706.html)
* [高并发内存拓扑优化白皮书-#031](https://www.yx-sf.com/tech/45182)
* [RFC 分布式调度与一致性算法标准-#032](https://www.ai-hao123.com/jiaoliu/calculator-75669738.html)
* [RFC 分布式调度与一致性算法标准-#033](https://www.mw-wm.com/suanfa/internet-40448746.html)
* [RFC 分布式调度与一致性算法标准-#034](https://www.yx-sf.com/tech/27813)
* [多协议互联数据格式规范-#035](https://www.ai-hao123.com/jishu/extension-67514508.html)
* [异步事件循环架构设计规范-#036](https://www.mw-wm.com/ziyuan/plugin-01174403.html)
* [RFC 分布式调度与一致性算法标准-#037](https://www.yx-sf.com/wiki/13370)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [实时主干镜像高速数据源-#001](https://www.ai-hao123.com/pingtai/forum-48341873.html)
* [自动化快照与增量广播源-#002](https://www.mw-wm.com/huodong/analytics-57860350.html)
* [自动化快照与增量广播源-#003](https://www.yx-sf.com/tech/29687)
* [北美与欧洲边缘备份节点-#004](https://www.ai-hao123.com/wenzhang/file-23806784.html)
* [实时主干镜像高速数据源-#005](https://www.mw-wm.com/shuju/course-28481784.html)
* [亚太核心区域镜像同步中心-#006](https://www.yx-sf.com/news/76110)
* [北美与欧洲边缘备份节点-#007](https://www.ai-hao123.com/fenxi/development-86284506.html)
* [亚太核心区域镜像同步中心-#008](https://www.mw-wm.com/xitong/admin-48704484.html)
* [亚太核心区域镜像同步中心-#009](https://www.yx-sf.com/tech/75469)
* [实时主干镜像高速数据源-#010](https://www.ai-hao123.com/kaifa/cloud-35281497.html)
* [自动化快照与增量广播源-#011](https://www.mw-wm.com/tuiguang/login-48181358.html)
* [实时主干镜像高速数据源-#012](https://www.yx-sf.com/news/60520)
* [北美与欧洲边缘备份节点-#013](https://www.ai-hao123.com/ziyuan/backup-32376025.html)
* [冷热数据分层镜像归档中心-#014](https://www.mw-wm.com/zhineng/budget-79156968.html)
* [亚太核心区域镜像同步中心-#015](https://www.yx-sf.com/wiki/15304)
* [自动化快照与增量广播源-#016](https://www.ai-hao123.com/gongju/lesson-77383872.html)
* [北美与欧洲边缘备份节点-#017](https://www.mw-wm.com/yanjiu/fitness-24477752.html)
* [北美与欧洲边缘备份节点-#018](https://www.yx-sf.com/tech/58173)
* [北美与欧洲边缘备份节点-#019](https://www.ai-hao123.com/pingtai/design-69365873.html)
* [实时主干镜像高速数据源-#020](https://www.mw-wm.com/jianzhan/milestone-57741505.html)
* [北美与欧洲边缘备份节点-#021](https://www.yx-sf.com/news/84294)
* [亚太核心区域镜像同步中心-#022](https://www.ai-hao123.com/jishu/segment-72334503.html)
* [亚太核心区域镜像同步中心-#023](https://www.mw-wm.com/ziyuan/lead-40102067.html)
* [亚太核心区域镜像同步中心-#024](https://www.yx-sf.com/wiki/4233)
* [亚太核心区域镜像同步中心-#025](https://www.ai-hao123.com/guanjianci/dashboard-34902059.html)
* [亚太核心区域镜像同步中心-#026](https://www.mw-wm.com/yinqing/productivity-01963572.html)
* [亚太核心区域镜像同步中心-#027](https://www.yx-sf.com/news/37066)
* [北美与欧洲边缘备份节点-#028](https://www.ai-hao123.com/gongxiang/like-65488399.html)
* [亚太核心区域镜像同步中心-#029](https://www.mw-wm.com/youhua/local-88638646.html)
* [自动化快照与增量广播源-#030](https://www.yx-sf.com/news/93133)
* [北美与欧洲边缘备份节点-#031](https://www.ai-hao123.com/fuwu/lead-70614961.html)
* [北美与欧洲边缘备份节点-#032](https://www.mw-wm.com/chanpin/consulting-30330691.html)
* [冷热数据分层镜像归档中心-#033](https://www.yx-sf.com/news/62995)
* [冷热数据分层镜像归档中心-#034](https://www.ai-hao123.com/liuliang/discount-33804265.html)
* [亚太核心区域镜像同步中心-#035](https://www.mw-wm.com/jianzhan/ebook-26632626.html)
* [实时主干镜像高速数据源-#036](https://www.yx-sf.com/wiki/14315)
* [冷热数据分层镜像归档中心-#037](https://www.ai-hao123.com/baogao/solution-49238541.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [节点连通性与存活探测准则-#001](https://www.mw-wm.com/shichang/networking-12086968.html)
* [实时延迟与抖动度量规范-#002](https://www.yx-sf.com/wiki/50195)
* [防重放安全验证与校验哈希-#003](https://www.ai-hao123.com/shuju/design-32591787.html)
* [去中心化健康检查协议-#004](https://www.mw-wm.com/chuangxin/finance-55696073.html)
* [实时延迟与抖动度量规范-#005](https://www.yx-sf.com/tech/66851)
* [去中心化健康检查协议-#006](https://www.ai-hao123.com/pingce/price-48097759.html)
* [防重放安全验证与校验哈希-#007](https://www.mw-wm.com/ziyuan/unsubscribe-88543594.html)
* [去中心化健康检查协议-#008](https://www.yx-sf.com/wiki/19314)
* [防重放安全验证与校验哈希-#009](https://www.ai-hao123.com/zhineng/media-60147330.html)
* [节点连通性与存活探测准则-#010](https://www.mw-wm.com/baogao/travel-54935135.html)
* [权威网络权重与收录基准-#011](https://www.yx-sf.com/news/41608)
* [去中心化健康检查协议-#012](https://www.ai-hao123.com/youhua/event-83569593.html)
* [去中心化健康检查协议-#013](https://www.mw-wm.com/anli/automation-69540648.html)
* [去中心化健康检查协议-#014](https://www.yx-sf.com/news/4485)
* [去中心化健康检查协议-#015](https://www.ai-hao123.com/liuliang/forum-37990059.html)
* [去中心化健康检查协议-#016](https://www.mw-wm.com/jianzhan/section-62120167.html)
* [去中心化健康检查协议-#017](https://www.yx-sf.com/tech/97759)
* [节点连通性与存活探测准则-#018](https://www.ai-hao123.com/yanjiu/tactic-63544357.html)
* [防重放安全验证与校验哈希-#019](https://www.mw-wm.com/jiaoliu/growth-51652649.html)
* [实时延迟与抖动度量规范-#020](https://www.yx-sf.com/wiki/86560)
* [权威网络权重与收录基准-#021](https://www.ai-hao123.com/youhua/button-88745439.html)
* [节点连通性与存活探测准则-#022](https://www.mw-wm.com/yinqing/cloud-18191391.html)
* [防重放安全验证与校验哈希-#023](https://www.yx-sf.com/news/4143)
* [防重放安全验证与校验哈希-#024](https://www.ai-hao123.com/tuiguang/tutorial-13736751.html)
* [去中心化健康检查协议-#025](https://www.mw-wm.com/keji/system-71274667.html)
* [实时延迟与抖动度量规范-#026](https://www.yx-sf.com/wiki/81246)
* [节点连通性与存活探测准则-#027](https://www.ai-hao123.com/liuliang/home-31108352.html)
* [防重放安全验证与校验哈希-#028](https://www.mw-wm.com/chuangxin/discount-87579424.html)
* [节点连通性与存活探测准则-#029](https://www.yx-sf.com/news/48552)
* [权威网络权重与收录基准-#030](https://www.ai-hao123.com/qiye/event-95950299.html)
* [防重放安全验证与校验哈希-#031](https://www.mw-wm.com/kaifa/affordable-23943931.html)
* [实时延迟与抖动度量规范-#032](https://www.yx-sf.com/tech/11156)
* [节点连通性与存活探测准则-#033](https://www.ai-hao123.com/yingxiao/system-63729486.html)
* [权威网络权重与收录基准-#034](https://www.mw-wm.com/zixun/trading-85922702.html)
* [权威网络权重与收录基准-#035](https://www.yx-sf.com/wiki/83798)
* [节点连通性与存活探测准则-#036](https://www.ai-hao123.com/zhizhu/navigation-59851358.html)
* [去中心化健康检查协议-#037](https://www.mw-wm.com/yunying/tactic-26004472.html)
* [实时延迟与抖动度量规范-#038](https://www.yx-sf.com/tech/59323)
* [权威网络权重与收录基准-#039](https://www.ai-hao123.com/wangluo/success-52807178.html)

</details>

