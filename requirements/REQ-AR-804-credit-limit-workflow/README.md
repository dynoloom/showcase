# REQ-AR-804 — Customer Credit Limit Increase Request & Multi-Tier Approval Workflow

**Status:** Delivered 2026-11-22 · Compiled with 0 errors · 1 of 17 test scenarios evidenced  
Walkthrough video: recorded, publishing on [@Dynoloom](https://www.youtube.com/@Dynoloom).

| Step | What you will find |
|---|---|
| [1 · Requirement](1-requirement/FRD.md) | The client's Functional Requirement Document ([original .docx](1-requirement/REQ-AR-804-FRD.docx)) |
| [2 · Design](2-design/TDD.md) | The Technical Design Document DynoLoom generated and the client approved · [readiness questions](2-design/readiness-questions.md) |
| [3 · Code](3-code/) | The X++ metadata exactly as delivered ([full diff](3-code/changes.patch)) |
| [4 · Testing](4-testing/test-cases.md) | Test scenarios from the TDD, results and screenshots |

## What was built

The summary below is section 1 of the approved design, unchanged.

**What you asked for and what this design delivers**

| Feature named in the requirement | This design |
|---|---|
| Lock direct editing of Credit limit on the customer | **Built.** Read-only for everyone except holders of a new "Finance Director" credit-limit privilege; read-only for everyone when a customer is created. |
| Credit limit increase request form (Request ID, customer, current limit, requested limit, net change, justification, requested by, approval status) | **Built.** New form under Accounts receivable > Customers > Credit limit increase requests. |
| Submission through the workflow action bar, with validation, and record locked while in review | **Built.** |
| Conditional routing: AR Team Lead only, or AR Team Lead then Finance Director, by the $25,000 / $100,000 thresholds | **Built.** The thresholds are a setting per legal entity (your answer); the routing itself is configured in the workflow editor. |
| Automatic update of the customer's credit limit on final approval, with approval date and final approver | **Built.** |
| Rejection, Request change | **Built** (standard workflow outcomes, handled by the new workflow). |
| Escalation of unactioned work items after 48 hours (24 h for the AR Team Lead in the diagram) | **Covered by standard workflow functionality**, as you asked: time limits and escalation paths are set per step in the workflow editor (section 5). Nothing is coded for it. |
| Credit limit decreases (from your answers) | **Built** through the same request form and workflow; the workflow approves them automatically. |

A sales coordinator raises a request for a customer. The current credit limit is filled in, they type the new limit and a justification, and submit it to workflow. On submit the system works out which approval path applies, using the two thresholds set per legal entity: a decrease, a single-step increase (AR Team Lead), or a two-step increase (AR Team Lead, then Finance Director). The workflow, set up in the standard workflow editor, routes the request on that basis. When the workflow approves it, the customer's credit limit is updated straight away, and the approval date and final approver are stamped on the request. A rejection leaves the customer unchanged.

Note: two standard features touch the same field. **Credit management > Credit limit adjustments** has its own approval workflow, and **customer change proposals** (Customer approvals) can also apply to Credit limit. This design does not use or change either of them (see the assumptions in section 2).

---

## Objects delivered

32 new objects and 9 extensions or event handlers on standard objects. No standard object is overlayered: every change to standard behaviour is a table/form/enum extension, a Chain of Command class or an event handler.

| Type | Object | Change |
|---|---|---|
| Class | [`DYLO_CustCreditLimitReqApprovalEventHandler`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxClass/DYLO_CustCreditLimitReqApprovalEventHandler.xml) | Added |
| Class | [`DYLO_CustCreditLimitReqApprovalResubmitActionMgr`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxClass/DYLO_CustCreditLimitReqApprovalResubmitActionMgr.xml) | Added |
| Class | [`DYLO_CustCreditLimitReqWFDocument`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxClass/DYLO_CustCreditLimitReqWFDocument.xml) | Added |
| Class | [`DYLO_CustCreditLimitReqWFTypeEventHandler`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxClass/DYLO_CustCreditLimitReqWFTypeEventHandler.xml) | Added |
| Class | [`DYLO_CustCreditLimitReqWFTypeSubmitManager`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxClass/DYLO_CustCreditLimitReqWFTypeSubmitManager.xml) | Added |
| Class | [`DYLO_CustCreditLimitRequestWFProcessor`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxClass/DYLO_CustCreditLimitRequestWFProcessor.xml) | Added |
| Class | [`DYLO_CustCreditLimitUpdateContext`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxClass/DYLO_CustCreditLimitUpdateContext.xml) | Added |
| Class | [`DYLO_CustParameters_Extension`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxClass/DYLO_CustParameters_Extension.xml) | Added |
| Class | [`DYLO_CustTableFormCustTableDS_Extension`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxClass/DYLO_CustTableFormCustTableDS_Extension.xml) | Added |
| Class | [`DYLO_CustTable_Extension`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxClass/DYLO_CustTable_Extension.xml) | Added |
| Class | [`DYLO_NumberSeqModuleCustomer_Extension`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxClass/DYLO_NumberSeqModuleCustomer_Extension.xml) | Added |
| Extended data type | [`DYLO_CustCreditLimitJustification`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxEdt/DYLO_CustCreditLimitJustification.xml) | Added |
| Extended data type | [`DYLO_CustCreditLimitRequestId`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxEdt/DYLO_CustCreditLimitRequestId.xml) | Added |
| Base enum | [`DYLO_CustCreditLimitApprovalPath`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxEnum/DYLO_CustCreditLimitApprovalPath.xml) | Added |
| Base enum | [`DYLO_CustCreditLimitRequestStatus`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxEnum/DYLO_CustCreditLimitRequestStatus.xml) | Added |
| Form | [`DYLO_CustCreditLimitRequest`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxForm/DYLO_CustCreditLimitRequest.xml) | Added |
| Form extension | [`CustParameters.DYLO_Extension`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxFormExtension/CustParameters.DYLO_Extension.xml) | Added |
| Menu extension | [`AccountsReceivable.DYLO_Extension`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxMenuExtension/AccountsReceivable.DYLO_Extension.xml) | Added |
| Action menu item | [`DYLO_CustCreditLimitReqApprovalApprove`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxMenuItemAction/DYLO_CustCreditLimitReqApprovalApprove.xml) | Added |
| Action menu item | [`DYLO_CustCreditLimitReqApprovalDelegate`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxMenuItemAction/DYLO_CustCreditLimitReqApprovalDelegate.xml) | Added |
| Action menu item | [`DYLO_CustCreditLimitReqApprovalReject`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxMenuItemAction/DYLO_CustCreditLimitReqApprovalReject.xml) | Added |
| Action menu item | [`DYLO_CustCreditLimitReqApprovalRequestChange`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxMenuItemAction/DYLO_CustCreditLimitReqApprovalRequestChange.xml) | Added |
| Action menu item | [`DYLO_CustCreditLimitReqApprovalResubmit`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxMenuItemAction/DYLO_CustCreditLimitReqApprovalResubmit.xml) | Added |
| Action menu item | [`DYLO_CustCreditLimitReqWFTypeCancelMenuItem`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxMenuItemAction/DYLO_CustCreditLimitReqWFTypeCancelMenuItem.xml) | Added |
| Action menu item | [`DYLO_CustCreditLimitReqWFTypeSubmitMenuItem`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxMenuItemAction/DYLO_CustCreditLimitReqWFTypeSubmitMenuItem.xml) | Added |
| Display menu item | [`DYLO_CustCreditLimitEdit`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxMenuItemDisplay/DYLO_CustCreditLimitEdit.xml) | Added |
| Display menu item | [`DYLO_CustCreditLimitRequest`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxMenuItemDisplay/DYLO_CustCreditLimitRequest.xml) | Added |
| Query | [`DYLO_CustCreditLimitReqWFQuery`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxQuery/DYLO_CustCreditLimitReqWFQuery.xml) | Added |
| Security duty | [`DYLO_CustCreditLimitMaintain`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxSecurityDuty/DYLO_CustCreditLimitMaintain.xml) | Added |
| Security duty | [`DYLO_CustCreditLimitRequestApprove`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxSecurityDuty/DYLO_CustCreditLimitRequestApprove.xml) | Added |
| Security duty | [`DYLO_CustCreditLimitRequestMaintain`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxSecurityDuty/DYLO_CustCreditLimitRequestMaintain.xml) | Added |
| Security privilege | [`DYLO_CustCreditLimitEdit`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxSecurityPrivilege/DYLO_CustCreditLimitEdit.xml) | Added |
| Security privilege | [`DYLO_CustCreditLimitRequestApprove`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxSecurityPrivilege/DYLO_CustCreditLimitRequestApprove.xml) | Added |
| Security privilege | [`DYLO_CustCreditLimitRequestMaintain`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxSecurityPrivilege/DYLO_CustCreditLimitRequestMaintain.xml) | Added |
| Security role | [`DYLO_ARTeamLead`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxSecurityRole/DYLO_ARTeamLead.xml) | Added |
| Security role | [`DYLO_CreditLimitRequester`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxSecurityRole/DYLO_CreditLimitRequester.xml) | Added |
| Security role | [`DYLO_FinanceDirector`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxSecurityRole/DYLO_FinanceDirector.xml) | Added |
| Table | [`DYLO_CustCreditLimitRequest`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxTable/DYLO_CustCreditLimitRequest.xml) | Added |
| Table extension | [`CustParameters.DYLO_Extension`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxTableExtension/CustParameters.DYLO_Extension.xml) | Added |
| Workflow approval | [`DYLO_CustCreditLimitReqApproval`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxWorkflowApproval/DYLO_CustCreditLimitReqApproval.xml) | Added |
| Workflow type | [`DYLO_CustCreditLimitReqWFType`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxWorkflowTemplate/DYLO_CustCreditLimitReqWFType.xml) | Added |

Labels are in the model's label file, [`DYLOExtensions.en-US.label.txt`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxLabelFile/LabelResources/en-US/DYLOExtensions.en-US.label.txt).
The label file and model descriptor are shared by every requirement in this repository, so the delivered copy also contains entries from requirements delivered before this one.
