## 🔹 Learning Objectives
- Understanding **Clustering** in Unsupervised Learning
- Implementing **K-Means Clustering**
- Exploring **DBSCAN (Density-Based Clustering)**
- Comparing **K-Means vs. DBSCAN**

---

## 🔹 What is Clustering?

Clustering is the task of dividing data points into **groups** such that:

- Data points **within the same cluster** are **similar**.
- Data points **in different clusters** are **dissimilar**.

Clustering is an **unsupervised learning** technique, meaning it works **without labeled data**.

---

## 🔹 K-Means Clustering
### 📌 How K-Means Works

1️⃣ **Choose K (number of clusters)**.

2️⃣ **Randomly initialize K cluster centroids**.

3️⃣ **Assign each data point** to the nearest centroid.

4️⃣ **Update centroids** based on the mean of assigned points.

5️⃣ **Repeat until convergence** (centroids stop moving).

### 📌 Hard vs. Soft Clustering
- **Hard Clustering**: A data point belongs to **only one cluster**.
- **Soft Clustering**: A data point has a **probability of belonging to multiple clusters**.

### 📌 Python Example: K-Means Clustering
```python
from sklearn.cluster import KMeans
import numpy as np
import matplotlib.pyplot as plt

# Sample data
X = np.array([[1, 2], [1, 4], [1, 0], [10, 2], [10, 4], [10, 0]])

# Apply K-Means with K=2
kmeans = KMeans(n_clusters=2, random_state=42)
kmeans.fit(X)
labels = kmeans.labels_

# Plot clusters
plt.scatter(X[:, 0], X[:, 1], c=labels, cmap='viridis')
plt.scatter(kmeans.cluster_centers_[:, 0], kmeans.cluster_centers_[:, 1], marker='x', color='red', label='Centroids')
plt.legend()
plt.show()
```

### 📌 Limitations of K-Means

❌ **Sensitive to initial centroids** (different runs can give different results).

❌ **Assumes spherical clusters** (fails for non-circular shapes).

❌ **Needs K to be pre-defined**.

❌ **Poor handling of outliers**.

---

## 🔹 DBSCAN (Density-Based Clustering)
### 📌 How DBSCAN Works

1️⃣ **Define density parameters**: $\varepsilon$ (radius) and **MinPts** (minimum points in $\varepsilon$).

2️⃣ **Identify core points** (points with at least MinPts neighbors).

3️⃣ **Expand clusters** from core points to their neighbors.

4️⃣ **Classify remaining points**:

   - **Border points** (less than MinPts but near a core point).
   - **Outliers** (not in any cluster).

### 📌 Python Example: DBSCAN Clustering
```python
from sklearn.cluster import DBSCAN

# Apply DBSCAN
dbscan = DBSCAN(eps=2, min_samples=2)
dbscan_labels = dbscan.fit_predict(X)

# Plot clusters
plt.scatter(X[:, 0], X[:, 1], c=dbscan_labels, cmap='viridis')
plt.title("DBSCAN Clustering")
plt.show()
```

### 📌 DBSCAN Properties

✅ **Handles arbitrary cluster shapes**.

✅ **Robust to noise and outliers**.

✅ **No need to specify K (number of clusters)**.

❌ **Sensitive to parameter choices $( \varepsilon$ and MinPts)**.

---

## 🔹 K-Means vs. DBSCAN

| Feature          | K-Means                         | DBSCAN                        |
|-----------------|--------------------------------|------------------------------|
| **Cluster Shape** | Assumes spherical clusters   | Detects arbitrary shapes     |
| **Handling Outliers** | Poor (outliers distort clusters) | Good (outliers are ignored) |
| **Predefined K?** | Yes, must specify K          | No, K is determined automatically |
| **Computation** | Fast on large datasets | Slower on high-dimensional data |

---

## 🔹 Summary

✅ **K-Means** is fast and effective for **well-separated, spherical clusters**.

✅ **DBSCAN** is robust for **arbitrarily shaped clusters and noisy data**.

✅ **Choosing between them** depends on **data structure and noise level**.

---

## 💡 Next Steps

Would you like to:

1️⃣ **Explore Hierarchical Clustering?**  
2️⃣ **Implement Clustering for Real-World Datasets?**  
3️⃣ **Optimize Clustering Algorithms with Hyperparameter Tuning?**  

Let me know how you'd like to proceed! 🚀