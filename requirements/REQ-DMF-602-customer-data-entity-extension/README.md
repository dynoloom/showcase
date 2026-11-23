# REQ-DMF-602 — Extend Customer Master Data Entity (CustCustomerV3Entity) with Loyalty Tier & Preferred Delivery Window

**Status:** Delivered 2026-11-22 · Compiled with 0 errors · 7 of 9 test scenarios evidenced  
Walkthrough video: [watch on YouTube](https://youtu.be/nBB0X_uTIaA).

| Step | What you will find |
|---|---|
| [1 · Requirement](1-requirement/FRD.md) | The client's Functional Requirement Document ([original .docx](1-requirement/REQ-DMF-602-FRD.docx)) |
| [2 · Design](2-design/TDD.md) | The Technical Design Document DynoLoom generated and the client approved · [readiness questions](2-design/readiness-questions.md) |
| [3 · Code](3-code/) | The X++ metadata exactly as delivered ([full diff](3-code/changes.patch)) |
| [4 · Testing](4-testing/test-cases.md) | Test scenarios from the TDD, results and screenshots |

## What was built

The summary below is section 1 of the approved design, unchanged.

**What you asked for and what this design delivers**

| Feature named in the requirement | This design |
|---|---|
| New field Loyalty Tier on the customer (Standard, Silver, Gold, Platinum; default Standard) | Built |
| New field Preferred Delivery Window on the customer (text, up to 50 characters) | Built |
| New group "Commercial Preferences" on the Sales order defaults FastTab of the customer details screen, view and edit | Built |
| Both fields on the Customers V3 data entity and its staging table, for Data management import/export (CSV, Excel, XML) | Built |
| Both fields in OData `/data/CustomersV3` (GET, POST, PATCH) | Built |
| A blank, empty or omitted Loyalty Tier becomes Standard on Data management import and OData creation | Built. This includes an OData call that sends an empty text `""`, as you confirmed. |
| An unknown Loyalty Tier value rejects the row with a clear error and does not change the customer | Built, with our own error message that names the value and lists the allowed values |

Two new fields are added to the customer master table (CustTable). They appear in a new group, **Commercial Preferences**, on the **Sales order defaults** FastTab of the All customers details screen. On the customer, Loyalty Tier is a selection list (enum) whose first value is Standard.

The same two fields are added to the standard **Customers V3** data entity (`CustCustomerV3Entity`, OData collection `CustomersV3`) and its staging table (`CustCustomerV3Staging`). This version follows your answer that the CRM may send an empty Loyalty Tier. In the entity, Loyalty Tier is therefore received as **text** and turned into the list value by our own code:
- empty or blank becomes Standard;
- Standard, Silver, Gold and Platinum are accepted, in any upper or lower case;
- anything else is rejected with our own message, and the customer is not changed.

Everything is built with extensions in the new model DYLOExtensions. No standard object is overlayered.

---

## Objects delivered

4 new objects and 5 extensions or event handlers on standard objects. No standard object is overlayered: every change to standard behaviour is a table/form/enum extension, a Chain of Command class or an event handler.

| Type | Object | Change |
|---|---|---|
| Class | [`DYLO_CustCustomerV3Entity_Extension`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxClass/DYLO_CustCustomerV3Entity_Extension.xml) | Added |
| Class | [`DYLO_CustLoyaltyTierConverter`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxClass/DYLO_CustLoyaltyTierConverter.xml) | Added |
| Data entity extension | [`CustCustomerV3Entity.DYLOExtensions`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxDataEntityViewExtension/CustCustomerV3Entity.DYLOExtensions.xml) | Added |
| Extended data type | [`DYLO_LoyaltyTierText`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxEdt/DYLO_LoyaltyTierText.xml) | Added |
| Extended data type | [`DYLO_PreferredDeliveryWindow`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxEdt/DYLO_PreferredDeliveryWindow.xml) | Added |
| Base enum | [`DYLO_LoyaltyTier`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxEnum/DYLO_LoyaltyTier.xml) | Added |
| Form extension | [`CustTable.DYLOExtensions`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxFormExtension/CustTable.DYLOExtensions.xml) | Added |
| Table extension | [`CustCustomerV3Staging.DYLOExtensions`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxTableExtension/CustCustomerV3Staging.DYLOExtensions.xml) | Added |
| Table extension | [`CustTable.DYLOExtensions`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxTableExtension/CustTable.DYLOExtensions.xml) | Added |

Labels are in the model's label file, [`DYLOExtensions.en-US.label.txt`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxLabelFile/LabelResources/en-US/DYLOExtensions.en-US.label.txt).
The label file and model descriptor are shared by every requirement in this repository, so the delivered copy also contains entries from requirements delivered before this one.
