# ENGINE-FIXES-1 report (2026-10-24 UTC)

A candidate, not a release. Branch `pitrelay-v11-fixes` on the v11 source handoff (commit `572e1dc`, filtered export), done with Claude at the owner's request. Every behaviour below was reproduced on the v11 engine first, has tests that fail on the unchanged code and pass with the change, and was checked by an independent review.

## What changed, as callers see it

| Area | v11 | Candidate |
|---|---|---|
| `rev_native` on REVLOG 2.x (REVLib after 2027.0.0-alpha-8) | `partial`, "Unknown REV native record ID 3/6", no channels | Decoded. SPARK status fields as `bus_<n>/device_type_<t>/can_<id>/<field>` at uint64 µs (frames: base + uint16 offset); `robot_state/enabled`, `robot_state/mode_code`; `match_info/*` plus metadata; logger events in `log.messages`; `bus_<n>/logger/dropped_frames`; buses and firmware in bounded metadata. Unknown content is an issue, malformed records `corrupt` (fields that decoded are kept). |
| `rev_native` on other headered versions (1.x, 3.x, ...) | `partial` | `unsupported REV_VERSION`; an incomplete header `error REV_HEADER` |
| A `.dslog` read with `dsevents_v4` (and the reverse) | `partial` with corrupt/truncated issues (reads as damage) | `not_applicable DS_DSLOG_CONTENT` / `DS_DSEVENTS_CONTENT` naming the right adapter, only on clear structural evidence; otherwise decoded as before |
| All-zero DS records (unwritten tail) | Decoded as 0 V cycles with every flag set, or epoch events; `complete` | `truncated_tail` (zeros to the end) or `corrupt` (zeros then data); decoding stops there |
| `artifact.upload_begin` | Transfer id only as `id` | Also `transfer_id` |
| Upload begin/chunk/abort with `workflow_id` | One workflow call, two events and a receipt per 48 KiB chunk; abort refused by a stopped or exhausted workflow | Not workflow calls (`result.workflow.admission` `not_admitted`); `upload_finish` is the one recorded call; abort never refused for workflow state; a malformed (begin/chunk: unknown) workflow ID still refused |
| `log.import_csv` `time_unit` | `unsupported` for typos and real units alike | Real time units, in any common spelling, `unsupported` naming the unit; anything else `error`. Code stays `CSV_TIME_UNIT` |

Help text (`log.inspect`, `log.entries`), CONTRACTS.md (artifact identity; staging), DECISIONS.md D064/D065, FOUNDATION_API.md, TOOLKIT.md, the CHANGELOG and three shipped skill references describe the changes. No tool, argument or existing result field was renamed or removed; the workflow contract stays `2`.

## Design notes

* **REVLOG 2.x** follows the record layouts of REV's BSD-3 `@rev-robotics/revlog-converter` 2.0.0, read as a reference; no converter code is included (provenance in `resources/derivation/rev/README.md`). Its SPARK DBC is byte-identical to the one the engine's fields already come from. Deliberate differences from the converter: no zero padding of short frames, no clamping to DBC bounds, exact API-class matching, no `0.0.0` firmware versions, unknown content reported. As in the converter, header bits 4-7 and CAN ID bit 31 carry no meaning (both counted in metadata); frames with ID bits 29-30 set are not decoded.
* **DS recognition** looks at the first records only. The two-day window recognizes event timestamps and is never applied to decoded times. Cycles whose first four bytes are zero (no communication yet) are no dslog evidence, because every event record starts with four zero bytes.
* **Upload staging** gives an upload the cost of `artifact.register` (one call per file); budgets bound investigation effort, and transfers keep their own limits (64 MiB, four open transfers).

## Verification

* Engine suite: 2614 run, 3 failed, 91 skipped; the unchanged source on the same host: 2595 run, the same 3 failed (WPILib natives unavailable), 91 skipped. 19 new tests.
* `validate_runtime_skills.py` PASS; `test_generate_rev_dbc.py` 18 passed; `test_package_local.py` 52 passed, 5 failed, the same 5 Windows-path tests as on the unchanged source.
* Review: one reviewer per area (DS, workflow, REVLOG, the rest) and two skeptics per finding. All confirmed findings were fixed in the branch, with tests (commit "Review fixes ..." and the following one).
* A blind REVLOG 2.x decoder, written only from the converter source by a separate agent, matched the engine on 318,728 values and timestamps, 5,371 channel names, 1,523 events and all metadata across 45 synthetic files; the remaining differences are the deliberate ones above.
* Package `pitrelay-0.1.0-dev-20261024-engine-fixes-1` (35 files) built and verified by `scripts/package_local.py`; prepared as a Claude Code plugin, it passed that plugin's end-to-end checks, with the REVLOG 2.0 file inspected `ok`.

## Not run or unverified

* Comparison with REV's own converter output (running the downloaded converter was not permitted on the build host).
* Real REVLib 2.x, Driver Station or REV logs: synthetic only. Whether REVLib writes API class 46 in 2.x frame IDs, and the 2.x clock origin, are not established.
* The build used OpenJDK 21 with `--release 17` and WPILib classes taken from the v11 jar (no access to frcmaven.wpi.edu): rebuild with the reference JDK 17 and full dependencies before any release. HTTP tests, Windows, macOS, client acceptance and physical validation were not run.
* STATIC_CHECKS.json, DEPENDENCY_LICENSE_EVIDENCE.json and PITRELAY_LICENSE_SCOPE.json still describe the v11 jar.

## Found but not changed

* v1 `rev_native`: REVLib 2027.0.0-alpha-1..8 v1 files store truncated nanoseconds, not milliseconds (per the converter's statistics tool); the engine labels them milliseconds. ServoHub firmware versions in v1 use the SPARK byte order, which converter 2.0.0 changed.
* `nonfinite_observation` issues give a record ordinal as `offset`, not a byte offset (pre-existing, all native adapters).
* A DS dslog read with `dsevents_v4` that has fewer than three cycles with communication still decodes as before (too little evidence to refuse).
