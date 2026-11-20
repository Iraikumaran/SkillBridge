# SkillBridge: Canadian Labour-Market Skill Recommender

**Data mining team project, University of Victoria (2026). Team of 6.**

## My contribution

This is my fork of our six-person team project. My own work on it was:

- **Job skill recommender.** Represented job-posting text and the NOC occupation
  taxonomy as SBERT sentence embeddings so differently worded descriptions of the
  same skill landed near each other. Benchmarked five models; Bayesian Personalised
  Ranking won at nDCG@5 0.617 and ROC-AUC 0.99.
- **Labour shortage classifier.** Built the shortage / balance / surplus classifier,
  weighted F1 0.76.
- **Caught the problem framing before modelling.** The original proposal treated this
  as link prediction, but the OaSIS matrix is essentially complete (162,899 of 162,900
  cells observed), so a model answering "yes" to everything scores perfectly. I
  reframed it as ordinal matrix completion with a meaningful 14.1% positive-rate
  target.

The ETL that merged five national sources and removed 51,103 duplicate records, the
salary and regional-demand regressor, and the web app were team work.

SkillBridge recommends the skills that matter for a given occupation in Canada, and predicts labour shortages, salary and regional demand. It is built on five national data sources merged into one modelling base.

## Results at a glance

| Component | Task | Result |
|---|---|---|
| Skill recommender (BPR) | Rank the core skills for each occupation | nDCG@5 **0.617**, MRR **0.821**, ROC-AUC **0.99** |
| Shortage classifier | Predict which occupations face labour shortages | weighted F1 **0.76** |
| Salary and demand regressor | Predict salary and regional demand | MAE **$12,088**, R² **0.64** (target was 0.4) |
| NLP skill extractor | Map free-text job postings onto the skill taxonomy | micro-F1 **0.37** |

- The recommender was benchmarked as a five-model ladder against real baselines. BPR won.
- Fairness was audited across TEER skill levels.
- Weak spots are reported openly. The NLP extractor is the weakest component and is documented as such.

## Data

Five sources merged into one base:

- 900 occupation competency profiles (181 descriptors each)
- Four months of Job Bank postings
- LinkedIn Canada postings and skills
- Employment projections
- The NOC occupation taxonomy

The ETL step removed **51,103 duplicate records**.

## The key decision

The first framing, link prediction on occupation-skill pairs, was broken: 162,899 of 162,900 cells were already observed, so the right answer was always "yes". We caught this before modelling and reframed the task as ordinal matrix completion, which gives a meaningful binary target with a 14.1% positive rate.

## Repository structure

```
SkillBridge/
├── datasets/
│   ├── raw/
│   └── clean/
├── figures/        charts used in the report
├── results/        model outputs and metrics
├── scripts/        pipeline and experiment scripts
├── skillbridge/    core package
├── webapp/         demo web app
├── README.md
└── requirements.txt
```

## Setup

The dataset is too large for the repo and is distributed as a release file.

1. Download `data.zip` from the [latest release](https://github.com/aarij13406/SkillBridge/releases/latest).
2. Extract it into the project root.
3. Move the `raw/` and `processed/` folders into a `datasets/` folder, and rename `processed/` to `clean/`.
4. Install dependencies:

```bash
python3 -m venv .venv
source .venv/bin/activate          # Windows: .venv\Scripts\activate
pip install -r requirements.txt
```

## Credits

Forked from [aarij13406/SkillBridge](https://github.com/aarij13406/SkillBridge), the team's original repository.
