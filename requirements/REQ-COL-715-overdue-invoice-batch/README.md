# REQ-COL-715 — Automated Overdue Invoice Batch Job & Notification Log Workspace

**Status:** Delivered 2026-11-22 · Compiled with 0 errors · 9 of 17 test scenarios evidenced, 1 failed  
Walkthrough video: recorded, publishing on [@Dynoloom](https://www.youtube.com/@Dynoloom).

| Step | What you will find |
|---|---|
| [1 · Requirement](1-requirement/FRD.md) | The client's Functional Requirement Document ([original .docx](1-requirement/REQ-COL-715-FRD.docx)) |
| [2 · Design](2-design/TDD.md) | The Technical Design Document DynoLoom generated and the client approved · [readiness questions](2-design/readiness-questions.md) |
| [3 · Code](3-code/) | The X++ metadata exactly as delivered ([full diff](3-code/changes.patch)) |
| [4 · Testing](4-testing/test-cases.md) | Test scenarios from the TDD, results and screenshots |

## What was built

The summary below is section 1 of the approved design, unchanged.

**What you asked for and what this design delivers**

| Feature named in the requirement | This design |
|---|---|
| Daily SysOperation batch job (scheduled e.g. 06:00) | **Built** (the schedule itself is the standard batch recurrence, see section 5) |
| Batch parameters: Grace period days, Customer group filter, Recipient email address | **Built** |
| Selection of open / unsettled posted customer invoices past due, incl. partly paid and free text invoices | **Built** |
| At most one notification per invoice per overdue tier; tiers maintained by finance | **Built** – new setup form "Overdue invoice notification setup" |
| Email with invoice ID, customer name, original amount, outstanding amount, original due date, days overdue | **Built** |
| Notification log form – grid (left) | **Built** |
| Email preview and error inspector (right pane) | **Built** |
| "Resend Notification" button | **Built** |
| "Purge Old Logs (>180d)" button | **Built** |
| "Refresh" button (mockup) | **Covered by standard** – the standard Refresh of every D365 form |
| "Run Batch Now" button (mockup) | **Built** – opens the batch job dialog from the log form |
| "View Email Payload" button (mockup) | **Left out, as you agreed** – the right-hand pane already shows the full recipient, subject and body |
| Filter bar with invoice/customer search and status counts (mockup) | **Built** with standard quick filter, a Status filter and a counts line |
| Exact look of the mockup (dark top bar, blue header band on the right pane, monospace preview font, coloured error box) | **Standard D365 look, as you agreed** – green/red status cells and red days overdue are built; the rest follows the standard D365 form style |

A daily batch job looks at every open customer invoice in the company and works out how many days it is
past due. When the invoice has just reached one of the overdue tiers that finance set up (for example 15,
30, 60 days), the job emails the finance or collections team. Every email attempt, sent or failed, is
written to a new log. The log is shown on a new screen **Credit and collections > Inquiries and reports >
Overdue invoice notification log**. On that screen users see the email that went out and any delivery
error. They can also resend a notification, purge old log records and start the batch job.

---

## Objects delivered

25 new objects and 1 extensions or event handlers on standard objects. No standard object is overlayered: every change to standard behaviour is a table/form/enum extension, a Chain of Command class or an event handler.

| Type | Object | Change |
|---|---|---|
| Class | [`DYLO_OverdueInvoiceNotificationContract`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxClass/DYLO_OverdueInvoiceNotificationContract.xml) | Added |
| Class | [`DYLO_OverdueInvoiceNotificationController`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxClass/DYLO_OverdueInvoiceNotificationController.xml) | Added |
| Class | [`DYLO_OverdueInvoiceNotificationEmail`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxClass/DYLO_OverdueInvoiceNotificationEmail.xml) | Added |
| Class | [`DYLO_OverdueInvoiceNotificationPurge`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxClass/DYLO_OverdueInvoiceNotificationPurge.xml) | Added |
| Class | [`DYLO_OverdueInvoiceNotificationResend`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxClass/DYLO_OverdueInvoiceNotificationResend.xml) | Added |
| Class | [`DYLO_OverdueInvoiceNotificationService`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxClass/DYLO_OverdueInvoiceNotificationService.xml) | Added |
| Base enum | [`DYLO_OverdueNotificationStatus`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxEnum/DYLO_OverdueNotificationStatus.xml) | Added |
| Base enum | [`DYLO_OverdueNotificationStatusFilter`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxEnum/DYLO_OverdueNotificationStatusFilter.xml) | Added |
| Form | [`DYLO_OverdueInvoiceNotificationLog`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxForm/DYLO_OverdueInvoiceNotificationLog.xml) | Added |
| Form | [`DYLO_OverdueNotificationSetup`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxForm/DYLO_OverdueNotificationSetup.xml) | Added |
| Menu extension | [`CreditAndCollections.DYLOExtensions`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxMenuExtension/CreditAndCollections.DYLOExtensions.xml) | Added |
| Action menu item | [`DYLO_OverdueInvoiceNotificationPurge`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxMenuItemAction/DYLO_OverdueInvoiceNotificationPurge.xml) | Added |
| Action menu item | [`DYLO_OverdueInvoiceNotificationResend`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxMenuItemAction/DYLO_OverdueInvoiceNotificationResend.xml) | Added |
| Action menu item | [`DYLO_OverdueInvoiceNotificationRun`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxMenuItemAction/DYLO_OverdueInvoiceNotificationRun.xml) | Added |
| Display menu item | [`DYLO_OverdueInvoiceNotificationLog`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxMenuItemDisplay/DYLO_OverdueInvoiceNotificationLog.xml) | Added |
| Display menu item | [`DYLO_OverdueNotificationSetup`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxMenuItemDisplay/DYLO_OverdueNotificationSetup.xml) | Added |
| Security duty | [`DYLO_OverdueInvoiceNotificationInquire`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxSecurityDuty/DYLO_OverdueInvoiceNotificationInquire.xml) | Added |
| Security duty | [`DYLO_OverdueInvoiceNotificationMaintain`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxSecurityDuty/DYLO_OverdueInvoiceNotificationMaintain.xml) | Added |
| Security privilege | [`DYLO_OverdueInvoiceNotificationLogMaintain`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxSecurityPrivilege/DYLO_OverdueInvoiceNotificationLogMaintain.xml) | Added |
| Security privilege | [`DYLO_OverdueInvoiceNotificationLogView`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxSecurityPrivilege/DYLO_OverdueInvoiceNotificationLogView.xml) | Added |
| Security privilege | [`DYLO_OverdueInvoiceNotificationRun`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxSecurityPrivilege/DYLO_OverdueInvoiceNotificationRun.xml) | Added |
| Security privilege | [`DYLO_OverdueNotificationSetupMaintain`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxSecurityPrivilege/DYLO_OverdueNotificationSetupMaintain.xml) | Added |
| Security role | [`DYLO_OverdueInvoiceNotificationManager`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxSecurityRole/DYLO_OverdueInvoiceNotificationManager.xml) | Added |
| Table | [`DYLO_OverdueInvoiceNotificationLog`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxTable/DYLO_OverdueInvoiceNotificationLog.xml) | Added |
| Table | [`DYLO_OverdueNotificationParameters`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxTable/DYLO_OverdueNotificationParameters.xml) | Added |
| Table | [`DYLO_OverdueNotificationTier`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxTable/DYLO_OverdueNotificationTier.xml) | Added |

Labels are in the model's label file, [`DYLOExtensions.en-US.label.txt`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxLabelFile/LabelResources/en-US/DYLOExtensions.en-US.label.txt).
The label file and model descriptor are shared by every requirement in this repository, so the delivered copy also contains entries from requirements delivered before this one.
