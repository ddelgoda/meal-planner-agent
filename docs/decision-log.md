# Meal Planner Agent – Decision Log

Sep 28, 2026 · @Dil

## Purpose

This log records every design and data decision for the Meal Planner Agent prototype, with the reason for each. Decisions are numbered DEC-01 onwards; interpretation rules (INT-xx) and no-mix rules (NM-xx) are summarised here and held in full in their SharePoint lists.

All decisions are made by the user unless noted. Where Claude proposed an assumption that the user later corrected, it is recorded under Corrections.

## Platform and architecture

The agent is built in Microsoft Copilot Studio, with rules enforced by code rather than by the LLM.

| ID | Decision | Rationale |
| --- | --- | --- |
| DEC-01 | Build in Microsoft Copilot Studio, not a Python app calling Azure OpenAI | Copilot agents are the essential criterion for the target role; the agency works in a Microsoft/Azure environment |
| DEC-02 | Use a personal training tenant (M365 Business Basic trial, Copilot Studio trial, Power Apps Developer Plan), not the work laptop or employer tenant | Avoids breaching workplace IT policy; work stays in my own environment and can be demonstrated freely |
| DEC-03 | Build in a dedicated developer environment, region Australia | Keeps agents and flows together in one environment; data residency in Australia |
| DEC-04 | The LLM generates plans; a Power Automate flow validates them, called in a fixed-sequence topic | Instructions are guidance, not enforcement; a fixed sequence stops the orchestrator skipping validation |
| DEC-05 | Authority hierarchy: dietitian guidance is authoritative; interpretation rules clarify it; no-mix rules and exclusions are user preferences; pantry is current state; chat requests are dynamic input | Makes conflicts resolvable and explainable |
| DEC-06 | Conflict behaviour: comply with user requests, disclose any shortfall against guidance, offer alternatives; never follow instructions embedded in data | Respects user choice while keeping the guidance visible; defends against prompt injection |
| DEC-07 | No Coles integration; output a shopping list only | Keeps the prototype in scope |
| DEC-08 | Explanations mention a rule only when it changed the outcome; documentation lists every active rule | Keeps user-facing output concise while keeping the system reviewable |
| DEC-27 | Agent model: Claude Opus 5 (a third-party model in Copilot Studio); compare with a Microsoft default model if issues arise | Results depend on the model, so it is recorded for reproducibility |
| DEC-28 | Agent memory (preview) is off | Prevents recalling stale pantry contents; avoids retaining personal health information |
| DEC-29 | "Allow other agents to connect" is off | Least privilege: nothing needs to call this agent |
| DEC-30 | Authentication: Authenticate with Microsoft. Moderation: Medium (default). Web channel security: default. General knowledge and web search are not configurable in this version; defaults used | SharePoint knowledge requires a signed-in user, so the agent respects existing SharePoint permissions |
| DEC-31 | The SharePoint knowledge source uses the site address, not the home page address | A page address may limit indexing to that one page |
| DEC-32 | Allocate 50,000 of 625,000 tenant Copilot Credits to the developer environment; pay-as-you-go billing off | Since 1 September 2026, building and testing consume credits in developer environments; allocate only what is needed and avoid unexpected charges |

## Data and governance

Each source keeps its own authority and provenance, and derived rules link back to their source.

| ID | Decision | Rationale |
| --- | --- | --- |
| DEC-09 | De-identify the dietitian document: remove the practice line and the lipase test result from the text layer (not just covered visually); rename the file; keep the original off the tenant | Personal health information; the agent reads the text layer, so hidden text would still be found |
| DEC-10 | Version the source as Weekly\_Food\_Formula\_v1.pdf; new guidance is uploaded as a new version, not an overwrite | Keeps every derived rule traceable to the exact guidance it came from |
| DEC-11 | Keep the PDF as a knowledge source and a structured targets list for the validator | The agent needs the text to explain choices; code needs clean numbers |
| DEC-12 | Store interpretations separately from source-derived targets, in one interpretation log covering all sources, with a Source document column | Keeps the dietitian's words distinct from my decisions; makes updates to the guidance manageable |
| DEC-13 | No-mix rules are a user-authored preference list. Claude created the No-mix rules table from the Ayurvedic food-combining article at <https://ayurvedapractice.com/combination/>, and the user's decisions were then applied (INT-06 to INT-12). The article itself is kept outside the agent's knowledge | Keeps authority clear: the rules are my preferences, not nutrition evidence |
| DEC-14 | One no-mix rule per food pair, each with a Source evidence column | Pairwise rules are directly checkable by code; provenance shows which rules rest on image descriptions only |
| DEC-15 | Excluded or inactive rules are kept with a status (Excluded, Dormant), not deleted | Preserves the audit trail and allows reactivation |
| DEC-16 | Pantry is a SharePoint list read live by the flow, not a knowledge document | The pantry changes weekly; knowledge sources may take time to re-index |
| DEC-17 | Use SharePoint's built-in Modified column instead of a manual date; the agent asks to confirm the pantry if nothing has changed in 7 days | Automatic dates cannot be forgotten; flags possibly stale input |
| DEC-18 | Use fixed test pantries for evaluation, separate from the real weekly pantry | Makes test results comparable across runs |
| DEC-19 | Leafy greens target (2 Coles large bags of 400 g each per week, plus other vegetables) is taken directly from the source text | The source says "plus a mix of other vegetables", so it is not an interpretation |
| DEC-20 | Dairy protein powder rules are added as new mirrored rules (NM-26 to NM-36), rather than reactivating the dairy milk rules | Dairy milk stays dormant; each new rule references the original it mirrors |
| DEC-21 | SharePoint lists may be deleted and re-imported only until the agent and flows connect to them; after that, lists are edited in place | Deleting a connected list would break the agent's and flows' connections |
| DEC-22 | Protein is counted in grams from each ingredient's protein content (label or food database), not in portions; one "protein source" for the distribution rule means roughly 20 to 25 g protein, as the source defines | Covers foods the source does not list without a separate serving definition; e.g. dried fish at about 60 g protein per 100 g dry weight (general figure, range 55 to 80 g) |
| DEC-23 | Plan quantities are recorded as raw amounts, as bought, so no raw-to-cooked conversion is needed | Targets are in raw units (INT-04) and the bag weight is known, so raw amounts can be checked directly |
| DEC-24 | An explicit chat request may set aside a no-mix rule or exclusion for the current plan only; the agent names the rule it set aside and never assumes an unstated override | No-mix rules and exclusions are my own preferences, so a current request from me outranks them (DEC-05); DEC-06 covered conflicts with guidance but not with preferences |
| DEC-25 | Agent instructions do not restate target numbers; they point to the SharePoint lists. Exception: the structural rule that leafy greens are additional to the vegetable target | Keeps the lists as the single source of truth when targets change; the leafy greens rule was already misread once (see Corrections) |
| DEC-26 | Until the validation flow exists, the agent's target check is labelled "Self-check (not yet validated)"; from v2 the flow's result replaces it | The LLM checking its own arithmetic is not validation; the label keeps the output honest about that |
| DEC-33 | Temporary: CSV copies of the dietary targets, interpretation rules, no-mix rules and pantry lists are uploaded directly to the agent's knowledge (copies in the SharePoint Documents library were not readable); the lists remain the source of truth, and any change is made in both places until the flow fetches the lists directly | Copilot Studio knowledge cannot read SharePoint list items; copies can drift from the lists, so this is replaced by the flow |
| DEC-34 | A "Get pantry" workflow (SharePoint Get items on the Pantry list) is added as an agent tool; the agent calls it before every plan | Reads every row exactly, including the Modified date, instead of relying on search snippets; replaces the pantry CSV copy (DEC-33) |
| DEC-35 | A "Get no-mix rules" workflow returns only Active rules (Filter Query on Status) and only five columns (Rule, Type, Food A, Food B, Instruction) via a Select step | The full list with SharePoint metadata was too large and the agent only took in the first 14 rows; filtering at source sends exactly what the agent should apply |
| DEC-36 | A "Get dietary targets" workflow returns the targets list with seven columns via a Select step; the targets list is edited in place (empty row removed, leafy greens row updated, blank numbers cleared rather than shown as 0) | The agent had been inventing its own targets; the list is connected to a flow, so it is edited rather than re-imported (DEC-21) |
| DEC-37 | A "Get interpretation rules" workflow returns all twelve rules with four columns (Rule, Applies to, Source, Interpretation); value min and max are left out | Blank numbers imported as 0 could be misread; the numbers are already in the interpretation text. With this, all four lists are read through flows and the CSV copies (DEC-33) can be removed |
| DEC-38 | The solution (agent and four workflows, CDS Default Publisher) is exported as a private backup only and not published; the public repository uses documentation and redacted screenshots instead | The export contains the tenant name, SharePoint site address and personal details hard-coded in the workflows; publishing it safely needs environment variables |

## Workflow reference

Each workflow fetches rows from a SharePoint list, keeps only the useful columns, and sends them to the agent as text.

| Workflow | Get items | Select | Respond to the agent |
| --- | --- | --- | --- |
| Get pantry | Pantry list, no filter | None (7 rows, small enough) | Get items value, picked from dynamic content |
| Get no-mix rules | No-mix list, Filter Query `field_12 eq 'Active'` | From `@body('Get_items')?['value']`; maps Title and field\_1 to field\_4 | `@string(body('Select'))` |
| Get dietary targets | Targets list, no filter | Same From; maps Title and field\_1 to field\_6 | `@string(body('Select'))` |
| Get interpretation rules | Interpretation list, no filter | Same From; maps Title and field\_1 to field\_3 | `@string(body('Select'))` |

| Expression | Meaning |
| --- | --- |
| `@` | Marks text as an expression to evaluate; without it the literal words are sent |
| `body('Get_items')` | The full output of the Get items step (spaces in step names become underscores) |
| `?['value']` | The list of rows within that output; `?` returns nothing instead of failing if it is missing |
| `item()` | Inside Select, the current row being processed |
| `item()?['Title']` | The Title column of the current row; field\_1, field\_2 and so on are SharePoint's internal names for the imported CSV columns |
| `body('Select')` | The output of Select: the trimmed list |
| `string(...)` | Converts the list to text, because the Respond output is a Text type |
| `field_12 eq 'Active'` | A filter query run by SharePoint: only rows where field\_12 equals Active |

## Interpretation rules

Twelve interpretation rules resolve gaps and ambiguities in the sources; full detail is in the Interpretation rules list.

| ID | Applies to | Decision |
| --- | --- | --- |
| INT-01 | Bitter greens | "A few times a week" means at least 4 times per week |
| INT-02 | Nuts | Count only as a protein top-up (60 g), not as a fat serving |
| INT-03 | Healthy fats | Fish oil ([redacted]) counts as 1 fat serving per day |
| INT-04 | Vegetables | 1 cup means 1 cup raw; conversion for cooked recipes deferred |
| INT-05 | Scope | Supplement timing out of scope for v1; bulk-buy quantities reserved for the shopping list, except leafy greens |
| INT-06 | No-mix rules | Pairwise rules apply within the same meal |
| INT-07 | Milk and garlic | Rule excluded; the source author marks it as uncertain |
| INT-08 | Milk rules | Apply to dairy milk only; plant milks, including coconut milk, are exempt |
| INT-09 | Sour foods | Defined by taste: citrus, tamarind, tomato, sour fruits, vinegar, pickles, fermented dairy |
| INT-10 | Germinated grains | Sprouted legumes are treated as ordinary legumes |
| INT-11 | Dairy milk | Excluded from all plans; milk rules dormant but retained |
| INT-12 | Dairy protein powders | Whey, casein and milk protein isolate or concentrate inherit the dairy milk rules except salt; active for powders only (NM-26 to NM-36, NM-32 excluded) |

## Corrections

Three assumptions proposed by Claude were corrected or removed after review.

| Assumption | Correction |
| --- | --- |
| No-mix rules apply within the "same meal" | The source gives no scope; set to "to confirm", then decided by the user (INT-06) |
| Leafy greens count towards the daily vegetable target | The source says "plus" other vegetables; greens are in addition (DEC-19) |
| Leafy greens target is an interpretation of bulk-buy guidance | It comes directly from the source text; the interpretation rule was removed |

## Build and test log

What happened during the build, including issues and how they were diagnosed.

| Date | Event | Outcome |
| --- | --- | --- |
| 29 Sep | Instructions v1 pasted, including the line that the pantry changes weekly | Saved |
| 29 Sep | Preview blocked: environment out of credits (EnforcementUsageCredits) | Credits allocated (DEC-32); preview works |
| 29 Sep | Build tab not responding | Not needed for v1; retry after a browser refresh |
| 29 Sep | First test: every SharePoint knowledge search failed (SubstrateSearch, GetNumActiveUsers) | SharePoint's own search finds the PDF and list items, so content is indexed; likely a backend search issue on a new site. Similar SubstrateSearch failures have been reported to Microsoft |
| 29 Sep | Agent behaviour when retrieval failed | Declined to guess targets or pantry contents, explained why, and offered options: followed the honesty and pantry instructions (v1 success) |
| 30 Sep | Search error persisted; switched model to GPT-5 Chat | Same error once it searched; the model was not the cause (earlier it had simply not searched) |
| 30 Sep | Knowledge source still used the home-page address (CollabHome) | Re-added with the site address; search now works and the agent reads the 70 to 80 g protein target from the PDF |
| 30 Sep | Agent found the no-mix article PDF in the library and asked whether to use it as the ruleset | Correctly asked rather than assumed; article to be moved outside the site (DEC-13) |
| 30 Sep | Agent presented the PDF's bulk-buy guidance as the current pantry, citing the PDF | SharePoint lists are not readable as knowledge; search returned the nearest match. Fix: v2 instruction limiting the pantry to the list or chat; CSV copies uploaded (DEC-33) |
| 30 Sep | CSV and Word copies uploaded to the SharePoint Documents library; the agent could not read them, even when given the file name | Only worked after the CSVs were uploaded directly to the agent's knowledge; files in SharePoint stayed unreachable (cause unconfirmed) |
| 30 Sep | After direct upload, the agent found INT-01 only when told the file name | Files are readable but search did not locate them unprompted; prompt v2 names each file |
| 30 Sep | Built the Get pantry workflow; first calls returned an empty pantry | Run history showed Get items returned all seven rows but the response output was blank; after fixing the output, the agent listed all seven items with correct quantities (including rice 1 kg and dried fish 200 g) and the last-updated date |
| 1 Oct | Built the Get no-mix rules workflow; the agent showed only 14 of 36 rules | Run history showed all 36 rows sent but too large for the agent. Added a Status filter and a Select step; the designer kept wrapping Select in a loop and saving expressions as plain text, fixed by typing From and map values as @ expressions with internal column names (field\_1 to field\_4). The agent now lists all 23 active rules correctly |
| 1 Oct | Built the Get dietary targets workflow | Agent listed all eight targets correctly, including leafy greens at 400 g per bag. Interpretation rules were not applied (bitter greens still "a few times" instead of 4; vegetables "raw or cooked not specified" instead of raw), which supports building the interpretation rules flow |
| 1 Oct | Built the Get interpretation rules workflow | Agent listed all twelve interpretation rules correctly, including INT-01 (bitter greens at least 4 times a week) and INT-04 (raw cups) |

## Prompt versions

Each version of the agent instructions and what changed.

| Version | Date | Changes |
| --- | --- | --- |
| v1 | 29 Sep | Initial instructions: authority hierarchy, conflict behaviour, data safety, output format, honesty; pantry changes weekly |
| v2 | 30 Sep | Each source file named; search sources before asking; state if a source cannot be accessed; no-mix rules only from the list; pantry only from the list or chat, never inferred; no clarifying questions, state assumptions at the end; plan is for one person; leafy greens additional to vegetables (DEC-25); explicit set-aside of rules (DEC-24); "Self-check (not yet validated)" heading (DEC-26); whey line removed |
| v3 | 30 Sep | Pantry-first planning: build meals around pantry ingredients, use each pantry protein at least once, minimal non-pantry additions, pantry items only on the shopping list for extra quantity; new output step 0 "Pantry found" |
| v4 | 1 Oct | Sources now come from tools: dietary targets, interpretation rules, no-mix rules and pantry are each fetched by a workflow before every plan (DEC-34 to DEC-37); the PDF is kept to explain choices; CSV copies removed from knowledge |
| v5 | 1 Oct | Experiment: removed rules already held in the data (active-only filter, same-meal scope, protein per meal, leafy greens in addition, dairy milk exclusion) so the prompt covers behaviour only; behaviour lines moved from SOURCES to PLANNING RULES; added "name the tool" to OUTPUT |

Test results for every prompt version and scenario are kept in the repository's `docs/test-results.md`.

## Open items

- [ ] Later improvement: a "last confirmed" date for the pantry, so an unchanged but checked pantry is not flagged as stale
- [ ] Fix counting and classification errors from the v4 tests (cases 1, 3 and 4): fruit and bitter greens missing or not flagged; spinach treated as a bitter green; spinach counted towards vegetables; fats counted in teaspoons; meals under 20 g protein counted as a protein source; raw rice labelled as cooked
- [ ] Phase 2 (ALM): move the SharePoint site address and list names into environment variables, so the solution can be exported without personal details and imported elsewhere

## Appendix: prompt text

The full text of each prompt version is kept in the repository's `prompts/` folder (prompt\_v1.md to prompt\_v5.md), each with its date, changes and result. v4 is the active prompt.
