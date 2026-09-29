# shoaibmahmud244-lang

Data engineer working on web extraction and pipeline reliability.

## What I work on

- **Web scraping that survives production** — resumable extraction, validation,
  dedupe, and clean CSV/XLSX output. No black-box scraping API: the pipeline is
  readable and debuggable.
- **Automation with visible state** — webhooks, idempotent upserts, and
  timestamped run logs, so a failure is diagnosable instead of speculative.

## Repos

- **[demo1-scraper](https://github.com/shoaibmahmud244-lang/demo1-scraper)** —
  config-driven Python scraper. Commits include the real output of a 1,000-row
  run, plus five defects I found by *running* the code rather than reading it
  (a `sys.exit` without `import sys`, a resume counter that double-counted rows,
  a BeautifulSoup multi-valued-attribute `TypeError`, and a currency-decoding
  bug that silently stripped `£` from every price).
- **[demo3-workflow](https://github.com/shoaibmahmud244-lang/demo3-workflow)** —
  form → CRM → email automation. The interesting bug: the run log was opened in
  `"w"` mode, so a re-run destroyed its own evidence and the replacement log
  *contradicted* the CSV it claimed to describe.

## How I work

I verify claims by executing them. Both repos document bugs found that way,
including the ones I'd shipped in a first pass — the currency bug in the scraper
silently produced plausible-looking numbers, which is exactly the kind of defect
that survives a code review and fails in front of a client.

Contributor to [OpenMontage](https://github.com/calesthio/OpenMontage) (61k
stars, AGPL-3.0) — reliability and checkpointing work in the video pipeline.
I contributed 42 of its 463 commits; it isn't mine, and I don't claim it as such.
