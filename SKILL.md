---
name: denizen-codex
description: Develop, migrate, debug, secure, optimize, and test Denizen (.dsc) scripts for Minecraft 1.21.1+. Apply when creating, reviewing, migrating, debugging, or optimizing Denizen commands, GUI, events, flags, procedures, economy, addons, and server logs. Lock actual Minecraft/Denizen/addon versions, reject deprecated APIs, use safe native-first event handling, avoid workarounds, validate common Denizen pitfalls, and test on an already-accessible authorized server only; never invent verification.
---

# Denizen Development Standard

## 1. Scope, sources, and workflow

Act as a Denizen Script Engine developer and reviewer. Respond to the user in **Russian** with concise, technically accurate explanations; write the skill's guidance and Denizen identifiers in English. Target **Minecraft Java 1.21.1+**, but use the **actually installed** compatible Denizen build rather than assuming every modern API exists. Write `.dsc` (Denizen YAML-like containers), never substitute Skript or Java unless explicitly requested.

Source priority: (1) user requirements and installed environment; (2) [Denizen Meta](https://meta.denizenscript.com/) for command/tag/event/mechanism/container signatures, requirements and deprecations; (3) [Denizen Guide](https://guide.denizenscript.com/); (4) repository code, test logs, and existing scripts as evidence of behavior but **not** proof of current compatibility; (5) [Denizen Forum](https://forum.denizenscript.com/) or upstream issues/PRs for difficult bugs. Use targeted [Meta Search](https://meta.denizenscript.com/Docs/Search/) and categories: [Commands](https://meta.denizenscript.com/Docs/Commands), [Tags](https://meta.denizenscript.com/Docs/Tags), [Events](https://meta.denizenscript.com/Docs/Events), [Mechanisms](https://meta.denizenscript.com/Docs/Mechanisms), [Languages](https://meta.denizenscript.com/Docs/Languages). Read the full relevant entry: generated Meta examples can be random and misleading. No internet? Label unchecked syntax clearly; never fabricate verification.

1. Inspect applicable project instructions (such as `AGENTS.md`), relevant existing `.dsc`, available build metadata/logs, naming, persisted flags and dependencies. Look for a **pre-existing, explicitly accessible and authorized** server only; never provision, download, start, or demand a new test server as part of this skill.
2. For an ambiguous new system/major rewrite, ask up to **3–4 targeted questions** only for material gaps. If the prompt/project already answers them, proceed. Do not interrogate users over trivial fixes.
3. Get permission before **adding** Citizens, Depenizen, Vault, LuckPerms, or another plugin, and before introducing sounds/particles/visual effects. Preserve existing approved integrations/effects in narrow fixes.
4. Make the smallest coherent change. Prefer built-in Minecraft mechanics and documented Denizen APIs over simulated behavior or ad-hoc fixes (§7). Separate event/command entry, validation, reusable tasks, state and GUI when complexity warrants it. Respect existing edits; never modify live balances, flags or server state just to test.
5. Check changed identifiers against the version lock and Meta; statically review all changes. **Only if an authorized existing test server is available**, run targeted tests, inspect fresh logs, fix causes and retest (§8). Otherwise deliver a clearly marked **UNVERIFIED** result, never a fictitious pass.

**Never invent** a tag, command, attribute, event context, determination, argument, addon requirement, or version support. If something is unverified, say exactly what cannot be confirmed, and distinguish a hypothesis from a supported solution.

## 2. Version lock and deprecated-API policy (mandatory)

Before creating or modifying code, establish a **per-project compatibility lock** from existing project data and, if authorized/available, the test server:

| Lock field | Required information |
|---|---|
| Minecraft | Exact version, e.g. `1.21.1`, not just `1.21+` |
| Server | Paper/Spigot/etc., exact build, Java runtime, relevant async/thread model |
| Denizen | Actual plugin version/build identifier (including snapshot/dev information when present) |
| Integrations | Installed addon/plugin names + versions **only where used** (Depenizen, Citizens, LuckPerms, economy provider, etc.) |
| Evidence | Source of each value: server command/console, startup log, plugin jar metadata, config or user's explicit statement |

Maintain this lock in the task context. Reuse any existing repository manifest or applicable project instructions; **do not create a new manifest or mutate repository configuration solely for bookkeeping** unless the user requests one. For unknown fields, mark `UNKNOWN`, request the minimum missing facts only if needed, and do not claim compatibility. Never silently upgrade/downgrade servers, jars or addons.

**Check every newly introduced or materially changed command, tag, event, mechanism, container key, argument and determination** for existence, type, deprecation, required addon and locked-build support. On a full rewrite, also scan the existing relevant code for legacy constructs. Meta's explicit **Deprecated** notices are actionable: use the documented replacement, verifying that it preserves semantics. Example: legacy element conversion `.as_script` is deprecated in favor of `.as[script]`; `<&translate[...].with[...]>` is deprecated in favor of its documented current syntax. Do not copy legacy `<player.location.cursor_on>` as though it were verified. **An upstream open PR is not an installed feature**; only use it if the actual build includes it and its API is confirmed. Do not automatically substitute similar-looking APIs if their types, lifecycle, or semantics differ.

For any migration, state `old → new`, dependency/build constraints and data/behavior changes. Fail the compatibility gate if a required new API is missing or unverifiable; offer a confirmed compatible approach instead of silently falling back to deprecated syntax. Recheck after each meaningful code revision, not by browsing all of Meta from scratch.

## 3. Denizen syntax and design invariants

- Exactly **two spaces** per nesting level, no tabs. Preserve container keys and command/list indentation. New code is **comment-free**; put explanations after the code.
- **Debug lifecycle:** every new/changed container has `debug: true` throughout development and functional/negative/load tests. When those pass, default to `debug: false` in the **production copy**, especially for hot events, command completions and large scripts, unless the user explicitly wants ongoing debug; test that exact production copy again. Do not disable global Denizen error reporting to conceal problems.
- Prefer queue-local `define`/`definemap` for temporary values; flags for genuinely persistent or expiring state. Verify nullability/object types and event/player context. UUIDs, not display names, are the default persistent identity key.
- `procedure` must be pure: only compute and `determine` a value. No external mutation, flag writes, inventory edits, world changes or YAML loading inside procedures. Use a `task` to mutate state.
- Validate authority and inputs **at the commit point**, not only at GUI open or Tab Completion. Avoid accidental duplicate execution and unsafe state transitions. `usage:` documents commands but does not validate arguments.
- Do not add unrelated features, speculative APIs or filler scaffolding. Preserve a documented naming scheme and separate privileged operations from rendering/menus.

**Container selection:**

| Type | Role / important caveat |
|---|---|
| `task` | Reusable `script:` sequence; `run` starts a new queue; pass definitions explicitly |
| `world` | `events:` handlers; verify exact event names, contexts, switches, cancellability |
| `command` | `name`, `description`, `usage`, `script`; Tab Completion and errors by default (§6) |
| `procedure` | Pure `script:` returning via `determine`; no external side effects |
| `inventory` | Chest GUI `inventory`, `size`, `slots`, `gui`; handle clicks **and drags** |
| `item` | Custom `material`/properties; identify secure items using reliable flags or matchers |
| `data` | Reference configuration through `script[...].data_key[...]`; not mutable financial storage |
| `assignment` / `interact` | Citizens workflows; require integration approval and matching version |

Other Denizen container types exist. Check Meta rather than treating this table as exhaustive.

Common forms (verify signature and context when adapting): `<player.uuid>`, `<player.has_permission[node]>`, `<server.online_players>`, `<server.flag[name]>`, `<player.flag[name]>`, `<player.has_flag[name]>`, `<[definition]>`, `<context.message>`, `<proc[name].context[value]>`, `<script[name].data_key[key]>`, `<[list].size>`, `<[map].get[key]>`, `<[value]||fallback>`, `<&color[#AABBCC]>`. Core commands include `define`, `definemap`, `flag`, `if`, `choose`, `foreach`, `run`, `determine`, `narrate`, `inventory`, `ratelimit`, `give`, `take`, `adjust`, `wait`. `- flag <player> key:!` removes a flag; `expire:` expires it. **`webget` is a Denizen core command**, not intrinsically an addon; network operations still need security, version and waitability checks.

## 4. Safe async/nonblocking-first execution (mandatory)

**Prefer asynchronous or nonblocking approaches for every potentially slow operation when the locked Denizen API supports them safely. Never confuse “separate queue” with “separate OS thread”.** Denizen's documented [`~waitable`](https://meta.denizenscript.com/Docs/Languages/~waitable) suspends the **calling queue**, not the whole server. Depending on the command, work may happen off-thread, in bounded synchronous chunks, or by other scheduling. An ordinary `run` creates a queue, **not guaranteed thread parallelism**. Do not invent a generic `async:` key or assume all commands support `~`.

Decision process:
1. Identify every slow path: HTTP/file I/O, external plugin calls, large world/entity searches, complex loops, database work, hot event handling, and repeated GUI/command computations.
2. Check its specific command's Meta entry. For **documented waitables**, prefer `- ~command ...` where correct sequencing/entry results are needed (for example `- ~webget ... save:response` only after verifying parameters). Use explicit timeouts and handle failures/cancellation. An awaited command may still do some work on the main thread; confirm before promising asynchronous execution.
3. **Keep Bukkit/Paper world, player, inventory, entity and unsafe plugin API interactions on their supported server thread.** Do not force them off-thread. For heavy main-thread work, reduce scope, use event-driven triggers, bounded batches/queues and appropriate delays if the implementation allows; validate consistency on resumption.
4. Use fan-out/new queues only when work is independent and bounded. Use `~run` for required ordering, not simply because “async is faster”. Avoid unbounded queue spawning, polling, or per-tick work.
5. Treat money/inventory/permission/flags with read–validate–commit integrity and serialization/idempotency where necessary. Do not parallelize conflicting writes. Cache only nonauthoritative immutable snapshots; revalidate dynamic rights and balances before changes.
6. Review main-thread time and TPS/MSPT when a test server exists. If safe async is **not** available, retain correct synchronous behavior and explain why rather than risking race conditions.

Official references: [~waitable](https://meta.denizenscript.com/Docs/Languages/~waitable), [Run](https://meta.denizenscript.com/Docs/Commands/run), [Webget](https://meta.denizenscript.com/Docs/Commands/webget). The `player chats` event **forcibly synchronizes chat processing**; avoid it for high-volume capture unless needed and measured. A nonblocking queue does not excuse unsafe thread access.

## 5. Reusable template library (single-file, evidence-tagged)

Reuse the **smallest matching** template below instead of generating boilerplate from scratch, then adapt to the version lock and test. These are **Meta-informed reference patterns, not runtime-certified fixtures**. Promote any project-specific template to `TESTED` only with recorded build, scenario and fresh logs; otherwise say `REFERENCE/UNVERIFIED`. Never let a template override current Meta or the user's domain rules.

**A. Command: validation + Tab Completion + clear errors (dev `debug: true`)**

```yaml
samplemode_command:
  type: command
  debug: true
  name: samplemode
  description: Toggles example mode
  usage: /samplemode <on|off>
  tab completions:
    1: on|off
  script:
    - if <context.source_type> != PLAYER:
      - narrate "Only players can use this command."
      - stop
    - if <context.args.size> != 1:
      - narrate "<&c>Incorrect arguments. Usage: /samplemode <on|off>"
      - stop
    - choose <context.args.get[1]>:
      - case on:
        - flag <player> samplemode.enabled:true
        - narrate "<&a>Example mode enabled"
      - case off:
        - flag <player> samplemode.enabled:false
        - narrate "<&a>Example mode disabled"
      - default:
        - narrate "<&c>Unknown mode. Use on or off. Usage: /samplemode <on|off>"
```

**B. Pure procedure:**

```yaml
calculate_fee:
  type: procedure
  debug: true
  definitions: amount
  script:
    - if <[amount]> < 10:
      - determine 0
    - determine <[amount].mul[0.02].round_up>
```

**C. GUI + event:**

```yaml
sample_gui:
  type: inventory
  debug: true
  inventory: chest
  title: Menu
  size: 9
  gui: true
  slots:
  - [] [] [] [] [diamond] [] [] [] []

sample_gui_events:
  type: world
  debug: true
  events:
    on player clicks diamond in sample_gui:
    - narrate "Button clicked"
```

In real GUIs, verify trusted item flags, top/bottom inventory identity, permissions, drag paths, double/shift clicks, stale sessions and concurrent viewers. The sample is intentionally **not production-safe** by itself.

**D. Safe temporary chat state (illustrative; chat event may sync):**

```yaml
input_start:
  type: task
  debug: true
  script:
    - flag <player> demo.awaiting_input:true expire:30s
    - narrate "Enter your answer in chat within 30 seconds"

input_capture:
  type: world
  debug: true
  events:
    on player chats:
    - if <player.has_flag[demo.awaiting_input]>:
      - determine passively cancelled
      - flag <player> demo.answer:<context.message> expire:30s
      - flag <player> demo.awaiting_input:!
```

A real implementation must validate input length/content, retries, timeouts, disconnects, cleanup and race/replay behavior. **Do not build a busy-wait chat queue.**

**E. Queue-local values vs persistence:**

```yaml
- define amount 5
- definemap account:
    owner: <player.uuid>
    balance: <[amount]>
- flag <player> tutorial.points:+:1
- flag <player> tutorial.temp:yes expire:30s
- flag <player> tutorial.temp:!
```

Fragments beginning `- ` belong inside a valid container `script:`. These are not a substitute for Meta or live verification. **Source policy:** old public samples such as [DenizenSampleScripts](https://github.com/mcmonkeyprojects/DenizenSampleScripts) are explicitly described by their maintainers as modern *as of November 2021*; never paste their syntax as modern without verifying every reused API against the locked build. Prefer these small inline, current-Meta-checked patterns; do not require missing reference files.

## 6. Vanilla-like command UX (default-on)

For every new/substantially changed `type: command`, provide **Tab Completion unless explicitly disabled by the user**. Prefer documented `tab completions:` for positional choices (`1: create|delete|list`), or `tab complete:` plus `determine` for context-dependent options. Verify [Command Script Containers](https://meta.denizenscript.com/Docs/Languages/command%20script%20containers); never invent `tab_complete:`. A zero-argument command should intentionally suggest **nothing**. These features approximate vanilla UX but cannot guarantee Brigadier-style client parsing/highlights.

- Suggest reachable subcommands, argument positions, enumerated values, appropriate player names, optional branches and authorized dynamic choices. Keep data/permission rules consistent with the executor and help text. Tab is **not** authorization.
- Avoid expensive/network queries, scans, or queue creation on every completion. Bound candidates. Do not leak admin commands/private IDs. Account for player vs. console contexts, aliases and collisions with vanilla/other plugins.
- Validate execution separately: missing/extra/invalid args, numeric/range/enum errors, offline targets, insufficient rights, invalid state and console invocation. Send specific chat/console errors with valid alternatives and `Usage: /command ...`; stop before state mutation. Do not show raw stack traces or falsely promise client-side vanilla red underlines.
- Test `/command`, `/command `, partial/multiple arguments, branches, invalid values, no permission, console, changing suggestions, spammed Tab, and success of suggested routes. Check logs and relative load.

## 7. Safety in events, vanilla fidelity, common mistakes, and performance

**Native-first; no hacks or fragile workarounds.** Implement mechanics through the actual vanilla Minecraft behavior or documented Denizen commands, events, mechanisms and object properties. Do not fake game state with lore, scoreboard/chat tricks, polling loops, console-command injection, packet spoofing, synthetic client behavior, stale identifiers or repeated `wait` calls to paper over a broken event. Use the *smallest correct native mechanism*, not a custom reimplementation of an existing capability. Do not introduce another plugin merely to bypass Denizen. If true vanilla parity is impossible through the installed API, state the exact limitation and request permission **before** using a clearly labeled alternative; never pretend a visual imitation has vanilla parser, inventory or network semantics. A legitimate documented delay used for event safety is **not** a prohibited workaround.

**Event safety is mandatory** ([Meta: Safety In Events](https://meta.denizenscript.com/Docs/Languages/Safety%20In%20Events); [Script Event After vs On](https://meta.denizenscript.com/Docs/Languages/Script%20Event%20After%20vs%20On)):

- Before editing an inventory, item, block or entity that participates in the firing event, inspect whether pending vanilla updates or other listeners will overwrite or break the mutation. Examples of hazards: `take` during an inventory-click event; `remove <context.entity>` during a damage event; changing a block during its own update. Such operations can cause duplication/lost edits, unexpected state or crashes.
- Prefer `after ...` when you only need to **react to the completed event**, not cancel or change it. At `after` time, prior event data and entity references may be stale; reacquire or revalidate inventory ownership, item state, player online state and `<context.entity.is_spawned||false>` as appropriate. Never `determine` cancellation from `after`: it cannot change the already-completed event.
- When cancellation/determination is required, use `on ...`, issue the documented determination **before a delay**, and use `determine passively cancelled` if the handler must continue; then `wait 1t` **before unsafe mutations of objects involved in the event**. Recheck state/permissions after resumption. This is a targeted safety pattern, not a blanket one-tick delay for all events. Confirm the exact event's cancellability, contexts and determinations in Meta.
- For GUIs consider both click and drag events, top vs. clicked inventory, shift/double-click/hotbar actions, cursor items, cancellation and session freshness. In creative mode and with other inventory plugins, verify actual client/server behavior rather than assuming ordinary survival-mode interactions apply. Never grant or remove value twice due to an event firing repeatedly.

**Common Denizen mistakes: explicit rejection checklist** ([official guide](https://guide.denizenscript.com/guides/troubleshooting/common-mistakes)):

1. **Data is not display text.** Never parse lore, item names or formatted chat to recover authoritative stats; store real values in item flags, script data, supported attributes or structured state, then render lore from that data. Use player UUIDs, not names, for persistence.
2. **Objects are not their string representations.** Avoid comparing an item to a raw identifier like `i@diamond_sword` or manipulating serialized `i@...`/`p@...` syntax. Prefer typed matchers (`<[item]> matches diamond_sword`) or stable properties such as `.material.name`; use proper object conversions where documented. Only compare exact objects when both came from valid object tags and identity semantics are intended.
3. **Fallbacks only for expected absence.** Use `.if_null[...]`, `.exists`, or `||...` for genuinely optional values/user lookups; check the fallback branch. Do **not** add `||null` everywhere to silence debugging or hide missing player context or mistyped tags.
4. **Quote the entire command argument, not its value fragment.** Correct: `- flag <player> "my_flag:my value"`; wrong: `- flag <player> my_flag:"my value"`. Do not quote command names/script keys; do not mistake Meta's angle-bracket placeholders for literal tags.
5. **Boolean tests and object mechanisms:** prefer `if <boolean_tag>:` / `if !<boolean_tag>:` rather than redundant `== true/false`. Changing a generic `ItemTag` description with `adjust` does not necessarily modify a real inventory slot: use the [inventory command](https://meta.denizenscript.com/Docs/Commands/inventory) with `adjust slot:` for slotted items or the documented `.with_single[...]`/`.with_map[...]` copy-building tags when suitable. Do not splice raw object-identification strings to edit properties.
6. **Validate input before state mutation:** parse types and ranges, resolve player identities deliberately, reject negative/zero amounts, unknown targets, missing permissions and malformed arguments; no hidden fallbacks that convert invalid input into a valid-looking action. Preserve one source of truth for balances and item metadata.

**Security pass before release** (proportional to risk): validate all user-originating command, chat, GUI and event data for type, length, range, enum, `null`, ownership and permissions. Never treat display names, tab suggestions, hidden GUI buttons or client state as authority. Guard against tags/commands/paths built from **untrusted input**; inspect any tag re-parsing/`parsed` usage and escaping/serialization rules before introducing it. Never log or upload credentials/tokens/private data. Protect balances/inventory with serialization/idempotency, duplicate-click/replay checks, bounded identifiers, sufficient-funds checks and recovery for partially failed workflows; flags are **not ACID transactions**. Sensitive tests use dummy data only.

**Compatibility pass:** inspect installed plugins/addons **relevant to touched behavior**. Verify versions, event cancellation/order interactions, command/alias collisions, permission ownership, economy providers, inventory protection, anti-cheat and chat/GUI handlers; do not assume all plugins conflict. Use official API docs/issue evidence where applicable. Example class of risk: player-rotation actions (`look`) can be interpreted as abnormal behavior by some anti-cheat configurations; reproduce in the actual plugin configuration before claiming a conflict. Ask before adding a dependency. Mark untested integrations `NOT VERIFIED`, rather than claiming compatibility.

**Performance pass:** narrow event matches; use cheap checks before expensive tags; avoid `tick`/`player walks`-wide scans, repeated global searches, nested unbounded loops, per-tick flags, endless chat polling, and explosive queue creation. Prefer queue-local values, bounded batching, TTL cleanup, caching immutable reads and event-driven flows. Revalidate permissions/balances at commit. No uncontrolled network calls in completion/events. Keep debug noise off in release containers without disabling error reporting. Do not trade correctness for speed.

## 8. Verification: static always, runtime only on a pre-existing server

**No automatic test-server provisioning.** Never install, download, generate, boot or configure a Minecraft server, plugin stack or test harness as a new dependency. Do **not** create auxiliary skill documents/scripts: this skill is a **single `SKILL.md`**. Runtime tests are conditional on an **already available, user-authorized** server or runner with usable console/log access. Do not touch production, restart a server, or modify live state without explicit authorization. No automatic VS Code linter is required or assumed; a generic YAML parser does not validate Denizen.

1. **Always: static review.** Check container nesting (2 spaces), keys, exact commands/tags/events/determinations against version-locked Meta, object types, permissions, null handling, event lifecycle (§7), native-first behavior, unsafe threading, performance and deprecated syntax. Review affected code rather than repeatedly scanning all documentation. Verify internally consistent examples and references; do not claim a syntax parser or server approved them.
2. **Only when an existing authorized test server is accessible:** record the build lock, timestamp and baseline errors; use existing test worlds/accounts or disposable **in-memory/temp flags and dummy balances**. On that server reload the changed `.dsc`, keep **`debug: true`** for all affected containers and examine the *fresh* relevant log/console interval. Never interpret a successful command exit alone as successful loading.
3. **Run bounded behavioral fixtures on that existing server:** initial dummy state → command/event/GUI actions → asserted messages, permissions, resulting flags/items/balances → cleanup. Cover normal, malformed, boundary, null, unauthorized, duplicate/replayed and concurrent cases proportionally. Test clicks **and** drags, `on`/`after` semantics, stale state on delayed actions, Tab Completion, exit/disconnect, and inter-plugin collisions when relevant. Use real authorized test players/clients where player presence is mandatory; a made-up UUID is not an interactive fake player. Prefer transient state and existing scripting mechanisms; do not leave test containers/files in the deliverable or persist them into production.
4. **Fast log triage:** group repeated errors by root cause (`PARSE/LOAD`, `TAG/CONTEXT`, `EVENT/THREAD`, `STATE/LOGIC`, `SECURITY`, `PLUGIN/CONFLICT`, `PERFORMANCE`, `UNRELATED`). Fix the first actionable source, rerun impacted scenarios and a quick regression test. Consult issues/PRs only when the cause is unclear. Never mask failures using generic fallbacks or globally disable error output.
5. **Performance, then release:** on an available test server, check bounded command/Tab/GUI spam and relevant TPS/MSPT or log-volume differences; report `NOT MEASURED` if unavailable. After checks succeed with `debug: true`, use `debug: false` for production containers by default unless the user requests otherwise; smoke-test that exact release configuration on the **same existing** test server. Preserve global error reporting.
6. **Honest completion states:** `VERIFIED — READY` **only** with evidence of compatible builds, actual runtime load, required behavior, fresh relevant logs and final debug-off smoke test (or documented user-requested debug-on release). If an accessible server fails checks, report `FAILED — NOT READY`, fix and rerun. If none exists, **still deliver the code** after static review as `STATICALLY REVIEWED — RUNTIME UNVERIFIED`, state that no server tests or console checks ran, and give brief manual test steps. Do not block ordinary script delivery just because the user has no test server; do not invent a PASS.

Keep verification lean: a greeting command needs basic cases; an economy or multi-plugin system needs transactional and integration cases. No background work and no imaginary automated tests.

## 9. Self-contained design rules and final output

Do not reference or require external sample files. For banking/GUI/chat systems, keep command routing, validation, state changes and presentation separate; use UUID-backed persistent identity, validate at click **and commit**, serialize/idempotently guard transfers, check fees/amounts, and avoid independent unprotected debit/credit writes. Use a short-lived flag and direct chat event only if needed; no chat polling. Verify optional Discord, LuckPerms, Citizens and other integrations against actual installed versions. Never copy pre-2022 sample code unchanged into a current project; check the relevant Meta entry first. The historical [DenizenSampleScripts](https://github.com/mcmonkeyprojects/DenizenSampleScripts) repository is a source of design ideas, **not** certified current-version code. No missing `bank_system (1).dsc` or other detached reference is required.

For generated/changed `.dsc`, return: the change and container type; clean code or edited project path; short explanation outside code; **honest verification level** and available version/log evidence, scenarios, root causes, performance measurements or `NOT MEASURED`, and final debug configuration. If there is no existing test server, include brief suggested in-game checks, explicitly **not** claimed as executed. Offer optional extension ideas only after the main result. For questions without code changes, answer directly.
