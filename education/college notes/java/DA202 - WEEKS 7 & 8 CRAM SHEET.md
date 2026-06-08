# WEEK 7: Control Statements

Week 7 is all about how you control the *flow* of your program. You're making decisions and repeating actions.

## 1. Decision Making: `if` Statements

This is the most basic way to make a decision.

### `if`
* **What it is:** Runs a block of code *only if* a condition is true.
* **Syntax:**
    ```java
    if (condition) {
      // statements to run if condition is true
    }
    ```

### `if-else`
* **What it is:** Runs one block of code if the condition is true, and a *different* block if it's false.
* **Syntax:**
    ```java
    if (condition) {
      // true block
    } else {
      // false block
    }
    ```

### Nested `if`
* **What it is:** An `if` statement *inside* another `if` statement. This is used for more complex, layered decisions.
* **Key Trap:** The `else` always pairs with the *closest* `if` that doesn't already have an `else`.
* **Example from slides**:
    ```java
    if (i == 10) {
        // First-level check
        if (j < 20) {
            a = b; // Only runs if i==10 AND j<20
        } else {
            a = c; // Only runs if i==10 AND j>=20
        }
    } else {
        a = d; // Only runs if i != 10
    }
    ```

### The `if-else-if` Ladder
* **What it is:** The *best* way to check a series of mutually exclusive conditions. As soon as one is true, it runs that block and *skips all the rest*.
* **Syntax & Example (from slides)**:
    ```java
    int month = 4; // April
    String season;

    if (month == 12 || month == 1 || month == 2) {
        season = "Winter";
    } else if (month == 3 || month == 4 || month == 5) {
        season = "Spring";
    } else if (month == 6 || month == 7 || month == 8) {
        season = "Summer";
    } else if (month == 9 || month == 10 || month == 11) {
        season = "Autumn";
    } else {
        season = "Bogus Month";
    }
    // At the end, season will be "Spring"
    ```

## 2. Decision Making: `switch` Statements

* **What it is:** A cleaner alternative to a long `if-else-if` ladder, but it only works for checking a variable against specific, constant values.
* **Works on:** `byte`, `short`, `int`, and `char`.
* **CRITICAL KEYWORD: `break`**
    * `break` stops the `switch` and jumps to the end.
    * **If you forget `break`:** The code will "fall through" and execute the *next* `case` as well, which is almost always a bug.
* **`default`:** This is the "else" block for a switch. It runs if *no other case matches*.

### `switch` Example (from slides)

This code will check the value of `i` in each loop.

```java
for (int i = 0; i < 3; i++) {
    switch (i) {
        case 0:
            System.out.println("i is zero");
            break; // Jumps out of the switch

        case 1:
            System.out.println("i is one");
            break; // Jumps out of the switch

        default:
            System.out.println("i is greater than 1");
            // No break needed here, it's the end anyway
    }
}
// --- Output ---
// i is zero
// i is one
// i is greater than 1