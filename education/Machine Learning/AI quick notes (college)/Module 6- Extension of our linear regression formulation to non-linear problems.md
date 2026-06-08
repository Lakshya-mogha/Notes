## 🔹 Learning Objectives

- Extending **Linear Regression** to **Non-Linear Problems**
- Understanding **Overfitting & Underfitting**
- Implementing **Cross-Validation**
- Using **Regularization (L2 - Ridge Regression)**
- Applying **Feature Standardization**

---

## 🔹 Change of Basis: Extending Regression to Non-Linear Problems
### 📌 Why Change the Basis?

Sometimes, data is **not linear**, making **simple linear regression ineffective**. We can transform the data using **polynomial features**:

$$y = w_0 + w_1 x + w_2 x^2 + ... + w_p x^p $$

For example, a **quadratic model**:

$$y = w_0 + w_1 x + w_2 x^2 $$

### 📌 Python Example: Polynomial Regression
```python
import numpy as np
import matplotlib.pyplot as plt
from sklearn.preprocessing import PolynomialFeatures
from sklearn.linear_model import LinearRegression
from sklearn.pipeline import make_pipeline

# Sample data
X = np.array([1, 2, 3, 4, 5]).reshape(-1, 1)
y = np.array([2, 5, 10, 17, 26])

# Create polynomial regression model (degree=2)
model = make_pipeline(PolynomialFeatures(2), LinearRegression())
model.fit(X, y)

# Predict new values
X_new = np.array([[6]])
predicted_value = model.predict(X_new)
print(f"Predicted Value for x=6: {predicted_value[0]}")
```

---

## 🔹 Overfitting & Underfitting
- **Underfitting**: Model is too simple and does not capture patterns.
- **Overfitting**: Model memorizes training data but fails on new data.

To balance complexity, we adjust **polynomial degree (p):**

✅ **Low p** → High bias, low variance (Underfitting)  
✅ **High p** → Low bias, high variance (Overfitting)  

---

## 🔹 Cross-Validation
### 📌 Why Cross-Validation?
- Helps select the best **model complexity** (e.g., best polynomial degree).
- Prevents **overfitting on test data**.

### 📌 K-Fold Cross-Validation

1️⃣ Split data into **K** equal parts.

2️⃣ Train on **K-1** parts, validate on the remaining part.

3️⃣ Repeat for all **K** splits and average results.

```python
from sklearn.model_selection import cross_val_score
from sklearn.pipeline import make_pipeline

# Perform 5-fold cross-validation
model = make_pipeline(PolynomialFeatures(3), LinearRegression())
scores = cross_val_score(model, X, y, cv=5, scoring='neg_mean_squared_error')
print("Cross-validation MSE:", -scores.mean())
```

---

## 🔹 Regularization (L2 - Ridge Regression)

To prevent overfitting, we add a **penalty term** to the regression model:

$$ J(w) = \sum (y_i - \hat{y}_i)^2 + \lambda \sum w_i^2 $$

where \( lambda \) controls complexity:

✅ **High \( \lambda \)** → Reduces variance, increases bias (Simplifies model).  
✅ **Low \( \lambda \)** → Reduces bias, increases variance (Complex model).  

### 📌 Python Example: Ridge Regression
```python
from sklearn.linear_model import Ridge

# Create Ridge Regression model with lambda=1.0
ridge_model = Ridge(alpha=1.0)
ridge_model.fit(X, y)
print("Ridge Regression Coefficients:", ridge_model.coef_)
```

---

## 🔹 Feature Standardization

Features must be on the **same scale** for regularization techniques to work properly.

### 📌 Standardization Formula

$$x_{standardized} = \frac{x - \mu}{\sigma} $$

### 📌 Python Example: Standardizing Features
```python
from sklearn.preprocessing import StandardScaler

# Example dataset
features = np.array([[500, 20], [700, 25], [900, 30]])
scaler = StandardScaler()
scaled_features = scaler.fit_transform(features)
print("Standardized Features:", scaled_features)
```

---

## 🔹 Summary

✅ **Polynomial Regression** handles non-linear data.  
✅ **Overfitting & Underfitting** affect model performance.  
✅ **Cross-Validation** helps find optimal model complexity.  
✅ **Regularization (L2 - Ridge Regression)** prevents overfitting.  
✅ **Feature Standardization** ensures fair influence of features.  

[[Module 7 - Classification Algorithms]]
