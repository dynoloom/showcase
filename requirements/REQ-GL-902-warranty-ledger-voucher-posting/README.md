# REQ-GL-902 — Automated Product Warranty Provision Subledger & General Ledger Voucher Posting

**Status:** Delivered 2026-11-22 · Compiled with 0 errors · 12 of 13 test scenarios evidenced  
Walkthrough video: recorded, publishing on [@Dynoloom](https://www.youtube.com/@Dynoloom).

| Step | What you will find |
|---|---|
| [1 · Requirement](1-requirement/FRD.md) | The client's Functional Requirement Document ([original .docx](1-requirement/REQ-GL-902-FRD.docx)) |
| [2 · Design](2-design/TDD.md) | The Technical Design Document DynoLoom generated and the client approved · [readiness questions](2-design/readiness-questions.md) |
| [3 · Code](3-code/) | The X++ metadata exactly as delivered ([full diff](3-code/changes.patch)) |
| [4 · Testing](4-testing/test-cases.md) | Test scenarios from the TDD, results and screenshots |

## What was built

The summary below is section 1 of the approved design, unchanged.

**What you asked for and what this design delivers**

| Feature named in the requirement | This design |
|---|---|
| "Warranty Eligible" checkbox on Released products, with a provision % (default 3.50%) | Builds it |
| Warranty posting setup: item group → warranty expense account (debit) and warranty provision liability account (credit) | Builds it (new setup page under General ledger > Posting setup) |
| Automatic, balanced warranty voucher lines during sales invoice posting for each eligible line | Builds it |
| Financial dimensions taken from the invoice line; accounting and reporting currency amounts at the invoice exchange rate | Builds it |
| Automatic exact reversal when a sales credit note is posted for a warranty item | Builds it |
| Posting stops with a setup error when the warranty setup is missing (AC-03) | Builds it |
| Voucher linked to the invoice (AC-01) | Builds it. The warranty lines post under the invoice's own voucher number, so the standard Voucher transactions page of the posted invoice shows them |
| Separate warranty voucher number ("WAR-000492" in Figure 1) | Replaced by the invoice's own voucher number, as you confirmed |

When a sales invoice is posted (this includes invoices for project sales orders), the system checks each invoice line. If the item is marked **Warranty eligible**, it calculates *line net amount (excluding sales tax, after line discount) × provision %*. It then adds two lines to the invoice's own voucher, with the invoice's voucher number and date:
- a debit to the warranty expense account;
- a credit to the warranty provision liability account.

Both accounts come from the new **Warranty posting setup** for the item's item group, combined with the invoice line's financial dimensions. The two lines use the same transaction currency and the same exchange rates (accounting and reporting) as the invoice line's revenue.

On a credit note the line amount is negative, so the same logic posts the exact opposite entry: debit liability, credit expense. When the credit note is linked to the original invoice line, the original line's provision % is used. If an eligible item's item group has no setup, the whole invoice posting is cancelled with a clear error.

---

## Objects delivered

11 new objects and 6 extensions or event handlers on standard objects. No standard object is overlayered: every change to standard behaviour is a table/form/enum extension, a Chain of Command class or an event handler.

| Type | Object | Change |
|---|---|---|
| Class | [`DYLO_WarrantyProvisionPostingService`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxClass/DYLO_WarrantyProvisionPostingService.xml) | Added |
| Class | [`DYLO_WarrantyProvisionPostingTest`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxClass/DYLO_WarrantyProvisionPostingTest.xml) | Added |
| Class | [`InventTable_DYLO_Extension`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxClass/InventTable_DYLO_Extension.xml) | Added |
| Class | [`SalesInvoiceJournalPostBase_DYLO_Extension`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxClass/SalesInvoiceJournalPostBase_DYLO_Extension.xml) | Added |
| Extended data type | [`DYLO_WarrantyProvisionPct`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxEdt/DYLO_WarrantyProvisionPct.xml) | Added |
| Base enum extension | [`LedgerPostingType.DYLO_Extensions`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxEnumExtension/LedgerPostingType.DYLO_Extensions.xml) | Added |
| Form | [`DYLO_WarrantyPostingSetup`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxForm/DYLO_WarrantyPostingSetup.xml) | Added |
| Form extension | [`EcoResProductDetailsExtended.DYLO_Extensions`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxFormExtension/EcoResProductDetailsExtended.DYLO_Extensions.xml) | Added |
| Menu extension | [`GeneralLedger.DYLO_Extensions`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxMenuExtension/GeneralLedger.DYLO_Extensions.xml) | Added |
| Display menu item | [`DYLO_WarrantyPostingSetup`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxMenuItemDisplay/DYLO_WarrantyPostingSetup.xml) | Added |
| Security duty | [`DYLO_WarrantyPostingSetupMaintain`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxSecurityDuty/DYLO_WarrantyPostingSetupMaintain.xml) | Added |
| Security privilege | [`DYLO_WarrantyPostingSetupMaintain`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxSecurityPrivilege/DYLO_WarrantyPostingSetupMaintain.xml) | Added |
| Security privilege | [`DYLO_WarrantyPostingSetupView`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxSecurityPrivilege/DYLO_WarrantyPostingSetupView.xml) | Added |
| Security role | [`DYLO_WarrantyPostingSetupManager`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxSecurityRole/DYLO_WarrantyPostingSetupManager.xml) | Added |
| Table | [`DYLO_WarrantyPostingSetup`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxTable/DYLO_WarrantyPostingSetup.xml) | Added |
| Table | [`DYLO_WarrantyProvisionTrans`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxTable/DYLO_WarrantyProvisionTrans.xml) | Added |
| Table extension | [`InventTable.DYLO_Extensions`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxTableExtension/InventTable.DYLO_Extensions.xml) | Added |

Labels are in the model's label file, [`DYLOExtensions.en-US.label.txt`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxLabelFile/LabelResources/en-US/DYLOExtensions.en-US.label.txt).
The label file and model descriptor are shared by every requirement in this repository, so the delivered copy also contains entries from requirements delivered before this one.
