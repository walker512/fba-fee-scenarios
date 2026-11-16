# A Narrow Workflow Difference: Preserve Two Rate Versions for One SKU

Baseline: [jhalzel/Amazon-Seller-Profit-Calculator](https://github.com/jhalzel/Amazon-Seller-Profit-Calculator), fixed commit `5bd15f42ed092351817363ac2de6138a6db64996`. Its [Profits.js](https://github.com/jhalzel/Amazon-Seller-Profit-Calculator/blob/5bd15f42ed092351817363ac2de6138a6db64996/src/pages/home/Profits.js) was reviewed on 2026-11-16.

## The same task

Product `SAME-ASIN`: price 25, cost 10, 100 units, inventory age 200 days, volume 0.1 cubic feet per unit. Both scenarios use a 15% referral rate and volume storage of 0.5 per cubic foot. Baseline fixed storage is 1 per unit versus 1.2 in the comparison. Baseline fulfillment is 4 and age surcharge 0.2; comparison fulfillment is 7 and age surcharge 0.3. The age surcharge is applicable in this fixture.

The user wants to preserve both complete input sets, compare the difference, close the tool, then restore and recalculate.

- **Our executed result:** both versions remain in the recoverable project. Baseline net amount is 600; comparison is 270; total difference is -330 USD. These are fictional deterministic inputs, not real Amazon rates.
- **Competitor source inspection:** saving a new result for an ASIN iterates through existing records, removes results with that ASIN, then adds the new result. The saved object contains the current aggregate result, not coexisting baseline/comparison rate inputs.
- For the specific workflow of retaining and restoring two complete rate sets for the same SKU, this tool supplies a path absent from that fixed version's save logic. A user of the competing tool could still use distinct ASIN labels or external records. This is not evidence of an arithmetic error in that tool.

## Evidence limits

Our restore round trip, net amounts, and differences were tested internally. The competitor was inspected at a fixed source version; its complete React/Firebase application was not installed or run. Click counts and speed were not benchmarked. We do not claim stronger overall functionality, greater arithmetic accuracy, faster execution, or market uniqueness. A complete two-application browser comparison has not been performed.

The competing tool supports FBA/FBM and cloud records. This project is limited to two local scenarios with user-supplied rates; it does not provide FBM support or cloud synchronization. Practical value still needs validation with real users.
