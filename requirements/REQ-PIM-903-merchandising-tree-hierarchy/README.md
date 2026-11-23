# REQ-PIM-903 — Multi-Level Commercial Merchandising Hierarchy & Margin Roll-Up Tree Form

**Status:** Delivered 2026-11-22 · Compiled with 0 errors · 12 of 16 test scenarios evidenced  
Walkthrough video: recorded, publishing on [@Dynoloom](https://www.youtube.com/@Dynoloom).

| Step | What you will find |
|---|---|
| [1 · Requirement](1-requirement/FRD.md) | The client's Functional Requirement Document ([original .docx](1-requirement/REQ-PIM-903-FRD.docx)) |
| [2 · Design](2-design/TDD.md) | The Technical Design Document DynoLoom generated and the client approved · [readiness questions](2-design/readiness-questions.md) |
| [3 · Code](3-code/) | The X++ metadata exactly as delivered ([full diff](3-code/changes.patch)) |
| [4 · Testing](4-testing/test-cases.md) | Test scenarios from the TDD, results and screenshots |

## What was built

The summary below is section 1 of the approved design, unchanged.

**What you asked for and what this design delivers**

| Feature named in the requirement | This design |
|---|---|
| Self-referencing hierarchy table (Node ID, Node Name, Parent Node, auto-calculated Hierarchy Level, Minimum Gross Margin %, Active) | **Built** (new table, per legal entity) |
| Tree-view form with expand/collapse | **Built** (new form with a tree control) |
| Dynamic child node creation | **Built** ("New child node" button) |
| Up/down sibling reordering | **Built** ("Move up" / "Move down") |
| Node re-parenting | **Built** ("Re-parent node" dialog) |
| Circular reference protection (recursive ancestor validation) | **Built** |
| Margin rule inheritance from nearest parent, updated automatically on change (AC-03) | **Built** |
| Assign released products to leaf nodes, one node per product per legal entity | **Built** |
| Delete guard (no delete while children or products exist) | **Built** |
| Category Manager per node (from mockup) | **Built** |
| Assigned products grid with Target Margin % and roll-up count (from mockup) | **Built** (Target Margin % = node's inherited threshold) |
| Minimum margin enforcement | **Built** as a **warning** (posting continues) when a trade agreement journal is posted with a sales price below the node's minimum gross margin |
| Screen look of the mockup (Figure 1) | **Built** with the tree, node details and product grid placed as in the mockup, in the standard D365 look (theme colours, spacing) and titled "Merchandising hierarchy" – as the client confirmed |

The design adds a new form *Product information management > Setup > Categories and attributes > Merchandising hierarchy*. It shows the hierarchy as a tree on the left and the selected node's details on the right. Below the details is a grid of released products assigned to the node and its sub-nodes. The hierarchy and product assignments are kept per legal entity. When a trade agreement journal with sales prices is posted, each price line for an item in the hierarchy is checked against the item's active cost price. If the gross margin is below the node's effective minimum, a warning is shown and posting continues.

---

## Objects delivered

20 new objects and 2 extensions or event handlers on standard objects. No standard object is overlayered: every change to standard behaviour is a table/form/enum extension, a Chain of Command class or an event handler.

| Type | Object | Change |
|---|---|---|
| Class | [`DYLO_MerchHierarchyManager`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxClass/DYLO_MerchHierarchyManager.xml) | Added |
| Class | [`DYLO_MerchHierarchyTreeDatasource`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxClass/DYLO_MerchHierarchyTreeDatasource.xml) | Added |
| Class | [`DYLO_MerchMarginCheck`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxClass/DYLO_MerchMarginCheck.xml) | Added |
| Class | [`PriceDiscAdmCheckPost_DYLO_Extension`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxClass/PriceDiscAdmCheckPost_DYLO_Extension.xml) | Added |
| Extended data type | [`DYLO_MerchHierarchyLevel`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxEdt/DYLO_MerchHierarchyLevel.xml) | Added |
| Extended data type | [`DYLO_MerchMinGrossMarginPct`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxEdt/DYLO_MerchMinGrossMarginPct.xml) | Added |
| Extended data type | [`DYLO_MerchNodeId`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxEdt/DYLO_MerchNodeId.xml) | Added |
| Extended data type | [`DYLO_MerchNodeName`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxEdt/DYLO_MerchNodeName.xml) | Added |
| Extended data type | [`DYLO_MerchNodeRefRecId`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxEdt/DYLO_MerchNodeRefRecId.xml) | Added |
| Extended data type | [`DYLO_MerchSortOrder`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxEdt/DYLO_MerchSortOrder.xml) | Added |
| Form | [`DYLO_MerchHierarchy`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxForm/DYLO_MerchHierarchy.xml) | Added |
| Form | [`DYLO_MerchHierarchyReparentDialog`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxForm/DYLO_MerchHierarchyReparentDialog.xml) | Added |
| Menu extension | [`ProductInformationManagement.DYLOExtensions`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxMenuExtension/ProductInformationManagement.DYLOExtensions.xml) | Added |
| Display menu item | [`DYLO_MerchHierarchy`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxMenuItemDisplay/DYLO_MerchHierarchy.xml) | Added |
| Display menu item | [`DYLO_MerchHierarchyReparentDialog`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxMenuItemDisplay/DYLO_MerchHierarchyReparentDialog.xml) | Added |
| Security duty | [`DYLO_MerchHierarchyInquire`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxSecurityDuty/DYLO_MerchHierarchyInquire.xml) | Added |
| Security duty | [`DYLO_MerchHierarchyMaintain`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxSecurityDuty/DYLO_MerchHierarchyMaintain.xml) | Added |
| Security privilege | [`DYLO_MerchHierarchyMaintain`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxSecurityPrivilege/DYLO_MerchHierarchyMaintain.xml) | Added |
| Security privilege | [`DYLO_MerchHierarchyView`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxSecurityPrivilege/DYLO_MerchHierarchyView.xml) | Added |
| Security role | [`DYLO_MerchCategoryManager`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxSecurityRole/DYLO_MerchCategoryManager.xml) | Added |
| Table | [`DYLO_MerchHierarchyNode`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxTable/DYLO_MerchHierarchyNode.xml) | Added |
| Table | [`DYLO_MerchHierarchyProduct`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxTable/DYLO_MerchHierarchyProduct.xml) | Added |
| Model descriptor | [`DYLOExtensions`](3-code/Metadata/DYLOExtensions/Descriptor/DYLOExtensions.xml) | Modified |

Labels are in the model's label file, [`DYLOExtensions.en-US.label.txt`](3-code/Metadata/DYLOExtensions/DYLOExtensions/AxLabelFile/LabelResources/en-US/DYLOExtensions.en-US.label.txt).
The label file and model descriptor are shared by every requirement in this repository, so the delivered copy also contains entries from requirements delivered before this one.
