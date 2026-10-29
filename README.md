# Call-Summary QA Review

A single-page review surface that helps a QA reviewer decide whether each call summary accurately represents its transcript, and whether it needs correcting.

**Live app:** [(https://yasinali23.github.io/qa-reviewer/)]. No login or setup is needed.

## How to run and check

- Open the live URL, or open `index.html` in any modern browser. It is one self-contained file, with no build step, server or API keys.
- **Results** (top of the page): an overall result and a per-call result, calculated with the severity rules' verdict order: any CRITICAL = FAIL, then any UNCLEAR = NOT FINAL, then any HIGH or MEDIUM = NEEDS EDITS, otherwise PASS.
- **Each call:** the transcript is on the left. The summary is on the right, split into sentences, each with a severity status, a one-line reason and the transcript turn IDs it is based on.
- **Click a sentence** to highlight the transcript lines that **support** (teal) or **contradict** (magenta) it.
- **Missing from summary** lists material points that the summary leaves out.
- **Reviewer decision** on every sentence: AGREE, NOT AN ERROR or a severity. The reviewer's decision always wins, and results update live. Overrides ask for a reason and turn ID.
- **Edit** corrects a sentence in place, and **Copy summary** copies the corrected summary. An edited sentence is flagged until the reviewer sets a decision on it.
- **Corrected summaries** (separate section): all 20 calls rewritten so that every sentence is supported. Overall result: PASS.
- **Demo** (bottom): CALL-002 with a planted error, £100 changed to £150, showing the page returning **FAIL**.

## What I prioritised and why

1. **Clear severity rules first** (`severity rules.txt`). The reviewer needs a consistent definition of "misleading" versus "acceptable summarisation" before any interface is useful.
2. **Evidence beside every judgement.** Each status cites transcript turns, so the reviewer can check it rather than trust it.
3. **The reviewer has the final say.** Statuses are suggestions, and the reviewer's decisions drive the result.
4. **Zero-friction deployment.** A single static HTML file means nothing can break at review time.

## Material assumptions

- The transcript is the source of truth. Transcription errors during hold or silence (e.g. CALL-003 T15) are ignored, as the summary rules instruct.
- "Material" means anything the summary rules require. Leaving out optional detail is not an error.
- UNCLEAR is used rather than guessing when the transcript doesn't settle a point. Example: the summary says the agent checked understanding when the customer only restated the plan.
- One reviewer per browser, so decisions and edits are saved in that browser only (localStorage).

## What I checked

- All 491 transcript lines and 103 summary sentences are parsed and shown, and every cited turn ID exists in its transcript.
- Verdict logic tested in a headless browser: resolving UNCLEARs gives PASS, any HIGH gives NEEDS EDITS, any CRITICAL gives FAIL. Decisions and edits survive a reload, and Copy summary includes edits.
- The demo section returns FAIL without affecting the main results.

## Known limitations

- **The summaries reviewed are a set I generated from `summary-rules.txt`**, not the "Summary to review" texts supplied in `candidate-calls.md` / `.json`. The supplied summaries (which contain errors such as CALL-008's invented default notice and CALL-002's £150 promise) are not yet on the page.
- Sentence statuses were assessed with AI assistance and are fixed in the page. The page does not re-analyse a summary after an edit; the reviewer's decision is what counts.
- Decisions and edits are not shared between reviewers or devices, and there is no export of review results.
- `candidate-calls.json` and `candidate-readme.md` were not used.

## What I would tackle next

1. Load the supplied summaries from `candidate-calls.json` and review those, as the main dataset.
2. Automated pre-checks for the CRITICAL rules (amounts, dates, times, call direction, "Payment made", consent), each with turn-ID evidence.
3. Shared storage for decisions, an audit trail of overrides, and CSV export.
4. Filters (show only FAIL or NOT FINAL calls) and keyboard navigation for faster review.
