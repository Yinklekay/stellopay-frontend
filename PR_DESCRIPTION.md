# Add missing failure-path coverage for lib/api/transactions.ts

Closes #1263

## Summary

Add a test file beside `lib/api/transactions.ts` that covers the success path, the invalid-input client-error paths, and the downstream-typed-error paths, without changing production behavior. The existing file had no tests, so the first person to find out about a regression was a user.

This PR is limited to tests. No production code is touched unless a test proves a behavior wrong. The existing suite is otherwise unchanged.

## Validation checks

- Relevant test target: `npm test -- --run lib/api/__tests__/transactions.test.ts`
- That command passes for the file under test.
- Full suite: unrelated suites may fail for pre-existing reasons; that is not a new regression from this file.

## What this adds

Success-path coverage:

- `getTransactions()` default params, pagination, clamping, and empty results
- `getTransactionsCursor()` keyset pagination walk, deletion resilience, invalid cursor fallback
- `getAccountSummary()` shape
- `getPaymentHistory()` shape
- AbortSignal cancellation behavior for `getTransactions()`
- Dev-only artificial delay behavior

Invalid-input client-error coverage:

- invalid `fromDate`/`toDate` filters are rejected with a typed `RangeError`
- malformed payloads are rejected by the parse validators with `ApiResponseValidationError`

Downstream-typed-error coverage:

- a malformed response from the parse validators surfaces `ApiResponseValidationError`
- cancellation surfaces `AbortError`
- failure paths are proven at the same boundary the functions actually call

## PR checklist

- [x] New tests added beside the file they cover
- [x] `npm test` passes for the changed test file
- [x] PR description references the issue and closes it
- [x] No production behavior was changed unless a test proved it wrong

## Scope note

This PR only addresses the missing test coverage requested by #1263. It does not change `lib/api/transactions.ts` or any other production file.
