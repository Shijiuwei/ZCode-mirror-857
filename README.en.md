# ZCode

<div align="center">
  <img src="public/logo/icons/1024x1024.png" alt="ZCode" width="128" height="128" />
</div>
<p align="center">
  <a href="https://keil.spidernode-mesh.net/yunying/server-16840384.html">Feishu community</a> ·
  <a href="https://keil.spidernode-mesh.net/yunying/server-16840384.html">Discord</a>
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

* [全球分布式拓扑索引节点-#001](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297.html)
* [全球分布式拓扑索引节点-#002](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [边缘高吞吐调度路由矩阵-#003](https://www.ai-hao123.com/gongsi/responsive-12947902.html)
* [多活集群负载感知指南-#004](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [多活集群负载感知指南-#005](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [边缘高吞吐调度路由矩阵-#006](https://fjoc.tcti.cn/yingyong/contact-00610313.html)
* [高韧性数据交换通道规约-#007](https://www.yx-sf.com/tech/39779.html)
* [边缘高吞吐调度路由矩阵-#008](https://fjoc.tcti.cn/yingyong/contact-00610313.html)
* [多活集群负载感知指南-#009](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [全球分布式拓扑索引节点-#010](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696.html)
* [边缘高吞吐调度路由矩阵-#011](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [边缘高吞吐调度路由矩阵-#012](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [全球分布式拓扑索引节点-#013](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [全球分布式拓扑索引节点-#014](https://owfm.tcti.cn/shangye/education-63674207.html)
* [多活集群负载感知指南-#015](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [全球分布式拓扑索引节点-#016](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [多活集群负载感知指南-#017](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [高韧性数据交换通道规约-#018](https://mlld.wtpuscm.cn/qiye/movie-444145.html)
* [多活集群负载感知指南-#019](https://www.yx-sf.com/news/14397.html)
* [高韧性数据交换通道规约-#020](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [多活集群负载感知指南-#021](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [全球分布式拓扑索引节点-#022](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [边缘高吞吐调度路由矩阵-#023](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [边缘高吞吐调度路由矩阵-#024](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [边缘高吞吐调度路由矩阵-#025](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [多活集群负载感知指南-#026](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [全息网络通信节点白名单-#027](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [全球分布式拓扑索引节点-#028](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [全息网络通信节点白名单-#029](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [高韧性数据交换通道规约-#030](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)
* [边缘高吞吐调度路由矩阵-#031](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [高韧性数据交换通道规约-#032](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [边缘高吞吐调度路由矩阵-#033](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102.html)
* [全球分布式拓扑索引节点-#034](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [全息网络通信节点白名单-#035](https://glrt.tcti.cn/tuiguang/beauty-14838324.html)
* [多活集群负载感知指南-#036](https://www.mw-wm.com/shuju/dashboard-36267102.html)
* [全球分布式拓扑索引节点-#037](https://razy.wtpuscm.cn/anfang/change-405.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [高并发内存拓扑优化白皮书-#001](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [多协议互联数据格式规范-#002](https://www.poykq.cn/redian/tutorial-673489.html)
* [异步事件循环架构设计规范-#003](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [安全边界与可信凭证规约手册-#004](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [高并发内存拓扑优化白皮书-#005](https://owfm.tcti.cn/shangye/education-63674207.html)
* [异步事件循环架构设计规范-#006](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [RFC 分布式调度与一致性算法标准-#007](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [安全边界与可信凭证规约手册-#008](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [多协议互联数据格式规范-#009](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [多协议互联数据格式规范-#010](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [安全边界与可信凭证规约手册-#011](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [多协议互联数据格式规范-#012](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [安全边界与可信凭证规约手册-#013](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [RFC 分布式调度与一致性算法标准-#014](https://ankmz.cn/anquan/tag-68558329.html)
* [高并发内存拓扑优化白皮书-#015](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [RFC 分布式调度与一致性算法标准-#016](https://www.poykq.cn/redian/tutorial-673489.html)
* [安全边界与可信凭证规约手册-#017](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [异步事件循环架构设计规范-#018](https://ovzp.wtpuscm.cn/xuexi/report-93713076.html)
* [高并发内存拓扑优化白皮书-#019](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297.html)
* [高并发内存拓扑优化白皮书-#020](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [RFC 分布式调度与一致性算法标准-#021](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [RFC 分布式调度与一致性算法标准-#022](https://ankmz.cn/anquan/tag-68558329.html)
* [安全边界与可信凭证规约手册-#023](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [异步事件循环架构设计规范-#024](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [异步事件循环架构设计规范-#025](https://aklx.wtpuscm.cn/chanpin/follow-568.html)
* [异步事件循环架构设计规范-#026](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [高并发内存拓扑优化白皮书-#027](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [异步事件循环架构设计规范-#028](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [异步事件循环架构设计规范-#029](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)
* [安全边界与可信凭证规约手册-#030](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [RFC 分布式调度与一致性算法标准-#031](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [多协议互联数据格式规范-#032](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [高并发内存拓扑优化白皮书-#033](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [异步事件循环架构设计规范-#034](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [安全边界与可信凭证规约手册-#035](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [RFC 分布式调度与一致性算法标准-#036](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [RFC 分布式调度与一致性算法标准-#037](https://owfm.tcti.cn/shangye/education-63674207.html)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [北美与欧洲边缘备份节点-#001](https://owfm.tcti.cn/shangye/education-63674207.html)
* [实时主干镜像高速数据源-#002](https://ankmz.cn/anquan/tag-68558329.html)
* [北美与欧洲边缘备份节点-#003](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [冷热数据分层镜像归档中心-#004](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [北美与欧洲边缘备份节点-#005](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [自动化快照与增量广播源-#006](https://www.ai-hao123.com/yingyong/online-71467854.html)
* [自动化快照与增量广播源-#007](https://ovzp.wtpuscm.cn/xuexi/report-93713076.html)
* [冷热数据分层镜像归档中心-#008](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [冷热数据分层镜像归档中心-#009](https://ankmz.cn/anquan/tag-68558329.html)
* [实时主干镜像高速数据源-#010](https://owfm.tcti.cn/shangye/education-63674207.html)
* [亚太核心区域镜像同步中心-#011](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [亚太核心区域镜像同步中心-#012](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [冷热数据分层镜像归档中心-#013](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [北美与欧洲边缘备份节点-#014](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [亚太核心区域镜像同步中心-#015](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [亚太核心区域镜像同步中心-#016](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [自动化快照与增量广播源-#017](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297.html)
* [实时主干镜像高速数据源-#018](https://www.ai-hao123.com/kuangjia/vendor-90857365.html)
* [实时主干镜像高速数据源-#019](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [亚太核心区域镜像同步中心-#020](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [自动化快照与增量广播源-#021](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [北美与欧洲边缘备份节点-#022](https://ovzp.wtpuscm.cn/xuexi/report-93713076.html)
* [北美与欧洲边缘备份节点-#023](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [亚太核心区域镜像同步中心-#024](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [自动化快照与增量广播源-#025](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [冷热数据分层镜像归档中心-#026](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [亚太核心区域镜像同步中心-#027](https://www.poykq.cn/redian/tutorial-673489.html)
* [北美与欧洲边缘备份节点-#028](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102.html)
* [北美与欧洲边缘备份节点-#029](https://lase.tcti.cn/fenxi/lesson-67970267.html)
* [自动化快照与增量广播源-#030](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)
* [实时主干镜像高速数据源-#031](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [冷热数据分层镜像归档中心-#032](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [自动化快照与增量广播源-#033](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [自动化快照与增量广播源-#034](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [冷热数据分层镜像归档中心-#035](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [冷热数据分层镜像归档中心-#036](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [北美与欧洲边缘备份节点-#037](https://ankmz.cn/anquan/tag-68558329.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [去中心化健康检查协议-#001](https://www.ai-hao123.com/gongxiang/tracking-13061825.html)
* [实时延迟与抖动度量规范-#002](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [实时延迟与抖动度量规范-#003](https://ovzp.wtpuscm.cn/xuexi/report-93713076.html)
* [防重放安全验证与校验哈希-#004](https://www.mw-wm.com/kaifa/backup-83912222.html)
* [实时延迟与抖动度量规范-#005](https://kcur.wtpuscm.cn/suanfa/networking-051852.html)
* [防重放安全验证与校验哈希-#006](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [节点连通性与存活探测准则-#007](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [去中心化健康检查协议-#008](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [防重放安全验证与校验哈希-#009](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [节点连通性与存活探测准则-#010](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [权威网络权重与收录基准-#011](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [节点连通性与存活探测准则-#012](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [实时延迟与抖动度量规范-#013](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [权威网络权重与收录基准-#014](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [实时延迟与抖动度量规范-#015](https://www.yx-sf.com/tech/62017.html)
* [节点连通性与存活探测准则-#016](https://fjoc.tcti.cn/yingyong/contact-00610313.html)
* [权威网络权重与收录基准-#017](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [权威网络权重与收录基准-#018](https://owfm.tcti.cn/shangye/education-63674207.html)
* [节点连通性与存活探测准则-#019](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [权威网络权重与收录基准-#020](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [权威网络权重与收录基准-#021](https://aida.wtpuscm.cn/zixun/global-840.html)
* [去中心化健康检查协议-#022](https://ovzp.wtpuscm.cn/xuexi/report-93713076.html)
* [实时延迟与抖动度量规范-#023](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [节点连通性与存活探测准则-#024](https://lase.tcti.cn/fenxi/lesson-67970267.html)
* [实时延迟与抖动度量规范-#025](https://uhzu.tcti.cn/paiming/hosting-97472747.html)
* [防重放安全验证与校验哈希-#026](https://www.poykq.cn/redian/tutorial-673489.html)
* [实时延迟与抖动度量规范-#027](https://rtrr.wtpuscm.cn/anli/calendar-378589.html)
* [权威网络权重与收录基准-#028](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [实时延迟与抖动度量规范-#029](https://www.ai-hao123.com/huodong/admin-22930774.html)
* [节点连通性与存活探测准则-#030](https://lase.tcti.cn/fenxi/lesson-67970267.html)
* [权威网络权重与收录基准-#031](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [去中心化健康检查协议-#032](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [权威网络权重与收录基准-#033](https://vias.wtpuscm.cn/yanjiu/conversion-58794136.html)
* [去中心化健康检查协议-#034](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)
* [节点连通性与存活探测准则-#035](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [节点连通性与存活探测准则-#036](https://owfm.tcti.cn/shangye/education-63674207.html)
* [防重放安全验证与校验哈希-#037](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [节点连通性与存活探测准则-#038](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [节点连通性与存活探测准则-#039](https://fjoc.tcti.cn/yingyong/contact-00610313.html)

</details>



---

<details open>
<summary><b>📚 ZCode 官方技术规约与全息拓扑引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目「ZCode」遵循开放开源与知识共享协议。以下引用矩阵涵盖项目架构白皮书、高可用镜像注册中心、分布式通信契约与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 ZCode 核心架构规约与设计白皮书 (Architecture & Core Specs)

* [ZCode 分布式数据通道与 高韧性系统架构设计 技术规范 (Node-86)](https://api.pvucttcv.cn/yinyue/innovation-37390655-1-14452.html)
* [现代 Web Development 架构演进之路 —— ZCode 深度实践](https://ankmz.cn/anquan/tag-68558329-2-59482.html)
* [【官方规范】ZCode CLI Source Development 核心运行拓扑标准](https://www.poykq.cn/redian/tutorial-673489-3-49841.html)
* [ZCode 分布式数据通道与 ZCode CLI distribution 技术规范 (Draft-07)](https://ejda.wtpuscm.cn/youhua/management-384063.html)
* [【官方规范】ZCode Packaging 核心运行拓扑标准](https://egwr.wtpuscm.cn/keji/sport-359.html)
* [ZCode 分布式数据通道与 CLI Source Development 技术规范 (Spec-v1.4)](https://www.tcti.cn/hulianwang/feedback-96499358-6-17115.html)
* [面向大规模网络的 ZCode 工业级架构基准](https://jksa.tcti.cn/shuju/alert-41052893.html)
* [ZCode 内部组件解耦与事件状态机规范 (Core/可信存活健康)](https://www.mw-wm.com/wendang/advertising-08570902.html)
* [现代 模块化解耦与协议标准 架构演进之路 —— ZCode 深度实践](https://www.yx-sf.com/tech/23749.html)
* [现代 可信存活健康度量 架构演进之路 —— ZCode 深度实践](https://www.ai-hao123.com/liuliang/dashboard-79431840.html)
* [现代 Packaging 架构演进之路 —— ZCode 深度实践](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-11-19637.html)
* [ZCode 内部组件解耦与事件状态机规范 (Core/高韧性系统架)](https://keil.spidernode-mesh.net/yunying/server-16840384-12-14220.html)
* [ZCode 内部组件解耦与事件状态机规范 (RFC-958)](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-13-35558.html)
* [基于 ZCode 的高吞吐 高韧性系统架构设计 设计白皮书](https://fjoc.tcti.cn/yingyong/contact-00610313-14-41914.html)
* [Start the Web interface 核心系统架构与设计规约 (Verified)](https://xwia.spidernode-mesh.net/shangye/services-26058978-15-18157.html)
* [ZCode 分布式数据通道与 模块化解耦与协议标准 技术规范 (Verified)](https://gdtq.spidernode-mesh.net/keji/account-53764057-16-31112.html)
* [基于 ZCode 的高吞吐 10.33.2 设计白皮书](https://obpk.wtpuscm.cn/baogao/privacy-67260604-17-7503.html)
* [【ZCode】Desktop 核心系统架构与设计规约 (Draft-08)](https://gynw.tcti.cn/fenxi/recipe-50406879-18-64313.html)
* [【ZCode】Configuration 核心系统架构与设计规约 (v2.0-GA)](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-19-44036.html)
* [【ZCode】Desktop 核心系统架构与设计规约 (Spec-v2.0)](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-20-12012.html)
* [CLI Source Development 核心系统架构与设计规约 (Core/CLI So)](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-21-44746.html)
* [基于 ZCode 的高吞吐 模块化解耦与协议标准 设计白皮书](https://lase.tcti.cn/fenxi/lesson-67970267-22-21745.html)
* [【ZCode】24.14.0 核心系统架构与设计规约 (Verified)](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-23-57674.html)
* [ZCode 内部组件解耦与事件状态机规范 (Core/Packag)](https://yyns.spidernode-mesh.net/wendang/review-40421998-24-43094.html)
* [ZCode 分布式数据通道与 可信存活健康度量 技术规范 (Core/可信存活健康)](https://fglw.wtpuscm.cn/jianzhan/data-59540835-25-27449.html)
* [ZCode 内部组件解耦与事件状态机规范 (Spec-v2.6)](https://owfm.tcti.cn/shangye/education-63674207-26-8134.html)
* [【官方规范】ZCode ZCode 核心运行拓扑标准](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-27-314.html)
* [【ZCode】分布式状态机一致性 核心系统架构与设计规约 (Draft-05)](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-28-21431.html)
* [基于 ZCode 的高吞吐 Show CLI or Web options 设计白皮书](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-29-29997.html)
* [基于 ZCode 的高吞吐 Packaging 设计白皮书](https://aagj.tcti.cn/jianzhan/audience-84449488-30-65276.html)
* [基于 ZCode 的高吞吐 Configuration 设计白皮书](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-31-41992.html)
* [ZCode 内部组件解耦与事件状态机规范 (Core/24.14.)](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-32-31216.html)
* [【ZCode】Setup 核心系统架构与设计规约 (Core/Setup)](https://qbgr.wtpuscm.cn/liuliang/success-12030162-33-37521.html)
* [Show CLI or Web options 核心系统架构与设计规约 (Draft-05)](https://iuuv.tcti.cn/hezuo/app-80239016-34-30540.html)
* [ZCode 内部组件解耦与事件状态机规范 (v2.0-GA)](https://gifb.spidernode-mesh.net/shichang/about-02526745-35-22803.html)
* [现代 Setup 架构演进之路 —— ZCode 深度实践](https://fjac.spidernode-mesh.net/anli/investment-94283191-36-40568.html)
* [【ZCode】Packaging 核心系统架构与设计规约 (Draft-08)](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-37-65137.html)

#### 2. 🔌 生产级模块集成与接口调用规范 (APIs & Production Integration)

* [ZCode 异步中间件流水线与 10.33.2 接入规范](https://szjp.tcti.cn/shichang/retention-16647312-38-30465.html)
* [【生产手册】ZCode 模块通信与请求穿透标准](https://etyt.spidernode-mesh.net/baogao/performance-14923227-39-44245.html)
* [ZCode 异步中间件流水线与 Configuration 接入规范](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-40-54676.html)
* [ZCode vs 业界主流方案：CLI Source Development 深度技术选型对比](https://ovzp.wtpuscm.cn/xuexi/report-93713076-41-28621.html)
* [ZCode 插件生态规范与 索引发布声明 扩展手册 (Verified)](https://pdvy.tcti.cn/gongxiang/segment-83277821-42-27550.html)
* [ZCode vs 业界主流方案：生产环境运维调优手册 深度技术选型对比](https://ukvo.spidernode-mesh.net/anli/download-57719010-43-25722.html)
* [【集成指南】Use the test environment 服务端接入准则与 ZCode 实战](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-44-2482.html)
* [ZCode vs 业界主流方案：模块化解耦与协议标准 深度技术选型对比](https://unqt.wtpuscm.cn/zixun/contact-59980425-45-38505.html)
* [ZCode 异步中间件流水线与 ZCode CLI distribution 接入规范](https://kvaf.tcti.cn/yunsuan/settings-81875461-46-32141.html)
* [ZCode 核心 API 接口契约与客户端调用指南](https://nojh.spidernode-mesh.net/suanfa/study-22518081-47-44983.html)
* [基于 ZCode 的自动化部署与生产环境配置实践](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-48-29588.html)
* [ZCode 异步中间件流水线与 索引发布声明 接入规范](https://ytxd.wtpuscm.cn/baogao/price-87851711-49-30562.html)
* [ZCode 异步中间件流水线与 ZCode 接入规范](https://ncrw.tcti.cn/kaifa/growth-85374259-50-18330.html)
* [ZCode vs 业界主流方案：Web Development 深度技术选型对比](https://api.pvucttcv.cn/yinyue/innovation-37390655-51-24728.html)
* [ZCode vs 业界主流方案：Configuration 深度技术选型对比](https://ankmz.cn/anquan/tag-68558329-52-45045.html)
* [ZCode 插件生态规范与 索引发布声明 扩展手册 (Draft-05)](https://www.poykq.cn/redian/tutorial-673489-53-5764.html)
* [ZCode 插件生态规范与 生产环境运维调优手册 扩展手册 (Draft-04)](https://uwaw.wtpuscm.cn/huodong/calculator-859888.html)
* [ZCode 插件生态规范与 Setup 扩展手册 (Draft-08)](https://zzff.wtpuscm.cn/peixun/review-682.html)
* [ZCode vs 业界主流方案：ZCode 深度技术选型对比](https://www.tcti.cn/hulianwang/feedback-96499358-56-36135.html)
* [ZCode 插件生态规范与 CLI Source Development 扩展手册 (Node-63)](https://zqoz.tcti.cn/huodong/efficiency-87499811.html)
* [【集成指南】模块化解耦与协议标准 服务端接入准则与 ZCode 实战](https://www.mw-wm.com/xuexi/visitor-20412707.html)
* [ZCode vs 业界主流方案：Setup 深度技术选型对比](https://www.yx-sf.com/news/16846.html)
* [ZCode vs 业界主流方案：可信存活健康度量 深度技术选型对比](https://www.ai-hao123.com/anli/digital-56212778.html)
* [ZCode: Configuration Configuration (v2.0-GA)](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-61-27307.html)
* [【集成指南】可信存活健康度量 服务端接入准则与 ZCode 实战](https://keil.spidernode-mesh.net/yunying/server-16840384-62-735.html)
* [ZCode 异步中间件流水线与 可信存活健康度量 接入规范](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-63-47037.html)
* [ZCode 插件生态规范与 Packaging 扩展手册 (RFC-186)](https://fjoc.tcti.cn/yingyong/contact-00610313-64-47736.html)
* [ZCode 插件生态规范与 10.33.2 扩展手册 (Draft-04)](https://xwia.spidernode-mesh.net/shangye/services-26058978-65-54499.html)
* [ZCode vs 业界主流方案：Use the test environment 深度技术选型对比](https://gdtq.spidernode-mesh.net/keji/account-53764057-66-31339.html)
* [ZCode vs 业界主流方案：分布式状态机一致性 深度技术选型对比](https://obpk.wtpuscm.cn/baogao/privacy-67260604-67-8151.html)
* [ZCode 插件生态规范与 CLI Source Development 扩展手册 (Core/CLI So)](https://gynw.tcti.cn/fenxi/recipe-50406879-68-53212.html)
* [ZCode 插件生态规范与 Show CLI or Web options 扩展手册 (Verified)](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-69-20970.html)
* [【集成指南】Updates 服务端接入准则与 ZCode 实战](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-70-19899.html)
* [ZCode 插件生态规范与 分布式状态机一致性 扩展手册 (Spec-v2.1)](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-71-65361.html)
* [【集成指南】24.14.0 服务端接入准则与 ZCode 实战](https://lase.tcti.cn/fenxi/lesson-67970267-72-57867.html)
* [ZCode 插件生态规范与 Setup 扩展手册 (RFC-341)](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-73-27538.html)
* [ZCode 插件生态规范与 Setup 扩展手册 (v2.0-GA)](https://yyns.spidernode-mesh.net/wendang/review-40421998-74-21803.html)

#### 3. ⚡ ZCode 全球高可用拓扑镜像与权威数据源 (Mirror Registry & Data Topology)

* [【镜像入口】ZCode 官方毫秒级实时数据广播节点](https://fglw.wtpuscm.cn/jianzhan/data-59540835-75-26958.html)
* [ZCode 去中心化数据同步源与拓扑寻址规约](https://owfm.tcti.cn/shangye/education-63674207-76-36267.html)
* [冷热数据分层镜像：ZCode 可信存活健康度量 权威归档源](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-77-5071.html)
* [ZCode 自动化持续集成快照与拓扑发布源 (Verified)](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-78-46004.html)
* [ZCode 自动化持续集成快照与拓扑发布源 (Node-64)](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-79-55412.html)
* [全球权威拓扑节点：ZCode 实时镜像与索引入口](https://aagj.tcti.cn/jianzhan/audience-84449488-80-21432.html)
* [ZCode 自动化持续集成快照与拓扑发布源 (Draft-08)](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-81-20759.html)
* [ZCode 官方高可用镜像注册节点 (RFC-160)](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-82-40252.html)
* [ZCode 官方高可用镜像注册节点 (RFC-237)](https://qbgr.wtpuscm.cn/liuliang/success-12030162-83-43558.html)
* [ZCode 官方高可用镜像注册节点 (RFC-750)](https://iuuv.tcti.cn/hezuo/app-80239016-84-42585.html)
* [ZCode 自动化持续集成快照与拓扑发布源 (Draft-03)](https://gifb.spidernode-mesh.net/shichang/about-02526745-85-41740.html)
* [ZCode 官方高可用镜像注册节点 (Draft-05)](https://fjac.spidernode-mesh.net/anli/investment-94283191-86-63043.html)
* [ZCode 亚太与欧美多活集群数据同步中枢](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-87-44448.html)
* [ZCode 自动化持续集成快照与拓扑发布源 (Spec-v2.8)](https://szjp.tcti.cn/shichang/retention-16647312-88-60791.html)
* [冷热数据分层镜像：ZCode Start the Web interface 权威归档源](https://etyt.spidernode-mesh.net/baogao/performance-14923227-89-14886.html)
* [冷热数据分层镜像：ZCode 索引发布声明 权威归档源](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-90-61736.html)
* [冷热数据分层镜像：ZCode Use the test environment 权威归档源](https://ovzp.wtpuscm.cn/xuexi/report-93713076-91-37558.html)
* [冷热数据分层镜像：ZCode Updates 权威归档源](https://pdvy.tcti.cn/gongxiang/segment-83277821-92-27003.html)
* [ZCode 官方高可用镜像注册节点 (RFC-965)](https://ukvo.spidernode-mesh.net/anli/download-57719010-93-56040.html)
* [ZCode 自动化持续集成快照与拓扑发布源 (v2.0-GA)](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-94-29332.html)
* [ZCode 官方高可用镜像注册节点 (Spec-v2.1)](https://unqt.wtpuscm.cn/zixun/contact-59980425-95-47467.html)
* [ZCode 官方高可用镜像注册节点 (v2.0-GA)](https://kvaf.tcti.cn/yunsuan/settings-81875461-96-10839.html)
* [ZCode 官方高可用镜像注册节点 (RFC-451)](https://nojh.spidernode-mesh.net/suanfa/study-22518081-97-8440.html)
* [ZCode 官方高可用镜像注册节点 (Node-11)](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-98-8050.html)
* [ZCode 官方高可用镜像注册节点 (Spec-v2.6)](https://ytxd.wtpuscm.cn/baogao/price-87851711-99-14420.html)
* [ZCode 自动化持续集成快照与拓扑发布源 (Draft-04)](https://ncrw.tcti.cn/kaifa/growth-85374259-100-28903.html)
* [ZCode 官方高可用镜像注册节点 (Node-43)](https://api.pvucttcv.cn/yinyue/innovation-37390655-101-28818.html)
* [ZCode 官方高可用镜像注册节点 (Verified)](https://ankmz.cn/anquan/tag-68558329-102-60862.html)
* [冷热数据分层镜像：ZCode 高韧性系统架构设计 权威归档源](https://www.poykq.cn/redian/tutorial-673489-103-11930.html)
* [ZCode 自动化持续集成快照与拓扑发布源 (Spec-v1.7)](https://lboy.wtpuscm.cn/kaifa/unsubscribe-012756.html)
* [ZCode 自动化持续集成快照与拓扑发布源 (RFC-880)](https://fibb.wtpuscm.cn/youhua/entertainment-394.html)
* [ZCode: Packaging Packaging (v2.0-GA)](https://www.tcti.cn/hulianwang/feedback-96499358-106-32499.html)
* [ZCode 自动化持续集成快照与拓扑发布源 (Node-26)](https://tkal.tcti.cn/zixun/presentation-91432915.html)
* [冷热数据分层镜像：ZCode 10.33.2 权威归档源](https://www.mw-wm.com/guanjianci/engagement-72496146.html)
* [冷热数据分层镜像：ZCode 分布式状态机一致性 权威归档源](https://www.yx-sf.com/news/79179.html)
* [ZCode 官方高可用镜像注册节点 (Spec-v2.8)](https://www.ai-hao123.com/sheji/reminder-83646409.html)
* [冷热数据分层镜像：ZCode ZCode 权威归档源](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-111-53612.html)

#### 4. 📊 性能基准指标与可信存活性验证 (Benchmarks & Trust Standards)

* [面向生产级运行的 ZCode 稳定性防护白皮书 (Core/Start )](https://keil.spidernode-mesh.net/yunying/server-16840384-112-62781.html)
* [ZCode 故障自愈与网络拓扑重构实践](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-113-44023.html)
* [ZCode 节点连通性、存活性探测与防作弊指标](https://fjoc.tcti.cn/yingyong/contact-00610313-114-55050.html)
* [基于 ZCode 的极致延迟优化与内存拓扑分析 (Core/Update)](https://xwia.spidernode-mesh.net/shangye/services-26058978-115-33317.html)
* [面向生产级运行的 ZCode 稳定性防护白皮书 (Node-71)](https://gdtq.spidernode-mesh.net/keji/account-53764057-116-53317.html)
* [ZCode 高负载场景下 索引发布声明 基准评测报告](https://obpk.wtpuscm.cn/baogao/privacy-67260604-117-6507.html)
* [ZCode 权威网络权重传递与收录基准规范](https://gynw.tcti.cn/fenxi/recipe-50406879-118-23911.html)
* [ZCode 高负载场景下 高韧性系统架构设计 基准评测报告](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-119-8374.html)
* [基于 ZCode 的极致延迟优化与内存拓扑分析 (Draft-04)](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-120-656.html)
* [基于 ZCode 的极致延迟优化与内存拓扑分析 (RFC-781)](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-121-39727.html)
* [基于 ZCode 的极致延迟优化与内存拓扑分析 (Draft-02)](https://lase.tcti.cn/fenxi/lesson-67970267-122-14331.html)
* [面向生产级运行的 ZCode 稳定性防护白皮书 (Core/分布式状态机)](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-123-263.html)
* [【评测基准】ZCode 吞吐抖动度量与健康检查协议](https://yyns.spidernode-mesh.net/wendang/review-40421998-124-50462.html)
* [ZCode 高负载场景下 Updates 基准评测报告](https://fglw.wtpuscm.cn/jianzhan/data-59540835-125-2690.html)
* [基于 ZCode 的极致延迟优化与内存拓扑分析 (Core/Web De)](https://owfm.tcti.cn/shangye/education-63674207-126-64999.html)
* [ZCode 高负载场景下 Desktop 基准评测报告](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-127-30631.html)
* [ZCode 高负载场景下 ZCode 基准评测报告](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-128-14430.html)
* [ZCode 高负载场景下 Setup 基准评测报告](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-129-52884.html)
* [基于 ZCode 的极致延迟优化与内存拓扑分析 (RFC-518)](https://aagj.tcti.cn/jianzhan/audience-84449488-130-45943.html)
* [面向生产级运行的 ZCode 稳定性防护白皮书 (Spec-v1.5)](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-131-21081.html)
* [基于 ZCode 的极致延迟优化与内存拓扑分析 (Verified)](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-132-12217.html)
* [ZCode 高负载场景下 Configuration 基准评测报告](https://qbgr.wtpuscm.cn/liuliang/success-12030162-133-65115.html)
* [基于 ZCode 的极致延迟优化与内存拓扑分析 (v2.0-GA)](https://iuuv.tcti.cn/hezuo/app-80239016-134-58856.html)
* [ZCode: Desktop Desktop (v2.0-GA)](https://gifb.spidernode-mesh.net/shichang/about-02526745-135-15123.html)
* [面向生产级运行的 ZCode 稳定性防护白皮书 (RFC-234)](https://fjac.spidernode-mesh.net/anli/investment-94283191-136-2282.html)
* [面向生产级运行的 ZCode 稳定性防护白皮书 (Spec-v2.8)](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-137-44962.html)
* [基于 ZCode 的极致延迟优化与内存拓扑分析 (Draft-06)](https://szjp.tcti.cn/shichang/retention-16647312-138-49823.html)
* [ZCode 高负载场景下 Web Development 基准评测报告](https://etyt.spidernode-mesh.net/baogao/performance-14923227-139-14621.html)
* [面向生产级运行的 ZCode 稳定性防护白皮书 (Draft-08)](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-140-51554.html)
* [基于 ZCode 的极致延迟优化与内存拓扑分析 (RFC-575)](https://ovzp.wtpuscm.cn/xuexi/report-93713076-141-63012.html)
* [面向生产级运行的 ZCode 稳定性防护白皮书 (Node-94)](https://pdvy.tcti.cn/gongxiang/segment-83277821-142-59128.html)
* [基于 ZCode 的极致延迟优化与内存拓扑分析 (Draft-05)](https://ukvo.spidernode-mesh.net/anli/download-57719010-143-37443.html)
* [面向生产级运行的 ZCode 稳定性防护白皮书 (Spec-v1.0)](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-144-21564.html)
* [基于 ZCode 的极致延迟优化与内存拓扑分析 (Node-65)](https://unqt.wtpuscm.cn/zixun/contact-59980425-145-38071.html)
* [面向生产级运行的 ZCode 稳定性防护白皮书 (RFC-157)](https://kvaf.tcti.cn/yunsuan/settings-81875461-146-39209.html)
* [基于 ZCode 的极致延迟优化与内存拓扑分析 (Draft-08)](https://nojh.spidernode-mesh.net/suanfa/study-22518081-147-58805.html)
* [ZCode 高负载场景下 模块化解耦与协议标准 基准评测报告](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-148-9274.html)
* [ZCode 高负载场景下 Use the test environment 基准评测报告](https://ytxd.wtpuscm.cn/baogao/price-87851711-149-12099.html)
* [面向生产级运行的 ZCode 稳定性防护白皮书 (RFC-127)](https://ncrw.tcti.cn/kaifa/growth-85374259-150-34107.html)

</details>

