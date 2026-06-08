## 1. Non-Linear Functional Forms and Interpretation (Week 5)

The sources detail four main functional forms, focusing on how the coefficient ($\beta_1$) is interpreted, especially regarding marginal returns and growth.

|Functional Form|Equation|$\beta_1$ Interpretation|Key Marginal Return Insight|
|:--|:--|:--|:--|
|**Level-Level**|$y= \beta_0 +\beta_1x+v$|Effect of **level of x** on **level of y**.|Standard Marginal Effect.|
|**Level-Log**|$y= \beta_0 +\beta_1 \ln(x)+v$|Measures the marginal effect of **growth of x** on **y**.|If $\beta_1 > 0$, consistent with positive and decreasing marginal return (without a maximum/minimum).|
|**Log-Level**|$\ln y= \beta_0 +\beta_1 (x)+v$|Measures the (marginal) effect of **x** on **growth of y**.|Marginal return increases with increasing $y$. If $\beta_1 < 0$, marginal return is negative and decreasing (in $y$).|
|**Log-Log**|$\ln y= \beta_0 +\beta_1 \ln(x)+v$|Measures the (marginal) effect of **growth of x** on **growth of y**. **This $\beta_1$ is the elasticity.**|Marginal return increases with increasing $y/x$ ratio if $\beta_1 > 0$.|

### Quadratic Forms

Quadratic functional forms are used when you expect **"turning points"** in the regression.

1. $y= \beta_0 +\beta_1x + \beta_2 x^2 +v$.
2. The regression curve is **U shaped** if $\hat{\beta}_1 < 0$ and $\hat{\beta}_2 > 0$ (marginal effect initially negative, then positive).
3. The regression curve is **Inverse U shaped** if $\hat{\beta}_1 > 0$ and $\hat{\beta}_2 < 0$ (marginal effect initially positive, then negative).
4. Quadratic forms can also capture the effect of one variable ($x_2$) on the marginal effect of another ($x_1$) using an interaction term (e.g., $x_1x_2$).

## 2. Elasticity (Week 5)

Elasticity is a crucial concept, especially for social scientists.

- **Definition:** It answers: If the explanatory variable ($x$) increases by 1% point, what would be the effect on the proportion increase (or decrease) in $y$?.
- **Key Properties:** Elasticity is **Scale Invariant** and a **Unit free number**.
- **Measurement:** Unless otherwise mentioned, elasticities are always estimated at the **sample mean**.
- **Log-Log Form:** In the $\ln y= \beta_0 +\beta_1 \ln x +v$ model, the estimated coefficient $\hat{\beta}_1$ _is_ the elasticity.

## 3. Model Selection and Testing (Week 5)

When choosing between models, you need specific tests depending on the relationship between the specifications:

### Chow Test (Groupwise Structural Stability)

The Chow Test determines if there is a difference in the regression parameters between two distinct subsamples (e.g., males vs. females, before vs. after a policy change).

- **Hypothesis:** The **Null Hypothesis ($H_0$)** is that **there is no difference between the two samples** (the pooled sample model is true).
- **Procedure:** You run the **Restricted regression** (pooled sample) and the **Unrestricted regression** (individual subsamples, giving RSS1 and RSS2). You must first test for the equality of sub-sample variance.
- **Outcome:** If $H_0$ is **rejected**, policy decisions should be based on **separate regressions** for each sub-sample. If $H_0$ is accepted, you can use the single pooled regression.

### Choosing Between Functional Forms (Ramsey RESET)

The Ramsey **RESET (Regression Equation Specification Error Test)** is used to check the functional form (e.g., linear vs. polynomial).

- **Procedure:** Estimate the linear model. Then, use the estimated $y$ values ($\hat{y}$) and run an augmented regression: $y= \beta_0 +\beta_1x_1 +\beta_2x_2+ \delta*(\hat{y}^2)+v$.
- **Hypothesis:** Perform a t-test on $H_0: \delta=0$. Acceptance of the null suggests the original linear form is correct.
- **Note:** RESET is **NOT a test for omitted variables**.

### Choosing Between Non-Nested Models

Non-nested models are those where neither model is a restricted version of the other (e.g., $y$ on $x$ vs. $y$ on $\ln x$).

- **Adjusted $\mathbf{R^2}$:** Adjusted $R^2$ **can be used** to compare non-nested models if the dependent variable ($y$) is the same in both models.
- **F Test Based Approach (Composite Model):** You combine both functional forms into a **composite model** (e.g., $y= \beta_0 +\beta_1x_1 + \beta_2x_2+\beta_3 \ln x_1 +\beta_4\ln x_2+v$). Then, test $H_0: \beta_3=\beta_4 =0$. If accepted, Model 1 may be the specification. The problem is that running the reverse test might also result in acceptance, meaning **No clear winner!**.

---

## 4. Prediction (Week 6)

Prediction involves estimating $y$ given a value $x_0$. There are two types:

### Mean Prediction vs. Individual Prediction

|Concept|What is Predicted?|Calculation of $\hat{y}$|Key Difference|
|:--|:--|:--|:--|
|**Mean Prediction**|The conditional mean of Y, $E(y|x_0)$.|$\hat{y}_0 = \hat{\beta}_0 + \hat{\beta}_1 x_0$|
|**Individual Prediction**|A specific value of Y, $y$.|$\hat{y}_0 = \hat{\beta}_0 + \hat{\beta}_1 x_0$|Focuses on a single instance of the outcome.|

### Properties of Predictors

1. **Unbiasedness:** The predicted value ($\hat{y}_0$) is an **unbiased estimator** of the conditional mean $E(y|x_0)$. Similarly, the predicted value is an **unbiased estimator** of the actual value in individual prediction.
2. **Variance/Error:**
    - The **variance and standard error (SE) of individual prediction is higher** compared to mean prediction.
    - Consequently, the confidence interval (CI) for **individual prediction is wider**.
    - For both types of prediction, the **error is lowest if the prediction is made at the mean of $x$** (i.e., $x_0 = \bar{x}$). The further $x_0$ is from the sample mean, the higher the error.

## 5. Outliers and Influential Points (Week 6)

In regression analysis, it's crucial to distinguish between different types of unusual data points:

|Term|Definition|Impact|
|:--|:--|:--|
|**Outlier**|A value of $y$ that does not follow the general pattern of other $y$ values. It is an **unusual y value**.|May or may not be influential.|
|**High Leverage**|The values of the independent variable $x$ that are either very high or very low (far from the mean of $x$).|May or may not be influential.|
|**Influential Point**|A point whose inclusion (or exclusion) significantly **affects the regression results** (e.g., slope coefficient, prediction, hypothesis testing).|Usually a combination of unusual y and high leverage.|

### Numerical Measures for Influence

1. **Leverage ($h_{ii}$):** This measures how far $x_i$ is from its mean. A standard rule suggests that an observation has high leverage if $h_{ii} > 3(k+1)/n$, where $k$ is the number of slope parameters and $n$ is the number of observations.
2. **Studentized t:** Used to detect **outliers (unusual y values)**. Any observation with a Studentized t value $> 3$ is typically considered an outlier.
3. **Cook’s D ($D_i$):** The most popular measure for influential points, encompassing both leverage and studentized t.
    - If $D_i$ value is **more than $4/n$** ($n$=number of observations), the point is usually considered an **influential point**. Including this point will affect the regression.

## 6. Dummy Variables (Week 6)

Dummy variables allow the use of categorical data in regression.

### Creating Dummies and Interpretation

- **The Collinearity Rule:** If there are $m$ categories (e.g., Male/Female, which is $m=2$), you must create only **$(m-1)$ dummy variables** (e.g., one dummy for Male, $D=1$, and Female, $D=0$). Using $m$ dummies (e.g., $D_1$ for Male, $D_2$ for Female) leads to the problem of collinearity.
- **Baseline Category:** The category assigned the value 0 is called the **"baseline"** or reference category.
- **Intercept Shift:** In a model like $wage = \beta_0 + \beta_1 \cdot educ + \beta_3 \cdot D$, the parameter $\beta_3$ picks up the effect of the dummy group relative to the baseline. If $\beta_3 = 4$ (and D=1 for Male, D=0 for Female), males earn, on average, 4 units more than females, given the same level of education.

### Interaction Dummies (Slope Shift)

Dummy variables can affect the slope as well as the intercept.

- **Model:** $wage = \beta_0 + \beta_1 \cdot educ + \beta_3 \cdot D + \beta_4 \cdot (D \cdot educ)$.
- **Interpretation:**
    - For the **baseline group** (e.g., Female, $D=0$): $wage = \beta_0 + \beta_1 \cdot educ$. The slope is $\beta_1$.
    - For the **dummy group** (e.g., Male, $D=1$): $wage = (\beta_0 + \beta_3) + (\beta_1 + \beta_4) \cdot educ$. The intercept is $(\beta_0 + \beta_3)$ and the slope is $(\beta_1 + \beta_4)$.
    - $\beta_3$ measures the difference in intercepts, and $\beta_4$ measures the difference in slopes (marginal effects).

### Piecewise Linear Regression

This uses dummies to model different slopes beyond a cutoff point, known as the **"knot"**.

- **Model:** $y_i = \beta_0 + \beta_1 x_i + \beta_2 D_i(x_i - x^*) + v_i$.
- $D_i = 1$ if $x_i > x^_$, and $0$ otherwise. $x^_$ is the known cutoff value (the knot).
- This is a piecewise approximation of a non-linear function.

### Oaxaca-Blinder Decomposition

This technique addresses _why_ mean outcomes (like wages) differ between two groups (e.g., Male vs. Female). The difference in mean wage ($\bar{w}_M - \bar{w}_F$) can be decomposed into two effects:

1. **Difference in Coefficients (Bias within labor market):** Difference due to the fact that the coefficients ($\beta$'s) are different, meaning even with the same endowments, the outcome would differ.
2. **Difference in Endowments (Bias within society):** Difference due to the fact that the average endowments (e.g., average level of education) are different.

---
---
----
---

In simple terms, **the overall topic we are discussing is how to build, test, and use Multiple Linear Regression (MLR) models when the relationships are complex or involve categorical data.** This is an extension of standard MLR techniques.

Here is a summary of the two main blocks of concepts covered in the sources:

---

## 1. Dealing with Complex Relationships (Non-Linearity and Model Choice)

Standard linear regression assumes a straight-line relationship (Level-Level). This first section teaches you how to handle real-world relationships that are not straight lines, and how to scientifically choose the best model.

### A. Non-Linear Functional Forms (Week 5)

We use mathematical transformations (like taking the natural logarithm, "$\ln$") of the dependent variable ($y$) and/or the independent variable ($x$) to capture non-linear effects.

- **Level-:

---

## 1. Dealing with Complex Relationships (Non-Linearity and Model Choice)

Standard linear regression assumes a straight-line relationship (Level-Level). This first section teaches you how to handle real-world relationships that are not straight lines, and how to scientifically choose the best model.

### A. Non-Linear Functional Forms (Week 5)

We use mathematical transformations (like taking the natural logarithm, "$\ln$") of the dependent variable ($y$) and/or the independent variable ($x$) to capture non-linear effects.

- **Level-Log and Log-Level:** These forms measure the effect of _growth_ (percentage change) in one variable on the _level_ or _growth_ of the other.
- **Log-Log:** This is the most important log form, as the coefficient ($\beta_1$) directly measures **Elasticity** (the effect of a 1% change in $x$ on the percentage change in $y$). Elasticity is a unit-free and scale-invariant measure.
- **Quadratic Forms:** These are used when you expect a **"turning point"** (U-shaped or Inverse U-shaped relationship) where the marginal effect changes sign. They can also capture interaction effects between variables.

### B. Choosing the Right Model (Specification Tests)

When you have multiple ways to specify a model, you need statistical tests to choose the best one:

- **Ramsey RESET:** This test specifically checks if your chosen **functional form** (e.g., linear vs. quadratic) is adequate.
- **Chow Test:** This test checks for **Groupwise Structural Stability**. It determines if the regression relationship (the intercept and/or slopes) is statistically different between two distinct sub-samples (e.g., if one equation works for everyone, or if you need separate equations for men and women).
- **Non-Nested Models:** For models where the dependent variables are the same but the explanatory variables differ (e.g., $y$ on $x$ versus $y$ on $\ln x$), you can compare them using the **Adjusted $R^2$**.

## 2. Using Models for Prediction and Handling Data Issues

This section focuses on using the estimated model to make forecasts and identifying problematic data points.

### A. Prediction (Week 6)

Prediction involves using the estimated equation to forecast $y$ for a given $x$.

- **Mean Prediction:** Predicting the _average_ outcome ($E(y|x_0)$).
- **Individual Prediction:** Predicting a _specific single value_ of the outcome ($y$).
- **Key Insight:** Both types of prediction are **unbiased**, but the **variance and confidence intervals for individual prediction are always wider** than for mean prediction. Also, prediction error is **lowest** when you predict at the **mean of $x$** and increases the further $x$ is from its mean.

### B. Outliers and Influential Points (Week 6)

These terms describe unusual observations that can skew your regression results.

- **Outlier:** An unusual $y$ value (far from the predicted pattern).
- **High Leverage:** An unusual $x$ value (far from the mean of $x$).
- **Influential Point:** A point whose inclusion significantly changes the regression results (usually a combination of high leverage and being an outlier).
- **Cook's D:** The numerical measure used to detect influential points. If $D_i > 4/n$, the point is generally considered influential.

### C. Dummy Variables (Week 6)

Dummy variables (which take values like 0 or 1) allow us to include **categorical variables** (like gender, race, or yes/no questions) as explanatory variables in a regression.

- **Intercept Shift:** A basic dummy shifts the **intercept** of the regression line, showing the difference in the outcome between the dummy group and the **baseline category** (the group assigned 0).
- **Collinearity Rule:** To avoid perfect multicollinearity, if there are $m$ categories, you must only create **$m-1$ dummy variables**.
- **Interaction Dummies (Slope Shift):** By multiplying a dummy variable by a continuous variable (e.g., $D \cdot \text{education}$), you can test if the dummy group has a different **slope** (marginal effect) compared to the baseline group.
- **Oaxaca-Blinder Decomposition:** This technique explains the difference in mean outcomes (like wages) between two groups by separating the difference caused by **endowments** (difference in average characteristics like education) and the difference caused by **coefficients** (discrimination or differences in how the labor market rewards those characteristics).