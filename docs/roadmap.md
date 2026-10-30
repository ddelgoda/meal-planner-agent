# Roadmap

The prototype agent, the validation flow, the Food lookup and the golden set are built and tested (see [test-results.md](test-results.md)). The next phases aim to touch each layer of the Microsoft stack once, end to end, rather than to perfect the meal plan. Every step has a clear "done when".

## Phase B: Power Platform (2–20 Nov 2026)

| Day | Focus | Done when |
|---|---|---|
| Mon 2 Nov | Custom solution and publisher; move components in; rename flow actions; export the unmanaged solution as a backup | Everything in the solution; run 1 still gives the same 17 issues |
| Wed 4 Nov | Environment variables for the site address and list names | No hard-coded values |
| Thu 5 Nov | Publish to Teams; require Entra ID sign-in | Plan generated in Teams |
| Tue 10 Nov | Test environment (Sandbox, or a second Developer environment), auditing on; managed import | Runs in the test environment |
| Mon 16 Nov | DLP policy; analytics, transcripts and credit use | Policy applied; screenshots |
| Tue 17 Nov | Pipeline from dev to test (DEC-45), or Purview audit log and a sensitivity label on the guidance PDF | Pipeline deploys, or audit entries and label visible |
| Wed 18 Nov | Golden set rerun | No regressions |
| Thu 19 Nov | Final solution export (unmanaged and managed), final demo recording | Exports and demo saved |

## Phase C1: Azure with the agent (9–12 Nov 2026)

| Day | Focus | Done when |
|---|---|---|
| Mon 9 Nov | Azure subscription in the same tenant; resource group, budget alert, one least-privilege role; guidance PDF in Blob storage | Budget alert active; PDF uploaded |
| Wed 11 Nov | Azure AI Search index; AI Search as a Copilot Studio knowledge source | Agent cites the PDF |
| Thu 12 Nov | Content Safety Prompt Shields on the injection text | Injection flagged |

## Copilot Studio wrap-up (23–25 Nov 2026)

| Day | Focus | Done when |
|---|---|---|
| Mon 23 Nov | Back up the SharePoint lists, the Food Item table, agent transcripts and flow run history | Each export opened and checked |
| Wed 25 Nov | Retire the Copilot Studio environment | Azure still works without it |

## Phase C2: Azure only (30 Nov–12 Dec 2026)

| Day | Focus | Done when |
|---|---|---|
| Mon 30 Nov | Python Azure Function for the validation checks, with Application Insights | Function returns the same result as the flow for the golden set |
| Thu 3 Dec | Foundry evaluation on the golden set and saved agent replies | Comparison table |
| Sat 5 Dec | Thin pro-code agent (Foundry Agents SDK, 1–2 tools) that calls the validation Function | End-to-end call works |
| Mon 7 Dec | Key Vault or managed identity for the Function | No secrets in code |
| Thu 10 Dec | One LLM and one SLM compared on the golden set | Comparison noted |
| Sat 12 Dec | Resource group exported as Bicep; one GitHub Actions workflow | Bicep in the repo; workflow runs green |

## Phase D: wrap-up (14–19 Dec 2026)

| Day | Focus |
|---|---|
| Mon 14 Dec | Architecture update, register entry, evaluation summary |
| Thu 17 Dec | README, runbook, final screenshots |
| Sat 19 Dec | Back up; delete Azure resources |

## Open decisions

- DEC-44: how pumpkin counts against the complex carbohydrate target.

## Written up as designs, not built

- User identity passed to the pantry flow, and the plan sent by email.
- The pipeline from dev to test, if the test environment work does not go well (DEC-45).

## Out of scope (future work)

Pro-code agents with the Microsoft 365 Agents Toolkit; Semantic Kernel or the Microsoft Agent Framework; multi-agent setups and MCP tools in Copilot Studio; Azure API Management as an AI gateway; fine-tuning; Defender for Cloud AI security posture; Graph connectors; the Power Platform CoE Starter Kit.

## Fallbacks

- If a Sandbox environment can't be created, a second Developer environment is the test target.
- If the pipeline is not built, the manual export and import is documented instead.
- If the pro-code agent stalls, the Function is called from a REST client with the agent's JSON.
- If anything slips, Could items drop first (GitHub Actions, then the LLM/SLM comparison), then Should items become written-up designs.
