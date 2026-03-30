# Entity Resolution POC – Approach & Analysis

## 1. Problem Context

The client is a large manufacturing company whose Procurement department is undergoing a digital transformation. Their current supplier database is cluttered with duplicate, inconsistent, and outdated entries.

As a result:

* they cannot accurately analyze spend
* they cannot identify cost-saving opportunities
* they lack a clear strategy for supplier optimization

Additionally, there is interest in incorporating sustainability (ESG) into the supply chain. However, this is currently not a priority due to the lack of reliable data.

 Key insight:
Entity resolution is not just a data-cleaning task. It is a foundational step that enables:

* spend analysis
* supplier consolidation
* future ESG initiatives (e.g., emissions tracking, supplier sustainability assessment)

---

## 2. Initial Exploration

I started by manually inspecting a subset of the data to better understand its structure and challenges.

Key observations:

* company names are inconsistent (formatting differences, legal suffixes)
* many columns
* some fields are missing or incomplete (e.g., city, website)
* multiple candidates may be plausible but refer to different real-world entities

I initially considered reducing the dataset (e.g., filtering or deduplication), but decided against it at this stage because:

* each candidate represents a potential valid match, even ifa some columns are the same for each 5 companies
* the main challenge is selection, not data reduction

 Therefore, I kept the original structure and approached the problem at the group level.

---

## 3. Problem Framing

This is not simply a string matching problem.

In my asumption, the goal is to:

> identify the real-world entity corresponding to each input, using multiple signals (name, location, digital identity, etc.)

I approached this as a multi-signal scoring system, designed to mimic how a human would evaluate potential matches (how I actually did). 

As a first step, I looked at the company name feature, which is the strongest identity signal. 

After that,  I focused on the country check which I considered a strong filtering signal.
In many cases, a mismatch at the country level is enough to discard a candidate, even if other features appear similar. 

Next, I looked at the city as a supporting signal. While it is less reliable than the country, it can help distinguish between companies with similar names within the same country. When both country and city matched, the confidence in the candidate increased significantly.

I also considered digital identity signals, such as the website domain and email domain. If the domain contains tokens from the company name or if the email domain matches the website domain, this provides additional evidence that the candidate represents a real and consistent entity. However, I treated this signal carefully, since it is not always available and can sometimes be misleading.

Finally, I incorporated a weak semantic signal based on industry and descriptive fields. Since the input data does not include a description, I used the input company name as a proxy and checked for overlap with the candidate’s industry and textual fields. This signal has a lower weight, as it is less precise and can introduce noise.

Overall, these features were selected to reflect how a human would reason through the matching process: starting with strong filtering signals, then validating identity through name similarity, and finally using contextual signals to refine the decision.

---

## 4. Feature Engineering (Raw Signals)

I defined several types of signals:

### 4.1 Name Similarity (Primary Signal)

* compares input name with:

  * official company name
  * legal names
  * commercial names

 This is the most important identity signal.

---

### 4.2 Location Signals

* country (strong signal)
* city (supporting signal)

 Helps distinguish between companies with similar names across different regions.

---

### 4.3 Digital Identity (Domain / Email)

* website domain
* email domain

 Strong signal when available, but not always present.

---

### 4.4 Industry / Semantic Signal

* industry classification
* textual descriptions

Weak signal, used only as supporting context.

---

## 5. Data-Aware Group Weighting

Not all features are consistently available across candidates.

To address this, I implemented:
 **dynamic weighting based on feature availability within each group**

* features with low coverage are down-weighted
* features with strong coverage have more influence

 This makes the system robust to missing or incomplete data.

---

## 6. Scoring Strategy

For each candidate:

* I computed a weighted score based on all available signals
* different weights were assigned depending on importance (e.g., name > industry)
* Weights used (after trying more combinations and understand better their importance)
  * name_score": 4.0
  * "country_score": 2.5
  * "city_score": 1.5
  * "domain_score": 1.0
  * "industry_score": 0.5
---

## 7. Decision Logic

For each input group:

* candidates are ranked by score
* the top candidate is selected

I defined three possible outcomes:

* **match** → clear and confident match
* **ambiguous** → multiple candidates with similar scores
* **no_match** → no candidate meets the minimum confidence threshold

 This avoids forcing incorrect decisions in uncertain cases.

---

## 8. Observations & Learnings

### 8.1 Name is the strongest signal

Low name similarity should not be compensated by other features.

---

### 8.2 Missing data is a major challenge

* many fields are incomplete
* inconsistent data reduces reliability

 Dynamic weighting is essential.

---

### 8.3 Secondary signals can be misleading

* domain and industry can introduce false positives
* they must be carefully controlled

---

## 9. Future Improvements

While the current solution performs well as a Proof of Concept, there are several areas that could be improved in a production setting.

### Additional Features

The system could be extended with additional signals such as:

* email-based matching (beyond domain equality)
* phone number matching
* social media presence (e.g. LinkedIn)
* company size or number of locations as consistency checks

These could provide stronger evidence in cases where name similarity alone is not sufficient.

---

### Data Cleaning and Standardization

Improving data quality would significantly enhance the results:

* more advanced normalization of company names (handling abbreviations, punctuation, ordering)
* reducing or handling missing values more effectively
* deduplicating entities at the database level

A cleaner dataset would reduce ambiguity and improve matching accuracy.

---

### Learning-Based Approach

The current system is rule-based. With labeled data, it could be extended to:

* a supervised learning model
* automatic learning of feature weights
* data-driven threshold tuning

---

### Improved Ambiguity Handling

Ambiguous cases could be handled more effectively by:

* flagging them for human review
* returning top-N candidates instead of a single result
* adding explicit confidence scores

---
## 10. Conclusion
The system is able to confidently resolve approximately 76% of cases automatically.

Around 23% of cases are flagged as ambiguous, which reflects situations where multiple candidates have similar scores and additional validation would be required.

Only about 1% of cases are classified as no match, indicating that the candidate generation step provides generally relevant options.

While no ground truth labels are available to compute exact accuracy, the distribution of decisions suggests that the system is effective at identifying strong matches while explicitly handling uncertainty.

This project demonstrates a practical approach to entity resolution using a combination of simple but effective signals.

By mimicking human reasoning and combining multiple features with dynamic weighting, the system is able to handle noisy and incomplete data while maintaining explainability.

Most importantly, this solution addresses the core business problem: transforming messy supplier data into a reliable foundation for analysis, cost optimization, and future strategic initiatives such as ESG.

## Development Notes
During development, I used AI tools as support for code generation and refinement, especially for structuring functions and improving clarity.

The core design of the solution — including feature engineering, scoring strategy, and decision logic — was entirely based on my own reasoning and understanding of the dataset.
