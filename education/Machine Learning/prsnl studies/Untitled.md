### Supervised vs. Unsupervised Learning

Machine learning algorithms can be broadly categorized based on the data they use:

- **Unsupervised Learning**: The algorithm is given data consisting of instances described by features, but with no known target variable to predict. A common unsupervised task is **clustering**, which involves grouping similar instances together. 
- **Supervised Learning**: The algorithm is trained on a dataset where each instance has both descriptive features and a known **target variable**. 

### Classification and Regression

This lecture focuses on two primary supervised learning tasks:

- **Classification**: The goal is to predict which category an instance belongs to. For example, predicting whether a client will default on a loan (1 for yes, 0 for no) is a binary classification problem. 
- **Regression**: The goal is to predict a continuous numerical value. For instance, predicting the amount of time a loan payment is overdue would be a regression problem. 

A simple and common performance metric for classification is **accuracy**, which is the proportion of correct predictions made on a test set. 

---

## 2. Decision Trees

A **decision tree** is a highly popular and intuitive algorithm used for both classification and regression. 14It mimics human decision-making by using a series of "if-then" rules organized into a tree structure. 

![[topic3_hse_instruction.png]]

(A visual example of a decision tree for a loan application task) 

One of the greatest advantages of decision trees is their **interpretability**. The model's logic is easy to visualize and explain, unlike "black box" models where the reasoning is opaque. 

### Building a Decision Tree

The core principle behind building a decision tree is to find splits that best separate the data. This is often explained with the "20 Questions" game analogy, where each question is chosen to narrow down the possibilities as much as possible. To do this mathematically, we use the concepts of entropy and information gain. 

#### Entropy

**Shannon's entropy** measures the degree of chaos or uncertainty in a system. For a system with N possible states, it's calculated as:

$$\Large S = -\sum_{i=1}^{N}p_i \log_2{p_i},​$$

where pi​ is the probability of the system being in the i-th state.

- **High entropy** means high uncertainty (e.g., a 50/50 mix of classes).
- **Low entropy** (down to 0) means high certainty (e.g., all instances belong to one class). 

#### Information Gain

When we split a set of data (a node in the tree) into smaller groups, we aim to reduce the overall uncertainty. The reduction in entropy is called **Information Gain (IG)**. 

$$\Large IG(Q) = S_O - \sum_{i=1}^{q}\frac{N_i}{N}S_i,$$

where $S_0$​ is the entropy of the original group, $q$ is the number of new groups after the split, $N_i$​ is the number of items in group $i$, and $S_i$​ is the entropy of group $i$.

The tree-building algorithm greedily chooses the split that provides the **greatest information gain** at each step. This process is repeated recursively until a stopping condition is met.

#### Other Split Criteria

Besides entropy, other metrics can be used to measure the quality of a split:

- **Gini Uncertainty (Gini Impurity)**: $G = 1 - \sum\limits_k (p_k)^2$. It is very similar in practice to entropy and is also widely used.    
- **Misclassification Error**: $E = 1 - \max\limits_k p_k$. This criterion is rarely used in practice.

### Overfitting in Decision Trees

A tree can be grown until every leaf contains only one instance, perfectly fitting the training data. However, such a tree is **overfitted**. it captures noise and details specific to the training set and will not generalize well to new, unseen data. 

![[585257b4a3d001fff62659255bb3f04f9a8be55f4f312a32b0cb0050e51a58b9.png]]

(An example of a complex, overfitted boundary created by a deep decision tree) 35

Common methods to prevent overfitting include:

- **Limiting tree depth**: Setting a `max_depth` for the tree. 3636
    
- **Setting minimum leaf size**: Requiring a `min_samples_leaf` to prevent splits that result in leaves with very few samples. 37373737
    
- **Pruning**: First building a full tree and then removing nodes from the bottom up if they do not significantly improve quality on a validation set. 38

---

## 3. k-Nearest Neighbors (k-NN)

The

**k-Nearest Neighbors (k-NN)** method is another simple and intuitive classification algorithm. 39Its core idea is based on the

**compactness hypothesis**: similar examples are likely to belong to the same class. 40

### How k-NN Works

To classify a new data point, k-NN follows these steps:

1. Calculate the
    
    **distance** from the new point to every point in the training dataset. 41
    
2. Select the
    
    **k** nearest points (neighbors) from the training set. 42
    
3. Assign the class to the new point based on a
    
    **majority vote** among its k neighbors. 43

For regression, instead of a majority vote, the algorithm typically assigns the mean or median of the target values of the k neighbors. 44

### Key Characteristics and Parameters

- **Lazy Algorithm**: k-NN is considered a "lazy" learner because it doesn't build an explicit model during training. 45All computations are deferred until a prediction is needed. 46
    
- **Parameter Sensitivity**: The performance of k-NN depends heavily on three key choices:
    
    - The number of neighbors,
        
        **k**. 47
        
    - The
        
        **distance metric** (e.g., Euclidean, Manhattan, Cosine). 48Most metrics require features to be scaled so that no single feature dominates the distance calculation. 49
        
    - The
        
        **weights** of neighbors (e.g., closer neighbors could have more influence). 50

---

## 4. Model Tuning and Cross-Validation

The primary goal of machine learning is to build models that

**generalize** well to new, unseen data. 51 To evaluate and tune a model's performance without using the final test data, we use validation techniques.

- **Hold-out Set**: A portion of the training data (e.g., 20-30%) is set aside for validation. 52The model is trained on the remaining data and evaluated on this hold-out set. 53
    
- **k-Fold Cross-Validation**: The dataset is divided into _k_ subsets. 54The model is trained

    _k_ times, each time using a different subset as the validation set and the remaining _k-1_ subsets for training. 55The final performance is the average of the

    _k_ evaluations. 56This provides a more robust estimate of the model's quality than a single hold-out set. 57

These techniques are essential for

**hyperparameter tuning**, such as finding the optimal `max_depth` for a decision tree or the best `k` for a k-NN model. 58

---

## 5. Pros and Cons of Each Method

### Decision Trees

**Pros:**

- **Highly interpretable** and easy to visualize. 59595959
    
- **Fast** for both training and prediction. 60
    
- Requires few parameters for tuning. 61
    
- Can handle both numerical and categorical features natively. 62

**Cons:**

- **Sensitive to noise** in the data; small changes can lead to a completely different tree. 63
    
- Prone to
    
    **overfitting**, requiring techniques like pruning or depth limitation. 64
    
- The greedy algorithm used to build the tree does not guarantee a globally optimal tree. 65
    
- The separating boundary is limited to axis-parallel hyperplanes. 66
    
- Cannot
    
    **extrapolate**; it makes constant predictions for data outside the range of the training set. 67

### k-Nearest Neighbors

**Pros:**

- **Simple** to implement. 68
    
- A
    
    **good baseline model** for many tasks, including classification and recommendations. 69
    
- **Adaptable** by choosing different distance metrics. 70
    
- **Interpretable** (for small `k`). 71

**Cons:**

- Can be
    
    **computationally slow** during prediction, as it must compare a new point to all training points. 72
    
- Performance is highly dependent on the choice of distance metric, which can be hard to determine. 73
    
- Does not perform well with a large number of features due to the
    
    **"curse of dimensionality"**. 74
    
- Sensitive to
    
    **outliers**, especially with a small number of neighbors (`k`). 75