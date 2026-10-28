# Evaluation test set

Fill this in after every refresh, before you publish. The agent is the system under test.
You are the grader. Nothing here is automated on purpose: the point is to decide, with a
source you trust, whether the agent told you the truth.

**Ground truth sources.** Pick one and use the same one every time, so your scores are
comparable week to week:

- Prices: Google Finance (google.com/finance), Yahoo Finance, or your brokerage app.
- News: the article itself. Open the link. Check the date on the page.
- Calendar: the company's investor relations page (search "TICKER investor relations
  events"), or the Fed's own meeting calendar for FOMC dates.

Refresh date: 2026-10-28 (BRIEF.asOf, mode "live", priceType "intraday")    Grader: number-checker agent

Grader notes: Google Finance (google.com/finance/quote/SYMBOL:EXCHANGE) returned only "Google Finance isn't supported on this device" for all five tickers, so it gave no data. Section A uses Yahoo Finance history pages (listed as an allowed ground truth above), which are independent of the brief's own source (stockanalysis.com). Pass for A therefore means "matches Yahoo Finance", not "matches Google Finance".

## A. Prices (one row per ticker)

Pass if the dashboard's last price is within $0.10 of the ground truth for the same day
and the same price type (close vs intraday). If the agent recorded an intraday price,
compare against the intraday price at about the same time; do not compare it to a close.

| Ticker | Date shown | Price type shown | Dashboard price | Ground truth price | Source you used | Pass / Fail |
|---|---|---|---|---|---|---|
| NVDA | 2026-10-21 | close | 221.97 | 221.97 | https://finance.yahoo.com/quote/NVDA/history/ (Google Finance did not load) | Pass |
| NVDA | 2026-10-22 | close | 212.21 | 212.21 | same | Pass |
| NVDA | 2026-10-23 | close | 210.96 | 210.96 | same | Pass |
| NVDA | 2026-10-26 | close | 216.00 | 216.00 | same | Pass |
| NVDA | 2026-10-27 | close | 217.94 | 217.94 | same | Pass |
| JPM | 2026-10-21 | close | 335.19 | 335.19 | https://finance.yahoo.com/quote/JPM/history/ | Pass |
| JPM | 2026-10-22 | close | 336.54 | 336.54 | same | Pass |
| JPM | 2026-10-23 | close | 331.58 | 331.58 | same | Pass |
| JPM | 2026-10-26 | close | 334.04 | 334.04 | same | Pass |
| JPM | 2026-10-27 | close | 335.79 | 335.79 | same | Pass |
| ACN | 2026-10-21 | close | 240.26 | 240.26 | https://finance.yahoo.com/quote/ACN/history/ | Pass |
| ACN | 2026-10-22 | close | 258.74 | 258.74 | same | Pass |
| ACN | 2026-10-23 | close | 249.37 | 249.37 | same | Pass |
| ACN | 2026-10-26 | close | 247.23 | 247.23 | same | Pass |
| ACN | 2026-10-27 | close | 235.16 | 235.16 | same | Pass |
| AME | 2026-10-21 | close | 250.28 | 250.28 | https://finance.yahoo.com/quote/AME/history/ | Pass |
| AME | 2026-10-22 | close | 238.82 | 238.82 | same | Pass |
| AME | 2026-10-23 | close | 240.03 | 240.03 | same | Pass |
| AME | 2026-10-26 | close | 242.81 | 242.81 | same | Pass |
| AME | 2026-10-27 | close | 246.22 | 246.22 | same | Pass |
| LLY | 2026-10-21 | close | 1270.75 | 1270.75 | https://finance.yahoo.com/quote/LLY/history/ | Pass |
| LLY | 2026-10-22 | close | 1306.69 | 1306.69 | same | Pass |
| LLY | 2026-10-23 | close | 1273.46 | 1273.46 | same | Pass |
| LLY | 2026-10-26 | close | 1279.14 | 1279.14 | same | Pass |
| LLY | 2026-10-27 | close | 1281.91 | 1281.91 | same | Pass |

Intraday `latest` values (not scored): the brief times are 10:21 to 10:24 AM; Yahoo pages were read at about 10:30 AM, so the timestamps are not close enough to grade. For reference, brief vs Yahoo at 10:30: JPM 337.75 vs 337.22; ACN 245.25 vs 244.74; AME 248.58 vs 247.90; LLY 1289.57 vs 1289.91; NVDA 218.07 vs 218.39 (Yahoo history row, intraday) and 218.24 (stockanalysis). Differences are $0.32 to $0.68 over 6 to 9 minutes, plausible movement but unverified.

## B. Weekly change (one row per ticker)

The page computes this from the five closes. Pass if it is within 0.3 percentage points of
your own calculation: (last price minus the close five trading days earlier) divided by
that earlier close.

Finding: `weekChange()` in the page uses `series(c)`, which is the five closes PLUS `latest.price` when `latest` exists. So the page computes (latest intraday minus Oct 21 close) / Oct 21 close, not (Oct 27 close minus Oct 21 close). The two disagree by more than 0.3 pp for four of five tickers. No percentage is typed into `BRIEF` (rule 6 ok); the CHANGELOG summary does contain typed percentages. All five tickers have five closes, so the page shows a figure from six points. Since all closes passed in A, the "correct close" recompute equals the column below.

| Ticker | Dashboard weekly change | Your calculation (last close vs first close) | Source link | Pass / Fail |
|---|---|---|---|---|
| NVDA | -1.8% = (218.07-221.97)/221.97 = -1.76% | (217.94-221.97)/221.97 = -4.03/221.97 = -1.82%; diff 0.06 pp | https://finance.yahoo.com/quote/NVDA/history/ | Pass |
| JPM | +0.8% = (337.75-335.19)/335.19 = +0.76% | (335.79-335.19)/335.19 = 0.60/335.19 = +0.18%; diff 0.58 pp | https://finance.yahoo.com/quote/JPM/history/ | Fail |
| ACN | +2.1% = (245.25-240.26)/240.26 = +2.08% | (235.16-240.26)/240.26 = -5.10/240.26 = -2.12%; diff 4.20 pp, opposite sign | https://finance.yahoo.com/quote/ACN/history/ | Fail |
| AME | -0.7% = (248.58-250.28)/250.28 = -0.68% | (246.22-250.28)/250.28 = -4.06/250.28 = -1.62%; diff 0.94 pp | https://finance.yahoo.com/quote/AME/history/ | Fail |
| LLY | +1.5% = (1289.57-1270.75)/1270.75 = +1.48% | (1281.91-1270.75)/1270.75 = 11.16/1270.75 = +0.88%; diff 0.60 pp | https://finance.yahoo.com/quote/LLY/history/ | Fail |

Note: the Fails are a method mismatch (page mixes an intraday price into a close-to-close change), not bad closes. The ACN case flips the direction shown (page: up, closes only: down). Owner decision needed: either compute from closes only, or label it as "since Oct 21 close, intraday".

## C. News grounding (one row per news item, three per ticker)

Open every link. Pass only if all four are true: the link opens, the headline matches the
article, the article date is within the last seven days, and the "why it matters" line is
supported by the article (not just plausible).

Window used: Oct 21 to Oct 28, 2026 inclusive. AME has no news items (brief says none found in window; not scored).

| Ticker | Headline (first few words) | Link opens | Headline matches | Dated within 7 days | "Why" is supported | Pass / Fail | Source link and note |
|---|---|---|---|---|---|---|---|
| NVDA | Nvidia to invest $3B in Meta's... | Yes | Yes | Yes (Oct 27) | Yes, mostly: $3B and expanded Nvidia infrastructure at Meta are in the article; the word "customer" is an inference (article never uses it) | Pass | https://stockanalysis.com/news/nvidia-3-billion-meta-offering/ |
| NVDA | OpenAI Turns to Google AI Chips... | Yes | Yes | Yes (Oct 28) | Yes: plans to buy Google chips, "final terms ... yet to be decided", not stopping Nvidia purchases | Pass | https://www.tipranks.com/news/openai-turns-to-google-ai-chips-to-diversify-beyond-nvidia |
| JPM | J.P. Morgan Asset Management Expands... | Yes | Yes | Yes (Oct 22) | Mostly: Solana launch and "multi-chain" language are in the release; "onto more blockchains" and "small but visible" are the brief's gloss (only Solana named) | Pass | https://www.prnewswire.com/news-releases/jp-morgan-asset-management-expands-access-to-its-tokenized-money-market-funds-on-solana-302917188.html |
| ACN | Accenture awarded $151.46M... | Yes | Yes | Yes (Oct 27) | Yes: "This contract was a sole-source acquisition"; $228M ceiling for "future in-scope special project requirements" | Pass | https://www.tipranks.com/news/the-fly/accenture-awarded-151-46m-air-force-task-order-thefly-news |
| ACN | Accenture downgraded to Neutral... | Yes | Yes | Yes (Oct 21, first day of window) | Yes: "shares have over doubled since June"; "fairly valued" paraphrases multiple "reasonable" | Pass | https://www.tipranks.com/news/the-fly/accenture-downgraded-to-neutral-from-outperform-at-baird-thefly-news |
| LLY | Lilly's retatrutide beats Ozempic... | Yes | Yes (shortened) | Yes (Oct 28) | Yes: 19.5% and 20.9% vs 9.4%; discontinuations due to adverse events 4.8% and 7.9% vs 2.7% | Pass | https://www.prnewswire.com/news-releases/lillys-retatrutide-delivered-greater-a1c-reduction-and-more-than-double-the-weight-loss-of-ozempics-highest-approved-dose-in-head-to-head-trial-302921328.html |
| LLY | Patrik Jonsson to retire... | Yes | Yes | Yes (Oct 27) | Yes: "U.S. and international businesses will come together as Lilly Global Markets" from Jan 1, 2027 | Pass | https://www.prnewswire.com/news-releases/patrik-jonsson-to-retire-after-36-years-ilya-yuffa-named-president-lilly-global-markets-302921191.html |
| LLY | Eli Lilly price target raised... | Yes | Yes | Yes (Oct 26) | Yes: Q3 revenue est. above Bloomberg consensus, upside led by international Mounjaro | Pass | https://www.tipranks.com/news/the-fly/eli-lilly-price-target-raised-to-1450-from-1385-at-bernstein-thefly-news |

(Add rows as needed. Fifteen items is normal for five tickers.)

## D. Calendar (one row per event)

Pass if the event and its date appear on the source linked, and the date is in the future
relative to the refresh date.

| Ticker | Event | Date shown | Confirmed on source | Pass / Fail | Source link and note |
|---|---|---|---|---|---|
| JPM (market-wide) | FOMC meeting ends; decision due afternoon | 2026-10-28 | Yes: Fed lists Oct 27-28 meeting, no asterisk, not tentative. Date equals asOf, not before. Brief itself says the Fed page does not state the decision day. | Pass | https://www.federalreserve.gov/monetarypolicy/fomccalendars.htm |
| AME | Q3 2026 earnings before open; call 8:30 AM ET | 2026-11-03 | Yes: AMETEK press release (Oct 15): Tuesday Nov 3, before market open, call 8:30 AM ET. AMETEK's own IR events page was not opened; confirmed via release plus search results quoting it. | Pass | https://www.prnewswire.com/news-releases/ametek-announces-third-quarter-2026-earnings-call-and-webcasted-investor-conference-call-information-302908794.html |
| LLY | Q3 2026 earnings release | 2026-10-29 | Yes, via secondary source quoting Lilly's own release ("will announce its third-quarter 2026 financial results on October 29, 2026", call 10 a.m. ET). Lilly IR page (investor.lilly.com) returned HTTP 403. One outlier site (EarningsWhispers) says Nov 2 as "expected". Brief cites stockanalysis.com, not Lilly. | Pass | https://www.biopharmawatch.com/news/LLY/lilly-third-quarter-2026-financial-results-call |

## E. Omissions

Did the agent miss something a human analyst would have included? List anything you found
in the ground-truth sources that the brief does not mention (a big headline, an earnings
date inside the window). Each one counts as a Fail in the score below.

- 
- 

## F. Your own test cases

Write two tests of your own that none of the sections above cover. Examples students have
used: "the as-of date on the page matches the changelog date", "no ticker shows a flat
sparkline when the real stock moved", "the page still renders if I delete one ticker's
news list in the data". Write the test, run it, record the result.

| Test | How you checked | Pass / Fail |
|---|---|---|
| | | |
| | | |

## Score

| Section | Passes | Total | Rate |
|---|---|---|---|
| A. Prices | 25 | 25 | 100% |
| B. Weekly change | 1 | 5 | 20% |
| C. News | 8 | 8 | 100% |
| D. Calendar | 3 | 3 | 100% |
| E. Omissions (fails only) | 0 | | |
| F. Your own | | | |
| **Overall** | | | |

Publish only if A and B are 100% and C is at least 80% after fixes. If a row failed, tell
Claude exactly which row and what the correct value is, let it fix only that, rerun the
`qa-reviewer` agent, and regrade that row. Keep the original Fail in this file: a record of
what the agent got wrong is more useful than a clean sheet.
