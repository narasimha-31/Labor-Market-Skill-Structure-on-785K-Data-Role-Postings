# Labor Market Skill Structure on 785K Data Role Postings

Analysis of 785,741 data job postings from 2023 to answer three questions about
skills, pay, and credentials.

**Data:** [lukebarousse/data_jobs](https://huggingface.co/datasets/lukebarousse/data_jobs) (Hugging Face, 231 MB CSV, not committed)
**Stack:** DuckDB, pandas, matplotlib, Jupyter

---

## Problem

Skill advice for data roles is published as global rankings. "SQL is the top
skill." "Spark adds $X." Both average across roles that require different things
and mislead anyone choosing a path.

Three questions, written before touching the data:

1. What skills does each data role actually require?
2. Which skill pairs pay more than the sum of their parts?
3. What does "no degree mentioned" cost, and does the gap shrink with seniority?

---

## What was done

* Profiled 785,741 rows, 17 columns. Salary present on 2.8%, skills on 85.1%.
* Found all 21,781 rows labelled Sudan were US jobs. `job_country` was derived
  from `search_location` (where the scrape ran), not `job_location`. Zero
  Sudanese cities in the data. Reassigned 17,759 to US, 3,930 to Remote,
  92 NULL. Verified no other country was affected.
* Parsed `job_skills` from Python list literals into one row per posting-skill.
  668,704 postings, 3.9M skill rows.
* Excluded `go` and `flow` as tagger false positives (flat across three roles
  that otherwise share little).
* Scoped all salary analysis to US postings, 15,448 rows, median $120,000.

---

## Findings

**Q1. Each role has a distinct signature.**

| skill | Analyst | Engineer | Scientist |
|---|---|---|---|
| sql | 58.0 | 66.8 | 54.2 |
| python | 35.9 | 63.8 | 78.0 |
| excel | 41.9 | 5.0 | 12.0 |
| tableau | 29.1 | 11.1 | 20.2 |
| r | 18.8 | 7.5 | 40.9 |
| aws | 5.7 | 36.6 | 18.0 |
| spark | 3.2 | 31.7 | 16.7 |

SQL is universal, so it does not differentiate. Excel splits Analyst from
Engineer 41.9 to 5.0.

**Q2. No skill pair beat the sum of its parts.**

Tested SQL+Spark and AWS+Python. In every case the better single skill carried
the effect and the combination landed at or below it. Spark is worth $17,500 to
a Data Engineer over SQL alone, and nothing to a Data Scientist. Before
controlling for role, the result reversed: Simpson's paradox.

**Q3. No degree penalty.**

Three of four comparable role-level cells are identical to the dollar. Senior
analysts without a stated degree earn more, $116,250 against $110,000. But only
1.3% of non-senior Data Scientist postings skip the requirement. The degree is
an access filter, not a price.

---

## Decisions

* **Target Data Engineer over Data Scientist.** SQL and Python carry over from an
  analyst profile. AWS (36.6%) and Spark (31.7%) close the gap. Scientist needs R
  (40.9%) and a degree in 98.7% of postings.
* **Learn Spark and AWS, stop collecting tools.** No combination pays. One skill
  that pays in the target role beats four that do not.
* **Do not buy a degree for salary.** It does not move pay at matched role and
  level. It buys entry to Data Scientist roles only.
* **SQL is a prerequisite, not an achievement.** Gets you considered, not paid.

---

## Limits

Salary is disclosed on 2.8% of postings and skews to US pay transparency states.
Data is calendar 2023, predating most GenAI hiring. No control for seniority
beyond title, company size, or industry. `job_no_degree_mention` records posting
language, not hiring behavior. These are associations, not causal effects.
