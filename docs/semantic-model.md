# Semantic model reference

The model screenshot is evidence of the existing structure. This document does not claim to reconstruct exact relationships from the image.

| Table | Visible / inferred role |
| --- | --- |
| Trans | Transfer records including dispatch and receipt fields |
| Append Transfer | IN/Out movement feed used by net branch receipt DAX |
| Sale | Sales quantities, values and transaction details |
| MRN | Quantity movements used by the supplied wastage measure; validate reason classification |
| Item List | Product attributes including code, name and category |
| Branch | Branch attributes |
| Date | Calendar used by the supplied previous-day measure |
| BB GRN Detail | Bakery goods receipt details |
| BB Dry Item | Physical stock submissions for dry items |
| Combo List | Combo product reference; component treatment is not supplied |
| SalesTargetTable | Branch/date sales targets |
| Date Only / Month Index | Additional date and month helper tables |

## Before rebuilding

- Identify the grain and unique key of every feed. Prevent duplicate ERP export rows.
- Confirm branch and item keys match across sources and dimension keys are unique.
- Use a contiguous calendar with one row per date and configure the appropriate date table.
- Normalize transaction timestamps to date keys before creating date relationships.
- Validate active relationships and filter propagation for branch, item and date on each fact.
- Review multiple date tables, bidirectional paths and fact-to-fact relationships for ambiguous filtering.
- Confirm IN/Out labels, return treatment, sales return signs and wastage signs.
- Restrict MRN to the relevant wastage reasons if it contains other movements.
- Confirm combo sales are expanded to components only once. The supplied screenshots do not establish whether this is already implemented.
- Reconcile a single item, branch and day against source documents before evaluating totals.

## Measure delivery

`dax/measures.dax` is a reference collection. Create each measure separately in Power BI Desktop; it is not an executable DAX query or an exported semantic model. Original table and field names are retained where supplied. Derived definitions are labeled in comments.
