# Phyllo Product Analyst Intern — Take-Home Assignment

## Meridian API Investigation

**Candidate:** Harsh Nilay  
**Role:** Product Analyst Intern  
**Submission:** Browser-based take-home assignment

### Submission File

**Harsh_Nilay_Product_Analyst_Assignment.html**

### Project Entry Point

`index.html` contains the same final submission and is the browser/deployment entry point.

## Source Basis

This analysis uses only the supplied candidate-pack files:

- `API_DOCS.md`
- `README.md`
- `ticket.md`
- `orders_page1.json`
- `orders_page2.json`
- `order_ord_9999.json`

## Tasks Covered

### Task 1 — What Doesn't Match?

Six documented-vs-actual mismatches are identified:

1. Status enum mismatch: `refunded` appears although it is not in the documented status enum.
2. Monetary format mismatch: `ord_1006` returns decimal monetary values rather than smallest-unit integers.
3. Nullable email mismatch: `ord_1005.customer.email` is `null` despite being documented as always present.
4. Missing-order status mismatch: the captured missing-order response is HTTP `200` with `{"order": null}` rather than the documented `404`.
5. Ordering mismatch: captured records progress oldest-to-newest rather than most-recent-first.
6. Pagination mismatch: page 1 has `has_more: false` while also providing a `next_cursor`, and page 2 exists.

The monetary-format mismatch in `ord_1006` is identified as the most serious issue because clients following the documented contract could interpret the monetary units incorrectly, affecting financial calculations and reconciliation.

### Task 2 — What's the Total Revenue?

The six captured order totals sum to **$328.03**.

This is stated as the **sum of captured order totals**, not confirmed net revenue. `ord_1003` is marked `refunded`, but no refund amount is supplied, so exact net revenue cannot be confirmed from the supplied data.

For `ord_1006`, the reported components reconcile as:

`$44.00 + $3.63 + $5.99 = $53.62`

### Task 3 — Stakeholder Communication

The submission contains a concise finance-facing response to Priya and an engineer-ready bug report for the monetary-format issue.

## Repository Structure

```text
HARSH-NIALY-PHYLLO/
├── index.html
├── Harsh_Nilay_Product_Analyst_Assignment.html
└── README.md
```

Open `index.html` for the final browser-based submission.

## Scope and Limitation

The analysis does not infer unavailable information. In particular, the supplied data does not provide the Meridian dashboard total or the refund amount for `ord_1003`, so the exact dashboard discrepancy and confirmed net revenue cannot be established from the supplied evidence alone.
