

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

> Here is an example of the derivative through a 2-variable function. Other multivariate functions are similar.

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

### Importance

> Apply the matrix vectorization to the general case as follows:

Cost function:

$$J(\theta) = \frac{1}{2m}(X\theta - y)^{T}(X\theta - y)$$

Gradient Descent

$$\theta = \theta - \frac{\alpha}{m} * (X^T (X\theta - y)) $$



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
m = len(df)
X = np.append(arr = np.ones((m,1)).astype(int), values = df[[0,1]], axis = 1) # training examples
y = df.iloc[:,2].to_numpy(dtype = float).reshape(-1, 1) # real values
theta = np.zeros((X.shape[1],1)) 
iters = 1000 # iteration
alpha = 0.001 # learning rate
cost = np.zeros((iters, 1)) # Cost function to visualization
```

Data Preprocessing

``` Python
from sklearn.preprocessing import MinMaxScaler
#Normalization for data not follow a Gaussian distribution

# Transform data
norm_X = MinMaxScaler().fit(X)
X_train = norm_X.transform(X)
norm_y = MinMaxScaler().fit(y)
y_train = norm_y.transform(y)
# First column is bias equal 1
X_train[:,0] = 1
```

Compute Cost function

``` python
# to monitor the convergence by computing the cost.
def computeCost(X, y, theta):
    r = X @ w - y
    return 0.5*np.sum(r*r)
```

Gradient Descent

``` python
def GradientDescentMulti(X, y, theta, alpha):
    m = float(len(y))
    theta = theta - (alpha / m) * (X.T @ (X @ theta - y))
    return theta
```

Training model

``` python
#training(X, y, theta, alpha, iters, cost):
for i in range(0, iters):
    r = X_train @ theta - y_train
    cost[i] = 0.5*np.sum(r*r)
    theta = GradientDescentMulti(X_train, y_train, theta, alpha)
```

Visualize cost function

``` Python
plt.plot(np.arange(len(cost)), cost, color = 'r')

# Chose the good learning rate to get like below.

```

![Cost function](https://github.com/pywind/pywind.github.io/blob/master/assets/img/cost-function.png)

## Linear Regression with scikit-learn
```Python
from sklearn.linear_model import LinearRegression
from sklearn.model_selection import train_test_split

X = df.iloc[:, :2]
y = df.iloc[:,2]
X_train, X_test, y_train, y_test = train_test_split(X_, y_, test_size=0.3, random_state=42)
lm = LinearRegression()
lm.fit(X_train, y_train)

prediction = lm.predict(X_test)
plt.scatter(y_test, prediction)
```

# This END!

## Đoạn này sẽ bằng tiếng Việt :v

### Toán học

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

