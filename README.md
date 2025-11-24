
[![Cancer Research Software Hub](https://img.shields.io/badge/Back_to-Hub-blue)](https://github.com/younghhk/NCI)

# 📘 Handling Missing Data: MCAR, MAR, and MNAR



Missing data are rarely random noise. How you handle them depends on *why* values are missing. Three standard missingness mechanisms guide this reasoning:

* **MCAR**: Missing Completely At Random
* **MAR**: Missing At Random
* **MNAR**: Missing Not At Random

These concepts describe how the probability of missingness relates to observed and unobserved values. They are assumptions about the data-generating process rather than properties you can prove from the dataset.

---

# 1. MCAR — Missing Completely At Random

### What MCAR means

The probability a value is missing does not depend on anything in the dataset: not on the variable itself, and not on any other variables.

> Missingness behaves like a coin flip.
> People are missing for reasons completely unrelated to the study.

### Examples where MCAR might be plausible

* A lab machine malfunctioned on one day and affected all samples from that day.
* A random batch of questionnaires was misplaced.
* A technician accidentally skipped every 50th record.

### How to check (informally)

You cannot prove MCAR, but you can look for evidence *against* it.

**1. Create a missingness indicator**

For variable `Y`, define:

* `R_Y = 1` if Y is observed
* `R_Y = 0` if Y is missing

**2. Compare subjects with and without missingness**

Check whether other variables differ between `R_Y = 1` and `R_Y = 0`. Examples:

* Age distributions
* Disease severity
* Study site or year

Large differences suggest missingness is *not* MCAR.

**3. Optional: Little’s MCAR test**
A formal but limited test. It can reject MCAR, but cannot confirm MAR or MNAR.

### What methods are appropriate under MCAR

* **Complete-case analysis** gives unbiased estimates if MCAR holds.
* **Multiple imputation (MI)** is also fine and often improves precision.
* **Simple imputation** (mean, median) is generally discouraged for final inference.

---

# 2. MAR — Missing At Random

### What MAR means

The probability a value is missing may depend on *observed* data, but not on the unobserved value itself *after conditioning on observed variables*.

> Missingness is systematic, but the drivers of missingness are measured in your dataset.

### Examples where MAR might be reasonable

* Older adults are less likely to return for follow-up, and you have age.
* Outcome data are missing more often at smaller clinics, and you have clinic type.
* Patients with worse baseline health drop out, and you have baseline health variables.

### How to assess MAR in practice

Again, you cannot prove MAR, but you can gather evidence that supports its plausibility.

**1. Model missingness using observed covariates**

For variable `Y`:

```r
glm(R_Y ~ age + sex + comorbidity + site, family = binomial)
```

If missingness is clearly associated with observed variables, MAR is more plausible than MCAR.

**2. Explore subgroup patterns**

* Are missing rates higher in certain demographic groups?
* In certain sites or years?
* In certain severity strata?

If so, include those predictors in imputation models.

### Methods appropriate under MAR

* **Multiple imputation (MI)** using models that include

  * outcome
  * exposure
  * predictors of missingness
  * predictors of the variable being imputed

* **Maximum likelihood methods**
  Mixed models and joint models implicitly handle MAR if specified correctly.

* **Inverse probability weighting (IPW)**
  Weights observations based on the probability of being observed.

---

# 3. MNAR — Missing Not At Random

### What MNAR means

The probability a value is missing depends on the *unobserved* value itself, even after accounting for all observed variables.

> Missingness depends on what you did not see.

### Examples where MNAR may be realistic

* Higher earners are less likely to report income, even after adjusting for occupation and education.
* Patients with severe symptoms are more likely to drop out and fail to report outcomes.
* Biomarker values below the detection limit are recorded as missing.

### How to check for MNAR

You cannot detect MNAR from the observed data alone. You rely on:

* Substantive knowledge (for example, clinical reasoning)
* External data sources
* Patterns that remain unexplained even after conditioning on many covariates

Because MNAR is fundamentally untestable, the usual recommendation is to perform **sensitivity analyses** rather than rely on a single model.

### Possible strategies under MNAR

There is no one "correct" remedy, but common approaches include:

* **Sensitivity analyses**
  Explore how conclusions change under different plausible assumptions about the missing data.

* **Pattern-mixture models** or **selection models**
  More complex approaches that explicitly model missingness.

* **Creating a "missing" category for categorical variables**
  A pragmatic approach that may allow the model to capture informative missingness.
  Not appropriate for continuous variables.

* **Multiple imputation with caution**
  MI will not recover the true values under MNAR, but may still be used with sensitivity checks.

##  References
-	Rubin DB. Inference and Missing Data. Biometrika. 1976;63(3):581–592. (The foundational paper that defines MCAR, MAR, and MNAR.)
-	Little RJA, Rubin DB. Statistical Analysis with Missing Data. 3rd ed. Wiley; 2019. (Standard textbook covering all missingness mechanisms.)
-	Rubin DB. Multiple Imputation for Nonresponse in Surveys. Wiley; 1987. (The original book on MI.)
-	Little RJA. A Test of Missing Completely at Random for Multivariate Data with Missing Values. Journal of the American Statistical Association. 1988;83(404):1198–1202. (Introduces Little’s MCAR test.)
-	Raghunathan TE, Lepkowski JM, van Hoewyk J, Solenberger P. A multivariate technique for multiply imputing missing values using a sequence of regression models.
Survey Methodology. 2001;27(1):85-95.
- [Imputation in NHANES](https://ehsanx.github.io/EpiMethods/missingdata2.html) (A tutorial by Dr. M. Ehsan Karim on performing multiple imputation for NHANES and other complex survey data.)
