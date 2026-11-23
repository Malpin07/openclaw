# PR #192879 — CLI and Gateway proof

[Read the recorded terminal output](terminal-output.txt). It is assembled from the original stdout and source-emitted files named in each section. JSON was not parsed or reserialized. Only local absolute paths, local fixture ports, and UUID-shaped identifiers were replaced; the source logs and receipts were left unchanged.

## Current candidate

- Commit: `7db7289ffa57b3580af3212cd0f0264175199819`
- Base: main `6963e4f4b632a4cd733e291d86b90021350e4ed5`
- Build: Node `v24.22.1`, pnpm `12.15.0`; full `pnpm build` exited 0 in **448.893 seconds**. Build metadata identifies the candidate commit.
- Focused tests: **282 passed** across four Vitest shards. Core and affected cron, Gateway, security, and UI test type checks passed.
- Completed end-to-end receipts: the CLI/Gateway candidate run made 44 calls; its separate escaped-space boundary run made 18 calls. Both report successful cleanup.

## Recorded behavior

The transcript contains the actual CLI create/edit results and Gateway stdout for these cases:

- `^ERROR ` and `^NOTICE ` retain their trailing spaces after CLI create/edit and Gateway reads.
- For `^ERROR `, the candidate run list is empty after `ERRORLESS`; after `ERROR ok` it records one successful run. The baseline's historical `ERRORLESS` run is shown separately.
- The unpadded control `^ERROR` records a successful `ERRORLESS` run, confirming the control behavior.
- For the leading-space pattern ` ERROR`, the source emits `ERROR ok` first and no run is recorded; after it emits ` ERROR ok`, the run list records success. The actual emitted lines are present in the transcript.
- The escaped trailing-space pattern (JSON string `"^ERROR\\ "`) is accepted, remains stored after Gateway restart, has an empty run list before positive input, and records a successful run after `ERROR ok`.
- Unsafe nested repetition, whitespace-only, and empty patterns are rejected.

The script payload is exactly `json({ state: { batch: trigger.streamBatch } });`; `toolsAllow` is empty and delivery mode is `none`.

## Historical baseline boundary

The baseline evidence is from commit `2c374b8cb52e5c25124a0afd46f66083f52f5430`, recorded November 4 with Node 26.12.1. It shows the old CLI trimming both `^ERROR ` and `^NOTICE ` and the old `^ERROR ` runtime firing on `ERRORLESS`. This is historical reproduction evidence; **it is not a test of the current main SHA** listed above. Do not combine its results with the new candidate validation.

## Receipt fingerprints

These hashes identify the original local evidence objects; the objects are not linked from this public folder.

| Evidence | SHA-256 |
|---|---|
| Candidate full-build receipt (`build.json`) | `658dd1847ae7b1a718bf5c63fe22e3e88f1c534c6a2bbff45805958304e06e98` |
| Candidate focused-test receipt (`tests.json`) | `cccc7eb2f95913665dfbf25127337e653cea7d124dfa507ed7655551042dc3ef` |
| Candidate CLI/Gateway receipt | `5defc75b4bf0ca7729f1309c932a2b6ce2f201430e9fbca38ab53f343100cd64` |
| Candidate escaped-space receipt | `a7cf2ffec3c1d10a22e36746c677a2920d0b6f7a8678ed0a7f01be2ffe7dbcb7` |
| Historical baseline receipt | `be7a450c0e3038bc5b5924f5ba68d488f9736f070ec4ea50e58b6fbf41d20ecb` |
| Historical baseline escaped-space receipt | `202465418b0c7cecdad15859aa0c5d6b64314864f9a685a31e81f72cc8cf6942` |
| Candidate build-info SHA-256 recorded by build receipt | `2181ef98b4f835908622fe2a631948151c6682f5d8d6f77c3cdb6fab3ff66f68` |

No token, account credential, or private user message is included. The test fixture records no provider credentials, an empty tool allowlist, and delivery mode `none`.
