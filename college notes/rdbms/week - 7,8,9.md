This is a high-speed review focusing on the core skills needed for your exam:

1.  **Finding Candidate Keys**
2.  **Determining Normal Forms**
3.  **Decomposing Relations**

---

## 1. The Core Skill: Attribute Closure (Finding $\alpha^+$)

The **closure** of an attribute set ($\alpha^+$) is *everything you can determine* if you start by knowing $\alpha$. This is the key to everything else.

### How-To: Find a Closure

Use this algorithm:

1.  Start with `result = { ...your starting attributes... }`.
2.  Scan your list of Functional Dependencies (FDs).
3.  If the left side (determinant) of an FD is fully contained in your `result` set, add the right side (dependent) to your `result` set.
4.  Repeat until no new attributes can be added.

### Example: Find $(AG)^+$

* **Relation:** $R = (A, B, C, G, H, I)$ [cite: 1193, 1289]
* **FDs:** $F = \{A \rightarrow B, A \rightarrow C, CG \rightarrow H, CG \rightarrow I, B \rightarrow H\}$ [cite: 1194, 1290-1294]

**Steps:**
1.  `result = {A, G}`
2.  Scan FDs:
    * `A \rightarrow B`: You have `A`. Add `B`.
    * `result = {A, G, B}`
    * `A \rightarrow C`: You have `A`. Add `C`.
    * `result = {A, G, B, C}` [cite: 1315-1316]
3.  Scan FDs again:
    * `CG \rightarrow H`: You have `C` and `G`. Add `H`.
    * `result = {A, G, B, C, H}` [cite: 1318-1319]
    * `CG \rightarrow I`: You have `C` and `G`. Add `I`.
    * `result = {A, G, B, C, H, I}` [cite: 1320-1321]
    * `B \rightarrow H`: You have `B`. Add `H` (already there).
4.  Scanning again adds nothing new.
5.  **Final Closure:** $(AG)^+ = \{A, B, C, G, H, I\}$

---

## 2. How to Find Candidate Keys (CK)

* **Superkey:** A set of attributes $\alpha$ whose closure $\alpha^+$ contains **all** attributes in the relation $R$ [cite: 1045-1046, 1328-1329].
    * *Example:* `AG` is a superkey because $(AG)^+ = R$.
* **Candidate Key (CK):** A **minimal** superkey. If you remove any attribute, it's no longer a superkey [cite: 1047, 1050-1051, 1369].

### How-To: Check if `AG` is a CK

1.  **Is it a superkey?**
    * Yes. We found $(AG)^+ = R$[cite: 1357, 1368].
2.  **Is a subset a superkey?**
    * Check $(A)^+$: `result = {A}` $\rightarrow$ `{A, B, C}` $\rightarrow$ `{A, B, C, H}`. This is **not R**. So `A` is not a superkey[cite: 1344, 1346, 1348, 1370].
    * Check $(G)^+$: `result = {G}`. This is **not R**. So `G` is not a superkey[cite: 1354, 1371].
3.  **Conclusion:** `AG` is a superkey, and no subset is. Therefore, **`AG` is a Candidate Key**[cite: 1362].

### Definitions
* **Prime Attribute:** Any attribute that is part of *any* Candidate Key.
* **Non-Prime Attribute:** Any attribute that is *not* part of *any* Candidate Key[cite: 237].

---

## 3. The Normal Forms (Hierarchy & Violations)

### 1NF (First Normal Form)
* **Rule:** All attribute domains must be **atomic** (indivisible) [cite: 153-156].
* **Violation:** Using multi-valued attributes (like `{phone_number}`) or composite attributes (like `address(street, city)`) [cite: 157, 161-165].
* **Fix:** Decompose. Create a new table for the multi-valued attribute[cite: 199, 225].
    * `instructor(ID, name, ...)`
    * `inst_phone(ID, phone_number)` [cite: 225]

### 2NF (Second Normal Form)
* **Rule:** Must be in 1NF + **No Partial Dependencies**[cite: 233, 235, 237].
* **Violation:** A **non-prime** attribute depends on *only a part* of a Candidate Key[cite: 237, 245]. (Only possible if CK has multiple attributes).
* **Example:**
    * $R = (emp\_id, proj\_no, emp\_name, proj\_name, hours)$
    * $CK = \{emp\_id, proj\_no\}$ [cite: 242]
    * FDs: `emp_id \rightarrow emp_name` [cite: 240] and `proj_no \rightarrow proj_name`[cite: 241].
    * **Violation!** `emp_name` (non-prime) depends on `emp_id` (part of the CK)[cite: 245, 247].
* **Fix:** Pull the partial dependencies into new tables [cite: 256-257].
    * `employee(emp_id, emp_name, salary)` [cite: 270]
    * `project(proj_no, proj_name, building)` [cite: 273]
    * `emp_proj_hrs(emp_id, proj_no, hours)` [cite: 274-275]

### 3NF (Third Normal Form)
* **Rule:** Must be in 2NF + **No Transitive Dependencies** [cite: 288-289, 291].
* **Violation:** A **non-prime** attribute depends on *another non-prime* attribute[cite: 291].
    * Path: $CK \rightarrow NonPrime\_A \rightarrow NonPrime\_B$
* **Example:**
    * $R = (ID, name, dept\_name, building, budget)$ [cite: 305]
    * $CK = \{ID\}$ [cite: 306]
    * FDs: `ID \rightarrow dept_name` and `dept_name \rightarrow building, budget`[cite: 306].
    * **Violation!** `ID` (CK) determines `dept_name` (non-prime), which in turn determines `building` and `budget` (non-primes). This is a transitive dependency [cite: 320-322].
* **Fix:** Pull the transitive dependency into a new table [cite: 323-324].
    * `instructor(ID, name, salary, dept_name)` [cite: 389]
    * `department(dept_name, building, budget)` [cite: 386]

### BCNF (Boyce-Codd Normal Form)
* **Rule:** The simplest, strictest rule. For **every** non-trivial FD $\alpha \rightarrow \beta$, $\alpha$ **must be a superkey**[cite: 417, 428].
* **How to Check:** For *every FD* on your list, find the closure of the left side. See if it's a superkey.
* **Example:**
    * $R = (s\_ID, i\_ID, dept\_name)$ [cite: 394]
    * $CKs = \{s\_ID, i\_ID\}$ and $\{s\_ID, dept\_name\}$ [cite: 399]
    * FD: `i_ID \rightarrow dept_name`[cite: 397].
    * **Check:** Is `i_ID` a superkey? No.
    * **Violation!** The relation is **not in BCNF**[cite: 444, 448].
* **Fix:** Decompose on the violating FD.
    * `in_dept(i_ID, dept_name)` [cite: 484-485]
    * `stud_dept(s_ID, i_ID)` [cite: 482]

---

## 4. How to Check Your Decomposition

Is a decomposition "good"? Check for two things:

### A. Lossless-Join
* **Goal:** You can re-join the tables to get the original data *without* creating fake rows[cite: 45, 936].
* **The Test (for $R \rightarrow R_1, R_2$):**
    1.  Find common attributes: $R_1 \cap R_2$.
    2.  Find the closure of the common attributes: $(R_1 \cap R_2)^+$.
    3.  It is **LOSSLESS** if this closure contains **all** attributes of $R_1$ **OR** **all** attributes of $R_2$ [cite: 106-108, 1234-1235].
* **Example:**
    * $R(A, B, C)$ with $F = \{A \rightarrow B, B \rightarrow C\}$[cite: 111, 1242].
    * Decomposition: $R_1(A, B)$ and $R_2(B, C)$[cite: 1256].
    * Common attributes: $\{B\}$[cite: 1261].
    * Closure: $(B)^+ = \{B, C\}$.
    * Check: Does $\{B, C\}$ contain all of $R_1(A, B)$? No.
    * Check: Does $\{B, C\}$ contain all of $R_2(B, C)$? Yes.
    * **Result:** This is a **LOSSLESS** decomposition[cite: 1257].

### B. Dependency Preservation
* **Goal:** You can still check all original FDs using *only* the new, smaller tables [cite: 46, 1268-1269].
* **The Test:** For each original FD $\alpha \rightarrow \beta$, is there at least one new table $R_i$ that contains **all** attributes of $\alpha \cup \beta$ (both sides)?
* **Example:**
    * $R(A, B, C)$ with $F = \{A \rightarrow B, B \rightarrow C\}$[cite: 126].
    * Decomposition: $R_1(A, B)$ and $R_2(B, C)$[cite: 132].
    * Check `A \rightarrow B`: All attributes (`A`, `B`) are in $R_1$. (Good) [cite: 134].
    * Check `B \rightarrow C`: All attributes (`B`, `C`) are in $R_2$. (Good) [cite: 134].
    * **Result:** This is **DEPENDENCY PRESERVING**[cite: 135].

**Key Tradeoff:**
* You can *always* get a Lossless, Dependency-Preserving decomposition into **3NF**.
* You can *always* get a Lossless decomposition into **BCNF**, but you *might lose* (not preserve) some dependencies[cite: 486].

---

## 5. Other Key Topics

### Canonical Cover
* **Goal:** Find the *simplest equivalent set* of FDs[cite: 1443].
* **Steps:**
    1.  **Union:** Combine FDs with the same left side. (`A \rightarrow B` and `A \rightarrow C` becomes `A \rightarrow BC`) [cite: 1185, 1517].
    2.  **Remove Extraneous LHS:** For `AB \rightarrow C`, is `B` extra? Check if `A \rightarrow C` is implied by the other FDs.
    3.  **Remove Extraneous RHS:** For `A \rightarrow BC`, is `C` extra? Check if `A \rightarrow C` is implied by other FDs (e.g., via transitivity: `A \rightarrow B` and `B \rightarrow C`) [cite: 1528-1529].
* See the full example on page 50 of the "NF" PDF [cite: 1512-1531].

### 4NF (Fourth Normal Form)
* **Rule:** Must be in BCNF + **No non-trivial Multivalued Dependencies (MVDs)**, unless the left side is a superkey [cite: 651, 654-656].
* **MVD:** $\alpha \rightarrow\rightarrow \beta$. Means $\alpha$ determines a *set* of $\beta$ values, and this set is *independent* of other attributes[cite: 619, 623, 1557, 1561].
* **Problem:** `ID \rightarrow\rightarrow child_name` and `ID \rightarrow\rightarrow phone_number`. Storing in one table `(ID, child_name, phone_number)` forces you to create rows for every possible combination, causing redundancy [cite: 1542-1549].
* **Fix:** Decompose the independent MVDs into their own tables[cite: 1572, 1577].
    * `inst_child(ID, child_name)` [cite: 1540]
    * `inst_phone(ID, phone_number)` [cite: 1540]