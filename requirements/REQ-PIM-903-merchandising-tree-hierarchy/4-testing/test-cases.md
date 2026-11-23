# Test cases — REQ-PIM-903

The scenarios below are the test plan from the approved TDD (section 6), unchanged. The **Result** column records only what is backed by evidence in this folder; a scenario without evidence is marked *Not yet recorded* rather than assumed to pass.

## Build verification

| Check | Result |
|---|---|
| Compile (X++ compiler, model DYLOExtensions) | ✅ 0 errors |
| Delivered | 2026-11-22 |

## Functional scenarios

Tested in Dynamics 365 Finance & Operations 10.0.48 on the Contoso demo data, legal entity USMF.

| # | Scenario | Expected | Result | Evidence |
|---|---|---|---|---|
| T1 (AC-01) | Open the form in a new legal entity | Root created and shown, level 1; children expand in sort order with levels 2, 3, 4; caption "Merchandising hierarchy" | ✅ Passed | [a_form_opens_root_created.png](evidence/a_form_opens_root_created.png) · [c_tree_expanded_120A_inherits_28.png](evidence/c_tree_expanded_120A_inherits_28.png) |
| T2 | New child node under 100 | Node created at level 3, last in order, inherits 100's effective margin | ✅ Passed | [b_new_child_node_under_100.png](evidence/b_new_child_node_under_100.png) |
| T3 | Move up / Move down on 120 | 120 and 110 swap order in the tree; first/last node's button disabled | ✅ Passed | [s_move_up_120_first_moveup_disabled.png](evidence/s_move_up_120_first_moveup_disabled.png) |
| T4 (AC-02) | Re-parent 100 under 120 (its descendant) | Error "cannot be moved under … descendants"; nothing changes | ✅ Passed The message is cut off on screen by the narrow dialog; the full text was read from the page. | [k_reparent_refused_circular.png](evidence/k_reparent_refused_circular.png) |
| T5 | Re-parent 120 under 200 | 120 and its subtree move; levels and inherited margins recalculated | ✅ Passed | [o_120_moved_under_200_inherits_22.png](evidence/o_120_moved_under_200_inherits_22.png) |
| T6 (AC-03) | Change 100's margin from 28 to 30; 110 blank, 120-B own 31.5 | 110 and 120-A effective 30 (inherited from 100); 120-B stays 31.5 | ✅ Passed | [c_tree_expanded_120A_inherits_28.png](evidence/c_tree_expanded_120A_inherits_28.png) · [g_120A_inherits_30.png](evidence/g_120A_inherits_30.png) · [h_120B_keeps_31_5.png](evidence/h_120B_keeps_31_5.png) |
| T7 (AC-04) | Delete 120 with children; delete a leaf with products | Both blocked with messages; deleting an empty leaf works | ✅ Passed | [x_delete_120_blocked_has_child_nodes.png](evidence/x_delete_120_blocked_has_child_nodes.png) · [y_delete_120A_blocked_has_products.png](evidence/y_delete_120A_blocked_has_products.png) |
| T8 | Assign A0012 to leaf 120-A, then to 120-B | First works; second error "already assigned to node 120-A" | ✅ Passed | [v_T0004_on_second_leaf_120B_refused.png](evidence/v_T0004_on_second_leaf_120B_refused.png) |
| T9 | Add product on non-leaf 120 or inactive node | Button disabled / validateWrite error | ✅ Passed The Add product button is disabled in both cases; the error path behind it was not exercised. | [w_select_120_rollup_3_products_target_margins.png](evidence/w_select_120_rollup_3_products_target_margins.png) · [zb_inactive_node_110_add_product_and_new_child_disabled.png](evidence/zb_inactive_node_110_add_product_and_new_child_disabled.png) |
| T10 | Select 120 | Grid lists products of 120-A and 120-B; heading shows roll-up count; Target margin % per product = its node's effective margin; Category manager shown | ✅ Passed | [w_select_120_rollup_3_products_target_margins.png](evidence/w_select_120_rollup_3_products_target_margins.png) |
| T11 | Same item, same node, in another legal entity | Independent assignment and hierarchy | — Not yet recorded |  |
| T12 | Post trade agreement journal, sales price line, margin below threshold (price in EUR, unit box, company USD) | Warning with item, date, margin and node; journal posted; agreement created | ✅ Passed Run in USD with unit ea; the EUR and box-unit variant was not run. The created agreements were confirmed in the database, not by screenshot. | [zd_post_result_margin_warning.png](evidence/zd_post_result_margin_warning.png) |
| T13 | Same journal, margin above threshold; item not in hierarchy; item-group line; purchase price line | No warning; posting as standard | ◐ Partly evidenced No warning for the line above the threshold or for the item outside the hierarchy. Item-group and purchase-price lines were not run. | [zd_post_result_margin_warning.png](evidence/zd_post_result_margin_warning.png) |
| T14 | Post the journal in batch | Warning in the batch job's log; journal posted | — Not yet recorded |  |
| T15 | Delete a released product with an assignment | Assignment deleted | — Not yet recorded |  |
| T16 | User with view duty only | Form opens read-only; buttons disabled | — Not yet recorded |  |

### a_form_opens_root_created.png

First opening of the Merchandising hierarchy form in a company with no nodes: the root node ROOT - All commercial products is created and shown at level 1.

![First opening of the Merchandising hierarchy form in a company with no nodes: the root node ROOT - All commercial products is created and shown at level 1.](evidence/a_form_opens_root_created.png)

### b_new_child_node_under_100.png

Right after New child node on 100 - Electronics: the new node is last under its parent, at level 3, with an effective margin of 28.00 inherited from 100 - Electronics.

![Right after New child node on 100 - Electronics: the new node is last under its parent, at level 3, with an effective margin of 28.00 inherited from 100 - Electronics.](evidence/b_new_child_node_under_100.png)

### c_tree_expanded_120A_inherits_28.png

The tree fully expanded over four levels; 120-A - Televisions has no margin of its own and shows an effective 28.00 inherited from 100 - Electronics.

![The tree fully expanded over four levels; 120-A - Televisions has no margin of its own and shows an effective 28.00 inherited from 100 - Electronics.](evidence/c_tree_expanded_120A_inherits_28.png)

### g_120A_inherits_30.png

After 100 - Electronics is changed from 28 to 30, 120-A - Televisions shows an effective 30.00 inherited from 100 - Electronics.

![After 100 - Electronics is changed from 28 to 30, 120-A - Televisions shows an effective 30.00 inherited from 100 - Electronics.](evidence/g_120A_inherits_30.png)

### h_120B_keeps_31_5.png

After the same change, 120-B - Projectors keeps its own minimum margin of 31.50.

![After the same change, 120-B - Projectors keeps its own minimum margin of 31.50.](evidence/h_120B_keeps_31_5.png)

### k_reparent_refused_circular.png

Re-parenting 100 - Electronics under its descendant 120 is refused with a warning, and the tree behind the dialog is unchanged.

![Re-parenting 100 - Electronics under its descendant 120 is refused with a warning, and the tree behind the dialog is unchanged.](evidence/k_reparent_refused_circular.png)

### o_120_moved_under_200_inherits_22.png

120 - Video re-parented under 200 - Home appliances: its subtree moved with it, and its effective margin is now 22.00 inherited from 200 - Home appliances.

![120 - Video re-parented under 200 - Home appliances: its subtree moved with it, and its effective margin is now 22.00 inherited from 200 - Home appliances.](evidence/o_120_moved_under_200_inherits_22.png)

### s_move_up_120_first_moveup_disabled.png

After two Move up actions 120 - Video is the first sibling under 100 - Electronics, and Move up is disabled.

![After two Move up actions 120 - Video is the first sibling under 100 - Electronics, and Move up is disabled.](evidence/s_move_up_120_first_moveup_disabled.png)

### v_T0004_on_second_leaf_120B_refused.png

Assigning item T0004 to a second node is refused: "Item T0004 is already assigned to node 120-A - Televisions."

![Assigning item T0004 to a second node is refused: "Item T0004 is already assigned to node 120-A - Televisions."](evidence/v_T0004_on_second_leaf_120B_refused.png)

### w_select_120_rollup_3_products_target_margins.png

Selecting 120 - Video rolls up the three products of its two product lines, each with the target margin of its own node; Add product is disabled because 120 is not a leaf.

![Selecting 120 - Video rolls up the three products of its two product lines, each with the target margin of its own node; Add product is disabled because 120 is not a leaf.](evidence/w_select_120_rollup_3_products_target_margins.png)

### x_delete_120_blocked_has_child_nodes.png

Deleting 120 - Video is refused: "Node 120 has child nodes; move or delete them first."

![Deleting 120 - Video is refused: "Node 120 has child nodes; move or delete them first."](evidence/x_delete_120_blocked_has_child_nodes.png)

### y_delete_120A_blocked_has_products.png

Deleting 120-A - Televisions is refused: "Node 120-A has assigned products; move or remove them first."

![Deleting 120-A - Televisions is refused: "Node 120-A has assigned products; move or remove them first."](evidence/y_delete_120A_blocked_has_products.png)

### zb_inactive_node_110_add_product_and_new_child_disabled.png

On the inactive node 110 - Audio, New child node and Add product are both disabled.

![On the inactive node 110 - Audio, New child node and Add product are both disabled.](evidence/zb_inactive_node_110_add_product_and_new_child_disabled.png)

### zd_post_result_margin_warning.png

After posting the trade agreement journal: "Operation completed", with one warning that item T0004 has a gross margin of 20.00 %, below the minimum 30.00 % of node 120-A - Televisions.

![After posting the trade agreement journal: "Operation completed", with one warning that item T0004 has a gross margin of 20.00 %, below the minimum 30.00 % of node 120-A - Televisions.](evidence/zd_post_result_margin_warning.png)

## Notes

Tested in USMF demo data on the dev box against section 6 of the approved design. Both hierarchy tables were empty before the test, so the first opening of the form is a true new-company case.

**Not run:**
- T11 (the same item and node in another legal entity): the test was limited to USMF.
- T14 (posting the journal in batch): left out to keep the test data small.
- T15 (deleting a released product that has an assignment): it would delete demo master data.
- T16 (a user with the view duty only): only an administrator session was available.

**Partly passed:** T13. The posted journal held three sales price lines and produced exactly one warning, for the item below the threshold; there was no warning for the item at a 40 % margin or for the item that is not in the hierarchy. Item-group lines and purchase-price lines were not run.

**Defects found (display only):**
1. The roll-up count in the products heading is not refreshed after a product is added. The grid shows the new row but the heading keeps the old count until another node is selected and the node is selected again.
2. The tree text is not refreshed after a node is renamed or set inactive. The tree keeps the old text until the form is reopened; the design says the tree item text is refreshed on save.

**Observations, not counted as failures:**
- The circular re-parent message is cut off on screen because the dialog is narrow; the full text is correct.
- The first grid column is headed "Item number" (the standard label) where the design names it "Item ID". The Status column is empty for the demo items because they have no product lifecycle state.
- New child node saves the node at once with a generated code and name, which the user then overwrites. This works but is not described in the design.
