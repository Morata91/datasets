# データ補正
引用：https://phi-ai.buaa.edu.cn/Gazehub/rectification/

## イントロダクション

データ補正の目的は、データ前処理手法によって環境要因を排除し、視線回帰問題を単純化することです。現在のデータ補正方法は、主に頭部のポーズや照明の要因に焦点を当てています。

私たちは[1]のプロセスに従います。キャプチャされた目の画像が3D空間内の平面であると仮定し、仮想カメラの回転を画像上の遠近変換として実行します。データ補正プロセス全体は以下の図に示されています。

まず、参照点を定義します。通常は目の中心または顔の中心です。次に、回転されたカメラが参照点を向くように仮想カメラを回転させます。この操作はカメラ位置によるバリエーションをキャンセルします。また、回転されたカメラでキャプチャされた外観が正面を向くように仮想カメラを回転させます。最後に、被写体とカメラの間の距離を同じにするために画像をスケーリングします。なお、上記の操作は画像の側面での補正のみを説明しています。視線方向も変換する必要があります。詳細は論文をご参照ください。

照明も人間の目の外観に影響を与えます。これに対処するために、研究者は通常、RGBの目の画像ではなくグレースケールの目の画像を入力として使用し、ヒストグラム均等化をグレースケール画像に適用して画像を強調します。

### スクリーンショット

このページのコードを使用する場合は、次の文献を引用してください:

@article{Cheng2021Survey,
    title={Appearance-based Gaze Estimation With Deep Learning: A Review and Benchmark},
    author={Yihua Cheng and Haofei Wang and Yiwei Bao and Feng Lu},
    journal={arXiv preprint arXiv:2104.12668},
    year={2021}
}

## チュートリアル

コード `data_processing_core.py` を[ここから](リンク)ダウンロードしてください。

また、1つの画像を正規化する例も提供します。

```python
import data_processing_core as dpc

norm = dpc.norm(center=facecenter,
                gazetarget=target,
                headrotvec=headrotvectors,
                imsize=(224, 224),
                camparams=camera)

# 正規化された顔画像を取得
im_face = norm.GetImage(im)

# 左目の画像をクロップ
llc = norm.GetNewPos(lefteye_leftcorner)
lrc = norm.GetNewPos(lefteye_rightcorner)
im_left = norm.CropEye(llc, lrc)
im_left = dpc.EqualizeHist(im_left)

# 右目の画像をクロップ
rlc = norm.GetNewPos(righteye_leftcorner)
rrc = norm.GetNewPos(righteye_rightcorner)
im_right = norm.CropEye(rlc, rrc)
im_right = dpc.EqualizeHist(im_right)

# 関連情報を取得
gaze = norm.GetGaze(scale)
head = norm.GetHeadRot(vector)
origin = norm.GetCoordinate(facecenter)
rvec, svec = norm.GetParams()
```

## ドキュメント

### クラス `norm`

`norm(center, gazetarget, headrotvec, imsize, camparams, newfocal=960, newdistance=600)`

#### パラメータ:
- `center (narray)` : CCS内の参照点の座標。形状は(3, )。
- `gazetarget (narray)` : CCS内の視線ターゲットの座標。形状は(3, )。
- `headrotvec (narray)` : CCS内の頭部ポーズの回転ベクトル。形状は(3, )。
- `imsize (narray)` : 正規化された画像のサイズ。形状は(3, )。
- `camparams (narray)` : カメラ内部パラメータ行列。形状は(3, )。
- `newfocal (int or float)` : 新しいカメラモデルの焦点距離。
- `newdistance (int or float)` : カメラと被写体の新しい距離。

### `norm.GetParams()`
正規化で計算されたパラメータを取得します。詳細なアルゴリズムについては論文を参照してください。

#### 戻り値:
- `rvec (narray)` : 正規化中のR。回転ベクトルに変換されます。形状は(3, )。
- `svec (narray)` : 正規化中のS。対角要素のみ含まれます。形状は(3, )。

### `norm.GetImage(image)`
元の画像を入力し、正規化された画像を返します。

#### パラメータ:
- `image (narray)` : 元の画像。

#### 戻り値:
- `im (narray)` : 正規化された画像。サイズはクラス `norm` 内の `imsize` と等しい。

### `norm.GetCoordinate(coordinate)`
元のCCS内の座標を入力し、正規化されたCCS内の対応する座標を出力します。

#### パラメータ:
- `coordinate (narray)` : 元のCCS内の座標。形状は(3, )。

#### 戻り値:
- `newcoordinate (narray)` : 正規化されたCCS内の座標。形状は(3, )。

### `norm.GetGaze(scale=True)`
正規化された空間内の視線方向を取得します。

#### パラメータ:
- `scale (bool)` : Trueの場合、このコードは[2]で提案された方法を使用します。Falseの場合、このコードは[3]で提案された方法を使用します。

#### 戻り値:
- `gaze (narray)` : 視線方向の単位ベクトル。

### `norm.GetHeadRot(vector=True)`
正規化された空間内の頭部回転情報を取得します。

#### パラメータ:
- `vector (bool)` : Trueの場合、頭の方向ベクトルを返します。Falseの場合、頭の回転行列を返します。

#### 戻り値:
- `head (narray)` : `vector=True`の場合、頭の方向ベクトルを返します。`vector=False`の場合、頭の回転行列を返します。

### `norm.GetNewPos(pos)`
元の画像内の2D座標を入力し、正規化された画像内の対応する2D座標を出力します。

#### パラメータ:
- `pos (narray)` : 元の画像内の2D座標。形状は(2, )。

#### 戻り値:
- `newpos (narray)` : 正規化された画像内の2D座標。形状は(2, )。

### `norm.CropEye(lcorner, rcorner)`
2つの目の角の座標を入力します。正規化された画像から目の画像をクロップします。なお、`norm.GetImage(image)`を最初に呼び出す必要があります。

#### パラメータ:
- `lcorner (narray)` : 左目の角の座標。形状は(2, )。
- `rcorner (narray)` : 右目の角の座標。形状は(2, )。

#### 戻り値:
- `image (narray)` : クロップされた目の画像。形状は(60, 36)。

### `norm.CropEyeWithCenter(center)`
目の中心の座標を入力します。正規化された画像から目の画像をクロップします。なお、`norm.GetImage(image)`を最初に呼び出す必要があります。

#### パラメータ:
- `center (narray)` : 目の中心の座標。形状は(2, )。

#### 戻り値:
- `image (narray)` : クロップされた目の画像。形状は(60, 36)。

## 参考文献
- [1] MPIIGaze: Real-World Dataset and Deep Appearance-Based Gaze Estimation
- [2] Learning-by-Synthesis for Appearance-based 3D Gaze Estimation
- [3] Revisiting Data Normalization for Appearance-Based Gaze Estimation