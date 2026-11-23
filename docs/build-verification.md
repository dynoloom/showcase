# Build verification

The code in this repository was compiled again, independently of the platform, on 2026-11-23. Each build was assembled
in an empty folder from this repository's files only and compiled with the X++ compiler (`xppc`) of a D365 F&O 10.0.48
development environment.

To prove a build cannot pick up objects from anywhere else, one object (`DYLO_PriceAdjLog`) was deliberately removed
from the full build afterwards: that build failed with 20 errors. Every result below is therefore a real compile of
exactly the files in this repository.

Every label the code uses (`@DYLOExtensions:…`) exists in the label file (0 missing).

A clean compile says the code builds. It does not say the code behaves as designed: that is what each requirement's
[`4-testing`](../README.md#delivered-requirements) page records, including the scenarios that failed.

## The nine requirements, in delivery order

Each requirement's `3-code/Metadata` copied on top of the requirements delivered before it, which is how the platform
delivered them into the one model, `DYLOExtensions`.

| Step | Requirement | Objects in build | Errors | Warnings |
|---:|---|---:|---:|---:|
| 1 | REQ-PRC-901 | 17 | **0** | 1 |
| 2 | REQ-PIM-903 | 39 | **0** | 1 |
| 3 | REQ-INV-105 | 50 | **0** | 1 |
| 4 | REQ-INT-508 | 89 | **0** | 0 |
| 5 | REQ-DMF-602 | 98 | **0** | 0 |
| 6 | REQ-AR-410 | 110 | **0** | 0 |
| 7 | REQ-GL-902 | 127 | **0** | 2 |
| 8 | REQ-COL-715 | 153 | **0** | 2 |
| 9 | REQ-AR-804 | 194 | **0** | 2 |

Step 9 is the complete model. Its files are identical, file for file, to the model the platform's connector applied to
the dev machine the tests ran on.

## About the warnings

None of the warnings is a compile error. There are two kinds:

- **Environment reference** (steps 1 to 3): *ExternalReference Warning … Microsoft.Dynamics.Commerce.Runtime …
  PricingEngine*. It comes from a standard Microsoft assembly reference on the development environment, not from this
  code.
- **Obsolete method** (REQ-GL-902, from step 7): the unit test class `DYLO_WarrantyProvisionPostingTest` calls
  `getDefaultAccountFromMainAccountId` twice, which the platform marks obsolete in favour of
  `getDefaultAccountFromMainAccountIdAndCOA`.
