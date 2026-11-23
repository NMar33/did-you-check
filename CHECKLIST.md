# Did you check …?

58 questions, and four closing checks, for the bugs that tests and reviews often miss. They come from 657 real bugs fixed between August and November 2026 in one project written mostly by coding agents, sorted into 60 classes, and from trial runs on a Flutter app and a Python data tool. "Seen" says how often a class occurred.

## How to use this (for agents)

1. **Scope.** Default: the whole project at HEAD. List uncommitted changes separately (`git status --porcelain`) and mark each finding HEAD, working tree, or both. A diff review answers only the questions the diff touches, but greps the whole repository for Q-02. Platforms: those the README or packaging names; if none, ask, or audit the one you run on and say so.
2. **Filter.** Mark which tags below fit. A tag fits only a real part of the product: one small script page is not `ui`, a dev-only script that calls git is not `proc`. A question applies if any of its tags fits. An `all` question that names a trigger (a cache, a total) is `n/a` after one grep.
3. **Answer** each question that applies, then the checks in [Before you report done](#before-you-report-done):
   `yes` = the property holds, with evidence · `no` = it fails, with evidence · `n/a` = the trigger is absent, show the grep · `unverified` = cannot be settled from code; say what would settle it.
   Evidence is a `file:line`, a test name, or a command you ran with its output. If you cannot do a check's run, use its reading steps.
4. **Severity** of each `no`: `high` = data loss, a wrong result users rely on, a security problem, or a user stuck · `medium` = wrong behaviour with a workaround · `low` = hygiene.
5. **Report** one line per question, then every `no`, sorted by severity, with evidence and the entry's typical fix. Do not change code unless asked.

```
Q-07 no high — app/settings.py:41 returns {} on OSError; save() at :58 writes it. Fix: a failed read blocks the write. (HEAD)
Q-15 yes — app/store.py:12 takes an fcntl lock for every writer; test_store.py::test_parallel_appends runs 8 processes.
Q-34 n/a — spawns no processes (grep subprocess|spawn|exec: 0 hits).
Q-32 unverified — needs a run from a login agent; from code: the plist calls /usr/bin/python3 by absolute path.
```

Greps are for Python and JavaScript; Go, Java/Kotlin, Dart and Rust forms are added where they differ.

| Tag | The project … | Tag | The project … |
|---|---|---|---|
| `all` | any project | `ext` | calls external APIs, CLIs or models, or acts on their output |
| `store` | writes files, a database or exports | `ui` | has a UI whose state loads or saves asynchronously |
| `input` | takes input from programs: HTTP, CLI, webhooks, tool calls, imported files | `web` | has a browser UI or an HTTP server |
| `proc` | builds commands, spawns processes, or takes paths from input | `ship` | is installed on users' devices, upgraded, or runs on several OSes |
| `time` | uses dates, durations or timers | `data` | shows numbers, statistics or model scores people rely on |
| `conc` | has several processes, requests, windows or users at once | `tests` | has a test suite |
| `long` | runs for hours: a server, daemon, watcher, desktop or mobile app | `pub` | is published: a package, an app build, a public repository |

**If you only do five:** [Q-07](#q-07) a failed read written back as empty (silent data loss; one grep finds most); [Q-15](#q-15) two writers without a lock; [Q-01](#q-01) swallowed errors, which hide every other bug here; [Q-02](#q-02) one rule in several copies; [Q-48](#q-48) tests that check nothing, without which a green suite proves none of the other answers.

## Index

**Product**
- [Q-01](#q-01) Every failure reaches the user or a log, with its cause? `all`
- [Q-02](#q-02) Every copy of a rule gives the same answer? `all`
- [Q-03](#q-03) One malformed record cannot break the reading of all the others? `all`
- [Q-04](#q-04) Empty string, 0, null and a missing field each mean the right thing? `all`
- [Q-05](#q-05) Prefix matches, substring searches and patterns match only what they should? `all`
- [Q-06](#q-06) Input with the right meaning in the wrong shape is normalised or cleanly refused? `input` `ext`
- [Q-07](#q-07) A failed read never leads to a write of empty data? `store`
- [Q-08](#q-08) A crash between two steps of a write leaves consistent data? `store`
- [Q-09](#q-09) A save changes only what it meant to, and an unchanged round trip keeps every byte? `store`
- [Q-10](#q-10) Reads and refused requests write nothing? `store` `input`
- [Q-11](#q-11) "Handled" and "installed" flags survive restarts and reset when they should? `long` `ship`
- [Q-12](#q-12) Every cache key covers every input the answer reads? `all` (any cache)
- [Q-13](#q-13) One unrelated change or one idle minute triggers almost no work? `long`
- [Q-14](#q-14) Two creates in the same second get two ids, and every sort has a tie-breaker? `store`
- [Q-15](#q-15) Two processes writing the same file or row at once lose nothing? `conc` `store`
- [Q-16](#q-16) A retry, a duplicate event or a double tap takes effect once? `input` `ext` `ui`
- [Q-17](#q-17) Every route refuses unauthenticated programs, foreign pages and lower roles? `web` `input`
- [Q-18](#q-18) Identity comes from an explicit id, not a pid, a time window or a fork's parent? `conc`
- [Q-19](#q-19) Each piece of state lives at its right scope: user, device, window or session? `conc` `ui`
- [Q-20](#q-20) Stored times use one convention and survive a DST change? `time`
- [Q-21](#q-21) Long-running pages and processes stay right past midnight and after sleep? `time` `long`
- [Q-22](#q-22) Stored data survives an app update, and an old backup restored onto a new version? `store` `ship`

**Data and results**
- [Q-23](#q-23) Parts add up to the total, in the same units? `data`
- [Q-24](#q-24) Fit and test sets are split by the unit of independence, with no item on both sides? `data`
- [Q-25](#q-25) Claims shown to users match what the code computes? `data`
- [Q-26](#q-26) A result can be reproduced: seeds, and code, data and cache versions stored with it? `data`

**Environment**
- [Q-27](#q-27) Values put into shell lines, arguments, configs and URLs are quoted for that language? `proc` `input`
- [Q-28](#q-28) Every file route resolves the real path and keeps it inside its root? `proc` `input`
- [Q-29](#q-29) Every component spells the same path, key or header the same way? `all`
- [Q-30](#q-30) File names and file replacement follow Windows and macOS rules? `store` `ship`
- [Q-31](#q-31) Every file and pipe has an explicit encoding, and line readers split on `\n` only? `store` `proc`
- [Q-32](#q-32) The program works when started from login, a GUI, a service or a minimal PATH? `ship`
- [Q-33](#q-33) OS-specific calls and newer syntax work on every platform and the oldest supported version? `ship`
- [Q-34](#q-34) Stopping the program leaves no child process, zombie or window behind? `proc`
- [Q-35](#q-35) The app survives the OS suspending it or killing it mid-work? `ship` `long`
- [Q-36](#q-36) The installed package works, not only the checkout? `pub` `ship`

**External systems**
- [Q-37](#q-37) Text written by a model, a tool or a stranger is never taken as the user's command? `ext` `input`
- [Q-38](#q-38) A missing, signed-out, rate-limited or failing tool is never read as success? `ext`
- [Q-39](#q-39) The integration is tested against real payloads and event sequences from the version users run? `ext`
- [Q-40](#q-40) Every external call has a timeout, and nothing blocking runs on the event loop? `ext` `long`

**User**
- [Q-41](#q-41) A late or out-of-order response cannot overwrite what the user did? `ui`
- [Q-42](#q-42) Clicks, keys and scrolls land where the user meant? `ui`
- [Q-43](#q-43) Popups and screens fit small windows, large text and layout shifts? `ui`
- [Q-44](#q-44) Every visible control works in the current configuration? `ui`
- [Q-45](#q-45) Lists, copies, backups and exports hold the whole data or say what they leave out? `ui` `store`
- [Q-46](#q-46) Exports open correctly in the programs that read them? `store`
- [Q-47](#q-47) A huge paste, a week-long session and a year of logs still work? `ui` `long`

**Tests and release**
- [Q-48](#q-48) Each test fails when the code it covers is broken? `tests`
- [Q-49](#q-49) Tests wait for conditions, not fixed times, and each has a time limit? `tests`
- [Q-50](#q-50) Date tests pass at 23:59, at month end and next month? `tests` `time`
- [Q-51](#q-51) Each test passes alone, in any order, in parallel, and leaves the machine untouched? `tests`
- [Q-52](#q-52) UI tests find elements by stable ids and tolerate the runner's OS and window size? `tests` `ui`
- [Q-53](#q-53) Tests run on the platforms you ship? `tests` `ship`
- [Q-54](#q-54) The release or merge gate requires every suite that matters? `tests`
- [Q-55](#q-55) Every module imports, every name resolves, and tests run against this commit's build? `all`
- [Q-56](#q-56) Docs, error messages and tool schemas match what the code enforces? `input` `pub`
- [Q-57](#q-57) Committed generated files match their generator, and HEAD runs without the working tree? `all`
- [Q-58](#q-58) The published artefact carries no developer paths, names or private data? `pub`

**[Before you report done](#before-you-report-done)**: D-1 every commit builds · D-2 red runs are read · D-3 parallel agents are separated · D-4 "done" is checked against the request.

---

## Product

<a id="q-01"></a>
### Q-01. Did you check that every failure reaches the user or a log they read, with its original cause?

**Applies when:** always; most often in `catch` blocks, subprocess wrappers, background jobs and shell scripts.
**What breaks:** An error is caught, ignored or made generic, so the caller sees success, an empty result or nothing.
**Real case:** A save that threw left its "saving" flag set, so every button stayed disabled and Back was blocked: the user was stuck until the app was killed.
**How to check:** Grep `except: pass`, `except Exception`, `catch {}`, `.catch(() => {})`, `|| true`, `2>/dev/null`, unchecked exit codes. Go: `_ =`, `if err != nil { return nil }`; Java/Kotlin: empty `catch`; Dart: `catch (_) {}`; Rust: `.ok()`, `let _ =`. Read first the blocks that return a value the caller trusts. Run: force each failure and assert what the user sees or what is logged.
**Typical fix:** Surface the error with its cause; pass exit codes through; reset busy flags in `finally`; log background failures where someone looks.
**Seen:** 25 times.

<a id="q-02"></a>
### Q-02. Did you check that every copy of a rule gives the same answer?

**Applies when:** always; most often with several entry points (server, CLI, scripts), similar adapters, or copied helpers.
**What breaks:** The same logic, constant, route or setting lives in two places; one copy changes and the other keeps the old behaviour.
**Real case:** A streak was computed by four copies of one walk; one was fed only the current project, so the user saw "day 3" on one screen and 10 on another.
**How to check:** For each rule, grep its name and a distinctive fragment of its body across the repository. Confirm each entry point loads settings through the same function. Run all adapters through one contract test; turn on duplicate-route warnings.
**Typical fix:** One implementation imported everywhere; one constant per setting, key or limit, read by every component, validator and schema (this also fixes Q-29 and Q-56); a contract test over every adapter.
**Seen:** 25 times.

<a id="q-03"></a>
### Q-03. Did you check that one malformed record cannot break the reading of all the others?

**Applies when:** always; loops over stored rows, files, log lines or mixed list items, including at startup.
**What breaks:** One malformed row, unknown enum value or null field raises inside a shared loop, and the whole page, list or startup fails.
**Real case:** One mis-encoded CSV file stopped the server from starting. A stored sticker id looked up without a fallback crashed the edit screen once a later version dropped that sticker.
**How to check:** Read each loop over records: can one iteration's exception leave the loop? Grep lookups without a fallback (`next(…)` without default, `firstWhere` without `orElse`). Fixtures: one broken row, one unknown status, one null required field, one undecodable file; assert the rest loads and the bad item is reported.
**Typical fix:** Skip and report the bad item; a fallback for unknown values; per-item error boundaries in the UI; validate settings before saving them.
**Seen:** 20 times.

<a id="q-04"></a>
### Q-04. Did you check what an empty string, 0, null and a missing field each mean to every reader?

**Applies when:** always; optional fields, filters, ids in paths, numbers where 0 is valid, imported files with missing sections.
**What breaks:** `''`, `0`, `None` or an absent key falls into a default that means something else: the current folder, "all rows", "delete everything".
**Real case:** A backup missing its "sessions" section was imported as an empty list, and the import deleted every session.
**How to check:** Grep `x or ""`, `x or default`, `if not x` on numbers and ids, `Path(` from optional values, `LIKE` with caller input. Go: zero values used as "unset"; Dart/Kotlin: `?? 0`, `?: ""`; Rust: `unwrap_or_default()`. Test each filter, path builder and importer with `''`, `0`, `None` and the key absent.
**Typical fix:** Explicit `is None` checks; refuse empty ids and missing sections at the boundary; show "unknown" instead of 0; never let an empty filter mean "all".
**Seen:** 17 times.

<a id="q-05"></a>
### Q-05. Did you check what else each prefix match, substring search and unanchored pattern matches?

**Applies when:** always; command parsers, format detection, ignore lists, lookups by text, detecting a tool by a file or folder.
**What breaks:** A prefix, substring, any-depth glob or first-match lookup matches more, or something other, than intended.
**Real case:** An ignore list of folder names matched at any depth, so a project whose own path contained an ignored name received no file events. A Markdown draft was taken for HTML because `"<p"` appeared in its first 500 characters.
**How to check:** Grep `startswith`, `in` on strings, `.find(`, `contains(`, unanchored `re.search`, `**/name` globs, `[0]` after a filter. Write near-miss tests: suffixes, prefixes, the name at another depth, the same text twice.
**Typical fix:** Anchor patterns; match on ids, not display text; detect a tool by a signal only it produces; refuse ambiguous matches.
**Seen:** 17 times.

<a id="q-06"></a>
### Q-06. Did you check what is stored when a caller sends the right meaning in the wrong shape?

**Applies when:** you accept JSON, arguments or imported files from clients, scripts or model tool calls.
**What breaks:** A list arrives for a string, or a string where a list is iterated; the value is stringified into storage, crashes, or is refused with a message that contradicts itself.
**Real case:** An agent sent tags as `["followup"]`; the server compared `"['followup']"` with the allowed names and replied "unknown tag followup — allowed: …, followup", and the agent gave up on the API.
**How to check:** Grep `str(` on request fields and loops over values that may be strings. Send each field as string, list, number, object and null; assert a 4xx naming the expected shape, or a normalised stored row.
**Typical fix:** A schema at the boundary; normalise documented alternatives; errors show the received value and type.
**Seen:** 23 times.

<a id="q-07"></a>
### Q-07. Did you check what gets written back when the read before it failed?

**Applies when:** read-modify-write of settings, `.env`, JSON state, CSV files, saved window lists, generated data files.
**What breaks:** A failed read (locked, corrupt, wrong encoding, not loaded yet, input missing) is treated as empty, and the next save writes the empty state over the real data.
**Real case:** Changing one setting while another process held the settings file open wiped every other entry. A refresh script found its input missing and wrote a stub over the committed research data.
**How to check:** Grep `except` and `catch` branches that return `{}`, `[]` or a default where the caller later writes. Run: make the file unreadable, locked or corrupt, trigger a write, assert the original bytes survive. What a good save may change: Q-09; two writers: Q-15.
**Typical fix:** A failed read raises and blocks the write; a "loaded" flag, and no save before it is set.
**Seen:** 9 times.

<a id="q-08"></a>
### Q-08. Did you check what is left on disk if the process dies between any two steps of a write?

**Applies when:** writes that touch several rows, files or stores; append-only logs and their cursors; deletes and archives across stores.
**What breaks:** An interrupted or misordered multi-step write leaves glued, cut or orphaned data.
**Real case:** An append after a cut-off JSONL line was glued onto it, corrupting both records. A template replace deleted old rows one by one and then inserted new ones outside a transaction, so a kill in between left half a set.
**How to check:** List each write path's steps and whether they share one transaction. Run: raise after each step and inspect the data; append to a file whose last line has no newline; delete, restore, and diff.
**Typical fix:** One transaction per logical change; temp file in the target folder, then rename; main record before side effects; cursors advanced after the write; a trailing newline before appending.
**Seen:** 13 times.

<a id="q-09"></a>
### Q-09. Did you check that a save changes only what it meant to, and that read-then-write without edits gives back the same bytes?

**Applies when:** forms that send whole records, undo from snapshots, editors of user config files, installers that rewrite managed files.
**What breaks:** The save replaces the whole object, so untouched fields, concurrent changes, hand edits, or content the code does not model (unknown keys, comments, line endings, fractions) are lost.
**Real case:** Editing a title re-sent the status field and cleared a flag another actor had set. An Undo restored whole rows from a snapshot, reverting a "done" tick made after it.
**How to check:** Grep saves and undos that write whole objects copied from an earlier read. Run: change field A elsewhere, save field B, assert A survives; load-then-save fixtures with unknown keys, comments and CRLF, and compare bytes.
**Typical fix:** Send diffs and merge on the server; write only changed columns; edit files with a format-preserving parser.
**Seen:** 16 times (9 whole-object saves, 7 lossy round trips).

<a id="q-10"></a>
### Q-10. Did you check that plain reads and refused requests write nothing?

**Applies when:** handlers that load, modify and save; lazy creation on read; catch-up work triggered by GET.
**What breaks:** A GET, a 404 or a refused request still writes, because state is saved before the decision or a read triggers catch-up work.
**Real case:** Two 404s for unknown ids rewrote their whole state file. A list endpoint created records as a side effect of being read.
**How to check:** Read each handler: does a write come before the last validation? Run: hash the data folder or database before and after every GET and every 4xx; fail on any change.
**Typical fix:** Validate fully before the first write; keep reads pure; move catch-up work into an explicit write path.
**Seen:** 8 times.

<a id="q-11"></a>
### Q-11. Did you check what a restart, a key change, a transient failure and a reappearing id do to each "already handled" or "installed" flag?

**Applies when:** long-lived processes, in-memory sets of processed ids, install markers, retry counters.
**What breaks:** A flag is set once and never cleared, reset at the wrong moment, or kept only in memory.
**Real case:** An "already handled" set lived only in memory; after a restart an item the user had deleted came back. An install marker recorded that dependencies were installed, not which list, so new dependencies were never installed.
**How to check:** For each flag, find where it is set and where it is cleared. Run: restart mid-flow; change the key; fail once, then succeed; bring the same id back.
**Typical fix:** Persist what must outlive the process; key markers by their input (a hash of the installed list); retry on failure, settle only on a definite refusal.
**Seen:** 16 times.

<a id="q-12"></a>
### Q-12. Did you check that every cache key covers every input the cached answer reads?

**Applies when:** any cache: ETags, memoisation, feature or build caches, change stamps, client caches.
**What breaks:** The key covers only some inputs, often not the code or reference data that computes the answer, or is taken after the read, so stale data is served.
**Real case:** A feature cache was keyed by metadata and source names, not by the formula code or the word lists, so published scores could come from stale features after an edit.
**How to check:** For each cached value, list the files, tables, settings and code it depends on, and compare them with its key. Run: write within one mtime tick; restore an older file; change the code; read again.
**Typical fix:** Derive the key from every input, including a version of the computing code; stamps before reading; content hashes, not mtime alone.
**Seen:** 18 times.

<a id="q-13"></a>
### Q-13. Did you check how much work one unrelated change, or one idle minute, triggers?

**Applies when:** derived views, file watchers, polling clients, per-event hooks, timers that rebuild UI.
**What breaks:** Expensive work runs on every event without checking whether its inputs changed, invalidates far more than needed, or wakes itself.
**Real case:** An idle server used 110% CPU: the watcher reacted to open events, and each rebuild opened the watched files again.
**How to check:** Read the watcher's event filter and each handler: does it exit early on unchanged inputs? Run: count rebuilds and files read; idle a minute and assert near zero; change one unrelated file and assert one rebuild. A once-a-second tick that rebuilds only what changed is fine.
**Typical fix:** Exit early on unchanged inputs; invalidate per input; filter watchers to write events; read logs from a saved offset.
**Seen:** 22 times.

<a id="q-14"></a>
### Q-14. Did you check that two creates in the same second, and an edit of a record, work with your id scheme, and that every sort has a tie-breaker?

**Applies when:** ids or file names derived from content and time; sorting by time; stores that keep whole seconds.
**What breaks:** Two records made in the same minute get the same id, an edited record gets a new id, or ties reorder between runs.
**Real case:** Two creates in the same minute minted the same id, the second returned the first record, and the user's new item vanished.
**How to check:** Grep ids built from a content hash plus a timestamp. Check the time precision the store keeps and every sort on time for a second key. Run: freeze the clock, create twice with the same content, assert two records.
**Typical fix:** Random or sequence ids; the id as the last sort key; an explicit update path for edits.
**Seen:** 7 times.

<a id="q-15"></a>
### Q-15. Did you check what happens when two processes write the same file or row at the same moment?

**Applies when:** several processes write the same data (server and CLI, hooks, workers, restore scripts), or requests run in parallel.
**What breaks:** Read-modify-write or append without a shared lock, or a fixed temp file name, loses or corrupts one update.
**Real case:** A hook appended to a log while the server rewrote it; the rewrite dropped the new line.
**How to check:** For each data file, grep its name to list every writer, and check each takes the same lock. Grep fixed temp names. Run with real processes, not threads: N writers at once; assert nothing is lost.
**Typical fix:** One lock or transaction used by every writer; check-and-write under one lock; unique temp names (Q-08).
**Seen:** 18 times.

<a id="q-16"></a>
### Q-16. Did you check what a retry, a duplicate event or a double tap does?

**Applies when:** clients that retry, webhooks that redeliver, key and paste handlers, action buttons on any UI.
**What breaks:** The same action is applied twice.
**Real case:** Retrying a report after a timeout created a duplicate: appends had no idempotency key. An End button stayed enabled while its handler ran, so a double tap wrote the end time twice and navigated twice.
**How to check:** Grep action handlers that `await` without an in-flight flag or disabled state. Run: send each write twice; deliver each event twice; double-tap each button; press each shortcut once. Count the effects.
**Typical fix:** Idempotency keys; dedupe by event id; disable buttons while in flight; `preventDefault` on handled events.
**Seen:** 9 times.

<a id="q-17"></a>
### Q-17. Did you check every route as an unauthenticated program, a foreign web page and a lower-privileged caller would call it?

**Applies when:** web only: local servers, dashboards, APIs shared by a UI and scripts. A mobile app checks its backend's access rules instead.
**What breaks:** The server assumes only its own page calls it, so any local program or website can read secrets or write; or a role check exists only in the client.
**Real case:** A local analysis server checked neither `Host` nor `Origin`, so any web page the user opened could POST to it. A scripted caller set admin-only fields because only the client UI left them out.
**How to check:** List routes from the router and find each one's auth and per-field role check. Run: call each route, GETs and WebSocket upgrades included, without credentials, with a foreign `Origin` and `Host`, and as the lowest role; assert 401/403 and an unchanged data folder (Q-10).
**Typical fix:** A token or cookie on every route and handshake; check `Origin` and `Host`; a new token per start; roles enforced per field on the server.
**Seen:** 11 times.

<a id="q-18"></a>
### Q-18. Did you check that each record's owner comes from an explicit id, not from a pid, a time window, a parent process or whoever saw it first?

**Applies when:** activity, writes or messages are credited to users, sessions, processes, devices or agents; forks and child sessions.
**What breaks:** Ownership is guessed from a weak signal. Pids are reused, a fork carries its parent's id, two sessions share a folder or a time window.
**Real case:** On Windows a new session's writes were stamped with an old session's id after the OS reused its process id. Commits were credited to the first session that saw them: 60 of 375 went to the wrong owner.
**How to check:** Grep identity derived from `getpid`, `getppid`, parent-process walks, cwd, environment variables, "last active" lookups and joins on time windows. Run: two sessions in one folder; a fork; a reused pid; assert each record's owner.
**Typical fix:** Pass an explicit identity end to end; give forks and children their own id; refuse ambiguous matches instead of guessing.
**Seen:** 20 times.

<a id="q-19"></a>
### Q-19. Did you check whether each piece of state belongs to the user, the device, the window or the session, and what a second one does to it?

**Applies when:** synced preferences, several windows or devices, shared background daemons, several working copies of one project.
**What breaks:** A per-device or per-window value is stored globally and overwritten; a shared daemon carries one client's settings to all.
**Real case:** Per-window tab order was synced to the server, and the older server copy overwrote the local one on every reload. A project bound to one folder missed the work done in a second git worktree.
**How to check:** For each stored preference, name its scope and find where it is written and read back. Run: two windows or devices, change state in each, reload both, see which copy wins.
**Typical fix:** Keep per-device and per-window state local; no per-client state in shared daemons; identify a project by its repository, not one path.
**Seen:** 9 times.

<a id="q-20"></a>
### Q-20. Did you check that stored times use one convention and that durations survive a DST change?

**Applies when:** you store or parse timestamps, compute durations, or group by day.
**What breaks:** Naive and zone-aware times are mixed, timestamps are compared as strings, modules parse dates their own way, or days are bucketed in the wrong zone.
**Real case:** In the hour the clocks went back, durations went negative: times were stored as naive local values. Timestamps compared as strings sorted zoned and naive values by characters.
**How to check:** Grep `datetime.now()` without a zone, `utcnow`, `new Date(` on strings without an offset, string comparison of timestamps. Go: `time.Local`; Java/Kotlin: `LocalDateTime.now()`; Dart: `DateTime.now()` stored without `toUtc()`; Rust: `chrono::Local`, `NaiveDateTime`. Run the suite under `TZ=America/Sao_Paulo`, `Asia/Kolkata` and `Pacific/Chatham`, and with the clock frozen across a DST change.
**Typical fix:** Store aware UTC, or local with its offset; one parser; compare datetime values, not strings; bucket days in the user's zone.
**Seen:** 10 times.

<a id="q-21"></a>
### Q-21. Did you check a page or process left running past midnight, and a machine waking from sleep?

**Applies when:** long-lived pages and screens, pollers, timers, schedulers, daily computations.
**What breaks:** "Today" or the current month is computed once, or one module reads two clocks, so windows go wrong after midnight or a clock jump.
**Real case:** A dashboard left open overnight kept showing yesterday's 30-day window: "today" was computed when the page mounted.
**How to check:** Grep "today", start-of-day, the selected month and window bounds computed at module load, mount or `initState`. Run with a fake clock: mount, cross midnight and a month end, assert the window moves; jump the clock an hour.
**Typical fix:** Derive "today" from the clock on each render or request; one clock per module; recompute after a clock jump.
**Seen:** 6 times.

<a id="q-22"></a>
### Q-22. Did you check what an app update does to stored data, and what an old backup restored onto a new version does?

**Applies when:** local databases and files that outlive one version; OS or cloud backups; import of old exports.
**What breaks:** An upgrade drops and recreates tables, a migration is missing or untested, or an OS backup restores an old schema that the next start wipes or misreads.
**Real case:** A mobile app's database upgrade handler dropped every table, and Android auto-backup could restore an old-schema database that the next start wiped.
**How to check:** Read every schema-version check, `onUpgrade` and migration (`DROP`, recreate on mismatch). Look for a test that opens a database from the previous release; none is a finding. Restore must take the writers' lock (Q-15) and refuse a newer schema. Compare backup settings (`allowBackup`) with the migrations.
**Typical fix:** Numbered, tested migrations from every released schema; old-database fixtures; back up before migrating; refuse a newer schema.
**Seen:** from trial runs; 6 restore and migration bugs in our data are counted under Q-07, Q-08, Q-15, Q-21, Q-30 and Q-55.

## Data and results

<a id="q-23"></a>
### Q-23. Did you check on real data that the parts add up to the total, in the same units?

**Applies when:** dashboards, usage and billing numbers, percentages, rates per unit, aggregates over imported data.
**What breaks:** A total double-counts, counts another group's rows, mixes units or denominators, or prices everything at one rate.
**Real case:** On real data the parts of a usage split added up to 443% while the total showed 402%: one tool's rows were counted under another.
**How to check:** For each aggregate, name its filter, unit and denominator, and check the parts use the same. Run on a copy of real data, or on stored aggregates: sum of parts equals the total; distinct source ids equal row count. With neither, answer `unverified`.
**Typical fix:** Filter by the owning entity; dedupe imports by origin id; price per item; units in names.
**Seen:** 14 times.

<a id="q-24"></a>
### Q-24. Did you check that the held-out set is split by the unit of independence, and that no item sits on both sides?

**Applies when:** models, thresholds or scores fitted on data and evaluated on held-out data; cross-validation; bootstrap.
**What breaks:** Rows are split at random or by index while rows of one author, user or source are correlated, or one item lands in both sets, so the held-out score is too good.
**Real case:** A "held-out" false-alarm rate split text windows by index parity. Windows were drawn with replacement from a few sources, so the same authors, sometimes the same window, sat in both halves, and the rate shown to users was too low.
**How to check:** Find every split (`train_test_split`, `KFold`, `[::2]`, `sample(`, random masks) and name its unit; compare it with the unit the claim is about. Count ids and duplicates shared by both sides. Check that scalers, vocabularies and thresholds are fitted on the fit side only.
**Typical fix:** Group splits (`GroupKFold`, by source id); dedupe before splitting; preprocessing fitted inside each fold.
**Seen:** from trial runs.

<a id="q-25"></a>
### Q-25. Did you check that every claim shown to users matches what the code computes?

**Applies when:** UI text, reports or READMEs describe a number: "held-out", "x% of people", "confidence interval", "updated daily".
**What breaks:** The words promise a method, population or freshness the code does not deliver, so users trust a number for the wrong reason.
**Real case:** A README promised rates "measured on held-out human windows" and the tool told users "x% of people show this", while the code split by row, not by author (Q-24).
**How to check:** List every sentence in the UI, report templates and README that describes a computed number. For each, find the code and compare population, denominator, split, window and update time.
**Typical fix:** Make the text say what the code does, or the code do what the text says; generate method notes from the code's parameters.
**Seen:** from trial runs.

<a id="q-26"></a>
### Q-26. Did you check that a result can be reproduced: seeds fixed, and the code, data and cache versions stored next to it?

**Applies when:** experiments, benchmarks, sampling, training, committed or published results.
**What breaks:** Unseeded sampling, file-system order or unrecorded data and cache versions make a result impossible to regenerate, and stale inputs go unnoticed.
**Real case:** Lab results fed published presets, but no corpus hash or cache version was stored with them, so a run on stale features could not be told from a fresh one.
**How to check:** Grep `random`, `shuffle`, `sample(`, `manual_seed`, and `listdir` or `glob` without `sorted`. Open a stored result and look for commit, data hash, cache version, parameters and seed. Run one step twice and diff.
**Typical fix:** One explicit seed; sorted listings; commit, data hash, cache key and parameters written into every result.
**Seen:** from trial runs.

## Environment

<a id="q-27"></a>
### Q-27. Did you check every value you put into a shell line, an argument list, a config file or a URL against hostile input?

**Applies when:** code builds shell commands, CLI calls, YAML/TOML, regexes, headers or URLs from paths or user data.
**What breaks:** A value is inserted without that language's quoting, so it is expanded, split, or read as an option or as syntax.
**Real case:** A `$` in a folder name made a generated hook command fail on every run. A caller value `--output=/any/path` reached `git` as an option and wrote a file anywhere.
**How to check:** Grep `shell=True`, `os.system`, f-strings that build commands, string-built YAML/TOML, `re.compile(f"`. Go: `exec.Command("sh", "-c", …)`; Java: `Runtime.exec(String)`; Dart: `runInShell: true`; Rust: `Command::new("sh").arg("-c")`. Run with a folder named `` a b $x `y` 'q" %p\ ``, a non-Latin name, and a value starting with `-`.
**Typical fix:** Argument lists, not strings; `--` before user arguments; quote per target shell; serializers for configs; escape regexes; build URLs and headers with a library.
**Seen:** 11 times.

<a id="q-28"></a>
### Q-28. Did you check that every file route resolves the real path and confirms it is inside its root?

**Applies when:** routes, tools or commands that open files by a name from input; archive extraction.
**What breaks:** The code checks the requested name, not the resolved path, so `..`, absolute paths, encoded paths or symlinks reach outside the folder.
**Real case:** A static-file fallback joined the request path to the app folder without a containment check and served files outside it. A preset name from an HTTP request was used as a file path, reaching outside the presets folder.
**How to check:** Grep `os.path.join(root, …)` and `Path(root) /` on input, and `extractall(`. Go: `filepath.Join` (`Clean` is not containment); Java: `Paths.get(root, input)` without `normalize().startsWith(root)`; Rust: `root.join(input)`. Run: `../../etc/passwd`, `%2e%2e%2f`, an absolute path, a symlink pointing out; an archive with a `../` member.
**Typical fix:** Resolve with `realpath`, then require it under the root; `tarfile` with `filter="data"`; names from an allow-list.
**Seen:** 5 times.

<a id="q-29"></a>
### Q-29. Did you check that every component spells the same path, key or header the same way?

**Applies when:** identities derived from paths, keys shared between components, CSV headers, status strings.
**What breaks:** A trailing slash, a symlink, NFC vs NFD, letter case or surrounding spaces make two components disagree.
**Real case:** The theme switch wrote one storage key and the startup script read another, so a dark theme flashed light on every load. A CSV header ` name ` did not match `name`, and the next rewrite emptied that column.
**How to check:** Grep each shared key's writers and readers. Feed variants: trailing separator, `/var` vs `/private/var` on macOS, NFD vs NFC, drive-letter case, spaces in headers, case in statuses.
**Typical fix:** One normalising function used by every side, and the one-constant rule of Q-02.
**Seen:** 9 times.

<a id="q-30"></a>
### Q-30. Did you check file names and file replacement against Windows and macOS rules?

**Applies when:** file names built from input, ids or timestamps; renames and replaces; shipping to Windows or macOS.
**What breaks:** Windows and macOS refuse or merge names Linux allows (reserved names, colons, trailing dots, case-only twins) and lock files held open elsewhere.
**Real case:** On Windows every create returned 500: the file name contained a colon from a timestamp.
**How to check:** Grep names built from `isoformat()`, times or input, and check they are sanitised. `git ls-files | sort -f | uniq -di` for case twins. Run with names `con`, `aux.txt`, `a:b`, `name.`; on Windows, hold the target open during a replace.
**Typical fix:** Sanitise names and refuse reserved ones; retry replace on a sharing violation; lint for case twins.
**Seen:** 10 times.

<a id="q-31"></a>
### Q-31. Did you check that every file and pipe has an explicit encoding, and that line readers split on `\n` only?

**Applies when:** text files, subprocess output, files edited by hand or by PowerShell, Windows users, JSONL and logs.
**What breaks:** Text is read in the system code page, with a BOM, as UTF-16 or with CRLF; command output is parsed in English only; `splitlines()` splits on U+2028 inside JSON strings.
**Real case:** On Windows a Cyrillic prompt passed through stdin was stored garbled: the pipe used the OS code page.
**How to check:** Grep `open(` without `encoding=` (ruff `PLW1514`), `subprocess` with `text=True` and no encoding, `splitlines(` in JSONL readers. Java/Kotlin: `FileReader`, `getBytes()` without a charset; Go: `bufio.Scanner` and its 64 KB line limit; Dart: `jsonDecode` without stripping a BOM. Fixtures: BOM, UTF-16, CRLF, U+2028 in a record. Run with a non-English `LC_ALL`.
**Typical fix:** Explicit UTF-8, `utf-8-sig` on read; keep line endings; `LC_ALL=C` for parsed command output; split on `\n`.
**Seen:** 15 times (12 encoding and locale, 3 Unicode line separators).

<a id="q-32"></a>
### Q-32. Did you check the program started the way users start it: at login, from a GUI, as a service, from another shell, with a minimal PATH?

**Applies when:** desktop and CLI tools that install hooks, start at login, run as a service, or call CLIs found on PATH. Not mobile apps.
**What breaks:** It finds a different PATH, shell, home or interpreter than in the developer's terminal.
**Real case:** A macOS login agent started the server with a PATH that lacked the user's tools. On Windows a launcher handed over to `python`, which was the Store alias, not an interpreter.
**How to check:** Grep generated hook, service and launcher files for bare `python`, `node` or tool names. Run the launch line with `env -i HOME=$HOME PATH=/usr/bin:/bin`, under each shell users have, and with a version-manager shim first on PATH.
**Typical fix:** Absolute interpreter paths written at install; run a candidate before trusting it; no reliance on interactive shell config.
**Seen:** 12 times.

<a id="q-33"></a>
### Q-33. Did you check every OS-specific call and every newer syntax on all supported platforms and the oldest supported version?

**Applies when:** cross-platform code, sockets, signals, FFI, platform APIs, a declared minimum version (`requires-python`, `minSdkVersion`, `engines`, `rust-version`).
**What breaks:** A POSIX-only module, a socket option with other semantics, a platform API with extra requirements, or newer syntax fails on one platform.
**Real case:** On Windows `SO_REUSEADDR` lets `bind` succeed on a busy port, so a free-port check passed while the port was taken. On iPad the share sheet needs an anchor rectangle, so Export failed there only.
**How to check:** Grep `fcntl`, `termios`, `os.fork`, `signal.SIG`, `SO_REUSEADDR`; Go `//go:build` gaps; Kotlin `SDK_INT` checks; Dart `Platform.is…`; Rust `#[cfg(unix)]` without an else. Set the linter or compiler target to the oldest supported version. Import each module alone on each OS.
**Typical fix:** Guard platform imports; probe ports by connecting, not binding; declare FFI types; lint against the declared minimum.
**Seen:** 9 times.

<a id="q-34"></a>
### Q-34. Did you check that stopping your program leaves no child process, zombie or console window behind?

**Applies when:** the app spawns CLIs, servers, workers or wrappers.
**What breaks:** Children are not killed with the parent, not reaped, open console windows on Windows, or are forked from a threaded process and can deadlock.
**Real case:** After a crash restart the old dev-server child still held its port: only the parent had been killed.
**How to check:** Grep spawns without a matching `wait`, kills of a pid rather than its group, `fork` in threaded code. Go: `exec.Cmd` without `Wait` or `Setpgid`; Java: `ProcessBuilder` without `destroy`. Run: start, stop, list the process tree (`ps -ef --forest`; `Get-CimInstance Win32_Process`).
**Typical fix:** Children in their own process group or job object, killed as a group; `wait()` on children; exec right after fork; `CREATE_NO_WINDOW` on Windows.
**Seen:** 6 times.

<a id="q-35"></a>
### Q-35. Did you check what happens when the OS suspends the app, kills it in the background, or kills it in the middle of a write?

**Applies when:** mobile apps, desktop apps on laptops, background services; timers, unsaved input, in-flight requests.
**What breaks:** State kept only in memory is lost, a timer counts through suspension or stops, in-flight work is never retried.
**Real case:** A model reply that had already arrived was held only in memory until the call returned, so a killed call lost it.
**How to check:** Find the lifecycle hooks (`AppLifecycleState`, `onPause`/`onStop`, `applicationDidEnterBackground`, `visibilitychange`, `SIGTERM` handlers) and what each saves. List in-memory state the user would miss; check timers compute from stored start times. Run: background, kill (`adb shell am kill <package>`), reopen; suspend a laptop mid-timer.
**Typical fix:** Save drafts and progress on change or pause; durations from stored start times; resume or retry in-flight work on start.
**Seen:** from trial runs; 1 bug in our data (counted under Q-11).

<a id="q-36"></a>
### Q-36. Did you check that the installed package works, not only the checkout?

**Applies when:** pip, npm, cargo and Go modules, app bundles; anything installed rather than run from the repository.
**What breaks:** Data files are missing from the package, or code finds resources through the repository layout, so it works editable and fails installed.
**Real case:** Presets and web assets were not declared as package data, so a non-editable install had none of them.
**How to check:** Compare the package manifest (`pyproject.toml` package data, `MANIFEST.in`, `files` in `package.json`, Flutter `assets:`, Cargo `include`) with the files the code opens. Grep `__file__` with `parents[` and repo-relative paths. Run: build, install into a clean environment outside the repository, run each entry point.
**Typical fix:** Declare every data file; load resources through the package API (`importlib.resources`, Go `embed`); a clean-install smoke test in CI.
**Seen:** from trial runs.

## External systems

<a id="q-37"></a>
### Q-37. Did you check who wrote each piece of text your code acts on?

**Applies when:** code finds commands, ids, links or instructions in model output, tool output, webhook bodies, emails, messages, templates or pastes.
**What breaks:** Text written by a machine or a stranger arrives on the user's channel and is trusted: its commands run, its ids are followed, its instructions are obeyed.
**Real case:** A tool's automatic "limit has reset, continue" prompt arrived on the user channel and was recorded as the user's turn, so a command word in it would have run. A command word quoted in an agent's prompt to a helper closed three of the user's tasks.
**How to check:** List every place that parses commands or acts on text, and the origin of that text. Grep command parsing over messages, and model or webhook output passed to a shell, SQL, paths, URLs or tool calls. Fixtures: a command word inside model output, a quoted message, a template expansion, an email body; assert nothing fires.
**Typical fix:** Record origin and check it before parsing; act only on the trusted channel; treat model and third-party output as data, checked against an allow-list.
**Seen:** 13 times.

<a id="q-38"></a>
### Q-38. Did you check what the code does when the external tool is missing, signed out, rate-limited, or answers with an error page?

**Applies when:** calls to CLIs, APIs, models, sign-in providers and services.
**What breaks:** Exit 0 with an error on stderr, HTTP 200 with an HTML error page, an empty answer, "not signed in" or a 429 is read as a valid result, or a permanent refusal is retried forever.
**Real case:** A CI job that ran a real external CLI was green on three OSes while the CLI answered "not signed in" every time. A sign-in answer "account exists with a different credential" was retried as transient, and the user was stuck on "try again".
**How to check:** For each call, read what the code does on: not installed, exit 0 with stderr, non-JSON body, 401/403, 429, 5xx, an empty result, a permanent error. Status and content type must be read before parsing. Run: stub each answer and assert the user sees a named state.
**Typical fix:** Check status, content type and required fields before trusting an answer; a named state per failure; probe a tool by running it; retry only transient errors.
**Seen:** 5 times, counted under Q-04, Q-05, Q-32, Q-39 and Q-48.

<a id="q-39"></a>
### Q-39. Did you check your integration against real payloads and event sequences from the version your users run?

**Applies when:** you consume another tool's API, CLI output, webhooks, hooks, plugin events or SDK callbacks.
**What breaks:** The code reads a field the tool never sends or has renamed, assumes events arrive in order and without overlap, or meets a new version that behaves differently.
**Real case:** Every reply from one CLI was captured empty: the adapter read a field name the tool does not send.
**How to check:** Find each field the code reads in a recorded payload or the pinned version's docs. Check that versions are pinned and a scheduled job runs the latest. Run: replay recorded sequences reordered, overlapping and missing a start event (duplicates: Q-16).
**Typical fix:** Pin and bump deliberately; documented fields only; fixtures per version; events treated as unordered and possibly missing, keyed by id.
**Seen:** 18 times (7 changed or misread payloads, 11 wrong assumptions about event order and overlap).

<a id="q-40"></a>
### Q-40. Did you check that every external call has a timeout and that nothing blocking runs on the event loop or UI thread?

**Applies when:** async servers and UIs; calls to CLIs, APIs or models; slow disks; locks; thread pools.
**What breaks:** A blocking call, a long-held lock or a call without a timeout stalls the whole server or UI.
**Real case:** Every request, even the health check, stalled 3 s whenever a log changed: a lock was held for the whole rebuild.
**How to check:** Grep `subprocess.run(`, `requests.`, `fetch(` without a timeout; sync `open`, `subprocess` or `sleep` inside `async def`; locks held across I/O. Go: `http.Client{}` without `Timeout`; Kotlin: blocking calls on `Dispatchers.Main`; Dart: `http.get` without `.timeout`, `…Sync` I/O in UI code; Rust: `std::fs` inside `async fn`. Run: replace a tool with one that never returns; assert the UI or health check still answers.
**Typical fix:** A timeout on every external call; blocking work in a bounded pool; no lock held across slow work.
**Seen:** 7 times.

## User

<a id="q-41"></a>
### Q-41. Did you check what happens when the user acts before a request returns, and when responses arrive out of order?

**Applies when:** the UI loads data into editable forms, does optimistic updates, or reuses one component across records.
**What breaks:** A late response or remount replaces newer state; a component not keyed by record keeps the previous record's state and saves it onto the new one.
**Real case:** Text typed into a description before it loaded was replaced when the load finished. An editor not keyed by record id kept the first record's text, and Save wrote it onto the second.
**How to check:** Grep fetches in effects without an abort or ignore flag, and editors without a per-record key (React `key={record.id}`; Flutter `ValueKey`, `setState` after `await` without a `mounted` check; Kotlin `collect` where `collectLatest` is meant). Run component tests with delayed and reordered responses; type during the load; switch records and save.
**Typical fix:** Drop stale responses; read-only until loaded; key components by record id; merge server data into local edits.
**Seen:** 25 times.

<a id="q-42"></a>
### Q-42. Did you check where each click, key and scroll actually lands, with focus elsewhere and on another keyboard layout?

**Applies when:** modals, global shortcuts, overlays, embedded editors, drag regions. The backdrop and `e.key` checks are web only.
**What breaks:** A backdrop, a global shortcut, an overlay or a drag region handles an event meant for something else.
**Real case:** Selecting text in a dialog and releasing over the backdrop closed the dialog and lost the edits. Letter shortcuts did nothing under a Cyrillic layout: handlers compared `e.key`, not `e.code`.
**How to check:** Grep backdrop `onClick` closers, global `keydown` listeners comparing `e.key` with letters, and overlays that capture pointer or wheel events. Check shortcuts skip editable elements. Run: mousedown inside, mouseup on the backdrop; a shortcut while typing; a shortcut under another layout.
**Typical fix:** Close only when both mousedown and mouseup hit the backdrop; skip shortcuts in editable elements; match on `e.code`; decorative overlays pass events through.
**Seen:** 18 times.

<a id="q-43"></a>
### Q-43. Did you check popups and screens in small windows, with large text, and after layout shifts?

**Applies when:** menus, popups, overlays, tables, mobile screens.
**What breaks:** Positions come from a stale or estimated size, a container clips its content, or one wide cell widens the page, so items land off-screen or under something else.
**Real case:** In a short window the last menu item fell below the viewport and could not be clicked. In a 420 px window a menu button was present but a click at its centre hit a panel lying over it.
**How to check:** Grep popup placement from fixed or estimated sizes, popups without a max height tied to the viewport, measurements before layout settles. Look for tests at small sizes and large text scale; none is a finding. Run at 800×500 and 1280×400: open each popup and hit-test each item at its centre (`elementFromPoint`).
**Typical fix:** Measure after layout; clamp popups to the viewport and let them scroll; test the smallest window and largest text scale.
**Seen:** 11 times.

<a id="q-44"></a>
### Q-44. Did you check that every visible control works in the current configuration and changes what its placement suggests?

**Applies when:** feature flags, server-side switches, platform-dependent features, permissions, controls shared by several items.
**What breaks:** A control or label promises something the system does not do in this configuration.
**Real case:** A tab for a feature was shown while the server had that feature off. A slider placed above nine sorted lists changed the order of only one.
**How to check:** Grep UI gated on the server, the platform or a permission; confirm each reads the real capability and shows a state when it is off or denied. Run with each flag off and each permission denied; click every control and assert a visible effect.
**Typical fix:** Ask the server and platform for capabilities; hide or disable with a reason; place each control next to what it changes.
**Seen:** 5 times.

<a id="q-45"></a>
### Q-45. Did you check that lists, copies, backups and exports hold the whole data, or say what they leave out?

**Applies when:** paged lists, filters, search, list endpoints, copy, backup and export actions, previews.
**What breaks:** A list sorts after paging or filters by default; a truncated preview or a filtered query is used where the whole is meant.
**Real case:** A client sorted the first 50 rows locally, so the newest items never appeared. A backup said it "always holds everything" but read only ended sessions, so restoring it deleted the running one.
**How to check:** Grep `preview`, `truncate`, `slice(0,`, `[:N]` and filtered queries in copy, backup and export paths, and sorts after a paged fetch. Fixtures: more rows than a page, archived and in-progress items, values longer than the preview.
**Typical fix:** Sort and filter on the server before paging; show active filters and "N more"; read the full, unfiltered data for copy, backup and export.
**Seen:** 11 times (6 hidden rows, 5 previews used as the whole).

<a id="q-46"></a>
### Q-46. Did you check each export in the programs that will open it?

**Applies when:** CSV, spreadsheet, JSON or calendar exports that users open in Excel, Numbers or other tools.
**What breaks:** Excel reads UTF-8 CSV without a BOM in the local code page, so non-Latin names are garbled; cells starting with `=`, `+`, `-` or `@` run as formulas; times lose their offset; separators clash with a decimal comma.
**Real case:** A CSV export offered as "open in spreadsheets" had no BOM, so accented and Cyrillic names came out garbled in Excel on Windows. One CSV of the same app carried time offsets and another did not.
**How to check:** Find every export writer and check: BOM or declared encoding, escaping of cells starting with `= + - @`, quoting of separators and newlines, offsets on times, the same formats across exports.
**Typical fix:** UTF-8 with BOM for spreadsheet CSV; prefix formula-like cells with `'`; a CSV library for quoting; ISO 8601 with offset.
**Seen:** from trial runs.

<a id="q-47"></a>
### Q-47. Did you check a 100 000-character paste, a week-long session and a year of logs?

**Applies when:** user pastes, long sessions, append-only logs, long lists, data passed to subprocesses.
**What breaks:** Data that grows with use is passed on a command line, rendered whole or appended without bound, until an OS or UI limit breaks it.
**Real case:** After a long session every save failed: session data went on the command line past Windows' 32 767-character limit.
**How to check:** Grep subprocess calls that put data in argv; lists rendered without virtualisation (Flutter `ListView(children:)` over user data); logs without rotation; fields without a length limit. Run fixtures at 10× the largest size you can find, or 10 000 items.
**Typical fix:** Data on stdin or in a temp file; virtualised or capped rendering; log rotation; limits on machine input, and never cut the user's own text silently.
**Seen:** 7 times.

## Tests and release

<a id="q-48"></a>
### Q-48. Did you check that each test fails when the code it covers is broken?

**Applies when:** there is a test suite; especially smoke tests of external tools, mocked integrations, conditional skips.
**What breaks:** A test passes because it asserts too little, is skipped, never reaches the code, or computes its expected value with the code under test.
**Real case:** 17 tests were skipped on Windows for a function they did not need; once run, they found a real bug.
**How to check:** Grep assertions that only check exit code 0, status 200, `>= 0` or truthiness; expected values computed by the function under test; skips and their conditions (`pytest -rs`, `t.Skip`, `@Disabled`, `skip:`, `#[ignore]`). Run: break the covered line or revert the fix; the test must fail.
**Typical fix:** Assert on output content; fail CI on unexpected skips; a regression test must fail on the old code first.
**Seen:** 21 times.

<a id="q-49"></a>
### Q-49. Did you check that tests wait for conditions rather than fixed times, and that each test has its own time limit?

**Applies when:** there is a test suite; async, UI, integration and process tests above all.
**What breaks:** A fixed sleep or a wall-clock threshold turns red under load; a test without a timeout hangs the run with no clue which test hung.
**Real case:** A test sent a command, waited a fixed 600 ms and checked the output; on a busy machine it was not there yet.
**How to check:** Grep tests for `sleep(`, `waitForTimeout`, fixed `setTimeout`, `time.Sleep`, `Thread.sleep`, `Future.delayed`, and assertions on elapsed milliseconds. Confirm a per-test timeout (`pytest-timeout`, `go test -timeout`, JUnit `@Timeout`, Dart `timeout:`) that dumps stacks. Run under load and in parallel.
**Typical fix:** Wait on signals with a bounded deadline; count work instead of timing it; per-test timeouts with stack dumps; no retrying into green.
**Seen:** 16 times (12 clock waits, 4 missing time limits).

<a id="q-50"></a>
### Q-50. Did you check that date tests pass at 23:59, on the last day of a month and next month?

**Applies when:** tests of anything with dates, rolling windows, day grouping or time-seeded ids.
**What breaks:** A test reads the real date, hard-codes a date into a rolling window, or takes "now" twice across midnight.
**Real case:** CI turned red in mid-September: a test had hard-coded dates into a rolling 30-day window.
**How to check:** Grep tests for literal dates and direct `now()`, `Date.now()`, `time.Now()`, `DateTime.now()`. Run with the clock frozen (freezegun, fake timers, an injected clock) at 23:59:30, at month end and a month later. Zones and DST: Q-20.
**Typical fix:** One frozen "now" per test; fixtures relative to it; no calendar dates in rolling windows.
**Seen:** 11 times.

<a id="q-51"></a>
### Q-51. Did you check that each test passes alone, in random order and in parallel, and leaves the machine and the repository as it found them?

**Applies when:** there is a test suite; tests that spawn processes, open ports, use git, read the environment or touch data folders.
**What breaks:** A test leaves state or files behind, shares a folder or fixed port with another test or run, reads the developer's environment, or writes into real data.
**Real case:** An integration test exported a file into the developer's real `~/Documents`; the leftover file was the evidence.
**How to check:** Grep tests for fixed ports and output paths (`/tmp/<name>`, shared `dist/`), home and documents folders, environment reads. Run in random order (`pytest -p randomly`, `go test -shuffle=on`, `dart test --test-randomize-ordering-seed=random`), in parallel, and one test alone. `git status --porcelain` must be unchanged; checksum the real data and home folders before and after.
**Typical fix:** Per-test temp folder and `HOME`; mocked platform folders; port 0; per-run output folders; clear the app's environment variables; `GIT_CONFIG_GLOBAL=/dev/null`.
**Seen:** 19 times (9 leaks between tests, 10 into the real machine).

<a id="q-52"></a>
### Q-52. Did you check that UI and end-to-end tests find elements by stable ids and tolerate the runner's OS and window size?

**Applies when:** there are UI or end-to-end tests that run on several OSes or headless.
**What breaks:** The test assumes one OS, window size, pixel rounding, label or machine name, and fails elsewhere for reasons unrelated to the product.
**Real case:** A headless run opened the app at its minimum window size, so the target scrolled out of view.
**How to check:** Grep selectors by visible text or styling classes, exact pixel comparisons, tests that never set the window size. Confirm failures save a screenshot or DOM.
**Typical fix:** Test-id selectors (`data-testid`, Flutter `Key`, Android resource ids); an explicit viewport; tolerances instead of exact pixels.
**Seen:** 11 times.

<a id="q-53"></a>
### Q-53. Did you check that the tests run on the platforms you ship, not only on the developer's?

**Applies when:** apps, libraries and CLIs shipped to several OSes or to an OS other than the CI host.
**What breaks:** The suite runs only on the host, so platform code, permissions, file locations and the oldest supported OS are never exercised.
**Real case:** A mobile app's 77 integration scenarios ran only on Linux desktop; nothing ran on Android or iOS. Browser end-to-end tests never ran on Windows although the product shipped there.
**How to check:** List the shipped platforms (store configs, `minSdkVersion`, deployment targets, release matrix) and where each suite runs (CI matrix, release scripts). Each shipped platform, and the oldest supported version, needs at least one run.
**Typical fix:** One emulator or runner per shipped platform at the oldest supported version, run before release.
**Seen:** 2 times, counted under Q-48 and Q-54; also from trial runs.

<a id="q-54"></a>
### Q-54. Did you check which suites your release or merge gate actually requires?

**Applies when:** there is a test suite. With no CI and no gate script, the answer is `no`: nothing gates a release.
**What breaks:** The gate checks some suites; the one that runs real services, another OS or the integration scenarios is left out or filtered away.
**Real case:** A beta was tagged with CI green while the real-service suite had not run; when it ran, it was red.
**How to check:** List all workflows, gate scripts and required checks, and compare with the list of suites. For each path filter or test selection, find what depends on the filtered paths.
**Typical fix:** Gate on every suite that matters; run the expensive suite before tagging; no filters that skip dependents.
**Seen:** 6 times.

<a id="q-55"></a>
### Q-55. Did you check that every module imports, every name still resolves after moves and renames, and tests run against a build of this commit?

**Applies when:** always; most often after refactors, moves, scripted edits, and end-to-end tests that serve a prebuilt bundle.
**What breaks:** A removed or renamed name is still used on an untested path; moved code keeps imports valid only in the old place; tests run against an old build.
**Real case:** A refactor removed a constant one handler still used; that route answered 500 because no test called it. A browser suite tested a four-day-old build.
**How to check:** Import every module in a fresh interpreter, or run `tsc --noEmit`, `go vet ./...`, `dart analyze`, `cargo check`. Grep each old name after a rename. Print the build's commit in end-to-end setup and compare it with `HEAD`.
**Typical fix:** Type check before each commit; rebuild before end-to-end tests; a test that calls every route once.
**Seen:** 9 times (5 dangling names, 4 wrong builds or step order).

<a id="q-56"></a>
### Q-56. Did you check that every call, name and limit in your docs, error messages and tool schemas matches what the code enforces today?

**Applies when:** an API, CLI help, README commands, agent instructions, tool schemas, or error messages that suggest a next step.
**What breaks:** Text that callers and agents read describes calls, limits, defaults or names the code no longer has, so they keep failing.
**Real case:** Eight server error messages told agents to make calls the server refused. The server enforced a title length the tool schema did not state, so agents kept getting 422.
**How to check:** Extract routes, commands and tools from the code and diff them against the docs. Test each call suggested in an error string. Compare validator limits and defaults with schemas and docs.
**Typical fix:** Generate docs and schemas from the code, with the one-constant rule of Q-02.
**Seen:** 7 times.

<a id="q-57"></a>
### Q-57. Did you check that committed generated files match their generator, and that HEAD works without the working tree's extra files?

**Applies when:** the repository holds generated data or copies, or the working tree has untracked or modified files.
**What breaks:** A committed output was made by older code or is a stub; code in HEAD depends on a file that exists only untracked.
**Real case:** The main module imported an untracked file, so a fresh clone of HEAD could not import it. A committed benchmarks file was a stub, not the output of the committed script.
**How to check:** List generated files and their generators; regenerate into a temp folder and diff. In `git status --porcelain`, check whether tracked code reads anything untracked. Run: `git worktree add <tmp> HEAD`, then install and test there.
**Typical fix:** Regenerate in CI and fail on a diff; commit or remove what HEAD needs; test from a clean checkout.
**Seen:** 1 time in our data (counted under Q-02); also from trial runs.

<a id="q-58"></a>
### Q-58. Did you check the published artefact itself (history, tags, bundles, generated config) for the developer's machine and private data?

**Applies when:** anything published: public repositories, packages, app builds, generated config.
**What breaks:** The release carries machine-specific paths, personal names or private data, or a leak check looks at the tree while the full history is published.
**Real case:** A fresh clone carried a generated config pointing at the author's interpreter and folders.
**How to check:** Grep the tree and `git log -p --all` for your username, home path, email and machine names. Run a secret scanner on the full history (`gitleaks detect`). Clone fresh as another user and run the install.
**Typical fix:** Values from config; neutral placeholders and synthetic fixtures; ignore generated files; release from a clean repository.
**Seen:** 11 times (6 release contents, 5 developer values in code).

---

## Before you report done

These four close every audit and every agent's own "done" report; answer each like a question.

**Evidence with only a repository and no CI:** what you can reproduce now: commands you ran with their output, `git log`, files in the tree. Past runs that left no record in the repository (chat logs, CI you cannot see, "tests pass" in a commit message) are `unverified`. Missing CI is answered at Q-54.

**D-1. Every commit in the range builds and type-checks**, not only the last. Check: `git rebase --exec "<build && type check && unit tests>" <base>`. Real case: seven intermediate refactoring commits failed the type check while CI, which built only the pushed head, was green. Seen: 5 times.

**D-2. Every red scheduled or paid run has been read and has an owner.** Check: `gh run list --workflow <name> --limit 20` per scheduled workflow; count reds since the last green and who acted. Real case: a scheduled workflow was red in 14 of 24 runs, and a real bug it found was twice dismissed as noise. Seen: 2 times.

**D-3. Parallel agents and runs did not share a working tree.** Check: `git worktree list`; whether the suite you ran saw another agent's uncommitted edits. Real case: nine agents committed into one tree; checks failed on another agent's half-done edit, and one ran `git stash pop` onto the main branch. Seen: 7 times (fixed ports and output folders: Q-51).

**D-4. "Done" was checked line by line against the request, with the full suite, on the user's setup.** Check: each requirement with its evidence; the full suite's count, or what was not run and why; the user's OS, display server and versions against what the feature relies on. Real case: an "always on top" feature was reported done; it works only on X11, and the user runs Wayland. Seen: 5 times.
