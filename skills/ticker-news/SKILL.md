---
name: ticker-news
description: Research material corporate news and developments from the last month for user-specified tickers, then write a per-company markdown report with vertical ASCII news timelines and a 6-month horizontal financial calendar. Use when the user asks for ticker news, company news research, or a financial calendar for specific tickers.
---

# ticker-news

Research material corporate news and developments (last month; at most one
older highly-impactful item) for each ticker supplied by the user,
and write one markdown report named `[yyyy-mm-dd]-[TICKER]-financial-news.md`
(current date, UPPERCASE ticker) into the current working directory.

## Inputs

- One or more tickers introduced by the user after the command, bare (e.g.
  `SKHY`) or exchange-qualified (`NASDAQ:SKHY`, `NYSE:BAC`, `LSE:HSBA`).
- **The company is the subject; the ticker is only its label.** The same
  company may trade under several tickers (SKHY = NASDAQ ADR of SK hynix,
  HY9H = its Xetra line, 000660 = its Korea Exchange line). Research the
  COMPANY's news once; the same event set feeds every listing's report.
  Only the report header differs per listing. If a symbol is ambiguous,
  resolve it first (verify against recent headlines before researching).

## Procedure

### 1. Scope and dates

- Today = the report date. Window = the last 1 calendar month. Order strictly
  reverse-chronological (newest top).
- Selection rule (HARD CAP: 6 items TOTAL per company):
  1. EVERY high-impact (🔴) item in the window, regardless of who originated
     it — company filing, broker note, regulator, or press.
  2. EVERY company-originated event in the window at ANY impact level
     (filings, board decisions, results, dividends, orders, official
     statements).
  3. At most ONE item older than the window; it MUST be highly impactful
     (🔴). It becomes the `└─` tail. If nothing qualifies, omit it.
- If candidates exceed 6, rank: company-originated 🔴 > other-origin 🔴 >
  company-originated 🟡/🟢 > third-party 🟡/🟢; keep the top 6.
- "High-impact" = price-moving or strategically significant: earnings and
  preannouncements, guidance changes, M&A, dividends, buybacks, leadership,
  regulatory/legal, litigation, major contracts, large capital actions.
  Exclude routine fluff, recycled wire copies, and third-party market
  commentary that is neither high-impact nor company-originated.

### 2. Research pipeline (per ticker, ≤5-minute budget)

`web_search` first; fall back to `xd://mcp__open_websearch_search` if it fails
or returns nothing. Headline-first, two passes — article fetches are the
exception, never the sweep.

**Stage A — headline sweep (one parallel batch, NO article fetches):**
- Read 1–2 per-stock news feed pages (dated headline lists with URLs) in the
  same parallel batch: cnyes per-stock feed (`.../twstock/<TICKER>/news/stock`
  for zh markets), Goodinfo announcements ticker, MarketScreener news page,
  or the company IR press list. A feed page beats a dozen keyword searches —
  it also catches quiet weeks that keyword queries miss.
- Add at most 3 keyword searches, same batch:
  - `<Company> <TICKER> news <month-name> <year>` (month coverage)
  - Run keyword sweeps in the listing's home-market language too (zh for
    TWSE/SHCOMP, fr for Euronext Paris, de for Xetra...): local-language
    queries surface filings and trade press that English-only sweeps miss,
    especially for small caps.
  - `<Company> earnings date 2026` + `dividend date 2026` (calendar, combined)
  - `<Company> merger OR acquisition OR buyback OR filing 2026` (capital actions)
- Collect headline, exact date, outlet, URL, snippet. Snippets are data.

**Stage B — filter on headlines (no tools):**
- Apply the section-1 selection rule to headline+snippet evidence alone.
  Rank overflow, pick the ≤6 winners + optional 🔴 pre-window tail, and the
  ≥3 calendar events. A dated snippet from a real outlet is sufficient
  sourcing when it confirms event + date + origin — no fetch needed.

**Stage C — verify winners only (one parallel batch):**
- Fetch full text ONLY when the snippet lacks the date, lacks the key numbers
  the paragraph needs, or conflicts with another source. Cap: 6 article
  fetches per ticker. Batch the fetches together with any remaining
  calendar-date searches in one parallel block.
- One retry max per failed/empty search (simplified query), then move on.
- Never list an event you could not confirm. Report fewer items when fewer
  qualify — never pad with immaterial items; note the shortfall under that
  company's section.

**Stage D — compose and write the report. No further searching.**

**Time discipline:** target ≤5 min wall clock per ticker; run multiple
tickers in parallel. The two main time sinks are sequential one-result
search chains and fetching articles the snippet already confirms — both
prohibited.

### 3. Financial calendar

Build a forward 6-month calendar (current month + 5) per ticker. Include
whenever scheduled or confirmable:

- Earnings dates (next 1–2 quarterly reports)
- Dividend dates (ex-dividend, payment/declaration)
- Merger/acquisition milestones (votes, closings, offer expiries)
- Special events (investor days, AGMs, guidance updates, splits, listings)

Do not miss dividend and earnings dates. Use only scheduled/announced dates
with their day-of-month; mark unannounced-but-expected events with `[*]` and
the expected month. At least 3 events per company.

### 4. Report format (exactly)

The template below shows the exact file layout: line 1 of the FILE is the
bare ticker, then the ` ```text ` fence opens with the `#` heading, which
names both the ticker and the company, then a blank line.


```
NASDAQ:SKHY
```text
# NASDAQ:SKHY - 🗒 SK hynix Material News & Developments

▲ [Mon DD, YYYY]: <short event title>
│ 🔴|🟡|🟢 (high|med|low)  <description paragraph — EVERY report line ≤160 chars>
│                          <continuation of the description>
│                          Source: [<Source Name>](<url>)
│
├─ [Mon DD, YYYY]: <short event title>
│ 🟡 (med)          <description paragraph>
│                   Source: [<Source Name>](<url>)
│
...
└─ [Mon DD, YYYY]: <oldest item — the single pre-window item if used; MUST be 🔴>
  🔴 (high)         <description paragraph>
                    Source: [<Source Name>](<url>)


📅 6-Month Upcoming Financial Calendar

 <YYYY>                                    <YYYY+1>
 MON   ...   MONTH   ...   MONTH
  |     |     |     |     |     |
              +-- [DDth] <event>
                          +-- [*] <expected event>
```
````
(the ``` above ends the inner ```text fence; the file's line 1
`NASDAQ:SKHY` stays outside it)

### Hard format rules

- One `#` company section per ticker; `---` separator between companies.
- Timeline head `▲` on the newest item; intermediate items `├─`; the final
  (oldest) item `└─`. A blank `│` row separates consecutive items.
- Every item: date `[Mon DD, YYYY]`, importance indicator + severity word on
  its own line, then the description paragraph, then `Source: [<Source Name>](<url>)`.
- Line length: EVERY line of the report body inside the fence — header, dates,
  titles, description lines, `Source:` lines, calendar hooks, notes — is at
  most 160 characters. No exceptions. `Source:` lines count the URL: if the
  line would exceed 160 chars, shorten the source name (e.g. drop the article
  title before the ` — `), and if still over, place the URL on its own
  continuation line at the same indent:
  `│                (https://...full.url.../path)`.
- No minimum line length: short lines are fine; wrap for readability, never
  pad.
- `Source: [<Source Name>](<url>)` — put only the source name (e.g. `Reuters`,
  `BMPS IR`, `Bloomberg`) inside the brackets, never the article title.
- 🔴 high (price-moving, strategic), 🟡 med, 🟢 low. Selection is rule-driven
  (section 1), not a curated mix.
- Calendar: months left to right across the next 6 chronological months,
  `|` tick rows, `+--` event hooks positioned under their month; day in
  brackets `[DDth]`, `[*]` marks unannounced/expected. Never drop earnings or
  dividend dates.
- Fence layout (two distinct zones, exact order):
  1. First line of the file, OUTSIDE the fence: `NASDAQ:SKHY` (the bare
     exchange-qualified ticker, no `#`, no other text).
  2. Next, the ` ```text ` fence opening. INSIDE the fence, as its first
     line: `# NASDAQ:SKHY - 🗒 <Company Name> Material News & Developments`
     — the heading MUST name the company (e.g. `SK hynix`), not just the
     ticker. Then one empty line, then the timeline + calendar body, then
     the closing ` ``` ` as the last line of the file. So the file starts:
     `NASDAQ:SKHY` / ` ```text ` / `# NASDAQ:SKHY - 🗒 SK hynix ...` /
     blank / body...
  The fences are a matched pair — a dangling ` ``` ` with no ` ```text `
  opener renders the whole report as broken markdown. After ANY edit to a
  written report, re-read it and verify: bare ticker line present outside
  the fence, fence pair intact (both lines present, exactly one pair). If
  an edit must replace a range touching either fence line, re-add the fence
  in the same edit.
- File name: `[yyyy-mm-dd]-[TICKER]-financial-news.md` — e.g.
  `2026-09-06-SKHY-financial-news.md`. Multiple listings of the SAME company
  (e.g. `SKHY` and `HY9H`) each get their own file, but the research is done
  once per company and the event content is identical; only line 1 (bare
  ticker) and the `#` heading differ.
- Write the file with the `write` tool; do not print the full report in chat.
- ASCII inside fenced `text` blocks so monospace alignment holds in any viewer.