# Readiness questions

Before writing the TDD, DynoLoom checks the requirement for gaps and asks the client only what it cannot decide from the document and standard D365 behaviour. These are the questions it asked for this requirement and the client's answers, verbatim.

**Q1.** Which overdue tiers should trigger a notification (for example 15, 30 and 60 days past due), and should finance users be able to change these tiers themselves?

> new setting form

**Q2.** Should an invoice that is overdue but has not yet reached the first tier get an alert, or should alerts only be sent when an invoice reaches each tier?

> alerts only be sent when an invoice reaches each tier

**Q3.** Should partly paid invoices and free text invoices be included, and should the email show the remaining open amount or the original invoice amount?

> yes both . the email show both

**Q4.** The mockup shows 'Run Batch Now' and 'View Email Payload' buttons that the document's text does not mention: should both be built, and what should 'View Email Payload' show that the right-hand preview pane does not already show?

> go with your recommendations and feel free to skip if they add no valueQ: The mockup's 'View Email Payload' button would show the same email that the right-hand pane already shows: may we leave it out?
> A: ok , leave it

**Q5.** The new screen will use the standard D365 look (green/red status cells, but no dark blue header band, pink error box or typewriter font in the email preview): is that acceptable?

> ok go with standard D365 look

