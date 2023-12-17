---
title: Whisper.cppを実行するサンプル
date: 2023-10-11
lastmod: 2023-10-11
---

## 概要

Whisper.cppを利用するサンプルです。

## 実行方法

```sh
# python環境の構築
$ python -V
Python 3.10.10
$ python -m venv .venv
$ source .venv/bin/activate
$ pip install ane_transformers
$ pip install openai-whisper
$ pip install coremltools

# whisperの取得
$ git clone https://github.com/ggerganov/whisper.cpp.git
$ cd whisper.cpp
$ git switch -c v1.4.2 tags/v1.4.2

# Core MLモデルの生成
$ make base
$ ./models/generate-coreml-model.sh base

# Core MLサポート版のビルド
$ make clean
$ WHISPER_COREML=1 make -j
I whisper.cpp build info:
I UNAME_S:  Darwin
I UNAME_P:  arm
I UNAME_M:  arm64
I CFLAGS:   -I.              -O3 -DNDEBUG -std=c11   -fPIC -pthread -DGGML_USE_ACCELERATE
I CXXFLAGS: -I. -I./examples -O3 -DNDEBUG -std=c++11 -fPIC -pthread -DWHISPER_USE_COREML
I LDFLAGS:   -framework Accelerate -framework Foundation -framework CoreML
# 省略
usage: ./main [options] file0.wav file1.wav ...

options:
  -h,        --help              [default] show this help message and exit
  -t N,      --threads N         [4      ] number of threads to use during computation

# 実行(日本語指定)
$ ./main -m models/ggml-base.bin -l ja -f hgoe.wav
```

## Tips

### coremlc not found

`./models/generate-coreml-model.sh base`のコマンド実行時にcoremlcがpathにないとエラーが発生するケースがある。
下記の記事を参考に、xcodeをapp storeからインストールして、`/Applications/Xcode.app/Contents/Developer/usr/bin`をPATHに通すように修正することで動作する。

- [GitHub whisper.cpp - issue#770](https://github.com/ggerganov/whisper.cpp/issues/770)

### mp4 to wav

下記のコマンドで変換できる。

```sh
ffmpeg -i hoge.mp4 -ar 16000 -ac 1 -c:a pcm_s16le hoge.wav
```

### 他のモデルの利用

モデルサイズを指定して一度だけモデルの変換を実施すれば良い。

```sh
# Core MLモデルの生成
$ make large
$ ./models/generate-coreml-model.sh large

# 実行(日本語指定)
$ ./main -m models/ggml-large.bin -l ja -f hgoe.wav
```
