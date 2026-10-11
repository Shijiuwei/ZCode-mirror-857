# zcode-cli

TypeScript + Node.js 24.14.0 CLI starter. The default artifact is a normal Node CLI bundle, and SEA is kept as an optional packaging path.

## Why This Shape

- Runtime code has zero production dependencies.
- The CLI uses Node built-ins for argument parsing and terminal control.
- `npm run build` produces `dist/zcode.cjs`, which works anywhere Node.js 24.14.0 is installed.
- `npm run sea` attempts to turn that same bundle into a single executable.
- If SEA breaks on a platform, the normal CLI artifact is still the fallback.

## Commands

```sh
npm run bootstrap
npm run dev -- --help
npm run build
npm run start -- doctor --json
npm test
npm run sea
npm run sea -- --target linux-x64 --target win-x64
npm run sea -- --all
```

## Project Layout

```txt
src/
  cli/       command parsing and process wiring
  core/      reusable runtime logic
  ui/        terminal UI layer
scripts/    build and optional SEA packaging scripts
tests/      subprocess-level CLI tests
```

## Bootstrap

Run `npm run bootstrap` after cloning the repository. It checks the local Node.js version, installs dependencies, and runs the full project check.

## Plugin Development

zcode plugins are local bundles that can contribute skills, custom commands, and MCP servers.

Plugin state lives under `~/.zcode/cli/plugins`:

- `cache/`: installed marketplace plugin code and static files.
- `data/<plugin-id>/`: persistent plugin data. MCP servers should write runtime output here, not into the plugin source directory.
- `marketplaces/zcode-plugins-official/`: bundled and CDN partitions plus the merged metadata for the single official marketplace.

This repository also ships built-in official plugins as workspace packages. The bundled Browser Use, Document Skills, Skill Creator, and ZCode Guide content plugins are default-enabled and appear as `browser-use@zcode-plugins-official`, `document-skills@zcode-plugins-official`, `skill-creator@zcode-plugins-official`, and `zcode-guide@zcode-plugins-official`. Runtime-heavy official plugins, and local-data migration plugins such as `ios-simulator@zcode-plugins-official`, `android-emulator@zcode-plugins-official`, and `restore-legacy-sessions@zcode-plugins-official`, are discovered by zcode but stay disabled until the user enables them.

```sh
zcode plugins list
zcode plugins enable ios-simulator
zcode plugins disable browser-use
zcode plugins enable restore-legacy-sessions
zcode plugins disable ios-simulator
```

For local plugin development, put the plugin in any directory, then add it to the user config. Local plugin dirs default to enabled for that config.

```json
{
  "plugins": {
    "enabled": true,
    "dirs": ["/absolute/path/to/my-plugin"]
  }
}
```

### Plugin Manifest

MCP config can live directly in `.zcode-plugin/plugin.json` through `mcpServers`. A plugin may provide both `.mcp.json` and manifest `mcpServers`; when the same server name appears in both places, `mcpServers` from the selected manifest wins.

Supported fields in the current zcode plugin surface:

- `name`, `version`, `description`, `author`, `license`
- `skills`: relative folder or folders containing `SKILL.md` files
- `commands`: relative folder or folders containing markdown custom commands
- `mcpServers`: inline MCP server config, or a relative path to one
- `userConfig`: option defaults used by `${user_config.key}` expansion

Example `.zcode-plugin/plugin.json` with inline MCP config:

```json
{
  "name": "ios-simulator",
  "version": "0.1.0",
  "skills": "skills",
  "commands": "commands",
  "mcpServers": {
    "ios-simulator": {
      "command": "node",
      "args": ["${ZCODE_PLUGIN_ROOT}/dist/mcp/server.js"],
      "cwd": "${ZCODE_PROJECT_DIR}",
      "env": {
        "PLUGIN_DATA": "${ZCODE_PLUGIN_DATA}",
        "DEFAULT_DEVICE": "${user_config.default_device}"
      }
    }
  },
  "userConfig": {
    "default_device": {
      "type": "string",
      "default": "iPhone 16"
    }
  }
}
```

### Variables

Plugin MCP config can use these variable names:

- `${ZCODE_PLUGIN_ROOT}`
- `${ZCODE_PLUGIN_DATA}`
- `${ZCODE_PROJECT_DIR}`
- `${user_config.key}`
- `${ZCODE_SOME_ENV}`

Only environment variables with the `ZCODE_` prefix are expanded. Missing variables disable the affected MCP server and produce a plugin diagnostic.

### Recommended Layout

```txt
my-plugin/
  .zcode-plugin/plugin.json
  .mcp.json
  skills/
    my-skill/SKILL.md
  commands/
    my-command.md
  src/
```

For MCP servers, prefer Node's normal package build and `bin` output when targeting zcode-cli, and keep all process/file/network side effects inside the MCP server boundary.

## MCP Configuration

zcode reads MCP servers from the main JSON config. The default user config path is `~/.zcode/cli/config.json`; MCP entries live under `mcp.servers`. MCP is enabled by default, so `features.mcp` only needs to be set when you want an explicit on/off switch. The current CLI does not auto-discover standalone `mcp.json` or `.mcp.json` files outside enabled plugins.

```json
{
  "features": {
    "mcp": true
  },
  "mcp": {
    "servers": {
      "filesystem": {
        "type": "stdio",
        "command": "npx",
        "args": ["-y", "@modelcontextprotocol/server-filesystem", "."],
        "cwd": ".",
        "timeoutMs": 30000
      },
      "docs": {
        "type": "http",
        "url": "https://mcp.example.com/mcp",
        "headers": {
          "Authorization": "Bearer <token>"
        }
      },
      "legacy-sse": {
        "type": "sse",
        "url": "https://mcp.example.com/sse",
        "enabled": false
      }
    }
  }
}
```

Supported server types:

- `stdio`: requires `command`; accepts `args`, `cwd`, `env`, `enabled`, and `timeoutMs`. `cwd` is resolved from the active working directory, and the server process inherits zcode's environment plus any `env` overrides.
- `http`: requires `url`; accepts `headers`, `enabled`, and `timeoutMs`.
- `sse`: requires `url`; accepts `headers`, `enabled`, and `timeoutMs`.

MCP tools are registered before the first model request and exposed as `mcp__<server>__<tool>`. Use `/mcp list`, `/mcp status`, `/mcp connect <server>`, and `/mcp disconnect <server>` inside the CLI to inspect or manage configured servers for the current session.

## Hooks Configuration

zcode reads hooks from the same main JSON config file as MCP, usually `~/.zcode/cli/config.json`. Hooks are disabled by default; set `hooks.enabled` to `true` and add process hooks under `hooks.events`.

Supported hook events:

- `SessionStart`: runs after session context is initialized and before the first normal prompt reaches the model. It can add context. Its matcher sees the source, such as `startup` or `resume`.
- `UserPromptSubmit`: runs before the user prompt is written to message history or sent to the model. It can block the prompt with `continue: false` or add context. Its matcher sees the raw prompt text.
- `PreToolUse`: runs before a client-side tool executes. It can deny, ask, allow, replace tool input, or add model-visible context. Its matcher sees the tool name.
- `PermissionRequest`: runs when a tool needs approval. It can allow, deny, update permissions, or modify the pending tool input. Its matcher sees the tool name.
- `PostToolUse`: runs after a tool succeeds and before the tool result is returned to the model. It can add context. Its matcher sees the tool name.
- `PostToolUseFailure`: runs after a tool fails and before the failure is returned to the model. It can add recovery context. Its matcher sees the tool name.
- `Stop`: runs when a turn is about to complete without another client-side tool call. It can add feedback and request one more model step with `continue: true`. Empty `continue: true` output is ignored, and repeated continuations are capped to avoid loops.

Example:

```json
{
  "hooks": {
    "enabled": true,
    "timeoutMs": 60000,
    "maxOutputBytes": 32768,
    "events": {
      "SessionStart": [
        {
          "matcher": "startup|resume",
          "hooks": [
            {
              "type": "process",
              "command": "node",
              "args": ["./scripts/session-start-hook.mjs"]
            }
          ]
        }
      ],
      "PreToolUse": [
        {
          "matcher": "^(Bash|Write|Edit)$",
          "hooks": [
            {
              "type": "process",
              "command": "node",
              "args": ["./scripts/pre-tool-hook.mjs"],
              "timeoutMs": 5000
            }
          ]
        }
      ],
      "Stop": [
        {
          "hooks": [
            {
              "type": "process",
              "command": "node",
              "args": ["./scripts/stop-hook.mjs"]
            }
          ]
        }
      ]
    }
  }
}
```

Configuration shape:

- `modelStream.idleTimeoutMs`: initial idle timeout between model SSE events. Defaults to `600000`.
- `hooks.enabled`: enables configured hook execution. Defaults to `false`.
- `hooks.timeoutMs`: default timeout for each hook process. Defaults to `60000`.
- `hooks.maxOutputBytes`: stdout/stderr capture limit for hook processes. Defaults to `32768`.
- `hooks.events.<EventName>`: an array of matcher groups. Groups run in config order.
- `matcher`: optional JavaScript regular expression string. If omitted, the group matches all inputs for that event.
- `hooks`: process hook list for the matcher group. Hooks run in order.
- `type`: currently only `process` is supported.
- `command`: executable to run, using argv execution rather than a shell string.
- `args`: optional argv array.
- `timeoutMs`: optional per-hook timeout override.
- `statusMessage`: optional status label for future UI projection.

Each process hook receives one JSON hook input on stdin and may print one JSON object to stdout. Empty stdout is treated as no-op. Non-JSON stdout, schema-invalid stdout, timeouts, and non-zero exits other than exit code `2` are recorded as hook failures and do not crash the turn by default. Exit code `2` is treated as an explicit block/deny request.

Common stdout examples:

```json
{
  "hookSpecificOutput": {
    "hookEventName": "SessionStart",
    "additionalContext": "Use the internal API migration checklist for this repository."
  }
}
```

```json
{
  "continue": false,
  "reason": "Do not run destructive shell commands in this workspace.",
  "hookSpecificOutput": {
    "hookEventName": "PreToolUse",
    "permissionDecision": "deny",
    "permissionDecisionReason": "Blocked by project hook."
  }
}
```

```json
{
  "continue": true,
  "hookSpecificOutput": {
    "hookEventName": "Stop",
    "additionalContext": "Before finalizing, verify that the answer mentions test coverage."
  }
}
```

## Packaging Strategy

1. Start with the normal Node CLI bundle from `npm run build`.
2. `npm run sea` builds the current host target by default.
3. Use `npm run sea -- --target <platform-arch>` or `npm run sea -- --all` for cross-target SEA packaging.
4. SEA target Node.js binaries are downloaded from the official Node.js release for the current `process.versions.node` and verified against `SHASUMS256.txt`.
5. Keep native addons and runtime dynamic imports out of the core CLI until SEA compatibility is proven.
6. Add richer TUI libraries later only behind a compatibility spike.


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [全息网络通信节点白名单-#001](https://fwra.wtpuscm.cn/yinqing/prospect-865467.html)
* [边缘高吞吐调度路由矩阵-#002](https://exsw.yx-sf.com/youhua/budget-75395254.html)
* [高韧性数据交换通道规约-#003](https://mznw.ai-hao123.com/kaifa/login-17204469.html)
* [多活集群负载感知指南-#004](https://acfo.ai-hao123.com/jishu/advertising-97553670.html)
* [高韧性数据交换通道规约-#005](https://jsmw.wtpuscm.cn/pingtai/success-293217.html)
* [边缘高吞吐调度路由矩阵-#006](https://aalj.wtpuscm.cn/sheji/engagement-807379.html)
* [高韧性数据交换通道规约-#007](https://pohd.ai-hao123.com/yunying/luxury-64366913.html)
* [全息网络通信节点白名单-#008](https://dpgc.ai-hao123.com/peixun/local-86996340.html)
* [高韧性数据交换通道规约-#009](https://nlhq.tcti.cn/zixun/message-75753271.html)
* [全息网络通信节点白名单-#010](https://klnd.yx-sf.com/xinwen/market-27283395.html)
* [全球分布式拓扑索引节点-#011](https://zaaj.yx-sf.com/xuexi/digital-45758894.html)
* [多活集群负载感知指南-#012](https://krqb.tcti.cn/fenxi/privacy-40467252.html)
* [多活集群负载感知指南-#013](https://gudu.wtpuscm.cn/shichang/products-919595.html)
* [边缘高吞吐调度路由矩阵-#014](https://pohd.ai-hao123.com/yunying/luxury-64366913.html)
* [高韧性数据交换通道规约-#015](https://yyak.yx-sf.com/liuliang/learning-84631732.html)
* [全球分布式拓扑索引节点-#016](https://zszv.tcti.cn/zhineng/digital-17268206.html)
* [高韧性数据交换通道规约-#017](https://www.poykq.cn/redian/tutorial-673489.html)
* [多活集群负载感知指南-#018](https://fljr.yx-sf.com/jiaocheng/sale-99240382.html)
* [全息网络通信节点白名单-#019](https://zaaj.yx-sf.com/xuexi/digital-45758894.html)
* [高韧性数据交换通道规约-#020](https://werd.ai-hao123.com/fenxi/settings-69929794.html)
* [全息网络通信节点白名单-#021](https://jjlo.tcti.cn/xuexi/reminder-15742330.html)
* [边缘高吞吐调度路由矩阵-#022](https://khaw.ai-hao123.com/anli/behavior-35439337.html)
* [全球分布式拓扑索引节点-#023](https://exsw.yx-sf.com/youhua/budget-75395254.html)
* [边缘高吞吐调度路由矩阵-#024](https://werq.yx-sf.com/yingyong/advertising-29792751.html)
* [全球分布式拓扑索引节点-#025](https://skyy.tcti.cn/shuju/price-06442157.html)
* [全息网络通信节点白名单-#026](https://twqa.yx-sf.com/fuwu/growth-54155500.html)
* [边缘高吞吐调度路由矩阵-#027](https://twqa.yx-sf.com/fuwu/growth-54155500.html)
* [边缘高吞吐调度路由矩阵-#028](https://fwra.wtpuscm.cn/yinqing/prospect-865467.html)
* [多活集群负载感知指南-#029](https://mznw.ai-hao123.com/kaifa/login-17204469.html)
* [边缘高吞吐调度路由矩阵-#030](https://elqa.yx-sf.com/baogao/premium-64991186.html)
* [高韧性数据交换通道规约-#031](https://zaaj.yx-sf.com/xuexi/digital-45758894.html)
* [边缘高吞吐调度路由矩阵-#032](https://aaoa.wtpuscm.cn/zhinan/forum-595217.html)
* [高韧性数据交换通道规约-#033](https://lelc.ai-hao123.com/xinwen/luxury-01042389.html)
* [全球分布式拓扑索引节点-#034](https://fljr.yx-sf.com/jiaocheng/sale-99240382.html)
* [全息网络通信节点白名单-#035](https://fwra.wtpuscm.cn/yinqing/prospect-865467.html)
* [边缘高吞吐调度路由矩阵-#036](https://khaw.ai-hao123.com/anli/behavior-35439337.html)
* [全息网络通信节点白名单-#037](https://mies.ai-hao123.com/xitong/funnel-40438469.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [异步事件循环架构设计规范-#001](https://fljr.yx-sf.com/jiaocheng/sale-99240382.html)
* [异步事件循环架构设计规范-#002](https://lsex.ai-hao123.com/gongju/calendar-17741275.html)
* [多协议互联数据格式规范-#003](https://ttgk.yx-sf.com/xitong/chapter-52812607.html)
* [安全边界与可信凭证规约手册-#004](https://fljr.yx-sf.com/jiaocheng/sale-99240382.html)
* [高并发内存拓扑优化白皮书-#005](https://dpgc.ai-hao123.com/peixun/local-86996340.html)
* [异步事件循环架构设计规范-#006](https://jsmw.wtpuscm.cn/pingtai/success-293217.html)
* [异步事件循环架构设计规范-#007](https://rpfb.yx-sf.com/zhinan/strategy-40918346.html)
* [高并发内存拓扑优化白皮书-#008](https://exsw.yx-sf.com/youhua/budget-75395254.html)
* [多协议互联数据格式规范-#009](https://xobx.tcti.cn/paiming/beauty-62525661.html)
* [异步事件循环架构设计规范-#010](https://werq.yx-sf.com/yingyong/advertising-29792751.html)
* [多协议互联数据格式规范-#011](https://cvxt.wtpuscm.cn/yanjiu/template-505607.html)
* [异步事件循环架构设计规范-#012](https://aaoa.wtpuscm.cn/zhinan/forum-595217.html)
* [多协议互联数据格式规范-#013](https://twqa.yx-sf.com/fuwu/growth-54155500.html)
* [异步事件循环架构设计规范-#014](https://scdi.wtpuscm.cn/liuliang/identity-112354.html)
* [高并发内存拓扑优化白皮书-#015](https://mies.ai-hao123.com/xitong/funnel-40438469.html)
* [高并发内存拓扑优化白皮书-#016](https://fwra.wtpuscm.cn/yinqing/prospect-865467.html)
* [RFC 分布式调度与一致性算法标准-#017](https://srec.ai-hao123.com/chuangxin/photo-93384708.html)
* [RFC 分布式调度与一致性算法标准-#018](https://fljr.yx-sf.com/jiaocheng/sale-99240382.html)
* [多协议互联数据格式规范-#019](https://werd.ai-hao123.com/fenxi/settings-69929794.html)
* [RFC 分布式调度与一致性算法标准-#020](https://gudu.wtpuscm.cn/shichang/products-919595.html)
* [RFC 分布式调度与一致性算法标准-#021](https://aalj.wtpuscm.cn/sheji/engagement-807379.html)
* [安全边界与可信凭证规约手册-#022](https://scdi.wtpuscm.cn/liuliang/identity-112354.html)
* [异步事件循环架构设计规范-#023](https://www.poykq.cn/redian/tutorial-673489.html)
* [RFC 分布式调度与一致性算法标准-#024](https://acfo.ai-hao123.com/jishu/advertising-97553670.html)
* [异步事件循环架构设计规范-#025](https://fljr.yx-sf.com/jiaocheng/sale-99240382.html)
* [异步事件循环架构设计规范-#026](https://zaaj.yx-sf.com/xuexi/digital-45758894.html)
* [异步事件循环架构设计规范-#027](https://mznw.ai-hao123.com/kaifa/login-17204469.html)
* [高并发内存拓扑优化白皮书-#028](https://yjdn.ai-hao123.com/guanjianci/interface-80887664.html)
* [多协议互联数据格式规范-#029](https://aalj.wtpuscm.cn/sheji/engagement-807379.html)
* [安全边界与可信凭证规约手册-#030](https://skwa.wtpuscm.cn/qiye/sales-452431.html)
* [安全边界与可信凭证规约手册-#031](https://werd.ai-hao123.com/fenxi/settings-69929794.html)
* [高并发内存拓扑优化白皮书-#032](https://mies.ai-hao123.com/xitong/funnel-40438469.html)
* [安全边界与可信凭证规约手册-#033](https://skwa.wtpuscm.cn/qiye/sales-452431.html)
* [多协议互联数据格式规范-#034](https://ptve.ai-hao123.com/zhineng/promotion-94588549.html)
* [高并发内存拓扑优化白皮书-#035](https://rpfb.yx-sf.com/zhinan/strategy-40918346.html)
* [安全边界与可信凭证规约手册-#036](https://bxxq.yx-sf.com/wenzhang/discount-10594767.html)
* [多协议互联数据格式规范-#037](https://skwa.wtpuscm.cn/qiye/sales-452431.html)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [实时主干镜像高速数据源-#001](https://cvxt.wtpuscm.cn/yanjiu/template-505607.html)
* [自动化快照与增量广播源-#002](https://scdi.wtpuscm.cn/liuliang/identity-112354.html)
* [自动化快照与增量广播源-#003](https://skwa.wtpuscm.cn/qiye/sales-452431.html)
* [实时主干镜像高速数据源-#004](https://www.poykq.cn/redian/tutorial-673489.html)
* [自动化快照与增量广播源-#005](https://dpgc.ai-hao123.com/peixun/local-86996340.html)
* [实时主干镜像高速数据源-#006](https://krqb.tcti.cn/fenxi/privacy-40467252.html)
* [北美与欧洲边缘备份节点-#007](https://aalj.wtpuscm.cn/sheji/engagement-807379.html)
* [冷热数据分层镜像归档中心-#008](https://ptve.ai-hao123.com/zhineng/promotion-94588549.html)
* [实时主干镜像高速数据源-#009](https://kido.yx-sf.com/jiaoliu/conference-73984125.html)
* [北美与欧洲边缘备份节点-#010](https://zszv.tcti.cn/zhineng/digital-17268206.html)
* [自动化快照与增量广播源-#011](https://yyak.yx-sf.com/liuliang/learning-84631732.html)
* [亚太核心区域镜像同步中心-#012](https://xobx.tcti.cn/paiming/beauty-62525661.html)
* [冷热数据分层镜像归档中心-#013](https://eqrt.wtpuscm.cn/paiming/discovery-059548.html)
* [实时主干镜像高速数据源-#014](https://ptve.ai-hao123.com/zhineng/promotion-94588549.html)
* [自动化快照与增量广播源-#015](https://ptve.ai-hao123.com/zhineng/promotion-94588549.html)
* [北美与欧洲边缘备份节点-#016](https://mznw.ai-hao123.com/kaifa/login-17204469.html)
* [冷热数据分层镜像归档中心-#017](https://ubjm.tcti.cn/hezuo/contact-66995532.html)
* [北美与欧洲边缘备份节点-#018](https://aaoa.wtpuscm.cn/zhinan/forum-595217.html)
* [亚太核心区域镜像同步中心-#019](https://gudu.wtpuscm.cn/shichang/products-919595.html)
* [实时主干镜像高速数据源-#020](https://mies.ai-hao123.com/xitong/funnel-40438469.html)
* [自动化快照与增量广播源-#021](https://kido.yx-sf.com/jiaoliu/conference-73984125.html)
* [实时主干镜像高速数据源-#022](https://jjlo.tcti.cn/xuexi/reminder-15742330.html)
* [亚太核心区域镜像同步中心-#023](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [自动化快照与增量广播源-#024](https://nnfu.tcti.cn/gongxiang/excellence-64418623.html)
* [实时主干镜像高速数据源-#025](https://jsmw.wtpuscm.cn/pingtai/success-293217.html)
* [亚太核心区域镜像同步中心-#026](https://nnfu.tcti.cn/gongxiang/excellence-64418623.html)
* [实时主干镜像高速数据源-#027](https://srec.ai-hao123.com/chuangxin/photo-93384708.html)
* [冷热数据分层镜像归档中心-#028](https://lsex.ai-hao123.com/gongju/calendar-17741275.html)
* [自动化快照与增量广播源-#029](https://cvxt.wtpuscm.cn/yanjiu/template-505607.html)
* [亚太核心区域镜像同步中心-#030](https://eqrt.wtpuscm.cn/paiming/discovery-059548.html)
* [亚太核心区域镜像同步中心-#031](https://khaw.ai-hao123.com/anli/behavior-35439337.html)
* [北美与欧洲边缘备份节点-#032](https://acfo.ai-hao123.com/jishu/advertising-97553670.html)
* [自动化快照与增量广播源-#033](https://dpgc.ai-hao123.com/peixun/local-86996340.html)
* [冷热数据分层镜像归档中心-#034](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [实时主干镜像高速数据源-#035](https://fljr.yx-sf.com/jiaocheng/sale-99240382.html)
* [北美与欧洲边缘备份节点-#036](https://zaaj.yx-sf.com/xuexi/digital-45758894.html)
* [北美与欧洲边缘备份节点-#037](https://pohd.ai-hao123.com/yunying/luxury-64366913.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [防重放安全验证与校验哈希-#001](https://aaoa.wtpuscm.cn/zhinan/forum-595217.html)
* [权威网络权重与收录基准-#002](https://skwa.wtpuscm.cn/qiye/sales-452431.html)
* [节点连通性与存活探测准则-#003](https://ivhw.wtpuscm.cn/wangluo/layout-331715.html)
* [权威网络权重与收录基准-#004](https://dpgc.ai-hao123.com/peixun/local-86996340.html)
* [节点连通性与存活探测准则-#005](https://zaaj.yx-sf.com/xuexi/digital-45758894.html)
* [去中心化健康检查协议-#006](https://fwra.wtpuscm.cn/yinqing/prospect-865467.html)
* [权威网络权重与收录基准-#007](https://zaaj.yx-sf.com/xuexi/digital-45758894.html)
* [去中心化健康检查协议-#008](https://ttgk.yx-sf.com/xitong/chapter-52812607.html)
* [权威网络权重与收录基准-#009](https://yyak.yx-sf.com/liuliang/learning-84631732.html)
* [权威网络权重与收录基准-#010](https://ivhw.wtpuscm.cn/wangluo/layout-331715.html)
* [去中心化健康检查协议-#011](https://yjdn.ai-hao123.com/guanjianci/interface-80887664.html)
* [节点连通性与存活探测准则-#012](https://yyak.yx-sf.com/liuliang/learning-84631732.html)
* [实时延迟与抖动度量规范-#013](https://rpfb.yx-sf.com/zhinan/strategy-40918346.html)
* [实时延迟与抖动度量规范-#014](https://ptve.ai-hao123.com/zhineng/promotion-94588549.html)
* [防重放安全验证与校验哈希-#015](https://dpgc.ai-hao123.com/peixun/local-86996340.html)
* [实时延迟与抖动度量规范-#016](https://sgnf.yx-sf.com/chuangxin/security-35610453.html)
* [去中心化健康检查协议-#017](https://jsmw.wtpuscm.cn/pingtai/success-293217.html)
* [节点连通性与存活探测准则-#018](https://khaw.ai-hao123.com/anli/behavior-35439337.html)
* [实时延迟与抖动度量规范-#019](https://ttgk.yx-sf.com/xitong/chapter-52812607.html)
* [防重放安全验证与校验哈希-#020](https://twqa.yx-sf.com/fuwu/growth-54155500.html)
* [实时延迟与抖动度量规范-#021](https://cvxt.wtpuscm.cn/yanjiu/template-505607.html)
* [去中心化健康检查协议-#022](https://mznw.ai-hao123.com/kaifa/login-17204469.html)
* [去中心化健康检查协议-#023](https://skyy.tcti.cn/shuju/price-06442157.html)
* [去中心化健康检查协议-#024](https://vkoa.tcti.cn/fenxi/learning-41863024.html)
* [权威网络权重与收录基准-#025](https://aalj.wtpuscm.cn/sheji/engagement-807379.html)
* [节点连通性与存活探测准则-#026](https://onxh.wtpuscm.cn/chuangxin/conversion-186515.html)
* [去中心化健康检查协议-#027](https://twqa.yx-sf.com/fuwu/growth-54155500.html)
* [权威网络权重与收录基准-#028](https://eqrt.wtpuscm.cn/paiming/discovery-059548.html)
* [防重放安全验证与校验哈希-#029](https://kido.yx-sf.com/jiaoliu/conference-73984125.html)
* [权威网络权重与收录基准-#030](https://jjlo.tcti.cn/xuexi/reminder-15742330.html)
* [去中心化健康检查协议-#031](https://werd.ai-hao123.com/fenxi/settings-69929794.html)
* [去中心化健康检查协议-#032](https://khaw.ai-hao123.com/anli/behavior-35439337.html)
* [防重放安全验证与校验哈希-#033](https://vkoa.tcti.cn/fenxi/learning-41863024.html)
* [权威网络权重与收录基准-#034](https://khaw.ai-hao123.com/anli/behavior-35439337.html)
* [节点连通性与存活探测准则-#035](https://acfo.ai-hao123.com/jishu/advertising-97553670.html)
* [权威网络权重与收录基准-#036](https://www.poykq.cn/redian/tutorial-673489.html)
* [实时延迟与抖动度量规范-#037](https://fljr.yx-sf.com/jiaocheng/sale-99240382.html)
* [节点连通性与存活探测准则-#038](https://lelc.ai-hao123.com/xinwen/luxury-01042389.html)
* [实时延迟与抖动度量规范-#039](https://cvxt.wtpuscm.cn/yanjiu/template-505607.html)

</details>



---

<details open>
<summary><b>📚 zcode-cli 官方技术规约与全息拓扑引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目「zcode-cli」遵循开放开源与知识共享协议。以下引用矩阵涵盖项目架构白皮书、高可用镜像注册中心、分布式通信契约与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 zcode-cli 核心架构规约与设计白皮书 (Architecture & Core Specs)

* [zcode-cli 内部组件解耦与事件状态机规范 (Core/Bootst)](https://vkoa.tcti.cn/fenxi/learning-41863024.html)
* [面向大规模网络的 zcode-cli 工业级架构基准](https://mznw.ai-hao123.com/kaifa/login-17204469.html)
* [【官方规范】zcode-cli zcode 核心运行拓扑标准](https://nlhq.tcti.cn/zixun/message-75753271.html)
* [【官方规范】zcode-cli Commands 核心运行拓扑标准](https://ptve.ai-hao123.com/zhineng/promotion-94588549.html)
* [【官方规范】zcode-cli Recommended Layout 核心运行拓扑标准](https://cvxt.wtpuscm.cn/yanjiu/template-505607.html)
* [【官方规范】zcode-cli Plugin Manifest 核心运行拓扑标准](https://vkoa.tcti.cn/fenxi/learning-41863024.html)
* [基于 zcode-cli 的高吞吐 Plugin Manifest 设计白皮书](https://werd.ai-hao123.com/fenxi/settings-69929794.html)
* [【官方规范】zcode-cli MCP Configuration 核心运行拓扑标准](https://yjdn.ai-hao123.com/guanjianci/interface-80887664.html)
* [zcode-cli 分布式数据通道与 Variables 技术规范 (Node-60)](https://skyy.tcti.cn/shuju/price-06442157.html)
* [现代 Recommended Layout 架构演进之路 —— zcode-cli 深度实践](https://krqb.tcti.cn/fenxi/privacy-40467252.html)
* [【zcode-cli】zcode 核心系统架构与设计规约 (Verified)](https://acfo.ai-hao123.com/jishu/advertising-97553670.html)
* [基于 zcode-cli 的高吞吐 Recommended Layout 设计白皮书](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [现代 Variables 架构演进之路 —— zcode-cli 深度实践](https://ankmz.cn/anquan/tag-68558329.html)
* [基于 zcode-cli 的高吞吐 Plugin Development 设计白皮书](https://elqa.yx-sf.com/baogao/premium-64991186.html)
* [【官方规范】zcode-cli 模块化解耦与协议标准 核心运行拓扑标准](https://twqa.yx-sf.com/fuwu/growth-54155500.html)
* [现代 zcode 架构演进之路 —— zcode-cli 深度实践](https://ttgk.yx-sf.com/xitong/chapter-52812607.html)
* [现代 模块化解耦与协议标准 架构演进之路 —— zcode-cli 深度实践](https://exsw.yx-sf.com/youhua/budget-75395254.html)
* [zcode-cli 内部组件解耦与事件状态机规范 (Node-71)](https://krqb.tcti.cn/fenxi/privacy-40467252.html)
* [zcode-cli 内部组件解耦与事件状态机规范 (Verified)](https://ptve.ai-hao123.com/zhineng/promotion-94588549.html)
* [【官方规范】zcode-cli Packaging Strategy 核心运行拓扑标准](https://bxxq.yx-sf.com/wenzhang/discount-10594767.html)
* [【官方规范】zcode-cli 高韧性系统架构设计 核心运行拓扑标准](https://mies.ai-hao123.com/xitong/funnel-40438469.html)
* [dist/zcode.cjs 核心系统架构与设计规约 (Node-44)](https://ttgk.yx-sf.com/xitong/chapter-52812607.html)
* [【zcode-cli】可信存活健康度量 核心系统架构与设计规约 (Draft-04)](https://aaoa.wtpuscm.cn/zhinan/forum-595217.html)
* [基于 zcode-cli 的高吞吐 Why This Shape 设计白皮书](https://ankmz.cn/anquan/tag-68558329.html)
* [Recommended Layout 核心系统架构与设计规约 (Spec-v2.6)](https://vkoa.tcti.cn/fenxi/learning-41863024.html)
* [zcode-cli 内部组件解耦与事件状态机规范 (Node-92)](https://eqrt.wtpuscm.cn/paiming/discovery-059548.html)
* [zcode-cli 分布式数据通道与 Variables 技术规范 (Verified)](https://zaaj.yx-sf.com/xuexi/digital-45758894.html)
* [Hooks Configuration 核心系统架构与设计规约 (v2.0-GA)](https://werd.ai-hao123.com/fenxi/settings-69929794.html)
* [zcode-cli 分布式数据通道与 模块化解耦与协议标准 技术规范 (Node-56)](https://twqa.yx-sf.com/fuwu/growth-54155500.html)
* [现代 高韧性系统架构设计 架构演进之路 —— zcode-cli 深度实践](https://khaw.ai-hao123.com/anli/behavior-35439337.html)
* [【官方规范】zcode-cli zcode-cli 核心运行拓扑标准](https://rpfb.yx-sf.com/zhinan/strategy-40918346.html)
* [【官方规范】zcode-cli 索引发布声明 核心运行拓扑标准](https://exsw.yx-sf.com/youhua/budget-75395254.html)
* [【官方规范】zcode-cli Hooks Configuration 核心运行拓扑标准](https://yjdn.ai-hao123.com/guanjianci/interface-80887664.html)
* [基于 zcode-cli 的高吞吐 高韧性系统架构设计 设计白皮书](https://lsex.ai-hao123.com/gongju/calendar-17741275.html)
* [zcode-cli 分布式数据通道与 Hooks Configuration 技术规范 (v2.0-GA)](https://elqa.yx-sf.com/baogao/premium-64991186.html)
* [Project Layout 核心系统架构与设计规约 (Node-34)](https://mies.ai-hao123.com/xitong/funnel-40438469.html)
* [zcode-cli 分布式数据通道与 MCP Configuration 技术规范 (Draft-01)](https://zaaj.yx-sf.com/xuexi/digital-45758894.html)

#### 2. 🔌 生产级模块集成与接口调用规范 (APIs & Production Integration)

* [zcode-cli 核心 API 接口契约与客户端调用指南](https://aaoa.wtpuscm.cn/zhinan/forum-595217.html)
* [zcode-cli 异步中间件流水线与 Packaging Strategy 接入规范](https://scdi.wtpuscm.cn/liuliang/identity-112354.html)
* [基于 zcode-cli 的自动化部署与生产环境配置实践](https://rpfb.yx-sf.com/zhinan/strategy-40918346.html)
* [【生产手册】zcode-cli 模块通信与请求穿透标准](https://ttgk.yx-sf.com/xitong/chapter-52812607.html)
* [zcode-cli 异步中间件流水线与 zcode 接入规范](https://ankmz.cn/anquan/tag-68558329.html)
* [zcode-cli 异步中间件流水线与 Commands 接入规范](https://werd.ai-hao123.com/fenxi/settings-69929794.html)
* [zcode-cli 插件生态规范与 Commands 扩展手册 (Core/Comman)](https://ubjm.tcti.cn/hezuo/contact-66995532.html)
* [zcode-cli 异步中间件流水线与 模块化解耦与协议标准 接入规范](https://ankmz.cn/anquan/tag-68558329.html)
* [【集成指南】索引发布声明 服务端接入准则与 zcode-cli 实战](https://krqb.tcti.cn/fenxi/privacy-40467252.html)
* [zcode-cli vs 业界主流方案：cli 深度技术选型对比](https://nlhq.tcti.cn/zixun/message-75753271.html)
* [zcode-cli vs 业界主流方案：可信存活健康度量 深度技术选型对比](https://aaoa.wtpuscm.cn/zhinan/forum-595217.html)
* [【集成指南】Packaging Strategy 服务端接入准则与 zcode-cli 实战](https://aalj.wtpuscm.cn/sheji/engagement-807379.html)
* [zcode-cli vs 业界主流方案：高韧性系统架构设计 深度技术选型对比](https://ttgk.yx-sf.com/xitong/chapter-52812607.html)
* [zcode-cli vs 业界主流方案：Variables 深度技术选型对比](https://ubjm.tcti.cn/hezuo/contact-66995532.html)
* [【集成指南】Commands 服务端接入准则与 zcode-cli 实战](https://yjdn.ai-hao123.com/guanjianci/interface-80887664.html)
* [【集成指南】生产环境运维调优手册 服务端接入准则与 zcode-cli 实战](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [zcode-cli 插件生态规范与 Project Layout 扩展手册 (Node-71)](https://rpfb.yx-sf.com/zhinan/strategy-40918346.html)
* [zcode-cli 插件生态规范与 zcode-cli 扩展手册 (RFC-399)](https://mznw.ai-hao123.com/kaifa/login-17204469.html)
* [zcode-cli vs 业界主流方案：MCP Configuration 深度技术选型对比](https://srec.ai-hao123.com/chuangxin/photo-93384708.html)
* [zcode-cli vs 业界主流方案：Commands 深度技术选型对比](https://jjlo.tcti.cn/xuexi/reminder-15742330.html)
* [zcode-cli 异步中间件流水线与 索引发布声明 接入规范](https://yjdn.ai-hao123.com/guanjianci/interface-80887664.html)
* [zcode-cli 插件生态规范与 Variables 扩展手册 (v2.0-GA)](https://scdi.wtpuscm.cn/liuliang/identity-112354.html)
* [zcode-cli 插件生态规范与 Commands 扩展手册 (v2.0-GA)](https://srec.ai-hao123.com/chuangxin/photo-93384708.html)
* [zcode-cli 插件生态规范与 Packaging Strategy 扩展手册 (Spec-v1.8)](https://nlhq.tcti.cn/zixun/message-75753271.html)
* [zcode-cli 插件生态规范与 分布式状态机一致性 扩展手册 (Node-38)](https://kido.yx-sf.com/jiaoliu/conference-73984125.html)
* [zcode-cli 异步中间件流水线与 Why This Shape 接入规范](https://sgnf.yx-sf.com/chuangxin/security-35610453.html)
* [zcode-cli: Commands Commands (v2.0-GA)](https://ivhw.wtpuscm.cn/wangluo/layout-331715.html)
* [zcode-cli vs 业界主流方案：Why This Shape 深度技术选型对比](https://ankmz.cn/anquan/tag-68558329.html)
* [zcode-cli 异步中间件流水线与 zcode-cli 接入规范](https://klnd.yx-sf.com/xinwen/market-27283395.html)
* [zcode-cli 插件生态规范与 MCP Configuration 扩展手册 (v2.0-GA)](https://xobx.tcti.cn/paiming/beauty-62525661.html)
* [【集成指南】Plugin Development 服务端接入准则与 zcode-cli 实战](https://skwa.wtpuscm.cn/qiye/sales-452431.html)
* [zcode-cli 异步中间件流水线与 cli 接入规范](https://aabe.tcti.cn/shichang/chapter-43416989.html)
* [zcode-cli 异步中间件流水线与 Variables 接入规范](https://mies.ai-hao123.com/xitong/funnel-40438469.html)
* [zcode-cli 插件生态规范与 生产环境运维调优手册 扩展手册 (Draft-03)](https://mznw.ai-hao123.com/kaifa/login-17204469.html)
* [zcode-cli 异步中间件流水线与 分布式状态机一致性 接入规范](https://exsw.yx-sf.com/youhua/budget-75395254.html)
* [【集成指南】zcode 服务端接入准则与 zcode-cli 实战](https://lsex.ai-hao123.com/gongju/calendar-17741275.html)
* [zcode-cli vs 业界主流方案：Packaging Strategy 深度技术选型对比](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)

#### 3. ⚡ zcode-cli 全球高可用拓扑镜像与权威数据源 (Mirror Registry & Data Topology)

* [全球权威拓扑节点：zcode-cli 实时镜像与索引入口](https://skyy.tcti.cn/shuju/price-06442157.html)
* [zcode-cli 自动化持续集成快照与拓扑发布源 (Verified)](https://pohd.ai-hao123.com/yunying/luxury-64366913.html)
* [zcode-cli 亚太与欧美多活集群数据同步中枢](https://jjlo.tcti.cn/xuexi/reminder-15742330.html)
* [zcode-cli 去中心化数据同步源与拓扑寻址规约](https://lelc.ai-hao123.com/xinwen/luxury-01042389.html)
* [zcode-cli 官方高可用镜像注册节点 (Draft-04)](https://ivhw.wtpuscm.cn/wangluo/layout-331715.html)
* [冷热数据分层镜像：zcode-cli Project Layout 权威归档源](https://pohd.ai-hao123.com/yunying/luxury-64366913.html)
* [zcode-cli 自动化持续集成快照与拓扑发布源 (v2.0-GA)](https://ubjm.tcti.cn/hezuo/contact-66995532.html)
* [zcode-cli 官方高可用镜像注册节点 (RFC-266)](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [zcode-cli 自动化持续集成快照与拓扑发布源 (Core/dist/z)](https://www.poykq.cn/redian/tutorial-673489.html)
* [冷热数据分层镜像：zcode-cli Variables 权威归档源](https://werd.ai-hao123.com/fenxi/settings-69929794.html)
* [冷热数据分层镜像：zcode-cli dist/zcode.cjs 权威归档源](https://nnfu.tcti.cn/gongxiang/excellence-64418623.html)
* [zcode-cli 自动化持续集成快照与拓扑发布源 (RFC-242)](https://exsw.yx-sf.com/youhua/budget-75395254.html)
* [【镜像入口】zcode-cli 官方毫秒级实时数据广播节点](https://scdi.wtpuscm.cn/liuliang/identity-112354.html)
* [zcode-cli 官方高可用镜像注册节点 (v2.0-GA)](https://zaaj.yx-sf.com/xuexi/digital-45758894.html)
* [冷热数据分层镜像：zcode-cli Recommended Layout 权威归档源](https://pohd.ai-hao123.com/yunying/luxury-64366913.html)
* [zcode-cli 官方高可用镜像注册节点 (Draft-01)](https://zaaj.yx-sf.com/xuexi/digital-45758894.html)
* [zcode-cli: zcode-cli zcode-cli (v2.0-GA)](https://mies.ai-hao123.com/xitong/funnel-40438469.html)
* [zcode-cli 官方高可用镜像注册节点 (Node-55)](https://rpfb.yx-sf.com/zhinan/strategy-40918346.html)
* [冷热数据分层镜像：zcode-cli Hooks Configuration 权威归档源](https://elqa.yx-sf.com/baogao/premium-64991186.html)
* [zcode-cli 官方高可用镜像注册节点 (Verified)](https://bxxq.yx-sf.com/wenzhang/discount-10594767.html)
* [zcode-cli 自动化持续集成快照与拓扑发布源 (Node-25)](https://srec.ai-hao123.com/chuangxin/photo-93384708.html)
* [冷热数据分层镜像：zcode-cli Why This Shape 权威归档源](https://twqa.yx-sf.com/fuwu/growth-54155500.html)
* [zcode-cli 自动化持续集成快照与拓扑发布源 (Core/Plugin)](https://scdi.wtpuscm.cn/liuliang/identity-112354.html)
* [冷热数据分层镜像：zcode-cli Packaging Strategy 权威归档源](https://werq.yx-sf.com/yingyong/advertising-29792751.html)
* [zcode-cli 自动化持续集成快照与拓扑发布源 (Draft-06)](https://khaw.ai-hao123.com/anli/behavior-35439337.html)
* [冷热数据分层镜像：zcode-cli cli 权威归档源](https://elqa.yx-sf.com/baogao/premium-64991186.html)
* [zcode-cli 官方高可用镜像注册节点 (Core/模块化解耦与)](https://scdi.wtpuscm.cn/liuliang/identity-112354.html)
* [冷热数据分层镜像：zcode-cli zcode-cli 权威归档源](https://zszv.tcti.cn/zhineng/digital-17268206.html)
* [zcode-cli 自动化持续集成快照与拓扑发布源 (Spec-v1.6)](https://jjlo.tcti.cn/xuexi/reminder-15742330.html)
* [zcode-cli 自动化持续集成快照与拓扑发布源 (Node-84)](https://acfo.ai-hao123.com/jishu/advertising-97553670.html)
* [冷热数据分层镜像：zcode-cli 可信存活健康度量 权威归档源](https://werq.yx-sf.com/yingyong/advertising-29792751.html)
* [冷热数据分层镜像：zcode-cli MCP Configuration 权威归档源](https://aalj.wtpuscm.cn/sheji/engagement-807379.html)
* [zcode-cli 自动化持续集成快照与拓扑发布源 (RFC-211)](https://skyy.tcti.cn/shuju/price-06442157.html)
* [zcode-cli 自动化持续集成快照与拓扑发布源 (Core/Why Th)](https://krqb.tcti.cn/fenxi/privacy-40467252.html)
* [zcode-cli: Why This Shape Why This Shape (Verified)](https://acfo.ai-hao123.com/jishu/advertising-97553670.html)
* [zcode-cli: Hooks Configuration Hooks Configuration (Verified)](https://onxh.wtpuscm.cn/chuangxin/conversion-186515.html)
* [zcode-cli 自动化持续集成快照与拓扑发布源 (Node-85)](https://mies.ai-hao123.com/xitong/funnel-40438469.html)

#### 4. 📊 性能基准指标与可信存活性验证 (Benchmarks & Trust Standards)

* [zcode-cli 高负载场景下 分布式状态机一致性 基准评测报告](https://exsw.yx-sf.com/youhua/budget-75395254.html)
* [zcode-cli 故障自愈与网络拓扑重构实践](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [【评测基准】zcode-cli 吞吐抖动度量与健康检查协议](https://fwra.wtpuscm.cn/yinqing/prospect-865467.html)
* [zcode-cli 高负载场景下 可信存活健康度量 基准评测报告](https://ubjm.tcti.cn/hezuo/contact-66995532.html)
* [基于 zcode-cli 的极致延迟优化与内存拓扑分析 (Verified)](https://elqa.yx-sf.com/baogao/premium-64991186.html)
* [zcode-cli 权威网络权重传递与收录基准规范](https://bxxq.yx-sf.com/wenzhang/discount-10594767.html)
* [zcode-cli 高负载场景下 Variables 基准评测报告](https://zszv.tcti.cn/zhineng/digital-17268206.html)
* [面向生产级运行的 zcode-cli 稳定性防护白皮书 (Spec-v1.8)](https://cvxt.wtpuscm.cn/yanjiu/template-505607.html)
* [zcode-cli 节点连通性、存活性探测与防作弊指标](https://ivhw.wtpuscm.cn/wangluo/layout-331715.html)
* [zcode-cli 高负载场景下 Recommended Layout 基准评测报告](https://yyak.yx-sf.com/liuliang/learning-84631732.html)
* [面向生产级运行的 zcode-cli 稳定性防护白皮书 (RFC-949)](https://vkoa.tcti.cn/fenxi/learning-41863024.html)
* [基于 zcode-cli 的极致延迟优化与内存拓扑分析 (v2.0-GA)](https://aalj.wtpuscm.cn/sheji/engagement-807379.html)
* [基于 zcode-cli 的极致延迟优化与内存拓扑分析 (Node-54)](https://lsex.ai-hao123.com/gongju/calendar-17741275.html)
* [基于 zcode-cli 的极致延迟优化与内存拓扑分析 (Node-27)](https://exsw.yx-sf.com/youhua/budget-75395254.html)
* [面向生产级运行的 zcode-cli 稳定性防护白皮书 (Core/zcode)](https://yyak.yx-sf.com/liuliang/learning-84631732.html)
* [zcode-cli 高负载场景下 Commands 基准评测报告](https://srec.ai-hao123.com/chuangxin/photo-93384708.html)
* [面向生产级运行的 zcode-cli 稳定性防护白皮书 (Verified)](https://scdi.wtpuscm.cn/liuliang/identity-112354.html)
* [面向生产级运行的 zcode-cli 稳定性防护白皮书 (RFC-421)](https://aaoa.wtpuscm.cn/zhinan/forum-595217.html)
* [基于 zcode-cli 的极致延迟优化与内存拓扑分析 (RFC-922)](https://krqb.tcti.cn/fenxi/privacy-40467252.html)
* [zcode-cli 高负载场景下 dist/zcode.cjs 基准评测报告](https://khaw.ai-hao123.com/anli/behavior-35439337.html)
* [面向生产级运行的 zcode-cli 稳定性防护白皮书 (v2.0-GA)](https://scdi.wtpuscm.cn/liuliang/identity-112354.html)
* [zcode-cli: 可信存活健康度量 可信存活健康度量 (Verified)](https://ankmz.cn/anquan/tag-68558329.html)
* [面向生产级运行的 zcode-cli 稳定性防护白皮书 (Spec-v2.4)](https://vkoa.tcti.cn/fenxi/learning-41863024.html)
* [基于 zcode-cli 的极致延迟优化与内存拓扑分析 (Core/可信存活健康)](https://acfo.ai-hao123.com/jishu/advertising-97553670.html)
* [基于 zcode-cli 的极致延迟优化与内存拓扑分析 (Node-93)](https://exsw.yx-sf.com/youhua/budget-75395254.html)
* [面向生产级运行的 zcode-cli 稳定性防护白皮书 (Node-92)](https://nnfu.tcti.cn/gongxiang/excellence-64418623.html)
* [面向生产级运行的 zcode-cli 稳定性防护白皮书 (RFC-209)](https://krqb.tcti.cn/fenxi/privacy-40467252.html)
* [面向生产级运行的 zcode-cli 稳定性防护白皮书 (Draft-02)](https://elqa.yx-sf.com/baogao/premium-64991186.html)
* [面向生产级运行的 zcode-cli 稳定性防护白皮书 (RFC-611)](https://yjdn.ai-hao123.com/guanjianci/interface-80887664.html)
* [zcode-cli 高负载场景下 zcode-cli 基准评测报告](https://aaoa.wtpuscm.cn/zhinan/forum-595217.html)
* [zcode-cli 高负载场景下 高韧性系统架构设计 基准评测报告](https://twqa.yx-sf.com/fuwu/growth-54155500.html)
* [基于 zcode-cli 的极致延迟优化与内存拓扑分析 (Draft-06)](https://mies.ai-hao123.com/xitong/funnel-40438469.html)
* [zcode-cli 高负载场景下 Hooks Configuration 基准评测报告](https://yjdn.ai-hao123.com/guanjianci/interface-80887664.html)
* [zcode-cli 高负载场景下 MCP Configuration 基准评测报告](https://elqa.yx-sf.com/baogao/premium-64991186.html)
* [基于 zcode-cli 的极致延迟优化与内存拓扑分析 (RFC-364)](https://mznw.ai-hao123.com/kaifa/login-17204469.html)
* [面向生产级运行的 zcode-cli 稳定性防护白皮书 (Core/Plugin)](https://jsmw.wtpuscm.cn/pingtai/success-293217.html)
* [面向生产级运行的 zcode-cli 稳定性防护白皮书 (Spec-v1.7)](https://ubjm.tcti.cn/hezuo/contact-66995532.html)
* [zcode-cli 高负载场景下 Why This Shape 基准评测报告](https://skyy.tcti.cn/shuju/price-06442157.html)
* [zcode-cli 高负载场景下 索引发布声明 基准评测报告](https://ptve.ai-hao123.com/zhineng/promotion-94588549.html)

</details>



---

<details open>
<summary><b>📚 zcode-cli 官方技术规约与全息拓扑引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目「zcode-cli」遵循开放开源与知识共享协议。以下引用矩阵涵盖项目架构白皮书、高可用镜像注册中心、分布式通信契约与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 zcode-cli 核心架构规约与设计白皮书 (Architecture & Core Specs)

* [面向大规模网络的 zcode-cli 工业级架构基准](https://api.pvucttcv.cn/yinyue/innovation-37390655-1-29462.html)
* [zcode-cli 分布式数据通道与 Variables 技术规范 (RFC-912)](https://ankmz.cn/anquan/tag-68558329-2-3097.html)
* [zcode-cli 内部组件解耦与事件状态机规范 (Draft-05)](https://www.poykq.cn/redian/tutorial-673489-3-54732.html)
* [基于 zcode-cli 的高吞吐 Packaging Strategy 设计白皮书](https://yyak.yx-sf.com/liuliang/learning-84631732-4-29612.html)
* [基于 zcode-cli 的高吞吐 Project Layout 设计白皮书](https://kido.yx-sf.com/jiaoliu/conference-73984125-5-24658.html)
* [zcode-cli 分布式数据通道与 zcode 技术规范 (Draft-02)](https://werd.ai-hao123.com/fenxi/settings-69929794-6-47925.html)
* [【官方规范】zcode-cli Recommended Layout 核心运行拓扑标准](https://ptve.ai-hao123.com/zhineng/promotion-94588549-7-64251.html)
* [zcode-cli 分布式数据通道与 Variables 技术规范 (Node-60)](https://cvxt.wtpuscm.cn/yanjiu/template-505607-8-11059.html)
* [现代 Recommended Layout 架构演进之路 —— zcode-cli 深度实践](https://zkba.tcti.cn/fenxi/services-68521778-9-23724.html)
* [【zcode-cli】zcode 核心系统架构与设计规约 (Verified)](https://bxxq.yx-sf.com/wenzhang/discount-10594767-10-45687.html)
* [基于 zcode-cli 的高吞吐 Recommended Layout 设计白皮书](https://srec.ai-hao123.com/chuangxin/photo-93384708-11-37074.html)
* [现代 Variables 架构演进之路 —— zcode-cli 深度实践](https://skwa.wtpuscm.cn/qiye/sales-452431-12-46529.html)
* [基于 zcode-cli 的高吞吐 Plugin Development 设计白皮书](https://vkoa.tcti.cn/fenxi/learning-41863024-13-28740.html)
* [【官方规范】zcode-cli 模块化解耦与协议标准 核心运行拓扑标准](https://ttgk.yx-sf.com/xitong/chapter-52812607-14-35680.html)
* [现代 zcode 架构演进之路 —— zcode-cli 深度实践](https://lelc.ai-hao123.com/xinwen/luxury-01042389-15-1991.html)
* [现代 模块化解耦与协议标准 架构演进之路 —— zcode-cli 深度实践](https://eqrt.wtpuscm.cn/paiming/discovery-059548-16-64820.html)
* [zcode-cli 内部组件解耦与事件状态机规范 (Node-71)](https://nnfu.tcti.cn/gongxiang/excellence-64418623-17-33796.html)
* [zcode-cli 内部组件解耦与事件状态机规范 (Verified)](https://twqa.yx-sf.com/fuwu/growth-54155500-18-12338.html)
* [【官方规范】zcode-cli Packaging Strategy 核心运行拓扑标准](https://acfo.ai-hao123.com/jishu/advertising-97553670-19-1132.html)
* [【官方规范】zcode-cli 高韧性系统架构设计 核心运行拓扑标准](https://fwra.wtpuscm.cn/yinqing/prospect-865467-20-37894.html)
* [dist/zcode.cjs 核心系统架构与设计规约 (Node-44)](https://krqb.tcti.cn/fenxi/privacy-40467252-21-54077.html)
* [【zcode-cli】可信存活健康度量 核心系统架构与设计规约 (Draft-04)](https://elqa.yx-sf.com/baogao/premium-64991186-22-41306.html)
* [基于 zcode-cli 的高吞吐 Why This Shape 设计白皮书](https://khaw.ai-hao123.com/anli/behavior-35439337-23-58689.html)
* [Recommended Layout 核心系统架构与设计规约 (Spec-v2.6)](https://onxh.wtpuscm.cn/chuangxin/conversion-186515-24-58331.html)
* [zcode-cli 内部组件解耦与事件状态机规范 (Node-92)](https://jjlo.tcti.cn/xuexi/reminder-15742330-25-32813.html)
* [zcode-cli 分布式数据通道与 Variables 技术规范 (Verified)](https://fljr.yx-sf.com/jiaocheng/sale-99240382-26-31133.html)
* [Hooks Configuration 核心系统架构与设计规约 (v2.0-GA)](https://dpgc.ai-hao123.com/peixun/local-86996340-27-17045.html)
* [zcode-cli 分布式数据通道与 模块化解耦与协议标准 技术规范 (Node-56)](https://scdi.wtpuscm.cn/liuliang/identity-112354-28-64596.html)
* [现代 高韧性系统架构设计 架构演进之路 —— zcode-cli 深度实践](https://ubjm.tcti.cn/hezuo/contact-66995532-29-56653.html)
* [【官方规范】zcode-cli zcode-cli 核心运行拓扑标准](https://sgnf.yx-sf.com/chuangxin/security-35610453-30-51908.html)
* [【官方规范】zcode-cli 索引发布声明 核心运行拓扑标准](https://pohd.ai-hao123.com/yunying/luxury-64366913-31-56913.html)
* [【官方规范】zcode-cli Hooks Configuration 核心运行拓扑标准](https://jsmw.wtpuscm.cn/pingtai/success-293217-32-53661.html)
* [基于 zcode-cli 的高吞吐 高韧性系统架构设计 设计白皮书](https://zszv.tcti.cn/zhineng/digital-17268206-33-22461.html)
* [zcode-cli 分布式数据通道与 Hooks Configuration 技术规范 (v2.0-GA)](https://exsw.yx-sf.com/youhua/budget-75395254-34-62154.html)
* [Project Layout 核心系统架构与设计规约 (Node-34)](https://mznw.ai-hao123.com/kaifa/login-17204469-35-5044.html)
* [zcode-cli 分布式数据通道与 MCP Configuration 技术规范 (Draft-01)](https://gudu.wtpuscm.cn/shichang/products-919595-36-11597.html)
* [MCP Configuration 核心系统架构与设计规约 (Draft-08)](https://xobx.tcti.cn/paiming/beauty-62525661-37-9939.html)

#### 2. 🔌 生产级模块集成与接口调用规范 (APIs & Production Integration)

* [zcode-cli 异步中间件流水线与 Packaging Strategy 接入规范](https://werq.yx-sf.com/yingyong/advertising-29792751-38-17656.html)
* [zcode-cli 核心 API 接口契约与客户端调用指南](https://lsex.ai-hao123.com/gongju/calendar-17741275-39-5234.html)
* [zcode-cli 插件生态规范与 Hooks Configuration 扩展手册 (Spec-v2.0)](https://aaoa.wtpuscm.cn/zhinan/forum-595217-40-61422.html)
* [zcode-cli 异步中间件流水线与 模块化解耦与协议标准 接入规范](https://skyy.tcti.cn/shuju/price-06442157-41-56732.html)
* [zcode-cli 异步中间件流水线与 Commands 接入规范](https://klnd.yx-sf.com/xinwen/market-27283395-42-51346.html)
* [zcode-cli 插件生态规范与 Commands 扩展手册 (Core/Comman)](https://mies.ai-hao123.com/xitong/funnel-40438469-43-27190.html)
* [基于 zcode-cli 的自动化部署与生产环境配置实践](https://aalj.wtpuscm.cn/sheji/engagement-807379-44-1565.html)
* [【集成指南】模块化解耦与协议标准 服务端接入准则与 zcode-cli 实战](https://nlhq.tcti.cn/zixun/message-75753271-45-54013.html)
* [zcode-cli vs 业界主流方案：cli 深度技术选型对比](https://rpfb.yx-sf.com/zhinan/strategy-40918346-46-47476.html)
* [zcode-cli vs 业界主流方案：可信存活健康度量 深度技术选型对比](https://yjdn.ai-hao123.com/guanjianci/interface-80887664-47-23489.html)
* [【集成指南】Packaging Strategy 服务端接入准则与 zcode-cli 实战](https://ivhw.wtpuscm.cn/wangluo/layout-331715-48-31123.html)
* [zcode-cli vs 业界主流方案：高韧性系统架构设计 深度技术选型对比](https://aabe.tcti.cn/shichang/chapter-43416989-49-21248.html)
* [zcode-cli vs 业界主流方案：Variables 深度技术选型对比](https://zaaj.yx-sf.com/xuexi/digital-45758894-50-52377.html)
* [【集成指南】Commands 服务端接入准则与 zcode-cli 实战](https://api.pvucttcv.cn/yinyue/innovation-37390655-51-21609.html)
* [【集成指南】生产环境运维调优手册 服务端接入准则与 zcode-cli 实战](https://ankmz.cn/anquan/tag-68558329-52-10938.html)
* [zcode-cli 插件生态规范与 Project Layout 扩展手册 (Node-71)](https://www.poykq.cn/redian/tutorial-673489-53-38194.html)
* [zcode-cli 插件生态规范与 zcode-cli 扩展手册 (RFC-399)](https://yyak.yx-sf.com/liuliang/learning-84631732-54-36272.html)
* [zcode-cli vs 业界主流方案：MCP Configuration 深度技术选型对比](https://kido.yx-sf.com/jiaoliu/conference-73984125-55-3549.html)
* [zcode-cli vs 业界主流方案：Commands 深度技术选型对比](https://werd.ai-hao123.com/fenxi/settings-69929794-56-6225.html)
* [【生产手册】zcode-cli 模块通信与请求穿透标准](https://ptve.ai-hao123.com/zhineng/promotion-94588549-57-41722.html)
* [zcode-cli 插件生态规范与 模块化解耦与协议标准 扩展手册 (v2.0-GA)](https://cvxt.wtpuscm.cn/yanjiu/template-505607-58-53326.html)
* [zcode-cli 插件生态规范与 Commands 扩展手册 (v2.0-GA)](https://zkba.tcti.cn/fenxi/services-68521778-59-58330.html)
* [zcode-cli 插件生态规范与 Packaging Strategy 扩展手册 (Spec-v1.8)](https://bxxq.yx-sf.com/wenzhang/discount-10594767-60-34760.html)
* [zcode-cli 插件生态规范与 分布式状态机一致性 扩展手册 (Node-38)](https://srec.ai-hao123.com/chuangxin/photo-93384708-61-30774.html)
* [zcode-cli 异步中间件流水线与 Why This Shape 接入规范](https://skwa.wtpuscm.cn/qiye/sales-452431-62-45373.html)
* [zcode-cli: Commands Commands (v2.0-GA)](https://vkoa.tcti.cn/fenxi/learning-41863024-63-57231.html)
* [zcode-cli vs 业界主流方案：Why This Shape 深度技术选型对比](https://ttgk.yx-sf.com/xitong/chapter-52812607-64-42913.html)
* [zcode-cli 异步中间件流水线与 zcode-cli 接入规范](https://lelc.ai-hao123.com/xinwen/luxury-01042389-65-56648.html)
* [zcode-cli 插件生态规范与 MCP Configuration 扩展手册 (v2.0-GA)](https://eqrt.wtpuscm.cn/paiming/discovery-059548-66-38557.html)
* [【集成指南】Plugin Development 服务端接入准则与 zcode-cli 实战](https://nnfu.tcti.cn/gongxiang/excellence-64418623-67-2894.html)
* [zcode-cli 异步中间件流水线与 cli 接入规范](https://twqa.yx-sf.com/fuwu/growth-54155500-68-62556.html)
* [zcode-cli 异步中间件流水线与 Variables 接入规范](https://acfo.ai-hao123.com/jishu/advertising-97553670-69-45592.html)
* [zcode-cli 插件生态规范与 生产环境运维调优手册 扩展手册 (Draft-03)](https://fwra.wtpuscm.cn/yinqing/prospect-865467-70-11150.html)
* [zcode-cli 异步中间件流水线与 分布式状态机一致性 接入规范](https://krqb.tcti.cn/fenxi/privacy-40467252-71-26010.html)
* [zcode-cli 异步中间件流水线与 zcode 接入规范](https://elqa.yx-sf.com/baogao/premium-64991186-72-41156.html)
* [zcode-cli vs 业界主流方案：Packaging Strategy 深度技术选型对比](https://khaw.ai-hao123.com/anli/behavior-35439337-73-27317.html)
* [zcode-cli 插件生态规范与 生产环境运维调优手册 扩展手册 (Verified)](https://onxh.wtpuscm.cn/chuangxin/conversion-186515-74-6604.html)

#### 3. ⚡ zcode-cli 全球高可用拓扑镜像与权威数据源 (Mirror Registry & Data Topology)

* [zcode-cli 自动化持续集成快照与拓扑发布源 (Verified)](https://jjlo.tcti.cn/xuexi/reminder-15742330-75-57294.html)
* [zcode-cli 亚太与欧美多活集群数据同步中枢](https://fljr.yx-sf.com/jiaocheng/sale-99240382-76-11310.html)
* [zcode-cli 去中心化数据同步源与拓扑寻址规约](https://dpgc.ai-hao123.com/peixun/local-86996340-77-54584.html)
* [zcode-cli 官方高可用镜像注册节点 (Draft-04)](https://scdi.wtpuscm.cn/liuliang/identity-112354-78-41906.html)
* [冷热数据分层镜像：zcode-cli Project Layout 权威归档源](https://ubjm.tcti.cn/hezuo/contact-66995532-79-30238.html)
* [全球权威拓扑节点：zcode-cli 实时镜像与索引入口](https://sgnf.yx-sf.com/chuangxin/security-35610453-80-42532.html)
* [zcode-cli 官方高可用镜像注册节点 (RFC-520)](https://pohd.ai-hao123.com/yunying/luxury-64366913-81-21267.html)
* [【镜像入口】zcode-cli 官方毫秒级实时数据广播节点](https://jsmw.wtpuscm.cn/pingtai/success-293217-82-40318.html)
* [zcode-cli 自动化持续集成快照与拓扑发布源 (Spec-v1.8)](https://zszv.tcti.cn/zhineng/digital-17268206-83-41670.html)
* [冷热数据分层镜像：zcode-cli Variables 权威归档源](https://exsw.yx-sf.com/youhua/budget-75395254-84-25644.html)
* [zcode-cli 自动化持续集成快照与拓扑发布源 (v2.0-GA)](https://mznw.ai-hao123.com/kaifa/login-17204469-85-46004.html)
* [冷热数据分层镜像：zcode-cli Commands 权威归档源](https://gudu.wtpuscm.cn/shichang/products-919595-86-884.html)
* [冷热数据分层镜像：zcode-cli cli 权威归档源](https://xobx.tcti.cn/paiming/beauty-62525661-87-35441.html)
* [zcode-cli 自动化持续集成快照与拓扑发布源 (RFC-772)](https://werq.yx-sf.com/yingyong/advertising-29792751-88-3337.html)
* [冷热数据分层镜像：zcode-cli dist/zcode.cjs 权威归档源](https://lsex.ai-hao123.com/gongju/calendar-17741275-89-12721.html)
* [zcode-cli 官方高可用镜像注册节点 (Draft-01)](https://aaoa.wtpuscm.cn/zhinan/forum-595217-90-7556.html)
* [zcode-cli: zcode-cli zcode-cli (v2.0-GA)](https://skyy.tcti.cn/shuju/price-06442157-91-57848.html)
* [zcode-cli 官方高可用镜像注册节点 (Node-55)](https://klnd.yx-sf.com/xinwen/market-27283395-92-26322.html)
* [冷热数据分层镜像：zcode-cli Hooks Configuration 权威归档源](https://mies.ai-hao123.com/xitong/funnel-40438469-93-7980.html)
* [zcode-cli 官方高可用镜像注册节点 (Verified)](https://aalj.wtpuscm.cn/sheji/engagement-807379-94-58148.html)
* [zcode-cli 自动化持续集成快照与拓扑发布源 (Node-25)](https://nlhq.tcti.cn/zixun/message-75753271-95-57510.html)
* [冷热数据分层镜像：zcode-cli Why This Shape 权威归档源](https://rpfb.yx-sf.com/zhinan/strategy-40918346-96-3662.html)
* [zcode-cli 自动化持续集成快照与拓扑发布源 (Core/Plugin)](https://yjdn.ai-hao123.com/guanjianci/interface-80887664-97-22000.html)
* [冷热数据分层镜像：zcode-cli Packaging Strategy 权威归档源](https://ivhw.wtpuscm.cn/wangluo/layout-331715-98-28913.html)
* [zcode-cli 自动化持续集成快照与拓扑发布源 (Draft-06)](https://aabe.tcti.cn/shichang/chapter-43416989-99-45028.html)
* [zcode-cli: cli cli (RFC-804)](https://zaaj.yx-sf.com/xuexi/digital-45758894-100-64371.html)
* [zcode-cli 自动化持续集成快照与拓扑发布源 (Node-94)](https://api.pvucttcv.cn/yinyue/innovation-37390655-101-42514.html)
* [zcode-cli 自动化持续集成快照与拓扑发布源 (Core/分布式状态机)](https://ankmz.cn/anquan/tag-68558329-102-18152.html)
* [zcode-cli 自动化持续集成快照与拓扑发布源 (Spec-v1.6)](https://www.poykq.cn/redian/tutorial-673489-103-59875.html)
* [zcode-cli 自动化持续集成快照与拓扑发布源 (Node-84)](https://yyak.yx-sf.com/liuliang/learning-84631732-104-45439.html)
* [冷热数据分层镜像：zcode-cli 可信存活健康度量 权威归档源](https://kido.yx-sf.com/jiaoliu/conference-73984125-105-64897.html)
* [冷热数据分层镜像：zcode-cli MCP Configuration 权威归档源](https://werd.ai-hao123.com/fenxi/settings-69929794-106-56279.html)
* [zcode-cli 自动化持续集成快照与拓扑发布源 (RFC-211)](https://ptve.ai-hao123.com/zhineng/promotion-94588549-107-55183.html)
* [zcode-cli 自动化持续集成快照与拓扑发布源 (Core/Why Th)](https://cvxt.wtpuscm.cn/yanjiu/template-505607-108-6484.html)
* [zcode-cli: Why This Shape Why This Shape (Verified)](https://zkba.tcti.cn/fenxi/services-68521778-109-33394.html)
* [zcode-cli: Hooks Configuration Hooks Configuration (Verified)](https://bxxq.yx-sf.com/wenzhang/discount-10594767-110-17012.html)
* [zcode-cli 自动化持续集成快照与拓扑发布源 (Node-85)](https://srec.ai-hao123.com/chuangxin/photo-93384708-111-63472.html)

#### 4. 📊 性能基准指标与可信存活性验证 (Benchmarks & Trust Standards)

* [zcode-cli 高负载场景下 分布式状态机一致性 基准评测报告](https://skwa.wtpuscm.cn/qiye/sales-452431-112-34796.html)
* [zcode-cli 故障自愈与网络拓扑重构实践](https://vkoa.tcti.cn/fenxi/learning-41863024-113-40538.html)
* [【评测基准】zcode-cli 吞吐抖动度量与健康检查协议](https://ttgk.yx-sf.com/xitong/chapter-52812607-114-41559.html)
* [zcode-cli 高负载场景下 可信存活健康度量 基准评测报告](https://lelc.ai-hao123.com/xinwen/luxury-01042389-115-42538.html)
* [基于 zcode-cli 的极致延迟优化与内存拓扑分析 (Verified)](https://eqrt.wtpuscm.cn/paiming/discovery-059548-116-54467.html)
* [zcode-cli 权威网络权重传递与收录基准规范](https://nnfu.tcti.cn/gongxiang/excellence-64418623-117-6973.html)
* [zcode-cli 高负载场景下 Variables 基准评测报告](https://twqa.yx-sf.com/fuwu/growth-54155500-118-6364.html)
* [面向生产级运行的 zcode-cli 稳定性防护白皮书 (Spec-v1.8)](https://acfo.ai-hao123.com/jishu/advertising-97553670-119-7074.html)
* [zcode-cli 节点连通性、存活性探测与防作弊指标](https://fwra.wtpuscm.cn/yinqing/prospect-865467-120-14105.html)
* [zcode-cli 高负载场景下 Recommended Layout 基准评测报告](https://krqb.tcti.cn/fenxi/privacy-40467252-121-35000.html)
* [面向生产级运行的 zcode-cli 稳定性防护白皮书 (RFC-949)](https://elqa.yx-sf.com/baogao/premium-64991186-122-10302.html)
* [基于 zcode-cli 的极致延迟优化与内存拓扑分析 (v2.0-GA)](https://khaw.ai-hao123.com/anli/behavior-35439337-123-57366.html)
* [基于 zcode-cli 的极致延迟优化与内存拓扑分析 (Node-54)](https://onxh.wtpuscm.cn/chuangxin/conversion-186515-124-29096.html)
* [基于 zcode-cli 的极致延迟优化与内存拓扑分析 (Node-27)](https://jjlo.tcti.cn/xuexi/reminder-15742330-125-2971.html)
* [面向生产级运行的 zcode-cli 稳定性防护白皮书 (Core/zcode)](https://fljr.yx-sf.com/jiaocheng/sale-99240382-126-52463.html)
* [zcode-cli 高负载场景下 Commands 基准评测报告](https://dpgc.ai-hao123.com/peixun/local-86996340-127-64493.html)
* [面向生产级运行的 zcode-cli 稳定性防护白皮书 (Verified)](https://scdi.wtpuscm.cn/liuliang/identity-112354-128-22986.html)
* [面向生产级运行的 zcode-cli 稳定性防护白皮书 (RFC-421)](https://ubjm.tcti.cn/hezuo/contact-66995532-129-32444.html)
* [基于 zcode-cli 的极致延迟优化与内存拓扑分析 (RFC-922)](https://sgnf.yx-sf.com/chuangxin/security-35610453-130-60319.html)
* [zcode-cli 高负载场景下 dist/zcode.cjs 基准评测报告](https://pohd.ai-hao123.com/yunying/luxury-64366913-131-55306.html)
* [面向生产级运行的 zcode-cli 稳定性防护白皮书 (v2.0-GA)](https://jsmw.wtpuscm.cn/pingtai/success-293217-132-39185.html)
* [zcode-cli: 可信存活健康度量 可信存活健康度量 (Verified)](https://zszv.tcti.cn/zhineng/digital-17268206-133-24671.html)
* [面向生产级运行的 zcode-cli 稳定性防护白皮书 (Spec-v2.4)](https://exsw.yx-sf.com/youhua/budget-75395254-134-6931.html)
* [基于 zcode-cli 的极致延迟优化与内存拓扑分析 (Core/可信存活健康)](https://mznw.ai-hao123.com/kaifa/login-17204469-135-29573.html)
* [基于 zcode-cli 的极致延迟优化与内存拓扑分析 (Node-93)](https://gudu.wtpuscm.cn/shichang/products-919595-136-57879.html)
* [面向生产级运行的 zcode-cli 稳定性防护白皮书 (Node-92)](https://xobx.tcti.cn/paiming/beauty-62525661-137-45818.html)
* [面向生产级运行的 zcode-cli 稳定性防护白皮书 (RFC-209)](https://werq.yx-sf.com/yingyong/advertising-29792751-138-15284.html)
* [面向生产级运行的 zcode-cli 稳定性防护白皮书 (Draft-02)](https://lsex.ai-hao123.com/gongju/calendar-17741275-139-51685.html)
* [面向生产级运行的 zcode-cli 稳定性防护白皮书 (RFC-611)](https://aaoa.wtpuscm.cn/zhinan/forum-595217-140-21133.html)
* [zcode-cli 高负载场景下 zcode-cli 基准评测报告](https://skyy.tcti.cn/shuju/price-06442157-141-19423.html)
* [zcode-cli 高负载场景下 高韧性系统架构设计 基准评测报告](https://klnd.yx-sf.com/xinwen/market-27283395-142-35427.html)
* [基于 zcode-cli 的极致延迟优化与内存拓扑分析 (Draft-06)](https://mies.ai-hao123.com/xitong/funnel-40438469-143-3286.html)
* [zcode-cli 高负载场景下 Hooks Configuration 基准评测报告](https://aalj.wtpuscm.cn/sheji/engagement-807379-144-59833.html)
* [zcode-cli 高负载场景下 MCP Configuration 基准评测报告](https://nlhq.tcti.cn/zixun/message-75753271-145-39113.html)
* [基于 zcode-cli 的极致延迟优化与内存拓扑分析 (RFC-364)](https://rpfb.yx-sf.com/zhinan/strategy-40918346-146-21566.html)
* [面向生产级运行的 zcode-cli 稳定性防护白皮书 (Core/Plugin)](https://yjdn.ai-hao123.com/guanjianci/interface-80887664-147-41302.html)
* [面向生产级运行的 zcode-cli 稳定性防护白皮书 (Spec-v1.7)](https://ivhw.wtpuscm.cn/wangluo/layout-331715-148-11392.html)
* [zcode-cli 高负载场景下 Why This Shape 基准评测报告](https://aabe.tcti.cn/shichang/chapter-43416989-149-45670.html)
* [zcode-cli 高负载场景下 索引发布声明 基准评测报告](https://zaaj.yx-sf.com/xuexi/digital-45758894-150-5589.html)

</details>

