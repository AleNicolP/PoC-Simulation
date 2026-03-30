# Challenge Description

## Part 1: POC Simulation

A large manufacturing company’s Procurement department is starting a digitalization journey.

Their category managers are currently facing a major challenge:
they are unable to properly analyze spend due to a supplier database that is cluttered with messy, duplicate, and outdated entries.

At the same time:

* leadership is pushing for a clear cost-saving strategy for the next year
* there is interest in exploring sustainability (ESG) in the supply chain
  → however, this is not a current priority due to limited resources

### Commercial Context

The company is currently piloting solutions with two competitors:

* a well-known legacy provider with strong market reputation
* a newer player with promising performance

While the newcomer performs well, the legacy provider’s proven track record still carries weight.

👉 Budget is already allocated, and a final decision will be made next quarter.

---

## 1. Entity Resolution

You are provided with a sample of companies from the client.

Each input entry has been processed through an entity resolution engine, which returns:

* up to 5 candidate matches per row

### Task

For each input company:

* select the best matching real-world entity from the candidates
* if none of the candidates are correct:

  * leave it unmatched, or
  * identify the correct entity externally

👉 Goal:
Resolve every input to a real-world company (if it exists online).

---

## 2. Data Analysis & Quality Control

After selecting the correct matches:

* review the provided data attributes
* identify inconsistencies, missing values, or potential issues

You are **not required to fix the data**, but you should:

* think about how the dataset could be curated
* consider what would be needed to make it clean and usable for the client

---

## 3. Summarize Your Work

Provide a clear explanation of your approach:

* how you understood the problem
* how you approached the matching process
* what decisions you made and why
* what observations you made along the way
