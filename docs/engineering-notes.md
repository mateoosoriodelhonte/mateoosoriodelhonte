# Engineering notes

Short checks for reviewing systems that handle uncertain data, external APIs, or generated output. Each note describes a failure mode and a way to verify the fix.

## 1. Define the input contract

List accepted types, size limits, required fields, and normalization rules before writing a parser. Keep one fixture for each boundary: empty input, maximum size, malformed encoding, and an unexpected field. A contract is useful only when tests enforce its edge cases.

## 2. Preserve unknown values

Missing evidence is different from a measured zero. Model unknown as a separate state and carry it through storage, scoring, and UI text. In a report, show why evidence is unavailable so readers can distinguish a weak result from a collection failure.

## 3. Keep source provenance

Attach a stable source identifier and location to each extracted fact. If text is transformed or chunked, retain enough offsets to find the original span. Reviewers should be able to move from a claim back to the exact input that supports it.

## 4. Separate collection from judgment

Normalize external API responses into a small internal model before scoring them. Save the raw response shape in fixtures, then test normalization and evaluation separately. This makes a provider change distinguishable from a scoring rule change.

## 5. Name assumptions in metrics

For each metric, write down its denominator, treatment of duplicates, and behavior when data is missing. Test these with tiny hand-computed examples. A precise label and worked example are more valuable than an impressive-looking number with ambiguous units.

## 6. Make rankings reproducible

Specify tie breakers, candidate limits, and sorting order for retrieval or scoring. Use fixed fixtures and compare identifiers as well as scores. Deterministic ranking makes regressions easier to isolate and makes review output easier to explain.

## 7. Keep retrieval stages visible

Record which stage supplied a candidate: lexical search, vector search, fusion, or reranking. Expose rank and score from each stage without treating unlike scores as interchangeable. This makes it possible to explain why a result appeared or disappeared.

## 8. Evaluate with traceable relevance

Store relevance judgments against stable source spans or document identifiers. Inspect mismatches between retrieved chunks and labeled evidence before changing an evaluator. A metric is useful only when a reviewer can inspect the examples behind it.

## 9. Treat empty states as outcomes

Define separate UI states for no matching data, data not loaded, permission denied, and request failure. Give each state a useful next step. Collapsing them into an empty list hides operational problems and can mislead the person using the product.

## 10. Put bounds on external calls

Set timeouts, response limits, pagination limits, and retry budgets for network requests. Retries need a stop condition and should respect rate limits. A test with a slow or malformed response can verify that the system fails predictably instead of hanging.

## 11. Make failures actionable

Log the operation, a correlation identifier, the failing boundary, and a safe summary of the cause. Avoid logging secrets or entire payloads. An error message should help a maintainer reproduce the failure while keeping private data out of diagnostics.

## 12. Distinguish stale from current

When showing cached data, expose when it was collected and whether a refresh failed. A stale value can still be useful if labeled. Test cache expiration and the case where no prior value exists; these paths often need different UI behavior.

## 13. Build minimal fixtures

Use the smallest input that demonstrates a behavior. A compact fixture makes expected output obvious and reviews faster. Keep large real-world samples only when they cover a boundary that smaller synthetic data cannot reproduce.

## 14. Test at the boundary

For size, count, and time limits, test just below, at, and just above the limit. Include invalid units and zero where allowed. Boundary tests catch off-by-one errors without duplicating every branch of the implementation.

## 15. Test the failure path

For every happy-path integration test, identify one failure that would matter to a user: rejected credentials, partial API data, corrupt file, or unavailable dependency. Assert both the error state and the absence of misleading success output.

## 16. Keep regression tests tied to causes

A regression test should capture the input that triggered the bug and the observable result that was wrong. Name the failure mode in the test. Avoid asserting internal steps unless those steps are part of the product contract.

## 17. Review configuration defaults

Document what happens when each optional setting is omitted. Test the default path explicitly because it is what most users run. If a setting changes privacy, network access, or resource use, make that effect clear at the point of configuration.

## 18. Design safe local defaults

Bind development services to loopback when remote access is not required. Limit file paths and output sizes, and keep destructive commands explicit. Verify the default setup before documenting optional ways to widen access.

## 19. Document repeatable setup

List prerequisites, exact install commands, one smoke test, and how to reset local state. Keep generated files and secrets out of the repository. A newcomer should be able to tell whether setup succeeded without guessing from a long log.

## 20. Explain command side effects

For each CLI action, state what it reads, writes, or deletes, and give a dry-run path when practical. Show the expected output of a successful small example. This helps users judge whether a command is safe to run on their own data.

## 21. Make reviews evidence based

Describe the observed behavior, the expected behavior, a reproduction path, and the smallest supporting artifact. Separate confirmed facts from hypotheses. A useful review comment lets the author verify the claim without trusting the reviewer’s intuition.

## 22. Keep changes bounded

State the scope of a change before editing. Keep unrelated formatting or refactoring out of a fix unless it is required for correctness. A focused diff makes tests, rollback, and review more reliable.

