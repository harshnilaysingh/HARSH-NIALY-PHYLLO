**PRODUCT ANALYST INTERN | TAKE-HOME ASSIGNMENT** 

# **Product Analyst Intern** 

### **Final Take-Home Submission | API, Data Quality & Product Investigation** 

#### **SOURCE BASIS** 

Analysis is based only on the provided candidate-pack files: API_DOCS.md, README.md, ticket.md, orders_page1.json, orders_page2.json, and order_ord_9999.json. 

## **1 WHAT DOESN'T MATCH?** 

|**No.**|**Documentation**|**Actual behavior**|**Impact**|
|---|---|---|---|
|1|`status` is one of `pending`, `shipped`,<br>`delivered`, `cancelled`.|`orders_page1.json` -> `data[2].status` is `refunded`.|Yes. A client validating the<br>documented enum could<br>reject or mishandle the order.|
|2|Monetary amounts are integers in the<br>smallest currency unit.|`orders_page2.json` -> `ord_1006` returns `subtotal: 44.0`, `tax:<br>3.63`, `shipping: 5.99`, `total: 53.62`.|Yes - high impact. Financial<br>calculations can be<br>incorrect.|
|3|`customer.email` is always present.|`orders_page2.json` -> `ord_1005.customer.email` is `null`.|Yes. Consumers expecting a<br>string may fail validation or<br>downstream processing.|
|4|A missing order returns HTTP 404.|`order_ord_9999.json` returns `{"order": null}`; `README.md`<br>records HTTP 200 for `GET /v1/orders/ord_9999`.|Yes. Clients may treat a<br>missing order as a successful<br>lookup.|
|5|Orders are returned most-recent-first.|The `created_at` values in `orders_page1.json` and<br>`orders_page2.json` increase from March 14 through March 16, so<br>the captured sequence is oldest-to-newest, not most-recent-first.|Yes. Consumers relying on<br>the documented order may<br>process or display records<br>incorrectly.|
|6|`has_more` determines whether another<br>page should be requested.|Page 1 has `has_more: false` but also provides `next_cursor`, and<br>page 2 exists.|Yes. A client following<br>`has_more` could stop early<br>and miss later orders.|



#### **MOST SERIOUS FINDING** 

The monetary-format mismatch in `ord_1006` is the most serious issue. The contract specifies integer amounts in the smallest currency unit, but the response contains decimal currency values. A consumer following the documented contract could scale the amount incorrectly, creating material reporting, reconciliation, and downstream financial errors. 

## **2 WHAT’S THE TOTAL REVENUE?** 

|**Order**|**Total**||
|---|---|---|
|ord_1001||$54.70|
|ord_1002||$23.81|
|ord_1003||$102.33|
|ord_1004||$68.10|
|ord_1005||$25.47|
|ord_1006||$53.62|
|**TOTAL**||**$328.03**|



The captured orders therefore sum to $328.03. 

Assumption: `ord_1001`-`ord_1005` follow the documented smallest-currency-unit format. `ord_1006` is inconsistent, but its values reconcile as: 

$44.00 + $3.63 + $5.99 = $53.62 

Meridian API Investigation | Harsh 

**PRODUCT ANALYST INTERN | TAKE-HOME ASSIGNMENT** 

I therefore treat `53.62` as $53.62. One limitation remains: `ord_1003` is marked `refunded`, but the supplied data contains no refund amount. Therefore, $328.03 is the sum of the order totals; exact net revenue cannot be determined without the refund information. 

#### **DECISION NOTE** 

The requested calculation is the sum of the captured order totals. It should not be presented as confirmed net revenue because the refund amount is not supplied. 

## **3.A REPLY TO PRIYA** 

#### **EMAIL** 

Finance-facing response: concise, factual, and clear. 

Subject: Revenue discrepancy 

Hi Priya, 

Thanks for flagging this. I reviewed the captured API responses and found two issues relevant to reconciliation: one order is marked `refunded`, and `ord_1006` uses decimal monetary values even though the API documents amounts as integer smallest-unit values. 

The captured orders sum to $328.03 when `ord_1006`'s `53.62` is treated as $53.62. I cannot confirm the exact difference with the Meridian dashboard because the supplied data does not include the dashboard total or the refund amount for `ord_1003`. 

I recommend correcting the unit handling for `ord_1006` and reviewing the refunded order before finalizing the reconciliation. 

Best, Harsh 

## **3.B BUG REPORT** 

Title: Order API returns monetary values in an inconsistent format 

Problem: `responses/orders_page2.json` returns `ord_1006` with decimal monetary values although the API documentation requires integer amounts in the smallest currency unit. 

Actual response: 

subtotal   = 44.0 tax        = 3.63 shipping   = 5.99 total      = 53.62 unit_price = 44.0 

Expected for USD: 

subtotal   = 4400 tax        = 363 shipping   = 599 total      = 5362 unit_price = 4400 

Impact: Clients implementing the documented contract may interpret these values incorrectly, affecting revenue calculations, reconciliation, reporting, and downstream financial processing. 

Fix: Return all monetary fields consistently in the documented smallest-unit integer format, and audit affected records and downstream consumers. 

Meridian API Investigation | Harsh 

