# Golden Dataset

The golden dataset is a small set of real documents with manually verified expected values. It is the yardstick for extraction accuracy, confidence calibration, duplicate detection, and regression across layout changes. Nothing is tuned against it until it exists, and the full backlog is not processed until results against it are acceptable.

## Where it lives

The documents are private financial records. The dataset lives outside the public repository, in the owner's gitignored `Docs/private/golden/` folder or another private location, and is backed up outside git. The public repository holds only this format description, the CSV templates in `Docs/templates/`, and, once implementation starts, a few synthetic or fully redacted fixtures for automated tests.

## Layout

```text
golden/
  inventory.csv            one row per sample document
  expected.csv             one row per expected field value
  documents/
    <sample_id>.pdf        the source PDF, filename is the sample id
```

Sample ids are short and opaque (`s001`, `s002`, …) so nothing in a filename identifies an account.

## inventory.csv

One row per document. Template: `Docs/templates/document-inventory.csv`.

| column | meaning |
|---|---|
| `sample_id` | opaque id, matches the PDF filename |
| `institution` | institution name as it appears on the statement |
| `account_alias` | a private alias for the account, never the account number; the alias-to-account mapping is private configuration |
| `statement_type` | `investment`, `ira`, or another class |
| `period_end` | statement ending date, ISO `YYYY-MM-DD` |
| `native_or_scanned` | `native` if the PDF has real text, `scanned` if image only |
| `pages` | page count |
| `scan_quality` | `good`, `fair`, or `poor` |
| `layout_version` | free label so layout changes within an institution can be grouped, e.g. `A`, `B` |
| `notes` | anything unusual: duplicate of another sample, summary not on page 1, rotated page, zero-dollar lines |

The same file works as the representative document inventory before the expected values are filled in. Coverage to aim for is in the golden dataset issue: multiple years, more than one account, a layout change within one institution, native and scanned, one realistic poor scan, one duplicate, one zero-dollar line, one statement where the summary is not where expected.

## expected.csv

One row per field per document. Template: `Docs/templates/golden-expected-values.csv`.

| column | meaning |
|---|---|
| `sample_id` | matches `inventory.csv` |
| `field` | one of the field names below |
| `expected_value` | the value a careful human reads from the statement, in the canonical form below |
| `source_page` | 1-based page the value appears on |
| `ambiguous` | `true` if the statement itself is unclear, so a mismatch is not counted as an extraction error |
| `notes` | why it is ambiguous, or anything a reviewer should know |

First-profile field names and canonical forms:

| field | canonical form |
|---|---|
| `statement_end_date` | ISO `YYYY-MM-DD` |
| `account_alias` | the private alias, resolved from the account number on the statement |
| `deposits` | decimal, no currency symbol or thousands separators, sign as printed |
| `withdrawals` | same |
| `income` | dividends, interest, and other income; same |
| `other_transactions` | same |
| `net_change` | net change in portfolio value; same |

Zero values are recorded as `0.00`, not omitted, so zero-value suppression in export can be tested.

## What gets measured

- **Field accuracy**: exact match on canonical form per field, excluding rows marked ambiguous.
- **Account-identification accuracy**: reported separately and treated as the higher-priority metric, because a wrong account is the costliest downstream error.
- **False-confidence rate**: values the system rated high-confidence that were wrong.
- **Human-review rate**: share of values the system routed to review.
- **Duplicate detection**: the known duplicate is flagged; non-duplicates are not.
- **Layout regression**: accuracy broken out by `institution` and `layout_version`.
