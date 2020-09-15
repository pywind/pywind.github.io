
> Propose: Regression problem, predict real-value, ...

`Note`:

- m = number of training
- $$x's$$ = input features
- $$y's$$ = output variables
- $$(x, y)$$ one training example
- $$x^{(i)}, y^{(i)}$$ : $$i^{th}$$ example
- $$\alpha$$ : learning rate

1. One variable

`Hypothesis`: $$h_{\theta}(x) = \theta_0 + \theta_1x$$

`Cost function`:

$$J(\theta_1, \theta_2) = \frac{1}{2m}\sum _{i=1}^m\:\left(h_\theta\left(x^{(i)}\right)- y^{(i)}\right)^2$$

Goal: minimize $$J(\theta_1, \theta_2)$$

### Gradient descent

Outline:

- Start with some $$\theta_0, \theta_1$$ (eg. $$\theta_0 = 0, \theta_1 = 0$$)
- Keep changing $$\theta_0, \theta_1$$ to reduce $$J(\theta_0, \theta_1)$$ to find the minimum.

Algorithm

- Repeat until convergence:
$$\theta_j = \theta_j - \alpha\frac{\partial }{\partial \theta_j}J(\theta_0,\theta_1)$$

### Correct: Simultaneous update (1)

- temp_0  $$=\theta_0 - \alpha \frac{\partial}{\partial \theta_0}J(\theta_0, \theta_1)$$
- temp_1  $$=\theta_1 - \alpha \frac{\partial}{\partial \theta_1}J(\theta_0, \theta_1)$$
- $$\theta_0$$ = temp_0
- $$\theta_1$$ = temp_1

As definition, $$J(\theta_0, \theta_1)$$ is a quadratic function.
![J()](https://www.mathworks.com/content/dam/mathworks/videos/s/surrogate-optimization-public.mp4/jcr:content/renditions/thumb-surrogate-optimization.png)
So finding the minimize point, we're going through the derivative.
[`In mathematics, the derivative is a way to show rate of change. For functions that act on the real numbers, it is the slope of the tangent line at a point on a graph.`](https://simple.wikipedia.org/wiki/Derivative_(mathematics)#:~:text=In%20mathematics%2C%20the%20derivative%20is,a%20point%20on%20a%20graph.)

### Derivative of $$J(\theta_0, \theta_1)$$

**Step 1**: The derivative of the sum is equal to the sum of the derivatives.

 $$\frac{\partial}{\partial\theta_0}J(\theta_0, \theta_1)=\frac{\partial}{\partial\theta_0}(\frac{1}{2m}\sum_{i=1}^m\:\left(\theta_0 + \theta_1x_i-y_i\right)^2) =\frac{1}{2m}\sum_{i=1}^m\:\frac{\partial}{\partial \theta_0}\left(\theta_0 + \theta_1x_i-y_i\right)^2$$

**Step 2**: [`Apply power rule`](https://en.wikipedia.org/wiki/Power_rule) and [`chain rule`](https://en.wikipedia.org/wiki/Chain_rule).
We have:
  
$$\frac{\partial}{\partial \theta_0}(\theta_0 + \theta_1x_i-y_i)^2=\frac{\partial}{\partial \theta_0}(\theta_0 + \theta_1x_i-y_i)*2(\theta_0 + \theta_1x_i-y_i)^{2-1}$$

$$=2(\theta_0 + \theta_1x_i-y_i)$$

**Step 3**:
Totally, $$\frac{\partial}{\partial\theta_0}J(\theta_0, \theta_1)=\frac{1}{m}\sum_{i=1}^m\:\left(\theta_0+\theta_1x_i-y_i\right)$$

Similar to $$\theta_0$$, $$\frac{\partial}{\partial \theta_1}J(\theta_0, \theta_1)=\frac{1}{m}\sum_{i=1}^m\:x_i(\theta_0 + \theta_1x_i-y_i)$$

### Gradient descent algorithm

Above we have the derivative which is the decrease of the function J.
If want to control the reduce of theta, we have the learning rate. Set that to 0.0000001 -> 0.001 would be reasonable for a good accuracy.

Repeat until convergence (`with amount iteration`) {

- temp_0  $$=\theta_0-\alpha \frac{1}{m}\sum_{i=1}^m\:(\theta_0 + \theta_1x_i-y_i)$$
- temp_1  $$=\theta_1 - \alpha\frac{1}{m}\sum_{i=1}^m\:x_i(\theta_0 + \theta_1x_i-y_i)$$
- $$\theta_0$$ = temp_0
- $$\theta_1$$ = temp_1

}

3. Source code with python

Import library

``` python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
```

Import data

``` python
dat = pd.read_csv('name_file')
```

Analysis and visualize before training

> Using seaborn or matplotlib to visualize for an overview of the data set. `In seaborn, can use jointplot or pairplot`

Preparing argument

``` Python
m = data[0].count()
theta = np.zeros(2) # [0.0, 0.0]
X = data[0] # training examples
y = data[1] # real-values
iters = 1500 # Depending size of data
alpha = 0.0001 # Learning rate
```

Compute Cost function

``` python
# to monitor the convergence by computing the cost.
def cost_function(X, y, theta):
    # set theta0 = 0, theta1 = 0
    total_error = 0
    for i in range(len(X)):
        total_error += ((theta[0] + theta[1]*X[i]) - y[i])**2
    return total_error / (2 * len(X))
```

Gradient Descent

``` python
def GradientDescent(X, y, theta, learning_rate):
    temp_0, temp_1 = 0, 0 # to update simultaneously
    m = float(len(X))
    for i in range(len(X)):
        temp_0 +=  (theta[0] + theta[1]*X[i] - y[i])
        temp_1 +=  X[i]*(theta[0] + theta[1]*X[i] - y[i])

    theta[0] = theta[0] - learning_rate*temp_0 / m
    theta[1] = theta[1] - learning_rate*temp_1 / m

    return theta
```

Training model

``` python
def training(X, y, theta, learning_rate, iters):
    for i in range(iters):
        theta = GradientDescent(X, y, theta, learning_rate)
    return theta

# The result of theta
theta = training(X, y, theta, alpha, iters)
```

> Đoạn này mình viết bằng tiếng việt sợ còn chưa hiểu nói gì ENG :v
> 
## Toán học

Cụ thể, cho hàm số $$f(x, y)$$ và một điểm $$M(x_0, y_0)$$ thuộc tập xác định của hàm, khi đó đạo hàm theo biến $$x$$ tại điểm M được gọi là đạo hàm riêng của $$f$$ theo $$x$$ tại M. Lúc này $$y$$ sẽ được cố định bằng giá trị $$y_0$$ và hàm của ta có thể coi là hàm 1 biến của biến $$x$$.

Định nghĩa:
$$f_x^{\prime}(x_0, y_0) = \lim\limits_{\triangle_x \rightarrow 0} \frac{\triangle_xf}{\triangle_x} = \lim\limits_{\triangle_x \rightarrow 0} \frac{f(x_0 + \triangle_x, y_0) - f(x_0, y_0)}{\triangle_x}$$

Với y tương tự như đạo hàm riêng của x

`Công thức đạo hàm riêng của hàm hợp` theo *chain rule*

$$F(u,v) = u(x,y)+v(x,y)$$

$$
\left\{\begin{matrix}
\frac{\partial F}{\partial x} = \frac{\partial F}{\partial u}\frac{\partial u}{\partial x}\\ \frac{\partial F}{\partial y} = \frac{\partial F}{\partial v}\frac{\partial v}{\partial y}
\end{matrix}\right.$$

* Ma trận Jacobi của phép đổi biến $$u=u(x,y), v=v(x, y)$$

$$J=\begin{pmatrix}\frac{\partial u}{\partial x}&\frac{\partial v}{\partial x}\\ \frac{\partial v}{\partial y}&\frac{\partial v}{\partial y}\end{pmatrix}$$

## Đạo hàm theo hướng - Gradient

Bổ đề: $$\overrightarrow{l}$$ là vector đơn vị.

Nếu ta kết hợp các đạo hàm riêng lại thành một véc-tơ và tính đạo hàm teo véc-tơ đó thì ta sẽ thu được đạo hàm toàn phần. Hay nói cách khác là đạo hàm theo tất cả các biến hay đạo hàm theo véc-tơ hợp thành đó. Đạo hàm này được gọi là gradient của hàm theo véc-tơ tương ứng.

Ta có gradient tại điểm M:

$$\nabla{f(x_0, y_0)} = \Bigg(\frac{\partial{f}}{\partial{x}}(x_0, y_0), \frac{\partial{f}}{\partial{y}}(x_0, y_0)\Bigg)$$

Gradient là **một vector cột**, kí hiệu $$\nabla{f} = \Bigg[\frac{\partial{f}}{\partial{x}}\Bigg]\text{\^{i}} + \Bigg[\frac{\partial{f}}{\partial{y}}\Bigg]\text{\^{j}}$$

Ví dụ, hàm số $$f(x, y) = x^2 + y^2$$
  sẽ có gradient là: $$\nabla{f} = \begin{bmatrix} 2x \cr 2y \end{bmatrix}$$

Đối với hàm véc-tơ, nhớ lại rằng đạo hàm riêng của nó là một véc-tơ hàng mà gradient thành kết hợp theo véc-tơ cột, nên gradient của hàm véc-tơ sẽ là một ma trận có số hàng bằng với số chiều véc-tơ giá trị và số cột bằng với số biến.

$$J = \nabla f = \begin{bmatrix}
 \nabla f_1& \cdots &\nabla f_n \end{bmatrix}=\begin{pmatrix}
\frac{\partial{f_1}}{\partial{x_1}} &\cdots & \frac{\partial{f_n}}{\partial{x_1}}\\ \vdots  & \ddots & \vdots \\ \frac{\partial{f_n}}{\partial{x_1}} &\cdots &\frac{\partial{f_n}}{\partial{x_n}} \end{pmatrix}$$

 Ta có thể thấy rằng chiều của gradient sẽ cùng chiều với véc-tơ lấy đạo hàm. Hay nói một cách khác, hàm số tăng nhanh nhất theo hướng của gradient và giảm nhanh nhất khi ngược hướng với gradient của nó.

