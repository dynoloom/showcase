# Readiness questions

Before writing the TDD, DynoLoom checks the requirement for gaps and asks the client only what it cannot decide from the document and standard D365 behaviour. These are the questions it asked for this requirement and the client's answers, verbatim.

**Q1.** When an approval is not actioned in time, to whom should it escalate (for example the approver's manager, the Credit Manager role, or the Finance Director), and does the time limit differ per step, since the document says 48 hours for all while the diagram shows 24 hours for the AR Team Lead and 48

> we need to design this in workflow editor

**Q2.** Which users or roles should still be allowed to edit the Credit Limit directly on the customer (for example Credit Managers or the Finance Director), and should the field stay editable when a new customer is first created?

> Finance Director only. the field should not be editable when customer is created

**Q3.** Once the field is locked, how should credit limit decreases be made: through the same request form (with its own approval), or by a role that keeps direct edit rights?

> its decreased the same way we increase it throw workflow. but in workflow design we will make it auto approved since we are decreasing

**Q4.** In which currency are the $25,000 and $100,000 thresholds meant: the legal entity's accounting currency, or always USD converted from each company's currency?

> setting per legal entity

