# Business process and reconciliation rules

## Operational flow

1. Central bakery records a transfer out, prints the delivery note and dispatches goods.
2. Outlet checks physical quantities and records the transfer in.
3. Outlet records sales, wastage, returns and onward transfers.
4. The following-day review identifies discrepancies and assigns investigation.
5. Authorized corrections are verified against source transactions after refresh.

The intended controls are accurate dispatch entries, timely receipts, responsible handling and complete daily wastage recording. A variance is an investigation signal, not evidence of misconduct.

## Quantity definitions

| Measure | Definition |
| --- | --- |
| Transfer Out | Bakery dispatch quantity from `Trans[out_qty]`; confirm source context identifies bakery dispatches |
| Net Branch In Qty | Inbound outlet quantities minus outbound outlet quantities |
| Sale Qty | Quantity in the sales fact, under the model's sign and filter conventions |
| Wastage Qty | Quantity classified as wastage; the supplied DAX sums MRN quantities and assumes appropriate source/filter scope |
| Difference Qty | Net branch in minus sales minus wastage |
| Yesterday Difference Qty | Difference for the previous calendar day under the relevant item and branch filters |
| Final Difference Qty | Current difference plus previous-day difference |

## Interpretation

| Current difference | Meaning to investigate |
| --- | --- |
| Zero | Recorded net receipts equal recorded sales plus wastage under the assumptions |
| Positive | Net receipts exceed recorded sales and wastage; investigate missing postings or goods |
| Negative | Recorded sales and wastage exceed net receipts; investigate missed receipts, excessive postings or prior-day settlement entries |

Let D be today's difference and Y yesterday's difference. Final difference F = D + Y.

| Condition | Valid interpretation |
| --- | --- |
| D = 0 and F = 0 | Both daily differences are zero |
| D = 0 and F ≠ 0 | Yesterday's difference remains visible in the two-day indicator |
| D ≠ 0 and F = 0 | Today and yesterday have equal and opposite differences; verify whether a linked correction explains this |
| F > 0 | Combined two-day difference is positive |
| F < 0 | Combined two-day difference is negative |

A positive D and zero F implies a negative Y, not an absence of yesterday's variance. This corrects an inconsistent interpretation in the original process notes. Two daily values alone cannot establish causation or settlement status.

## Worked example

Bakery dispatch: 25 fish buns. Outlet physical receipt and posted receipt: 20. Sales: 15. Wastage: 3.

- Dispatch-to-receipt gap = 25 − 20 = 5. Investigate separately with the bakery and delivery records.
- Outlet difference = 20 − 15 − 3 = 2. Investigate at the outlet.

For the prior day, suppose the original difference was 3. A supported backdated wastage entry of 1 changes that difference to 2. If the next day has net receipts of 20, ordinary sales of 15, wastage of 5 and an authorized adjustment recorded in the sales feed for 2 additional units, the next-day difference becomes −2. The two-day indicator is 0.

This is consistent with an offsetting correction, but the audit trail must confirm the link. Adjustment quantities should be identifiable separately from genuine customer sales so demand and revenue analysis are not distorted. This documentation does not recommend creating customer sales to conceal missing stock.

## Wastage percentage

The dashboard total shows 42 wastage units and 108 sale units; 42 / (42 + 108) = 28%. This supports a display denominator of sales plus wastage for the shown total. The original percentage DAX was not supplied; the reference measure uses this denominator explicitly.

This differs from wastage divided by receipts. Do not interchange the two measures.

## Boundaries

The general stock equation is opening stock + net receipts − sales − wastage − closing stock, with other movements added according to the agreed accounting treatment. The simplified fresh-item calculation assumes opening and closing quantities are zero. `BB Dry Item` physical stock appears in the model, but its original reconciliation measures were not supplied.

Use a single date when interpreting the previous-day indicator. With a range selected, DATEADD shifts the whole selected date set; the result is not necessarily one day's difference. Late corrections also mean refreshed history may differ from a previously distributed report. Daily snapshots or a separate correction log would be needed for historical as-reported tracking.
