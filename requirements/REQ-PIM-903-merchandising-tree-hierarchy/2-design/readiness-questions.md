# Readiness questions

Before writing the TDD, DynoLoom checks the requirement for gaps and asks the client only what it cannot decide from the document and standard D365 behaviour. These are the questions it asked for this requirement and the client's answers, verbatim.

**Q1.** Should the Minimum Gross Margin % actually be enforced anywhere (for example a warning or block when a sales price or trade agreement gives a lower margin), or is it only stored, inherited and shown on the hierarchy form?

> a warning or block when a sales price or trade agreement gives a lower margin

**Q2.** Can a released product be assigned to only one node of the hierarchy or to several, and is the hierarchy and its product assignment shared across all legal entities or kept per legal entity?

> only one node of the hierarchy. its per legal entity

**Q3.** The mockup shows a Category Manager field on each node and a Target Margin % per assigned product, which the text does not mention: should both be built, and where does the product's Target Margin % come from (the node's inherited threshold, or a value entered per product)?

> build both, it comes from node's inherited thresholdQ: When a sales price or trade agreement gives a margin below the node's Minimum Gross Margin %, should the user only get a warning and be allowed to continue, or should the price be blocked from being saved or posted?
> A: warning and be allowed to continue

**Q4.** Which cost should the gross margin be calculated against: the item's active cost price on the released product, its standard cost, or another cost figure?

> the item's active cost price on the released product

**Q5.** Where exactly should the margin check happen: only when the default sales price is entered on the released product and when trade agreement journals are posted, or also on each sales order or quotation line?

> when trade agreement journals are postedQ: The new screen will use the standard D365 look (theme colours, standard spacing) with the tree, node details and product grid placed as in your mockup, but not the mockup's exact dark-blue colours and positions, and it will be titled 'Merchandising hierarchy' rather than 'Commercial Catalog Hierarchy' - is that acceptable?
> A: its fine that new screen use the standard D365 look and its fine to be  titled 'Merchandising hierarchy'

