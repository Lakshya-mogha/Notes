# Probability & Statistics: Key Concepts Summary

## 1. Variance and Standard Deviation (The Basics)
---

* **What is Variance?** It measures how "spread out" a set of data is from its average (mean). A small variance means the data is tightly clustered. A large variance means it's spread far apart.
* **Mean (Expectation):** This is the average value, written as $E[X]$ or $\mu$.
* **Key Formulas for Variance $Var(X)$:**
    1.  **The Definition:** $Var(X) = E[(X - E[X])^2]$ [cite: 2, 6]
    2.  **The Shortcut (USE THIS ONE):** This formula is almost always easier to calculate: [cite: 5]
        $$Var(X) = E[X^2] - (E[X])^2$$ [cite: 5, 11]
* **Standard Deviation ($std$ or $\sigma$):** This is just the square root of the variance[cite: 3]. It's used because its units are the same as the data.
    $$\sigma = \sqrt{Var(X)}$$
* **Key Properties of Variance:**
    * $Var(aX + b) = a^2 Var(X)$[cite: 89]. (Notice the '$b$' disappears! Adding a constant shifts the data but doesn't change its spread [cite: 87, 88]).
    * $Var(c) = 0$[cite: 94]. (The variance of a constant is zero, as it has no spread [cite: 93]).

## 2. Key Distributions: Formulas to Memorize
---

This table summarizes the most important distributions from your notes[cite: 31, 41, 44, 47].

| Distribution                   | $E[X]$ (Mean)                      | $Var(X)$ (Variance, $\sigma^2$)          | Notes                                                         |
| :----------------------------- | :--------------------------------- | :--------------------------------------- | :------------------------------------------------------------ |
| **Uniform $U(a, b)$**          | $\frac{a+b}{2}$ [cite: 14, 33]     | $\frac{(b-a)^2}{12}$ [cite: 16, 34]      | All outcomes from $a$ to $b$ are equally likely.              |
| **Bernoulli $Ber(p)$**         | $p$ [cite: 42, 62]                 | $p(1-p)$ [cite: 43, 65]                  | A single trial (e.g., one coin flip). $p$ = prob of success.  |
| **Binomial $B(n, p)$**         | $np$ [cite: 45]                    | $np(1-p)$ [cite: 46]                     | $n$ independent Bernoulli trials (e.g., $n$ coin flips).      |
| **Poisson $Pois(\lambda)$**    | $\lambda$ [cite: 48, 70]           | $\lambda$ [cite: 49, 77]                 | Events in a fixed time/space. **Mean = Variance.**            |
| **Exponential $Exp(\lambda)$** | $\frac{1}{\lambda}$ [cite: 21, 36] | $\frac{1}{\lambda^2}$ [cite: 19, 26, 37] | Time *between* Poisson events.                                |
| **Normal $N(\mu, \sigma^2)$**  | $\mu$ [cite: 39]                   | $\sigma^2$ [cite: 30, 40]                | The "bell curve." The parameters *are* the mean and variance. |

## 3. Moments and Central Moments
---

This is just a fancy name for $E[X^k]$[cite: 95].

### $k$-th Moment
* **Definition:** $E[X^k]$ [cite: 96, 99]
* **1st Moment ($k=1$):** $E[X]$ (This is just the **mean**)[cite: 98, 100].
* **2nd Moment ($k=2$):** $E[X^2]$. (You need this for the variance shortcut).

### $k$-th Central Moment [cite: 116]
* **Definition:** $E[(X - \mu)^k]$, where $\mu = E[X]$[cite: 117, 118].
* **1st Central Moment ($k=1$):** $E[X - \mu] = 0$[cite: 121, 123].
* **2nd Central Moment ($k=2$):** $E[(X - \mu)^2]$ (This is the **definition of variance**)[cite: 122, 124].

### How to Calculate Moments (Example)

> Let X take the values 2, 3, 4 with probabilities 0.1, 0.7, and 0.2[cite: 50, 104].

* **$E[X]$ (1st Moment):** $(2 \times 0.1) + (3 \times 0.7) + (4 \times 0.2) = 3.1$[cite: 52, 57].
* **$E[X^2]$ (2nd Moment):** $(2^2 \times 0.1) + (3^2 \times 0.7) + (4^2 \times 0.2) = 9.9$[cite: 56].
* **$E[X^3]$ (3rd Moment):** $(2^3 \times 0.1) + (3^3 \times 0.7) + (4^3 \times 0.2) = 32.5$ [cite: 107-109].
* **$E[(X-\mu)^3]$ (3rd Central Moment):**
    * Use $\mu = 3.1$[cite: 170].
    * $(2-3.1)^3 \times 0.1 + (3-3.1)^3 \times 0.7 + (4-3.1)^3 \times 0.2 = 0.012$[cite: 171, 172].

## 4. Markov and Chebyshev Inequalities
---

These are used to find the *probability* of an event when you don't know the full distribution.

### Markov's Inequality [cite: 154]
* **When to Use:** When you **only know the mean ($\mu$)** and the variable is **non-negative** ($X \ge 0$)[cite: 156].
* **Formula:** $P(X \ge a) \le \frac{\mu}{a}$ (where $\mu = E[X]$)[cite: 162].
* **Example:** A biased coin ($p=0.1$) is flipped 200 times. Find the bound on $P(X \ge 120)$[cite: 163, 164].
    1.  Find the mean: $\mu = np = 200 \times 0.1 = 20$[cite: 165].
    2.  Identify '$a$': $a = 120$.
    3.  Apply formula: $P(X \ge 120) \le \frac{20}{120} = \frac{1}{6}$[cite: 166].

### Chebyshev's Inequality [cite: 126]
* **When to Use:** When you know **both the mean ($\mu$) and variance ($\sigma^2$)**. It's more powerful than Markov[cite: 138].
* **Key Formulas:**
    1.  $P(|X - \mu| \ge \epsilon) \le \frac{\sigma^2}{\epsilon^2}$ [cite: 132] ($\epsilon$ is the *distance* from the mean).
    2.  $P(|X - \mu| \ge k\sigma) \le \frac{1}{k^2}$ [cite: 134] ($k$ is the *number of standard deviations*).
* **Example:** Fair coin tossed 50 times. Find the bound on $P(X \ge 35 \text{ or } X \le 15)$[cite: 145].
    1.  Find mean: $\mu = np = 50 \times 0.5 = 25$[cite: 146].
    2.  Find variance: $\sigma^2 = np(1-p) = 50 \times 0.5 \times 0.5 = 12.5$[cite: 147].
    3.  Find standard deviation: $\sigma = \sqrt{12.5}$[cite: 148].
    4.  The question is asking for the probability of being 10 or more units away from the mean (since $|35-25|=10$ and $|15-25|=10$)[cite: 149]. This is $P(|X-25| \ge 10)$.
    5.  You can find $k$: $k = \frac{\epsilon}{\sigma} = \frac{10}{\sqrt{12.5}} \approx 2.828$[cite: 150, 151].
    6.  Apply formula 2: $P(|X-25| \ge 2.828\sigma) \le \frac{1}{k^2} = \frac{1}{2.828^2} \approx 0.125$[cite: 152, 153].

## 5. Moment Generating Functions (MGFs)
---

* **What is it?** A function $M_X(s) = E[e^{sX}]$[cite: 175, 176].
* **WHY do we use it?** It's a "machine" to *generate* moments[cite: 188].
* **THE MOST IMPORTANT FORMULA FOR MGFs:**
    $$E[X^k] = \frac{d^k}{ds^k} M_X(s) \Big|_{s=0}$$ [cite: 190]
    * This means:
    * To find $E[X]$ (1st moment), take the **1st derivative** of $M_X(s)$, then **plug in $s=0$**[cite: 190].
    * To find $E[X^2]$ (2nd moment), take the **2nd derivative** of $M_X(s)$, then **plug in $s=0$**[cite: 190].
* **Example (Bernoulli $Ber(p)$):** [cite: 201]
    * **MGF:** $M_X(s) = E[e^{sX}] = (p \times e^{s \times 1}) + ((1-p) \times e^{s \times 0}) = pe^s + 1 - p$[cite: 203, 204].
    * **Find $E[X]$:**
        * 1st Derivative: $\frac{dM}{ds} = pe^s$[cite: 207].
        * Plug in $s=0$: $pe^0 = p$. So $E[X] = p$[cite: 208].
    * **Find $E[X^2]$:**
        * 2nd Derivative: $\frac{d^2M}{ds^2} = pe^s$[cite: 211].
        * Plug in $s=0$: $pe^0 = p$. So $E[X^2] = p$[cite: 211].
    * **Find Variance:**
        * $Var(X) = E[X^2] - (E[X])^2 = p - p^2 = p(1-p)$[cite: 65].

## 6. Exam Strategy
---
1.  Breathe.
2.  Memorize the **Summary Table (Section 2)**.
3.  Remember the **Variance Shortcut:** $Var(X) = E[X^2] - (E[X])^2$.
4.  Remember the **MGF Moment Formula:** $E[X^k] = (\text{k-th derivative of MGF}) \text{ at } s=0$.
5.  Know **when** to use Markov (mean only, non-negative) vs. Chebyshev (mean and variance).