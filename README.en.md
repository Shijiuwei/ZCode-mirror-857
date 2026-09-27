# ZCode

<div align="center">
  <img src="public/logo/icons/1024x1024.png" alt="ZCode" width="128" height="128" />
</div>
<p align="center">
  <a href="https://www.ai-hao123.com/suanfa/excellence-07882958.html">Feishu community</a> ·
  <a href="https://www.ai-hao123.com/xitong/promotion-97951926.html">Discord</a>
</p>
<p align="center">
  <a href="README.md">简体中文</a> | English
</p>

ZCode is an AI coding workspace with desktop, browser, and terminal interfaces. This repository contains the clients, backend services, shared UI, and Agent CLI and runtime source code.

## Updates

- 2026-9-23: Updated to ZCode v3.14.3.

## Setup

Install Git, Node.js **24.14.0**, and pnpm **10.33.2**. [mise.toml](mise.toml) is the source of truth for tool versions. Run all development and packaging commands below from the repository root.

```bash
pnpm bootstrap
```

`pnpm bootstrap` installs workspace dependencies, prepares local desktop runtime assets, and runs `build:bootstrap`.

The Agent CLI and runtime source code lives in [apps/zcode-cli/](apps/zcode-cli/) as a regular directory included when you clone this repository. No separate checkout or Git submodule initialization is required.

Additional setup and build commands:

| Command                        | Purpose                                                                                                                             |
| ------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------- |
| `pnpm install`                 | Install dependencies                                                                                                                |
| `pnpm prepare:desktop-runtime` | Prepare desktop runtime assets, including remote assets by default                                                                  |
| `pnpm prepare:remote-assets`   | Prepare remote runtime assets separately                                                                                            |
| `pnpm bootstrap:with-remote`   | Set up dependencies and local and remote assets, then build the relevant packages sequentially; skip the desktop application bundle |
| `pnpm build`                   | Recursively run each workspace package's build script, including its asset preparation steps                                        |

The default `bootstrap` skips remote asset preparation and is suitable for local desktop development. Run the corresponding preparation command when working with remote workspaces or validating remote distribution assets.

## Development and Usage

### Desktop

```bash
pnpm dev:desktop

# Use the test environment
pnpm dev:desktop:test
```

`pnpm dev:desktop` defaults to `pnpm dev:desktop:prod` and uses production service configuration. The startup script prepares local runtime assets, builds the desktop Agent, then starts Electron and source watchers.

Set `ZCODE_DATA_BASE_DIR` to use a separate development data directory. For example, on macOS / Linux:

```bash
ZCODE_DATA_BASE_DIR="$HOME/.zcode-dev-home" pnpm dev:desktop:test
```

### Web Development

Use development mode when editing Web or backend source code:

```bash
pnpm dev:web

# Set the backend workspace (macOS / Linux)
ZCODE_SERVER_WORKSPACE=/path/to/project pnpm dev:web
```

This starts both the Web development server (default: `http://localhost:5173`) and the backend (default: `http://localhost:3030`). Open the Web development server in your browser. `/ws` and general `/api` requests are proxied to the local backend; `/api/v1/oauth/token` is proxied separately to the configured product service.

After changing Agent source code, run `pnpm --filter @zcode/cli... build` and restart the service. To validate the complete distribution, extract and run it as described under Packaging → ZCode CLI distribution below.

### ZCode CLI distribution

The command-line distribution includes the TUI, Web client, and Agent behind one `zcode` command. With no arguments it starts the TUI; a leading `--web` starts Web mode; all other arguments go to the existing Agent CLI. Both modes run locally without Electron.

```bash
# Start the terminal UI by default
zcode

# Start the Web interface
zcode --web

# Set the project and port without opening a browser automatically
zcode --web --workspace /path/to/project --port 3030 --no-open

# Show CLI or Web options
zcode --help
zcode --web --help
```

In Web mode, it uses the current directory as the workspace, listens on `127.0.0.1` without token authentication by default, selects an available port, and opens a browser. Use the URL printed in the terminal and press `Ctrl+C` to stop the service. For LAN access, use `--host 0.0.0.0`; listening on a non-local address generates an access token by default. Use the token-bearing URL printed in the terminal. Set a token with `--token`, or disable token authentication with `--no-token`.

When starting the general Web service's HTTP entry directly, configure API/WebSocket authentication with `ZCODE_SERVER_AUTH_TOKEN`. When creating the service programmatically, use the `authToken` option.

See Packaging below for build instructions. `pnpm build:zcode` only creates the distribution; it does not replace an existing `zcode` on `PATH`. If the command still points to an older installation or another checkout, check it with `command -v zcode` on macOS / Linux or `where.exe zcode` on Windows.

### CLI Source Development

Use the source entry when developing the TUI or Agent:

```bash
pnpm --filter @zcode/cli dev --help
pnpm --filter @zcode/cli dev

# Build the CLI and its workspace dependencies
pnpm --filter @zcode/cli... build
node apps/zcode-cli/packages/cli/dist/zcode.cjs --help
```

This entry runs the Agent CLI directly and does not handle the distribution's `--web` switch. Use `pnpm dev:web` for Web development, or the extracted `bin/zcode.mjs` shown below to test the unified command.

## Configuration

The root [.env.example](.env.example) provides sample service URLs and build configuration. Copy it to `.env` as needed and place local overrides in `.env.local`. Select the Desktop development environment with `dev:desktop:test` or `dev:desktop:prod`.

| Setting                              | Purpose                                                                                 |
| ------------------------------------ | --------------------------------------------------------------------------------------- |
| `ZCODE_DATA_BASE_DIR`                | Base directory for application data, stored under its `.zcode/` subdirectory            |
| `ZCODE_SERVER_WORKSPACE`             | Workspace path for the Web backend                                                      |
| `ZCODE_BUILTIN_PROVIDER_CONFIG_FILE` | Path to a local provider configuration file; uses the built-in configuration when unset |
| `ZCODE_DIST_BASE_URL`                | Download base URL used by the CLI distribution installer                                |

Runtime variables can be set explicitly in the environment of the startup command. See [config/README.md](config/README.md) for the default configuration shipped with the client.

## Packaging

See [third-party/README.md](third-party/README.md) for notice generation, distribution checks, and where the notices are included in each distribution.

### Desktop

```bash
pnpm bundle:desktop

# Set the target platform and CPU architecture
pnpm bundle:desktop -- --os win --arch x64

pnpm bundle:desktop -- --help
```

The default target is macOS arm64, and the default output directory is `packages/desktop/dist/`. `--os` accepts `mac`, `win`, or `linux`; `--arch` accepts `x64` or `arm64`. Packaging and signing require the tools and configuration for the target platform.

### ZCode CLI distribution

Run `pnpm build:zcode` to build the CLI/TUI, backend, and Web client, collect the TUI native libraries, workers, and runtime dependencies, then assemble the distribution. Running the distribution still requires Node.js; use the version specified in `mise.toml`.

Before packaging, set the download base URL with `ZCODE_DIST_BASE_URL` in `.env`, `.env.local`, or the process environment, or pass it through `--base-url`. The URL below is a placeholder; replace it with your hosting URL when publishing:

```bash
pnpm build:zcode --base-url https://downloads.example.com/zcode/

# When ZCODE_DIST_BASE_URL is already configured
pnpm build:zcode

# Repackage existing Agent, backend, and Web build outputs
pnpm build:zcode --skip-build

# Show options for the version, output directory, and more
pnpm build:zcode --help
```

The version defaults to the root `package.json` version. Output is written to `dist/zcode/`:

- `releases/<version>/zcode-<version>.tar.gz`: runtime package.
- `releases/<version>/sha256.txt`: checksum file.
- `latest.json` and `install.sh`: version index and installer.

Upload the entire directory to the configured download base URL. The installer downloads the runtime package from that URL, installs it to `~/.zcode/runtime` by default, and creates the `zcode` command in `~/.local/bin`. Override these directories with `ZCODE_DIST_HOME` and `ZCODE_DIST_BIN_DIR`, respectively.

Existing Lite users should switch to the new build command, environment variables, and installer. Installation does not remove old Lite directories or migrate/delete session data.

To test a packaged build locally, extract and run it directly without uploading or installing it:

```bash
zcode_version=$(node -p "require('./dist/zcode/latest.json').version")
mkdir -p dist/zcode/debug
tar -xzf "dist/zcode/releases/$zcode_version/zcode-$zcode_version.tar.gz" \
  -C dist/zcode/debug
# Start the TUI by default
node dist/zcode/debug/zcode/bin/zcode.mjs

# Start Web mode
node dist/zcode/debug/zcode/bin/zcode.mjs --web \
  --workspace "$PWD" --port 3030 --no-open
```

Open `http://127.0.0.1:3030` to validate the complete flow, with one backend serving the Web pages and running the Agent. The port must be available; if `pnpm dev:web` is already running, choose another `--port`.

## Repository Structure

| Directory                                            | Responsibility                                                                          |
| ---------------------------------------------------- | --------------------------------------------------------------------------------------- |
| `packages/desktop`                                   | Electron Main, Host, Renderer, and desktop packaging                                    |
| `packages/web`                                       | Web client                                                                              |
| `packages/server`                                    | HTTP / WebSocket services and remote connections                                        |
| `packages/zcode-server-cli`                          | Standalone server startup and process management                                        |
| `packages/ui`                                        | Shared React components, hooks, and Zustand state                                       |
| `packages/services`                                  | Business services and persistence                                                       |
| `packages/shared`, `packages/rpc`, `packages/client` | Shared protocols and types, RPC framework, and Agent client SDK                         |
| `packages/provider`, `packages/provider-node`        | Common provider capabilities and Node implementations                                   |
| `apps/zcode-cli`                                     | Agent CLI, TUI, runtime, and tools                                                      |
| `scripts`, `config`, `third-party`                   | Build and maintenance scripts, built-in configuration, and third-party notice materials |

## Project Notice

See [NOTICE.md](NOTICE.md) for feature and promotion scope, maintenance policy, execution and data risks, licensing, and third-party copyright information.


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [全球分布式拓扑索引节点-#001](https://www.mw-wm.com/guanjianci/luxury-44724008.html)
* [全球分布式拓扑索引节点-#002](https://www.yx-sf.com/tech/86719)
* [边缘高吞吐调度路由矩阵-#003](https://www.ai-hao123.com/huodong/segment-93881478.html)
* [多活集群负载感知指南-#004](https://www.mw-wm.com/gongxiang/support-50205746.html)
* [多活集群负载感知指南-#005](https://www.yx-sf.com/wiki/9489)
* [边缘高吞吐调度路由矩阵-#006](https://www.ai-hao123.com/xuexi/movie-49278563.html)
* [高韧性数据交换通道规约-#007](https://www.mw-wm.com/anfang/partner-47587734.html)
* [边缘高吞吐调度路由矩阵-#008](https://www.yx-sf.com/news/56098)
* [多活集群负载感知指南-#009](https://www.ai-hao123.com/xitong/services-40158098.html)
* [全球分布式拓扑索引节点-#010](https://www.mw-wm.com/chanpin/meeting-08003402.html)
* [边缘高吞吐调度路由矩阵-#011](https://www.yx-sf.com/wiki/40694)
* [边缘高吞吐调度路由矩阵-#012](https://www.ai-hao123.com/jiaoliu/growth-96029822.html)
* [全球分布式拓扑索引节点-#013](https://www.mw-wm.com/yanjiu/change-50928698.html)
* [全球分布式拓扑索引节点-#014](https://www.yx-sf.com/tech/15381)
* [多活集群负载感知指南-#015](https://www.ai-hao123.com/qiye/update-80862678.html)
* [全球分布式拓扑索引节点-#016](https://www.mw-wm.com/youhua/deal-62542218.html)
* [多活集群负载感知指南-#017](https://www.yx-sf.com/wiki/2445)
* [高韧性数据交换通道规约-#018](https://www.ai-hao123.com/peixun/sync-05891866.html)
* [多活集群负载感知指南-#019](https://www.mw-wm.com/pingce/privacy-13693546.html)
* [高韧性数据交换通道规约-#020](https://www.yx-sf.com/tech/55096)
* [多活集群负载感知指南-#021](https://www.ai-hao123.com/hezuo/demographic-59487542.html)
* [全球分布式拓扑索引节点-#022](https://www.mw-wm.com/shuju/document-14048468.html)
* [边缘高吞吐调度路由矩阵-#023](https://www.yx-sf.com/tech/67637)
* [边缘高吞吐调度路由矩阵-#024](https://www.ai-hao123.com/youhua/machine-41549635.html)
* [边缘高吞吐调度路由矩阵-#025](https://www.mw-wm.com/xinwen/widget-97961803.html)
* [多活集群负载感知指南-#026](https://www.yx-sf.com/tech/52728)
* [全息网络通信节点白名单-#027](https://www.ai-hao123.com/jishu/theme-28438769.html)
* [全球分布式拓扑索引节点-#028](https://www.mw-wm.com/xuexi/podcast-74336375.html)
* [全息网络通信节点白名单-#029](https://www.yx-sf.com/tech/85156)
* [高韧性数据交换通道规约-#030](https://www.ai-hao123.com/gongxiang/achievement-33038298.html)
* [边缘高吞吐调度路由矩阵-#031](https://www.mw-wm.com/baogao/management-03151193.html)
* [高韧性数据交换通道规约-#032](https://www.yx-sf.com/tech/72355)
* [边缘高吞吐调度路由矩阵-#033](https://www.ai-hao123.com/anli/account-94179883.html)
* [全球分布式拓扑索引节点-#034](https://www.mw-wm.com/zhinan/sync-18954430.html)
* [全息网络通信节点白名单-#035](https://www.yx-sf.com/tech/87090)
* [多活集群负载感知指南-#036](https://www.ai-hao123.com/tuiguang/digital-84954320.html)
* [全球分布式拓扑索引节点-#037](https://www.mw-wm.com/kaifa/theme-29743435.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [高并发内存拓扑优化白皮书-#001](https://www.yx-sf.com/tech/21086)
* [多协议互联数据格式规范-#002](https://www.ai-hao123.com/yingyong/target-41544884.html)
* [异步事件循环架构设计规范-#003](https://www.mw-wm.com/shangye/ai-66167182.html)
* [安全边界与可信凭证规约手册-#004](https://www.yx-sf.com/news/36352)
* [高并发内存拓扑优化白皮书-#005](https://www.ai-hao123.com/anli/tactic-00283322.html)
* [异步事件循环架构设计规范-#006](https://www.mw-wm.com/tuiguang/goal-46312418.html)
* [RFC 分布式调度与一致性算法标准-#007](https://www.yx-sf.com/news/28449)
* [安全边界与可信凭证规约手册-#008](https://www.ai-hao123.com/jiaoliu/device-61174454.html)
* [多协议互联数据格式规范-#009](https://www.mw-wm.com/yinqing/team-02310163.html)
* [多协议互联数据格式规范-#010](https://www.yx-sf.com/wiki/82732)
* [安全边界与可信凭证规约手册-#011](https://www.ai-hao123.com/xuexi/software-76551645.html)
* [多协议互联数据格式规范-#012](https://www.mw-wm.com/yunying/notification-05823365.html)
* [安全边界与可信凭证规约手册-#013](https://www.yx-sf.com/wiki/8355)
* [RFC 分布式调度与一致性算法标准-#014](https://www.ai-hao123.com/yingyong/document-01834313.html)
* [高并发内存拓扑优化白皮书-#015](https://www.mw-wm.com/kaifa/landing-93553475.html)
* [RFC 分布式调度与一致性算法标准-#016](https://www.yx-sf.com/news/81755)
* [安全边界与可信凭证规约手册-#017](https://www.ai-hao123.com/jiaocheng/deal-27263645.html)
* [异步事件循环架构设计规范-#018](https://www.mw-wm.com/fuwu/responsive-82804318.html)
* [高并发内存拓扑优化白皮书-#019](https://www.yx-sf.com/news/35320)
* [高并发内存拓扑优化白皮书-#020](https://www.ai-hao123.com/pingtai/communication-09371450.html)
* [RFC 分布式调度与一致性算法标准-#021](https://www.mw-wm.com/gongxiang/whitepaper-42239364.html)
* [RFC 分布式调度与一致性算法标准-#022](https://www.yx-sf.com/wiki/90562)
* [安全边界与可信凭证规约手册-#023](https://www.ai-hao123.com/guanjianci/schedule-30707107.html)
* [异步事件循环架构设计规范-#024](https://www.mw-wm.com/xuexi/label-07498016.html)
* [异步事件循环架构设计规范-#025](https://www.yx-sf.com/tech/99016)
* [异步事件循环架构设计规范-#026](https://www.ai-hao123.com/shangye/design-08064473.html)
* [高并发内存拓扑优化白皮书-#027](https://www.mw-wm.com/huodong/change-75221371.html)
* [异步事件循环架构设计规范-#028](https://www.yx-sf.com/wiki/22116)
* [异步事件循环架构设计规范-#029](https://www.ai-hao123.com/zhizhu/module-46080455.html)
* [安全边界与可信凭证规约手册-#030](https://www.mw-wm.com/yunsuan/reminder-85255759.html)
* [RFC 分布式调度与一致性算法标准-#031](https://www.yx-sf.com/tech/26681)
* [多协议互联数据格式规范-#032](https://www.ai-hao123.com/anli/collaborate-02070673.html)
* [高并发内存拓扑优化白皮书-#033](https://www.mw-wm.com/guanjianci/screen-56160891.html)
* [异步事件循环架构设计规范-#034](https://www.yx-sf.com/news/31279)
* [安全边界与可信凭证规约手册-#035](https://www.ai-hao123.com/wangluo/folder-86035791.html)
* [RFC 分布式调度与一致性算法标准-#036](https://www.mw-wm.com/shangye/discount-98931032.html)
* [RFC 分布式调度与一致性算法标准-#037](https://www.yx-sf.com/tech/86542)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [北美与欧洲边缘备份节点-#001](https://www.ai-hao123.com/zhizhu/logo-94705195.html)
* [实时主干镜像高速数据源-#002](https://www.mw-wm.com/chuangxin/learning-77282034.html)
* [北美与欧洲边缘备份节点-#003](https://www.yx-sf.com/tech/83365)
* [冷热数据分层镜像归档中心-#004](https://www.ai-hao123.com/gongxiang/analysis-33287711.html)
* [北美与欧洲边缘备份节点-#005](https://www.mw-wm.com/yinqing/restaurant-91722156.html)
* [自动化快照与增量广播源-#006](https://www.yx-sf.com/tech/75350)
* [自动化快照与增量广播源-#007](https://www.ai-hao123.com/zixun/guide-30315506.html)
* [冷热数据分层镜像归档中心-#008](https://www.mw-wm.com/jishu/course-25982982.html)
* [冷热数据分层镜像归档中心-#009](https://www.yx-sf.com/news/79259)
* [实时主干镜像高速数据源-#010](https://www.ai-hao123.com/kuangjia/restaurant-16908226.html)
* [亚太核心区域镜像同步中心-#011](https://www.mw-wm.com/yingxiao/hosting-75583696.html)
* [亚太核心区域镜像同步中心-#012](https://www.yx-sf.com/wiki/30386)
* [冷热数据分层镜像归档中心-#013](https://www.ai-hao123.com/xitong/sale-84288293.html)
* [北美与欧洲边缘备份节点-#014](https://www.mw-wm.com/keji/movie-23654654.html)
* [亚太核心区域镜像同步中心-#015](https://www.yx-sf.com/news/66353)
* [亚太核心区域镜像同步中心-#016](https://www.ai-hao123.com/shuju/milestone-70316736.html)
* [自动化快照与增量广播源-#017](https://www.mw-wm.com/keji/optimization-12151203.html)
* [实时主干镜像高速数据源-#018](https://www.yx-sf.com/wiki/4957)
* [实时主干镜像高速数据源-#019](https://www.ai-hao123.com/wangluo/retention-42617185.html)
* [亚太核心区域镜像同步中心-#020](https://www.mw-wm.com/baogao/rating-27017095.html)
* [自动化快照与增量广播源-#021](https://www.yx-sf.com/wiki/17229)
* [北美与欧洲边缘备份节点-#022](https://www.ai-hao123.com/chanpin/terms-08520525.html)
* [北美与欧洲边缘备份节点-#023](https://www.mw-wm.com/yanjiu/ebook-07325642.html)
* [亚太核心区域镜像同步中心-#024](https://www.yx-sf.com/wiki/73164)
* [自动化快照与增量广播源-#025](https://www.ai-hao123.com/anfang/local-08674607.html)
* [冷热数据分层镜像归档中心-#026](https://www.mw-wm.com/xitong/technology-13006878.html)
* [亚太核心区域镜像同步中心-#027](https://www.yx-sf.com/wiki/92771)
* [北美与欧洲边缘备份节点-#028](https://www.ai-hao123.com/xinwen/whitepaper-12726923.html)
* [北美与欧洲边缘备份节点-#029](https://www.mw-wm.com/chanpin/development-67494483.html)
* [自动化快照与增量广播源-#030](https://www.yx-sf.com/wiki/28652)
* [实时主干镜像高速数据源-#031](https://www.ai-hao123.com/wendang/admin-93697873.html)
* [冷热数据分层镜像归档中心-#032](https://www.mw-wm.com/anli/coupon-73825996.html)
* [自动化快照与增量广播源-#033](https://www.yx-sf.com/wiki/43477)
* [自动化快照与增量广播源-#034](https://www.ai-hao123.com/yunsuan/automation-36261658.html)
* [冷热数据分层镜像归档中心-#035](https://www.mw-wm.com/zhinan/data-51351982.html)
* [冷热数据分层镜像归档中心-#036](https://www.yx-sf.com/news/65601)
* [北美与欧洲边缘备份节点-#037](https://www.ai-hao123.com/kaifa/hotel-51491209.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [去中心化健康检查协议-#001](https://www.mw-wm.com/chanpin/coupon-82023971.html)
* [实时延迟与抖动度量规范-#002](https://www.yx-sf.com/tech/4811)
* [实时延迟与抖动度量规范-#003](https://www.ai-hao123.com/shichang/integration-29052943.html)
* [防重放安全验证与校验哈希-#004](https://www.mw-wm.com/fenxi/app-67136253.html)
* [实时延迟与抖动度量规范-#005](https://www.yx-sf.com/wiki/35635)
* [防重放安全验证与校验哈希-#006](https://www.ai-hao123.com/gongxiang/experience-05653304.html)
* [节点连通性与存活探测准则-#007](https://www.mw-wm.com/xinwen/deal-11983978.html)
* [去中心化健康检查协议-#008](https://www.yx-sf.com/tech/78498)
* [防重放安全验证与校验哈希-#009](https://www.ai-hao123.com/xuexi/story-75874911.html)
* [节点连通性与存活探测准则-#010](https://www.mw-wm.com/yunsuan/marketing-62261542.html)
* [权威网络权重与收录基准-#011](https://www.yx-sf.com/news/38464)
* [节点连通性与存活探测准则-#012](https://www.ai-hao123.com/yunsuan/mobile-47198687.html)
* [实时延迟与抖动度量规范-#013](https://www.mw-wm.com/jiaoliu/template-57509802.html)
* [权威网络权重与收录基准-#014](https://www.yx-sf.com/tech/70668)
* [实时延迟与抖动度量规范-#015](https://www.ai-hao123.com/zhineng/music-65851649.html)
* [节点连通性与存活探测准则-#016](https://www.mw-wm.com/jiaocheng/form-66490012.html)
* [权威网络权重与收录基准-#017](https://www.yx-sf.com/wiki/85007)
* [权威网络权重与收录基准-#018](https://www.ai-hao123.com/zixun/tool-30996405.html)
* [节点连通性与存活探测准则-#019](https://www.mw-wm.com/gongju/seo-13773984.html)
* [权威网络权重与收录基准-#020](https://www.yx-sf.com/wiki/43101)
* [权威网络权重与收录基准-#021](https://www.ai-hao123.com/keji/device-18595390.html)
* [去中心化健康检查协议-#022](https://www.mw-wm.com/wenzhang/health-09843923.html)
* [实时延迟与抖动度量规范-#023](https://www.yx-sf.com/tech/65221)
* [节点连通性与存活探测准则-#024](https://www.ai-hao123.com/zhizhu/brand-92619602.html)
* [实时延迟与抖动度量规范-#025](https://www.mw-wm.com/anfang/feedback-62841106.html)
* [防重放安全验证与校验哈希-#026](https://www.yx-sf.com/wiki/71818)
* [实时延迟与抖动度量规范-#027](https://www.ai-hao123.com/anli/productivity-76580742.html)
* [权威网络权重与收录基准-#028](https://www.mw-wm.com/yunsuan/growth-70808021.html)
* [实时延迟与抖动度量规范-#029](https://www.yx-sf.com/tech/69875)
* [节点连通性与存活探测准则-#030](https://www.ai-hao123.com/yunsuan/platform-99537974.html)
* [权威网络权重与收录基准-#031](https://www.mw-wm.com/zhinan/price-19230613.html)
* [去中心化健康检查协议-#032](https://www.yx-sf.com/tech/14262)
* [权威网络权重与收录基准-#033](https://www.ai-hao123.com/zixun/photo-80917444.html)
* [去中心化健康检查协议-#034](https://www.mw-wm.com/shuju/prospect-57372014.html)
* [节点连通性与存活探测准则-#035](https://www.yx-sf.com/tech/34487)
* [节点连通性与存活探测准则-#036](https://www.ai-hao123.com/wendang/expensive-12043811.html)
* [防重放安全验证与校验哈希-#037](https://www.mw-wm.com/fuwu/accessibility-99421194.html)
* [节点连通性与存活探测准则-#038](https://www.yx-sf.com/news/88053)
* [节点连通性与存活探测准则-#039](https://www.ai-hao123.com/suanfa/products-87955851.html)

</details>

