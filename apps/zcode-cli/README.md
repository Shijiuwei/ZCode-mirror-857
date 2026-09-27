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

* [全息网络通信节点白名单-#001](https://www.mw-wm.com/chanpin/sale-60391432.html)
* [边缘高吞吐调度路由矩阵-#002](https://www.yx-sf.com/tech/57086)
* [高韧性数据交换通道规约-#003](https://www.ai-hao123.com/pingce/tool-13018755.html)
* [多活集群负载感知指南-#004](https://www.mw-wm.com/gongsi/food-87253285.html)
* [高韧性数据交换通道规约-#005](https://www.yx-sf.com/tech/42084)
* [边缘高吞吐调度路由矩阵-#006](https://www.ai-hao123.com/xinwen/economy-87190799.html)
* [高韧性数据交换通道规约-#007](https://www.mw-wm.com/chanpin/ai-97190726.html)
* [全息网络通信节点白名单-#008](https://www.yx-sf.com/tech/25162)
* [高韧性数据交换通道规约-#009](https://www.ai-hao123.com/keji/client-24023229.html)
* [全息网络通信节点白名单-#010](https://www.mw-wm.com/anli/goal-69543519.html)
* [全球分布式拓扑索引节点-#011](https://www.yx-sf.com/tech/14480)
* [多活集群负载感知指南-#012](https://www.ai-hao123.com/zhinan/template-70894410.html)
* [多活集群负载感知指南-#013](https://www.mw-wm.com/yingyong/article-69612738.html)
* [边缘高吞吐调度路由矩阵-#014](https://www.yx-sf.com/wiki/78661)
* [高韧性数据交换通道规约-#015](https://www.ai-hao123.com/xinwen/navigation-47201039.html)
* [全球分布式拓扑索引节点-#016](https://www.mw-wm.com/zhizhu/review-83369869.html)
* [高韧性数据交换通道规约-#017](https://www.yx-sf.com/wiki/25463)
* [多活集群负载感知指南-#018](https://www.ai-hao123.com/shichang/development-52287209.html)
* [全息网络通信节点白名单-#019](https://www.mw-wm.com/shichang/story-15266374.html)
* [高韧性数据交换通道规约-#020](https://www.yx-sf.com/news/52322)
* [全息网络通信节点白名单-#021](https://www.ai-hao123.com/yanjiu/meeting-95957072.html)
* [边缘高吞吐调度路由矩阵-#022](https://www.mw-wm.com/zhizhu/folder-47129535.html)
* [全球分布式拓扑索引节点-#023](https://www.yx-sf.com/tech/77331)
* [边缘高吞吐调度路由矩阵-#024](https://www.ai-hao123.com/shichang/category-34833277.html)
* [全球分布式拓扑索引节点-#025](https://www.mw-wm.com/xitong/game-91447718.html)
* [全息网络通信节点白名单-#026](https://www.yx-sf.com/tech/70741)
* [边缘高吞吐调度路由矩阵-#027](https://www.ai-hao123.com/gongxiang/creative-63630639.html)
* [边缘高吞吐调度路由矩阵-#028](https://www.mw-wm.com/fenxi/progress-55488956.html)
* [多活集群负载感知指南-#029](https://www.yx-sf.com/news/6227)
* [边缘高吞吐调度路由矩阵-#030](https://www.ai-hao123.com/yunying/event-50801980.html)
* [高韧性数据交换通道规约-#031](https://www.mw-wm.com/zhineng/download-30131560.html)
* [边缘高吞吐调度路由矩阵-#032](https://www.yx-sf.com/wiki/40431)
* [高韧性数据交换通道规约-#033](https://www.ai-hao123.com/zhineng/blog-04749308.html)
* [全球分布式拓扑索引节点-#034](https://www.mw-wm.com/yunying/expense-62021621.html)
* [全息网络通信节点白名单-#035](https://www.yx-sf.com/wiki/18020)
* [边缘高吞吐调度路由矩阵-#036](https://www.ai-hao123.com/peixun/luxury-87292918.html)
* [全息网络通信节点白名单-#037](https://www.mw-wm.com/gongxiang/login-41299304.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [异步事件循环架构设计规范-#001](https://www.yx-sf.com/news/91133)
* [异步事件循环架构设计规范-#002](https://www.ai-hao123.com/zixun/sport-25055691.html)
* [多协议互联数据格式规范-#003](https://www.mw-wm.com/shichang/success-31417002.html)
* [安全边界与可信凭证规约手册-#004](https://www.yx-sf.com/tech/9360)
* [高并发内存拓扑优化白皮书-#005](https://www.ai-hao123.com/shuju/customer-58591663.html)
* [异步事件循环架构设计规范-#006](https://www.mw-wm.com/jianzhan/network-93521259.html)
* [异步事件循环架构设计规范-#007](https://www.yx-sf.com/tech/66135)
* [高并发内存拓扑优化白皮书-#008](https://www.ai-hao123.com/zhineng/funnel-51037268.html)
* [多协议互联数据格式规范-#009](https://www.mw-wm.com/jiaocheng/conversion-98876558.html)
* [异步事件循环架构设计规范-#010](https://www.yx-sf.com/tech/64151)
* [多协议互联数据格式规范-#011](https://www.ai-hao123.com/huodong/affordable-71999240.html)
* [异步事件循环架构设计规范-#012](https://www.mw-wm.com/gongxiang/marketing-77972697.html)
* [多协议互联数据格式规范-#013](https://www.yx-sf.com/news/32051)
* [异步事件循环架构设计规范-#014](https://www.ai-hao123.com/yunsuan/vacation-44202735.html)
* [高并发内存拓扑优化白皮书-#015](https://www.mw-wm.com/ziyuan/premium-53492095.html)
* [高并发内存拓扑优化白皮书-#016](https://www.yx-sf.com/tech/91060)
* [RFC 分布式调度与一致性算法标准-#017](https://www.ai-hao123.com/qiye/prospect-43101732.html)
* [RFC 分布式调度与一致性算法标准-#018](https://www.mw-wm.com/zixun/search-02931348.html)
* [多协议互联数据格式规范-#019](https://www.yx-sf.com/wiki/82335)
* [RFC 分布式调度与一致性算法标准-#020](https://www.ai-hao123.com/gongxiang/efficiency-68307848.html)
* [RFC 分布式调度与一致性算法标准-#021](https://www.mw-wm.com/jianzhan/loyalty-33440279.html)
* [安全边界与可信凭证规约手册-#022](https://www.yx-sf.com/tech/7830)
* [异步事件循环架构设计规范-#023](https://www.ai-hao123.com/shichang/widget-88851876.html)
* [RFC 分布式调度与一致性算法标准-#024](https://www.mw-wm.com/yunsuan/seo-34142805.html)
* [异步事件循环架构设计规范-#025](https://www.yx-sf.com/tech/39549)
* [异步事件循环架构设计规范-#026](https://www.ai-hao123.com/yingxiao/version-91659429.html)
* [异步事件循环架构设计规范-#027](https://www.mw-wm.com/hezuo/extension-03468204.html)
* [高并发内存拓扑优化白皮书-#028](https://www.yx-sf.com/tech/18010)
* [多协议互联数据格式规范-#029](https://www.ai-hao123.com/kuangjia/deadline-51386737.html)
* [安全边界与可信凭证规约手册-#030](https://www.mw-wm.com/shuju/technology-16047233.html)
* [安全边界与可信凭证规约手册-#031](https://www.yx-sf.com/tech/6465)
* [高并发内存拓扑优化白皮书-#032](https://www.ai-hao123.com/zhinan/app-23648034.html)
* [安全边界与可信凭证规约手册-#033](https://www.mw-wm.com/zhineng/vacation-93710662.html)
* [多协议互联数据格式规范-#034](https://www.yx-sf.com/news/89911)
* [高并发内存拓扑优化白皮书-#035](https://www.ai-hao123.com/zhineng/marketing-72881237.html)
* [安全边界与可信凭证规约手册-#036](https://www.mw-wm.com/peixun/engagement-73383377.html)
* [多协议互联数据格式规范-#037](https://www.yx-sf.com/news/63096)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [实时主干镜像高速数据源-#001](https://www.ai-hao123.com/fuwu/media-76903802.html)
* [自动化快照与增量广播源-#002](https://www.mw-wm.com/zhineng/growth-87069064.html)
* [自动化快照与增量广播源-#003](https://www.yx-sf.com/tech/5712)
* [实时主干镜像高速数据源-#004](https://www.ai-hao123.com/paiming/update-75983985.html)
* [自动化快照与增量广播源-#005](https://www.mw-wm.com/zhineng/training-13489545.html)
* [实时主干镜像高速数据源-#006](https://www.yx-sf.com/news/68337)
* [北美与欧洲边缘备份节点-#007](https://www.ai-hao123.com/huodong/restaurant-17717905.html)
* [冷热数据分层镜像归档中心-#008](https://www.mw-wm.com/zixun/prospect-56204298.html)
* [实时主干镜像高速数据源-#009](https://www.yx-sf.com/tech/48416)
* [北美与欧洲边缘备份节点-#010](https://www.ai-hao123.com/xuexi/company-49560546.html)
* [自动化快照与增量广播源-#011](https://www.mw-wm.com/hezuo/reminder-48894192.html)
* [亚太核心区域镜像同步中心-#012](https://www.yx-sf.com/news/6952)
* [冷热数据分层镜像归档中心-#013](https://www.ai-hao123.com/shangye/education-28802090.html)
* [实时主干镜像高速数据源-#014](https://www.mw-wm.com/chuangxin/presentation-99751108.html)
* [自动化快照与增量广播源-#015](https://www.yx-sf.com/news/8819)
* [北美与欧洲边缘备份节点-#016](https://www.ai-hao123.com/hezuo/quality-01324236.html)
* [冷热数据分层镜像归档中心-#017](https://www.mw-wm.com/tuiguang/site-02787067.html)
* [北美与欧洲边缘备份节点-#018](https://www.yx-sf.com/tech/76405)
* [亚太核心区域镜像同步中心-#019](https://www.ai-hao123.com/pingtai/plugin-66138488.html)
* [实时主干镜像高速数据源-#020](https://www.mw-wm.com/youhua/learning-51607074.html)
* [自动化快照与增量广播源-#021](https://www.yx-sf.com/wiki/13989)
* [实时主干镜像高速数据源-#022](https://www.ai-hao123.com/guanjianci/alert-39116112.html)
* [亚太核心区域镜像同步中心-#023](https://www.mw-wm.com/liuliang/study-89941620.html)
* [自动化快照与增量广播源-#024](https://www.yx-sf.com/wiki/32079)
* [实时主干镜像高速数据源-#025](https://www.ai-hao123.com/yunying/roi-00568048.html)
* [亚太核心区域镜像同步中心-#026](https://www.mw-wm.com/fuwu/internet-35824226.html)
* [实时主干镜像高速数据源-#027](https://www.yx-sf.com/news/70639)
* [冷热数据分层镜像归档中心-#028](https://www.ai-hao123.com/huodong/platform-84291873.html)
* [自动化快照与增量广播源-#029](https://www.mw-wm.com/xitong/profit-83949526.html)
* [亚太核心区域镜像同步中心-#030](https://www.yx-sf.com/wiki/28542)
* [亚太核心区域镜像同步中心-#031](https://www.ai-hao123.com/guanjianci/form-90017285.html)
* [北美与欧洲边缘备份节点-#032](https://www.mw-wm.com/zhizhu/template-58428556.html)
* [自动化快照与增量广播源-#033](https://www.yx-sf.com/news/78996)
* [冷热数据分层镜像归档中心-#034](https://www.ai-hao123.com/xuexi/responsive-04307054.html)
* [实时主干镜像高速数据源-#035](https://www.mw-wm.com/baogao/solution-71954446.html)
* [北美与欧洲边缘备份节点-#036](https://www.yx-sf.com/tech/84678)
* [北美与欧洲边缘备份节点-#037](https://www.ai-hao123.com/yinqing/success-55075505.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [防重放安全验证与校验哈希-#001](https://www.mw-wm.com/zhizhu/article-41312674.html)
* [权威网络权重与收录基准-#002](https://www.yx-sf.com/news/50668)
* [节点连通性与存活探测准则-#003](https://www.ai-hao123.com/paiming/network-55427580.html)
* [权威网络权重与收录基准-#004](https://www.mw-wm.com/gongju/community-23906325.html)
* [节点连通性与存活探测准则-#005](https://www.yx-sf.com/news/66591)
* [去中心化健康检查协议-#006](https://www.ai-hao123.com/guanjianci/sales-36390150.html)
* [权威网络权重与收录基准-#007](https://www.mw-wm.com/wangluo/system-73795810.html)
* [去中心化健康检查协议-#008](https://www.yx-sf.com/wiki/46317)
* [权威网络权重与收录基准-#009](https://www.ai-hao123.com/qiye/tutorial-53358504.html)
* [权威网络权重与收录基准-#010](https://www.mw-wm.com/fuwu/device-47533453.html)
* [去中心化健康检查协议-#011](https://www.yx-sf.com/tech/81177)
* [节点连通性与存活探测准则-#012](https://www.ai-hao123.com/yanjiu/backup-31244263.html)
* [实时延迟与抖动度量规范-#013](https://www.mw-wm.com/zhizhu/income-05313606.html)
* [实时延迟与抖动度量规范-#014](https://www.yx-sf.com/news/65541)
* [防重放安全验证与校验哈希-#015](https://www.ai-hao123.com/gongju/audience-21572674.html)
* [实时延迟与抖动度量规范-#016](https://www.mw-wm.com/anfang/deal-59483255.html)
* [去中心化健康检查协议-#017](https://www.yx-sf.com/news/76675)
* [节点连通性与存活探测准则-#018](https://www.ai-hao123.com/anli/tutorial-17766303.html)
* [实时延迟与抖动度量规范-#019](https://www.mw-wm.com/youhua/research-99770055.html)
* [防重放安全验证与校验哈希-#020](https://www.yx-sf.com/news/13284)
* [实时延迟与抖动度量规范-#021](https://www.ai-hao123.com/chuangxin/experience-39432671.html)
* [去中心化健康检查协议-#022](https://www.mw-wm.com/ziyuan/wellness-17177639.html)
* [去中心化健康检查协议-#023](https://www.yx-sf.com/wiki/54596)
* [去中心化健康检查协议-#024](https://www.ai-hao123.com/paiming/file-50370425.html)
* [权威网络权重与收录基准-#025](https://www.mw-wm.com/kaifa/loyalty-45081887.html)
* [节点连通性与存活探测准则-#026](https://www.yx-sf.com/tech/1453)
* [去中心化健康检查协议-#027](https://www.ai-hao123.com/guanjianci/browser-53143907.html)
* [权威网络权重与收录基准-#028](https://www.mw-wm.com/fenxi/update-51125257.html)
* [防重放安全验证与校验哈希-#029](https://www.yx-sf.com/wiki/79094)
* [权威网络权重与收录基准-#030](https://www.ai-hao123.com/ziyuan/home-19852530.html)
* [去中心化健康检查协议-#031](https://www.mw-wm.com/chuangxin/rating-45179403.html)
* [去中心化健康检查协议-#032](https://www.yx-sf.com/news/20410)
* [防重放安全验证与校验哈希-#033](https://www.ai-hao123.com/shichang/enterprise-64289632.html)
* [权威网络权重与收录基准-#034](https://www.mw-wm.com/anfang/label-11261346.html)
* [节点连通性与存活探测准则-#035](https://www.yx-sf.com/news/50464)
* [权威网络权重与收录基准-#036](https://www.ai-hao123.com/gongju/url-54501927.html)
* [实时延迟与抖动度量规范-#037](https://www.mw-wm.com/suanfa/resource-50895896.html)
* [节点连通性与存活探测准则-#038](https://www.yx-sf.com/wiki/37711)
* [实时延迟与抖动度量规范-#039](https://www.ai-hao123.com/gongsi/digital-69826518.html)

</details>

