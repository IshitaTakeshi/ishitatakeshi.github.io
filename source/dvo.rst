DVO
===

.. raw:: html

    <iframe width="560" height="315" src="https://www.youtube.com/embed/oDgBgdHUwOM" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>


.. math::
    \def\SE(#1){{\mathrm{SE}(#1)}}
    \def\pose{{\mathbf{\xi}}}
    \def\skew#1{{\left[ #1 \right]_{\times}}}
    \def\se(#1){{\mathfrak{se}(#1)}}

|  　Dense Visual Odometry(DVO)とはミュンヘン工科大学によって提供されている，direct methodに基づいたVisual Odometryのパッケージである．上の動画は本稿の筆者がそれを再現実装し，動作させたものである．
|  　DVOはSteinbrückerらの手法 [#Steinbrucker_et_al_2011]_ とKerlらの手法 [#Kerl_et_al_2013]_ から成り立っている．いずれの論文も，2 つの視点で観測されたカメラの輝度情報と深度情報を用いて，視点間の移動を推定する手法について解説している．Steinbrückerらの手法 [#Steinbrucker_et_al_2011]_ では，測光誤差を正規分布でモデル化し，最尤推定によってカメラ姿勢の変化を求めている．一方で，Kerlらの手法 [#Kerl_et_al_2013]_ では，測光誤差をt分布でモデル化し，MAP 推定によってカメラ姿勢の変化を求めている．ここでは2つの手法それぞれについて解説する．

Steinbrückerらの手法 [#Steinbrucker_et_al_2011]_
------------------------------------------------

問題設定
~~~~~~~~

| 　Visual Odometry は，カメラから連続的に得られる輝度情報を用いて，カメラの姿勢変化を逐次的に推定する問題である．カメラの姿勢変化を :math:`\mathbf{\xi} \in \mathbb{R}^{6} \; \textrm{s.t.} \;\skew{\mathbf{\xi}} \in \se(3)` ，時刻 :math:`t` における画像を :math:`I_{t}` ， :math:`I_{t}` の座標 :math:`\mathbf{x}_{i},(i=1,\dots,N)` における輝度を :math:`I_{t}(\mathbf{x}_{i})` で表現する．ここで :math:`\skew{\cdot}` は次で定義される演算子である．

.. math::
    \skew{\mathbf{\xi}} &= \begin{bmatrix}
        0 & -\xi_{6} & \xi_{5} & \xi_{1} \\
        \xi_{6} & 0 & -\xi_{4} & \xi_{2} \\
        -\xi_{5} & \xi_{4} & 0 & \xi_{3} \\
        0 &      0 &      0 &     0 \\
   \end{bmatrix} \in \se(3),\;\mathbf{\xi} \in \mathbb{R}^{6}

:math:`I_{t_{0}}(\mathbf{x}_{i})` が各ピクセル :math:`\mathbf{x}_{i}` についてi.i.dだと仮定すると，2視点間の姿勢変化 :math:`\pose` を画像変化から求める問題は，尤度

.. math::
    L(\xi) = p(I_{t_{0}}|\pose) = \prod_{i} p(I_{t_{0}}(\mathbf{x}_{i})|\pose)

| を最大化する問題とみなすことができる．
| 　問題をより詳細に記述するため，warping関数を定義する．:math:`\mathbf{w}_{\pose}(\mathbf{x}, t)` を，ピクセル :math:`\mathbf{x}` を時刻 :math:`t_{0}` における画像平面から時刻 :math:`t` における画像平面に写す関数

.. math::
    \mathbf{w}_{\pose}(\mathbf{x}, t)
    = \mathbf{\pi}(g(G(t), \mathbf{\pi}^{-1}(\mathbf{x}))) \\

| とする． :math:`G(t)\in \SE(3)` は時刻 :math:`t` におけるカメラ姿勢を表すユークリッド群の元 :math:`G(t) = \exp((t-t_{0})\skew{\pose})G(t_{0})` であり， :math:`g(G, \mathbf{p})` は3次元点 :math:`\mathbf{p} \in \mathbb{R}^{3}` の変換 :math:`g(G, \mathbf{p}) = R\mathbf{p} + \mathbf{t}` を表す関数である．また， :math:`\mathbf{\pi}^{-1}(\mathbf{x})` は投影モデルの逆写像を表す．
| 　Steinbrückerらの手法 [#Steinbrucker_et_al_2011]_ では :math:`p(I_{t_{0}}(\mathbf{x}_{i})|\pose)` を :math:`N(I_{t_{1}}(\mathbf{w}_{\pose}(\mathbf{x}_{i}, t_{1})), \sigma^{2})`  でモデル化し，最尤推定によって姿勢変化 :math:`\pose` を求める．結果として姿勢 :math:`\pose` をパラメータとした二乗誤差関数が得られる．すなわち，

.. math::
    p(I_{t_{0}}(\mathbf{x}_i)|\pose)
    = \frac{1}{\sqrt{2\pi}\sigma}\exp(
        -\frac{
            [I_{t_{0}}(\mathbf{x}_{i})-I_{t_{1}}(\mathbf{w}_{\pose}(\mathbf{x}_{i}, t_{1}))]^2
        }{\sigma^2}
    )

とおいて，推定値 :math:`\hat{\pose}` を求める．

.. math::
    \begin{align}
    \hat{\pose}
    &= \underset{\pose}{\arg \max} \log p(I_{t_{0}}|\pose)  \\
    &= \underset{\pose}{\arg \max} \log \prod_{i} p(I_{t_{0}}(\mathbf{x}_i)|\pose) \\
    &= \underset{\pose}{\arg \max} \sum_{i} \log p(I_{t_{0}}(\mathbf{x}_i)|\pose)
    \end{align}

確率密度関数を代入すると，

.. math::
    \begin{align}
    \hat{\pose}
    &= \underset{\pose}{\arg \max} \sum_{i} \log p(I_{t_{0}}(\mathbf{x}_i)|\pose) \\
    &= \underset{\pose}{\arg \max} \sum_{i} \log [\frac{1}{\sqrt{2\pi}\sigma}
        \exp(-\frac{[I_{t_{0}}(\mathbf{x}_{i})-I_{t_{1}}(\mathbf{w}_{\pose}(\mathbf{x}_{i}, t_{1}))]^2}{\sigma^2})
    ]
    \end{align}

定数項を省くと

.. math::
    \hat{\pose} = \underset{\pose}{\arg \min}\; \sum_{i}[I_{t_{0}}(\mathbf{x}_{i})-I_{t_{1}}(\mathbf{w}_{\pose}(\mathbf{x}_{i}, t_{1}))]^2

となり，二乗誤差関数

.. math::
    E(\pose)
    = \sum_{i}[I_{t_{1}}(\mathbf{w}_{\pose}(\mathbf{x}_{i}, t_{1})) - I_{t_{0}}(\mathbf{x}_{i})]^2
    :label: error-function-dvo


が得られる．


解法
~~~~

| 　Steinbrückerらの手法 [#Steinbrucker_et_al_2011]_ では誤差関数を一次近似し， :eq:`error-function-dvo` を最小二乗法に落としこむ．
| 　まず画像を空間方向に一次近似する．

.. math::
    I_{t_{1}}(\mathbf{x}_{i} + \Delta\mathbf{x})
    \approx I_{t_{1}}(\mathbf{x}_{i}) +
    \frac{\partial I_{t_{1}}(\mathbf{x}_{i})}{\partial \mathbf{x}}
    \Delta \mathbf{x}

:math:`\mathbf{x}_{i} + \Delta\mathbf{x} = \mathbf{w}_{\pose}(\mathbf{x}_{i}, t_{1})` とおくと次のようになる．

.. math::
    I_{t_{1}}(\mathbf{w}_{\pose}(\mathbf{x}_{i}, t_{1}))
    \approx I_{t_{1}}(\mathbf{x}_{i}) +
    \frac{\partial I_{t_{1}}(\mathbf{x}_{i})}{\partial \mathbf{x}}
    (\mathbf{w}_{\pose}(\mathbf{x}_{i}, t_{1})-\mathbf{x}_{i})
    :label: imageapprox


warping関数を近似する．

.. math::
    \begin{align}
    \mathbf{w}_{\pose}(\mathbf{x}_{i}, t_{1})
    &\approx \mathbf{w}_{\pose}(\mathbf{x}_{i}, t_{0}) +
    \frac{\partial \mathbf{w}_{\pose}(\mathbf{x}_{i}, t_{0})}{\partial t}(t_{1} - t_{0}) \\
    &= \mathbf{x}_{i} +
    \frac{\partial \mathbf{w}_{\pose}(\mathbf{x}_{i}, t_{0})}{\partial t} (t_{1} - t_{0})
    \end{align}


これを :eq:`imageapprox` に代入すると


.. math::
    I_{t_{1}}(\mathbf{w}_{\pose}(\mathbf{x}_{i}, t_{1}))
    \approx I_{t_{1}}(\mathbf{x}_{i}) +
    \frac{\partial I_{t_{1}}(\mathbf{x}_{i})}{\partial \mathbf{x}}
    \frac{\partial \mathbf{w}_{\pose}(\mathbf{x}_{i}, t_{0})}{\partial t} (t_{1} - t_{0})


:math:`t_{1} - t_{0}` はフレームの撮影間隔(フレームレートの逆数)である．今回は :math:`t_{1} - t_{0} = 1` とおく．すなわち，フレームの撮影間隔を1単位時間とみなす．

.. math::
    I_{t_{1}}(\mathbf{w}_{\pose}(\mathbf{x}_{i}, t_{1}))
    \approx I_{t_{1}}(\mathbf{x}_{i}) +
    \frac{\partial I_{t_{1}}(\mathbf{x}_{i})}{\partial \mathbf{x}}
    \frac{\partial \mathbf{w}_{\pose}(\mathbf{x}_{i}, t_{0})}{\partial t}

この結果を用いて誤差関数を書き換えると次のようになる．

.. math::
    \begin{align}
    E(\pose)
        &= \sum_{i}[I_{t_{1}}(\mathbf{w}_{\pose}(\mathbf{x}_{i}, t_{1})) - I_{t_{0}}(\mathbf{x}_{i})]^2 \\
        &\approx \sum_{i}[
            I_{t_{1}}(\mathbf{x}_{i})-I_{t_{0}}(\mathbf{x}_{i}) +
            \frac{\partial I_{t_{1}}(\mathbf{x}_{i})}{\partial \mathbf{x}}
            \frac{\partial \mathbf{w}_{\pose}(\mathbf{x}_{i}, t_{0})}{\partial t}
        ]^2
    \end{align}
    :label: modified-error-function-dvo


| 　さて， :math:`I_{t_{1}}(\mathbf{x}_{i})-I_{t_{0}}(\mathbf{x}_{i})` は画像間の差分を意味しており， :math:`\frac{\partial I_{t_{1}}(\mathbf{x}_{i})}{\partial \mathbf{x}}` は一次の勾配を表しているため，これらは容易に実装することができる．しかし :math:`\frac{\partial \mathbf{w}_{\pose}(\mathbf{x}_{i}, t_{0})}{\partial t}` はその中身が具体的なかたちで書かれていないため，さらに詳しく表現する必要がある．
| 　warping関数の微分は，chain ruleより

.. math::
    \begin{align}
    \frac{
        \partial \mathbf{w}_{\pose}(\mathbf{x}_{i}, t_{0})
    }{\partial t}
    &= \frac{\partial \mathbf{\pi}(g(G(t_{0}), \mathbf{p}_{i}))}{\partial g}
    \cdot \frac{\partial g(G(t_{0}), \mathbf{p}_{i})}{\partial G}
    \cdot \frac{\partial G(t_{0})}{\partial t}
    \end{align}

である．:math:`\frac{\partial G(t)}{\partial t} = \skew{\mathbf{\xi}}G(t)` (参考: :doc:`so3`) を用いると，

.. math::
    \begin{align}
    \frac{
        \partial \mathbf{w}_{\pose}(\mathbf{x}_{i}, t_{0})
    }{\partial \pose}
    &= \frac{\partial \mathbf{\pi}(g(G(t_{0}), \mathbf{p}_{i}))}{\partial g}
    \cdot \frac{\partial g(G(t_{0}), \mathbf{p}_{i})}{\partial G}
    \cdot \skew{\pose} \cdot G(t_{0})
    \end{align}

:math:`\frac{\partial g(G(t_{0}), \mathbf{p}_{i})}{\partial G}` は行列 :math:`G` による微分であり，コードで実装すると3次元配列になってしまう．
これを回避するため，以下で定義される :math:`\mathrm{stack}(G)` を導入する．

    :math:`G \in \SE(3)` を

    .. math::
       G = \begin{bmatrix}
           r_{11} & r_{12} & r_{13} & t_{1} \\
           r_{21} & r_{22} & r_{23} & t_{2} \\
           r_{33} & r_{32} & r_{33} & t_{3} \\
                0 &      0 &      0 &     1 \\
       \end{bmatrix}

    と表現したとき

    :math:`\mathrm{stack}(G) = \begin{bmatrix} r_{11} & r_{21} & r_{33} & r_{12} & r_{22} & r_{32} & r_{13} & r_{23} & r_{33} & t_{1} & t_{2} & t_{3} \end{bmatrix}^{\top}`

ここで， :math:`\mathrm{stack}(\skew{\pose_{k}} \cdot G(t_{0})) = J_{G} \cdot \pose` を満たすような :math:`J_{G}` が存在する(具体的な導出は後で示す)．これを用いると，

.. math::
    \begin{align}
    \frac{\partial \mathbf{w}_{\pose}(\mathbf{x}_{i}, t_{0})}{\partial \pose}
    &= \frac{\partial \mathbf{\pi}(g(G(t_{0}), \mathbf{p}_{i}))}{\partial g}
    \cdot \frac{\partial g(G(t_{0}), \mathbf{p}_{i})}{\partial \mathrm{stack}(G)}
    \cdot J_{G} \cdot \pose
    \end{align}

もとの誤差関数 :eq:`modified-error-function-dvo` に代入すると

.. math::
    \begin{align}
    E(\pose)
    &= \sum_{i}\left[
        I_{t_{1}}(\mathbf{w}_{\pose}(\mathbf{x}_{i}, t_{1}))-I_{t_{0}}(\mathbf{x}_{i})
    \right]^2 \\
    &\approx \sum_{i}\left[
        I_{t_{1}}(\mathbf{x}_{i}) -
        I_{t_{0}}(\mathbf{x}_{i}) +
        \frac{\partial I_{t_{1}}(\mathbf{x}_{i})}{\partial \mathbf{x}}
        \frac{\partial \mathbf{\pi}(g(G(t_{0}), \mathbf{p}_{i}))}{\partial g}
        \cdot \frac{\partial g(G(t_{0}), \mathbf{p}_{i})}{\partial \mathrm{stack}(G)}
        \cdot J_{G} \cdot \pose
    \right]^2
    \end{align}

となる．

.. math::
    \begin{align}
    C_{i}
    &=  \frac{\partial I_{t_{1}}(\mathbf{x}_{i})}{\partial \mathbf{x}}
        \cdot \frac{\partial \mathbf{\pi}(g(G(t_{0}, \mathbf{p}_{i})))}{\partial g}
        \cdot \frac{\partial g(G(t_{0}, \mathbf{p}_{i}))}{\partial \mathrm{stack}(G)}
        \cdot J_{G} \cdot \pose \\
    y_{i}
    &= -\left[ I_{t_{1}}(\mathbf{x}_{i}) - I_{t_{0}}(\mathbf{x}_{i}) \right]
    \end{align}

とおけば，誤差関数 :math:`E(\pose)` は

.. math::
    E(\pose) \approx \sum_{i} \left[ C_{i} \pose - y_{i} \right]^2

| という最小二乗法の形で記述でき，これを解けば :eq:`error-function-dvo` を近似的に最小化する姿勢変化 :math:`\pose` が得られる．
| 　以降は :math:`C_{i}` の各項の具体的な形を計算していく． :math:`\frac{\partial I_{t_{1}}(\mathbf{x}_{i})}{\partial \mathbf{x}}` については先述のとおりであるため，それ以外の項を計算する．


.. math::
   G(t) = \begin{bmatrix}
       r_{11} & r_{12} & r_{13} & t_{1} \\
       r_{21} & r_{22} & r_{23} & t_{2} \\
       r_{33} & r_{32} & r_{33} & t_{3} \\
            0 &      0 &      0 &     1 \\
   \end{bmatrix}

と表現すると， :math:`J_{G}` は

.. math::
    J_{G} = \begin{bmatrix}
        0 & 0 & 0 & 0       & r_{31}  & -r_{21}\\
        0 & 0 & 0 & -r_{31} & 0       & r_{11} \\
        0 & 0 & 0 & r_{21}  & -r_{11} & 0      \\
        0 & 0 & 0 & 0       & r_{32}  & -r_{22}\\
        0 & 0 & 0 & -r_{32} & 0       & r_{12} \\
        0 & 0 & 0 & r_{22}  & -r_{12} & 0      \\
        0 & 0 & 0 & 0       & r_{33}  & -r_{23}\\
        0 & 0 & 0 & -r_{33} & 0       & r_{13} \\
        0 & 0 & 0 & r_{23}  & -r_{13} & 0      \\
        1 & 0 & 0 & 0       & t_{3}   & -t_{2} \\
        0 & 1 & 0 & -t_{3}  & 0       & t_{1}  \\
        0 & 0 & 1 & t_{3}   & -t_{1}  & 0      \\
    \end{bmatrix}

| となる．  :math:`J_{G}` は確かに :math:`J_{G} \cdot \pose = \mathrm{stack}(\skew{\pose} \cdot G)` を満たしている．
| 　:math:`\partial g / \partial \mathrm{stack}(G)` は， :math:`G(t)` によって変換された点 :math:`\mathbf{p}' = g(G(t), \mathbf{p})` を :math:`G(t)` の各成分で微分したものなので，

.. math::
    \frac{\partial g(G(t), \mathbf{p})}{\partial \mathrm{stack}(G)}
    = \begin{bmatrix}
    x & 0 & 0 & y & 0 & 0 & z & 0 & 0 & 1 & 0 & 0 \\
    0 & x & 0 & 0 & y & 0 & 0 & z & 0 & 0 & 1 & 0 \\
    0 & 0 & x & 0 & 0 & y & 0 & 0 & z & 0 & 0 & 1 \\
    \end{bmatrix}

である． :math:`\mathbf{p}' = \left[x', y', z'\right] = g(G(t), \mathbf{p})` とおくと

.. math::
    \frac{\partial g(G(t), \mathbf{p})}{\partial \mathrm{stack}(G)}
    \cdot J_{G}
    = \begin{bmatrix}
        1 & 0 & 0 & 0 & z' & -y' \\
        0 & 1 & 0 & -z' & 0 & x' \\
        0 & 0 & 1 & y' & -x' & 0 \\
    \end{bmatrix}

　以上より :math:`C_{i}` が計算できる．

.. math::
    \begin{align}
    C_{i}
    &=  \frac{\partial I_{t_{1}}(\mathbf{x}_{i})}{\partial \mathbf{x}} \cdot
        \frac{\partial \mathbf{\pi}(\mathbf{p}'_{i})}{\partial \mathbf{p}'} \cdot
        \frac{\partial g(G(t_{1}), \mathbf{p}_{i})}{\partial \mathrm{stack}(G)} \cdot
        J_{G} \\
    &= \frac{\partial I_{t_{1}}(\mathbf{x}_{i})}{\partial \mathbf{x}}
       \begin{bmatrix}
            \frac{f_{x}}{z'} & 0 & -\frac{f_{x}x'}{z'^2} &
            -\frac{f_{x}x'y'}{z'^2} & f_{x}(1+\frac{x'^2}{z'^2}) & -\frac{f_{x}y'}{z'} \\
            0 & \frac{f_{y}}{z'} & -\frac{f_{y}y'}{z'^2} &
            -f_{y}(1+\frac{y'^2}{z'^2}) & \frac{f_{y}x'y'}{z'^2} & \frac{f_{y}x'}{z'} \\
        \end{bmatrix}
    \end{align}


Kerlらの手法 [#Kerl_et_al_2013]_
--------------------------------

　Kerlらの手法 [#Kerl_et_al_2013]_ は，Steinbrückerらの手法 [#Steinbrucker_et_al_2011]_ をつぎの点で改良した手法である．

-  測光誤差をt分布でモデル化し，MAP推定で解いている
-  t分布は裾野が広いため外れ値に対して高いロバスト性を確保できる
-  MAP推定を用いているため，IMUなどカメラ以外から得られた情報を事前分布として使うことができる

MAP推定による記述
~~~~~~~~~~~~~~~~~

　姿勢変化を推定する問題は最尤推定で記述できるが，この論文では最尤推定ではなくMAP推定で記述している．これはMAP推定を用いるとIMUなどのカメラ以外のセンサ情報を事前分布 :math:`p(\pose)` として設定することができるためである．すなわちベイズの定理から

.. math::
    p(\pose|I_{t_{1}}) \propto p(I_{t_{1}}|\pose)p(\pose)

として

.. math::
    \begin{align}
    \pose_{\text{MAP}}
    &= \underset{\pose}{\arg \max}\; p(\pose|I_{t_{1}})\\
    &= \underset{\pose}{\arg \max}\; p(I_{t_{1}}|\pose)p(\pose) \\
    &= \underset{\pose}{\arg \max}\;
        \prod_{i} p(I_{t_{1}}(\mathbf{x}_{i})|\pose)p(\pose) \\
    &= \underset{\pose}{\arg \max}\;
        [\sum_{i}\log p(I_{t_{1}}(\mathbf{x}_{i})|\pose) + \log p(\pose)]
    \end{align}

を解いている．事前分布 :math:`p(\pose)` は自由に設定できるため，このうち対数尤度関数 :math:`\log p(r_{i}|\pose)` のみに着目し式を変形していくと

.. math::
    \begin{align}
    \pose_{\text{MAP}} &= \underset{\xi}{\arg \min} \sum_{i} w(r_{i})(r_{i}(\pose))^2 \\
    r_{i} &= I_{t_{1}}(\mathbf{w}_{\pose}(\mathbf{x}_{i}, t_{1})) - I_{t_{1}}(\mathbf{x}_{i})
    \end{align}

という式が得られる．これはピクセルごとの誤差 :math:`r_{i}(\pose)` を :math:`w(r_{i})` で重み付けし総和をとったものだと解釈することができる． :math:`w(r_{i})` の形は分布の設定方法によって変わってくるが，測光誤差 :math:`r_{i}` がt分布に従うという仮定のもとでは

.. math::
    w(r_{i}) = \frac{\nu + 1}{\nu + \frac{r_{i}^{2}}{\sigma_{2}}}

となる．ここで分散は

.. math::
    \sigma^{2} = \frac{1}{n} \sum_{i} r_{i}^{2} \frac{\nu + 1}{\nu + \frac{r_{i}^{2}}{\sigma^{2}}}

で与えられる． :math:`n` は画像のピクセル数である．分散の式は再帰的に記述されているが， :math:`\sigma^{2}` の値は数回の反復で収束する．


.. [#Steinbrucker_et_al_2011] Steinbrücker Frank, Jürgen Sturm, and Daniel Cremers. "Real-time visual odometry from dense RGB-D images." Computer Vision Workshops (ICCV Workshops), 2011 IEEE International Conference on. IEEE, 2011.
.. [#Kerl_et_al_2013] Kerl, Christian, Jürgen Sturm, and Daniel Cremers. "Robust odometry estimation for RGB-D cameras." Robotics and Automation (ICRA), 2013 IEEE International Conference on. IEEE, 2013.
