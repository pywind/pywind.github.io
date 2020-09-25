---
layout: post
title:  "Logistic Regression"
date:   2020-09-15
excerpt: "Just about everything you need to know about logistic regression, math, derivative and more."
tag:
- ML
- GD
comments: true
---
## Classification:

- Can't use linear regression
- Binary classification problem: Take only tow values 0 & 1. 0 is negative, 1 is positive.

<figure>
	<a href="https://miro.medium.com/max/1428/1*Vd9ZTC1zWJPtV7iXPMJk1Q.png"><img src="https://miro.medium.com/max/1428/1*Vd9ZTC1zWJPtV7iXPMJk1Q.png"></a>
	<figcaption> Linear Regression vs Logistic Regression
    </figcaption>
</figure>


##Logistic regression model

$$0\leqslant h_\theta(x)\leqslant 1$$

> With:
> $$h_\theta(x) = \sigma(\theta x)$$

* Sigmoid function: $$\sigma(s) = \frac{1}{1+e^{-s}}$$ with $$s=\theta x$$

## Estimated probability

$$h_\theta=P(y=a|x;\theta)$$
is  probability that y = a given x, param by $$\theta$$ and $$a\in\begin{Bmatrix}0,1
\end{Bmatrix}$$

### Cost Function
> Use log to avoid numbers being too small. The logo here is ln (log base e)

$$Cost(\sigma(s), y)=\left\{\begin{matrix}
-ln(\sigma(s)), y = 1\\
-ln(1-\sigma(s)),y=0
\end{matrix}\right.$$

## Derivative

We have

$$\frac{\partial \sigma}{\partial s}=\frac{e^{-s}}{(1+e^{-s})^2}=\frac{1}{1+e^{-s}}.\frac{e^{-s}+1-1}{(1+e^{-s})^2}=\sigma(s)(1-\sigma(s))$$

### Compress cost function

$$Cost(\sigma(s),y)=-y.ln(\sigma(s))-(1-y)ln(1-\sigma(s))$$

### $$J(\theta)$$ in Gradient Descent

> $$J(\theta) = \frac{1}{m}\sum_{i=1}^{m}Cost(\sigma(x),y)$$

This is `cross-entropy` use to measure the distance between 2 probability distributions.

## Derivative of $$J(\theta)$$

Apply Chain rule:

$$\frac{\partial J(\theta;x,y)}{\partial\theta} =\frac{\partial J}{\partial\sigma}\frac{\partial \sigma}{\partial \theta} =\left( \frac{-y}{\sigma}+\frac{1-y}{1-\sigma}\right)\frac{\partial\sigma}{\partial\theta}=\frac{\sigma-y}{\sigma(1-\sigma)}\frac{\partial\sigma}{\partial\theta}$$

Continue and we had in above: 

$$\frac{\partial\sigma}{\partial\theta}=\frac{\partial\sigma}{\partial s}\frac{\partial s}{\partial \theta}=\sigma(1-\sigma).x$$

Summary:

$$\frac{\sigma-y}{\sigma(1-\sigma)}\frac{\partial\sigma}{\partial\theta}=\frac{\sigma-y}{\sigma(1-\sigma)}\frac{\partial\sigma}{\partial s}\frac{\partial s}{\partial \theta}=(\sigma-y)x$$

As you see, GD of logistic regression same GD of linear regression.

## Vectorized

$$h=\sigma(X\theta)$$
Cost function

$$J(\theta)=\frac{1}{m}(-y^T ln(h)-(1-y)^T ln(1-h))$$

Gradient descent

$$\theta = \theta - \frac{\alpha}{m}X^T(\sigma(X\theta)-y)$$

## Source code with python

Sigmoid function

``` python
def sigmoid(X, theta):
    return 1/(1+np.exp(-(X @ theta)))
```

Cost function

``` python
def computeCost(X, y, theta, sig):
    cost = -y.T @ np.exp(sig) - (1 - y).T @ np.exp(1-sig)
    return cost / (float(len(y)))
```

Fit the data by training

``` python
# fit
cost = np.zeros((iters, 1))
m = float(len(y))
for i in range(iters):
    sigma = sigmoid(X_norm, theta)
    theta = theta - (alpha / m) * X_norm.T @ (sigma - y)
    cost[i] = computeCost(X_norm, y, theta, sigma)
```

### By using scikit-learn

``` python
from sklearn.model_selection import train_test_split
from sklearn.linear_model import LogisticRegression

X_train, X_test, y_train, y_test = train_test_split(X_k, y_k, test_size=0.3, random_state=0)
logreg = LogisticRegression()
logreg.fit(X_train, y_train)
pred = logreg.predict(X_test)

```

* Source code details [in here](https://github.com/pywind/ML_repo/blob/master/Logistic%20Regression.ipynb).
