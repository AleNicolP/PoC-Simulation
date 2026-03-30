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

---

## Notes

This project focuses on building an explainable and robust matching system, rather than optimizing for maximum automation at the cost of incorrect matches.
