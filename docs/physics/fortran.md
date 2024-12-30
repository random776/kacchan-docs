---
sidebar_position: 3
---

# Fortran：自分用メモ

> 最終更新：2024 年 12 月 30 日

自分が物理学の勉強で使う Fortran コードをまとめたものです。自分用のメモになる程度の内容です。

## 数値シミュレーション用のdatファイルの出力

1. `data` ディレクトリを作成

1. `simulation.f90` に数値計算のコードを書く

1. 数値計算のコードにファイル出力のコードを追加（以下）

```fortran title="simulation.f90"
program simulation
    implicit none

    ! 各変数の宣言

    integer :: n_out
    character(128) :: filename
    integer :: ios

    n_out = 0
    t = 0.0_8

    ! 初期条件の設定

    do while (t <= t_end)
        
        ! 時間発展の計算

        if (mod(n_out, 10) == 0) then ! 10ステップごとに出力
            write(filename, '("./data/", i4.4, ".dat")') n_out ! ファイル名を作成
            open(10, file=filename, status='replace', iostat=ios) ! ファイルを開く
            if (ios /= 0) then
                print *, "ファイルを開けません: ", filename
                stop
            end if
            write(10, *) u ! データを書き込む
            close(10)
        end if

        ! 時間を進める
        t = t + dt

        ! 出力番号を更新
        n_out = n_out + 1
    end do

end program simulation
```

## これをPythonで読み込む

以下のコードを使うと、`data` ディレクトリに保存された全ての `dat` ファイルを読み込んで、アニメーションを作成できる。

```python title="read.py"
import matplotlib.pyplot as plt
import numpy as np
import glob

import re

from matplotlib import animation, rc

# 自然順ソート用のキー生成関数
def natural_key(string_):
    return [int(s) if s.isdigit() else s for s in re.split(r'(\d+)', string_)]

# 正規表現に一致する全てのファイルを取得
file_list = glob.glob('./data/*.dat')

# 自然順でソート
file_list.sort(key=natural_key)

# 各ファイルからデータを読み込んでリストに格納
data_list = [np.loadtxt(file) for file in file_list]

# 全てのデータを一つの配列に結合
data_sniplet = np.vstack(data_list)

# データの表示
ims = []

fig, ax = plt.subplots(figsize=(5,4)) # グラフの描画
ax.set_title("Numerical solution")
ax.set_xlabel("x")
ax.set_ylabel("y")
ax.set_ylim(-1.2, 1.2)
ax.grid()

x = np.linspace(0, 1, N)
print(x.shape)

for i in range(len(data_sniplet)): 
    im = ax.plot(x, data_sniplet[i, 1:N+1], c="green")
    ims.append(im)

anim = animation.ArtistAnimation(fig, ims, interval=10)
rc("animation", html="jshtml")
plt.close()
# anim.save("./plot6.gif",writer='imagemagick') # gif で保存
anim

```

