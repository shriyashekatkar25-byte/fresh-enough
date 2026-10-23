# fresh-enough · what should a new connector's default sync cadence be?

**Live demo:** https://shriyashekatkar25-byte.github.io/fresh-enough/
**Gallery:** [APM Lab](https://shriyashekatkar25-byte.github.io/apm-lab/)

An APM Lab decision prototype by **Shriya Shekatkar** (UCLA, Statistics & Data Science minor · Stats 102C Monte Carlo Methods).

## The launch it mirrors

On **October 20, 2026**, Databricks added three managed connectors to Lakeflow Connect in **Beta**: **SurveyMonkey** (surveys, collectors, responses, contacts), **Oracle Eloqua** (contacts, campaigns, form submissions, website visitors) and **Asana** (projects, tasks, goals). All three use OAuth U2M authentication, and the SurveyMonkey and Eloqua connectors support incremental ingestion.

Sources (looked up for this project):
- [Databricks platform release notes, October 2026](https://docs.databricks.com/aws/en/release-notes/product/2026/october)
- [SurveyMonkey connector docs](https://docs.databricks.com/aws/en/ingestion/lakeflow-connect/surveymonkey) · [Oracle Eloqua connector docs](https://docs.databricks.com/aws/en/ingestion/lakeflow-connect/oracle-eloqua) · [Asana connector docs](https://docs.databricks.com/aws/en/ingestion/lakeflow-connect/asana)

## The PM decision

If I were the PM for these connectors: **what should the default sync schedule be for a newly created pipeline: every 15 minutes, hourly, every 6 hours, or daily?**

SaaS apps meter API calls per account per day. Syncing too often burns the customer's quota (and breaks their other integrations); syncing too rarely makes the dashboard stale. Most users never change the default, so the default is the product.

- **Success metric:** fresh-and-safe day rate, the share of customer-days where a dashboard read sees data within the freshness target *and* the connector stayed under the daily API quota.
- **Guardrails:** quota-breach days ≤ 2% · P90 staleness at read ≤ target · median quota used ≤ 60% · sync compute ≤ $40 per connector-month.
- **Stakeholders:** ingestion engineering, connector partners' API teams, field engineering & support, pricing/FinOps, dashboard PMs.

With the default settings (2 h target, 2,500 calls/day, 10 objects, 4,000 records/day, seed 102), hourly wins: about 99.8% fresh-and-safe days and no breaches, while 15-minute syncs breach quota on about 9% of days. Drag the quota or object sliders and the memo rewrites itself, including the case where no fixed cadence passes and an adaptive schedule becomes the next step.

## What it shows about product judgment

| Databricks APM skill | Where it shows up |
|---|---|
| Customer problem framing | "Fresh enough" is defined as a measurable promise, with the hidden cost (someone else's API quota) made explicit |
| Prototyping | A working simulator with sliders, seeded RNG, live canvas and a memo that updates itself |
| SQL / Python | Section 05 shows how to replace simulated days with observed connector-days (illustrative tables) |
| Data-driven decisions | Guardrails decide which options are eligible; the noise band decides whether a gap is real |
| Clear writing | A director memo and a one-sentence VP summary generated from the numbers |

## Methods

| Method (Stats 102C) | Used for |
|---|---|
| Ch1 · Monte Carlo "fraction inside" estimation | Each simulated customer-day is a dart at (staleness ÷ target, calls ÷ quota); the share landing in the unit square estimates the fresh-and-safe probability |
| Ch1 · Binomial count, 1/n variance | ±1.96·SE band on the running estimate, shown shrinking as n grows |
| Ch1 · Sample means & the Strong Law of Large Numbers | Expected cost per connector-month and expected staleness (width × average height of the sync window) |
| Ch1 · Seeded pseudorandom numbers | Same seed → same darts → same memo; common random numbers across the four cadences |

## Design baseline

Four pins picked at random from the home feed, each redrawn from scratch in CSS/SVG. No pin images are used, embedded or linked as images.

- **Hero:** [Yes You Can Print · Desenio](https://www.pinterest.com/pin/684969424623597677/): cobalt field (#0B4EA2), cream (#EFE3C6) tall condensed caps staggered left / center / right.
- **Cards & buttons:** [Engineer Definition Sticker · Footnotes Paper Inc.](https://www.pinterest.com/pin/4608097324341110400/): white die-cut stickers with a soft shadow, a dictionary-entry serif word, pronunciation and italic part of speech.
- **KPI badges:** [é meu sonho! · nefte](https://www.pinterest.com/pin/452752568813274617/): lotus palette of petal pink (#F7C3DA), magenta (#D23C82) and bud green (#A9BE7B), shaped like petals.
- **Simulation room:** [Satin Polka Dots Tabi Mary Jane Flats · RABOESY](https://www.pinterest.com/pin/928163804476944005/): black satin sheen with white polka dots (the samples are the dots), plus a strap-and-buckle divider.

---

Unofficial concept prototype by Shriya Shekatkar. All data is simulated. Not affiliated with any company.
