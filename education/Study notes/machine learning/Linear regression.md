In the most simplest terms machine learning is to create a best fit line that goes through the whole data staying as close to each data point as it can and linear in itself means straight line.

we get the sum of squares of the distance between the line the data point and mean it to get the number to compare and iterate 

## Regularisation 

regularisation is to protect the model from overfitting.

overfitting means the model is too accustomed to the data and will perform very poorly to the unseen data. it also mean the model is learning too much noise.

### Types of regularisation
#### Lasso regularisation 

A regression model which uses the L1 Regularization technique is called LASSO (Least Absolute Shrinkage and Selection Operator) regression. 

It introduces the penalty into the loss function for model ,

$$
\rm{Cost} = \frac{1}{n}\sum_{i=1}^{n}(y_i-\hat{y_i})^2 +\lambda \sum_{j=1}^{m}{|w_j|}
$$

these penalty can shrink some coefficients to zero

#### Ridge regularisation 

A regression model that uses the L2 regularization technique is called [Ridge regression](https://www.geeksforgeeks.org/machine-learning/what-is-ridge-regression/).It adds the squared magnitude of the coefficient as a penalty term to the loss function(L).It handles multicollinearity by shrinking the coefficients of correlated features, reducing their variance and preventing any single feature from dominating the model.

$$
\rm{Cost} = \frac{1}{n}\sum_{i=1}^{n}(y_i-\hat{y}_i)^2 + \lambda \sum_{j=1}^{m}{w_j^2}
$$

#### Elastic net regularisation 

[Elastic Net Regression](https://www.geeksforgeeks.org/machine-learning/implementation-of-elastic-net-regression-from-scratch/) is a combination of both L1 as well as L2 regularization. It combines both L1 (absolute values) and L2 (squared values) penalties on the coefficients. With the help of an extra hyperparameter that controls the ratio of the L1 and L2 regularization.

$$
\rm{Cost} = \frac{1}{n}\sum_{i=1}^{n}(y_i-\hat{y}_i)^2 + \lambda \left( (1-\alpha)\sum_{j=1}^{m}|w_j| + \alpha \sum_{j=1}^{m}{w_j^2} \right)
$$
