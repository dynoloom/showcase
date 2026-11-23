# Readiness questions

Before writing the TDD, DynoLoom checks the requirement for gaps and asks the client only what it cannot decide from the document and standard D365 behaviour. These are the questions it asked for this requirement and the client's answers, verbatim.

**Q1.** Can the CRM leave out Loyalty Tier (or send "Standard") when it has no value, rather than sending an empty text, which OData rejects with an error?

> no if it has no value the default is standard

