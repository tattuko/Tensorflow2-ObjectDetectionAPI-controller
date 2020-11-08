# Tensorflow2-controller
![Videotogif](https://user-images.githubusercontent.com/53849682/98470230-adce9d00-2227-11eb-9de0-2596827d3ccf.gif)

VoTTでのアノテーションをローカルPCで実施し、学習～推論はColaboratory上で実施します。<br><br>
以下の内容を含みます。<br>
* 学習用データセット
* テスト用データセット
* ファインチューニング用モデル(EffientDet D0)
* Colaboratory用スクリプト(環境設定、モデル訓練、推論結果確認)

<details>
<summary>ディレクトリ構成</summary>

<pre>
│ [Colaboratory]Tensorflow2_controller.ipynb
|
├─01_train_data─┬─000000.jpg
│               │     :
│               └─000049.jpg
│      
├─02_tfrecord
│      
├─03_pretrained_model─efficientdet_d0_coco17_tpu-32─┬─pipeline.config
│                                                   ├─checkpoint──┬─checkpoint
│                                                   │             ├─ckpt-0.data-00000-of-00001
│                                                   │             └─ckpt-0.index
│                                                   └─saved_model─┬─saved_model.pb
│                                                                 └─variables─┬─variables.data-00000-of-00001
│                                                                             └─variables.index
│
└─04_test_data─┬─000101.jpg
               │     :
               └─000128.jpg

</pre>

#### [Colaboratory]Tensorflow2_controller.ipynb
Colaboratory用スクリプト(環境設定、モデル訓練、推論結果確認)

#### 01_train_data
学習用データセット ※アノテーション未実施

#### 02_tfrecord
アノテーション実施済みTFRecord格納先

#### 03_pretrained_mode
ファインチューニング用モデル(EffientDet D0)

#### 04_test_data
テスト用データセット

</details>

# Requirement
Tensorflow 2.3.0

# Overview
2時間程度のボリュームの想定です。
1. VoTT：アノテーション(約30～60分)
1. Colaboratory：Object Detection API設定
1. パイプラインコンフィグ修正
1. Colaboratory：モデル訓練(約25分)
1. Colaboratory：推論

# Preparations
事前準備として以下が必要です。
* このリポジトリのローカル環境へのクローン
* [VoTT](https://github.com/microsoft/VoTT)のインストール
* Googleアカウント(Google Colaboratory、Googleドライブで使用)
* [Anaconda](https://www.anaconda.com/products/individual)のインストール

# 1. VoTT：アノテーション
[VoTT](https://github.com/microsoft/VoTT)を使用してアノテーションを行い、TFRecord形式で出力します。

<details>
<summary>VoTTのプロジェクト設定</summary>
	
#### 「新規プロジェクト」を選択する
![2020-09-19 (3)](https://user-images.githubusercontent.com/53849682/97794196-2d61d800-1c3a-11eb-91e4-a095393537d1.png)
#### プロジェクト設定を行う
表示名：Tensorflow2-controller<br>
セキュリティトークン：Generate New Security Token<br>
ソース接続：「Add Connection」を押下<br>
![2020-09-19 (4)](https://user-images.githubusercontent.com/53849682/97794199-35ba1300-1c3a-11eb-908e-2427a6edcd15.png)
#### ソース接続の接続設定を行う
表示名：Tensorflow2-controller-TrainData
![2020-09-19 (6)](https://user-images.githubusercontent.com/53849682/97794201-394d9a00-1c3a-11eb-8134-557fd0a8c85b.png)
プロバイダー：ローカルファイルシステム
![2020-09-19 (7)](https://user-images.githubusercontent.com/53849682/97794202-3f437b00-1c3a-11eb-8652-3941ba91bb78.png)
フォルダーパス：クローンしたリポジトリの「01_train_data」ディレクトリを指定
![2020-09-19 (8)](https://user-images.githubusercontent.com/53849682/97794204-41a5d500-1c3a-11eb-9c0d-e9432f382995.png)
#### ターゲット接続の接続設定を行う
ターゲット接続：Add Connection
![2020-09-19 (9)](https://user-images.githubusercontent.com/53849682/97794206-44082f00-1c3a-11eb-8446-a680ca9da92d.png)
表示名：Tensorflow2-controller-TFRecord<br>
プロバイダー：ローカルファイルシステム<br>
フォルダーパス：クローンしたリポジトリの「02_tfrecord」ディレクトリを指定<br>
![2020-09-19 (10)](https://user-images.githubusercontent.com/53849682/97794207-466a8900-1c3a-11eb-8a09-4cb13f481dff.png)
<!-- #### 8
![2020-09-19 (11)](https://user-images.githubusercontent.com/37477845/94047572-3d052a00-fe0d-11ea-80cb-e6b2f39fbfc9.png)-->
#### タグを追加し設定を保存する
タグ：「controller」を追加<br>
「プロジェクトを保存」を押下
![94047577-3d9dc080-fe0d-11ea-9f4f-b5fe7727fc12](https://user-images.githubusercontent.com/53849682/97794209-48cce300-1c3a-11eb-9a75-d7c3578a6885.png)
</details>

<details>
<summary>VoTTを使用してアノテーションを実施</summary>
	
#### マウス左ドラッグでコントローラーを選択する
![2020-09-19 (13)](https://user-images.githubusercontent.com/37477845/94047578-3e365700-fe0d-11ea-86b9-2d88ef24d0c0.png)
#### TAGSから「controller」を選択する
南京錠のマークを選択しておくことでタグを使用するタグを固定することが可能
![2020-09-19 (14)](https://user-images.githubusercontent.com/37477845/94047588-41314780-fe0d-11ea-9574-0cb6c77f8be5.png)
<!-- #### 12
![2020-09-19 (15)](https://user-images.githubusercontent.com/37477845/94047598-442c3800-fe0d-11ea-9285-d72713520a65.png)-->
</details>

<details>
<summary>TFRecordエクスポート</summary>
	
#### エクスポート設定
プロバイダー：Tensorflow レコード<br>
アセットの状態：タグ付きアセットのみ<br>
「エクスポート設定を保存」を押下する
![2020-09-19 (16)](https://user-images.githubusercontent.com/37477845/94047601-44c4ce80-fe0d-11ea-89fc-92b86e4ba3b8.png)
アノテーション画面からエクスポートマークを押下し、TFRecordをエクスポートする。
![2020-09-19 (14)](https://user-images.githubusercontent.com/37477845/94047588-41314780-fe0d-11ea-9574-0cb6c77f8be5.png)
</details>

<details>
<summary>注意事項</summary>

画像の端の対象をアノテーションする際に、以下のように端から少し隙間を設けてください。
![2020-09-19 (17)](https://user-images.githubusercontent.com/37477845/94047603-44c4ce80-fe0d-11ea-8c0d-3ebc2e740560.png)<br>
問題の詳細は確認中ですが、隙間を開けずにアノテーションをすると、<br>
VoTTの問題かTensorflowの問題か、モデル学習時にエラーが発生します。

</details>

# 2. Colaboratory：Object Detection API設定
[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/tattuko/Tensorflow2-controller/blob/main/[Colaboratory]Tensorflow2-controller.ipynb)<br>
以降の作業はGoogle Colaboratory上で実施します。※パイプラインコンフィグ修正をのぞく<br>
[Open In Colab]リンクからノートブックを開き、以下の順に実行してください。
* Google Driveマウント
* Tensorflow Object Detection API設定
* Tensorflow2-controllerリポジトリクローン

# 3.TFRecordアップロード
「Tensorflow2-controller/02_tfrecord」に<br>VoTTからエクスポートしたTFRecordとtf_label_map.pbtxtを格納してください。<br>
格納後、以下を実行してください。
* 学習データ/検証データ 分割

# 4. パイプラインコンフィグ修正
「03_pretrained_model\efficientdet_d0_coco17_tpu-32\pipeline.config」のパイプラインコンフィグを以下のように修正して、<br>
Colaboratory上の「Tensorflow2-controller/03_pretrained_model」にアップロードしてください。<br>
<details>
<summary>パイプラインコンフィグ修正箇所</summary>

* 3行目(Line 3)：クラス数(num_classes)<br>変更前(Before) : 90<br>変更後(After) : 1<br>
* 134行目(Line 134)：バッチサイズ(batch_size)<br>変更前(Before) : 128<br>変更後(After) : 16<br>
* 161行目(Line 161)：ファインチューニング用のチェックポイント格納先(fine_tune_checkpoint)<br>変更前(Before) : "PATH_TO_BE_CONFIGURED"<br>変更後(After) : "/content/models/research/Tensorflow2-controller/03_pretrained_model/efficientdet_d0_coco17_tpu-32/checkpoint/ckpt-0"
* 167行目(Line 167)：ファインチューニング方法(fine_tune_checkpoint_type)<br>変更前(Before) : "classification"<br>変更後(After) : "detection"<br>
* 168行目(Line 168)：Googleカスタム 16ビットbrain浮動小数点の使用有無(use_bfloat16)<br>変更前(Before) : true<br>変更後(After) : false<br>
* 172行目(Line 172)：ラベルマップファイルの格納先(label_map_path)<br>変更前(Before) : "PATH_TO_BE_CONFIGURED/label_map.txt"<br>変更後(After) : "/content/models/research/Tensorflow2-controller/02_tfrecord/tf_label_map.pbtxt"<br>
* 174行目(Line 174)：学習データの格納先(input_path)<br>変更前(Before) : "PATH_TO_BE_CONFIGURED/train2017-?????-of-00256.tfrecord"<br>変更後(After) : "/content/models/research/train_data/??????.tfrecord"<br>
* 185行目(Line 185)：ラベルマップファイルの格納先(label_map_path)<br>変更前(Before) : "PATH_TO_BE_CONFIGURED/label_map.txt"<br>変更後(After) : "/content/models/research/Tensorflow2-controller/02_tfrecord/tf_label_map.pbtxt"<br>
* 189行目(Line 189)：バリデーションデータの格納先(input_path)<br>変更前(Before) : "PATH_TO_BE_CONFIGURED/val2017-?????-of-00032.tfrecord"<br>変更後(After) : "/content/models/research/val_data/??????.tfrecord"
</details>

# 5. Colaboratory：モデル訓練
以下の順に実行してください。
* Googleドライブに保存先ディレクトリを作成
* TensorBoard
* 学習
* saved model形式へエクスポート

# 6. Colaboratory：推論
以下の順に実行してください。
* モデルロード
* 推論
* 推論結果確認

960 * 540　にリサイズ

