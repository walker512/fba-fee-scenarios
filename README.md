# FBA Fee Scenarios

An experimental v0.2, dependency-free, offline tool for comparing two fee scenarios per SKU. The application interface remains in Simplified Chinese. MIT licensed.

**All rates are entered manually. Sample rates are fictional, not official Amazon fees. This is not tax, investment, or business advice.**

## Start locally

Download the complete repository ZIP and extract it. Open a terminal in the directory containing `index.html`. With Python 3 installed, start a loopback-only static server:

```sh
python3 -m http.server 8000 --bind 127.0.0.1
```

On Windows with Python Launcher:

```powershell
py -3 -m http.server 8000 --bind 127.0.0.1
```

Open `http://127.0.0.1:8000` in your browser. Stop the server with Ctrl+C. Serve only this project directory, not a parent directory containing private files. No API, key, or paid service is required; Python only serves the local page.

Directly opening `index.html` with `file:///` has not been verified and is not guaranteed here.

## Workflow

The first page provides a blank form. Enter your data directly, or load the fictional example and generate a comparison. Creating a new blank project replaces unsaved content, so save first.

Edit the generated fields, add or remove records, then validate again. Advanced users can paste JSON and apply it to the form. Download a JSON report, or save a **recoverable project** containing the complete inputs. Restore that project from a local file later. A read-only report cannot restore editing.

No account, API key, external assets, or backend is involved. Closing the page discards unsaved input. Both projects and reports may contain business information; keep them secure.

## Input schema and limits

- `currency`: a three-letter uppercase currency code.
- `volumeUnit`: `cubic_ft` or `cubic_m`. Every volume-based storage rate must use that same unit; the tool does not convert units.
- `scenarios`: exactly two entries, each with a unique `name`, valid effective `date`, `validUntil`, manual verification date `verifiedAt`, billing-period length `periodDays` (integer 1-366), and `source` description.
- `asOfDate`: your analysis date. Verification dates cannot be later than it.

Dates only flag rates that are not yet effective or are expired. They do not validate official fees or project future inventory age. Different scenario periods generate a warning: their difference must not be interpreted as a same-period change.

Each `skus` entry requires a unique `sku`, selling `price`, `cost`, integer `units`, integer inventory age `ageDays`, and `volumePerUnit`. Each SKU has two `rates` entries in scenario order:

| Field | Meaning |
| --- | --- |
| `referralPct` | Referral percentage, 0-99.99 |
| `fulfillment` | Fulfillment fee per unit |
| `storagePerUnit` | Fixed, non-volume storage allocation per unit |
| `storagePerVolume` | Storage fee per volume unit for the specified period |
| `otherPerUnit` | Other fees per unit |
| `agedThresholdDays` | Inventory-age threshold |
| `agedPerUnit` | Additional per-unit charge at or above that threshold |

All numeric values must be nonnegative. You must convert fees to the intended period and allocate them per sold unit without double counting.

Results include a readable table and full JSON. Limits: 500 records, 300 characters per text field, 500,000 characters of page input, and **500,000 UTF-8 bytes** for both saving and restoring a project. The complete formatted project's byte size is checked before saving; non-ASCII characters may use multiple bytes. Invalid input blocks the report rather than silently inserting zeroes or guessing values.

See `sample.json`. All example data is fictional, not customer data. JSON labels are rendered as text.

## Formulas and scope

- Volume storage fee per unit = `volumePerUnit * storagePerVolume`.
- Inventory-age surcharge per unit = `agedPerUnit` when age reaches the threshold; otherwise zero.
- Fees per unit = selling price times referral percentage / 100, plus fulfillment, fixed storage allocation, volume storage, other fees, and the age surcharge.
- Net amount per unit = selling price minus cost minus fees.
- Total net amount = net amount per unit times units.
- Break-even price = (cost plus fixed fees) / (1 - referral percentage / 100).

This is **arithmetic on user-supplied per-SKU fee scenarios**, not an automatic official-fee audit. Both scenarios use the same supplied inventory age. All units are assumed sold with unchanged allocation. Historical inventory batches, complex age tiers, size classes, monthly inventory accounting, taxes, returns, and exchange rates are not modeled.

Calculations use floating point. Tables display two decimals; JSON reports display at most six. Recoverable projects preserve original inputs. Check the calculations manually; do not use them as settlement records.

## Verification

**68 internal automated tests passed**, covering calculations, invalid input, backup/restore, UTF-8 file-size boundaries, import races, and UI state. DOM simulation and static CSS checks are not real-browser acceptance. The public package contains no test scripts or dedicated test fixtures.

A downloaded and extracted copy was tested in real Chromium on the cloud computer over localhost: manual entry, two-rate calculations, save/restore, real downloads, and a 400 px narrow layout. `file:///` and other browsers remain unverified. A narrow browser window is not a physical-phone test.

## Comparison

See [COMPARISON.md](COMPARISON.md) for the fixed-version comparison and its evidence limits. No claim of overall superiority, profitability, or guaranteed results is made.

## Privacy, contributions, and license

The code makes no network requests, automatically reads no filesystem data, and does not persist data automatically. It reads only project files you explicitly select, and downloads are user-triggered. Do not put sensitive records in public issues. Report bugs with a redacted minimal example. Contributions should be tested and must not introduce tracking, paid endpoints, or required keys.

This prototype has no professional certification. MIT; see [LICENSE](LICENSE).
