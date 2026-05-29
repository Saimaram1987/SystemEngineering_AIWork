# SYS_SRS_4113 — Requirements Review
**Document:** SY_SRS_4113_DTS AutoStage Audio Messages V1.0.0.2  
**Reviewed by:** Principal Systems Engineering Lead  
**Review Date:** 2026-05-29  
**Artifacts Reviewed:** SYS_SRS_4113_requirements.md, ASAM_Scenarios.xlsx (Scenario 1, Scenario 2)

---

## EXECUTIVE SUMMARY

The requirements set covers the DTS AutoStage Audio Messages system with 60+ individual requirements spanning registration, privacy, campaign management, scheduling, playback, and impression reporting. While the overall structure is reasonable, **there are critical ambiguities, a duplicate requirement ID, a missing requirement, a serious double-negative typo, and significant gaps between what the test scenarios cover and what the requirements demand.** The two test scenarios submitted cover only the scheduling and frequency-capping subset of the requirements.

---

## PART 1 — REQUIREMENT CLARITY ASSESSMENT

### 1.1 Critical Defects (Must Fix Before Test Plan)

| ID | Issue | Severity |
|----|-------|----------|
| REQ-7 (geo-target) | **Duplicate ID** — There are two requirements labeled "REQ-7". One is under Ad Selection (no actual REQ-7 exists there) and one is under Geographical Restrictions about Last-Modified geo-targeting. Based on the surrounding sequence (REQ-66, REQ-71) the geo-target version should be **REQ-67**. This will cause traceability failures in any test management tool. | CRITICAL |
| REQ-71 | **Double-negative typo**: "when user consent has **not been not** granted" — this inverts the intended meaning. Should read "when user consent **has not been granted**." As written, coarse location applies when consent IS granted, which is the opposite of the intent. | CRITICAL |
| REQ-24 | **Missing requirement** — The sequence jumps from REQ-23 to REQ-25 with no REQ-24. Either it was intentionally removed (should be noted with a tombstone entry) or it was accidentally omitted. Either way, a gap in the requirement numbering must be formally accounted for. | HIGH |

---

### 1.2 Ambiguous Requirements (Untestable As Written)

#### REQ-01 — Compound Requirement
> "The DTS AutoStage Receiver shall download, store, play, and report impressions for Audio Messages."

**Problem:** This bundles four independent behaviors (download, store, play, report) into a single "shall." It is acceptable as a parent/summary requirement only if each verb is separately decomposed into child requirements — which they are (REQ-06 through REQ-75). The issue is this requirement as a standalone has no testable pass/fail criterion.

**Recommendation:** Mark REQ-01 explicitly as a "heading/summary requirement" and note that test coverage is provided by REQ-06 through REQ-75.

---

#### REQ-03 — "user signals (such as...)"
> "...provide user signals (such as a hashed email address or a hashed phone number for a **specified** country and/or region)..."

**Problems:**
- "such as" is open-ended — are other user signals permitted? If yes, what are they? If no, remove "such as" and say "specifically."
- "specified country and/or region" — who specifies this? The OEM? The API? The user? There is no reference to where the specification comes from.

**Recommendation:**
> Replace "such as" with "specifically." Add: "The country and/or region shall be determined by the vehicle's configured locale, as obtained from [reference config field]."

---

#### REQ-05 — "One or more... depending on applicable regulations"
> "...One or more of the following parameters (or a valid equivalent parameter) must be passed via the DTS AutoStage API, depending on the applicable regulations..."

**Problems:**
- "or a valid equivalent parameter" — completely undefined. Who determines validity? There is no reference to a registry or specification.
- There is no mapping of which parameter is required for which regulation (COPPA vs. GDPR vs. US Privacy). A test engineer cannot verify compliance without a decision matrix.
- Uses "must" instead of "shall" — inconsistent with the rest of the document (IEEE 830 / INCOSE convention).

**Recommendation:** Provide an explicit mapping table (e.g., "If operating in the EU, `gdpr` and `gdprConsent` shall be passed. If operating in the US, `usPrivacy` or `gppString`/`gppSid` shall be passed."). Remove "or a valid equivalent parameter" unless the equivalence criteria are defined in a referenced normative document.

---

#### REQ-06 — "Periodically"
> "The DTS AutoStage Receiver shall periodically request Audio Message campaigns..."

**Problem:** "Periodically" with no defined interval is unmeasurable. REQ-18 partially addresses this by saying "at least once per day" and delegating to `/v1/config`, but REQ-06 itself gives no guidance on what "periodic" means or where the period comes from.

**Recommendation:** Either remove REQ-06 as redundant (since REQ-18 is more specific) or update it to reference REQ-18's constraints directly.

---

#### REQ-07 — "Pre-launch setup phase"
> "...capable of downloading and caching Audio Messages at any time during the **pre-launch setup phase** of a campaign."

**Problem:** "Pre-launch setup phase" is not defined anywhere in the document. A test engineer has no way to know when this phase begins and ends.

**Recommendation:** Define "pre-launch setup phase" in the Definitions section or inline: "the period between receipt of campaign metadata and the campaign `startTime`."

---

#### REQ-18 — Polling Frequency Conflict
> "...at least once per day... The polling frequency shall be retrieved via the /v1/config endpoint using the **preloadAdsPref.pollInterval** field."

**Problem:** There is a potential conflict. If `preloadAdsPref.pollInterval` returns a value greater than 86400 seconds (once per day), which rule wins — the "at least once per day" floor or the server-configured interval? As written, the requirement is contradictory.

**Recommendation:** Clarify precedence: "The polling frequency shall be retrieved from `preloadAdsPref.pollInterval`. If the retrieved value exceeds 86400 seconds, the receiver shall default to a polling interval of 86400 seconds."

---

#### REQ-20 — "Either Etag or Last-Modified"
> "The DTS AutoStage Receiver shall use either the **Etag** header or the **Last-Modified** header to make conditional requests."

**Problem:** "Either...or" does not specify:
1. What happens if neither header is returned by the server?
2. Is there a preference for one over the other?
3. Can the receiver switch between the two between sessions?

**Recommendation:** Add: "If neither header is returned, the receiver shall make an unconditional request. If both headers are returned, the `Etag` header shall take precedence."

---

#### REQ-27 — "Most restrictive rule"
> "...The most restrictive rule shall be applied."

**Problem:** "Most restrictive" is undefined. Rules operate on different dimensions (max impressions per daypart, lifetime max impressions, minimum ad spacing). Across these dimensions, there is no universal ordering. For example:
- OEM says: max 5 ads/day, no daypart restriction
- Campaign says: max 2 ads in 06:00–12:00 daypart, max 10 lifetime

It is unclear how to compare OEM-level rules (which span all campaigns) to campaign-level rules (which are campaign-scoped). REQ-30 through REQ-39 define the individual rule types, but none of them define a precedence algorithm.

**Recommendation:** Provide a conflict-resolution algorithm or priority table, e.g.:
1. Apply OEM-level `maxImpressions` and `minAdSpacing` as global floors/ceilings.
2. Apply campaign-level `maxImpressions`, `capping`, and `minAdSpacing` within that envelope.
3. Apply ad-level `maxImpressions`, `capping`, and `minAdSpacing` within the campaign envelope.
At each level, the more restrictive numeric value applies.

---

#### REQ-28 — "Must" and "Specified Playback Moments"
> "The DTS AutoStage Receiver **must** have access to accurate local time of day at specified playback moments..."

**Problems:**
- Uses "must" (informal requirement) instead of "shall" (normative).
- "Specified playback moments" — who specifies them? Where is this defined? This appears to be a precondition, not a verifiable requirement.
- "Accurate" local time — what is the acceptable tolerance (e.g., ±1 second, ±1 minute)?

**Recommendation:** Rewrite as: "The DTS AutoStage Receiver **shall** maintain access to local time with an accuracy of [±X seconds], synchronized to [NTP/vehicle RTC/GPS], prior to ad selection at each playback opportunity."

---

#### REQ-40 — "Balances playback distribution"
> "...shall select them in a manner that **balances playback distribution** across the set of available ads and **minimizes consecutive repeats**."

**Problem:** Both "balances" and "minimizes" are qualitative terms with no measurable definition. A round-robin algorithm, a weighted random algorithm, or a least-recently-played algorithm all "balance" to some degree. Without a specification, any implementation can claim compliance.

**Recommendation:** Specify the algorithm type, e.g.: "shall use a round-robin or least-recently-played selection algorithm. Consecutive selection of the same ad is prohibited when more than one eligible ad exists."

---

#### REQ-51 — "User presence (e.g., a door opening event)"
> "...shall be tied to user presence (e.g., a door opening event)..."

**Problem:** "e.g." opens this to any implementation-defined event. The test engineer cannot write a deterministic test case without a defined set of accepted user presence signals.

**Recommendation:** Replace "e.g." with "specifically" and enumerate the acceptable signals, or reference a vehicle integration specification that defines the user presence event interface.

---

#### REQ-55 & REQ-50 — "Playback Moment" Not Defined
Both requirements reference "playback moments" or "self-launch" without ever defining what constitutes a playback moment (ignition on? source change? time-based? event-driven?).

**Recommendation:** Define "playback moment" in the Definitions section and reference it consistently across REQ-26, REQ-50, REQ-51, REQ-55.

---

#### REQ-62 — "Within 30 minutes of playing"
> "Within 30 minutes of playing an Audio Message, the DTS AutoStage Receiver shall use its current position to periodically determine whether the vehicle has stopped for at least **minDwell** seconds within any of the returned H3 cells..."

**Problems:**
- Is the 30-minute window measured from the **start** or **end** of playback?
- "Periodically" — what is the polling period for position checks? If GPS is polled every 5 minutes vs. every 1 minute, the dwell detection accuracy changes significantly.
- What happens if the vehicle powers off within the 30-minute window?

**Recommendation:** Define: start time of window, position polling interval, and behavior on power-off during the monitoring window.

---

#### REQ-64 — Post-Campaign Reporting Trigger
> "After a campaign has been terminated..."

**Problem:** "Terminated" is ambiguous — does it mean:
1. Natural expiry (campaign `endTime` has passed)?
2. Administrative removal by the server?
3. Campaign deleted from the receiver's cache (per REQ-25)?

If it means all three, the reporting behavior should be the same for all cases.

**Recommendation:** Clarify: "After a campaign's `endTime` has passed, or the campaign has been removed from the server, whichever occurs first, the DTS AutoStage Receiver shall report..."

---

#### REQ-77 — "OEM-defined minimum volumes"
> "...shall respect OEM-defined minimum volumes..."

**Problem:** There is no specification for how OEM minimum volumes are communicated to the receiver. Is this a static compile-time configuration, a `/v1/config` field, or a vehicle-level API?

**Recommendation:** Specify the source: "The minimum volume level shall be obtained from the `[field name]` parameter in the `/v1/config` endpoint response / [vehicle audio API reference]."

---

#### REQ-78 — "Other Technical Considerations"
> "...based on the amount of available storage, bandwidth, or **other technical considerations**."

**Problem:** "Other technical considerations" is a catch-all that makes this requirement untestable. Any behavior can be justified by claiming an "other technical consideration."

**Recommendation:** Either enumerate the specific considerations or remove the phrase and restrict the requirement to explicitly named factors (storage, bandwidth).

---

## PART 2 — INTERPRETATION FOR TEST PLANNING

### 2.1 How to Structure the Test Plan

The requirements naturally group into the following **test domains**. Each domain maps to a set of testable behaviors:

| Test Domain | Key Requirements | Test Type |
|-------------|-----------------|-----------|
| **Registration & Device Identity** | REQ-02 | Integration |
| **Privacy & Consent Signaling** | REQ-03, REQ-04, REQ-05 | API/Integration |
| **Campaign Preload & Polling** | REQ-06, REQ-07, REQ-08, REQ-18, REQ-19 | Integration |
| **Campaign Restrictions** | REQ-09, REQ-10, REQ-11 | Integration |
| **Geo-targeting** | REQ-12 through REQ-17, REQ-66, REQ-67, REQ-71 | Integration/Simulation |
| **Conditional Requests (ETag/Last-Modified)** | REQ-20, REQ-68, REQ-69 | API/Integration |
| **Storage** | REQ-21, REQ-22, REQ-23, REQ-25, REQ-78 | System |
| **Ad Selection Algorithm** | REQ-27, REQ-29, REQ-40, REQ-49 | Unit/Integration |
| **Frequency Capping** | REQ-28, REQ-30–REQ-39 | Integration |
| **Data Tracking** | REQ-41 through REQ-48 | Integration |
| **Playback Behavior** | REQ-50, REQ-52, REQ-53, REQ-55, REQ-56, REQ-57, REQ-77 | System/Integration |
| **Vehicle Startup** | REQ-51, REQ-54, REQ-70 | System |
| **Impression Reporting** | REQ-58, REQ-59, REQ-60, REQ-72–REQ-76 | Integration |
| **Points of Interest** | REQ-61, REQ-62, REQ-63 | Integration/Simulation |
| **Post-Campaign Analytics** | REQ-64 | Integration |

### 2.2 Key Test Interpretation Notes per Requirement

**REQ-30 (OEM daypart capping):**
A value of `maxImpressions: 0` in the OEM capping config is not explicitly addressed at the OEM level (only at campaign level per REQ-34). The test plan must include a case where `maxImpressions = 0` is set at OEM level and verify that zero ads play in that daypart. This is exercised in Scenario 2 (06:00–12:00) but there is no explicit requirement backing the behavior for OEM-level zero.

**REQ-34 (Campaign daypart capping):**
"`A value of 0 indicates that no ads are allowed in that daypart`" — this is the only place where the zero-value semantics are defined, and it is only stated at the campaign level. Need a corresponding statement at the OEM level (REQ-30) and ad level (REQ-37).

**REQ-35, REQ-36, REQ-38, REQ-39 ("`If a value is not specified, there shall be no limit`"):**
The test plan must explicitly include cases where these fields are absent from the API response, verifying that playback is truly unconstrained.

**REQ-41/REQ-43/REQ-46 (Daily impression reset at midnight):**
Test must verify reset behavior at local midnight, not UTC midnight. This requires a time-zone-aware test harness.

**REQ-59/REQ-60 (Offline impression batching):**
Test must simulate: (a) ad plays with no connectivity → impression stored locally; (b) connectivity restored → batch submitted; (c) verify order of submission matches playback chronological order.

---

## PART 3 — TEST SCENARIO REVIEW (ASAM_Scenarios.xlsx)

### 3.1 Defects Found in the Excel Test Scenarios

| Location | Defect | Impact |
|----------|--------|--------|
| Sheet 2, C2_2 `endTime` | **Typo: `"2026-07-014T23:59:59Z"`** — the date `014` is invalid. Should be `"2026-07-14T23:59:59Z"`. | The test server/simulator will likely reject or misparse this campaign. Campaign C2_2 may not activate, making the test invalid. |
| Sheet 2, C2_3 `periodEnd` | **`"24:00"`** — ISO 8601 does not support 24:00 as a time value in all parsers. Some implementations treat it as invalid; others accept it as equivalent to 00:00 of the next day. | May produce inconsistent results across implementations. Recommend using `"23:59"` or explicitly stating whether 24:00 is supported by the API. |
| Sheet 2, OEM Capping `maxImpressions: 0` (06:00–12:00) | No requirement explicitly defines the behavior of `maxImpressions: 0` at **OEM level** (only campaign level per REQ-34). | The expected outcome for this test case is not formally backed by a requirement. The test cannot have a normative pass/fail criterion. |

---

### 3.2 Coverage Analysis — What the Two Scenarios Test vs. Do Not Test

**COVERED (Scheduling & Frequency Capping Only):**
- REQ-26: Play only during specified playout period
- REQ-30: OEM daypart capping (`maxImpressions`)
- REQ-31: OEM `minAdSpacing`
- REQ-32: Campaign `startTime`/`endTime`
- REQ-33: Campaign frequency capping
- REQ-34: Campaign daypart `capping.maxImpressions`
- REQ-35: Campaign `minAdSpacing`
- REQ-36: Campaign lifetime `maxImpressions`
- REQ-37: Ad-level daypart capping
- REQ-39: Ad-level lifetime `maxImpressions`

**NOT COVERED — Significant Gaps:**

| Gap Area | Requirements Not Covered |
|----------|--------------------------|
| Registration & device identity | REQ-02 |
| Privacy & consent signaling | REQ-03, REQ-04, REQ-05 |
| Campaign downloading/caching | REQ-07, REQ-08, REQ-18, REQ-19 |
| Conditional HTTP requests | REQ-20, REQ-68, REQ-69 |
| Brand/model/language targeting | REQ-09, REQ-10 |
| Geographic restrictions | REQ-11 through REQ-17, REQ-66, REQ-67, REQ-71 |
| Storage limits & cleanup | REQ-21, REQ-22, REQ-23, REQ-25, REQ-78 |
| Ad selection algorithm (distribution) | REQ-40 |
| Impression tracking (data) | REQ-41 through REQ-48 |
| Playback behavior (display, volume, muting) | REQ-52, REQ-53, REQ-55, REQ-56, REQ-57, REQ-77 |
| Vehicle startup integration | REQ-51, REQ-54, REQ-70 |
| Impression reporting (API) | REQ-58, REQ-59, REQ-60 |
| Error reporting (API) | REQ-72, REQ-73, REQ-74, REQ-75, REQ-76 |
| Points of Interest | REQ-61, REQ-62, REQ-63 |
| Post-campaign analytics | REQ-64 |
| Certification test setup | REQ-65 |

The two scenarios cover roughly **10 out of 60+ verifiable requirements (~17%).**

---

### 3.3 Scenario Logic Observations

**Scenario 1:**
- Campaigns C1_1, C1_2, C1_3, and C1_4 run concurrently on days July 2–4 — this is a valid multi-campaign overlap test per REQ-08.
- C1_1 has both campaign-level daypart caps (max 1 per 06:00–12:00, max 1 per 16:00–20:00) AND a lifetime cap of 10, AND an ad-level cap of 5. This is a good layered test for REQ-27 (most restrictive), but only if the expected outcome is explicitly documented in the scenario (it is not — there are no expected results in the Excel).
- **The scenario has no expected outcome column.** A test scenario without expected results is not testable. The reviewer cannot determine pass/fail without knowing what the correct behavior should be.

**Scenario 2:**
- OEM capping blocks all ads 06:00–12:00. C2_2's capping window is 05:00–15:00 (which overlaps the OEM block). Per REQ-27 ("most restrictive"), zero ads should play from 06:00–12:00 despite C2_2 allowing up to 2. This is a good edge case but again has no documented expected outcome.
- C2_4 has `maxImpressions: 0` during 18:00–22:00 — per REQ-34, no ads should play, but C2_4 has an ad-level lifetime cap of 13, creating an apparent conflict. The expected result must be specified.

---

## PART 4 — SUMMARY OF ACTIONS REQUIRED

### For the Requirements Author (SRS Owner):

| Priority | Action |
|----------|--------|
| P0 | Fix REQ-71 double-negative typo immediately — this inverts the privacy behavior |
| P0 | Resolve duplicate REQ-7 — rename geo-target version to REQ-67 |
| P1 | Account for missing REQ-24 (tombstone or restore) |
| P1 | Define "playback moment" in Definitions section; referenced by REQ-26, REQ-50, REQ-51, REQ-55 |
| P1 | Add OEM-level zero-impression semantics to REQ-30 (parallel to REQ-34's explicit statement) |
| P1 | Resolve polling frequency conflict in REQ-18 (floor vs. server-configured value) |
| P1 | Disambiguate REQ-27 "most restrictive" with a precedence algorithm |
| P2 | Remove "e.g." from REQ-51 and enumerate accepted user presence signals |
| P2 | Specify "balanced distribution" algorithm in REQ-40 |
| P2 | Define source of OEM minimum volume in REQ-77 |
| P2 | Add tolerance specification to REQ-28 time accuracy |
| P2 | Remove "or a valid equivalent parameter" from REQ-05 or define equivalence criteria |
| P3 | Replace all "must" with "shall" (REQ-05, REQ-28, REQ-59) for RFC 2119/INCOSE consistency |

### For the Test Scenario Author (Staff Engineer):

| Priority | Action |
|----------|--------|
| P0 | Fix date typo in C2_2 `endTime`: `"2026-07-014T"` → `"2026-07-14T"` |
| P0 | Add an **Expected Outcome** column to all scenarios — scenarios without expected results are not test cases |
| P1 | Clarify whether `"24:00"` is a supported periodEnd value (C2_3); if not, change to `"23:59"` |
| P1 | Add scenarios covering impression reporting (REQ-58–REQ-60) and error reporting (REQ-72–REQ-76) |
| P1 | Add offline/connectivity-failure scenario (REQ-59, REQ-60) |
| P1 | Add geo-targeting scenarios (REQ-11–REQ-17, REQ-71) |
| P2 | Add vehicle startup scenario (REQ-51, REQ-54, REQ-70) |
| P2 | Add multi-user / no-user-login scenario (REQ-53) |
| P2 | Add campaign termination and cleanup scenario (REQ-25, REQ-57) |
| P2 | Document the OEM-level `maxImpressions: 0` expected behavior explicitly before executing Scenario 2 |
