# ticker-news

A ready-made skill (a set of instructions an AI assistant can follow) that
researches the latest important company news for stocks you pick and writes
them up as tidy reports.

## What it does

This skill is meant to be used as a monthly routine: once a month, it looks
back over the **last month** and collects the important news for the stock
tickers you care about (if any happened). For every ticker you give it, the
assistant searches for real, meaningful news from that one-month window and
saves one report file per company in the folder you run it from:

1. **Picking the news** — only the things that actually matter: big
   price-moving events (earnings, mergers, dividends, new leadership, lawsuits,
   major contracts), anything the company itself announced, plus at most one
   older item if it was truly significant. Six items max per company, newest
   first.
2. **Finding the news** — the assistant scans news pages and runs a few smart
   web searches, including searches in the company's home language, so it
   doesn't miss announcements that never made it into English media.
3. **Upcoming dates** — a simple calendar for the next 6 months showing when
   the next earnings report, dividend, or big company event is expected. If a
   date hasn't been officially announced yet, it's marked with `[*]`.
4. **The report** — one file per company, named like
   `2026-09-06-SKHY-financial-news.md`. It contains a timeline of recent news
   (newest on top, each with date, importance marker 🔴🟡🟢, a short summary,
   and a link to the source) followed by the 6-month calendar.

Everything is plain text inside the file, lines stay short so nothing breaks
the layout in any viewer, and nothing is included that couldn't be confirmed —
if there's less real news than expected, the report simply says so instead of
padding with fluff.

## What you need

- An AI assistant with **web search capability** — this is the one hard
  requirement, since searching the news is the whole job. Any assistant that
  can search the internet will do.
- The ability to save files, since reports are written to disk rather than
  printed in chat.
- No accounts or sign-ups required. My setup runs on a **free Brave
  Search API key** as the main search, with **free open-websearch**
  (DuckDuckGo-based, no key needed) as backup when the first one is busy or
  rate-limited — but the skill works with whatever search your assistant
  already has.

## How to use it

Just ask your assistant, mentioning the skill and the ticker(s) — plain
symbols or with the exchange name in front:

```
/ticker-news SKHY
/ticker-news NASDAQ:SKHY NYSE:BAC LSE:HSBA
```

Several tickers can be handled at once; each gets its own report file. The
exchange prefix is just for show — the symbol is what gets searched, and if a
symbol is ambiguous the assistant double-checks recent headlines first.

## Sharing / installing elsewhere

Copy the whole `skills/ticker-news` folder — the skill file and this README
travel together. Two ways to install it:

- **For you personally**: drop it into the personal skills folder your
  assistant reads (wherever your other custom skills live).
- **Install straight from GitHub**: paste this prompt into your assistant:

  ```
  Install the skill from https://github.com/mpascariu/ticker-news — it's
  the ticker-news skill in the skills/ticker-news folder. Download it, put
  it into my personal skills folder, and confirm it's ready to use.
  ```
