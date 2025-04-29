=========
石田 岳志
=========

職歴
====

`CyberAgent AI Lab <https://research.cyberagent.ai/>`__
-------------------------------------------------------

2024年7月 -

`Tier IV, Inc. <https://tier4.jp/>`__
--------------------------------------

車両の自己位置推定の研究開発

* Visual SLAM 技術の調査と実装
* 難しい環境を対象とした位置推定アルゴリズムの開発

2020年7月 - 2024年6月


`未踏プロジェクト <https://www.ipa.go.jp/jinzai/mitou/2019/gaiyou_s-2.html>`__
------------------------------------------------------------------------------

| Visual SLAMフレームワークの開発
| 2019年4月 - 2020年3月


`株式会社ディー・エヌ・エー <https://dena.com/>`__
--------------------------------------------------

| コンピュータビジョンに関連する手法の調査と実装
| 2018年4月 - 2020年3月
| アルバイト


`クックパッド株式会社 <https://info.cookpad.com>`__
---------------------------------------------------

| 料理画像から具材を推定する手法の開発
| 2016年12月 - 2017年7月
| アルバイト


`株式会社ウサギィ <http://usagee.co.jp/>`__
-------------------------------------------

| コンピュータビジョンや自然言語処理におけるさまざまな手法の調査と実装
| 2014年5月 - 2017年1月
| アルバイト

学歴
====

| 東京高専 専攻科 機械情報システム工学専攻
| 2017年4月 - 2019年3月

| フィンランド メトロポリア応用科学大学 (交換留学)
| 2017年8月 - 2017年12月

| 東京高専 情報工学科
| 2012年4月 - 2017年3月


特許
====

`特許6306770 <https://www.j-platpat.inpit.go.jp/web/PU/JPB_6306770/062D067C8381CD29700292EC1ED536D9>`__

料理に用いられている具材を料理画像から推定する手法

成果物
======

`Visual Relocalization  <https://github.com/CyberAgentAILab/visual-relocalization-colmap>`__
--------------------------------------------------------------------------------------------

COLMAP 地図と単眼カメラを活用した自己位置推定

.. image:: images/demo-relocalization.gif
    :width: 800



`icp_rust <https://github.com/tier4/icp_rust>`__
------------------------------------------------

| ICP (Iterative Closest Point) の Rust による実装。
| TIER IV で開発中の OS の上で動かすことを目的としているが、Linux でも動作する。

.. image:: images/icp.gif
    :width: 800

`YDLiDAR Driver <https://github.com/tier4/lidar_feature_extraction>`__
----------------------------------------------------------------------

手のひらサイズの小型な LiDAR である `YDLiDAR T-mini Pro <https://www.ydlidar.com/products/view/22.html>`__ の Rust 製ドライバ。

.. image:: https://raw.githubusercontent.com/tier4/ydlidar_driver/main/images/plot_scan.gif

|

`LOAM (Refactored version) <https://github.com/tier4/lidar_feature_extraction>`__
---------------------------------------------------------------------------------

| LiDAR ベースの SLAM アルゴリズムである LOAM [#Shan_et_al_2018]_ [#Shan_et_al_2020]_ をリファクタリングしたもの。
| 自動運転用 OSS である `Autoware <https://github.com/autowarefoundation/autoware>`__ と統合して動かすことを目的としている。
| LiDAR スキャンから構造特徴を抽出する処理をオリジナルの実装よりも高速化している。
|

`Tadataka <https://github.com/IshitaTakeshi/Tadataka>`__
--------------------------------------------------------

| 　Visual Odometry のアルゴリズムを複数実装したパッケージ。RGB-D カメラ向けのオドメトリ推定アルゴリズムと、特徴点ベースの単眼カメラ向けオドメトリ推定システムを使うことができる。

**DVO (Dense Visual Odometry)** [#Steinbrucker_et_al_2011]_ [#Kerl_et_al_2013]_

.. raw:: html

    <iframe width="560" height="315" src="https://www.youtube.com/embed/oDgBgdHUwOM" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

　深度情報および画像の輝度情報からカメラの移動経路を推定できる。

**特徴点ベースのVisual Odometry**

.. raw:: html

    <iframe width="560" height="315" src="https://www.youtube.com/embed/h4KrMJQDoX4" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

　単眼RGB画像列のみから3次元地図とカメラの軌跡を推定できる。

|

`RoadDamageDetector <https://github.com/IshitaTakeshi/RoadDamageDetector>`__
-------------------------------------------------------------------------------

.. image:: images/road-damage-1.png
    :width: 800

| SSD (Single Shot Multibox Detector) [#Liu_et_al_2016]_ によって道路のひび割れや損傷を検出できる。
| Maeda らの研究 [#Maeda_et_al_2018]_ を追証している。詳細な解説は `Qiita <https://qiita.com/IshitaTakeshi/items/915de731d8081e711ae5>`__ に掲載されている。
|

`SBA <https://github.com/IshitaTakeshi/SBA>`__
-----------------------------------------------

| 高効率な3次元復元アルゴリズム Sparse Bundle Adjustment (SBA) [#Lourakis_et_al_2009]_ を Python で実装した。
|

`Tomasi-Kanade <https://github.com/IshitaTakeshi/Tomasi-Kanade>`__
------------------------------------------------------------------

.. image:: images/tomasi-kanade-output-2.png
    :width: 800

| Tomasi-Kanade法 [#Tomasi_et_al_1992]_ による3次元復元の実装。
| 正投影カメラモデルを用いるので、実世界のアプリケーションとしては利用範囲が限定されるものの、SVDによって高速に3次元復元を実行できる。
|

`Bilinear-Interpolation-SIMD <https://github.com/IshitaTakeshi/Bilinear-Interpolation-SIMD>`__
----------------------------------------------------------------------------------------------

| 画像の画素補間に用いられるアルゴリズム Bilinear Interpolation を SIMD を用いて実装した。
|

`PCANet <https://github.com/IshitaTakeshi/PCANet>`__
-------------------------------------------------------

.. image:: images/pcanet.png
    :width: 800

| 　PCANet [#Chan_et_al_2015]_ はネットワークの重みを PCA で計算することで高速にバッチ学習できるニューラルネットワークである。このネットワークは Pooling 層でヒストグラム計算を行っており、これが実行速度のボトルネックとなっていた。
| 　この問題を解消するため、私はヒストグラム計算を CuPy で GPU 向けに実装した。計算を並列化したことで実行速度も大幅に向上した。CuPy開発者の方々の協力もあり、これは後に CuPy 本体にマージされている。 `#298 <https://github.com/cupy/cupy/pull/298>`__
|

`Ensemble PCANet <https://github.com/IshitaTakeshi/PCANet/tree/ensemble>`__
---------------------------------------------------------------------------

| PCANet をアンサンブル学習することで分類器の性能向上を図った。また、これを研究成果として `JSAI 2017 <https://www.ai-gakkai.or.jp/jsai2017/webprogram/2017/paper-504.html>`__ で発表した。
|

`DTrie <https://github.com/IshitaTakeshi/dtrie>`__
--------------------------------------------------
| 簡潔データ構造 Trie のD言語による実装。
| かな漢字変換で使う辞書を少ないメモリで保持するために実装した。
|

`SCW <https://github.com/IshitaTakeshi/SCW>`__
-------------------------------------------------
| オンライン教師あり線形分類器 SCW (Soft Confidence-Weighted Learning) の実装 [#Wang_et_al_2012]_。
|

Qiita
=====

- `プログラマが持つべき心構え (The Zen of Python) <https://qiita.com/IshitaTakeshi/items/e4145921c8dbf7ba57ef>`__
- `カルマンフィルタってなに？ <https://qiita.com/IshitaTakeshi/items/740ac7e9b549eee4cc04>`__
- `Tomasi-Kanade法による3次元復元 <https://qiita.com/IshitaTakeshi/items/297331b3878e72c65276>`__

執筆
====

- `日経ソフトウエア 2017年8月号 「Pythonで機械学習」 <https://shop.nikkeibp.co.jp/front/commodity/0000/SW1231/>`__
- `日経ソフトウエア 2020年5月号 「撮影した物体を3次元データで復元」 <https://shop.nikkeibp.co.jp/front/commodity/0000/SW1248/>`__
- `TIER IV Tech Blog 「Visual SLAMの可能性と技術的チャレンジ」 <https://tech.tier4.jp/entry/2021/01/27/160000>`__
- `TIER IV Tech Blog 「Visual-Inertial Odometryが自動運転に与えるインパクトと応用への課題」 <https://tech.tier4.jp/entry/2021/07/22/120000>`__
- 日本写真測量学会　写真測量とリモートセンシング　2023年11月号　「市街地における自動運転へのSLAMの活用」

発表資料
=============

- `自己位置推定の取り組み <https://docswell.com/s/IshitaTakeshi/ZXE482-roscon-jp-lt/1>`__
- `Visual Relocalization <https://www.docswell.com/s/IshitaTakeshi/KR264N-visual-relocalization-colmap>`__
- `Graph SLAM の Marginalization <https://drive.google.com/file/d/1PxPDX3rvSvlKhNZMtHt2xWiYyDU44WSj/view?pli=1>`__
- `Sparse Bundle Adjustment <https://speakerdeck.com/ishitatakeshi/sparse-bundle-adjustment>`__
- `3次元回転表現とその実装 <https://speakerdeck.com/ishitatakeshi/3d-rotation-representation-and-its-implementation>`__
- `CUDAでヒストグラム計算を書いてcupyにmergeしてもらった <https://speakerdeck.com/ishitatakeshi/cudadehisutoguramuji-suan-woshu-itecupynimergesitemoratuta-1>`__
- `Juliaでオンライン線形分類器つくった <https://www.slideshare.net/TakeshiIshita/julia-56356347>`__
- `マルウェア分類に用いられる特徴量 Kaggle - Malware Classification Challenge勉強会 <https://www.slideshare.net/TakeshiIshita/kaggle-malware-classification-challenge>`__

リンク
======

- `GitHub       <https://github.com/IshitaTakeshi>`__
- `Blog         <https://ishitatakeshi.netlify.com>`__
- `Qiita        <https://qiita.com/IshitaTakeshi>`__
- `SpeakerDeck  <https://speakerdeck.com/ishitatakeshi>`__

References
==========

.. [#Chan_et_al_2015] Chan, Tsung-Han, et al. "PCANet: A simple deep learning baseline for image classification?." IEEE transactions on image processing 24.12 (2015): 5017-5032.
.. [#Kerl_et_al_2013] Kerl, Christian, Jürgen Sturm, and Daniel Cremers. "Robust odometry estimation for RGB-D cameras." Robotics and Automation (ICRA), 2013 IEEE International Conference on. IEEE, 2013.
.. [#Liu_et_al_2016] Liu, Wei, et al. "Ssd: Single shot multibox detector." Computer Vision–ECCV 2016: 14th European Conference, Amsterdam, The Netherlands, October 11–14, 2016, Proceedings, Part I 14. Springer International Publishing, 2016.
.. [#Lourakis_et_al_2009] Lourakis, Manolis IA, and Antonis A. Argyros. "SBA: A software package for generic sparse bundle adjustment." ACM Transactions on Mathematical Software (TOMS) 36.1 (2009): 1-30.
.. [#Steinbrucker_et_al_2011] Steinbrücker Frank, Jürgen Sturm, and Daniel Cremers. "Real-time visual odometry from dense RGB-D images." Computer Vision Workshops (ICCV Workshops), 2011 IEEE International Conference on. IEEE, 2011.
.. [#Tomasi_et_al_1992] Tomasi, Carlo, and Takeo Kanade. "Shape and motion from image streams under orthography: a factorization method." International journal of computer vision 9 (1992): 137-154.
.. [#Maeda_et_al_2018] Maeda, Hiroya, et al. "Road damage detection using deep neural networks with images captured through a smartphone." arXiv preprint arXiv:1801.09454 (2018).
.. [#Shan_et_al_2018] Shan, Tixiao, and Brendan Englot. "Lego-loam: Lightweight and ground-optimized lidar odometry and mapping on variable terrain." 2018 IEEE/RSJ International Conference on Intelligent Robots and Systems (IROS). IEEE, 2018.
.. [#Shan_et_al_2020] Shan, Tixiao, et al. "Lio-sam: Tightly-coupled lidar inertial odometry via smoothing and mapping." 2020 IEEE/RSJ international conference on intelligent robots and systems (IROS). IEEE, 2020.
.. [#Wang_et_al_2012] Wang, Jialei, Peilin Zhao, and Steven CH Hoi. "Exact soft confidence-weighted learning." arXiv preprint arXiv:1206.4612 (2012).
