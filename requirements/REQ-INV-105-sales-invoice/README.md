# REQ-INV-105 — Display Customer PO Reference and Delivery Instructions on Posted Sales Invoices and Printouts

**Status:** Delivered 2026-11-22 · Compiled with 0 errors · 9 of 10 test scenarios evidenced, 1 failed  
Walkthrough video: recorded, publishing on [@Dynoloom](https://www.youtube.com/@Dynoloom).

| Step | What you will find |
|---|---|
| [1 · Requirement](1-requirement/FRD.md) | The client's Functional Requirement Document ([original .docx](1-requirement/REQ-INV-105-FRD.docx)) |
| [2 · Design](2-design/TDD.md) | The Technical Design Document DynoLoom generated and the client approved · [readiness questions](2-design/readiness-questions.md) |
| [3 · Code](3-code/) | The X++ metadata exactly as delivered ([full diff](3-code/changes.patch)) |
| [4 · Testing](4-testing/test-cases.md) | Test scenarios from the TDD, results and screenshots |

## What was built

The summary below is section 1 of the approved design, unchanged.

**What you asked for and what this design delivers**

| Feature in the requirement (REQ-INV-105-FRD.docx) | This design |
|---|---|
| New Delivery Instructions field (up to 150 characters) on the sales order header | Builds it |
| Delivery Instructions shown on the sales order, Delivery tab, editable | Builds it |
| Delivery Instructions shown read-only on the posted sales invoice, General tab | Builds it |
| Delivery Instructions copied automatically from the order to the posted invoice | Builds it |
| Customer PO Reference (the order's **Customer reference**) on the printed invoice | Builds it (the field is already copied to the posted invoice by standard; only the printout is new) |
| Delivery Instructions on the printed invoice | Builds it |
| Printed sales invoice redesigned to look like Figure 1 (blue banner, boxed bill-to and invoice details, blue line header, totals block) | Builds it, as a new invoice report format |
| Standard invoice content not shown in Figure 1 (logo, bank details, delivery address, due date, tax breakdown, notes, footer texts, country blocks) | Left out, as you answered ("skip them") |

The design adds a Delivery Instructions field to sales orders and posted customer invoices and copies it when the invoice is posted. It also adds a new sales invoice report, `DYLO_SalesInvoice`, laid out like Figure 1. The report is registered in Print management and made the default format for the Customer invoice document. Everything is built with extensions and Chain of Command in the new model **DYLOExtensions**.

---

## Objects delivered

2 new objects and 9 extensions or event handlers on standard objects. No standard object is overlayered: every change to standard behaviour is a table/form/enum extension, a Chain of Command class or an event handler.

| Type | Object | Change |
|---|---|---|
| Class | [`CustInvoiceJour_DYLO_Extension`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxClass/CustInvoiceJour_DYLO_Extension.xml) | Added |
| Class | [`DYLO_PrintMgmtDocType_EventHandler`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxClass/DYLO_PrintMgmtDocType_EventHandler.xml) | Added |
| Class | [`DYLO_PrintMgmtReportFormatPopulator_Extension`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxClass/DYLO_PrintMgmtReportFormatPopulator_Extension.xml) | Added |
| Class | [`SalesInvoiceDP_DYLO_Extension`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxClass/SalesInvoiceDP_DYLO_Extension.xml) | Added |
| Extended data type | [`DYLO_DeliveryInstructions`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxEdt/DYLO_DeliveryInstructions.xml) | Added |
| Form extension | [`CustInvoiceJournal.DYLOExtensions`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxFormExtension/CustInvoiceJournal.DYLOExtensions.xml) | Added |
| Form extension | [`SalesTable.DYLOExtensions`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxFormExtension/SalesTable.DYLOExtensions.xml) | Added |
| SSRS report | [`DYLO_SalesInvoice`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxReport/DYLO_SalesInvoice.xml) | Added |
| Table extension | [`CustInvoiceJour.DYLOExtensions`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxTableExtension/CustInvoiceJour.DYLOExtensions.xml) | Added |
| Table extension | [`SalesInvoiceHeaderFooterTmp.DYLOExtensions`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxTableExtension/SalesInvoiceHeaderFooterTmp.DYLOExtensions.xml) | Added |
| Table extension | [`SalesTable.DYLOExtensions`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxTableExtension/SalesTable.DYLOExtensions.xml) | Added |

Labels are in the model's label file, [`DYLOExtensions.en-US.label.txt`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxLabelFile/LabelResources/en-US/DYLOExtensions.en-US.label.txt).
The label file and model descriptor are shared by every requirement in this repository, so the delivered copy also contains entries from requirements delivered before this one.
