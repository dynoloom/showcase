# Reviewer's guide

How to evaluate a requirement in this repository in about fifteen minutes, and how to load the code on a dev box if you
want to go further.

## A quick review

1. **Read the FRD** (`1-requirement/FRD.md`). Note what is clearly specified and what is left open.
2. **Read the TDD** (`2-design/TDD.md`). Check that:
   - every functional rule and acceptance criterion in the FRD is covered;
   - the open points became either a readiness question (`readiness-questions.md`) or a stated assumption in
     section 2 of the TDD — nothing is silently guessed;
   - the objects it names are real standard D365 objects, and the design extends them rather than replacing them.
3. **Read the code** (`3-code/`). The requirement's README lists every object with a link. Look at:
   - Chain of Command classes: correct signatures, `next` called, logic placed before or after `next` deliberately;
   - validation and error messages: labels, clear wording, no data corrupted on failure;
   - security: privileges and duties for every new menu item, service and batch job;
   - performance: set-based operations and indexed lookups where volumes matter.
4. **Check the tests** (`4-testing/test-cases.md`). The scenarios are the TDD's test plan; the Result column shows
   which ones are backed by evidence.

`3-code/changes.patch` shows the same delivery as a diff, which is the quickest way to read it end to end.

## Loading the code on a dev box

Use a D365 F&O development environment (10.0.4x) with Contoso demo data.

**All nine together.** The nine requirements were submitted together and delivered into one model, `DYLOExtensions`,
one after the other. The shared files (model descriptor, label file, and the standard menus and roles several
requirements extend) therefore carry content from the requirements delivered before them. Apply the `3-code/Metadata`
folders in delivery order, letting each one overwrite the shared files of the one before
(see [build verification](build-verification.md)):

| Order | Requirement | Delivered |
|---:|---|---|
| 1 | REQ-PRC-901 | 2026-11-22 |
| 2 | REQ-PIM-903 | 2026-11-22 |
| 3 | REQ-INV-105 | 2026-11-22 |
| 4 | REQ-INT-508 | 2026-11-22 |
| 5 | REQ-DMF-602 | 2026-11-22 |
| 6 | REQ-AR-410 | 2026-11-22 |
| 7 | REQ-GL-902 | 2026-11-22 |
| 8 | REQ-COL-715 | 2026-11-22 |
| 9 | REQ-AR-804 | 2026-11-22 |

Then apply the two warranty fixes on top: copy `3-code/warranty-fix/Metadata` of REQ-PRC-901 and of REQ-AR-410 over the
result. Each replaces files of its own requirement only.

**One requirement on its own.** Apply the folders up to and including the one you want, in the same order, then that
requirement's warranty fix if it has one.

Then build the `DYLOExtensions` model in Visual Studio, synchronise the database, deploy the reports for report
requirements, and follow the scenarios in `4-testing/test-cases.md`.
