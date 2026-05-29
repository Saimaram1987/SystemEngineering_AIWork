# SystemEngineering_AIWork

Automation assets, agent prompts, and supporting documentation for system engineering, validation, and QA workflows.

## Overview

This repository contains reusable prompt-driven assets for automotive infotainment validation and related system-engineering activities. It currently includes two agent specifications with different purposes:

- a **Welcome Ads project-context assistant** for planning, documentation, requirement interpretation, and test-design support
- an **Audio Messages QA agent** for execution-focused validation of DTS AutoStage Audio Messages behavior on Android devices and emulator environments

The repository is intended to keep reusable engineering instructions in version control so they can be:

- reviewed and improved over time
- reused consistently by humans or agents
- aligned to system requirements and validation goals
- extended into structured deliverables such as test evidence, reports, and traceability artifacts

## Repository Purpose

The goal of this repo is to centralize agent-based engineering assets that support:

- test strategy development
- requirement-driven validation
- QA workflow standardization
- reusable prompt and agent design
- documentation and evidence generation

These assets are meant to help teams work in a repeatable, auditable, and maintainable way across different validation scenarios.

## Current Contents

### Agent and prompt assets
- `.github/agents/requirements-reviewer.agent.md`
  Staff/Principal-level requirements review agent. Given any SRS markdown file and optional test scenario Excel files, it produces a structured review covering requirement clarity, ambiguity analysis, test plan interpretation, coverage gap analysis, and a prioritized action list. See **[Quick Start — Requirements Reviewer](#quick-start--requirements-reviewer)** below.

- `DTS_AutoStage_WelcomeAds_Assistant.prompt.md`  
  Project-context assistant focused on DTS AutoStage Welcome Ads test strategy, requirement interpretation, campaign planning, workbook support, and structured engineering deliverables.

- `audio-messages-tester.agent.md`  
  Execution-focused QA agent for validating DTS AutoStage Audio Messages / Welcome Ads behavior using ADB, logcat, dumpsys, sqlite, and emulator-based time-travel testing.

### Requirements artifacts (SYS_SRS_4113)
- `SYS_SRS_4113_requirements.md` — Requirements extracted from the SRS source document
- `SYS_SRS_4113_Review.md` — Full requirements review produced by the Requirements Reviewer agent
- `ASAM_Scenarios.xlsx` — Test scenarios authored by the Staff Engineer
- `SY_SRS_4113_DTS AutoStage Audio Messages_V1.0.0.2.docx` — Original SRS source document

### Documentation
- `README.md`  
  Repository overview, usage guidance, and maintenance notes.

## Quick Start — Requirements Reviewer

This is the fastest way for a colleague to get a Principal-level requirements review on any SRS document — no prompting experience needed.

### Prerequisites

| Requirement | Version / Notes |
|-------------|----------------|
| [VS Code](https://code.visualstudio.com/) | Latest stable |
| [GitHub Copilot extension](https://marketplace.visualstudio.com/items?itemName=GitHub.copilot) | Must be signed in with a Copilot-enabled account |
| [GitHub Copilot Chat extension](https://marketplace.visualstudio.com/items?itemName=GitHub.copilot-chat) | Required for agent mode |
| Python 3 | Any version; used to parse `.xlsx` test scenario files |

### Step 1 — Clone the repository

```bash
git clone https://github.com/Saimaram1987/SystemEngineering_AIWork.git
cd SystemEngineering_AIWork
```

Then open the folder in VS Code:

```bash
code .
```

### Step 2 — Open Copilot Chat in Agent Mode

1. Press `Ctrl+Shift+I` (macOS: `Cmd+Shift+I`) to open the Copilot Chat panel.
2. At the top of the chat panel, click the **agent/model picker** (shows the current model name or a `@` symbol).
3. Select **"Requirements Reviewer"** from the list.

> If you don't see "Requirements Reviewer", check that both Copilot and Copilot Chat extensions are installed and that you have opened this repo folder (not a parent folder). The agent is defined in `.github/agents/requirements-reviewer.agent.md` and VS Code picks it up automatically.

### Step 3 — Run a review

**Option A — Auto-discover (easiest):**
Type this in the chat box and press Enter:
```
review
```
The agent will automatically find all `*requirements*.md` and `*.xlsx` files in the workspace and begin the review.

**Option B — Target a specific file:**
```
review SYS_SRS_4113_requirements.md
```

**Option C — Review any SRS you bring:**
1. Drop your own requirements `.md` file and/or test scenario `.xlsx` into the workspace folder.
2. Type `review <your-filename>.md` in chat.

### Step 4 — Read the output

The agent writes a `_Review.md` file next to your source document (e.g. `SYS_SRS_4113_Review.md`). Open it in VS Code's Markdown Preview (`Cmd+Shift+V` / `Ctrl+Shift+V`) for a formatted view.

The review contains five sections:

| Section | What it tells you |
|---------|-------------------|
| **1. Structural Defects** | Duplicate IDs, missing IDs, typos that invert meaning, compound requirements |
| **2. Ambiguity Analysis** | Every "shall" that cannot be tested as written, with suggested rewrites |
| **3. Test Plan Interpretation** | Requirement grouped into test domains, with concrete test cases and boundary conditions |
| **4. Coverage Gap Analysis** | Which requirements are NOT covered by the provided test scenarios, expressed as % |
| **5. Prioritized Action List** | P0–P3 actions for both the requirements author and the test scenario author |

### Troubleshooting

| Symptom | Fix |
|---------|-----|
| "Requirements Reviewer" not in agent picker | Make sure you opened the repo root folder (`SystemEngineering_AIWork`), not a subfolder |
| Agent can't parse `.xlsx` | It will auto-install `openpyxl`. Requires Python 3 on your `$PATH` |
| Push/auth errors when committing back | Use a [GitHub Personal Access Token](https://github.com/settings/tokens) — GitHub no longer accepts passwords over HTTPS |
| Review file not created | Check the Copilot Chat output for errors; ensure you have write permission to the workspace folder |

---

## Agent Summary

### 1. Welcome Ads Assistant

The Welcome Ads assistant is designed to help with engineering and documentation tasks such as:

- interpreting SY_SRS_4113 requirements
- drafting or refining test cases
- supporting campaign and scenario design
- helping structure strategy documents and timeline workbooks
- preserving project-specific architecture context and terminology

This asset is most useful when the task is analytical, documentation-heavy, or planning-oriented.

### 2. Audio Messages QA Agent

The Audio Messages QA agent is designed for execution-oriented validation activities such as:

- discovering and routing between physical and emulator Android targets
- validating installation, registration, and consent behavior
- checking campaign download, storage, restrictions, and geo-targeting
- verifying playback, impression reporting, and negative scenarios
- running emulator-only time-travel and expiry/reset tests
- producing structured QA evidence and report-ready outputs

This asset is most useful when the task is operational, test-execution-focused, and evidence-driven.

## Typical Use Cases

You can use this repository for activities such as:

- building or refining infotainment QA workflows
- generating requirement-linked test cases
- preparing campaign-validation strategies
- validating AutoStage audio-message behavior on target devices
- standardizing agent instructions for repeatable engineering tasks
- creating inputs for reports, traceability, and release-readiness reviews

## Prerequisites

Prerequisites depend on which asset is being used.

### For documentation and planning tasks
- access to relevant system requirements and supporting project documents
- understanding of the target architecture and test objectives

### For execution-based Android QA workflows
- `adb`
- `python3`
- `sqlite3`
- Android Emulator tooling
- a connected physical Android device where applicable
- an Android Automotive emulator where emulator-only scenarios are required

## Safety and Usage Notes

Some assets in this repository include environment-specific safety constraints.

Examples include:

- separating physical-device actions from emulator-only actions
- avoiding destructive operations on production-like devices
- keeping time manipulation and root-required actions limited to approved emulator environments

When updating or extending agent files, keep these operational constraints explicit and easy to find.

## Suggested Repo Structure

As the repository grows, a cleaner structure could be:

```text
.
├── README.md
├── agents/
│   └── audio-messages-tester.agent.md
├── prompts/
│   └── DTS_AutoStage_WelcomeAds_Assistant.prompt.md
├── templates/
│   ├── report_template.py
│   └── report_template.html
├── reports/
│   └── .gitkeep
└── docs/
    ├── requirements-traceability.md
    └── conventions.md
```

## Recommended Improvements

This repository will be easier to maintain if it evolves toward clearer separation of concerns:

1. **Group assets by type**
   - keep execution agents under `agents/`
   - keep planning/context prompts under `prompts/`

2. **Separate logic from presentation**
   - move report generation into standalone scripts and templates
   - keep long generated output out of core agent instructions

3. **Standardize output formats**
   - use JSON or YAML for structured run results
   - use explicit statuses such as:
     - `PASS`
     - `FAIL`
     - `WARN`
     - `SKIPPED`
     - `BLOCKED`
     - `ERROR`

4. **Reduce duplication across assets**
   - reuse common terminology
   - centralize shared validation patterns
   - define reusable evidence and verdict conventions

5. **Improve traceability**
   - map tests and outputs back to requirement IDs
   - document assumptions, environment dependencies, and known limitations

## How to Use

### Option 1: Use the Welcome Ads assistant
Open and use:

- `DTS_AutoStage_WelcomeAds_Assistant.prompt.md`

Use it for:
- project-context guidance
- requirement interpretation
- test-case drafting
- workbook and strategy support
- campaign/test design discussions

### Option 2: Use the Audio Messages QA agent
Open and use:

- `audio-messages-tester.agent.md`

Use it for:
- end-to-end validation workflows
- Android device and emulator testing
- evidence collection from system tools
- suite-based QA execution
- structured result and report generation

## Output Expectations

Depending on the asset used, expected outputs may include:

- clarified requirements and engineering decisions
- structured test cases or validation plans
- device and environment metadata
- suite-by-suite QA results
- exact evidence from commands, logs, and data stores
- release-readiness summaries or final verdicts
- reusable report artifacts and templates

## Maintenance Notes

When updating files in this repo:

- prefer clear, machine-usable instructions
- keep purpose and scope explicit near the top of each asset
- separate context, execution logic, and presentation where possible
- avoid embedding oversized generated content unless necessary
- keep terminology consistent across prompts and agents
- maintain traceability to requirements whenever applicable

## Future Enhancements

Potential next steps for this repository:

- move assets into `agents/` and `prompts/` directories
- add reusable report template systems
- add sample result JSON or YAML files
- add requirement traceability documentation
- add a changelog for workflow revisions
- define repo-level conventions for prompt and agent design
- add contribution guidelines for maintaining shared automation assets

## License

Add a license file if this repository is intended for reuse or collaboration.
