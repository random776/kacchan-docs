---
sidebar_position: 2
---

# Python：自分用メモ

自分が実験などで解析に使うPythonコードをまとめたものです。

## 数値積分

### 実装

台形公式を用いて計算します。

``` python title="integral.py"
import numpy as np
import matplotlib.pyplot as plt

data1 = np.linspace(0, 2*np.pi, 101)
data2 = np.sin(data1) + 1

# プロット
fig, ax = plt.subplots()
ax.plot(data1, data2)
ax.grid()
plt.show()

# 数値積分（台形公式）
dif = data1[1] - data1[0]
print("計算値", (np.sum(data2[:-1]) + np.sum(data2[1:])) * dif/2)
print("理論値", np.pi * 2)
```

### 結果

![graph](./img/sin.png)

```
計算値 6.283185307179587
理論値 6.283185307179586
```

$$
\int_{0}^{2 \pi} (1 + \sin x) dx = 2 \pi
$$

であり、計算値と理論値とは、$10^{-14}$ 程度のオーダーで一致した。

## 最小二乗法

### 実装

`numpy.polyfit`を使うようです。

``` python title="least_squares.py"
import numpy as np
import matplotlib.pyplot as plt

x_axis = [1, 2, 3, 4]
y_axis = [2, 6, 9, 12]

# 最小二乗法による直線当てはめ
slope, intercept = np.polyfit(x_axis, y_axis, 1)

# 回帰直線のy座標を計算
fit_line = np.array(x_axis) * slope + intercept

# プロット
fig, ax = plt.subplots()
ax.plot(x_axis, y_axis, ".", label="Data points")
ax.plot(x_axis, fit_line, "-", label=f"Fit: y = {slope:.2f}x + {intercept:.2f}")
ax.grid()
ax.legend()
plt.show()
```

### 結果

$y = 3.30x - 1.00$ に線形回帰される。

![graph](./img/least_squares.png)

