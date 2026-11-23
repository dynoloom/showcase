# Readiness questions

Before writing the TDD, DynoLoom checks the requirement for gaps and asks the client only what it cannot decide from the document and standard D365 behaviour. These are the questions it asked for this requirement and the client's answers, verbatim.

**Q1.** Which person should the report show as the 'Account Rep' in each customer header: the customer's sales responsible or main contact on the customer record, or the sales responsible on each sales order?

> sales responsible on each sales order

**Q2.** How should 'Shortage Units (Uncovered)' be calculated: for each line, open quantity minus available physical stock (never below zero), or by item and warehouse so that stock shared by several open lines for the same item is only counted once?

> open quantity minus available physical stock

**Q3.** Should the 'Total Open Backorders' card and the high-risk card count distinct sales orders (as the mockup's 'Orders' suggests) or individual backordered order lines?

> yes as mockup's 'Orders' suggests

**Q4.** Which orders count as being 'on credit hold' for the 'Include Credit-Hold Orders' option: orders held by credit management, orders of customers whose account is blocked, or both?

> both

