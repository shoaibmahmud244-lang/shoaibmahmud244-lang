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

The n8n pack also ships the validator that caught it. A file that parses as JSON
is not a workflow that works, and a validator that only ever passes is a rubber
stamp — so it's negative-tested against the known-broken export too.

Contributor to [OpenMontage](https://github.com/calesthio/OpenMontage) (61k
stars, AGPL-3.0) — reliability and checkpointing work in the video pipeline.
I contributed 42 of its 463 commits; it isn't mine, and I don't claim it as such.
