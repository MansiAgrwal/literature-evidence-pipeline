# Literature evidence pipeline

A set of Google Colab notebooks for mapping a broad, multi-disciplinary literature in a way that can be checked afterwards. I built it to survey research on **how people learn inside personalized algorithmic environments**: recommender feedback loops, adaptive tutoring, adaptive experimental design, measurement reactivity, and model identifiability, among other areas.

The pipeline separates **finding** papers from **judging** them. The code logs every search, keeps the link from each paper back to the query that found it, and flags problems. It never decides on its own whether a paper is relevant or what it shows.

## The five notebooks

| Notebook | What it does | Result of the August 2026 run |
|---|---|---|
| [01 Open discovery](notebooks/01_open_discovery.ipynb) | 20 deliberately broad queries in OpenAlex and PubMed, to check whether the planned search was missing a neighbouring literature. Reports recurring vocabulary, database subject terms, and papers that bridge several queries. | 1,936 unique candidates; 202 retrieved by two or more queries |
| [02 Targeted discovery](notebooks/02_targeted_discovery.ipynb) | 28 narrower queries in 9 adjacent traditions that the open pass surfaced, such as intelligent tutoring, just-in-time adaptive interventions, performative prediction and algorithm awareness. | 2,017 unique candidates |
| [03 Full search harvest](notebooks/03_full_search_harvest.ipynb) | The main search: 153 pre-specified queries across 40 domains, each run in both databases. The plan is frozen by a SHA-256 hash and checked before any search runs. | 306 of 306 searches completed; 15,676 retrievals; 12,494 unique candidates; 0 blocking integrity issues |
| [04 Evidence ledger](notebooks/04_evidence_ledger.ipynb) | Tables for the stage after screening: verified publication → experiment → individual result → evidence claim, each linked back to its search. Holds the original 121-query plan that notebook 03 extends. | Schema and synthetic smoke test |
| [05 Logic audit](notebooks/05_logic_audit.ipynb) | Deterministic checks on the claims built from the evidence: does every causal claim name an estimand, does every normative claim name a benchmark, are there circular dependencies, and is novelty stated more strongly than the search supports? | Synthetic unit tests |

## Design choices

- **Every result keeps its provenance.** The chain runs from search plan to search execution, retrieval, candidate, screening decision, publication, experiment, result and claim. Any claim can be traced back to the query that found it.
- **Duplicates are merged conservatively.** Records are merged only on an identical DOI, or an identical title and year. Fuzzy title matches are reported for manual review, never merged automatically.
- **Every type of search gets the same retrieval cap.** Supportive searches therefore cannot harvest more records than counterevidence searches.
- **Screening queues are stratified.** Papers are queued by domain and by search purpose (direct, counterevidence, foundational, recent, methodological), so a few highly cited papers cannot crowd out counterevidence or niche domains. There is no single relevance score.
- **Gaps are listed, not hidden.** Sources the notebooks cannot query automatically (ACM, IEEE, OSF, publisher sites, citation chains) become an explicit list of manual tasks: 51 in the main run.
- **Runs can resume safely.** Completed searches are skipped on rerun; a search whose saved retrieval count is short is detected and rerun.

## Limitations

- **This is discovery, not a complete systematic review.** Each query retrieves at most 100 records per database. At least 100 of the 306 searches in the main run reported more matches than that, some over 50,000; the saved audit output lists only the first 100 such warnings. Recall therefore depends on each database's relevance ranking.
- **Broad OpenAlex queries are noisy.** The open pass returned highly cited but off-topic machine-learning surveys. That noise is why notebook 02 and the stratified queues exist.
- **The screened corpus is not included here.** This repository contains the pipeline, not the screening decisions or extracted evidence.

## Running it

The notebooks run in Google Colab and save their tables to a folder in Google Drive. Notebook 03 asks for an email address on first run (PubMed and OpenAlex request one for polite API use). Run the notebooks in order, 01 to 03; 04 and 05 are used after screening.

To run outside Colab, install the requirements and replace the Drive paths with a local folder:

```bash
pip install -r requirements.txt
```

### Checked on 22 October 2026

- All five notebooks parse with no syntax errors, and their outputs were cleared before upload.
- The SHA-256 hash of notebook 03's frozen query plan, recomputed from this copy, matches the hash recorded at the August run, and the notebook's pre-flight audit passes.
- Outside Colab, notebook 05's synthetic unit tests and notebook 04's synthetic smoke test both pass.

## License

MIT (see `LICENSE`).
