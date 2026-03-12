# STAT 27850 / 30850 — Multiple Testing, Modern Inference, and Replicability
---
 
## Lecture Notes — Conceptual Roadmap
 
The 15 sets of lecture notes (notes 1–8, 10–16; notes 9 was not included in the repository) develop the course material in roughly four thematic arcs. The progression is deliberate: each arc introduces a new challenge and the tools to handle it.
 
### Arc 1 — Foundations of Hypothesis Testing and Global Null Tests (Notes 1–2)
 
The course opens with a concrete motivating example: public vs. private leaderboard scores on Kaggle. When teams repeatedly submit predictions and adapt to public feedback, their public scores become systematically inflated — a manifestation of overfitting, data snooping, and p-hacking. The hypergeometric model formalizes the null hypothesis that a team's public-set accuracy is consistent with its overall accuracy, and a one-sided p-value flags suspiciously good public performance. This example already surfaces the themes that recur throughout the course: exchangeability as a core assumption, selection effects created by leaderboards, and the need for multiple-testing corrections when many teams are compared.
 
From this motivation, Notes 1–2 lay out the classical framework: null hypotheses, test statistics, Type I error, and the super-uniform property of valid p-values (meaning that under the null, $P(p \le t) \le t \text{ for all } t$. Three global null tests are then developed side by side:
 
- **Bonferroni** rejects the global null if the smallest p-value falls below $\alpha / m$. It is always valid — no assumptions on dependence — but can be conservative when p-values are correlated.
- **Fisher's combination test** sums $-2 \log(p_i)$ and compares the result to a chi-squared distribution. It is exact under independence and powerful when many alternatives are present, but its null distribution is wrong under dependence.
- **Simes' test** checks whether any ordered p-value $p_{(k)}$ falls below αk/m. It is exact under independence, valid under positive regression dependence on a subset (PRDS), and at most $\alpha H_m$ anti-conservative under arbitrary dependence, where $H_m = 1 + \frac{1}{2} + \cdots + \frac{1}{m}$ is the harmonic number.
 
The proof of the Simes bound under arbitrary dependence uses a neat scoring argument with Markov's inequality: assign each p-value a score based on which Simes threshold it falls below, then show that the total score exceeds 1 whenever Simes rejects.
 
### Arc 2 — Multiple Testing Procedures and FDR Control (Notes 3–6)
 
With global null tests in hand, the course shifts to the identification problem: given m hypotheses, which specific ones should be rejected?
 
**Notes 3** introduces the multiple testing framework — a procedure maps p-values $(p_1, \dots, p_m)$ to a discovery set $D \subseteq \{1, \dots, m\}$ — and the error metrics used to evaluate it. The family-wise error rate (FWER) controls the probability of any false rejection. The false discovery rate (FDR) controls the expected proportion of false discoveries. The **Holm–Bonferroni procedure** is presented as a step-down method that iteratively applies Bonferroni and peels off the most significant p-value, controlling FWER under arbitrary dependence while being strictly more powerful than vanilla Bonferroni.
 
**Notes 3–4** then develop the **Benjamini–Hochberg (BH) procedure**, the central algorithm of the course. BH finds the largest k such that $p_{(k)} \le \frac{\alpha k}{m}$ and rejects the k smallest p-values. The key intuition: for any threshold t, the number of false discoveries among p-values below t is roughly $m_0 \cdot t$ (since true null p-values are uniform), and the total number of rejections is observed. BH chooses the largest t for which this estimated false discovery proportion stays below α. Under independence, $\mathrm{FDR} \le \alpha \cdot \frac{m_0}{m}$; under PRDS, the same bound holds; under arbitrary dependence, a logarithmic penalty of Hₘ appears.
 
A critical subtlety is that **FDR control does not imply stable FDP**. The notes construct a stark example: if all m p-values are identical (perfect dependence), BH controls FDR at level $\alpha$, but the false discovery proportion is either 0 or 1 — never close to α in any single realization.
 
**Notes 5** addresses the conservatism of BH when many nulls are false. If the true null proportion $\pi_0 = \frac{m_0}{m}$ is substantially less than 1, BH underuses its FDR budget. The **Storey-type estimator** $\hat{\pi}_0(\gamma) = \frac{\#\{p_i > \gamma\}}{m(1-\gamma)}$ estimates $\pi_0$ from the data, and an adaptive BH procedure runs BH at level $\frac{\alpha}{\hat{\pi}_0}$, recovering the lost power. The notes carefully explain why dependence between $\hat{\pi}_0$ and the rejection decisions makes the theoretical analysis delicate, and how a data-splitting approach (using p-values above γ to estimate $\pi_0$ and p-values below γ for rejection) with a +1 correction to prevent $\hat{\pi}_0$ = 0 restores finite-sample FDR control.
 
**Notes 6** extends beyond binary reject/don't-reject decisions to post-discovery inference. Once BH has selected a set of discoveries, a researcher typically wants confidence intervals for the selected effects — but constructing intervals after selection inflates the **false coverage rate (FCR)**. The solution: for each selected hypothesis, construct a confidence interval at level 1 − $\frac{qR}{m}$ (wider than the marginal level), where R is the number of BH rejections. Under independence, this controls FCR at level q. The argument connects BH's step-up threshold to an FCR-controlling coverage adjustment: after selection, each interval is widened just enough to compensate for the selection.
 
### Arc 3 — Nonparametric and Conditional Testing (Notes 7–8)
 
The third arc moves from parametric p-values to **permutation and randomization tests**, which construct null distributions directly from data without distributional assumptions.
 
**Notes 7** develops three canonical settings for testing independence between X and Y. When both marginals are known, one can simulate directly from the product distribution. When only the X-marginal is known, one resamples X from its known distribution while holding Y fixed. When neither marginal is known, one permutes the X-labels. In each case, the observed test statistic and the resampled statistics are exchangeable under the null, and the permutation p-value $p = \frac{1 + \#\{\tilde{T}_b \ge T\}}{1 + M}$ is super-uniform. The +1 in numerator and denominator ensures the p-value is never zero.
 
**Notes 8** tackles **conditional independence testing** — $H_0: X \perp Y \mid Z$ — which is substantially harder. The local permutation strategy clusters observations with similar Z and permutes X within clusters. This is exact when Z is discrete and approximately valid when clusters are small enough that P(X|Z) is nearly constant within each cluster. But there is an intrinsic tradeoff: smaller clusters improve validity but reduce permutations and power. In high dimensions (Z ∈ ℝᵖ with large p), the curse of dimensionality makes it impossible to find small, well-populated clusters, and fully nonparametric conditional independence testing becomes fundamentally difficult. The notes survey three alternatives — parametric modeling, conditional randomization tests that model P(X|Z), and local permutation — each with its own modeling burden.
 
### Arc 4 — Selective Inference, Knockoffs, and Conformal Prediction (Notes 10–16)
 
The final arc addresses two modern frontiers: valid inference after data-driven model selection, and distribution-free predictive inference.
 
**Notes 10–11** develop **post-selection inference for the Lasso**. The Lasso's KKT conditions imply that the selection event — which variables are active and with what signs — can be written as a polyhedral constraint {y : Ay ≤ b}. This geometric characterization enables three correction strategies: data splitting (select on one half, infer on the other), multiplicity correction (Bonferroni over all possible selections), and selective inference (condition on the selection event and use the resulting truncated Gaussian distribution for inference). The randomized Lasso introduces external noise W to the optimization, decoupling the selection from the response Y and enabling cleaner conditional inference.
 
**Notes 14–15** present the **Model-X knockoffs framework** for FDR-controlled variable selection. The idea is to construct fake copies X̃₁, …, X̃ₚ of the covariates that are pairwise exchangeable with the originals — swapping Xⱼ and X̃ⱼ does not change the joint distribution — but that are conditionally independent of Y given X. If Xⱼ is truly null, then Xⱼ and X̃ⱼ are equally likely to be selected, so selected knockoffs serve as negative controls. The estimated FDP at any threshold is simply the number of selected knockoffs divided by the number of selected originals. The key theoretical property is that, conditional on the maximum importance statistics, the "coin flips" deciding whether each null variable or its knockoff is selected are independent Bernoulli(1/2) random variables, which yields FDR control. The Gaussian knockoff construction (where X ~ N(μ, Σ) and the augmented matrix has off-diagonal block Σ − D) and the sequential conditional construction for general distributions are both presented.
 
**Notes 12–13** and **Note 16** develop **conformal prediction**, a distribution-free framework for constructing prediction intervals. The setup is minimal: given exchangeable data (X₁, Y₁), …, (Xₙ, Yₙ), (Xₙ₊₁, Yₙ₊₁), define a score function s(Xᵢ, Yᵢ) measuring prediction error (e.g., |Yᵢ − f̂(Xᵢ)|). By exchangeability, the scores are exchangeable, so the test score Sₙ₊₁ is equally likely to be any rank among {S₁, …, Sₙ₊₁}. The split conformal prediction interval is f̂(Xₙ₊₁) ± q̂, where q̂ is the ⌈(1 − α)(n + 1)⌉-th smallest calibration score. This guarantees P(Yₙ₊₁ ∈ C(Xₙ₊₁)) ≥ 1 − α for any data distribution and any regression algorithm — accuracy of the model affects interval width, not validity.
 
The notes also cover cross-validation for tuning parameter selection, the **Jackknife+** (which uses leave-one-out models to avoid the instability of the classical jackknife, with a coverage guarantee of 1 − 2α via a tournament argument), **weighted conformal prediction** for covariate shift (using importance weights dQₓ/dPₓ to achieve approximate conditional coverage under distribution shift), and **conformalized quantile regression (CQR)**, which estimates conditional quantiles and adapts interval width to heteroskedasticity, achieving a "best of both worlds" guarantee: marginal coverage always, and approximate conditional coverage when the quantile estimates are accurate. A hardness result shows that exact conditional coverage is impossible without strong assumptions when Pₓ is nonatomic.
 
---
 
## Projects
 
### Project 0 — Real Data Critique: "Lung Cancer Incidence Decreases with Elevation"
 
A critical evaluation of [Simeonov & Himmelstein (2015)](https://doi.org/10.7717/peerj.705), who claim that county-level lung cancer incidence in the Western U.S. decreases with elevation, interpreted as evidence that oxygen is an inhaled carcinogen.
 
The critique identifies several layers of implicit multiple testing that are unaccounted for in the original paper. The authors test elevation associations across four cancer sites (lung, breast, colorectal, prostate), apply Bonferroni at α = 0.05/4, and find that only lung cancer survives — which is valid as far as it goes. However, this Bonferroni correction is applied *after* model selection: best subset regression with elevation forced into all candidate models and model size chosen by BIC, plus a parallel Lasso analysis. Each candidate model implicitly tests a different conditional specification for the elevation coefficient, and the selection of the best-fitting model introduces post-selection bias that classical t-tests cannot account for. Additional robustness analyses (stratification by smoking terciles, state-stratified meta-analysis, age/sex subgroups, elevation-substitution models) further expand the effective hypothesis space without corresponding multiplicity adjustments.
 
The report also examines modeling assumptions — linearity, county-level homogeneity, the SUTVA assumption for geographically adjacent counties, and temporal stability of covariates — and the statistical consequences of collinearity among predictors. The conclusion is that the paper documents a robust ecological association, but the reported p-values and confidence intervals should be interpreted cautiously given the uncontrolled post-selection inference throughout the pipeline.
 
📄 **Files:** `Project0/Real_Data_Critique.pdf`, `Project0/Lung cancer incidence decreases with elevation.pdf`
 
---
 
### Project 1 — Hypothesis Testing on Chicago Taxi Data (2014 vs. 2019)
 
A large-scale multiple testing study using 400,000 Chicago taxi rides to detect neighborhood-level changes in customer behavior between 2014 and 2019. Rather than treating the data analysis as an end in itself, the project uses the taxi data as a testbed for studying the methodological tradeoffs inherent in high-dimensional observational inference.
 
The study is organized around three binary outcomes, each designed to be robust to common confounders like fare inflation and traffic changes:
 
1. **Rush-hour share** — the proportion of rides during peak commute hours, invariant to changes in overall demand.
2. **Cash payment share** — payment preference, unconfounded by fare levels.
3. **Tipping propensity** — whether any tip is left among non-cash rides, avoiding the selection bias from unrecorded cash tips.
 
Two hypothesis family designs are compared at each granularity level. **Family 1** tests one hypothesis per community area (~77 neighborhoods), while **Family 2** crosses neighborhoods with five time-of-day bins (~385 cells), increasing scientific resolution at the cost of a five-fold multiplicity burden and smaller per-cell sample sizes.
 
The testing framework applies BH and BY (Benjamini–Yekutieli, valid under arbitrary dependence) corrections, and systematically varies the minimum cell-size threshold nₘᵢₙ to study the tradeoff between power (retaining more neighborhoods) and reliability (filtering low-volume cells that produce erratic p-values). The project finds that raising nₘᵢₙ from 0 to 400 dramatically reduces the number of BH discoveries in Family 2 — from 105 to 38 — confirming that many small-cell discoveries are variance-driven.
 
For validation, the project implements two placebo tests: within-year random splits (which should produce no real signal) and year-label permutation nulls (which destroy the year structure). It also runs subsampling stability analysis, computing the Jaccard overlap between full-sample and subsample discovery sets. P-value diagnostics (histograms, QQ-plots against uniformity, KS tests) confirm that the null p-values behave as expected and that the signals are concentrated in a left-skewed mode near zero.
 
Additional analyses compare three p-value engines — pooled z-tests, covariate-adjusted logistic regression (controlling for month and weekend effects), and permutation tests — and find that they produce broadly concordant results, reinforcing the robustness of the detected behavioral shifts.
 
📄 **Files:** `Project1/Hypothesis_Testing.pdf`, `Project1/project_guideline.pdf`
 
---
 
### Project 2 — Prediction-Powered Inference and Adaptive Subgroup Discovery
 
An expository and research project built around [Angelopoulos et al. (2023), "Prediction-Powered Inference"](https://arxiv.org/abs/2301.09633). The first half formalizes the PPI framework: given a small labeled dataset of size n and a large unlabeled dataset of size N ≫ n with ML predictions, the PPI estimator combines the imputed estimate from unlabeled data with a rectifier — an empirical correction for prediction bias computed on the labeled data. The resulting confidence intervals are valid regardless of the ML model's accuracy, and narrower than classical intervals when the model is good (specifically, when σ²(f−Y) < σ²(Y)).
 
The second half identifies and addresses a gap in the original framework: **PPI assumes the estimand is pre-specified**, but in practice researchers often want to discover which subgroups exhibit the largest effects — an adaptive selection step that destroys the rectifier's unbiasedness (the "winner's curse"). The project develops two strategies to restore validity:
 
**Sample splitting for PPI:** The labeled data is partitioned into a selection set (used to fit a CART tree on residuals Yᵢ − f(Xᵢ), discovering regions of interest) and an inference set (used to compute PPI estimates within each discovered region). Because selection and inference use disjoint data, the rectifier is unbiased by construction. The split ratio ρ acts as a tuning parameter mediating the tradeoff between selection quality and interval width.
 
**Randomized inference for PPI:** An alternative that avoids splitting by injecting Gaussian noise εᵢ into the labels, using the noisy Zᵢ = Yᵢ + εᵢ for selection, and extracting an inference residual Wᵢ = Yᵢ − ηZᵢ that is provably independent of the selection event (where η = σ²(Y)/(σ²(Y) + τ²)). The PPI estimator is then computed using Wᵢ in place of Yᵢ, and its conditional variance is (1 − η)σ²(Y)/nᵣ + σ²(f)/Nᵣ. The project derives conditions under which randomized inference has lower variance than splitting.
 
The project extends the sample-splitting approach to a **multi-region discovery pipeline** with BH-based FDR control: PPI p-values are computed for each discovered region, adjusted via Benjamini–Hochberg, and confidence intervals are reported only for the selected regions. Simulations validate coverage, interval width, selection accuracy, and the optimal split ratio ρ* across varying signal strengths.
 
📄 **Files:** `Project2/Valid Adaptive Subgroup Discovery for Prediction-Powered Inference.pdf`, `Project2/Angelopoulos, Prediction Powered Inference, 2023.pdf`
 
---
 
## Repository Structure
 
```
stat27850/
├── Lecture/
│   ├── notes1.pdf       Overfitting, hypothesis testing, multiple testing intro
│   ├── notes2.pdf       Global null tests: Bonferroni, Fisher, Simes
│   ├── notes3.pdf       Simes bounds, Holm–Bonferroni, BH introduction
│   ├── notes4.pdf       BH: FDR control under independence, PRDS, arbitrary dependence
│   ├── notes5.pdf       Adaptive BH, Storey estimator, π₀ estimation
│   ├── notes6.pdf       FCR control, post-discovery confidence intervals
│   ├── notes7.pdf       Permutation and randomization tests
│   ├── notes8.pdf       Conditional independence testing, local permutations
│   ├── notes10.pdf      Post-selection inference for the Lasso
│   ├── notes11.pdf      Selective inference: truncated Gaussians
│   ├── notes12.pdf      Conformal prediction fundamentals
│   ├── notes13.pdf      Split conformal, cross-validation
│   ├── notes14.pdf      Model-X knockoffs and FDR control
│   ├── notes15.pdf      Knockoff constructions, feature importance statistics
│   └── notes16.pdf      Jackknife+, weighted conformal, CQR, conditional coverage
├── Project0/
│   ├── Lung cancer incidence decreases with elevation.pdf
│   └── Real_Data_Critique.pdf
├── Project1/
│   ├── project_guideline.pdf
│   └── Hypothesis_Testing.pdf
├── Project2/
│   ├── Angelopoulos, Prediction Powered Inference, 2023.pdf
│   └── Valid Adaptive Subgroup Discovery for Prediction-Powered Inference.pdf
└── README.md
```
 
