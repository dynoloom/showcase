# REQ-INT-508 — Inbound Sales Order Creation Custom Service API with Full Payload Tracing & Reprocessing Form

**Status:** Delivered 2026-11-22 · Compiled with 0 errors · 15 of 18 test scenarios evidenced  
Walkthrough video: [watch on YouTube](https://youtu.be/c-qsxyWWLUw).

| Step | What you will find |
|---|---|
| [1 · Requirement](1-requirement/FRD.md) | The client's Functional Requirement Document ([original .docx](1-requirement/REQ-INT-508-FRD.docx)) |
| [2 · Design](2-design/TDD.md) | The Technical Design Document DynoLoom generated and the client approved · [readiness questions](2-design/readiness-questions.md) |
| [3 · Code](3-code/) | The X++ metadata exactly as delivered ([full diff](3-code/changes.patch)) |
| [4 · Testing](4-testing/test-cases.md) | Test scenarios from the TDD, results and screenshots |

## What was built

The summary below is section 1 of the approved design, unchanged.

**What you asked for and what this design delivers**

| Feature named in the FRD | This design |
|---|---|
| Real-time sales order creation API (JSON in, JSON out) | Built: a custom JSON service |
| Idempotency / duplicate prevention by External Order Reference | Built |
| Business validation (customer active and not blocked for invoicing, items exist and are released, quantity > 0) | Built |
| Structured synchronous JSON response (status, Sales ID, Correlation ID, errors) | Built |
| Mandatory trace logging of every call (request, response, timestamp, duration, user) | Built |
| Sales Order API Trace Log form: grid on the left, payload and error inspector on the right | Built |
| "Reprocess Failed Order" button (failed records only) | Built |
| Purge of successful records older than 90 days, failed records kept | Built |
| Toolbar buttons "Refresh" and "View JSON Payload" from the mockup | Built (Refresh is the standard form refresh; View JSON Payload opens a larger viewer) |
| Filter bar (text filter, status filter with counts) from the mockup | Built |
| Mockup colours of the Status column (green / red, amber for Reprocessed) | Built |
| Mockup's dark-blue header on the details pane, pink error box, grey JSON boxes | Covered by the standard D365 look, as you agreed: these areas use the normal D365 theme |

External systems (e-commerce portals, B2B platforms, CRM) call one REST endpoint and send an order as JSON. D365 checks the order and creates an open sales order with its lines. It then answers immediately with the Sales ID, or with readable errors. Every call is written to a trace log.

Support staff work with the log in a new inquiry form under **Sales and marketing > Inquiries and reports > Integration > Sales Order API Trace Log**. There they can:
- read the request, the response and the error;
- reprocess a failed call after fixing the master data;
- purge old successful records.

Everything is built in the new model **DYLOExtensions**, using only new objects and extensions. No standard object is overlayered.

---

## Objects delivered

39 new objects and 1 extensions or event handlers on standard objects. No standard object is overlayered: every change to standard behaviour is a table/form/enum extension, a Chain of Command class or an event handler.

| Type | Object | Change |
|---|---|---|
| Class | [`DYLO_JsonFormatter`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxClass/DYLO_JsonFormatter.xml) | Added |
| Class | [`DYLO_SalesOrderApiLineContract`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxClass/DYLO_SalesOrderApiLineContract.xml) | Added |
| Class | [`DYLO_SalesOrderApiOrderCreator`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxClass/DYLO_SalesOrderApiOrderCreator.xml) | Added |
| Class | [`DYLO_SalesOrderApiProcessor`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxClass/DYLO_SalesOrderApiProcessor.xml) | Added |
| Class | [`DYLO_SalesOrderApiPurgeContract`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxClass/DYLO_SalesOrderApiPurgeContract.xml) | Added |
| Class | [`DYLO_SalesOrderApiPurgeController`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxClass/DYLO_SalesOrderApiPurgeController.xml) | Added |
| Class | [`DYLO_SalesOrderApiPurgeService`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxClass/DYLO_SalesOrderApiPurgeService.xml) | Added |
| Class | [`DYLO_SalesOrderApiReprocess`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxClass/DYLO_SalesOrderApiReprocess.xml) | Added |
| Class | [`DYLO_SalesOrderApiRequestContract`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxClass/DYLO_SalesOrderApiRequestContract.xml) | Added |
| Class | [`DYLO_SalesOrderApiResponseContract`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxClass/DYLO_SalesOrderApiResponseContract.xml) | Added |
| Class | [`DYLO_SalesOrderApiService`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxClass/DYLO_SalesOrderApiService.xml) | Added |
| Class | [`DYLO_SalesOrderApiTraceLogger`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxClass/DYLO_SalesOrderApiTraceLogger.xml) | Added |
| Class | [`DYLO_SalesOrderApiValidator`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxClass/DYLO_SalesOrderApiValidator.xml) | Added |
| Extended data type | [`DYLO_ApiDurationMs`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxEdt/DYLO_ApiDurationMs.xml) | Added |
| Extended data type | [`DYLO_ApiErrorCode`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxEdt/DYLO_ApiErrorCode.xml) | Added |
| Extended data type | [`DYLO_ApiRetryCount`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxEdt/DYLO_ApiRetryCount.xml) | Added |
| Extended data type | [`DYLO_CorrelationId`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxEdt/DYLO_CorrelationId.xml) | Added |
| Extended data type | [`DYLO_ExternalOrderRef`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxEdt/DYLO_ExternalOrderRef.xml) | Added |
| Base enum | [`DYLO_SalesOrderApiStatus`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxEnum/DYLO_SalesOrderApiStatus.xml) | Added |
| Base enum | [`DYLO_SalesOrderApiStatusFilter`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxEnum/DYLO_SalesOrderApiStatusFilter.xml) | Added |
| Form | [`DYLO_SalesOrderApiPayloadView`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxForm/DYLO_SalesOrderApiPayloadView.xml) | Added |
| Form | [`DYLO_SalesOrderApiTraceLog`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxForm/DYLO_SalesOrderApiTraceLog.xml) | Added |
| Menu extension | [`SalesAndMarketing.DYLOExtensions`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxMenuExtension/SalesAndMarketing.DYLOExtensions.xml) | Modified |
| Action menu item | [`DYLO_SalesOrderApiPurge`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxMenuItemAction/DYLO_SalesOrderApiPurge.xml) | Added |
| Action menu item | [`DYLO_SalesOrderApiReprocess`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxMenuItemAction/DYLO_SalesOrderApiReprocess.xml) | Added |
| Display menu item | [`DYLO_SalesOrderApiPayloadView`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxMenuItemDisplay/DYLO_SalesOrderApiPayloadView.xml) | Added |
| Display menu item | [`DYLO_SalesOrderApiTraceLog`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxMenuItemDisplay/DYLO_SalesOrderApiTraceLog.xml) | Added |
| Security duty | [`DYLO_SalesOrderApiIntegrate`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxSecurityDuty/DYLO_SalesOrderApiIntegrate.xml) | Added |
| Security duty | [`DYLO_SalesOrderApiMaintain`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxSecurityDuty/DYLO_SalesOrderApiMaintain.xml) | Added |
| Security duty | [`DYLO_SalesOrderApiMonitor`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxSecurityDuty/DYLO_SalesOrderApiMonitor.xml) | Added |
| Security privilege | [`DYLO_SalesOrderApiInvoke`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxSecurityPrivilege/DYLO_SalesOrderApiInvoke.xml) | Added |
| Security privilege | [`DYLO_SalesOrderApiPurge`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxSecurityPrivilege/DYLO_SalesOrderApiPurge.xml) | Added |
| Security privilege | [`DYLO_SalesOrderApiReprocess`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxSecurityPrivilege/DYLO_SalesOrderApiReprocess.xml) | Added |
| Security privilege | [`DYLO_SalesOrderApiTraceLogView`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxSecurityPrivilege/DYLO_SalesOrderApiTraceLogView.xml) | Added |
| Security role | [`DYLO_SalesOrderApiAdministrator`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxSecurityRole/DYLO_SalesOrderApiAdministrator.xml) | Added |
| Security role | [`DYLO_SalesOrderApiIntegrationUser`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxSecurityRole/DYLO_SalesOrderApiIntegrationUser.xml) | Added |
| Security role | [`DYLO_SalesOrderApiSupport`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxSecurityRole/DYLO_SalesOrderApiSupport.xml) | Added |
| Service | [`DYLO_SalesOrderApiService`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxService/DYLO_SalesOrderApiService.xml) | Added |
| Service group | [`DYLO_SalesOrderApiServices`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxServiceGroup/DYLO_SalesOrderApiServices.xml) | Added |
| Table | [`DYLO_SalesOrderApiTrace`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxTable/DYLO_SalesOrderApiTrace.xml) | Added |
| Model descriptor | [`DYLOExtensions`](3-code/Metadata/DYLOExtensions/Descriptor/DYLOExtensions.xml) | Modified |

Labels are in the model's label file, [`DYLOExtensions.en-US.label.txt`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxLabelFile/LabelResources/en-US/DYLOExtensions.en-US.label.txt).
The label file and model descriptor are shared by every requirement in this repository, so the delivered copy also contains entries from requirements delivered before this one.
