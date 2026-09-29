# Engineering notes

Short checks for reviewing systems that handle uncertain data, external APIs, or generated output. Each note describes a failure mode and a way to verify the fix.

## 1. Define the input contract

List accepted types, size limits, required fields, and normalization rules before writing a parser. Keep one fixture for each boundary: empty input, maximum size, malformed encoding, and an unexpected field. A contract is useful only when tests enforce its edge cases.

## 2. Preserve unknown values

Missing evidence is different from a measured zero. Model unknown as a separate state and carry it through storage, scoring, and UI text. In a report, show why evidence is unavailable so readers can distinguish a weak result from a collection failure.

## 3. Keep source provenance

Attach a stable source identifier and location to each extracted fact. If text is transformed or chunked, retain enough offsets to find the original span. Reviewers should be able to move from a claim back to the exact input that supports it.

