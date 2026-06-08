Detailed Briefing Document: The Hundred-Page Machine Learning Book

This briefing document summarises the core concepts and key takeaways from "The Hundred-Page Machine Learning Book" by Andriy Burkov, a text distributed on a "read first, buy later" principle. The book aims to provide a comprehensive yet concise overview of fundamental machine learning principles and practical considerations for data analysts and machine learning engineers.

Core Philosophy: "All models are wrong, but some are useful." - George Box

This quote serves as a central tenet of the book, highlighting the practical utility of machine learning models despite their inherent imperfections. The focus is on building models that are effective for solving real-world problems.

# 1. What is Machine Learning?

Machine learning (or "learning") is defined as a subfield of computer science focused on "building algorithms which, to be useful, rely on a collection of examples of some phenomenon." It's essentially a two-step process:

1. Gathering a dataset.

2. Algorithmically building a statistical model based on that dataset. This statistical model is then used to solve a practical problem.

# 2. Types of Learning

The book categorises machine learning into four main types:

## Supervised Learning 

- **Dataset:** Consists of **labelled examples** ${(xi, yi)}N i=1$, where $xi$ is a "feature vector" (a collection of values describing an example, e.g., height, weight) and $yi$ is the "label" (the desired output, e.g., spam/not_spam, a real number).

- **Goal:** To produce a model that takes a feature vector $x$ as input and outputs information allowing the deduction of its label $yi$.

- **Example:** Spam detection (classes: {spam, not_spam}).

## Unsupervised Learning

- **Dataset:** A collection of **unlabelled examples** ${xi}N i=1$.

- **Goal:** To create a model that transforms the feature vector $x$ into another vector or a value to solve a practical problem.

- **Examples:** Clustering (returns cluster ID), dimensionality reduction (reduces number of features), outlier detection (indicates how different $x$ is from typical examples).

## **Semi-Supervised Learning:**

- **Dataset:** Contains **both labeled and unlabeled examples**, with a significantly higher quantity of unlabeled data.
- **Goal:** Same as supervised learning, with the hope that "using many unlabeled examples can help the learning algorithm to find... a better model." The rationale is that unlabeled data provides more information about the underlying probability distribution.

## Reinforcement Learning

- **Mechanism:** A machine "lives" in an environment, perceives its state as a feature vector, and can execute actions that yield rewards and/or change the state.
- **Goal:** To learn a "policy" – a function that takes a state's feature vector and outputs an optimal action to maximise expected average reward.

- **Applications:** Sequential decision-making problems like game playing, robotics, resource management. (This type of learning is largely outside the scope of this particular book's focus on one-shot decision making.)

# 3. How Supervised Learning Works (Illustrated with SVM)

The supervised learning process typically involves:

1. **Data Gathering:** Collection of (input, output) pairs.

2. **Feature Engineering:** Converting real-world entities (e.g., emails) into numerical "feature vectors."

    ◦ **Example: Bag of Words:** For text, creating a binary feature for each word in a dictionary, indicating presence (1) or absence (0).

3. **Label Transformation:** Converting human-readable labels into numerical formats (e.g., "not_spam" to -1, "spam" to +1 for SVM).

4. **Model Building (Training):** Applying a learning algorithm to the dataset to find optimal "parameters" for the "statistical model."

    ◦ **Support Vector Machine (SVM):** Views feature vectors as points in a high-dimensional space and "draws an imaginary ... line (a hyperplane) that separates examples with positive labels from examples with negative labels." This boundary is called the "decision boundary."

    ◦ **Objective:** For SVM, the goal is to find optimal parameters $w*$ and $b*$ for the hyperplane equation $wx - b = 0$ such that $y = sign(w*x - b)$ correctly predicts labels. The algorithm achieves this by solving an "optimization problem" to: * Minimize the Euclidean norm of $w$ ($‖w‖$) to maximize the "margin" (distance between the closest examples of two classes). A larger margin contributes to better "generalization." * Satisfy constraints: $wxi - b ≥ +1$ if $yi = +1$, and $wxi - b ≤ -1$ if $yi = -1$.

    ◦ **Linear vs. Non-linear Models:** SVM can create "linear" decision boundaries or "arbitrarily non-linear" ones using "kernels."

    ◦ **Model Performance Trade-offs:** Beyond accuracy, "speed of model building and prediction processing time" are crucial differentiators.

# 4. Why Models Work on New Data (Generalization)

Models are capable of predicting new, unseen examples because, if training examples are "selected randomly, independently of one another, and following the same procedure," then new examples are statistically likely to fall within the regions defined by the decision boundary for their respective classes. Algorithms like SVM "explicitly tries to draw the decision boundary in such a way that it lies as far as possible from examples of both classes" to minimise error probability on new data. This concept is explored in PAC (Probably Approximately Correct) learning theory.

# 5. Notation and Definitions (Mathematical Foundations)

The book provides a review of essential mathematical notation and concepts:

- **Data Structures:** Scalar (simple numerical value), Vector (ordered list of scalar "attributes," visualised as arrows or points), Matrix (rectangular array of numbers), Set (unordered collection of unique elements).

- **Operators:** Summation ($∑$), Product ($∏$), Set operations (intersection $∩$, union $∪$, cardinality $|S|$), Vector operations (sum, difference, scalar multiplication, dot-product $wx$), Matrix-vector multiplication.

- **Functions:** Definition of a function $y = f(x)$, local and global minima.

- **Max and Arg Max:** Operators to return the maximum value or the element that produces the maximum value of a function.

- **Assignment Operator:** $a ← f(x)$ means $a$ gets the result of $f(x)$.

- **Derivative and Gradient:** Derivative $f'$ describes rate of growth/decrease for single-input functions; Gradient $∇f$ is a vector of partial derivatives for multi-input functions, indicating direction of fastest growth. Chain rule is crucial for complex function differentiation.

# 6. Random Variables and Statistics

- **Random Variable (X):** A variable whose values are numerical outcomes of a random phenomenon (discrete or continuous).

- **Probability Mass Function (pmf):** Describes probability distribution for discrete random variables.

- **Probability Density Function (pdf):** Describes probability distribution for continuous random variables (area under curve equals 1).

- **Expectation (E[X] or µ):** The mean or average value of a random variable.

- **Standard Deviation (σ):** Measure of spread, $√E[(X - µ)2]$.

- **Variance (σ2 or var(X)):** The squared standard deviation, $E[(X - µ)2]$.

- **Unbiased Estimators:** Sample statistics (e.g., sample mean $(1/N)∑xi$) that, on average, equal the true population statistic.

# 7. Bayes' Rule and Parameter Estimation

- **Bayes' Rule:** $Pr(X = x|Y = y) = [Pr(Y = y|X = x) * Pr(X = x)] / Pr(Y = y)$.

- **Parameter Estimation:** Used to update model parameters (e.g., $µ$, $σ$ for a Gaussian function) from data.

    ◦ **Maximum A Posteriori (MAP):** $θ* = arg maxθ ∏N i=1 Pr(θ = θ̂|X = xi)$ used to find optimal parameter values given examples. Often maximised in its logarithmic form for numerical stability.

# 8. Parameters vs. Hyperparameters

- **Parameters:** Variables that define the model, directly learned and modified by the algorithm from training data (e.g., $w*$, $b*$ in SVM). The goal of learning is to find optimal parameter values.

- **Hyperparameters:** Properties of the learning algorithm, usually numerical, that influence its behaviour. They are _not_ learned from data but "have to be set by the data analyst before running the algorithm" (e.g., $C$ in SVM, $ε$ and $d$ in ID3, $α$ in gradient descent).

# 9. Classification vs. Regression

- **Classification:** Assigning a label from a _finite set of classes_ to an unlabeled example (e.g., spam detection).

    ◦ **Binary Classification:** Two classes (e.g., {sick, healthy}).

    ◦ **Multiclass Classification:** Three or more classes.

- **Regression:** Predicting a _real-valued label_ (target) for an unlabeled example (e.g., estimating house price).

# 10. Model-Based vs. Instance-Based Learning

- **Model-Based Learning:** Algorithms use training data to create a model with learned parameters. Training data can be discarded after the model is built (e.g., SVM).

- **Instance-Based Learning:** Algorithms use the entire dataset as the model. Predictions are made by examining the "close neighborhood" of an input example (e.g., k-Nearest Neighbors).

# 11. Shallow vs. Deep Learning

- **Shallow Learning:** Learns model parameters directly from features of training examples. Most supervised learning algorithms are shallow.

- **Deep Learning:** Refers to training neural networks with more than one "hidden layer" between input and output. Most parameters are learned from the outputs of preceding layers, not directly from raw features.

# 12. Fundamental Algorithms (Chapter 3)

The book details five key algorithms, serving as foundational or building blocks:

- **Linear Regression (Regression):**

    ◦ **Model:** $fw,b(x) = wx + b$ (a linear combination of features).

    ◦ **Objective:** Minimises the "mean squared error" (MSE), which is the average of the "squared error loss" $(fw,b(xi)− yi)2$. This objective is also known as the "cost function" or "empirical risk."

    ◦ **Justification for Linear Model:** Simplicity, less prone to "overfitting" (model predicts training data well but new data poorly).

    ◦ **Justification for Squared Loss:** Mathematical convenience (continuous derivative, makes functions smooth for linear algebra solutions), exaggerates larger errors.

    ◦ **Solution:** Found by setting the gradient of the objective function to zero.

- **Logistic Regression (Classification -** **despite its name****):**

    ◦ **Model:** $fw,b(x) = 1 / (1 + e^-(wx+b))$ (uses the "standard logistic function" or "sigmoid function").

    ◦ **Interpretation:** Output is interpreted as the probability of the label being positive.

    ◦ **Objective:** Maximises the "likelihood" of the training set according to the model, typically by maximising the "log-likelihood" to avoid numerical issues with products of many small probabilities.

    ◦ **Solution:** No closed-form solution; typically solved using "gradient descent."

- **Decision Tree Learning (Classification):**

    ◦ **Model:** An acyclic graph where each branching node examines a feature and directs to a branch based on its value. Leaf nodes make the classification decision.

    ◦ **Algorithm (ID3):** Approximately optimises average log-likelihood by recursively splitting the dataset based on features and thresholds.

    ◦ **Split Criterion:** "Entropy" (a measure of uncertainty) is minimised at each split. A split is "good" if it significantly reduces entropy in the resulting subsets.

    ◦ **Stopping Criteria:** All examples in node correctly classified, no attribute to split upon, entropy reduction below $ε$, or maximum depth $d$ reached.

    ◦ **Overfitting:** Prone to overfitting (e.g., very tall trees), mitigated by "pruning" (removing insignificant branches).

    ◦ **C4.5:** A widely used formulation of decision tree learning that handles continuous/discrete features, incomplete examples, and pruning.

- **Support Vector Machine (SVM) (Classification):**

    ◦ **Core Idea:** Finds an optimal hyperplane that separates classes with the largest margin.

    ◦ **Dealing with Noise/Non-Separability:** * **Soft-Margin SVMs:** Introduces a "hinge loss function" $max(0, 1 − yi(wxi − b))$ to penalise misclassifications, allowing for errors in noisy data. A "hyperparameter C" balances margin size vs. misclassification penalty. * **Kernel Trick:** Implicitly transforms the original feature space into a higher-dimensional space where data becomes linearly separable, without explicit computation of high-dimensional vectors. Common kernels: Quadratic, RBF (Radial Basis Function). The dot product $xixk$ in the optimisation problem is replaced by a kernel function $k(xi, xk)$.

- **k-Nearest Neighbors (kNN) (Non-parametric, Instance-Based):**

    ◦ **Mechanism:** Stores all training examples. For a new example, it finds $k$ closest training examples (based on a "distance function," e.g., Euclidean distance, negative cosine similarity).

    ◦ **Prediction:** Returns the majority label (classification) or average label (regression) among the $k$ neighbours.

    ◦ **Hyperparameters:** $k$ and the choice of distance metric.

# 13. Anatomy of a Learning Algorithm (Chapter 4)

Every learning algorithm comprises three building blocks:

1. **A loss function:** Measures penalty for misclassification.

2. **An optimization criterion:** A cost function based on the loss function (e.g., average loss).

3. **An optimization routine:** Leverages training data to find a solution to the optimization criterion.

- **Gradient Descent:** An iterative optimization algorithm for finding the minimum of a differentiable function. It takes steps proportional to the negative of the gradient.

    ◦ **Process:** Initialise parameters (e.g., $w$, $b$ to 0). In each "epoch," calculate partial derivatives of the cost function with respect to each parameter. Update parameters by subtracting $α * (partial_derivative)$, where $α$ is the "learning rate" (a hyperparameter). Repeat until "convergence."

    ◦ **Variants:** "Minibatch stochastic gradient descent (minibatch SGD)" (uses smaller data batches), Adagrad (scales $α$ per parameter), Momentum (accelerates SGD, reduces oscillations), RMSprop, Adam (frequently used in neural networks).

    ◦ **Key Point:** Gradient descent and its variants are _solvers_ of minimisation problems, not machine learning algorithms themselves.

- **Practical Work with Libraries:** Machine learning engineers typically use open-source libraries (e.g., $scikit-learn$ in Python) rather than implementing algorithms from scratch, which provide stable and efficient implementations.

- **Learning Algorithm Particularities:**

    ◦ **Hyperparameters:** Differ between algorithms (e.g., $C$ for SVM, $ε$, $d$ for ID3, $α$ for gradient descent).

    ◦ **Categorical Features:** Some algorithms (e.g., decision trees) can handle them directly; others (SVM, linear/logistic regression, kNN) require numerical features.

    ◦ **Class Weightings:** Some algorithms (e.g., SVM) allow weighting classes to prioritise correct classification of minority classes.

    ◦ **Output Confidence:** Some classifiers (logistic regression, decision trees) return probability/confidence scores (0-1), while others (SVM, kNN) may only output the class.

    ◦ **Training Method:** Some build models on the whole dataset at once (SVM, decision trees); others can be trained iteratively ("incremental learning").

    ◦ **Applicability:** Some algorithms solve both classification and regression (decision trees, SVM, kNN), others only one.

# 14. Basic Practice (Chapter 5)

- **Feature Engineering:** The labour-intensive process of transforming raw data into informative features. Requires creativity and domain knowledge. Informative features are "features with high predictive power."

    ◦ **One-Hot Encoding:** Converting categorical features (e.g., "red", "yellow") into binary numerical vectors (e.g., [1,0,0], [0,1,0]) to avoid implying order where none exists. Increases dimensionality.

    ◦ **Binning (Bucketing):** Converting continuous numerical features into discrete binary bins based on value ranges (e.g., age 0-5 into bin 1, 6-10 into bin 2). Can provide "hints" to the algorithm and reduce examples needed.

    ◦ **Normalization:** Rescaling numerical features to a standard range (e.g., [0,1] or [-1,1]). Helps increase learning speed (prevents larger features from dominating gradient updates) and avoids numerical overflow. * Formula: $x̄(j) = (x(j) - min(j)) / (max(j) - min(j))$

    ◦ **Standardization (z-score normalization):** Rescaling features to have a mean of 0 and standard deviation of 1. * Formula: $x̂(j) = (x(j) - µ(j)) / σ(j)$ * **When to use:** Standardization often better for unsupervised learning, features with normal distribution or outliers. Normalization preferred otherwise.

    ◦ **Dealing with Missing Features:** * Removing examples (if dataset large enough). * Using algorithms that handle missing values. * **Data Imputation Techniques:** Replacing missing values with average, values outside normal range, or using a regression model to predict the missing value from other features. * Adding a binary indicator feature for presence/absence of original feature.

- **Learning Algorithm Selection:** A difficult task, often guided by questions like:

    ◦ **Explainability:** Is the model's decision-making process required to be transparent (e.g., kNN, linear regression, decision trees vs. "black boxes" like neural networks)?

    ◦ **In-memory vs. Out-of-memory:** Can the dataset fit in RAM? (Impacts choice of incremental learning algorithms).

    ◦ **Number of Features and Examples:** Some algorithms (neural networks, gradient boosting) handle huge numbers; others (SVM) are more limited.

    ◦ **Categorical vs. Numerical Features:** Requires feature conversion for some algorithms.

    ◦ **Nonlinearity of Data:** Linear models (SVM with linear kernel, logistic/linear regression) for linear data; deep neural networks or ensemble algorithms for non-linear data.

    ◦ **Training Speed:** How much time is allowed for model building? (Neural networks are slow, linear/logistic regression faster).

    ◦ **Prediction Speed:** How fast does the model need to make predictions in production? (SVMs, linear/logistic regression, some neural networks are fast).

    ◦ **Decision Aid:** Scikit-learn's algorithm selection diagram is a useful tool.

- **Three Sets (for Data Splitting):**

    ◦ **Training Set:** Largest portion, used to build the model.

    ◦ **Validation Set:** Used to 1) choose the learning algorithm and 2) find the best values of hyperparameters.

    ◦ **Test Set:** Used to assess the model's final performance before delivery/production.

    ◦ **Purpose:** To prevent "memorisation" of training data and ensure good "generalisation" to unseen examples. Typical split: 70% train, 15% validation, 15% test (proportions can vary for big data).

- **Underfitting and Overfitting:**

    ◦ **Underfitting (High Bias):** Model makes many mistakes on training data. Reasons: model too simple, uninformative features. Solution: more complex model, better features.

    ◦ **Overfitting (High Variance):** Model predicts training data very well but poorly on holdout sets. Reasons: model too complex, too many features for a small training set. Model learns "idiosyncrasies" and "noise" of the training set. Solution: simpler model, dimensionality reduction, more data, "regularization."

- **Regularization:** Methods to force the learning algorithm to build a less complex model, reducing variance (overfitting) at the cost of slightly higher bias. This is the "bias-variance tradeoff."

    ◦ **L1 Regularization (Lasso):** Adds $C|w|$ (sum of absolute values of $w$ components) to objective. Produces "sparse models" (many $w(j)$ values become zero), performing feature selection.

    ◦ **L2 Regularization (Ridge):** Adds $C‖w‖2$ (sum of squared $w$ components) to objective. Generally provides better performance on holdout data and is differentiable.

    ◦ **Hyperparameter C:** Controls the strength of regularisation.

    ◦ **Elastic Net:** Combines L1 and L2 regularization.

    ◦ **Neural Network Specific Regularization:** Dropout, Batch-Normalisation, Data Augmentation, Early Stopping.

- **Model Performance Assessment (for Classification):**

    ◦ **Confusion Matrix:** A table summarising correct and incorrect predictions for each class (True Positives, False Negatives, False Positives, True Negatives). Helps identify mistake patterns.

    ◦ **Precision:** $TP / (TP + FP)$ (proportion of correct positive predictions among all positive predictions).

    ◦ **Recall (True Positive Rate - TPR):** $TP / (TP + FN)$ (proportion of correct positive predictions among all actual positive examples).

    ◦ **Trade-off:** Often impossible to have both high precision and high recall simultaneously. Achieved by weighting classes, hyperparameter tuning, or adjusting prediction thresholds.

    ◦ **Accuracy:** $(TP + TN) / (TP + TN + FP + FN)$ (overall proportion of correct predictions). Useful when all errors are equally important.

    ◦ **Cost-Sensitive Accuracy:** Assigns different costs to FP and FN mistakes, useful when error types have different implications.

    ◦ **Area Under the ROC Curve (AUC):** Measures classifier performance by plotting True Positive Rate (TPR) against False Positive Rate (FPR) at various prediction thresholds. Higher AUC indicates better performance (AUC > 0.5 is better than random, 1.0 is perfect).

- **Hyperparameter Tuning:** Experimentally finding the best combination of hyperparameter values.

    ◦ **Grid Search:** Systematically trying all combinations from a predefined discrete set of values (often on a logarithmic scale).

    ◦ **Random Search:** Randomly sampling values from a statistical distribution for each hyperparameter. More efficient than grid search for many hyperparameters.

    ◦ **Bayesian Hyperparameter Optimization:** Uses past evaluation results to intelligently choose the next values to evaluate, aiming to limit expensive optimizations.

    ◦ **Cross-Validation:** Used when a decent validation set is not available (e.g., small datasets). Splits the training set into "folds" (e.g., 5-fold cross-validation), iteratively trains on $k-1$ folds and validates on the remaining fold, then averages results. The final model is trained on the entire training set with the best hyperparameters found via cross-validation.

# 15. Neural Networks and Deep Learning (Chapter 6)

- **Neural Network (NN):** A mathematical function $y = fNN(x)$ that is "nested" (composed of layers).

    ◦ **Layer** **l****:** $fl(z) = gl(Wlz + bl)$, where $gl$ is a fixed, usually non-linear "activation function," and $Wl$ (matrix) and $bl$ (vector) are learned parameters.

    ◦ **Multilayer Perceptron (MLP):** A "feed-forward" (no loops) neural network with "fully-connected" layers (all outputs of one layer connect to all inputs of the next).

    ◦ **Activation Functions:** Essential for approximating non-linear functions. Common choices: Logistic (Sigmoid), TanH (hyperbolic tangent), ReLU (Rectified Linear Unit). Differentiability is crucial for gradient descent.

    ◦ **Output Layer:** Linear activation for regression, logistic for binary classification, softmax for multiclass classification.

- **Deep Learning:** Refers to training NNs with more than two non-output layers ("hidden layers").

    ◦ **Historical Challenges:** "Exploding gradient" (parameters change too much) and "vanishing gradient" (parameters change too little, preventing earlier layers from training).

    ◦ **Solutions:** Gradient clipping, L1/L2 regularization (for exploding gradient); ReLU, LSTM/GRU (gated units), skip connections, and advanced gradient descent variants (for vanishing gradient).

    ◦ **Modern Definition:** "Training neural networks using the modern algorithmic and mathematical toolkit independently of how deep the neural network is."

- **Convolutional Neural Network (CNN):**

    ◦ **Purpose:** Significantly reduces parameters, especially for high-dimensional inputs like images. Found applications in image and text processing.

    ◦ **Core Idea:** Exploits the local nature of information in images (pixels close together are related).

    ◦ **Filters (Kernels):** Small matrices (e.g., 3x3) that "convolve" (slide) across input images/volumes, detecting specific patterns. Filter values and biases are trainable parameters.

    ◦ **Volume:** Output of a convolution layer can be a collection of matrices, treated as a volume by subsequent layers.

    ◦ **Stride:** Step size of the moving window for filters (e.g., stride 1 moves one cell at a time). Larger stride reduces output matrix size.

    ◦ **Padding:** Adding extra cells (usually zeros) around the image/volume to increase output matrix size and help filters scan boundaries.

    ◦ **Pooling:** A layer that follows convolution, applying a fixed operation (max or average) within a moving window. Reduces parameter count and improves training speed/accuracy. Max pooling is more popular.

- **Recurrent Neural Network (RNN):**

    ◦ **Purpose:** Used for labeling, classifying, or generating "sequences" (ordered lists of feature vectors where order matters). Applications in text and speech processing.

    ◦ **Mechanism:** Contains loops. Each unit has a "state" (memory). Receives input from previous layer and previous timestep of the same layer.

    ◦ **Timestep:** Processes sequence elements sequentially.

    ◦ **Gated RNNs (LSTM, GRU):** Most effective RNNs. Overcome vanishing gradient and long-term dependency problems. They use "gates" (sigmoid activation functions) to control information flow (reading, writing, erasure) to and from their memory cells.

# 16. Problems and Solutions (Chapter 7)

- **Kernel Regression:** Non-parametric method (no parameters to learn) for non-linear regression.

    ◦ **Model:** $f(x) = (1/N) * ∑wi*yi$, where $wi$ are weights determined by a "kernel" function (e.g., Gaussian kernel) that measures similarity between $x$ and $xi$.

    ◦ **Hyperparameter** **b****:** Controls bias-variance trade-off (smoothness of regression line).

- **Multiclass Classification:**

    ◦ **Adaptation:** Some algorithms (ID3, logistic regression with softmax, kNN) naturally extend.

    ◦ **One vs. Rest Strategy:** Transforms a multiclass problem into C binary classification problems (e.g., class 1 vs. rest, class 2 vs. rest, etc.). Predicts the class with the highest certainty/score.

- **One-Class Classification (Unary Classification, Class Modeling):**

    ◦ **Problem:** Training a model to distinguish examples of a _specific class_ from everything else, using only examples from that specific class in training.

    ◦ **Applications:** Outlier detection, anomaly detection, novelty detection.

    ◦ **Methods:** * **One-class Gaussian:** Models data as a Multivariate Normal Distribution (MND) and predicts likelihood of an example belonging to the distribution. * **One-class k-means:** Clusters training data; new examples are classified based on their distance to cluster centers. * **One-class SVM:** Tries to separate training examples from the origin or obtain a spherical boundary around them.

- **Multi-Label Classification:**

    ◦ **Problem:** Assigning _more than one label_ to an example (e.g., an image being "conifer," "mountain," and "road").

    ◦ **Strategies:** * Treat each label as a separate binary classification and combine using a threshold on prediction scores. * Neural networks: output layer with one sigmoid unit per label, using "binary cross-entropy" cost. * Create "fake classes" by combining all possible original label values (works for small label combinations, preserves label correlations).

- **Ensemble Learning:** Combines predictions from a large number of low-accuracy ("weak") models to achieve high accuracy. Weak learners are often shallow decision trees.

    ◦ **Mechanism:** Weighted voting of predictions.

    ◦ **Boosting:** Iteratively creates models, with each new model trying to "fix" errors made by previous ones (e.g., Gradient Boosting). Reduces bias.

    ◦ **Bagging:** Creates multiple random samples (with replacement) of training data, builds a model on each, and combines their predictions (e.g., Random Forest). Reduces variance.

- **Random Forest (Bagging-based):**

    ◦ **Process:** Builds B decision trees on different bootstrap samples of the training data. At each split, considers only a random subset of features to reduce correlation between trees.

    ◦ **Prediction:** Averages predictions (regression) or takes majority vote (classification).

    ◦ **Effectiveness:** Reduces variance (overfitting) by averaging out noise and artifacts from individual dataset samples.

    ◦ **Hyperparameters:** Number of trees (B), size of random feature subset.

- **Gradient Boosting (Boosting-based):**

    ◦ **Process:** Starts with a constant model. Iteratively adds new decision trees that are trained on the "residuals" (errors) of the current ensemble model. Each new tree "fixes" the previous errors.

    ◦ **Learning Rate (α):** A hyperparameter controlling the contribution of each new tree.

    ◦ **Named "Gradient Boosting":** Because it uses residuals as a proxy for the gradient, showing the direction in which the model needs to be adjusted.

    ◦ **Effectiveness:** Reduces bias (underfitting), can handle huge datasets. Generally outperforms Random Forest in accuracy but is slower.

    ◦ **Hyperparameters:** Number of trees (M), learning rate (α), tree depth.

- **Learning to Label Sequences:** Assigning a label to each element in a sequence (e.g., part-of-speech tagging for words in a sentence).

    ◦ **RNNs:** Can be used, with each timestep processing an input vector and outputting a label.

    ◦ **Conditional Random Fields (CRF):** An alternative, effective for informative features, but slower than deep gated RNNs.

- **Sequence-to-Sequence Learning (seq2seq):**

    ◦ **Problem:** Input and output sequences can have different lengths (e.g., machine translation).

    ◦ **Architecture:** Encoder-decoder model. * **Encoder:** Neural network (RNN, CNN) that reads input sequence and generates an "embedding" (numerical representation of meaning). * **Decoder:** Neural network that takes the embedding and generates the output sequence.

    ◦ **Training:** Encoder and decoder trained simultaneously; errors propagate back.

    ◦ **Attention Mechanism:** An advanced technique that allows the decoder to selectively focus on relevant parts of the encoder's output when generating each part of the output sequence, improving long-term dependency retention.

- **Active Learning:** Supervised learning paradigm used when obtaining labeled examples is costly.

    ◦ **Idea:** Start with few labeled examples, many unlabeled. Only label examples that contribute most to model quality.

    ◦ **Strategies:** * **Data Density and Uncertainty Based:** Labels examples that are both dense in the feature space and for which the current model is most uncertain (e.g., prediction score close to 0.5 for binary classification, high entropy for multiclass). * **Support Vector-Based:** Labels unlabeled examples closest to the SVM decision boundary (most uncertain).

    ◦ **Stopping Criterion:** Max requests to expert, or model performance threshold.

- **Semi-Supervised Learning (SSL):** Leverages a large number of unlabeled examples to improve model performance without asking for additional labels.

    ◦ **Self-Learning:** Build initial model on labeled data, apply to unlabeled data, add high-confidence predictions to training set, retrain. Performance increase often not dramatic.

    ◦ **Ladder Networks:** Advanced neural network architecture (denoising autoencoder with an upgrade) that shows impressive SSL results (e.g., near-perfect MNIST classification with few labels). * **Autoencoder:** A feed-forward NN trained to reconstruct its input ($(x,x)$). Has a "bottleneck layer" to learn a low-dimensional "embedding." * **Denoising Autoencoder:** Corrupts input with noise during training, but aims to reconstruct the original uncorrupted input. * **Ladder Network Mechanism:** Denoising autoencoder with cost functions that penalise both reconstruction error (for each layer) and prediction error (from bottleneck layer).

- **One-Shot Learning:** Training a model to recognise if two inputs belong to the same entity, often with limited examples per entity (e.g., face recognition).

    ◦ **Siamese Neural Network (SNN):** Takes one image as input and outputs an "embedding."

    ◦ **Triplet Loss Function:** $max(‖f(Ai)− f(Pi)‖2 − ‖f(Ai)− f(Ni)‖2 + α, 0)$ for a triplet (Anchor, Positive, Negative example). Forces similar embeddings for $A$ and $P$, and dissimilar for $A$ and $N$ with a margin $α$.

- **Zero-Shot Learning (ZSL):** Model can predict labels not present in the training data.

    ◦ **Mechanism:** Uses "embeddings" not just for input $x$ but also for output $y$ (e.g., "word embeddings" where similar words have similar vectors).

    ◦ **Process:** Train a multi-label model to predict word embeddings. For new $x$, get predicted embedding $ŷ$, then find closest word embedding from a dictionary.

    ◦ **Benefit:** Allows generalisation to unseen classes if their distinguishing features are captured in the embeddings.

# 17. Advanced Practice (Chapter 8)

- **Handling Imbalanced Datasets:** When some classes are underrepresented in training data.

    ◦ **Class Weighting:** Assign higher cost to misclassification of minority class examples (e.g., in SVM).

    ◦ **Oversampling:** Increase importance of minority class by making multiple copies of examples.

    ◦ **Undersampling:** Randomly remove examples from majority class.

    ◦ **Synthetic Example Generation (SMOTE, ADASYN):** Create new synthetic examples for minority class by interpolating between existing minority examples and their neighbours.

    ◦ **Algorithm Robustness:** Decision trees, Random Forest, Gradient Boosting often perform well on imbalanced datasets.

- **Combining Models:** Boosting performance by combining multiple "strong" models (usually 2-3) made with different learning algorithms.

    ◦ **Averaging:** For regression or models with scores, average predictions.

    ◦ **Majority Vote:** For classification, take the class with the most votes.

    ◦ **Stacking:** Building a "meta-model" that takes the outputs of base models as its input features. Base models should ideally be "uncorrelated."

- **Training Neural Networks:**

    ◦ **Data Preparation:** Resizing images, normalising pixel values. Tokenising text, converting to one-hot or word embeddings.

    ◦ **Architecture Choice:** Research state-of-the-art solutions, or use robust existing architectures.

    ◦ **Iterative Tuning:** Start with small network, gradually increase size/layers until training data is fitted well (low bias). If overfitting occurs (high variance on validation), add regularization.

- **Advanced Regularization (Neural Networks):**

    ◦ **Dropout:** Randomly excludes some units from computation during training, preventing over-reliance on specific units.

    ◦ **Early Stopping:** Stop training when validation performance starts to deteriorate, even if training loss continues to decrease.

    ◦ **Batch Normalization (Batch Standardization):** Standardises outputs of each layer before input to subsequent layers. Leads to faster, more stable training and often a regularization effect.

    ◦ **Data Augmentation:** Creating synthetic training examples by applying transformations (zooming, rotating, flipping) to original images/data, increasing effective training data size.

- **Handling Multiple Inputs (Multimodal Data):**

    ◦ **Shallow Models:** Train separate models on each input type, then combine predictions.

    ◦ **Neural Networks:** Build separate "subnetworks" for each input type (e.g., CNN for image, RNN for text). Concatenate their "embeddings" and add a final classification/regression layer.

- **Handling Multiple Outputs:** Predicting multiple outputs for one input (e.g., object coordinates and object tag from an image).

    ◦ **Multi-Label Conversion:** Can convert to multi-label classification if outputs are of the same nature.

    ◦ **Neural Networks with Multiple Heads:** One encoder subnetwork generates an embedding. Multiple separate decoder subnetworks then take this embedding and predict different output types. Cost functions for each output are combined using a hyperparameter ($γ$).

- **Transfer Learning:** Adapting an existing model (often a deep neural network) trained on one dataset to predict examples from a different, new dataset.

    ◦ **Process:** 1. Train a deep model on a large, original dataset. 2. Create a smaller labeled dataset for the new problem. 3. Remove last few layers of the original model (classification/regression layers). 4. Replace with new layers adapted for the new problem. 5. "Freeze" parameters of the original layers. 6. Train only the new layers on the smaller dataset using gradient descent.

    ◦ **Benefit:** Leverages pre-learned features from large datasets, significantly reducing the amount of new labeled data required.

- **Algorithmic Efficiency (Big O Notation):** Classifying algorithms by how their running time or space requirements grow with input size ($O(N)$, $O(N^2)$, etc.).

    ◦ **Optimisation:** Prefer $O(N)$ over $O(N^2)$. Avoid explicit loops in Python where possible; use $numpy$ operations on matrices/vectors.

    ◦ **Data Structures:** Use appropriate structures (e.g., $set$ or $dict$ for fast lookups if order doesn't matter).

    ◦ **Libraries:** Prefer popular scientific libraries ($numpy$, $scipy$, $scikit-learn$) as they are highly optimised (often in C).

    ◦ **Tools:** $cProfile$ for profiling, $multiprocessing$ for parallelism, $PyPy$/$Numba$ for compilation.

# 18. Other Forms of Learning (Chapter 10)

- **Metric Learning:** Learning a customised similarity/dissimilarity metric (e.g., parametrised Euclidean distance) from data, for use in algorithms like k-means or kNN. One-shot learning with siamese networks is a form of metric learning.

- **Learning to Rank:** Supervised learning problem, commonly used for search engine result optimisation.

    ◦ **Pointwise:** Treats each document as an independent example for regression/classification (often poor).

    ◦ **Pairwise:** Compares pairs of documents to predict which should be ranked higher.

    ◦ **Listwise (e.g., LambdaMART):** Directly optimises a metric that reflects the quality of the entire ranking (e.g., Mean Average Precision - MAP). LambdaMART uses gradient boosting and modifies the gradient based on the metric.

- **Learning to Recommend:** Building recommender systems.

    ◦ **Content-Based Filtering:** Recommends items based on features of content previously consumed by the user (can lead to "filter bubble").

    ◦ **Collaborative Filtering:** Recommends items based on preferences of other similar users (ignores content). Uses sparse user-item interaction matrices.

    ◦ **Hybrid Approach:** Combines both.

    ◦ **Algorithms:** * **Factorization Machines (FM):** Designed for sparse datasets, models pairwise feature interactions more efficiently than traditional linear models. * **Denoising Autoencoders (DAE):** Reconstructs corrupted input (e.g., user's movie ratings with some removed). Recommends removed items with highest reconstructed scores. * **FFNN with Two Inputs:** Learns ratings from user (one-hot) and movie (one-hot) inputs.

- **Self-Supervised Learning: Word Embeddings (e.g., word2vec skip-gram):**

    ◦ **Problem:** Learning dense feature vectors (embeddings) for words such that similar words have similar vectors.

    ◦ **Mechanism:** Extracts "skip-grams" (windows of words around a central word) from unlabeled text.

    ◦ **Training:** A neural network learns to predict context words given a central word (or vice-versa).

    ◦ **"Self-Supervised":** Labeled examples are extracted directly from unlabeled data.

    ◦ **Efficiency Techniques:** Hierarchical softmax, negative sampling.

# 19. What Wasn't Covered (Additional Topics Mentioned)

- **Topic Modeling (Latent Dirichlet Allocation - LDA):** Unsupervised text analysis to discover topics within documents.

- **Gaussian Processes (GP):** Supervised learning for regression, provides confidence intervals.

- **Generalized Linear Models (GLM):** Generalisation of linear regression for various forms of dependency (logistic regression is an example).

- **Probabilistic Graphical Models (PGMs):** Represent conditional dependencies between random variables as graphs (e.g., Conditional Random Fields). Also known as Bayesian networks.

- **Markov Chain Monte Carlo (MCMC):** Algorithms for sampling from complex probability distributions.

- **Generative Adversarial Networks (GANs):** Unsupervised neural networks where two networks (generator, discriminator) contest to generate authentic-looking data (e.g., images).

- **Genetic Algorithms (GA):** Numerical optimisation technique mimicking evolutionary processes to find global optima for non-differentiable objectives.

- **Reinforcement Learning (RL):** Agent learns optimal long-term actions in an unknown environment for maximum reward (e.g., Q-learning).

This concludes the detailed briefing document based on "The Hundred-Page Machine Learning Book." The book provides a solid, practical foundation for understanding and applying machine learning techniques.