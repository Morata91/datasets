# MPIIFaceGazeの処理(2d)
参考：https://phi-ai.buaa.edu.cn/Gazehub/2D-dataset/
このデータセットは顔と目の画像を提供し、PoG（Point of Gaze）も提供します。

元のプロトコルに従ってデータセットを分割します。評価には15名の被験者と各被験者の3000枚の画像を使用します。顔と目の画像を切り取り、MPIIGazeで提供されたPoR（Point of Regard）を直接使用します。


## ①データセットのダウンロード

### MPIIFaceGazeのダウンロード

https://www.mpi-inf.mpg.de/departments/computer-vision-and-machine-learning/research/gaze-based-human-computer-interaction/its-written-all-over-your-face-full-face-appearance-based-gaze-estimation
から、MPIIFaceGazeをダウンロード

```bash
cd datasets/rawdata
wget http://datasets.d2.mpi-inf.mpg.de/MPIIGaze/MPIIFaceGaze.zip
unzip MPIIFaceGaze.zip
```

### MPIIGazeのダウンロード
https://www.mpi-inf.mpg.de/departments/computer-vision-and-machine-learning/research/gaze-based-human-computer-interaction/appearance-based-gaze-estimation-in-the-wild
から、MPIIGazeをダウンロード
```bash
cd datasets/rawdata
wget "http://datasets.d2.mpi-inf.mpg.de/MPIIGaze/MPIIGaze.tar.gz"
tar -zxvf MPIIGaze.tar.gz
```

## ②準備
```bash
cd codes/GazeHub/mpii_2d/
python -m venv .env
source .env/bin/activate
pip install -r requirements.txt
```

## ②処理
`data_processing_mpii.py`
コードには以下のパラメータが含まれています。

- `root = "/home/cyh/dataset/Original/MPIIFaceGaze"`
- `sample_root = "/home/cyh/dataset/Original/MPIIGaze/Origin/Evaluation Subset/sample list for eye image"`
- `out_root = "/home/cyh/dataset/GazePoint/MPIIGaze"`
`root` は MPIIFaceGaze のパスです。
`sample_root` は MPIIGaze のサンプルリストを示します。このファイルは MPIIFaceGaze には含まれていないため、MPIIGaze からダウンロードする必要があります。
`out_root` は結果ファイルを保存するパスです。


よって、
```python
root = "/home/datasets/rawdata/MPIIFaceGaze"
sample_root = "/home/datasets/rawdata/MPIIGaze/Origin/Evaluation Subset/sample list for eye image"
out_root = "/home/datasets/processed/MPII_2d"
```
と変更する。

次のように実行します：
```bash
cd datasets
python datasets/codes/GazeHub/mpii_2d/data_processing_mpii.py
```

正規化されたデータの使用ガイドも提供しています。





このオリジナルデータセットに私たちのコードを適用する場合は、以下を引用してください：

```bibtex
@inproceedings{zhang2017s,
    title={It’s written all over your face: Full-face appearance-based gaze estimation},
    author={Zhang, Xucong and Sugano, Yusuke and Fritz, Mario and Bulling, Andreas},
    booktitle={Computer Vision and Pattern Recognition Workshops (CVPRW), 2017 IEEE Conference on},
    pages={2299--2308},
    year={2017},
    organization={IEEE}
}
```