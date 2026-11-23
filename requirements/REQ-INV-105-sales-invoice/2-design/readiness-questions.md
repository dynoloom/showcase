# Readiness questions

Before writing the TDD, DynoLoom checks the requirement for gaps and asks the client only what it cannot decide from the document and standard D365 behaviour. These are the questions it asked for this requirement and the client's answers, verbatim.

**Q1.** Figure 1 shows a whole sales invoice in your own style (blue banner, boxed bill-to and invoice details, blue line header, totals block), so should the printed invoice be redesigned to look like that picture, or should the standard invoice keep its current look with only Customer PO Reference and Del

> yes the printed invoice be redesigned to look like that picture

**Q2.** If the invoice is to be redesigned like Figure 1, what should happen to the things the standard invoice prints that the picture does not show (for example the company logo, bank details, delivery address, due date, tax breakdown, notes and footer texts)?

> skip them

**Q3.** Which existing sales order field holds the Customer PO Reference you want printed: 'Customer requisition' (the customer's purchase order number) or 'Customer reference'?

> Customer reference

**Q4.** When several sales orders are invoiced together on one invoice, which Delivery Instructions should the posted invoice show and print: those of the first order, all of them combined, or something else?

> first order

