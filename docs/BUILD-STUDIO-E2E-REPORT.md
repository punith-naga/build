# /build studio - e2e findings (2026-10-08)

Run against **production** (vegaduta.ai/build), signed in by the owner. Manual, browser-driven; no automated suite yet.

## Tested
| Scenario | Result |
|---|---|
| Studio loads (desktop) | OK - screenshot 01 |
| One-shot "4-page CRM with pipeline + workflow page" | **FAIL** - `Cloud model unavailable (HTTP 500)` (02) |
| Tiny edit "change the title" | OK, 3 s, cloud (Vertex) |
| Same CRM request as an edit | **FALSE SUCCESS** - "1 change applied"; only `<title>` became "Sales CRM"; no Dashboard/Leads/Pipeline/Workflow |
| Compact "Builds with" row at 1440 px | **FAIL** - chip text runs under the model selector (03) |
| Studio at phone width (~435 px) | **FAIL** - change box and Send button clipped at right edge (04) |

## Root cause of the missing multi-page apps
Cloud edits reply ~1k tokens, so a multi-screen app cannot land in one edit. The studio accepted any applied block as success.

## Fixes (VegaDuta `main`)
- `01cb70e4` - engine badge: cost text truncates inside the chip, row wraps.
- `acb88a1f` - cloud leg plans multi-screen ideas (max 6 steps) and builds one screen per step, chaining each edit on the last (`web/app/build/studio/buildPlan.ts`, 6 unit tests).

## Not verified
Neither fix is deployed, so nothing above has been re-checked in production. The HTTP 500 on the large request is a core-side error (`/api/assist/code-edit`) not diagnosed here (outside `web/` scope). Phone-width clipping is not yet fixed. Not yet tested: pipeline/workflow wiring, other app types (game, camera, auth, data), publish flow.
