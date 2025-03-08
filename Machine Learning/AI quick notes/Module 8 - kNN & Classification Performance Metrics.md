## 🔹 Learning Objectives
- Enhancing **k-Nearest Neighbors (kNN)**
- Understanding **kNN for Regression**
- Exploring **Computational Complexity of kNN**
- Learning **Performance Metrics for Classification**

---

## 🔹 Improving kNN Classification
### 📌 Effect of Outliers
- **k=1** is highly sensitive to outliers.
- **Solution:** Increase k to smooth decision boundary.

### 📌 Choosing the Right k
- **Low k** → Overfitting (Highly unstable boundary)
- **Optimal k** → Balanced decision boundary
- **High k** → Oversmoothing (All classified into the majority class)
- **Solution:** Use **Cross-Validation** to select the best k.

### 📌 Handling Tie Cases
- Use **odd k**.
- Assign class **randomly**.
- Choose class with **higher prior probability**.

### 📌 Probabilistic kNN

Instead of hard classification, return probabilities for each class:

\[ P(y_i) = \frac{k_i}{k} \]

where **k_i** is the number of neighbors in class **y_i**.

---

## 🔹 kNN for Regression

Instead of classification, kNN can **predict continuous values**:

1️⃣ Find the **k closest points**.

2️⃣ Compute the **mean value** of their target outputs.

### 📌 Python Example: kNN Regression
```python
from sklearn.neighbors import KNeighborsRegressor
import numpy as np

# Sample data (Experience vs. Salary)
X = np.array([[1], [3], [5], [7], [9]])
y = np.array([20, 50, 80, 100, 130])

# Train kNN Regression model
knn_reg = KNeighborsRegressor(n_neighbors=3)
knn_reg.fit(X, y)

# Predict salary for 6 years of experience
print("Predicted Salary:", knn_reg.predict([[6]]))
```

---

## 🔹 Computational Complexity of kNN
### 📌 Brute Force kNN
- **Training Time:** \( O(1) \)
- **Prediction Time:** \( O(k \cdot n \cdot d) \)
- **Prediction Space:** \( O(1) \)

### 📌 k-d Tree Optimized kNN
- **Training Time:** \( O(d \cdot n \log n) \)
- **Prediction Time:** \( O(k \cdot \log n) \)
- **Space Complexity:** \( O(d \cdot n) \)

---

## 🔹 Performance Metrics for Classification
### 📌 Confusion Matrix

A **table summarizing classification results**:

| Actual | Predicted: Positive | Predicted: Negative |
|--------|---------------------|---------------------|
| Positive | True Positive (TP) | False Negative (FN) |
| Negative | False Positive (FP) | True Negative (TN) |

### 📌 Accuracy

\[ \text{Accuracy} = \frac{TP + TN}{TP + TN + FP + FN} \]

### 📌 Precision

\[ \text{Precision} = \frac{TP}{TP + FP} \]

*High precision minimizes False Positives.*

### 📌 Recall (Sensitivity)

\[ \text{Recall} = \frac{TP}{TP + FN} \]

*High recall minimizes False Negatives.*

### 📌 F1-Score

\[ \text{F1} = 2 \times \frac{\text{Precision} \times \text{Recall}}{\text{Precision} + \text{Recall}} \]

### 📌 Python Example: Classification Metrics
```python
from sklearn.metrics import confusion_matrix, classification_report
from sklearn.neighbors import KNeighborsClassifier
from sklearn.model_selection import train_test_split

# Sample Data (X: Features, y: Labels)
X = np.array([[1], [2], [3], [4], [5], [6], [7], [8], [9]])
y = np.array([0, 1, 0, 1, 0, 1, 0, 1, 0])

# Train/Test Split
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

# Train kNN Classifier
knn = KNeighborsClassifier(n_neighbors=3)
knn.fit(X_train, y_train)

# Predictions
y_pred = knn.predict(X_test)

# Print Metrics
print(confusion_matrix(y_test, y_pred))
print(classification_report(y_test, y_pred))
```

---

## 🔹 Area Under the ROC Curve (AUC-ROC)
- **ROC Curve**: Plots **True Positive Rate (TPR)** vs. **False Positive Rate (FPR)**.
- **AUC (Area Under Curve)**: Measures classification performance.
  - **AUC = 1** → Perfect model
  - **AUC = 0.5** → Random guessing

### 📌 Python Example: AUC-ROC
```python
from sklearn.metrics import roc_curve, auc
import matplotlib.pyplot as plt

# Compute ROC curve
fpr, tpr, _ = roc_curve(y_test, knn.predict_proba(X_test)[:,1])
roc_auc = auc(fpr, tpr)

# Plot ROC Curve
plt.plot(fpr, tpr, label=f'AUC = {roc_auc:.2f}')
plt.xlabel('False Positive Rate')
plt.ylabel('True Positive Rate')
plt.title('ROC Curve')
plt.legend()
plt.show()
```

---

## 🔹 Summary

✅ **kNN Classification**: Effect of outliers, optimal k, probabilistic kNN.  
✅ **kNN Regression**: Uses **mean of k-nearest targets** for prediction.  
✅ **Computational Complexity**: Brute-force vs. **k-d tree** optimization.  
✅ **Classification Metrics**: Confusion Matrix, Accuracy, Precision, Recall, F1-Score.  
✅ **AUC-ROC**: Measures classification performance.
