## 1. Omitted Variable Bias (OVB)

### 🎯 Core Concept

Omitted Variable Bias (OVB) happens when you **leave out an important variable** from your regression model[cite: 302, 308].

* **True Model (What you *should* run):**
    $y = \beta_{0} + \beta_{1}x_{1} + \beta_{2}x_{2} + \beta_{3}x_{3} + u$ [cite: 291]
* **Your Model (What you *actually* run, omitting $x_{3}$):**
    $y = \tilde{\beta}_{0} + \tilde{\beta}_{1}x_{1} + \tilde{\beta}_{2}x_{2} + v$ [cite: 300]

### ⚠️ Consequences: Why It's Bad

If the omitted variable ($x_{3}$) is important, your estimated coefficients ($\tilde{\beta}_{1}$ and $\tilde{\beta}_{2}$) will be **BIASED**[cite: 306]. This means your results are wrong and misleading.

### ❓ When Does Bias Happen? (The 2 Conditions)

You *only* get bias if the omitted variable ($x_3$) meets **BOTH** of these conditions:

1.  **Condition 1:** The omitted variable $x_3$ must actually affect $y$ (i.e., its true coefficient $\beta_{3} \ne 0$)[cite: 352].
2.  **Condition 2:** The omitted variable $x_3$ must be **correlated** with at least one of your included variables (e.g., $x_1$)[cite: 353, 359].

If *either* of these is not true, there is no bias.

### ⬆️⬇️ Direction of the Bias

This is a classic exam question. The bias is the *effect of the omitted variable* multiplied by the *correlation between the omitted and included variables*[cite: 344].

**Bias in $\tilde{\beta}_{1} = \beta_{3} \times \text{Corr}(x_{1}, x_{3})$**

Use this table to find the direction:

| Effect of Omitted on Y ($\beta_{3}$) | Correlation of Omitted ($x_3$) with Included ($x_1$) | Direction of Bias |
| :--- | :--- | :--- |
| **Positive (+)** | **Positive (+)** | **Positive (Upward) Bias** [cite: 345] |
| **Positive (+)** | **Negative (-)** | **Negative (Downward) Bias** [cite: 346] |
| **Negative (-)** | **Positive (+)** | **Negative (Downward) Bias** [cite: 347] |
| **Negative (-)** | **Negative (-)** | **Positive (Upward) Bias** [cite: 348] |

**Example from PDF:**
* **Model:** `wage` on `education`, but you **omit `ability`** [cite: 303-305, 372].
* **Condition 1:** Does `ability` affect `wage`? Yes, likely **positive** ($\beta_{3} > 0$)[cite: 374].
* **Condition 2:** Is `ability` correlated with `education`? Yes, likely **positive**[cite: 373].
* **Result:** (Positive) x (Positive) = **Positive (Upward) Bias**[cite: 375]. Your model will *overestimate* the effect of education on wages.

---

## 2. Measurement Error

### 🎯 Core Concept

This happens when you have the *right* variable, but your data for it is **measured incorrectly**[cite: 14, 15]. Example: Using "reported income" when the true value is "actual income"[cite: 16].

There are two different cases with very different results.

### Case 1: Error in the Dependent Variable ($y$)

* **True Model:** $y^{*} = \beta_{0} + \beta_{1}x_{1} + u$ [cite: 21] (where $y^{*}$ is the *true* value)
* **Your Model:** You use $y$, where $y = y^{*} + e_{0}$ ($e_0$ is the measurement error) [cite: 23-25].
* **Your new error term is:** $(u + e_0)$[cite: 28].

#### ⚠️ Consequences (Error in $y$)

* **The "Good" News:** If the measurement error $e_0$ is random (i.e., not correlated with your $x$ variables), your coefficients ($\beta_{0}$, $\beta_{1}$) are **STILL UNBIASED**[cite: 36, 40].
* **The "Bad" News:** The variance of your new error term is bigger ($Var(u + e_0) > Var(u)$)[cite: 41, 42].
    * This causes your **Standard Errors to increase**[cite: 43].
    * This makes your **t-statistics smaller**[cite: 44].
    * **Bottom Line:** You are *less likely* to find that your variables are statistically significant, even if they truly have an effect[cite: 44].

### Case 2: Error in an Independent Variable ($x$)

* **True Model:** $y = \beta_{0} + \beta_{1}x_{1}^{*} + u$ [cite: 68] (where $x_{1}^{*}$ is the *true* value)
* **Your Model:** You use $x_1$, where $x_1 = x_{1}^{*} + e_{0}$ ($e_0$ is the measurement error)[cite: 70, 71].
* **Your new error term is:** $(u - \beta_{1}e_0)$[cite: 76].

#### ⚠️ Consequences (Error in $x$)

* **The "Bad" News:** This is a much worse problem. Your measured variable $x_1$ is *by definition* correlated with the new error term (because both contain $e_0$).
* **Classical Errors-in-Variables (CEV) Assumption:** This is a key term. It assumes the measurement error $e_0$ is **uncorrelated** with the *true* value $x_{1}^{*}$[cite: 107].
* **Bottom Line:** Under the CEV assumption, your coefficient $\hat{\beta}_{1}$ will be **BIASED**[cite: 116, 123].
* **Direction of Bias:** The bias is **towards zero**. This is called **Attenuation Bias**. Your model will *underestimate* the true effect of $x_1$.

### 📊 Summary: Measurement Error Cheat Sheet

| Error Location | Are Coefficients Biased? | Main Consequence |
| :--- | :--- | :--- |
| **Dependent ($y$)** | **No** (if error is random) | **Larger Standard Errors** (Lower t-stats, less significance) [cite: 43, 44] |
| **Independent ($x$)**| **Yes** (almost always) | **Biased Coefficients** (Bias is *towards zero* - "Attenuation Bias") [cite: 116, 123] |

---

## 3. Proxy Variables

### 🎯 Core Concept

A proxy variable is a **solution to Omitted Variable Bias**[cite: 143].

When you can't get data for an unobserved variable (like `ability`), you use a **proxy** for it (like `IQ`)[cite: 144, 155].

* **Problem:** $y = \beta_{0} + \beta_{1}x_{1} + \beta_{3}q + u$ (where $q$ is unobserved)[cite: 141].
* **Solution:** You add a proxy variable $z$ that is related to $q$.
* **Your New Model:** $y = \alpha_{0} + \beta_{1}x_{1} + \alpha_{1}z + v$[cite: 201, 213].

### ✅ What Makes a *Good* Proxy? (The 2 Conditions)

1.  **Redundancy:** The proxy $z$ must only affect $y$ *through* the unobserved variable $q$. If you *could* control for $q$, $z$ would become irrelevant[cite: 169].
2.  **No "Leftover" Correlation:** Let's say $q = \theta_{0} + \theta_{1}z + r$ (where $r$ is the part of $q$ that $z$ *doesn't* explain)[cite: 181, 197]. This leftover part $r$ **must not** be correlated with your other variables (like $x_1$)[cite: 181].

### ⚠️ Consequences of Using a Proxy

* **If it's a *good* proxy (meets both conditions):**
    * Your estimates for $\beta_{1}$ (and other $x$ variables) are now **UNBIASED**[cite: 214]. You have solved the OVB problem[cite: 238]!
    * **Bonus:** Error variance is *lower* than in the omitted variable model, giving you *smaller standard errors* and higher t-stats[cite: 243, 244].
    * **Warning:** You **cannot** interpret the coefficient on the proxy itself ($\alpha_1$). Its only job is to "soak up" the bias[cite: 202].

* **If it's an *imperfect* proxy (fails condition 2):**
    * This means the "leftover" part $r$ *is* correlated with your other variables[cite: 256, 257].
    * **Consequence:** Your coefficients will **still be BIASED**[cite: 274]. An imperfect proxy does not fully solve the problem.

---