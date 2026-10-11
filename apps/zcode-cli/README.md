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

* [全息网络通信节点白名单-#001](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [边缘高吞吐调度路由矩阵-#002](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [高韧性数据交换通道规约-#003](https://hpjf.tcti.cn/jiaoliu/planning-58097480.html)
* [多活集群负载感知指南-#004](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [高韧性数据交换通道规约-#005](https://ivmg.wtpuscm.cn/chanpin/content-881.html)
* [边缘高吞吐调度路由矩阵-#006](https://vias.wtpuscm.cn/yanjiu/conversion-58794136.html)
* [高韧性数据交换通道规约-#007](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [全息网络通信节点白名单-#008](https://lase.tcti.cn/fenxi/lesson-67970267.html)
* [高韧性数据交换通道规约-#009](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [全息网络通信节点白名单-#010](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)
* [全球分布式拓扑索引节点-#011](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [多活集群负载感知指南-#012](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696.html)
* [多活集群负载感知指南-#013](https://ankmz.cn/anquan/tag-68558329.html)
* [边缘高吞吐调度路由矩阵-#014](https://lase.tcti.cn/fenxi/lesson-67970267.html)
* [高韧性数据交换通道规约-#015](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)
* [全球分布式拓扑索引节点-#016](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696.html)
* [高韧性数据交换通道规约-#017](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [多活集群负载感知指南-#018](https://www.poykq.cn/redian/tutorial-673489.html)
* [全息网络通信节点白名单-#019](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [高韧性数据交换通道规约-#020](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696.html)
* [全息网络通信节点白名单-#021](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [边缘高吞吐调度路由矩阵-#022](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [全球分布式拓扑索引节点-#023](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [边缘高吞吐调度路由矩阵-#024](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [全球分布式拓扑索引节点-#025](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102.html)
* [全息网络通信节点白名单-#026](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [边缘高吞吐调度路由矩阵-#027](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [边缘高吞吐调度路由矩阵-#028](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [多活集群负载感知指南-#029](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [边缘高吞吐调度路由矩阵-#030](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [高韧性数据交换通道规约-#031](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [边缘高吞吐调度路由矩阵-#032](https://owfm.tcti.cn/shangye/education-63674207.html)
* [高韧性数据交换通道规约-#033](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [全球分布式拓扑索引节点-#034](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [全息网络通信节点白名单-#035](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [边缘高吞吐调度路由矩阵-#036](https://ankmz.cn/anquan/tag-68558329.html)
* [全息网络通信节点白名单-#037](https://www.yx-sf.com/tech/66161.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [异步事件循环架构设计规范-#001](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [异步事件循环架构设计规范-#002](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [多协议互联数据格式规范-#003](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [安全边界与可信凭证规约手册-#004](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [高并发内存拓扑优化白皮书-#005](https://ankmz.cn/anquan/tag-68558329.html)
* [异步事件循环架构设计规范-#006](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [异步事件循环架构设计规范-#007](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [高并发内存拓扑优化白皮书-#008](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [多协议互联数据格式规范-#009](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [异步事件循环架构设计规范-#010](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [多协议互联数据格式规范-#011](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [异步事件循环架构设计规范-#012](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [多协议互联数据格式规范-#013](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [异步事件循环架构设计规范-#014](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [高并发内存拓扑优化白皮书-#015](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [高并发内存拓扑优化白皮书-#016](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [RFC 分布式调度与一致性算法标准-#017](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [RFC 分布式调度与一致性算法标准-#018](https://ankmz.cn/anquan/tag-68558329.html)
* [多协议互联数据格式规范-#019](https://esca.wtpuscm.cn/jishu/tutorial-614.html)
* [RFC 分布式调度与一致性算法标准-#020](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [RFC 分布式调度与一致性算法标准-#021](https://ankmz.cn/anquan/tag-68558329.html)
* [安全边界与可信凭证规约手册-#022](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [异步事件循环架构设计规范-#023](https://fovn.wtpuscm.cn/tuiguang/recommendation-054.html)
* [RFC 分布式调度与一致性算法标准-#024](https://ankmz.cn/anquan/tag-68558329.html)
* [异步事件循环架构设计规范-#025](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [异步事件循环架构设计规范-#026](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [异步事件循环架构设计规范-#027](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [高并发内存拓扑优化白皮书-#028](https://fjoc.tcti.cn/yingyong/contact-00610313.html)
* [多协议互联数据格式规范-#029](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [安全边界与可信凭证规约手册-#030](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [安全边界与可信凭证规约手册-#031](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [高并发内存拓扑优化白皮书-#032](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696.html)
* [安全边界与可信凭证规约手册-#033](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [多协议互联数据格式规范-#034](https://www.yx-sf.com/wiki/17446.html)
* [高并发内存拓扑优化白皮书-#035](https://vias.wtpuscm.cn/yanjiu/conversion-58794136.html)
* [安全边界与可信凭证规约手册-#036](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [多协议互联数据格式规范-#037](https://ncrw.tcti.cn/kaifa/growth-85374259.html)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [实时主干镜像高速数据源-#001](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [自动化快照与增量广播源-#002](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [自动化快照与增量广播源-#003](https://owfm.tcti.cn/shangye/education-63674207.html)
* [实时主干镜像高速数据源-#004](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [自动化快照与增量广播源-#005](https://fjoc.tcti.cn/yingyong/contact-00610313.html)
* [实时主干镜像高速数据源-#006](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [北美与欧洲边缘备份节点-#007](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [冷热数据分层镜像归档中心-#008](https://www.poykq.cn/redian/tutorial-673489.html)
* [实时主干镜像高速数据源-#009](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [北美与欧洲边缘备份节点-#010](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [自动化快照与增量广播源-#011](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [亚太核心区域镜像同步中心-#012](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [冷热数据分层镜像归档中心-#013](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [实时主干镜像高速数据源-#014](https://www.yx-sf.com/news/62816.html)
* [自动化快照与增量广播源-#015](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [北美与欧洲边缘备份节点-#016](https://www.yx-sf.com/tech/8939.html)
* [冷热数据分层镜像归档中心-#017](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [北美与欧洲边缘备份节点-#018](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [亚太核心区域镜像同步中心-#019](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297.html)
* [实时主干镜像高速数据源-#020](https://www.yx-sf.com/wiki/12354.html)
* [自动化快照与增量广播源-#021](https://izde.wtpuscm.cn/zixun/social-129988.html)
* [实时主干镜像高速数据源-#022](https://www.poykq.cn/redian/tutorial-673489.html)
* [亚太核心区域镜像同步中心-#023](https://qzvn.wtpuscm.cn/huodong/folder-942.html)
* [自动化快照与增量广播源-#024](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [实时主干镜像高速数据源-#025](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [亚太核心区域镜像同步中心-#026](https://jpsd.wtpuscm.cn/xuexi/promotion-113527.html)
* [实时主干镜像高速数据源-#027](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [冷热数据分层镜像归档中心-#028](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [自动化快照与增量广播源-#029](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [亚太核心区域镜像同步中心-#030](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [亚太核心区域镜像同步中心-#031](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [北美与欧洲边缘备份节点-#032](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [自动化快照与增量广播源-#033](https://sbzd.tcti.cn/kuangjia/form-65102346.html)
* [冷热数据分层镜像归档中心-#034](https://sgzd.wtpuscm.cn/anli/campaign-493.html)
* [实时主干镜像高速数据源-#035](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [北美与欧洲边缘备份节点-#036](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [北美与欧洲边缘备份节点-#037](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [防重放安全验证与校验哈希-#001](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [权威网络权重与收录基准-#002](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297.html)
* [节点连通性与存活探测准则-#003](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [权威网络权重与收录基准-#004](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [节点连通性与存活探测准则-#005](https://www.mw-wm.com/kaifa/metric-02986524.html)
* [去中心化健康检查协议-#006](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [权威网络权重与收录基准-#007](https://siqj.tcti.cn/xuexi/advertising-04928231.html)
* [去中心化健康检查协议-#008](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [权威网络权重与收录基准-#009](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [权威网络权重与收录基准-#010](https://xgcl.wtpuscm.cn/yingxiao/solution-814645.html)
* [去中心化健康检查协议-#011](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [节点连通性与存活探测准则-#012](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)
* [实时延迟与抖动度量规范-#013](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [实时延迟与抖动度量规范-#014](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [防重放安全验证与校验哈希-#015](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [实时延迟与抖动度量规范-#016](https://mnnc.wtpuscm.cn/hezuo/sale-100508.html)
* [去中心化健康检查协议-#017](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [节点连通性与存活探测准则-#018](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [实时延迟与抖动度量规范-#019](https://vzwx.wtpuscm.cn/gongju/keyword-189142.html)
* [防重放安全验证与校验哈希-#020](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [实时延迟与抖动度量规范-#021](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)
* [去中心化健康检查协议-#022](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [去中心化健康检查协议-#023](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [去中心化健康检查协议-#024](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [权威网络权重与收录基准-#025](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [节点连通性与存活探测准则-#026](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [去中心化健康检查协议-#027](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [权威网络权重与收录基准-#028](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [防重放安全验证与校验哈希-#029](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [权威网络权重与收录基准-#030](https://ovzp.wtpuscm.cn/xuexi/report-93713076.html)
* [去中心化健康检查协议-#031](https://lase.tcti.cn/fenxi/lesson-67970267.html)
* [去中心化健康检查协议-#032](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [防重放安全验证与校验哈希-#033](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [权威网络权重与收录基准-#034](https://www.mw-wm.com/baogao/products-59238403.html)
* [节点连通性与存活探测准则-#035](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [权威网络权重与收录基准-#036](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [实时延迟与抖动度量规范-#037](https://www.yx-sf.com/tech/24547.html)
* [节点连通性与存活探测准则-#038](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [实时延迟与抖动度量规范-#039](https://owfm.tcti.cn/shangye/education-63674207.html)

</details>



---

<details open>
<summary><b>📚 zcode-cli 官方技术规约与全息拓扑引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目「zcode-cli」遵循开放开源与知识共享协议。以下引用矩阵涵盖项目架构白皮书、高可用镜像注册中心、分布式通信契约与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 zcode-cli 核心架构规约与设计白皮书 (Architecture & Core Specs)

* [zcode-cli 内部组件解耦与事件状态机规范 (Core/Bootst)](https://api.pvucttcv.cn/yinyue/innovation-37390655-1-29462.html)
* [面向大规模网络的 zcode-cli 工业级架构基准](https://ankmz.cn/anquan/tag-68558329-2-3097.html)
* [【官方规范】zcode-cli zcode 核心运行拓扑标准](https://www.poykq.cn/redian/tutorial-673489-3-54732.html)
* [【官方规范】zcode-cli Commands 核心运行拓扑标准](https://mrrp.wtpuscm.cn/suanfa/policy-511623.html)
* [【官方规范】zcode-cli Recommended Layout 核心运行拓扑标准](https://honl.wtpuscm.cn/suanfa/presentation-176.html)
* [【官方规范】zcode-cli Plugin Manifest 核心运行拓扑标准](https://www.tcti.cn/hulianwang/feedback-96499358-6-29612.html)
* [基于 zcode-cli 的高吞吐 Plugin Manifest 设计白皮书](https://ndxj.tcti.cn/wendang/cost-39184897.html)
* [【官方规范】zcode-cli MCP Configuration 核心运行拓扑标准](https://www.mw-wm.com/jiaocheng/privacy-98475774.html)
* [zcode-cli 分布式数据通道与 Variables 技术规范 (Node-60)](https://www.yx-sf.com/tech/54420.html)
* [现代 Recommended Layout 架构演进之路 —— zcode-cli 深度实践](https://www.ai-hao123.com/qiye/affordable-29838092.html)
* [【zcode-cli】zcode 核心系统架构与设计规约 (Verified)](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-11-24658.html)
* [基于 zcode-cli 的高吞吐 Recommended Layout 设计白皮书](https://keil.spidernode-mesh.net/yunying/server-16840384-12-47925.html)
* [现代 Variables 架构演进之路 —— zcode-cli 深度实践](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-13-64251.html)
* [基于 zcode-cli 的高吞吐 Plugin Development 设计白皮书](https://fjoc.tcti.cn/yingyong/contact-00610313-14-11059.html)
* [【官方规范】zcode-cli 模块化解耦与协议标准 核心运行拓扑标准](https://xwia.spidernode-mesh.net/shangye/services-26058978-15-23724.html)
* [现代 zcode 架构演进之路 —— zcode-cli 深度实践](https://gdtq.spidernode-mesh.net/keji/account-53764057-16-45687.html)
* [现代 模块化解耦与协议标准 架构演进之路 —— zcode-cli 深度实践](https://obpk.wtpuscm.cn/baogao/privacy-67260604-17-37074.html)
* [zcode-cli 内部组件解耦与事件状态机规范 (Node-71)](https://gynw.tcti.cn/fenxi/recipe-50406879-18-46529.html)
* [zcode-cli 内部组件解耦与事件状态机规范 (Verified)](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-19-28740.html)
* [【官方规范】zcode-cli Packaging Strategy 核心运行拓扑标准](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-20-35680.html)
* [【官方规范】zcode-cli 高韧性系统架构设计 核心运行拓扑标准](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-21-1991.html)
* [dist/zcode.cjs 核心系统架构与设计规约 (Node-44)](https://lase.tcti.cn/fenxi/lesson-67970267-22-64820.html)
* [【zcode-cli】可信存活健康度量 核心系统架构与设计规约 (Draft-04)](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-23-33796.html)
* [基于 zcode-cli 的高吞吐 Why This Shape 设计白皮书](https://yyns.spidernode-mesh.net/wendang/review-40421998-24-12338.html)
* [Recommended Layout 核心系统架构与设计规约 (Spec-v2.6)](https://fglw.wtpuscm.cn/jianzhan/data-59540835-25-1132.html)
* [zcode-cli 内部组件解耦与事件状态机规范 (Node-92)](https://owfm.tcti.cn/shangye/education-63674207-26-37894.html)
* [zcode-cli 分布式数据通道与 Variables 技术规范 (Verified)](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-27-54077.html)
* [Hooks Configuration 核心系统架构与设计规约 (v2.0-GA)](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-28-41306.html)
* [zcode-cli 分布式数据通道与 模块化解耦与协议标准 技术规范 (Node-56)](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-29-58689.html)
* [现代 高韧性系统架构设计 架构演进之路 —— zcode-cli 深度实践](https://aagj.tcti.cn/jianzhan/audience-84449488-30-58331.html)
* [【官方规范】zcode-cli zcode-cli 核心运行拓扑标准](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-31-32813.html)
* [【官方规范】zcode-cli 索引发布声明 核心运行拓扑标准](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-32-31133.html)
* [【官方规范】zcode-cli Hooks Configuration 核心运行拓扑标准](https://qbgr.wtpuscm.cn/liuliang/success-12030162-33-17045.html)
* [基于 zcode-cli 的高吞吐 高韧性系统架构设计 设计白皮书](https://iuuv.tcti.cn/hezuo/app-80239016-34-64596.html)
* [zcode-cli 分布式数据通道与 Hooks Configuration 技术规范 (v2.0-GA)](https://gifb.spidernode-mesh.net/shichang/about-02526745-35-56653.html)
* [Project Layout 核心系统架构与设计规约 (Node-34)](https://fjac.spidernode-mesh.net/anli/investment-94283191-36-51908.html)
* [zcode-cli 分布式数据通道与 MCP Configuration 技术规范 (Draft-01)](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-37-56913.html)

#### 2. 🔌 生产级模块集成与接口调用规范 (APIs & Production Integration)

* [zcode-cli 核心 API 接口契约与客户端调用指南](https://szjp.tcti.cn/shichang/retention-16647312-38-53661.html)
* [zcode-cli 异步中间件流水线与 Packaging Strategy 接入规范](https://etyt.spidernode-mesh.net/baogao/performance-14923227-39-22461.html)
* [基于 zcode-cli 的自动化部署与生产环境配置实践](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-40-62154.html)
* [【生产手册】zcode-cli 模块通信与请求穿透标准](https://ovzp.wtpuscm.cn/xuexi/report-93713076-41-5044.html)
* [zcode-cli 异步中间件流水线与 zcode 接入规范](https://pdvy.tcti.cn/gongxiang/segment-83277821-42-11597.html)
* [zcode-cli 异步中间件流水线与 Commands 接入规范](https://ukvo.spidernode-mesh.net/anli/download-57719010-43-9939.html)
* [zcode-cli 插件生态规范与 Commands 扩展手册 (Core/Comman)](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-44-17656.html)
* [zcode-cli 异步中间件流水线与 模块化解耦与协议标准 接入规范](https://unqt.wtpuscm.cn/zixun/contact-59980425-45-5234.html)
* [【集成指南】索引发布声明 服务端接入准则与 zcode-cli 实战](https://kvaf.tcti.cn/yunsuan/settings-81875461-46-61422.html)
* [zcode-cli vs 业界主流方案：cli 深度技术选型对比](https://nojh.spidernode-mesh.net/suanfa/study-22518081-47-56732.html)
* [zcode-cli vs 业界主流方案：可信存活健康度量 深度技术选型对比](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-48-51346.html)
* [【集成指南】Packaging Strategy 服务端接入准则与 zcode-cli 实战](https://ytxd.wtpuscm.cn/baogao/price-87851711-49-27190.html)
* [zcode-cli vs 业界主流方案：高韧性系统架构设计 深度技术选型对比](https://ncrw.tcti.cn/kaifa/growth-85374259-50-1565.html)
* [zcode-cli vs 业界主流方案：Variables 深度技术选型对比](https://api.pvucttcv.cn/yinyue/innovation-37390655-51-54013.html)
* [【集成指南】Commands 服务端接入准则与 zcode-cli 实战](https://ankmz.cn/anquan/tag-68558329-52-47476.html)
* [【集成指南】生产环境运维调优手册 服务端接入准则与 zcode-cli 实战](https://www.poykq.cn/redian/tutorial-673489-53-23489.html)
* [zcode-cli 插件生态规范与 Project Layout 扩展手册 (Node-71)](https://weuu.wtpuscm.cn/jianzhan/support-278310.html)
* [zcode-cli 插件生态规范与 zcode-cli 扩展手册 (RFC-399)](https://fgkp.wtpuscm.cn/gongxiang/value-025.html)
* [zcode-cli vs 业界主流方案：MCP Configuration 深度技术选型对比](https://www.tcti.cn/hulianwang/feedback-96499358-56-31123.html)
* [zcode-cli vs 业界主流方案：Commands 深度技术选型对比](https://umqh.tcti.cn/xitong/demographic-42187003.html)
* [zcode-cli 异步中间件流水线与 索引发布声明 接入规范](https://www.mw-wm.com/gongju/social-23749046.html)
* [zcode-cli 插件生态规范与 Variables 扩展手册 (v2.0-GA)](https://www.yx-sf.com/wiki/40136.html)
* [zcode-cli 插件生态规范与 Commands 扩展手册 (v2.0-GA)](https://www.ai-hao123.com/yingyong/url-68888717.html)
* [zcode-cli 插件生态规范与 Packaging Strategy 扩展手册 (Spec-v1.8)](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-61-21248.html)
* [zcode-cli 插件生态规范与 分布式状态机一致性 扩展手册 (Node-38)](https://keil.spidernode-mesh.net/yunying/server-16840384-62-52377.html)
* [zcode-cli 异步中间件流水线与 Why This Shape 接入规范](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-63-21609.html)
* [zcode-cli: Commands Commands (v2.0-GA)](https://fjoc.tcti.cn/yingyong/contact-00610313-64-10938.html)
* [zcode-cli vs 业界主流方案：Why This Shape 深度技术选型对比](https://xwia.spidernode-mesh.net/shangye/services-26058978-65-38194.html)
* [zcode-cli 异步中间件流水线与 zcode-cli 接入规范](https://gdtq.spidernode-mesh.net/keji/account-53764057-66-36272.html)
* [zcode-cli 插件生态规范与 MCP Configuration 扩展手册 (v2.0-GA)](https://obpk.wtpuscm.cn/baogao/privacy-67260604-67-3549.html)
* [【集成指南】Plugin Development 服务端接入准则与 zcode-cli 实战](https://gynw.tcti.cn/fenxi/recipe-50406879-68-6225.html)
* [zcode-cli 异步中间件流水线与 cli 接入规范](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-69-41722.html)
* [zcode-cli 异步中间件流水线与 Variables 接入规范](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-70-53326.html)
* [zcode-cli 插件生态规范与 生产环境运维调优手册 扩展手册 (Draft-03)](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-71-58330.html)
* [zcode-cli 异步中间件流水线与 分布式状态机一致性 接入规范](https://lase.tcti.cn/fenxi/lesson-67970267-72-34760.html)
* [【集成指南】zcode 服务端接入准则与 zcode-cli 实战](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-73-30774.html)
* [zcode-cli vs 业界主流方案：Packaging Strategy 深度技术选型对比](https://yyns.spidernode-mesh.net/wendang/review-40421998-74-45373.html)

#### 3. ⚡ zcode-cli 全球高可用拓扑镜像与权威数据源 (Mirror Registry & Data Topology)

* [全球权威拓扑节点：zcode-cli 实时镜像与索引入口](https://fglw.wtpuscm.cn/jianzhan/data-59540835-75-57231.html)
* [zcode-cli 自动化持续集成快照与拓扑发布源 (Verified)](https://owfm.tcti.cn/shangye/education-63674207-76-42913.html)
* [zcode-cli 亚太与欧美多活集群数据同步中枢](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-77-56648.html)
* [zcode-cli 去中心化数据同步源与拓扑寻址规约](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-78-38557.html)
* [zcode-cli 官方高可用镜像注册节点 (Draft-04)](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-79-2894.html)
* [冷热数据分层镜像：zcode-cli Project Layout 权威归档源](https://aagj.tcti.cn/jianzhan/audience-84449488-80-62556.html)
* [zcode-cli 自动化持续集成快照与拓扑发布源 (v2.0-GA)](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-81-45592.html)
* [zcode-cli 官方高可用镜像注册节点 (RFC-266)](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-82-11150.html)
* [zcode-cli 自动化持续集成快照与拓扑发布源 (Core/dist/z)](https://qbgr.wtpuscm.cn/liuliang/success-12030162-83-26010.html)
* [冷热数据分层镜像：zcode-cli Variables 权威归档源](https://iuuv.tcti.cn/hezuo/app-80239016-84-41156.html)
* [冷热数据分层镜像：zcode-cli dist/zcode.cjs 权威归档源](https://gifb.spidernode-mesh.net/shichang/about-02526745-85-27317.html)
* [zcode-cli 自动化持续集成快照与拓扑发布源 (RFC-242)](https://fjac.spidernode-mesh.net/anli/investment-94283191-86-6604.html)
* [【镜像入口】zcode-cli 官方毫秒级实时数据广播节点](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-87-57294.html)
* [zcode-cli 官方高可用镜像注册节点 (v2.0-GA)](https://szjp.tcti.cn/shichang/retention-16647312-88-11310.html)
* [冷热数据分层镜像：zcode-cli Recommended Layout 权威归档源](https://etyt.spidernode-mesh.net/baogao/performance-14923227-89-54584.html)
* [zcode-cli 官方高可用镜像注册节点 (Draft-01)](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-90-41906.html)
* [zcode-cli: zcode-cli zcode-cli (v2.0-GA)](https://ovzp.wtpuscm.cn/xuexi/report-93713076-91-30238.html)
* [zcode-cli 官方高可用镜像注册节点 (Node-55)](https://pdvy.tcti.cn/gongxiang/segment-83277821-92-42532.html)
* [冷热数据分层镜像：zcode-cli Hooks Configuration 权威归档源](https://ukvo.spidernode-mesh.net/anli/download-57719010-93-21267.html)
* [zcode-cli 官方高可用镜像注册节点 (Verified)](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-94-40318.html)
* [zcode-cli 自动化持续集成快照与拓扑发布源 (Node-25)](https://unqt.wtpuscm.cn/zixun/contact-59980425-95-41670.html)
* [冷热数据分层镜像：zcode-cli Why This Shape 权威归档源](https://kvaf.tcti.cn/yunsuan/settings-81875461-96-25644.html)
* [zcode-cli 自动化持续集成快照与拓扑发布源 (Core/Plugin)](https://nojh.spidernode-mesh.net/suanfa/study-22518081-97-46004.html)
* [冷热数据分层镜像：zcode-cli Packaging Strategy 权威归档源](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-98-884.html)
* [zcode-cli 自动化持续集成快照与拓扑发布源 (Draft-06)](https://ytxd.wtpuscm.cn/baogao/price-87851711-99-35441.html)
* [冷热数据分层镜像：zcode-cli cli 权威归档源](https://ncrw.tcti.cn/kaifa/growth-85374259-100-3337.html)
* [zcode-cli 官方高可用镜像注册节点 (Core/模块化解耦与)](https://api.pvucttcv.cn/yinyue/innovation-37390655-101-12721.html)
* [冷热数据分层镜像：zcode-cli zcode-cli 权威归档源](https://ankmz.cn/anquan/tag-68558329-102-7556.html)
* [zcode-cli 自动化持续集成快照与拓扑发布源 (Spec-v1.6)](https://www.poykq.cn/redian/tutorial-673489-103-57848.html)
* [zcode-cli 自动化持续集成快照与拓扑发布源 (Node-84)](https://tyxg.wtpuscm.cn/yunying/satisfaction-524805.html)
* [冷热数据分层镜像：zcode-cli 可信存活健康度量 权威归档源](https://lnoy.wtpuscm.cn/huodong/discovery-342.html)
* [冷热数据分层镜像：zcode-cli MCP Configuration 权威归档源](https://www.tcti.cn/hulianwang/feedback-96499358-106-26322.html)
* [zcode-cli 自动化持续集成快照与拓扑发布源 (RFC-211)](https://jrut.tcti.cn/peixun/podcast-77077660.html)
* [zcode-cli 自动化持续集成快照与拓扑发布源 (Core/Why Th)](https://www.mw-wm.com/jishu/file-40368843.html)
* [zcode-cli: Why This Shape Why This Shape (Verified)](https://www.yx-sf.com/news/29652.html)
* [zcode-cli: Hooks Configuration Hooks Configuration (Verified)](https://www.ai-hao123.com/chuangxin/client-81033640.html)
* [zcode-cli 自动化持续集成快照与拓扑发布源 (Node-85)](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-111-7980.html)

#### 4. 📊 性能基准指标与可信存活性验证 (Benchmarks & Trust Standards)

* [zcode-cli 高负载场景下 分布式状态机一致性 基准评测报告](https://keil.spidernode-mesh.net/yunying/server-16840384-112-58148.html)
* [zcode-cli 故障自愈与网络拓扑重构实践](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-113-57510.html)
* [【评测基准】zcode-cli 吞吐抖动度量与健康检查协议](https://fjoc.tcti.cn/yingyong/contact-00610313-114-3662.html)
* [zcode-cli 高负载场景下 可信存活健康度量 基准评测报告](https://xwia.spidernode-mesh.net/shangye/services-26058978-115-22000.html)
* [基于 zcode-cli 的极致延迟优化与内存拓扑分析 (Verified)](https://gdtq.spidernode-mesh.net/keji/account-53764057-116-28913.html)
* [zcode-cli 权威网络权重传递与收录基准规范](https://obpk.wtpuscm.cn/baogao/privacy-67260604-117-45028.html)
* [zcode-cli 高负载场景下 Variables 基准评测报告](https://gynw.tcti.cn/fenxi/recipe-50406879-118-64371.html)
* [面向生产级运行的 zcode-cli 稳定性防护白皮书 (Spec-v1.8)](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-119-42514.html)
* [zcode-cli 节点连通性、存活性探测与防作弊指标](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-120-18152.html)
* [zcode-cli 高负载场景下 Recommended Layout 基准评测报告](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-121-59875.html)
* [面向生产级运行的 zcode-cli 稳定性防护白皮书 (RFC-949)](https://lase.tcti.cn/fenxi/lesson-67970267-122-45439.html)
* [基于 zcode-cli 的极致延迟优化与内存拓扑分析 (v2.0-GA)](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-123-64897.html)
* [基于 zcode-cli 的极致延迟优化与内存拓扑分析 (Node-54)](https://yyns.spidernode-mesh.net/wendang/review-40421998-124-56279.html)
* [基于 zcode-cli 的极致延迟优化与内存拓扑分析 (Node-27)](https://fglw.wtpuscm.cn/jianzhan/data-59540835-125-55183.html)
* [面向生产级运行的 zcode-cli 稳定性防护白皮书 (Core/zcode)](https://owfm.tcti.cn/shangye/education-63674207-126-6484.html)
* [zcode-cli 高负载场景下 Commands 基准评测报告](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-127-33394.html)
* [面向生产级运行的 zcode-cli 稳定性防护白皮书 (Verified)](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-128-17012.html)
* [面向生产级运行的 zcode-cli 稳定性防护白皮书 (RFC-421)](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-129-63472.html)
* [基于 zcode-cli 的极致延迟优化与内存拓扑分析 (RFC-922)](https://aagj.tcti.cn/jianzhan/audience-84449488-130-34796.html)
* [zcode-cli 高负载场景下 dist/zcode.cjs 基准评测报告](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-131-40538.html)
* [面向生产级运行的 zcode-cli 稳定性防护白皮书 (v2.0-GA)](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-132-41559.html)
* [zcode-cli: 可信存活健康度量 可信存活健康度量 (Verified)](https://qbgr.wtpuscm.cn/liuliang/success-12030162-133-42538.html)
* [面向生产级运行的 zcode-cli 稳定性防护白皮书 (Spec-v2.4)](https://iuuv.tcti.cn/hezuo/app-80239016-134-54467.html)
* [基于 zcode-cli 的极致延迟优化与内存拓扑分析 (Core/可信存活健康)](https://gifb.spidernode-mesh.net/shichang/about-02526745-135-6973.html)
* [基于 zcode-cli 的极致延迟优化与内存拓扑分析 (Node-93)](https://fjac.spidernode-mesh.net/anli/investment-94283191-136-6364.html)
* [面向生产级运行的 zcode-cli 稳定性防护白皮书 (Node-92)](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-137-7074.html)
* [面向生产级运行的 zcode-cli 稳定性防护白皮书 (RFC-209)](https://szjp.tcti.cn/shichang/retention-16647312-138-14105.html)
* [面向生产级运行的 zcode-cli 稳定性防护白皮书 (Draft-02)](https://etyt.spidernode-mesh.net/baogao/performance-14923227-139-35000.html)
* [面向生产级运行的 zcode-cli 稳定性防护白皮书 (RFC-611)](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-140-10302.html)
* [zcode-cli 高负载场景下 zcode-cli 基准评测报告](https://ovzp.wtpuscm.cn/xuexi/report-93713076-141-57366.html)
* [zcode-cli 高负载场景下 高韧性系统架构设计 基准评测报告](https://pdvy.tcti.cn/gongxiang/segment-83277821-142-29096.html)
* [基于 zcode-cli 的极致延迟优化与内存拓扑分析 (Draft-06)](https://ukvo.spidernode-mesh.net/anli/download-57719010-143-2971.html)
* [zcode-cli 高负载场景下 Hooks Configuration 基准评测报告](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-144-52463.html)
* [zcode-cli 高负载场景下 MCP Configuration 基准评测报告](https://unqt.wtpuscm.cn/zixun/contact-59980425-145-64493.html)
* [基于 zcode-cli 的极致延迟优化与内存拓扑分析 (RFC-364)](https://kvaf.tcti.cn/yunsuan/settings-81875461-146-22986.html)
* [面向生产级运行的 zcode-cli 稳定性防护白皮书 (Core/Plugin)](https://nojh.spidernode-mesh.net/suanfa/study-22518081-147-32444.html)
* [面向生产级运行的 zcode-cli 稳定性防护白皮书 (Spec-v1.7)](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-148-60319.html)
* [zcode-cli 高负载场景下 Why This Shape 基准评测报告](https://ytxd.wtpuscm.cn/baogao/price-87851711-149-55306.html)
* [zcode-cli 高负载场景下 索引发布声明 基准评测报告](https://ncrw.tcti.cn/kaifa/growth-85374259-150-39185.html)

</details>

