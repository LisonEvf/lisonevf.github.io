---
title: Pytorch入门
date: 2022-12-30 16:57:35
tags: Pytorch
mathjax: true
---

# 环境

[![Anaconda](https://img.shields.io/badge/Anaconda-green?style=for-the-badge&logo=Anaconda&logoColor=white)](https://www.anaconda.com/downloads)

[![Jupyter](https://img.shields.io/badge/Jupyter-orange?style=for-the-badge&logo=Jupyter&logoColor=white)](https://jupyter.org/)

优势在于可以即时运行，随时观察结果

```python
import torch
import numpy as np
```


# 基础知识

```python
# Vector
t1 = torch.tensor([1., 2, 3, 4])
t2 = torch.tensor([[11., 12], [21, 22], [31, 32]])
```
> tensor([1., 2., 3., 4.])
> tensor([[11., 12.],
          [21., 22.],
          [31., 32.]])


```python
# Matrix
x = np.array([[1, 2], [3, 4]])
y = torch.from_numpy(x)
```

# 线性回归

先看案例中的线性回归
|  Region   | Temp.(F)  |  Rainfall(mm)   | Humidity(%)  |  Apples(ton)   | Oranges(ton)  |
|  ----  | ----  |  ----  | ----  |  ----  | ----  |
| Kanto  | 73 | 67  | 43 | 56  | 70 |
| Johto  | 91 | 88  | 64 | 81  | 101 |
| Hoenn  | 87 | 134  | 58 | 119  | 133 |
| Sinnoh  | 102 | 43  | 37 | 22  | 37 |
| Unova  | 69 | 96  | 70 | 103  | 119 |

`各地区因环境因素差异而造成苹果和橙子的产量差异`

于是有以下线性回归方程用以预测水果产量，相对于不同水果，温度、降水、湿度分别予以不同的权重，外加一个偏差

$$yieldApple = w11 * temp + w12 * rainfall + w13 * humidity + b1$$
$$yieldOrange = w21 * temp + w22 * rainfall + w23 * humidity + b2$$

此刻，我们得到了
1. 长度为5，宽度为3的输入
2. 长度为5，宽度为2的输出
3. 长度为3，宽度为2的权重
4. 宽度为2的偏差

```python
# Input (temp, rainfall, humidity)
inputs = np.array([[73, 67, 43],
                   [91, 88, 64],
                   [87, 134, 58],
                   [102, 43, 37],
                   [69, 96, 70]], dtype='float32')

# Targets (apple, oranges)
inputs = np.array([[56, 70],
                   [81, 101],
                   [119, 133],
                   [22, 37],
                   [103, 119]], dtype='float32')

# Convert inputs and targets to tensors
inputs = torch.from_numpy(inputs)
targets = torch.from_numpy(targets)

# 权重和偏差从随机数开始
# Weights 
w = torch.randn(2, 3, requires_grad=True)
# Biases
b = torch.randn(2, requires_grad=True)
```

\begin {pmatrix}
     1 & a_1 & a_1^2 & \cdots & a_1^n \\
     1 & a_2 & a_2^2 & \cdots & a_2^n \\
     \vdots  & \vdots& \vdots & \ddots & \vdots \\
     1 & a_m & a_m^2 & \cdots & a_m^n    
\end {pmatrix} 

所以我们定义模型
```python
def model(input):
    return input @ w.t() + b
```
预测即为
```python
preds = model(inputs)
```
这里的权重和偏移是随机生成的，所以预测结果合不上很正常

# 损失函数
##### 1.计算preds和targets之间的差异
##### 2.平方差矩阵所有元素以消除负值
##### 3.计算结果矩阵中元素的平均值
##### 最终结果为均方误差MSE

```python
# MSE loss
def mse(t1, t2):
    diff = t1 - t2
    return torch.sum(diff * diff) / diff.numel()

# Compute loss
loss = mse(preds, targets)
```

计算梯度

```python
# Compute gradients
loss.backward()
```

计算权重梯度

```python
# Gradients for weights
w.grad
```

重置梯度

```python
# Reset the gradients
w.grad.zero_()
b.grad.zero_()
```

> 1. 生成预测
  2. 计算损失
  3. 计算梯度w.r.t权重和偏差
  4. 通过减去与梯度成比例的小量来调整权重
  5. 将渐变重置为零

```python
# Train for 100 epochs
for _ in range(100):
    preds = model(inputs)
    loss = mse(preds, targets)
    loss.backward()
    with torch.no_grad():
        w -= w.grad * 1e-5
        b -= b.grad * 1e-5
        w.grad.zero_()
        b.grad.zero_()
```

# 总结

创建TensorDataset和DataLoader
```python
from torch.utils.data import TensorDataset
from torch.utils.data import DataLoader

batch_size = 5

train_ds = TensorDataset(inputs, targets)
train_dl = DataLoader(train_ds, batch_size, shuffle=True)
```

```python
class Net(nn.Module):
    def __init__(self):
        super(Net, self).__init__()
        self.linear = nn.Linear(1,1)

    def forward(self, x):
        out = self.linear(x)
        return out
```
