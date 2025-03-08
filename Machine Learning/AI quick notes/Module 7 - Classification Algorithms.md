

## 🔹 Learning Objectives
- Understanding **Classification**
- Implementing **Naïve Bayes Classifier**
- Introduction to **k-Nearest Neighbors (kNN) Classifier**

---

## 🔹 What is Classification?
### 📌 Definition

Classification is a supervised learning task where the goal is to predict **discrete class labels** for given input features.

- If **C = 2**, it is **Binary Classification** (e.g., Spam or Not Spam).
- If **C > 2**, it is **Multi-Class Classification** (e.g., Dog, Cat, or Bird).

### 📌 Steps in Classification

1️⃣ **Collect labeled training data**  
2️⃣ **Split into training and test sets**  
3️⃣ **Choose a classification algorithm**  
4️⃣ **Train the model on the training set**  
5️⃣ **Test the model on unseen data**  

---

## 🔹 Naïve Bayes Classifier
### 📌 What is Naïve Bayes?

A **probabilistic classifier** based on **Bayes' Theorem**, assuming **independent features**:

\[ P(Y|X) = \frac{P(X|Y) P(Y)}{P(X)} \]

Where:

- \( P(Y|X) \) → Posterior Probability (probability of class Y given features X)
- \( P(X|Y) \) → Likelihood (probability of X given class Y)
- \( P(Y) \) → Prior Probability (probability of class Y before seeing data)
- \( P(X) \) → Evidence (total probability of data X)

### 📌 Python Example: Naïve Bayes for Classification
```python
from sklearn.naive_bayes import GaussianNB
from sklearn.model_selection import train_test_split
from sklearn.metrics import accuracy_score
import numpy as np

# Sample dataset (Age, Income) → (Buys Computer: Yes/No)
X = np.array([[25, 50000], [30, 60000], [35, 80000], [40, 100000], [45, 120000]])
y = np.array([0, 0, 1, 1, 1])  # 0 = No, 1 = Yes

# Split into training and test sets
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

# Train Naïve Bayes model
model = GaussianNB()
model.fit(X_train, y_train)

# Predict on test data
y_pred = model.predict(X_test)
print("Accuracy:", accuracy_score(y_test, y_pred))
```

### 📌 Pros & Cons of Naïve Bayes

✅ **Fast & Efficient** for large datasets  
✅ **Works well for categorical data**  
❌ **Assumes independence** between features (not always true)  
❌ **Performs poorly with small datasets**  

---

## 🔹 k-Nearest Neighbors (kNN) Classifier
### 📌 What is kNN?

kNN is a **distance-based classifier** that assigns a class label based on **majority voting** of the **k-nearest neighbors**.

### 📌 Steps in kNN Classification

1️⃣ **Choose k (number of neighbors)**  
2️⃣ **Calculate distances** from test point to all training points  
3️⃣ **Select k closest points**  
4️⃣ **Assign class label based on majority vote**  

### 📌 Python Example: kNN Classifier
```python
from sklearn.neighbors import KNeighborsClassifier

# Create kNN classifier with k=3
knn = KNeighborsClassifier(n_neighbors=3)
knn.fit(X_train, y_train)

# Predict on test data
y_pred_knn = knn.predict(X_test)
print("kNN Accuracy:", accuracy_score(y_test, y_pred_knn))
```

### 📌 Distance Metrics in kNN
- **Euclidean Distance** (default): \( d = \sqrt{(x_1 - x_2)^2 + (y_1 - y_2)^2} \)
- **Manhattan Distance**: \( d = |x_1 - x_2| + |y_1 - y_2| \)

### 📌 Pros & Cons of kNN

✅ **Simple & Intuitive**  
✅ **Works well for non-linear decision boundaries**  
❌ **Computationally expensive for large datasets**  
❌ **Sensitive to outliers**  

---

## 🔹 Summary

✅ **Classification predicts discrete class labels**  
✅ **Naïve Bayes** is a **probabilistic classifier** using **Bayes' Theorem**  
✅ **kNN** is a **distance-based classifier** using **majority voting**  
