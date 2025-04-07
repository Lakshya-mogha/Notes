## 🔹 Introduction to Machine Learning

Machine Learning (ML) is a subset of **Artificial Intelligence (AI)** that enables computers to learn from data and make predictions or decisions **without being explicitly programmed**.

### 📌 Key Features of Machine Learning
- Learns patterns **directly from data**.
- Does **not require explicit programming**.
- Improves performance **over time** as more data is available.

### **Types of Machine Learning**

1️⃣ **Supervised Learning** - Model learns from labeled data (e.g., Regression, Classification).  
2️⃣ **Unsupervised Learning** - Model finds hidden patterns in data (e.g., Clustering, PCA).  
3️⃣ **Reinforcement Learning** - Model learns by trial and error with rewards (e.g., Robotics, Games).  

---

## 🔹 Motivating Example

A dataset containing:

- **Features**: Inhabitants, Percentage Below Income, Percentage Unemployed.
- **Target**: Number of murders per million inhabitants.

Given training examples:

```text
x1 = [587000, 16.5, 6.2] ; y1= 11.2
x2 = [643000, 20.5, 6.4] ; y2= 13.4
x3 = [635000, 26.3, 9.3] ; y3= 40.7
```

The goal is to find a function **f(x)** that maps inputs to outputs.

---

## 🔹 Supervised Learning: Regression
### 📌 Regression Problem Setup
- **Feature Matrix (X)**: Contains multiple independent variables (e.g., area, bedrooms, age).
- **Target Variable (y)**: The dependent variable we want to predict.
- **Weight Vector (w)**: Represents the influence of each feature.

A simple linear regression equation:

$$y = w_1 x_1 + w_2 x_2 + ... + w_n x_n + b$$

---

## 🔹 Solving the Regression Problem
### 📌 Least Squares Error Minimization

To minimize the prediction error, we use the **Least Squares Method**:

$$ J(w) = \sum (y_i - \hat{y}_i)^2 $$

where $y_i$ is the actual value and $\hat{y}_i$ is the predicted value.

### 📌 Gradient Descent

Gradient Descent optimizes weights by updating them iteratively:

\[ w = w - \alpha \frac{\partial J}{\partial w} \]

where \( \alpha \) is the learning rate.

---

## 🔹 Bias Variable in Regression
### 📌 The Problem

In a simple model:

\[ y = w_1 x_1 + w_2 x_2 \]

If we force the model to pass through the origin, it becomes inflexible.

### 📌 The Solution

Introduce a **bias term** (b):

\[ y = w_1 x_1 + w_2 x_2 + b \]

This improves the model’s ability to fit data.

---

## 🔹 Python Implementation: Linear Regression
```python
import numpy as np
import matplotlib.pyplot as plt
from sklearn.linear_model import LinearRegression
from sklearn.model_selection import train_test_split

# Sample data: Area, Bedrooms, and Age vs. Price
X = np.array([[500, 1, 10], [700, 2, 8], [800, 2, 5], [1000, 3, 4], [1500, 3, 2]])
y = np.array([100, 130, 150, 180, 260])

# Split data
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

# Train model
model = LinearRegression()
model.fit(X_train, y_train)

# Predict
predicted_price = model.predict(np.array([[1200, 3, 3]]))
print(f"Predicted Price: ${predicted_price[0] * 1000:.2f}")
```

---

## 🔹 Summary

✅ **Machine Learning**: Learning patterns from data.  
✅ **Regression**: Predicting continuous values using models.  
✅ **Least Squares & Gradient Descent**: Methods to minimize prediction error.  
✅ **Bias Term**: Improves model flexibility.  
✅ **Python Example**: Implemented a regression model for house price prediction.  
