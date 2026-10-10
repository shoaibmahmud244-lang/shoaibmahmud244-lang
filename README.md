# shoaibmahmud244-lang

Data engineer working on web extraction and pipeline reliability.

## What I work on

- **Web scraping that survives production** — resumable extraction, validation,
  dedupe, and clean CSV/XLSX output. No black-box scraping API: the pipeline is
  readable and debuggable.
- **Automation with visible state** — webhooks, idempotent upserts, and
  timestamped run logs, so a failure is diagnosable instead of speculative.
- **n8n workflows that import without secrets** — every credential-dependent
  node ships disabled, so a client can load the file and see the real graph
  before handing over any key.

## Repos

- **[jarvis](https://github.com/shoaibmahmud244-lang/jarvis)** —
  real-time push-to-talk voice and visual assistant for Wayland/Hyprland. Integrates
  Voxtype as an on-demand systemd user daemon (~18MB idle RAM, 0% CPU) with single-frame
  and continuous screen capture (Grim + FFmpeg) into OpenCode reasoning via Nvidia Nemotron.
  Speech synthesis runs local Kokoro ONNX CPU inference (`bm_george` voice) at 10x–15x
  faster than real-time with zero resident memory, backed by Engram MCP persistent memory.

- **[sandbox-n8n](https://github.com/shoaibmahmud244-lang/sandbox-n8n)** —
  lets an n8n workflow plan and approve system changes **without giving n8n a
  shell**. n8n 2.x disables the Execute Command node on purpose; re-enabling it
  hands a browser-reachable web app arbitrary command execution. This exposes two
  endpoints instead (`/plan`, `/approve`), each a fixed argv into a local
  orchestrator, with the run id regex-validated before it ever reaches a shell —
  `run_id: "; rm -rf /"` is a 400, not an execution. The README documents the
  systemd directives that silently break the sandbox underneath it, found by
  bisecting each one: two of them (`RestrictNamespaces`, `CapabilityBoundingSet=`)
  made every step fail, and a third (`MemoryDenyWriteExecute`) killed V8 with a
  `SIGTRAP` core dump.

- **[demo1-scraper](https://github.com/shoaibmahmud244-lang/demo1-scraper)** —
  config-driven Python scraper. Commits include the real output of a 1,000-row
  run, plus four defects I found by *running* the code rather than reading it
  (a `sys.exit` without `import sys`, a resume counter that double-counted rows,
  a BeautifulSoup multi-valued-attribute `TypeError`, and a currency-decoding
  bug that silently stripped `£` from every price).
- **[demo3-workflow](https://github.com/shoaibmahmud244-lang/demo3-workflow)** —
  form → CRM → email automation. The interesting bug: the run log was opened in
  `"w"` mode, so a re-run destroyed its own evidence and the replacement log
  *contradicted* the CSV it claimed to describe.
- **[n8n-workflow-pack](https://github.com/shoaibmahmud244-lang/n8n-workflow-pack)** —
  two n8n workflows that import and run with **zero credentials** (every
  credentialed node ships disabled), plus `validate_workflows.py`, which checks
  the things that actually break an import. The first version of workflow 1
  couldn't be imported at all — missing `connections` and `typeVersion` — and its
  Gmail node had a subject and a recipient but no message body, so it would have
  sent empty emails. It also treated `Ada@x.com` and `ada@x.com` as two
  contacts. None of that is visible if you only check that the JSON parses.

## How I work

I verify claims by executing them. Every repo here documents bugs found that
way, including ones I'd shipped in a first pass:

- the scraper's currency bug silently produced plausible-looking numbers
- `demo3-workflow`'s log was opened in `"w"` mode, so a re-run destroyed its own
  evidence and the replacement log *contradicted* the CSV it described
- the first n8n workflow **could not be imported at all** — missing
  `connections` and `typeVersion` — and its email node had a subject and
  recipient but no message body
- my own hardening broke the sandbox shim twice before I checked: a unit that
  made bubblewrap fail on every step, and a `ufw` rule keyed on the wrong end
  of the connection — both looked plausible until executed

The n8n pack also ships the validator that caught it. A file that parses as JSON
is not a workflow that works, and a validator that only ever passes is a rubber
stamp — so it's negative-tested against the known-broken export too.

Contributor to [OpenMontage](https://github.com/calesthio/OpenMontage) (61k
stars, AGPL-3.0) — reliability and checkpointing work in the video pipeline.
I contributed 42 of its 463 commits; it isn't mine, and I don't claim it as such.
