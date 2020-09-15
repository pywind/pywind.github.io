

Classification:

- Can't use linear regression
- Binary classification problem: Take only tow values 0 & 1. 0 is negative, 1 is positive.

Logistic regression model

$$0 \leqslant  h_\theta(x) \leqslant 1$$

> With:
> $$h_\theta(x) = \sigma(\theta^Tx)$$
> Sigmoid function: $$\sigma(s) = \frac{1}{1+e^{-s}}$$ with $$s=\theta x$$

Estimated probability

$$h_\theta=P(y=a|x;\theta)$$
is  probability that y = a given x, param by $$\theta$$ and $$a \in \begin{Bmatrix}0,1\end{Bmatrix}$$

Cost Function
> Use log to avoid numbers being too small. The logo here is ln (log base e)

$$Cost(\sigma(s), y)=\left\{\begin{matrix}
-ln(\sigma(s)), y = 1\\
-ln(1-\sigma(s)),y=0
\end{matrix}\right.$$

## Derivative

We have

$$\frac{\partial \sigma}{\partial s}=\frac{e^{-s}}{(1+e^{-s})^2}=\frac{1}{1+e^{-s}}.\frac{e^{-s}+1-1}{(1+e^{-s})^2}=\sigma(s)(1-\sigma(s))$$(1)

### Compress cost function

$$Cost(\sigma(s),y)=-y.ln(\sigma(s))-(1-y)ln(1-\sigma(s))$$

### $$J(\theta)$$ in Gradient Descent

> $$J(\theta) = \frac{1}{m}\sum_{i=1}^{m}Cost(\sigma(x),y)$$

This is `cross-entropy` use to measure the distance between 2 probability distributions.

## Derivative of $$J(\theta)$$

Apply Chain rule

$$\frac{\partial J(\theta;x,y)}{\partial\theta}=\frac{\partial J}{\partial\sigma}.\frac{\partial \sigma}{\partial \theta} =\left( \frac{-y}{\sigma}+\frac{1-y}{1-\sigma}\right)\frac{\partial\sigma}{\partial\theta}=\frac{\sigma-y}{\sigma(1-\sigma)}\frac{\partial\sigma}{\partial\theta}$$ (2)

Continue and we had $$\frac{\partial\sigma}{\partial s}$$

$$\frac{\partial\sigma}{\partial\theta}=\frac{\partial\sigma}{\partial s}\frac{\partial s}{\partial \theta}=\sigma(1-\sigma).x$$

Summary:

$$(2)=\frac{\sigma-y}{\sigma(1-\sigma)}\frac{\partial\sigma}{\partial\theta}=\frac{\sigma-y}{\sigma(1-\sigma)}\frac{\partial\sigma}{\partial s}\frac{\partial s}{\partial \theta}=(\sigma-y)x$$

As you see, GD of logistic regression same GD of linear regression.

## Vectorized 

$$h=\sigma(X\theta)$$

Cost function

$$J(\theta)=\frac{1}{m}(-y^T ln(h)-(1-y)^T ln(1-h))$$

Gradient descent

$$\theta = \theta - \frac{\alpha}{m}X^T(\sigma(X\theta)-y)$$
