# SystemEngineering_AIWork

Automation assets, agent prompts, and QA workflows for system engineering and validation tasks.

## Overview

This repository contains agent-driven test specifications and supporting documentation for validating Android-based infotainment and audio-message workflows. The current focus is on automated QA for the DTS AutoStage Audio Messages app, including:

- installation and service validation
- consent and AAID verification
- campaign download and storage checks
- geo-targeting and eligibility evaluation
- playback and impression reporting validation
- negative and edge-case testing
- emulator-based time-travel tests for expiry and reset behavior

## Repository Purpose

The goal of this repo is to keep repeatable, auditable test instructions in version control so they can be:

- reviewed and improved over time
- executed consistently by humans or agents
- mapped back to system requirements
- used to generate structured QA evidence and release-readiness reports

## Current Contents

### Agent specifications
- `audio-messages-tester.agent.md`  
  Autonomous QA agent instructions for testing the DTS AutoStage Audio Messages flow on:
  - a physical Android device for Suites A–K
  - an Android Automotive emulator for Suite L

### Documentation
- `README.md`  
  Repository overview, usage guidance, and maintenance notes.

## Audio Messages QA Agent

The `audio-messages-tester.agent.md` file defines a full validation workflow for the Audio Messages app package:

- **Package:** `com.dts.autostage.audiomsgapp`
- **Primary service:** `com.dts.autostage.audiomsgapp.service.AudioMessagesService`
- **SDK service:** `com.dts.autostage.sdk.service.AutoStageSDKService`
- **Activity:** `com.dts.autostage.audiomsgapp.activity.AdActivity`

### Test coverage

The agent currently covers the following suites:

- **Suite A** — Installation & Registration
- **Suite B** — User Consent & AAID
- **Suite C** — Campaign Download & Storage
- **Suite D** — Campaign Restrictions
- **Suite E** — Geo-Targeting
- **Suite F** — Campaign Updates & Conditional Requests
- **Suite G** — Ad Selection & Scheduling
- **Suite H** — Playback
- **Suite I** — Impressions & Reporting
- **Suite J** — POI Tracking
- **Suite K** — Negative & Edge Cases
- **Suite L** — Time Travel / Campaign Expiry / Midnight Reset (emulator only)

## Prerequisites

Before using the QA agent, make sure the following are available:

### Local tools
- `adb`
- `python3`
- `sqlite3`
- Android Emulator tooling

### Devices
- **Physical Android device** connected through ADB for Suites A–K
- **Android Automotive emulator** named `Automotive_Ultrawide` for Suite L

### Environment assumptions
- The physical device has the DTS AutoStage packages installed
- The emulator supports `adb root`
- The emulator contains pre-seeded campaign data in the expected database path
- Network and log access are available for validation

## Safety Constraints

The audio testing workflow contains important device-safety rules.

### Physical device rules
The physical device is used for observation only. Do **not** run destructive commands against it.

Forbidden on the physical device:
- rebooting the device
- deleting files
- force-stopping the app
- clearing app data
- modifying system time

### Emulator rules
The emulator is the only allowed target for:
- clock manipulation
- `adb root`
- `am force-stop`
- network blocking for controlled tests
- database and preference manipulation for Suite L

## Suggested Repo Structure

As this repository grows, a cleaner structure would be:

```text
.
├── README.md
├── agents/
│   └── audio-messages-tester.agent.md
├── templates/
│   ├── audio_messages_report.py
│   └── audio_messages_report.html
├── reports/
│   └── .gitkeep
└── docs/
    └── requirements-traceability.md
```

## Recommended Improvements

This repo will be easier to maintain if the current agent file is split into smaller responsibilities:

1. **Keep agent instructions focused on execution**
   - device discovery
   - validation steps
   - evidence collection
   - result evaluation

2. **Move report generation into standalone files**
   - Python script for transforming results into HTML
   - HTML template for presentation

3. **Store results in a structured format**
   - JSON or YAML output
   - explicit statuses such as:
     - `PASS`
     - `FAIL`
     - `WARN`
     - `SKIPPED`
     - `BLOCKED`
     - `ERROR`

4. **Reduce duplication**
   - define helper procedures once
   - centralize evidence and verdict rules
   - reuse a common test-result schema

## How to Use

### Option 1: Review and improve the agent spec
Open and refine:

- `audio-messages-tester.agent.md`

Focus on:
- deterministic execution steps
- stricter pass/fail criteria
- reusable helper logic
- smaller report-generation components

### Option 2: Run the workflow through an agent
Invoke the Audio Messages QA Agent with a target scope such as:

- `full suite`
- `campaign download`
- `playback`
- `impressions`
- `geo-targeting`
- `REQ-06`

## Output Expectations

A successful QA run should produce:

- device and environment metadata
- suite-by-suite results
- exact evidence from commands and logcat
- a final verdict such as:
  - `READY FOR RELEASE`
  - `NOT READY`
- an HTML report saved to a known output path

## Maintenance Notes

When updating test specs in this repo:

- prefer explicit, machine-checkable criteria
- separate execution logic from presentation logic
- keep safety rules near the top of the file
- avoid embedding huge generated templates inside agent instructions
- keep requirement IDs traceable to individual tests

## Future Enhancements

Potential next steps for this repository:

- move agent files into an `agents/` directory
- add a reusable report template system
- add sample result JSON files
- add requirement traceability documentation
- add a changelog for QA workflow revisions
- add repo-level conventions for agent prompt design

## License

Add a license file if this repository is intended for reuse or collaboration.
