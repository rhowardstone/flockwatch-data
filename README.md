# FlockWatch corpus

99,435,526 deduplicated ALPR search records from 6,466
law-enforcement agencies, 2023-01-01 to 2026-06-30.

Every record was released by a government agency under public-records law.
Nothing here was scraped, purchased, or obtained through access to any system.

## Files

| file | what it is |
|---|---|
| `searches.parquet` | every search; ~785 MB |
| `sources.json` | every released file, its FOIA request URL, row count, date range, camera-owning agency |

## Reading it

```sql
-- DuckDB, no import step
SELECT reason, count(*) c FROM 'searches.parquet'
GROUP BY 1 ORDER BY c DESC LIMIT 20;
```

## Columns

| column | meaning |
|---|---|
| `searched_at` | when the search ran |
| `agency` | the agency that RAN the search |
| `owner` | the agency whose CAMERAS were searched |
| `officer` | officer identifier as released |
| `plate` | plate as released; `***` means the agency redacted it |
| `reason` | free text the officer typed |
| `reason_norm` | the same string, lowercased and trimmed. Nothing more |
| `case_number` | as released; absent on 95.5% of searches |
| `source_file` | the released file, relative to its FOIA request |
| `foia_url` | the request that produced it |

`agency` and `owner` are different agencies in almost every row. In a network
audit the exported file belongs to the agency that owns the cameras, while the
agency column names whoever ran the search. Reversing them inverts the finding.

## What this data does not say

**Reasons are not categories.** `reason` is free text. There are
1,675,563 distinct strings and the twenty most common
cover under a third of all searches. `reason_norm` differs only in case and
whitespace - it does not group `inv` with `investigation`, because deciding
those mean the same thing is a judgement about intent.

An earlier version of this project did make such judgements, and got them
wrong: a keyword rule counted `HSI` - Homeland Security Investigations, a
narcotics and trafficking unit - as immigration enforcement, including rows
reading `Drugs/Narcotics - HSI`. Those rules are gone. If you classify, publish
your rule.

**A missing case number is not misconduct.** 95.5% of searches
carry none. That is the norm in this corpus, and a fact about record-keeping
before it is a fact about any officer.

**Absence is not evidence of absence.** An agency missing here has not been
shown to search less. It has only not released a log.

**No plate-to-owner resolution.** These records do not identify vehicle owners
and this project performs no such lookup. Please do not build one.

## Deduplication

99,435,526 events from 887,928,694 released rows. The collapse is not
redundancy: a single search appears in the audit of every agency whose cameras
it touched - one search was observed in 80 separate FOIA productions. That
overlap is what makes cross-jurisdiction access provable, and reconciling
across witnesses recovered reasons and case numbers that individual agencies
had redacted.

Generated 2026-08-07T03:06:21Z · https://flockwatch.ryehowardstone.com
