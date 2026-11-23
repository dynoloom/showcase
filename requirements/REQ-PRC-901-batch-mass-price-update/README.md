# REQ-PRC-901 — Periodic Mass Trade Agreement Price Adjustment Batch Job (SysOperation)

**Status:** Delivered 2026-11-22 · Compiled with 0 errors · 1 of 12 test scenarios evidenced, 5 failed  
Walkthrough video: not recorded yet.

| Step | What you will find |
|---|---|
| [1 · Requirement](1-requirement/FRD.md) | The client's Functional Requirement Document ([original .docx](1-requirement/REQ-PRC-901-FRD.docx)) |
| [2 · Design](2-design/TDD.md) | The Technical Design Document DynoLoom generated and the client approved · [readiness questions](2-design/readiness-questions.md) |
| [3 · Code](3-code/) | The X++ metadata exactly as delivered ([full diff](3-code/changes.patch)) |
| [4 · Testing](4-testing/test-cases.md) | Test scenarios from the TDD, results and screenshots |

## What was built

The summary below is section 1 of the approved design, unchanged.

**What you asked for and what this design delivers**

| Feature named in the requirement | This design |
|---|---|
| Batch dialog with these fields: Journal name, Item group (several can be chosen), Customer price group, Adjustment %, Effective from date, Rounding rule (Nearest 0.05 / 0.10 / Exact), Auto-post | Built |
| Selection of active sales price agreements for the chosen item groups and customer price group | Built |
| Old agreements end the day before the effective date, and new agreement lines are created at the adjusted price in a Price/discount agreement journal | Built. Both are done through the standard journal, so the old prices end when the journal is posted (see 3.3) |
| Multi-threaded batch: items split into task bundles of a size you set (default 500) | Built |
| Optional automatic posting of the journal | Built (the standard journal posting runs) |
| Completion Infolog: lines processed, journal number, items skipped because their records were locked | Built |
| Validation: block 0 % and an invalid journal name (AC-01) | Built |
| Menu item: Sales and marketing > Periodic tasks > Mass trade agreement price update | Built |

The new periodic job is a SysOperation batch job. Each time it runs, it creates one standard Price/discount agreement journal. For every active sales price agreement that matches the filters, the journal gets two lines:
- one line that closes the existing agreement on the effective date minus 1 day;
- one line that creates the new agreement from the effective date, at the adjusted and rounded price.

You can review the journal in the standard Price/discount agreement journals form before posting, or the job posts it for you (Auto-post). As you decided, the existing agreements stay unchanged until the journal is posted. At posting the old agreement is closed and the new price starts in the same step, so there is never a day without a price. Posting is done by the standard posting class `PriceDiscAdmCheckPost`. Standard posting updates the existing agreement (through `PriceDiscTableRef`) and creates the new one, so the job never writes to the trade agreement table `PriceDiscTable` itself.

All objects are new or extensions of standard objects, in the new model **DYLOExtensions**, which is built by extension only.

---

## Objects delivered

15 new objects and 2 extensions or event handlers on standard objects. No standard object is overlayered: every change to standard behaviour is a table/form/enum extension, a Chain of Command class or an event handler.

| Type | Object | Change |
|---|---|---|
| Class | [`DYLO_TradeAgreementPriceAdjCalc`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxClass/DYLO_TradeAgreementPriceAdjCalc.xml) | Added |
| Class | [`DYLO_TradeAgreementPriceAdjContract`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxClass/DYLO_TradeAgreementPriceAdjContract.xml) | Added |
| Class | [`DYLO_TradeAgreementPriceAdjController`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxClass/DYLO_TradeAgreementPriceAdjController.xml) | Added |
| Class | [`DYLO_TradeAgreementPriceAdjService`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxClass/DYLO_TradeAgreementPriceAdjService.xml) | Added |
| Class | [`DYLO_TradeAgreementPriceAdjTaskContract`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxClass/DYLO_TradeAgreementPriceAdjTaskContract.xml) | Added |
| Class | [`DYLO_TradeAgreementPriceAdjTaskController`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxClass/DYLO_TradeAgreementPriceAdjTaskController.xml) | Added |
| Class | [`DYLO_TradeAgreementPriceAdjUIBuilder`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxClass/DYLO_TradeAgreementPriceAdjUIBuilder.xml) | Added |
| Extended data type | [`DYLO_PriceAdjPercent`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxEdt/DYLO_PriceAdjPercent.xml) | Added |
| Extended data type | [`DYLO_PriceAdjRunId`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxEdt/DYLO_PriceAdjRunId.xml) | Added |
| Base enum | [`DYLO_PriceAdjLogStatus`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxEnum/DYLO_PriceAdjLogStatus.xml) | Added |
| Base enum | [`DYLO_PriceAdjRoundingRule`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxEnum/DYLO_PriceAdjRoundingRule.xml) | Added |
| Menu extension | [`SalesAndMarketing.DYLOExtensions`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxMenuExtension/SalesAndMarketing.DYLOExtensions.xml) | Added |
| Action menu item | [`DYLO_TradeAgreementPriceAdj`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxMenuItemAction/DYLO_TradeAgreementPriceAdj.xml) | Added |
| Security duty | [`DYLO_TradeAgreementPriceAdjMaintain`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxSecurityDuty/DYLO_TradeAgreementPriceAdjMaintain.xml) | Added |
| Security privilege | [`DYLO_TradeAgreementPriceAdjGenerate`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxSecurityPrivilege/DYLO_TradeAgreementPriceAdjGenerate.xml) | Added |
| Security role extension | [`TradeSalesManager.DYLOExtensions`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxSecurityRoleExtension/TradeSalesManager.DYLOExtensions.xml) | Added |
| Table | [`DYLO_PriceAdjLog`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxTable/DYLO_PriceAdjLog.xml) | Added |
| Model descriptor | [`DYLOExtensions`](3-code/Metadata/DYLOExtensions/Descriptor/DYLOExtensions.xml) | Added |

Labels are in the model's label file, [`DYLOExtensions.en-US.label.txt`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxLabelFile/LabelResources/en-US/DYLOExtensions.en-US.label.txt).
The label file and model descriptor are shared by every requirement in this repository, so the delivered copy also contains entries from requirements delivered before this one.
