# Test cases — REQ-DMF-602

The scenarios below are the test plan from the approved TDD (section 6), unchanged. The **Result** column records only what is backed by evidence in this folder; a scenario without evidence is marked *Not yet recorded* rather than assumed to pass.

## Build verification

| Check | Result |
|---|---|
| Compile (X++ compiler, model DYLOExtensions) | ✅ 0 errors |
| Delivered | 2026-11-22 |

## Functional scenarios

Tested in Dynamics 365 Finance & Operations 10.0.48 on the Contoso demo data, legal entity USMF.

| # | Scenario (AC) | Steps | Expected result | Result | Evidence |
|---|---|---|---|---|---|
| T1 | UI display and entry (AC-01) | Open a customer and go to the Sales order defaults FastTab. Set Loyalty Tier = Gold and Preferred Delivery Window = `08:00 AM - 12:00 PM`. Save and reopen. | The Commercial Preferences group appears after Site, Warehouse and Pool. The values are saved and shown. | ✅ Passed | [a_T1_1_form_before_US-004.png](evidence/a_T1_1_form_before_US-004.png) · [a_T1_3_form_saved_reopened_US-004_Gold.png](evidence/a_T1_3_form_saved_reopened_US-004_Gold.png) |
| T2 | New customer default | Create a new customer on the form without touching Loyalty Tier. | Loyalty Tier = Standard. | ✅ Passed | [b_T2_2_new_customer_LT-001_defaults_Standard.png](evidence/b_T2_2_new_customer_LT-001_defaults_Standard.png) |
| T3 | DMF export (AC-02) | Export Customers V3 to CSV, Excel and XML. | Both columns are present. T1's customer shows `Gold` and the window. Other customers show `Standard`. | ✅ Passed CSV, Excel and XML exports each succeeded with 38 records. The Excel and XML files were read in full; the CSV file has no text qualifier, so only part of its rows could be checked column by column. | [h_T3_CSV_1_export_execution_summary.png](evidence/h_T3_CSV_1_export_execution_summary.png) · [h_T3_EXCEL_2_exported_file_columns.png](evidence/h_T3_EXCEL_2_exported_file_columns.png) |
| T4 | DMF import and defaulting (AC-03) | Import a file with: one new customer with `silver`; one new customer with a blank tier; one existing Gold customer with a blank tier; one existing customer with `Platinum` and a new window. | The results are Silver, Standard, Standard (A2), and Platinum with the new window. | ✅ Passed | [i_T4_3_import_execution_summary.png](evidence/i_T4_3_import_execution_summary.png) · [i_T4_4_import_file_and_customers_after.png](evidence/i_T4_4_import_file_and_customers_after.png) |
| T5 | Invalid value, DMF (AC-05) | Import a row with Loyalty Tier `Diamond` and a changed window for an existing customer whose tier is Gold. | The row is an error in the execution log with the message "Loyalty Tier value 'Diamond' is not valid…". The customer is still Gold, with the old window. The other rows import. | ✅ Passed | [i_T5_1_import_staging_rows_Diamond_error.png](evidence/i_T5_1_import_staging_rows_Diamond_error.png) · [i_T4_4_import_file_and_customers_after.png](evidence/i_T4_4_import_file_and_customers_after.png) |
| T6 | OData (AC-04) | (a) GET `/data/CustomersV3?$select=CustomerAccount,LoyaltyTier,PreferredDeliveryWindow`. (b) POST a new customer without LoyaltyTier. (c) POST a new customer with `"LoyaltyTier":""`. (d) PATCH a Silver customer with `"LoyaltyTier":"Gold"`. (e) PATCH a Gold customer with `"LoyaltyTier":""`. (f) PATCH a Gold customer with only `PreferredDeliveryWindow`. (g) PATCH with `"LoyaltyTier":"Diamond"`. (h) Send an existing CRM payload without the new properties. | (a) The text values are returned. (b) and (c) The customers are created as Standard. (d) Gold. (e) Standard. (f) Still Gold, with the new window. (g) An error with our message, and the customer is unchanged. (h) Works as before and leaves the fields unchanged. | ✅ Passed Parts (a) to (h) all passed. Screenshots of (a), (b), (d), (e) and (g) are shown here; (c), (f) and (h) were captured as well. | [c_T6a_2_odata_GET_request_response.png](evidence/c_T6a_2_odata_GET_request_response.png) · [d_T6b_odata_POST_without_tier_Standard.png](evidence/d_T6b_odata_POST_without_tier_Standard.png) · [d_T6d_odata_PATCH_Silver_to_Gold.png](evidence/d_T6d_odata_PATCH_Silver_to_Gold.png) · [d_T6e_1_odata_PATCH_empty_tier_Standard.png](evidence/d_T6e_1_odata_PATCH_empty_tier_Standard.png) · [d_T6g_odata_PATCH_Diamond_refused.png](evidence/d_T6g_odata_PATCH_Diamond_refused.png) |
| T7 | Security | As a user with only the Accounts receivable clerk role, open a customer. As a user with only view rights, open a customer. | The clerk can edit both fields. The viewer sees them read-only. | — Not yet recorded |  |
| T8 | Length | Import a window of 60 characters. | It is stored cut to 50 characters (A6). | ✅ Passed Data management import. Through OData a longer value is also cut instead of rejected; see the notes. | [i_T4_4_import_file_and_customers_after.png](evidence/i_T4_4_import_file_and_customers_after.png) |
| T9 | Converter | Call `fromText` with `" Gold "`, `"PLATINUM"`, `""` and `"X"`; call `toText` for each enum value. | The results are Gold, Platinum, Standard and an error. `toText` returns the four names. | — Not yet recorded |  |

### a_T1_1_form_before_US-004.png

Customer US-004 Cave Wholesales before the test: the new Commercial preferences group on the Sales order defaults FastTab, directly after the Sales order group, with Loyalty tier Standard and an empty Preferred delivery window.

![Customer US-004 Cave Wholesales before the test: the new Commercial preferences group on the Sales order defaults FastTab, directly after the Sales order group, with Loyalty tier Standard and an empty Preferred delivery window.](evidence/a_T1_1_form_before_US-004.png)

### a_T1_2_form_dropdown_values.png

The same customer in edit mode with the Loyalty tier drop-down open, showing the four values Standard, Silver, Gold and Platinum.

![The same customer in edit mode with the Loyalty tier drop-down open, showing the four values Standard, Silver, Gold and Platinum.](evidence/a_T1_2_form_dropdown_values.png)

### a_T1_3_form_saved_reopened_US-004_Gold.png

US-004 reopened after saving: Loyalty tier is Gold and Preferred delivery window is 08:00 AM - 12:00 PM.

![US-004 reopened after saving: Loyalty tier is Gold and Preferred delivery window is 08:00 AM - 12:00 PM.](evidence/a_T1_3_form_saved_reopened_US-004_Gold.png)

### b_T2_2_new_customer_LT-001_defaults_Standard.png

A customer just created on the form, LT-001, shows Loyalty tier Standard and an empty Preferred delivery window.

![A customer just created on the form, LT-001, shows Loyalty tier Standard and an empty Preferred delivery window.](evidence/b_T2_2_new_customer_LT-001_defaults_Standard.png)

### h_T3_CSV_1_export_execution_summary.png

Data management export of Customers V3 to CSV: the execution summary shows Succeeded, no errors and 38 records exported.

![Data management export of Customers V3 to CSV: the execution summary shows Succeeded, no errors and 38 records exported.](evidence/h_T3_CSV_1_export_execution_summary.png)

### h_T3_EXCEL_2_exported_file_columns.png

The exported Excel file, four of its 244 columns for all 38 customers: LOYALTYTIER and PREFERREDDELIVERYWINDOW are present, US-004 shows Gold and 08:00 AM - 12:00 PM, and customers that were never set show Standard.

![The exported Excel file, four of its 244 columns for all 38 customers: LOYALTYTIER and PREFERREDDELIVERYWINDOW are present, US-004 shows Gold and 08:00 AM - 12:00 PM, and customers that were never set show Standard.](evidence/h_T3_EXCEL_2_exported_file_columns.png)

### i_T4_3_import_execution_summary.png

Data management import of a six-row Customers V3 file: partially succeeded, with 3 customers created, 2 updated and 1 row in error.

![Data management import of a six-row Customers V3 file: partially succeeded, with 3 customers created, 2 updated and 1 row in error.](evidence/i_T4_3_import_execution_summary.png)

### i_T4_4_import_file_and_customers_after.png

The imported file and the customers afterwards: silver became Silver, a blank tier became Standard for a new customer and for a Gold one, Platinum and its window were written, the Diamond row left its customer unchanged, and the long window was cut at 50 characters.

![The imported file and the customers afterwards: silver became Silver, a blank tier became Standard for a new customer and for a Gold one, Platinum and its window were written, the Diamond row left its customer unchanged, and the long window was cut at 50 characters.](evidence/i_T4_4_import_file_and_customers_after.png)

### i_T5_1_import_staging_rows_Diamond_error.png

Staging rows of the import: the row with Loyalty tier Diamond is in error with the message that the value is not valid and lists Standard, Silver, Gold and Platinum; the other five rows completed.

![Staging rows of the import: the row with Loyalty tier Diamond is in error with the message that the value is not valid and lists Standard, Silver, Gold and Platinum; the other five rows completed.](evidence/i_T5_1_import_staging_rows_Diamond_error.png)

### c_T6a_2_odata_GET_request_response.png

OData GET on CustomersV3 returns both new fields as text for four customers; US-004 carries Gold and 08:00 AM - 12:00 PM, the others Standard.

![OData GET on CustomersV3 returns both new fields as text for four customers; US-004 carries Gold and 08:00 AM - 12:00 PM, the others Standard.](evidence/c_T6a_2_odata_GET_request_response.png)

### d_T6b_odata_POST_without_tier_Standard.png

OData POST that creates a customer without the LoyaltyTier property returns 201, and the customer is Standard.

![OData POST that creates a customer without the LoyaltyTier property returns 201, and the customer is Standard.](evidence/d_T6b_odata_POST_without_tier_Standard.png)

### d_T6d_odata_PATCH_Silver_to_Gold.png

OData PATCH sets a customer to Silver and then to Gold; each GET that follows returns the new tier.

![OData PATCH sets a customer to Silver and then to Gold; each GET that follows returns the new tier.](evidence/d_T6d_odata_PATCH_Silver_to_Gold.png)

### d_T6e_1_odata_PATCH_empty_tier_Standard.png

OData PATCH with an empty LoyaltyTier on a Gold customer returns 204, and the GET that follows returns Standard.

![OData PATCH with an empty LoyaltyTier on a Gold customer returns 204, and the GET that follows returns Standard.](evidence/d_T6e_1_odata_PATCH_empty_tier_Standard.png)

### d_T6g_odata_PATCH_Diamond_refused.png

OData PATCH with LoyaltyTier Diamond returns 400 with the message that the value is not valid and lists Standard, Silver, Gold and Platinum; the GET that follows shows the customer unchanged.

![OData PATCH with LoyaltyTier Diamond returns 400 with the message that the value is not valid and lists Standard, Silver, Gold and Platinum; the GET that follows shows the customer unchanged.](evidence/d_T6g_odata_PATCH_Diamond_refused.png)

## Notes

Tested in USMF demo data on the dev box against section 6 of the approved design.

**Passed:** the customer form (T1, T2), Data management export to CSV, Excel and XML (T3), Data management import with defaulting, the unknown value and the long window (T4, T5, T8), and every OData case (T6 a to h).

**History.** In a first run the Data management scenarios could not pass: the dev box's database still held the Loyalty Tier staging column from an earlier version of this same demo, with a numeric type, and the import service was stopped. After a full database sync and a start of that service the scenarios were retested and passed. That sync also reset the two new fields on every customer, so the values used in the export test were set again through OData before exporting.

**Not run:**
- T7 (security): only a system administrator session was available.
- T9 (converter): not called directly. The same inputs were sent through OData, which calls the converter: " Gold " gave Gold, "PLATINUM" gave Platinum, an empty text gave Standard, and "X" was refused with the error message.

**Deviations from the design found in OData:**
1. A PATCH that sends `"LoyaltyTier": null` does not set Standard: it returns 204 and the customer keeps its tier. An empty text `""` works as designed. POST with `null` was not tried.
2. A Preferred delivery window longer than 50 characters is accepted and stored cut to 50 characters; the design says OData rejects it.

**Observations, not counted as failures:**
- An OData POST with an empty LoyaltyTier returns 201 and echoes the empty text in its response; a GET immediately after returns Standard.
- The exported CSV file has no text qualifier (standard behaviour), so rows whose address contains a comma or a line break cannot be split into columns reliably; the Excel and XML files were checked in full.
- In the long-window import the fiftieth character is a space, so the stored text shows 49 characters.
