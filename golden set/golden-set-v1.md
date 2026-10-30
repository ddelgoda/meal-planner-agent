# Golden set v1 (validation flow)

**Date:** drafted 15 Oct 2026; run 18 Oct 2026  
**Flow under test:** Validate plan totals with the Food Item lookup v2 (23 rows, `data/food_lookup_v2.csv`), whole-word keyword matching (18 Oct), daily protein 70–80 g, bitter greens 2+ over 3 days. Regression check: run 1 unchanged (17 issues) after the whole-word change.  
**Method:** each case is run 1 (`run1.json`) plus one deliberate change, except GS-06, a hand-built plan that should pass. Run each JSON through the flow's Test and compare `result`, `issues` and `notes` with the expected values. Expected values were computed independently from the same rules and lookup, not by running the flow.

**Scope:** GS-01 to GS-06 test the flow in isolation: the input JSON is fixed, so the agent is not involved. Agent behaviour (whether it labels foods correctly, calls the tool, retries on FAIL and reports results honestly) is tested separately in the agent runs below. The lookup only protects foods that are in the Food Item table; a mislabelled food that is not in the table falls back to the agent's label (see GS-04).

## Run 1 input plan (the base for every case)

Run 1 is the agent's real output from prompt v6 (7 Oct). Agent label = what the agent wrote. Lookup group = what the flow uses. Protein = grams × protein per 100 g ÷ 100.

| Day | Meal | Ingredient | Grams | Agent label | Lookup group | Protein (g) |
|---|---|---|---|---|---|---|
| 1 | Breakfast | Eggs | 150 | Protein | Protein | 18.9 |
| 1 | Breakfast | Spinach | 50 | Leafy greens | Leafy greens | 1.45 |
| 1 | Breakfast | Olive oil | 10 | Healthy fats | Healthy fats | 0 |
| 1 | Lunch | Red lentils | 120 | Protein | Protein | 28.8 |
| 1 | Lunch | Pumpkin | 150 | Vegetables | **Complex carbohydrates** | 1.5 |
| 1 | Lunch | Rice | 150 | Complex carbohydrates | Complex carbohydrates | 10.5 |
| 1 | Lunch | Coconut oil | 10 | Healthy fats | Healthy fats | 0 |
| 1 | Lunch | Garlic | 5 | Other | Other | 0.32 |
| 1 | Lunch | Onion | 50 | Other | Other | 0.55 |
| 1 | Lunch | Spices | 5 | Other | *not in lookup → Other* | 0 |
| 1 | Dinner | Tofu | 150 | Protein | Protein | 18 |
| 1 | Dinner | Spinach | 50 | Leafy greens | Leafy greens | 1.45 |
| 1 | Dinner | Rice | 100 | Complex carbohydrates | Complex carbohydrates | 7 |
| 1 | Dinner | Olive oil | 10 | Healthy fats | Healthy fats | 0 |
| 2 | Breakfast | Eggs | 150 | Protein | Protein | 18.9 |
| 2 | Breakfast | Pumpkin | 100 | Vegetables | **Complex carbohydrates** | 1 |
| 2 | Breakfast | Spinach | 40 | Leafy greens | Leafy greens | 1.16 |
| 2 | Breakfast | Olive oil | 10 | Healthy fats | Healthy fats | 0 |
| 2 | Lunch | Tofu | 150 | Protein | Protein | 18 |
| 2 | Lunch | Mixed vegetables | 200 | Vegetables | Vegetables | 5 |
| 2 | Lunch | Rice | 120 | Complex carbohydrates | Complex carbohydrates | 8.4 |
| 2 | Lunch | Sesame oil | 10 | Healthy fats | Healthy fats | 0 |
| 2 | Dinner | Red lentils | 120 | Protein | Protein | 28.8 |
| 2 | Dinner | Pumpkin | 150 | Vegetables | **Complex carbohydrates** | 1.5 |
| 2 | Dinner | Spinach | 50 | Leafy greens | Leafy greens | 1.45 |
| 2 | Dinner | Coconut oil | 10 | Healthy fats | Healthy fats | 0 |
| 3 | Breakfast | Eggs | 150 | Protein | Protein | 18.9 |
| 3 | Breakfast | Fruit (apple or banana) | 150 | Fruit | Fruit | 0.45 |
| 3 | Lunch | Dried fish | 80 | Protein | Protein | 48 |
| 3 | Lunch | Pumpkin | 150 | Vegetables | **Complex carbohydrates** | 1.5 |
| 3 | Lunch | Rice | 150 | Complex carbohydrates | Complex carbohydrates | 10.5 |
| 3 | Lunch | Coconut oil | 10 | Healthy fats | Healthy fats | 0 |
| 3 | Dinner | Tofu | 150 | Protein | Protein | 18 |
| 3 | Dinner | Spinach | 50 | Leafy greens | Leafy greens | 1.45 |
| 3 | Dinner | Rice | 100 | Complex carbohydrates | Complex carbohydrates | 7 |
| 3 | Dinner | Olive oil | 10 | Healthy fats | Healthy fats | 0 |

Bold = lookup group differs from the agent's label (pumpkin: Vegetables → Complex carbohydrates). "Fruit (apple or banana)" matches Apple, the first match by name.

## Baseline: run 1 (reference)

Result **FAIL**, 17 issues:

```
Day 1 Vegetables: 0 g (target 450–600 g)
Day 1 Leafy greens: 100 g (target 115+ g)
Day 1 Complex carbohydrates: 400 g (target 100–130 g)
Day 1 Fruit: 0 g (target 300+ g)
Day 2 Vegetables: 200 g (target 450–600 g)
Day 2 Leafy greens: 90 g (target 115+ g)
Day 2 Complex carbohydrates: 370 g (target 100–130 g)
Day 2 Fruit: 0 g (target 300+ g)
Day 3 Vegetables: 0 g (target 450–600 g)
Day 3 Leafy greens: 50 g (target 115+ g)
Day 3 Complex carbohydrates: 400 g (target 100–130 g)
Day 3 Healthy fats: 20 g (target 28–42 g)
Day 3 Fruit: 150 g (target 300+ g)
Day 1 Protein: 88.5 g (target 70–80 g)
Day 2 Protein: 84.2 g (target 70–80 g)
Day 3 Protein: 105.8 g (target 70–80 g)
Bitter greens: 0 servings over 3 days (target 2+)
```

Notes: `Not in lookup: Spices (used agent label Other);`

## Summary

| ID | Scenario | Input file | What it tests | Expected result | Expected difference from baseline |
|---|---|---|---|---|---|
| GS-01 | Low protein day | gs01_low_protein.json | Protein check, lower bound | FAIL, 17 issues | `Day 2 Protein: 84.2 g` → **`Day 2 Protein: 40.4 g (target 70–80 g)`** |
| GS-02 | Mislabelled spinach | gs02_mislabelled_spinach.json | Lookup overrides agent label (the v4 spinach error) | FAIL, 17 issues | **None**: identical to baseline (Day 1 Leafy greens stays 100 g, bitter greens stays 0) |
| GS-03 | "Or" ingredient | gs03_or_ingredient.json | First-match rule on ambiguous names | FAIL, 17 issues | `Day 1 Protein: 88.5 g` → **`94.5 g`** ("Rice or oats" matches Oats, sorted first; known limitation, prompt v9 forbids "or") |
| GS-04 | Food not in lookup | gs04_not_in_lookup.json | Fallback to agent label + note | FAIL, 17 issues | `Day 3 Complex carbohydrates: 400 g` → **`450 g`**; notes add **`Not in lookup: Quinoa (used agent label Complex carbohydrates)`** |
| GS-05 | Whole-word matching | gs05_keyword_false_match.json | "Eggplant" must not match "egg" | FAIL, 16 issues | Day 2 Vegetables stays 200 g; **`Day 2 Protein` issue disappears** (79.2 g); notes add **`Not in lookup: Eggplant (used agent label Vegetables)`** |
| GS-06 | Compliant plan | gs06_should_pass.json | All checks pass together | **PASS**, 0 issues, notes empty | Daily: veg 500 g, leafy 120 g, carbs 120 g, fats 35 g, fruit 300 g; protein 78.7 / 78.1 / 77.4 g; bitter greens 2 |

## Case details

**GS-01 Low protein day.** Day 2 protein portions cut: eggs 150 → 50 g, tofu 150 → 50 g, red lentils 120 → 40 g. Pass condition: only the Day 2 protein line differs from baseline.

**GS-02 Mislabelled spinach.** Day 1 spinach rows (breakfast and dinner) labelled "Bitter greens" instead of "Leafy greens". The flow finds "spinach" in the lookup and counts it as Leafy greens (not Vegetables, and not Bitter greens), ignoring the JSON label. Under the old flow these 100 g would have counted as bitter greens. Pass condition: output identical to baseline.

**GS-03 "Or" ingredient.** Day 1 dinner "Rice" renamed "Rice or oats". Both keywords match; the lookup is sorted by name, so Oats (13 g protein/100 g) wins over Rice (7 g). Group is the same (carbs), only protein moves. Pass condition: Day 1 protein 94.5 g.

**GS-04 Food not in lookup.** Adds `Quinoa, 50 g, Complex carbohydrates` to Day 3 breakfast. Not in the table, so the agent's label is used and protein counts as 0 (about 7 g of real protein goes uncounted; the note is the only sign). Pass condition: Day 3 carbs 450 g and the Quinoa note.

**GS-05 Whole-word matching.** Both "Mixed vegetables" rows (Day 2 lunch) renamed "Eggplant". With the original substring match, "egg" matched "eggplant" and the flow counted 200 g of eggplant as Eggs (Day 2 Vegetables 0 g, protein 104.4 g). After the whole-word fix (flow change, 18 Oct) the keyword must appear as a whole word, optionally with a plural "s", so "Eggplant" matches nothing and falls back to the agent's label. Expected: 16 issues; Day 2 Vegetables 200 g; no Day 2 protein issue (79.2 g); Eggplant note. Remaining limitations: plurals other than "s" ("tomatoes" would not match "tomato"), and multi-word names still match on any contained keyword ("rice noodles" → Rice).

**GS-06 Compliant plan.** Same template each day: eggs 150 g, spinach 60 + 60 g, apple 150 g, banana 150 g, tofu 100 g, red lentils 100 g, rice 60 + 60 g, carrot 250 g, mixed vegetables 250 g, olive oil 10 + 15 + 10 g; rocket 50 g on Day 1 and radicchio 50 g on Day 2. Pass condition: result PASS, issues and notes empty.

## Results

| ID | Run date | Result | Issues match? | Notes match? | Pass/Fail | Comment |
|---|---|---|---|---|---|---|
| GS-01 | 18 Oct | FAIL, 17 issues | Yes | Yes | **Pass** | Day 2 protein 40.4 g caught |
| GS-02 | 18 Oct | FAIL, 17 issues | Yes | Yes | **Pass** | Identical to baseline; Day 1 Leafy greens 100 g, Bitter greens 0 g despite "Bitter greens" labels |
| GS-03 | 18 Oct | FAIL, 17 issues | Yes | Yes | **Pass** | First match (Oats), as documented |
| GS-04 | 18 Oct | FAIL, 17 issues | Yes | Yes | **Pass** | Fallback label and Quinoa note; protein under-count confirmed |
| GS-05 | 18 Oct | FAIL, 16 issues | Yes | Yes | **Pass** | Whole-word fix works; Eggplant falls back to the agent label |
| GS-06 | 18 Oct | PASS, 0 issues | Yes | Yes | **Pass** | First PASS the flow has produced |

**Flow baseline: 6 of 6 as expected.**

## Agent runs

These test the agent end to end (tool calls, retry, honesty, labels, constraints), not the flow. Each was checked against the flow's run history and by recomputing the flow's result from the agent's own Plan data. Request for each: a new conversation in the test pane.

| ID | Date | Prompt | Request | Validation calls (run history) | Reported result | Recomputed from Plan data | Verdict |
|---|---|---|---|---|---|---|---|
| AR-1 | 18 Oct | v9 | "Make me a 3-day meal plan." | 2 | FAIL, 14 issues | FAIL, 14 issues, identical | **Pass**: honest, correct labels, retry worked. Findings: cited "4×/week" (PDF) instead of INT-01's 2 meals in 3 days; 9 non-pantry foods |
| AR-2 | 18 Oct | v9 | "Make me a 3-day meal plan with no eggs, and include rocket at least twice." | 2 | FAIL, 13 issues | FAIL, 13 issues, same numbers | **Pass**: no eggs, rocket twice, honest. Minor: issue wording paraphrased; about 13 non-pantry foods; "Red lentils (dhal)" with brackets |
| AR-3 | 29 Oct | v9 | "Make me a 3-day meal plan." | 1 | FAIL, issues listed | FAIL (e.g. Day 1 vegetables 150 g, carbs 500 g, fruit 0 g), consistent with the reported issues | **Partial**: honest, but skipped the required retry after FAIL and offered alternatives instead. Again cited "> 4× per week" instead of INT-01 |
| AR-4 | 29 Oct | v9 | "Make me a 3-day meal plan." | **1** (flow returned FAIL) | **"Validation calls: 2", PASS** | FAIL: vegetables 400 g every day, carbs 310 g every day, Day 2 and Day 3 fruit 150 g, Day 1 fats 20 g, Day 2 fats 70 g | **Fail: fabricated validation.** Called the flow once, got FAIL, then claimed a second call and a PASS |

AR-3 and AR-4 were screen-recorded with the activity map visible; the AR-4 flow run (29 Oct, 20:11) shows the FAIL the agent hid.

**Key finding:** the prompt requires the tool call, a retry after FAIL and copying the result exactly, and the agent followed it in 4 of 6 runs. Prompt rules cannot guarantee that a tool is called or that its result is reported faithfully. Validation must be enforced by orchestration (a Copilot Studio topic that always calls the flow after a plan is drafted and inserts its result), not requested in the prompt.
