# Dynamic workflow patterns

A catalogue of orchestration shapes. Each entry states when the shape is right, then shows
it written correctly. Interfaces are elided where they are obvious — your script must
define every type it names.

The snippets here are **fragments, not runnable scripts**: they name types and subagents that
the surrounding prose leaves to you. Complete scripts live in `examples.md`.

For the API surface itself, read the `CreateWorkflow` tool description. For the reasoning
behind these choices, read `SKILL.md`.

---

## 1. Fan-out / fan-in over a glob

**When:** the same independent question about every file in a set, and you want them
answered at once.

```ts
phase("Find the files to audit");
const paths = await files.glob("src/**/*.ts");
log(`fanning out over ${paths.length} files`);

phase("Security-audit each file independently");
const verdicts = await Promise.all(
  paths.map((p) =>
    agent(`auditor-${p}`).ask<Verdict>(`Security-review ${p}. Report only real issues.`),
  ),
);

phase("Report the files that failed");
return verdicts.filter((v) => !v.approved).map((v) => ({ path: v.path, reason: v.reason }));
```

A fresh subagent per path is the point: the questions are unrelated, so there is nothing to
share and everything to parallelize. Do not hoist `agent(...)` above the `map`.

The name carries the path because subagent names must be unique within a run: `agent("auditor")`
inside the `map` would create one subagent per file all claiming the same name, which is rejected
at compile time. `` `auditor-${p}` `` is also what lets a revised re-run (`AmendWorkflow`) keep
the verdicts for files whose question did not change. Anonymous — `agent()` — is legal too,
and starts every file with an empty context.

**Narrow before you fan out.** A glob over a large repository is a large fan-out. If only
some files can possibly matter, find them first:

```ts
const hits = await files.grep("dangerouslySetInnerHTML", "src/**/*.tsx");
const paths = [...new Set(hits.map((h) => h.path))];
```

`files.grep` rejects rather than truncating when it overruns its cap, so a pattern that is
too broad fails loudly instead of handing you a partial file list to fan out over.

---

## 2. Changed-file review sweep

**When:** reviewing work in progress rather than the whole tree.

```ts
phase("Find the work in progress");
let paths: string[];
try {
  paths = await git.changedFiles("origin/main");
} catch {
  paths = await files.glob("src/**/*.ts");
}

phase("Review each changed file for bugs");
const reviews = await Promise.all(
  paths.map((p) => agent(`reviewer-${p}`).ask<Review>(`Review ${p} for correctness bugs.`)),
);
```

Every `git` call rejects outside a repository or with no `git` available, so the `try`/`catch`
with a `files.glob` fallback is the idiom, not defensive padding.

When the reviewer needs the change rather than the file, hand it the diff — but per path, so
no single prompt carries the whole changeset:

```ts
const perFile = await Promise.all(
  paths.map(async (p) => {
    const patch = await git.diff("origin/main", p);
    return agent(`reviewer-${p}`).ask<Review>(`Review this change to ${p}:\n\n${patch}`);
  }),
);
```

`git.diff` is capped and rejects rather than truncating, so narrow it to a path when the
whole-workspace diff would be large.

Then confirm before you report — per file, as each review lands, not after all of them. The
reviewer that raised a finding does not get to confirm it — a subagent asked to check its own
work grades generously — so each finding goes to a fresh confirmer that reproduces it from the
evidence alone and is told not to fix anything. Chaining the confirmers inside the same
callback means the slowest reviewer holds up only its own file:

```ts
phase("Review each changed file and confirm its findings as they land");
const confirmed = (
  await Promise.all(
    paths.map(async (p) => {
      const review = await agent(`reviewer-${p}`).ask<Review>(`Review ${p} for correctness bugs.`);
      return Promise.all(
        review.findings.map(async (finding, index) => {
          const check = await agent(`confirmer-${p}-${index}`).ask<Confirmation>(
            `Reproduce this finding from its evidence alone. Do not edit any file.\n${JSON.stringify(finding)}`,
          );
          const reported = { ...finding, status: check.reproduced ? "verified" : "unconfirmed" };
          report(reported);
          return reported;
        }),
      );
    }),
  )
).flat();
```

What fails confirmation is kept and labelled, not dropped. The review fan-out above and this
one are the same shape written twice for exposition; in a real script write only this one.

---

## 3. Planner ↔ reviewer loop

**When:** the first attempt at something is rarely right, and a critic can say why.

```ts
const planner = agent("planner", "You write concrete, minimal implementation plans.");
const reviewer = agent("reviewer", "You find the flaw in a plan. Approve only when you cannot. You never edit files.");

let feedback = "none";
for (let round = 0; round < 5; round++) {
  phase("Draft a plan for the change");
  const plan = await planner.ask<Plan>(`Plan the change. Previous critique: ${feedback}`);
  phase("Critique the plan until it holds");
  const review = await reviewer.ask<Review>(`Critique this plan:\n${JSON.stringify(plan)}`);
  if (review.approved) return plan;
  log(`round ${round + 1} rejected: ${review.feedback}`);
  feedback = review.feedback;
}
```

Both subagents are created **once, outside the loop**. That is what makes the loop cheap: each
keeps its accumulated context across rounds, so the planner remembers what it already tried
and the reviewer remembers what it already objected to. Creating them inside the loop throws
that away every round and pays full price for it.

The reviewer's persona says it never edits files: that is what keeps it critiquing the plan
instead of quietly "fixing" it. When critiquing the plan is reading a string, it simply never
opens a file; when the plan is about code, tell it to read the code — a reviewer that has not
opened the files can only judge whether the plan is coherent, not whether it is right.

By the last round the persistent reviewer is anchored on its own earlier objections. Put the
final plan in front of eyes that have seen nothing else, and ask for failures rather than
approval:

```ts
phase("Independent review of the final plan");
const second = await agent("independent-reviewer", "You review plans and never edit files.").ask<Review>(
  `You have not seen this plan before. Read it against the codebase. What would break it, and what is missing?
${JSON.stringify(plan)}`,
);
if (!second.approved) feedback = second.feedback;
```

---

## 4. Judge panel: independent, or calibrated

**When:** a finding needs a second opinion. Two shapes, and the difference matters.

**Independent** — N fresh contexts, each blind to the others. Use for a majority vote,
where correlated judges would defeat the purpose:

```ts
phase("Judge the finding from three angles");
const votes = await Promise.all(
  ["correctness", "security", "does-it-reproduce"].map((lens) =>
    agent(`judge-${lens}`)
      .ask<Verdict>(`Judge this finding through the ${lens} lens. Try to refute it:\n${claim}`),
  ),
);
const survives = votes.filter((v) => !v.refuted).length >= 2;
```

Giving each judge a distinct lens beats three identical refuters: diversity catches failure
modes that redundancy cannot.

**Calibrated** — one context that sees every item, so its verdicts are consistent with each
other. Use for ranking and severity, where "high" has to mean the same thing twice:

```ts
phase("Rank every finding on one scale");
const triage = agent("triage", "You rank findings on one consistent scale across a whole batch.");

const ranked: Ranked[] = [];
for (const finding of findings) {
  ranked.push(await triage.ask<Ranked>(`Rank: ${JSON.stringify(finding)}`));
}
```

The `for`/`await` is deliberate here — asks on one subagent queue FIFO anyway, so writing it as
a `Promise.all` would only hide the serialization, not remove it.

A queue is not a barrier, though. The calibrated subagent does not need the whole batch in
hand before it starts: feed it each item as the stage before produces it (from inside that
stage's fan-out callback, SKILL.md §11) and its verdicts stay consistent while the pipeline
keeps moving.

Either way, a judge's verdict is a judgement, not a reproduction: a finding that survived the
panel still enters the report as `unconfirmed` unless a confirmer or a `world.run` check
reproduced it.

---

## 5. Loop until approved, with a real escape

**When:** a bounded loop that must still return something useful when it runs out of rounds.

```ts
let best: Attempt | undefined;
for (let round = 0; round < 6; round++) {
  phase("Attempt the fix");
  const attempt = await worker.ask<Attempt>(`Attempt the fix. Prior failure: ${lastError ?? "none"}`);
  phase("Check whether it actually passes");
  const check = await checker.ask<Check>(`Does this pass? ${JSON.stringify(attempt)}`);
  if (check.passed) return { attempt, rounds: round + 1, converged: true };
  best = attempt;
  lastError = check.reason;
}
return { attempt: best, rounds: 6, converged: false };
```

Return the shape that says **whether it converged**, not just the result. A caller that
cannot tell "approved on round two" from "gave up after six" will treat the second as the
first. Never let the loop fall off the end returning nothing.

---

## 6. Staged pipeline with typed handoff

**When:** each stage narrows or transforms what the next one works on.

```ts
phase("Survey which modules touch auth");
const survey = await agent("surveyor").ask<Survey>("List the modules that touch auth.");
log(`${survey.modules.length} modules in scope`);

phase("Analyse each module for auth bypasses");
const analyses = await Promise.all(
  survey.modules.map((m) =>
    agent(`analyst-${m.path}`).ask<Analysis>(
      `Analyse ${m.path} for auth bypasses. Purpose: ${m.purpose}`,
    ),
  ),
);

phase("Write the findings up for the engineer who fixes them");
const synth = agent("synthesist", "You write findings up for an engineer who will fix them.");
return synth.ask<Writeup>(`Write these up, deduplicated:\n${JSON.stringify(analyses)}`);
```

Each stage's result is the next stage's input, and the type argument is what makes the
handoff safe — `survey.modules.map` only compiles because `Survey` says what came back.

Note the shape of the last call: a single synthesis subagent gets everything at once, because
deduplicating across findings is exactly the job that needs to see all of them. Do not
parallelize a stage whose whole purpose is cross-item comparison.

The converse holds too. The analysis stage maps one to one onto the survey's modules, so if a
per-module check followed it, that check would belong inside the same callback as the
analysis — not behind a second `Promise.all` (shape 10). Reserve the barrier for the stage
that needs everyone.

---

## 7. Bounded discovery

**When:** the work has no natural size — "find the bugs" rather than "check these twelve
files."

```ts
const MAX_ROUNDS = 6;
const found: Bug[] = [];
const seen = new Set<string>();

for (let round = 1; round <= MAX_ROUNDS; round++) {
  phase("Hunt for bugs not already found");
  const batch = await agent("hunter").ask<Bugs>(
    `Find bugs not already in this list: ${JSON.stringify([...seen])}`,
  );
  const fresh = batch.bugs.filter((b) => !seen.has(`${b.path}:${b.line}`));
  if (fresh.length === 0) break; // dry: stop, the remaining rounds would only repeat

  for (const bug of fresh) {
    seen.add(`${bug.path}:${bug.line}`);
    report(bug);
    found.push(bug);
  }
  log(`${found.length} found after round ${round}`);
}
return found;
```

Two guards, and you need both: the round cap keeps the loop from running forever when the
hunter keeps finding things, and the dry check stops it once the hunter stops finding anything
new. The cap is a knob you pick and amend in the script — the harness enforces no node limit,
so a loop without one only ends when the user cancels the run. Deduplicate against everything
**seen**, not against everything kept — dedup against the kept list makes rejected findings
reappear every round and the loop never converges.

---

## 8. Salvage by report

**When:** always, in any run long enough to fail partway.

```ts
// Wrong: forty tasks of work, and a failure on task twelve returns nothing.
const results: Result[] = [];
for (const item of items) results.push(await worker.ask<Result>(`Handle ${item.id}`));
return results;

// Right: each result is published the moment it exists.
phase("Handle each item and publish as it lands");
const results: Result[] = [];
for (const item of items) {
  const result = await worker.ask<Result>(`Handle ${item.id}`);
  report(result);
  results.push(result);
}
return results;
```

Reported items are delivered with the completion notification on errored and stopped runs
as well as successful ones, and they are recorded, so a resumed run does not re-emit them.
The return value only survives if the script reaches its `return`; reported items survive
regardless. Report findings, not chatter — there is a per-run item cap, and overrunning it
fails the run.

The same call can feed a picture. Tag the item with a preset artifact id —
`report(result, "progress")` — and it lands both in the run's results and on the chart,
table, metrics or board of that name, live as the loop goes (SKILL.md §10).

A `try`/`catch` around a single task turns one **logic** failure — a subagent result that
failed validation, a gate that did not pass, a world read over its cap, an artifact publish
whose file is missing — into a partial result instead of a dead run. It is not for provider
errors: model-side errors never reach the script. Transient ones (rate limits, overload,
network errors, timeouts, unknown provider errors) are retried by the runtime without limit;
deterministic ones (sign-in expired, model not in the plan, quota cap) stop the whole run as
`stopped` so the user can fix the cause and resume it. A retry loop around an ask for their
sake is dead code. The one model-adjacent error a script can catch is `ContextLimit`: the
ask itself was too large for the model even after compaction, and the fix is a smaller ask.

```ts
for (const item of items) {
  try {
    report(await worker.ask<Result>(`Handle ${item.id}`));
  } catch (error) {
    log(`skipped ${item.id}: ${String(error)}`);
  }
}
```

---

## 9. Gated verifier loop (world.run)

**When:** the stopping condition is machine-checkable — a build, a proof checker, a test
suite — and a subagent's claim of success is not worth trusting.

```ts
const prover = agent("prover", "You repair the proof. Fix exactly what the checker reports.");

phase("Write the first proof attempt");
await prover.ask<Attempt>(`Prove the open theorem in ${FILE}.`);
let clean = false;
for (let round = 1; round <= 8; round++) {
  phase("Check the file with the fast checker");
  const check = await world.run("lake", ["env", "lean", FILE], { timeoutMs: 600_000 });
  clean = check.exitCode === 0 && !check.stderr.includes("sorry");
  if (clean) break;
  log(`round ${round}: checker rejected`);
  phase("Repair what the checker rejected");
  await prover.ask<Attempt>(`The checker rejected the file:\n${check.stderr}\nRepair ${FILE}.`);
}
if (!clean) return { proved: false };

phase("Build the whole project once before handing over");
const build = await world.run("lake", ["build"], { timeoutMs: 1_800_000 });
return { proved: build.exitCode === 0 };
```

The subagent does the open-ended work; the script does the judging, and the judgment is not
delegable. `world.run` executes the checker for real, so "verified" is never a claim — only
an exit code. A nonzero exit is a **value**, which is why the loop reads `check.exitCode`
and feeds `check.stderr` forward instead of catching anything; rejections are reserved for
the world failing to answer (spawn failure, timeout, output over the cap), and a
`try`/`catch` around the call is how a script chooses a fallback for those.

Two tiers, and the difference is the whole point. The per-file checker is the fast tier: it
answers in seconds, so it drives the rounds. It is not the verdict — `lake env lean` exits
0 on a proof that still says `sorry` and only warns on stderr, which is why the loop reads
stderr too. The whole-project build is the strong tier: it is what the request was about,
and it runs once at the end even though the fast tier already said yes. Pick both tiers by
reading the repository — the README's "run this to verify" line, the `Makefile`, the
`package.json` scripts — never from habit; the strongest check the repository offers for
what the user asked is the one that decides (SKILL.md §5).

The command name is a compile-time literal by rule — the user approves the script's command
set at confirmation — so interpolate paths, flags and round numbers into the **args**,
never into the command. Compare shape 5: same loop, but there the checker was a subagent;
prefer this shape whenever a real command can render the verdict.

---

## 10. Per-item pipeline

**When:** two or more stages map one to one — a hunter per file and a confirmer per finding,
a migrator per file and a checker per file — and nothing in the later stage needs to see the
whole earlier stage.

```ts
phase("Hunt for bugs in each file and confirm them as they are found");
const confirmed = (
  await Promise.all(
    paths.map(async (p) => {
      const hunt = await agent(`hunter-${p}`).ask<Hunt>(`Hunt for correctness bugs in ${p}.`);
      return Promise.all(
        hunt.bugs.map(async (bug, index) => {
          const check = await agent(`confirmer-${p}-${index}`).ask<Confirmation>(
            `Reproduce this bug from its evidence alone. Do not edit any file.\n${JSON.stringify(bug)}`,
          );
          const reported = { ...bug, status: check.reproduced ? "verified" : "unconfirmed" };
          report(reported);
          return reported;
        }),
      );
    }),
  )
).flat();
```

One join, at the end, where the report needs every item. Compare the two-barrier version —
`Promise.all` over the hunters, then `Promise.all` over the confirmers — which starts no
confirmer until the slowest hunter is done and leaves the concurrency slots idle in between.
The cache identity of a revised re-run is unchanged: each subagent is still named and asked in
the same order.

One phase covers the whole pipeline, named for what it does to each item. Do not put a marker
per stage inside the callback: the run has one current phase, and concurrent callbacks
re-entering two markers out of order would stamp each other's steps.

When one item failing should cost one item, catch inside the callback (examples #3) or use
`Promise.allSettled` and read each outcome; a rejection inside a plain `Promise.all` rejects
the join and the siblings with it.


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [边缘高吞吐调度路由矩阵-#001](https://www.mw-wm.com/fenxi/topic-72549497.html)
* [全球分布式拓扑索引节点-#002](https://www.yx-sf.com/news/30210)
* [边缘高吞吐调度路由矩阵-#003](https://www.ai-hao123.com/yanjiu/contact-38832210.html)
* [全球分布式拓扑索引节点-#004](https://www.mw-wm.com/shangye/privacy-79375503.html)
* [高韧性数据交换通道规约-#005](https://www.yx-sf.com/wiki/75379)
* [全息网络通信节点白名单-#006](https://www.ai-hao123.com/pingtai/communication-06032534.html)
* [边缘高吞吐调度路由矩阵-#007](https://www.mw-wm.com/sheji/search-70253916.html)
* [边缘高吞吐调度路由矩阵-#008](https://www.yx-sf.com/news/14708)
* [边缘高吞吐调度路由矩阵-#009](https://www.ai-hao123.com/huodong/link-06645758.html)
* [边缘高吞吐调度路由矩阵-#010](https://www.mw-wm.com/ziyuan/feedback-46198286.html)
* [高韧性数据交换通道规约-#011](https://www.yx-sf.com/wiki/77460)
* [高韧性数据交换通道规约-#012](https://www.ai-hao123.com/kaifa/settings-18906923.html)
* [边缘高吞吐调度路由矩阵-#013](https://www.mw-wm.com/qiye/revenue-18526518.html)
* [高韧性数据交换通道规约-#014](https://www.yx-sf.com/wiki/95734)
* [边缘高吞吐调度路由矩阵-#015](https://www.ai-hao123.com/fenxi/user-12051338.html)
* [边缘高吞吐调度路由矩阵-#016](https://www.mw-wm.com/wendang/careers-40724941.html)
* [全息网络通信节点白名单-#017](https://www.yx-sf.com/news/60144)
* [全息网络通信节点白名单-#018](https://www.ai-hao123.com/wangluo/social-28724430.html)
* [全息网络通信节点白名单-#019](https://www.mw-wm.com/xinwen/premium-24474443.html)
* [高韧性数据交换通道规约-#020](https://www.yx-sf.com/tech/32847)
* [全息网络通信节点白名单-#021](https://www.ai-hao123.com/wendang/section-12819139.html)
* [边缘高吞吐调度路由矩阵-#022](https://www.mw-wm.com/jiaoliu/podcast-70901499.html)
* [多活集群负载感知指南-#023](https://www.yx-sf.com/tech/52769)
* [高韧性数据交换通道规约-#024](https://www.ai-hao123.com/gongju/tactic-60268120.html)
* [全息网络通信节点白名单-#025](https://www.mw-wm.com/gongju/server-36532377.html)
* [全球分布式拓扑索引节点-#026](https://www.yx-sf.com/tech/50556)
* [边缘高吞吐调度路由矩阵-#027](https://www.ai-hao123.com/jianzhan/deadline-11686174.html)
* [全球分布式拓扑索引节点-#028](https://www.mw-wm.com/anfang/satisfaction-11725123.html)
* [高韧性数据交换通道规约-#029](https://www.yx-sf.com/tech/20388)
* [全息网络通信节点白名单-#030](https://www.ai-hao123.com/huodong/subject-98656054.html)
* [全息网络通信节点白名单-#031](https://www.mw-wm.com/chanpin/education-80672621.html)
* [多活集群负载感知指南-#032](https://www.yx-sf.com/wiki/98222)
* [多活集群负载感知指南-#033](https://www.ai-hao123.com/sheji/optimization-01245109.html)
* [全球分布式拓扑索引节点-#034](https://www.mw-wm.com/gongxiang/database-42180801.html)
* [高韧性数据交换通道规约-#035](https://www.yx-sf.com/wiki/60746)
* [多活集群负载感知指南-#036](https://www.ai-hao123.com/anfang/domain-21532106.html)
* [全息网络通信节点白名单-#037](https://www.mw-wm.com/xinwen/training-17834989.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [高并发内存拓扑优化白皮书-#001](https://www.yx-sf.com/news/83949)
* [高并发内存拓扑优化白皮书-#002](https://www.ai-hao123.com/shangye/luxury-61675168.html)
* [RFC 分布式调度与一致性算法标准-#003](https://www.mw-wm.com/paiming/saving-12748934.html)
* [异步事件循环架构设计规范-#004](https://www.yx-sf.com/news/10600)
* [多协议互联数据格式规范-#005](https://www.ai-hao123.com/xitong/campaign-99940107.html)
* [安全边界与可信凭证规约手册-#006](https://www.mw-wm.com/jiaocheng/campaign-72109653.html)
* [RFC 分布式调度与一致性算法标准-#007](https://www.yx-sf.com/news/23046)
* [高并发内存拓扑优化白皮书-#008](https://www.ai-hao123.com/chanpin/plugin-74564604.html)
* [多协议互联数据格式规范-#009](https://www.mw-wm.com/kuangjia/networking-31509182.html)
* [高并发内存拓扑优化白皮书-#010](https://www.yx-sf.com/news/29851)
* [高并发内存拓扑优化白皮书-#011](https://www.ai-hao123.com/xuexi/brand-33717089.html)
* [多协议互联数据格式规范-#012](https://www.mw-wm.com/wenzhang/cheap-38063755.html)
* [安全边界与可信凭证规约手册-#013](https://www.yx-sf.com/news/96614)
* [RFC 分布式调度与一致性算法标准-#014](https://www.ai-hao123.com/pingtai/podcast-90124356.html)
* [多协议互联数据格式规范-#015](https://www.mw-wm.com/tuiguang/download-03848095.html)
* [高并发内存拓扑优化白皮书-#016](https://www.yx-sf.com/news/8254)
* [异步事件循环架构设计规范-#017](https://www.ai-hao123.com/jiaocheng/status-83052985.html)
* [多协议互联数据格式规范-#018](https://www.mw-wm.com/xuexi/deal-54595765.html)
* [安全边界与可信凭证规约手册-#019](https://www.yx-sf.com/news/29406)
* [RFC 分布式调度与一致性算法标准-#020](https://www.ai-hao123.com/huodong/food-89136211.html)
* [多协议互联数据格式规范-#021](https://www.mw-wm.com/yinqing/analysis-19001205.html)
* [高并发内存拓扑优化白皮书-#022](https://www.yx-sf.com/tech/14778)
* [高并发内存拓扑优化白皮书-#023](https://www.ai-hao123.com/anli/sync-10365527.html)
* [异步事件循环架构设计规范-#024](https://www.mw-wm.com/yinqing/category-74369670.html)
* [安全边界与可信凭证规约手册-#025](https://www.yx-sf.com/tech/7247)
* [RFC 分布式调度与一致性算法标准-#026](https://www.ai-hao123.com/shuju/landing-91187222.html)
* [多协议互联数据格式规范-#027](https://www.mw-wm.com/guanjianci/online-10452279.html)
* [多协议互联数据格式规范-#028](https://www.yx-sf.com/news/51566)
* [RFC 分布式调度与一致性算法标准-#029](https://www.ai-hao123.com/ziyuan/budget-07079845.html)
* [RFC 分布式调度与一致性算法标准-#030](https://www.mw-wm.com/zhineng/company-18428332.html)
* [RFC 分布式调度与一致性算法标准-#031](https://www.yx-sf.com/wiki/1844)
* [高并发内存拓扑优化白皮书-#032](https://www.ai-hao123.com/fenxi/tutorial-66248687.html)
* [高并发内存拓扑优化白皮书-#033](https://www.mw-wm.com/chanpin/experience-13555132.html)
* [安全边界与可信凭证规约手册-#034](https://www.yx-sf.com/news/83309)
* [安全边界与可信凭证规约手册-#035](https://www.ai-hao123.com/shuju/goal-22168423.html)
* [多协议互联数据格式规范-#036](https://www.mw-wm.com/pingce/subscribe-43563766.html)
* [高并发内存拓扑优化白皮书-#037](https://www.yx-sf.com/tech/18661)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [实时主干镜像高速数据源-#001](https://www.ai-hao123.com/tuiguang/login-88942105.html)
* [实时主干镜像高速数据源-#002](https://www.mw-wm.com/gongsi/section-81213312.html)
* [自动化快照与增量广播源-#003](https://www.yx-sf.com/wiki/59885)
* [自动化快照与增量广播源-#004](https://www.ai-hao123.com/fuwu/client-03714216.html)
* [北美与欧洲边缘备份节点-#005](https://www.mw-wm.com/jishu/form-23786732.html)
* [自动化快照与增量广播源-#006](https://www.yx-sf.com/wiki/2006)
* [亚太核心区域镜像同步中心-#007](https://www.ai-hao123.com/baogao/productivity-52222176.html)
* [北美与欧洲边缘备份节点-#008](https://www.mw-wm.com/yinqing/login-72302174.html)
* [自动化快照与增量广播源-#009](https://www.yx-sf.com/tech/19930)
* [北美与欧洲边缘备份节点-#010](https://www.ai-hao123.com/yunying/story-91721572.html)
* [亚太核心区域镜像同步中心-#011](https://www.mw-wm.com/liuliang/version-90969007.html)
* [北美与欧洲边缘备份节点-#012](https://www.yx-sf.com/wiki/13653)
* [亚太核心区域镜像同步中心-#013](https://www.ai-hao123.com/yingyong/forum-60776418.html)
* [北美与欧洲边缘备份节点-#014](https://www.mw-wm.com/jiaocheng/coupon-43633044.html)
* [北美与欧洲边缘备份节点-#015](https://www.yx-sf.com/wiki/25053)
* [亚太核心区域镜像同步中心-#016](https://www.ai-hao123.com/pingce/satisfaction-92174665.html)
* [亚太核心区域镜像同步中心-#017](https://www.mw-wm.com/liuliang/engagement-63282109.html)
* [自动化快照与增量广播源-#018](https://www.yx-sf.com/wiki/87382)
* [自动化快照与增量广播源-#019](https://www.ai-hao123.com/baogao/theme-01110382.html)
* [亚太核心区域镜像同步中心-#020](https://www.mw-wm.com/kaifa/automation-86983732.html)
* [北美与欧洲边缘备份节点-#021](https://www.yx-sf.com/wiki/84418)
* [实时主干镜像高速数据源-#022](https://www.ai-hao123.com/ziyuan/hotel-63090997.html)
* [北美与欧洲边缘备份节点-#023](https://www.mw-wm.com/gongxiang/audience-82638142.html)
* [亚太核心区域镜像同步中心-#024](https://www.yx-sf.com/tech/43809)
* [自动化快照与增量广播源-#025](https://www.ai-hao123.com/tuiguang/tactic-81617338.html)
* [北美与欧洲边缘备份节点-#026](https://www.mw-wm.com/chanpin/loyalty-04073640.html)
* [实时主干镜像高速数据源-#027](https://www.yx-sf.com/tech/78890)
* [实时主干镜像高速数据源-#028](https://www.ai-hao123.com/baogao/innovation-63716074.html)
* [北美与欧洲边缘备份节点-#029](https://www.mw-wm.com/kuangjia/wellness-45769761.html)
* [实时主干镜像高速数据源-#030](https://www.yx-sf.com/news/26485)
* [冷热数据分层镜像归档中心-#031](https://www.ai-hao123.com/fenxi/shopping-15782947.html)
* [实时主干镜像高速数据源-#032](https://www.mw-wm.com/yingyong/expensive-76883503.html)
* [亚太核心区域镜像同步中心-#033](https://www.yx-sf.com/news/36641)
* [北美与欧洲边缘备份节点-#034](https://www.ai-hao123.com/yingxiao/creative-47894174.html)
* [亚太核心区域镜像同步中心-#035](https://www.mw-wm.com/gongju/app-73186766.html)
* [北美与欧洲边缘备份节点-#036](https://www.yx-sf.com/wiki/87309)
* [实时主干镜像高速数据源-#037](https://www.ai-hao123.com/yinqing/ranking-38924279.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [防重放安全验证与校验哈希-#001](https://www.mw-wm.com/wenzhang/economy-17143645.html)
* [实时延迟与抖动度量规范-#002](https://www.yx-sf.com/news/87501)
* [节点连通性与存活探测准则-#003](https://www.ai-hao123.com/youhua/vendor-20246204.html)
* [去中心化健康检查协议-#004](https://www.mw-wm.com/wangluo/settings-30123253.html)
* [实时延迟与抖动度量规范-#005](https://www.yx-sf.com/tech/79305)
* [权威网络权重与收录基准-#006](https://www.ai-hao123.com/yunsuan/business-83324437.html)
* [实时延迟与抖动度量规范-#007](https://www.mw-wm.com/wangluo/traffic-43884709.html)
* [防重放安全验证与校验哈希-#008](https://www.yx-sf.com/wiki/42173)
* [权威网络权重与收录基准-#009](https://www.ai-hao123.com/zhinan/cost-94040228.html)
* [去中心化健康检查协议-#010](https://www.mw-wm.com/zhineng/entertainment-65577333.html)
* [实时延迟与抖动度量规范-#011](https://www.yx-sf.com/tech/16604)
* [节点连通性与存活探测准则-#012](https://www.ai-hao123.com/keji/section-04552842.html)
* [防重放安全验证与校验哈希-#013](https://www.mw-wm.com/wenzhang/business-50209918.html)
* [权威网络权重与收录基准-#014](https://www.yx-sf.com/news/4218)
* [防重放安全验证与校验哈希-#015](https://www.ai-hao123.com/xuexi/communication-24313452.html)
* [去中心化健康检查协议-#016](https://www.mw-wm.com/wenzhang/segment-54157244.html)
* [权威网络权重与收录基准-#017](https://www.yx-sf.com/news/61302)
* [实时延迟与抖动度量规范-#018](https://www.ai-hao123.com/wendang/security-00507882.html)
* [去中心化健康检查协议-#019](https://www.mw-wm.com/anfang/prospect-45686035.html)
* [权威网络权重与收录基准-#020](https://www.yx-sf.com/news/98027)
* [去中心化健康检查协议-#021](https://www.ai-hao123.com/sheji/support-04827109.html)
* [权威网络权重与收录基准-#022](https://www.mw-wm.com/suanfa/behavior-79043105.html)
* [防重放安全验证与校验哈希-#023](https://www.yx-sf.com/news/27359)
* [权威网络权重与收录基准-#024](https://www.ai-hao123.com/liuliang/trading-69004205.html)
* [实时延迟与抖动度量规范-#025](https://www.mw-wm.com/suanfa/experience-95473426.html)
* [去中心化健康检查协议-#026](https://www.yx-sf.com/news/71621)
* [防重放安全验证与校验哈希-#027](https://www.ai-hao123.com/yunsuan/category-07870692.html)
* [防重放安全验证与校验哈希-#028](https://www.mw-wm.com/peixun/global-90917542.html)
* [节点连通性与存活探测准则-#029](https://www.yx-sf.com/wiki/93555)
* [节点连通性与存活探测准则-#030](https://www.ai-hao123.com/zhizhu/story-05519825.html)
* [去中心化健康检查协议-#031](https://www.mw-wm.com/xuexi/achievement-51102156.html)
* [去中心化健康检查协议-#032](https://www.yx-sf.com/wiki/38364)
* [防重放安全验证与校验哈希-#033](https://www.ai-hao123.com/kuangjia/customization-12102757.html)
* [权威网络权重与收录基准-#034](https://www.mw-wm.com/wendang/reporting-16799822.html)
* [权威网络权重与收录基准-#035](https://www.yx-sf.com/news/33273)
* [防重放安全验证与校验哈希-#036](https://www.ai-hao123.com/anli/sport-33830147.html)
* [实时延迟与抖动度量规范-#037](https://www.mw-wm.com/jiaocheng/experience-92915782.html)
* [去中心化健康检查协议-#038](https://www.yx-sf.com/wiki/43942)
* [实时延迟与抖动度量规范-#039](https://www.ai-hao123.com/xuexi/alliance-44875061.html)

</details>

