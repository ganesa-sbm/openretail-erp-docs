# TASK-039 — Peripheral adapters: scanner, scale, drawer, customer display

| Field | Value |
| --- | --- |
| Phase | 1 |
| Module(s) | `erp.client.fx` |
| Traces to | FR-260..FR-264, BR-034 |
| Depends on | TASK-032 |
| Size | M |
| Primary role | UI engineer |

## Goal

Talk to the hardware on a real counter, tolerantly.

## Scope

**In:** HID and serial scanner handling, serial/USB weighing-scale protocols, drawer kick via printer or direct, pole/customer display, device configuration UI with a test button, `PeripheralDriver` SPI.

**Out:** Vendor-specific exotic devices (plugins).

## Implementation notes

- Devices are abstracted behind the SPI so a plugin can add a protocol without touching the client.
- Scanner input must not be confused with keyboard typing: use prefix/suffix framing and timing heuristics, configurable per device.
- A missing or failed device degrades to manual entry with a clear indicator — never a crash or a modal that traps the cashier.
- Each device has a self-test in settings that a support engineer can ask a shopkeeper to run by phone.

## Acceptance criteria

1. Scanning during manual quantity entry does not corrupt the entry (timing/framing test).
2. Scale reading is captured with the correct decimals and unit; an unstable reading is rejected.
3. Unplugging any device mid-session leaves billing possible and shows the device as offline.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
