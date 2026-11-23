# Code standards

Every object DynoLoom generates follows the rules below. They are the rules a senior D365 F&O developer would apply on
a client project, and each one can be checked against the code in this repository.

## Extension model only — no overlayering

- All work goes into one extension model, **`DYLOExtensions`** ([descriptor](../requirements/REQ-PRC-901-batch-mass-price-update/3-code/Metadata/DYLOExtensions/Descriptor/DYLOExtensions.xml)).
- Standard objects are never overlayered. Standard behaviour is changed only through **table, form, enum, menu and
  security role extensions**, **Chain of Command** classes and **event handlers**.
- Every Chain of Command wrapper calls `next`, so standard logic and other extensions keep running.
  For example, [`CustInvoiceJour_DYLO_Extension`](../requirements/REQ-INV-105-sales-invoice/3-code/Metadata/DYLOExtensions/DYLOExtensions/AxClass/CustInvoiceJour_DYLO_Extension.xml).

## Naming

- Every new object and field carries the **`DYLO_`** prefix, so nothing can collide with Microsoft or ISV objects.
  The one exception is a data entity field whose public name the requirement fixes (`LoyaltyTier` and
  `PreferredDeliveryWindow` on Customers V3, [REQ-DMF-602](../requirements/REQ-DMF-602-customer-data-entity-extension/)).
  `DYLO` is the prefix this demonstration workspace chose; a client's own work carries the client's prefix.
- Every extension class carries the prefix too and ends in the required `_Extension` suffix:
  `DYLO_<Object>_Extension` or `<Object>_DYLO_Extension`.
- Extension elements are named `<StandardObject>.DYLOExtensions` (or `.DYLO_Extension`).

## Types and labels

- Fields use **extended data types** and **base enums**, new ones where needed (`DYLO_LoyaltyTier`,
  `DYLO_DeliveryInstructions`, …), never raw primitive types.
- Every user-facing text — field labels, help text, menu items, info/warning/error messages — is a **label**
  (`@DYLOExtensions:…`) in the model's label file. No message is hard-coded in X++.

## Security

- Every new entry point (menu item, service operation, batch job) has a **privilege**, grouped into **duties**, and
  the duties are added to the standard roles named in the design (for example *Accounts receivable manager*), or to new
  roles when the requirement asks for them.
- Custom services are exposed through a **service group** and are invoked under a dedicated privilege
  ([REQ-INT-508](../requirements/REQ-INT-508-sales-order-api/)).

## Frameworks used the standard way

- Batch processing uses the **SysOperation** framework (contract, controller, service), with multi-threading through
  batch tasks where volumes call for it ([REQ-PRC-901](../requirements/REQ-PRC-901-batch-mass-price-update/)).
- Reports use **RDP classes**, contracts, UI builders and temp tables, and are registered with **Print management**
  where they replace a standard document ([REQ-INV-105](../requirements/REQ-INV-105-sales-invoice/)).
- Data integration extends the **standard data entities** and their staging tables rather than copying them
  ([REQ-DMF-602](../requirements/REQ-DMF-602-customer-data-entity-extension/)).
- Approvals use the **workflow framework**: workflow type, approval, document class and event handlers
  ([REQ-AR-804](../requirements/REQ-AR-804-credit-limit-workflow/)).
- Ledger postings go through **ledger vouchers** and the standard posting framework
  ([REQ-GL-902](../requirements/REQ-GL-902-warranty-ledger-voucher-posting/)).

## Documentation in code

- Classes and methods carry XML documentation comments (`/// <summary>`) that say what they do and why.

## How to check

From a clone of this repository:

```bash
# Every new object carries the DYLO prefix
find requirements -path "*/Ax*/*.xml" ! -path "*Extension/*" ! -path "*AxLabelFile*" -printf "%f\n" | grep -v "DYLO_"

# Every Chain of Command class (ExtensionOf) — open each and check that wrappers call next
grep -rl "ExtensionOf" requirements/*/3-code --include=*.xml

# No hard-coded messages: info/warning/error/checkFailed take labels
grep -rhoE '(info|warning|error|checkFailed)\("[^@][^"]*"' requirements/*/3-code
```

The first and last commands print nothing when the rule holds.
