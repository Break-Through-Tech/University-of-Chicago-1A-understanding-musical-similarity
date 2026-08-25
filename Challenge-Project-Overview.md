# Understanding Musical Similarity: Building a Feature-Based Recommendation System with Graph-Based Interpretability

**Company / Org:** Thrivent   
**Challenge Advisor:** Nathan Rickert, naterick12@gmail.com   
**AI Studio Coach:** Julio Contreras, Julio.Contreras@breakthroughtech.org   
**Program:** Break Through Tech AI Studio - Fall 2026

> **Scope note:** This challenge is representative of industry-relevant work in financial services and financial education. However, it is not connected to any current or future Thrivent project, it uses no Thrivent data or systems, and nothing produced in it reflects Thrivent products, practices, or positions. All data is public-use microdata published by the Federal Reserve Board.

---

## 🌐 About the Problem Space

Financial services organizations, community lenders, credit unions, financial-education nonprofits, and public agencies all face a version of the same question: which households are financially fragile, and what is actually driving that fragility?

The industry answer has historically been a credit score, which measures how reliably someone has repaid debt; yet that is not the same thing as resilience. A household can have excellent credit and no cushion. A household can have thin credit and a strong savings habit. Resilience is about what happens when something goes wrong: the car breaks, the hours get cut, the medical bill arrives.

The Federal Reserve has measured exactly this every year since 2013 through the Survey of Household Economics and Decisionmaking. One of its most-cited findings is deceptively straightforward: what share of adults could cover a $400 emergency expense using cash or its equivalent? In the 2025 survey, that figure was 63 percent.

Your job is to move from that headline number to something more useful: a model that predicts which households are fragile, an honest account of how well that model actually works, and an explanation of what is driving each prediction that a non-technical person could act on.

---

## 🎯 The Challenge

### Project Summary
In this project, you will use public-use survey microdata from the Federal Reserve's Survey of Household Economics and Decisionmaking (SHED) and machine learning techniques such as survey-weighted descriptive analysis, feature engineering on categorical data, logistic regression, tree-based and gradient-boosted models, probability calibration, mixed-data segmentation, and SHAP-based explanation to build and evaluate a household financial-resilience model that predicts which households are unable to absorb a financial shock and explains why. This will help financial advisors, financial-education programs, and community organizations address the challenge of understanding household financial fragility at a population level and turning that understanding into better-informed conversations.

The deliverable is decision support for a human, as opposed to an automated decision. 

### Success Criteria

**Replication (this comes first):**
- Reproduce one published Federal Reserve estimate from the 2025 report within a tolerance your team declares in advance
- If your weighted estimate does not land near the published figure, your data pipeline is wrong and every model built on it is wrong
- For reference, weighting with `weight_pop` reproduces all four headline figures to within 0.5 of a point. Unweighted, they come out one to two points optimistic, which is the size of error that ruins a replication without necessarily looking broken

**Model performance:**
- ROC AUC and PR AUC on a held-out set
- Brier score and a calibration curve, because a probability that is not calibrated is not a probability
- Comparison across logistic regression, a tree-based model, and a gradient-boosted model
- A stated baseline (majority class and a weighted-random baseline) that every model must beat

**Structural insight:**
- Whether mixed-data segmentation produces household segments that are stable across bootstrap samples
- Whether those segments align with, or cut across, familiar demographic groupings

**Interpretability:**
- A short, plain-language explanation for any individual prediction and for the model overall
- Agreement (or disagreement) between logistic-regression coefficients and SHAP values, and an account of why
 
### Stretch Goals

- Extend to multiple survey years (2019 through 2025) and test whether a model trained on one year holds up on a later one
- PCA or UMAP visualization of respondent space, colored by predicted fragility
- Build an interactive "household explorer" notebook where changing one input shows how the prediction moves
- Compare unsupervised segments against supervised risk deciles quantitatively
- Model a secondary target from the table above and report where the two disagree
  
### Project Milestones

| Month | Milestone | Key Activities |
|---|---|---|
| September | Data Understanding & Replication | • Read the codebook before touching the data<br>• Build the weighted analysis pipeline and reproduce a published Fed estimate<br>• Map skip logic and encode "not asked" separately from "missing"<br>• Build a logistic-regression baseline |
| October | Model Comparison & Segmentation | • Train and compare logistic regression, tree-based, and gradient-boosted models<br>• Calibrate probabilities and report Brier score and calibration curves<br>• Build mixed-data segments (K-prototypes or equivalent) and test their stability |
| November | Explanation & Final System | • Complete subgroup performance and calibration analysis<br>• Produce SHAP-based global and local explanations<br>• Finalize the resilience dashboard and the evidence-backed conversation prompts |

> **Note for the team:** Please create a GitHub Projects board in this repository to break these milestones into weekly tasks. Go to the **Projects** tab → **New project** → Choose **Board** → Add columns for each month.

---

## ⚖️ Scope and Guardrails

This project describes population patterns. It does not score people.

**What this project does:**
- Estimates the probability that a household with given characteristics is financially fragile
- Explains which factors move that estimate and by how much
- Produces conversation prompts and educational material grounded in evidence

**What this project must not do:**
- Identify sales prospects or rank individuals for outreach
- Recommend a financial product to any individual
- Present survey-based predictions as an underwriting, eligibility, or creditworthiness decision
- Treat a segment label as a fixed type of person

Keep protected attributes in the analysis so you can audit performance across groups. Auditing a model for unequal errors and targeting people by group membership are different activities, and the report you write should make clear which one you did.

---

## 📊 Dataset

**Name and Source:** Survey of Household Economics and Decisionmaking (SHED), Board of Governors of the Federal Reserve System  
**Format:** CSV and Stata (.dta), with a PDF codebook per year  
**Size:** A few MB per year. Nothing here requires sampling or a GPU  
**Location:** https://www.federalreserve.gov/consumerscommunities/shed_data.htm  
**Codebook (2025):** https://www.federalreserve.gov/consumerscommunities/files/SHED_2025codebook.pdf  
**Questionnaire with variable IDs (Appendix A):** https://www.federalreserve.gov/publications/2026-supplemental-appendixes-report-economic-well-being-us-households-2025-appendix-a.htm  
**Published response tables (Appendix B):** https://www.federalreserve.gov/publications/2026-supplemental-appendixes-report-economic-well-being-us-households-2025-appendix-b.htm  
**FAQ:** https://www.federalreserve.gov/consumerscommunities/shed-faqs.htm


### Key Details

- Public-use microdata is available for every survey year from **2013 through 2025**. The 2025 file was posted **July 7, 2026**. The 2025 survey was fielded in **October 2025** and released in the report published **May 13, 2026**
- One row per survey respondent, identified by `shedid`. The first four digits of that ID indicate the first year the respondent took the survey, which is how you link panel respondents across years
- **Survey weights are not optional.** `weight` scales qualified respondents to the sample size; `weight_pop` scales them to the U.S. population. Unweighted percentages will not match anything the Federal Reserve published, and an unweighted model learns the panel's composition rather than the country's
- The 2025 file is **12,934 rows by 815 columns**, and **357 of those columns end in `_iflag`**. Those are imputation flags. Drop them from your feature matrix; `K0_iflag` in a model predicting `K0` is leakage. Keep them only to report how much of a variable was imputed, which for the target columns is under 1%
- Most variables are **categorical**, and values arrive as readable labels rather than numeric codes. `EF1` contains `Yes` and `No`; `B2` contains `Living comfortably` and three siblings
- Many columns are governed by **skip logic**. A blank means "this person was never asked," which is different from "this person declined to answer." Item nonresponse has already been imputed and flagged, so what remains blank is almost always a skip. Requiring every survey column to be non-blank leaves you with **zero rows out of 12,934**, so keep that in mind when data cleaning as to not delete data.
- Geography is available at state (`ppstaten`), census region (`ppreg4`), and census division (`ppreg9`). The Fed explicitly cautions that the survey is not designed for representative state-level estimates. Use regions or divisions
- Demographic variables generally carry a `pp` prefix and come from the Ipsos KnowledgePanel profile
- **After you drop the `_iflag` columns you still have plenty.** 446 candidate features remain, 183 of which are asked of more than 80% of respondents, and 153 of which are never blank at all. One-hot encoded that is roughly 1,100 columns against 12,934 rows, which is a comfortable ratio for everything this project asks you to do
- **Question wording and response options change between years.** Check each year's Appendix A before combining files. 
- Responses are self-reported and carry measurement error, including on the subjective "on track" retirement question, where respondents decide for themselves what "on track" means
- **Citation:** Board of Governors of the Federal Reserve System, Survey of Household Economics and Decisionmaking [dataset], https://doi.org/10.17016/datasets.002

---

## 🛠️ Suggested Approach

**ML Problem Type:** Binary classification with probability calibration, survey-weighted descriptive statistics, mixed-data clustering / segmentation, model explainability

**Recommended Libraries:**
- pandas
- scikit-learn (including `CalibratedClassifierCV` and `calibration_curve`)
- statsmodels (for weighted logistic regression with proper standard errors)
- XGBoost or LightGBM
- shap
- kmodes (K-prototypes for mixed numeric and categorical data)
- matplotlib / seaborn
- yellowbrick (optional, for quick diagnostic plots)

**Evaluation Metrics:**
- ROC AUC and PR AUC
- Brier score and calibration curves
- Recall and calibration reported per subgroup, not just overall
- Adjusted Rand index or silhouette score for segment quality, plus a bootstrap stability check
- Absolute error against the published Federal Reserve estimate for the replication step

---

## 📚 Resources to Get Started

**Background Reading:**
- [Report on the Economic Well-Being of U.S. Households in 2025](https://www.federalreserve.gov/publications/2026-economic-well-being-of-us-households-in-2025-savings-investments.htm): the Savings and Investments chapter, where the headline resilience figures live
- [2025 report fact sheet](https://www.federalreserve.gov/newsevents/pressreleases/files/other20260513a1.pdf): one page, every number your replication step needs
- [SHED interactive data visualizations](https://www.federalreserve.gov/consumerscommunities/sheddataviz/unexpectedexpenses.html): the $400 question broken out by demographic group, useful as a sanity check on your own cuts
- [Supplemental Appendix A](https://www.federalreserve.gov/publications/2026-supplemental-appendixes-report-economic-well-being-us-households-2025-appendix-a.htm) and [Appendix B](https://www.federalreserve.gov/publications/2026-supplemental-appendixes-report-economic-well-being-us-households-2025-appendix-b.htm): the questionnaire with variable IDs, and every published percentage keyed to its variable

**Technical Tutorials:**
- [SHED FAQ](https://www.federalreserve.gov/consumerscommunities/shed-faqs.htm): weights, geography, panel linkage, and cross-year comparability, all in one place. Read this before writing a single line of pandas
- [scikit-learn: probability calibration](https://scikit-learn.org/stable/modules/calibration.html): why a model can be accurate and still lie about its confidence
- [scikit-learn: preprocessing categorical features](https://scikit-learn.org/stable/modules/preprocessing.html#encoding-categorical-features)
- [SHAP documentation](https://shap.readthedocs.io/en/latest/): start with the tabular tutorial, not the theory
- [Google: Classification crash course](https://developers.google.com/machine-learning/crash-course/classification): accuracy, precision, recall, ROC, and thresholds

**Code Examples:**
- [kmodes: K-prototypes for mixed data](https://github.com/nicodv/kmodes): the standard tool when your features are half categorical
- [scikit-learn calibration examples gallery](https://scikit-learn.org/stable/auto_examples/calibration/index.html)

**Other:**
- The 2025 codebook PDF. Not optional reading, and not a document to skim

*Feel free to explore beyond these, and share anything interesting you find with me!*
---

## 🤝 How We'll Work Together

**Official check-ins:** During our biweekly 45-minute AI Studio Lab Section meeting block (2nd and 4th week of every month)

**Other ways to reach out to me with questions:** 
* email: naterick12@gmail.com; discord: nathanrickert
* Note: I will aim to respond within 48 hours. Please reach out to your AI Studio Coach with urgent questions.

**Recommended free coding / collaboration tools**
* Google Colab
  
---

## 🚀 Getting Started

1. **Review this overview document** and note any questions for our first meeting
2. **Begin Exploring the Data!!** using the link above
3. **Read more on technical concepts that look unfamiliar**

I’m excited to work with you!

---

## ❓ Questions?

Please bring any questions to our first meeting during the week of August 24th (Break Through Tech’s Bridge to Studio - Session C). 
