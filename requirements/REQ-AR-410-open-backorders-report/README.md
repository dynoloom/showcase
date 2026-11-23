# REQ-AR-410 — Open Customer Backorders & Fulfillment Risk Summary Report

**Status:** Delivered 2026-11-22 · Compiled with 0 errors · 7 of 17 test scenarios evidenced, 7 failed  
Walkthrough video: not recorded yet.

| Step | What you will find |
|---|---|
| [1 · Requirement](1-requirement/FRD.md) | The client's Functional Requirement Document ([original .docx](1-requirement/REQ-AR-410-FRD.docx)) |
| [2 · Design](2-design/TDD.md) | The Technical Design Document DynoLoom generated and the client approved · [readiness questions](2-design/readiness-questions.md) |
| [3 · Code](3-code/) | The X++ metadata exactly as delivered ([full diff](3-code/changes.patch)) |
| [4 · Testing](4-testing/test-cases.md) | Test scenarios from the TDD, results and screenshots |

## What was built

The summary below is section 1 of the approved design, unchanged.

**What you asked for and what this design delivers**

| Feature named in the requirement | Delivered by |
|---|---|
| New report "Open Customer Backorders & Fulfillment Risk Summary", opened from Accounts receivable > Inquiries and reports > Orders | Built (new SSRS report, menu item, menu entry) |
| Dialog parameters: Site / Warehouse, Customer group, Customer account (multi-select), Min days overdue (default 1), Include credit-hold orders (default Yes) | Built |
| Eligible lines: open order, remaining quantity > 0, requested ship date overdue by at least Min days overdue | Built |
| Days late, available physical stock per warehouse, backorder amount in accounting currency | Built |
| Risk badge per line: HIGH (> 7 days), MEDIUM (3–7), LOW (1–2) | Built |
| Four KPI cards: total open backorders (orders), total backorder value, high risk (orders and value), shortage units (uncovered) | Built |
| Lines grouped by customer, with credit limit and account rep in the group header, customer subtotal, grand total | Built |
| Layout as in Figure 1 of the FRD (banner, parameter strip, KPI cards, coloured table header, badges) | Built (one precision design of its own) |
| Clean export to PDF and Excel | Built (fixed-width landscape page, no overlapping items) |

The report is new and built from scratch: a data contract, a UI builder with a multi-select customer lookup, a controller, and a pre-processed data provider that fills two temporary tables. It also has one landscape precision design that follows the FRD mockup. Everything is created in the new extension model **DYLOExtensions** with the prefix **DYLO_**. No standard object is changed except by extension: an extension of the Accounts receivable menu and of one security duty.

Note: standard D365 already has the **Backorder lines** inquiry (Accounts receivable > Inquiries and reports > Orders, menu item `SalesLineBackOrder`). It lists overdue open order lines on screen. It has no stock-shortage figures, risk classes, KPI totals or customer grouping, so it does not replace this report.

---

## Objects delivered

10 new objects and 3 extensions or event handlers on standard objects. No standard object is overlayered: every change to standard behaviour is a table/form/enum extension, a Chain of Command class or an event handler.

| Type | Object | Change |
|---|---|---|
| Class | [`DYLO_OpenBackorderRiskContract`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxClass/DYLO_OpenBackorderRiskContract.xml) | Added |
| Class | [`DYLO_OpenBackorderRiskController`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxClass/DYLO_OpenBackorderRiskController.xml) | Added |
| Class | [`DYLO_OpenBackorderRiskDP`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxClass/DYLO_OpenBackorderRiskDP.xml) | Added |
| Class | [`DYLO_OpenBackorderRiskUIBuilder`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxClass/DYLO_OpenBackorderRiskUIBuilder.xml) | Added |
| Base enum | [`DYLO_BackorderRiskLevel`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxEnum/DYLO_BackorderRiskLevel.xml) | Added |
| Menu extension | [`AccountsReceivable.DYLOExtensions`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxMenuExtension/AccountsReceivable.DYLOExtensions.xml) | Added |
| Output menu item | [`DYLO_OpenBackorderRiskReport`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxMenuItemOutput/DYLO_OpenBackorderRiskReport.xml) | Added |
| SSRS report | [`DYLO_OpenBackorderRiskReport`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxReport/DYLO_OpenBackorderRiskReport.xml) | Added |
| Security duty extension | [`SalesOrderProgressInquire.DYLOExtensions`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxSecurityDutyExtension/SalesOrderProgressInquire.DYLOExtensions.xml) | Added |
| Security privilege | [`DYLO_OpenBackorderRiskReportGenerate`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxSecurityPrivilege/DYLO_OpenBackorderRiskReportGenerate.xml) | Added |
| Table | [`DYLO_OpenBackorderRiskHeaderTmp`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxTable/DYLO_OpenBackorderRiskHeaderTmp.xml) | Added |
| Table | [`DYLO_OpenBackorderRiskLineTmp`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxTable/DYLO_OpenBackorderRiskLineTmp.xml) | Added |
| Model descriptor | [`DYLOExtensions`](3-code/Metadata/DYLOExtensions/Descriptor/DYLOExtensions.xml) | Modified |
| GeneratedXppSource | [`DataEntityView_CustCustomerV3Entity+__Persister__`](3-code/Metadata/DYLOExtensions/bin/GeneratedXppSource/DataEntityView_CustCustomerV3Entity+__Persister__.xpp) | Added |

Labels are in the model's label file, [`DYLOExtensions.en-US.label.txt`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxLabelFile/LabelResources/en-US/DYLOExtensions.en-US.label.txt).
The label file and model descriptor are shared by every requirement in this repository, so the delivered copy also contains entries from requirements delivered before this one.
