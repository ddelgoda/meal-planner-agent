# Roadmap

The prototype is built and tested (see [test-results.md](test-results.md)). The next phases aim to touch each layer of the Microsoft stack once, end to end, rather than to perfect the meal plan. Each phase builds on the last, and every step has a clear "done when".

## Phase A: core agent (7–17 Oct 2026)

| Day | Focus | Done when |
|---|---|---|
| Wed 7 Oct | Agent returns the plan as JSON (day, meal, ingredient, grams, food group) | Valid JSON on 3 runs in a row |
| Thu 8 Oct | Validation flow sums grams by food group per day | Totals calculated in the flow |
| Sat 10 Oct | Compare totals to targets; pass/fail shown in the reply | "Not yet validated" replaced |
| Mon 12 Oct | Food lookup of about 10 foods (from the v4 errors) as a Dataverse table | Table populated |
| Tue 13 Oct | Validation uses the lookup instead of the agent's labels | Spinach error caught[^spinach] |
| Wed 14 Oct | Golden set: 6 scenarios with expected outcomes | Set written |
| Thu 15 Oct | Baseline run | Results table |
| Sat 17 Oct | Demo recording, screenshots, solution export | Demo evidence saved |

## Phase B: Power Platform breadth (22 Oct–7 Nov 2026)

| Day | Focus | Done when |
|---|---|---|
| Thu 22 Oct | Custom solution and publisher; move components in | Everything in the solution |
| Sat 24 Oct | Environment variables for the site address and list names | No hard-coded values |
| Mon 26 Oct | Test environment (Sandbox, or a second Developer environment), auditing on; managed import | Runs in the test environment |
| Tue 27 Oct | Power Platform pipeline from dev to test | Pipeline deploys, or the manual route is documented |
| Wed 28 Oct | DLP policy | Policy applied |
| Thu 29 Oct | Analytics, transcripts, credit use | Screenshots |
| Sat 31 Oct | Purview audit log and a sensitivity label on the guidance PDF | Audit entries and label visible |
| Mon 2 Nov | Publish to Teams; require Entra ID sign-in | Plan generated in Teams |
| Tue 3 Nov | User identity into the pantry flow; "Send an email" step | Plan arrives in Outlook |
| Wed 4 Nov | Golden set rerun | No regressions |
| Thu 5, Sat 7 Nov | Buffer | |

## Phase C: Azure (9–21 Nov 2026)

| Day | Focus | Done when |
|---|---|---|
| Mon 9 Nov | Azure subscription in the same tenant; resource providers, resource group, budget alert, one least-privilege role | Budget alert active |
| Tue 10 Nov | Azure AI Foundry project; one LLM and one SLM compared on 2 golden prompts | Comparison noted |
| Wed 11 Nov | Guidance PDF in Blob storage; Azure AI Search index | Index searchable |
| Thu 12 Nov | AI Search as a Copilot Studio knowledge source | Agent cites the PDF |
| Sat 14 Nov | Foundry evaluation (groundedness, relevance) against manual scores | Comparison table |
| Mon 16 Nov | Content Safety Prompt Shields on the injection text | Injection flagged |
| Tue 17 Nov | Python Azure Function for validation totals, with Application Insights | Function returns pass/fail |
| Wed 18 Nov | Key Vault or managed identity; agent calls the Function | End-to-end call works |
| Thu 19 Nov | Resource group exported as Bicep and committed | Bicep in the repo |
| Sat 21 Nov | One GitHub Actions workflow | Workflow runs green |

## Phase D: wrap-up (23–25 Nov 2026)

| Day | Focus |
|---|---|
| Mon 23 Nov | Architecture update (current state plus Azure), register entry, evaluation summary |
| Tue 24 Nov | README, final screenshots, migration runbook |
| Wed 25 Nov | Back up the solution, lists, Dataverse data and transcripts; delete Azure resources |

## Out of scope (future work)

Pro-code agents with the Microsoft 365 Agents Toolkit; Foundry Agent Service, Semantic Kernel or the Microsoft Agent Framework; multi-agent setups and MCP tools in Copilot Studio; Azure API Management as an AI gateway; fine-tuning; Defender for Cloud AI security posture; Graph connectors; the Power Platform CoE Starter Kit.

## Fallbacks

- If a Sandbox environment can't be created, a second Developer environment is the test target.
- If the pipeline is blocked, the manual export and import is documented instead.
- If calling the Function from the agent stalls, it is called from a REST client with the agent's JSON.
- If anything slips, the Phase B buffer absorbs it, and the GitHub Actions step is the first to drop.

[^spinach]: In the prompt v4 tests, the agent treated spinach as a bitter green (bitter greens are rocket, radicchio or endive) and counted it towards the vegetable target, although leafy greens are in addition to vegetables. A lookup table gives each food one fixed food group, so validation no longer depends on the agent's labels. Details in [test-results.md](test-results.md).
