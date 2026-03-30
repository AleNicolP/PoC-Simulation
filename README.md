# Entity Resolution POC

This project implements an entity resolution system designed to match supplier data, based on a real-world procurement use case.

The goal is to identify the correct real-world company for each input record, despite noisy, duplicate, and incomplete data.

---

## Input Data

The dataset contains multiple attributes for both input companies and candidate matches (over 70 columns).

For this solution, I selected the most relevant features for matching:

- company name (and variations)
- location data (country, city)
- website and email domain
- industry and descriptive fields

The system is designed to work even when some of these features are missing.
---

## Approach

The solution is based on a multi-signal scoring system that mimics human reasoning.

For each candidate, multiple features are computed:

* name similarity (primary signal)
* country match (strong filtering signal)
* city match (supporting signal)
* domain consistency (digital identity signal)
* industry similarity (weak semantic signal)

A dynamic weighting mechanism is applied based on data availability.

Candidates are ranked, and a decision is made per group:

* **match** – clear best candidate
* **ambiguous** – multiple similar candidates
* **no_match** – no reliable candidate

---

## Output

The system produces:

* a selected match per input (if available)
* a confidence score
* a decision label (match / ambiguous / no_match)
* an explanation of why the match was chosen

Results are saved in:

* `entity_resolution_results_simple.csv`

---

## Results

* **~76%** of cases are confidently resolved automatically
* **~23%** are flagged as ambiguous
* **~1%** are classified as no match

These results indicate that the system is effective at identifying strong matches while explicitly handling uncertainty.

---

## Results Analysis (charts)
### Decision Distribution

Most cases are confidently classified as matches, while a smaller portion is flagged as ambiguous. Very few cases are classified as no match, indicating that the candidate generation step provides generally relevant options.

### Distribution of Normalized Scores

The majority of top candidates have high normalized scores, which suggests that the scoring system is effective at identifying strong matches. Lower scores are associated with more uncertain or weaker matches.

### Distribution of Score Differences (Top1 vs Top2)

In many cases, the difference between the top two candidates is small, which explains the presence of ambiguous classifications. Larger gaps indicate clearer decisions and higher confidence in the selected match.

## Tech Stack

* Python
* pandas
* numpy
* matplotlib

---

## Project Structure

* `solution.py` – main implementation
* `REQUIREMENTS.md` – challenge description
* `APPROACH.md` – detailed explanation of the solution
* `entity_resolution_results_simple.csv` – generated results
* decision_distribution.png - chart
* score_distribution.png - chart
* score_gap_distribution.png - chart

---

## Notes

This project focuses on building an explainable and robust matching system, rather than optimizing for maximum automation at the cost of incorrect matches.
