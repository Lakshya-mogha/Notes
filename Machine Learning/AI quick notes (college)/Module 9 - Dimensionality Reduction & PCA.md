## 🔹 Learning Objectives
- Understanding **Dimensionality Reduction**
- Addressing the **Curse of Dimensionality**
- Difference between **Feature Selection & Feature Extraction**
- **Principal Component Analysis (PCA)** for reducing dimensions
- Computing **PCA using Covariance Matrix & SVD**

---

## 🔹 Why Dimensionality Reduction?
### 📌 The Curse of Dimensionality
- In **high-dimensional spaces**, models require **more data** to learn effectively.
- **Overfitting increases** when dimensions grow without enough training data.
- **Computation becomes expensive**.

### 📌 How to Address It?

✅ **Feature Selection**: Choosing the most relevant features.

✅ **Feature Extraction**: Transforming data into a smaller set of important features (**PCA**).

---

## 🔹 Principal Component Analysis (PCA)
### 📌 What is PCA?

PCA is an **unsupervised technique** that:

- **Transforms data** into a new coordinate system.
- **Finds the directions (principal components) of maximum variance**.
- **Reduces dimensionality while preserving important patterns**.

### 📌 Key Properties of PCA
- **Unsupervised Learning**: Works without class labels.
- **Orthogonal Transformation**: Principal components are uncorrelated.
- **Variance Maximization**: Captures the most important data trends.

---

## 🔹 Computing PCA
### 📌 Method 1: Covariance Matrix Decomposition

1️⃣ **Center the data** (subtract the mean).

2️⃣ **Compute the covariance matrix** \( \Sigma \).

3️⃣ **Find eigenvectors & eigenvalues** of \( \Sigma \).

4️⃣ **Select the top principal components** (those with largest eigenvalues).

5️⃣ **Project data onto new basis**.

### 📌 Method 2: Singular Value Decomposition (SVD)
- **More efficient** than covariance method when **features > samples**.
- **Breaks data matrix into 3 matrices**: \( X = U S V^T \).
- **First k singular vectors** represent **top k principal components**.

### 📌 Python Example: PCA
```python
import numpy as np
import matplotlib.pyplot as plt
from sklearn.decomposition import PCA
from sklearn.preprocessing import StandardScaler

# Sample Data (2D for visualization)
X = np.array([[2, 3], [3, 4], [4, 5], [5, 6], [6, 7]])

# Standardize data
scaler = StandardScaler()
X_scaled = scaler.fit_transform(X)

# Apply PCA
pca = PCA(n_components=1)
X_pca = pca.fit_transform(X_scaled)

print("Explained Variance Ratio:", pca.explained_variance_ratio_)
print("Transformed Data:", X_pca)
```

---

## 🔹 PCA vs. Non-Linear Methods
- **PCA works well for linear structures**.
- **Kernel PCA** handles **non-linear** patterns but is more complex.

---

## 🔹 Summary

✅ **Dimensionality Reduction** improves performance by removing redundant features.  
✅ **Curse of Dimensionality** leads to overfitting & computational issues.  
✅ **PCA extracts the most important directions (variance) in data.**  
✅ **Two PCA computation methods**: Covariance Matrix & SVD.  

---

## 💡 Next Steps

Would you like to:

1️⃣ **Explore Kernel PCA for non-linear feature extraction?**  
2️⃣ **Learn LDA (Linear Discriminant Analysis) for supervised dimensionality reduction?**  
3️⃣ **Visualize PCA with high-dimensional datasets?**  

Let me know how you'd like to proceed! 🚀
