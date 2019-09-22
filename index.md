---
layout: default
---

1. toc
{:toc}

# はじめに

このドキュメントは NanoVNA に関する非公式なユーザーガイドです。

[github レポジトリ](https://github.com/cho45/NanoVNA-manual) で管理しています。最新ファームウェアと齟齬がある場合など、修正内容がある場合は Pull-request を送ってください。

## NanoVNA とは

NanoVNA のハードウェアにはいくつか種類があり、このドキュメントが対象とするのは以下のハードウェアです

- ttrftech版 (オリジナル) [ttrftech/NanoVNA](https://github.com/ttrftech/NanoVNA)
- hugen79 版 [hugen79/NanoVNA-H](https://github.com/hugen79/NanoVNA-H)

これらはハードウェアは回路上のコンポーネントの種類はほぼ同じであり、共通のファームウェアが利用できます。

## 動作に必要なもの

最低限以下のものが必要です。

 * NanoVNA 本体
 * SMA LOAD 50Ω
 * SMA SHORT
 * SMA OPEN
 * SMA Female to Female スルーコネクタ
 * SMA Male to Male ケーブル × 2

# 最初にすること

使用するためには最初に必ず較正を行う必要があります。最初は以下の通りに較正を行います。

 * START が 50kHz であることを確認する
 * STOP が 900MHz であることを確認する
 * <a href="#%E8%BC%83%E6%AD%A3%E6%96%B9%E6%B3%95">較正方法</a>に従って較正を行う

# 画面の見方

## メイン画面

<img src="./images/nanovna-display-infor_0000_start-stop.png"/>

### 1. START 2. STOP 周波数
スタート・ストップを指定したときのそれぞれの周波数が表示されます

### 3. マーカー
トレースそれぞれのマーカーの位置が表示されます。選択されたマーカーは以下の方法で移動することができます

 * タッチパネルでマーカーをドラッグ
 * ジョグキーのLR長押し

### 4. 較正状態

読みこんでいる較正のデータ番号及び、適用されている誤差補正について表示されます。

 * `C0` `C1` `C2` `C3` `C4` それぞれ、対応する番号の較正データがロードされていることを示します。
 * `c0` `c1` `c2` `c3` `c4` それぞれ、対応する番号の較正データがロードされていることを示していますが、ロード後に周波数範囲が変更されており誤差補正に補完を使っていることを示します。
 * `D` directivity 誤差補正が適用されていることを示します
 * `R` refrection tracking 誤差補正が適用されていることを示します
 * `S` source match 誤差補正が適用されていることを示します
 * `T` transmission tracking 誤差補正が適用されていることを示します
 * `X` isolation (crosstalk) 誤差補正が適用されていることを示します

### 5. リファレンスポジション

対応するトレースのリファレンスポジションを示します。`DISPLAY` `SCALE` `REFERENCE POSITION` で位置を変更できます。

### 6. マーカー状態

選択中のアクティブなマーカーと、以前にアクティブだったマーカーが1つ表示されます。

### 7. トレース状態

各トレースフォーマットの状態と、アクティブなマーカーに対応する値が表示されます。

例えば `CH0 LOGMAG 10dB/ 0.02dB` という表示の場合、チャンネル CH0 (リフレクション) をフォーマット LOGMAT で、スケールは 10dB、現在の値が 0.02dB、となります。 

またアクティブなトレースはチャンネルの表示が反転します。

### 8. バッテリー状態

バッテリーが装着され、PCB 上の `D2` が実装済みの場合バッテリー電圧に応じてアイコンが表示されます。

---

## メイン画面2

<img src="./images/nanovna-display-infor_0001_center-span.png"/>

### 9. CENTER 周波数 10. スパン

センター周波数とスパンを指定したときのそれぞれの周波数が表示されます

---

### メニュー画面

<img src="./images/nanovna-display-infor_0002_menu.png"/>

### 11. メニュー

以下の操作でメニューを表示できます。

 * タッチパネルのマーカー以外の場所をタップした場合
 * ジョグキーの押し込み

---

## キーパッド画面

<img src="./images/nanovna-display-infor_0003_keypad.png"/>

### 12. 数字キー

数字をタップすると1文字入力されます

### 13. バックキー

1文字削除します。1文字も入力していないときは入力をキャンセルし、直前の状態に戻ります。

### 14. 単位キー

現在の入力に該当する単位を乗算して、ただちに入力を終了します。×1 の場合は入力した数値がそのまま設定されます。

### 15. 入力欄

入力対象項目名と、入力した数字が表示されます。

# 測定を開始する

## 基本的な測定シーケンス

 1. 測定周波数範囲を設定する
 2. 較正を行う
 3. DUT を接続する

# 較正方法

<img src="./images/cal.png">

較正は基本的に、測定周波数範囲を変更する度に実行する必要があります。正しくエラー修正がされている場合、画面上の較正状態表示は `Cn D R S T X` となります。n はロードしているデータ番号です。

ただし NanoVNA は既存の較正情報を補完してある程度正しい表示を行うことができます。較正データをロードした後に周波数範囲を変更した場合、この状態になります。このとき、画面上の較正状態の表示は `cn D R S T X` となります。n はロードしているデータ番号です。

 1. 現在の較正状態をリセットします `CAL` `RESET` 
 2. CH0 ポートに OPEN スタンダードを接続し、`CAL` `CALIBRATE` `OPEN` を実行します。
 3. CH0 ポートに SHORT スタンダードを接続し、`CAL` `CALIBRATE` `SHORT` を実行します。
 4. CH0 ポートに LOAD スタンダードを接続し、`CAL` `CALIBRATE` `LOAD` を実行します。
 5. CH0, CH1 ポートに LOAD スタンダードを接続し、`CAL` `CALIBRATE` `ISOLN` を実行します。CH1 ポートは未接続でもかまいません。
 6. CH0, CH1 ポートにケーブルを接続し、ケーブル同士をスルーコネクタで接続して、`CAL` `CALIBRATE` `THRU` を実行します。
 7. 較正を終了し、誤差の補正情報を計算します `CAL` `CALIBRATE` `DONE`
 8. データ番号を指定して保存します。`CAL` `CALIBRATE` `SAVE` `SAVE 0`

※
各較正データの取り込みは、十分に表示が安定してから行う必要があります。

# 機能

## トレース表示

<img src="./images/traces.png"/>

トレースは最大4つ表示でき、そのうちの1つなアクティブなトレースとなります。

トレースは必要なものだけを表示させることができます。表示を切り替えるには `DISPLAY` `TRACE` `TRACE n` を選択します。 

アクティブなトレースを切り替えるには以下の方法があります

 * アクティブにしたいトレースのマーカーをタップする
 * `DISPLAY` `TRACE` `TRACE n` を選択して表示させる。(既に表示されている場合、一時非表示にする必要があります)

### トレースフォーマット

各トレースはそれぞれにフォーマットを指定できます。アクティブなトレースのフォーマットを変更するには
`DISPLAY` `FORMAT` 変更したいフォーマットを選択します。

各フォーマットの表示は以下の通りです

 * `LOGMAG`: 測定値の絶対値の対数
 * `PHASE`: -180°から+180°の範囲での位相
 * `DELAY`: 遅延
 * `SMITH`: スミスチャート
 * `SWR`: Standing Wave Ratio
 * `POLAR`: 極座標形式
 * `LINEAR`: 測定値の絶対値
 * `REAL`: 測定値の実数
 * `IMAG`: 測定値の虚数
 * `RESISTANCE`: 測定値のインピーダンスのうち、レジスタンス成分
 * `REACTANCE`: 測定値のインピーダンスのうち、リアクタンス成分

### トレースのチャンネル

NanoVNA には `CH0` `CH1` の2つのポートがあります。それぞれのポートで以下のSパラメータが測定できます。

 * `CH0` S11 (反射損失)
 * `CH1` S21 (挿入損失)

 トレースのチャンネルを変更するには `DISPLAY` `CHANNEL` の `CH0 REFLECT` または `CH1 THROGH` を選択します。

## マーカー

<img src="./images/marker.png">
<img src="./images/marker-select.png">

マーカーは最大4つまで表示することができます。マーカーの表示は `MARKER` `SELECT MARKER` `MARKER n` から行います。マーカーを表示すると、アクティブなマーカーは表示したマーカーに設定されます。

## 時間ドメインオペレーション

NanoVNA は周波数ドメインデータを信号処理することにより、時間ドメイン測定をエミュレーションできます。

測定データを時間ドメインに変換する場合 `DISPLAY` `TRANSOFRM` `TRANSFORM ON` を選択します。`TRANSFORM ON` が有効な場合、測定データは直ちに時間ドメインに変換されて表示されます。

### 時間ドメインバンドパス

(TODO)

### 時間ドメインローパス インパルス

ローパスモードでは、TDR をシミュレートできます。ローパスモードでは、スタート周波数は 50kHz、ストップ周波数は計測したい距離に応じて設定する必要があります。

ローパスモードではインパルス応答をシミュレートできます。以下のオープン状態のステップ応答と、ショート状態のインパルス応答の例を示します。

<img src="images/impulse-open.png">
<img src="images/impulse-short.png">

### 時間ドメインローパス ステップ

ローパスモードでは、TDR をシミュレートできます。ローパスモードでは、スタート周波数は 50kHz、ストップ周波数は計測したい距離に応じて設定する必要があります。

ローパスモードではステップ応答をシミュレートできます。以下のオープン状態のステップ応答と、ショート状態のステップ応答の例を示します。

<img src="images/step-open.png">
<img src="images/step-short.png">

### 時間ドメイン ウィンドウニング

測定できる範囲は有限個数であり、最低周波数及び最大周波数が存在します。ウィンドウニングはこの不連続な測定データを滑らかにし、意図しない測定データのリンギングなどを抑えるめに使用できます。

ウィンドウニングには3段階あります

 * MINIMUM (ウィンドウなし、すなわち矩形窓と同じです)
 * NORMAL (カイザー窓のβ=6に相当します)
 * MAXIMUM (カイザー窓のβ=13に相当します)

MINIMUM では最大限分解能が高くなります、MAXIMUM では最大限綺麗な波形を表示します。NORMAL はその中間です。

### 時間ドメインでの波長短縮率の設定

距離表示の際使用される波長短縮率は `DISPLAY` `TRANSFORM` `VELOCITY FACTOR` で設定できます。例えば、67% の波長短縮率を持つケーブルの TDR を測定した場合は、`VELOCITY FACTOR` で `67` を指定します。

### マーカーから周波数を設定

以下のように、マーカーから周波数範囲を設定できます

 * `MARKER` `→START` アクティブマーカーの周波数をスタート周波数に設定します
 * `MARKER` `→STOP` アクティブマーカーの周波数をストップ周波数に設定します
 * `MARKER` `→CENTER` アクティブマーカーの周波数をセンター周波数に設定します。スパンは現在の範囲をできるだけ維持するように調整されます。
 * `MARKER` `→SPAN` アクティブマーカーを含む、表示されている2つのマーカーをスパンに設定します。マーカーが1つだけ表示されている場合は何も起きません。

## 測定範囲の設定

<img src="./images/stimulus.png">

測定範囲の設定には3つの種類があります。

 * スタート周波数・ストップ周波数を設定する
 * センター周波数・スパンを設定する
 * ゼロスパン

### スタート周波数・ストップ周波数を設定する

それぞれ、`STIMULUS` `START`、`STIMULUS` `STOP` を選択して設定します。

### センター周波数・スパンを設定する

それぞれ、`STIMULUS` `CENTER`、`STIMULUS` `SPAN` を選択して設定します。

### ゼロスパン

ゼロスパンは周波数スイープを行わず、1つの周波数を連続で送出するモードです。

`STIMULUS` `CW FREQ` を選択して設定します。

### 一時的に測定を停止する

`STIMULUS` `PAUSE SWEEP` を選択すると、一時的に測定を停止します。


## 較正と設定の呼び出し

<img src="./images/save.png">

較正データは最大5つ保存可能です。NanoVNA は起動直後、番号0のデータをロードします。

較正データとは、以下の情報を含むデータです。

 * 周波数設定範囲
 * 各測定点における誤差補正
 * トレースの設定状態
 * マーカーの設定状態
 * ドメインモード設定
 * 波長短縮率設定
 * electrical delay

`CAL` `SAVE` `SAVE n` を選択することで、現在の設定を保存することができます。

`CAL` `RESET` を選択することで、現在の較正データをリセットすることができます。較正を再度行う場合は `RESET` を行う必要があります。

`CAL` `CORRECTION` は現在エラー修正が行われているかを示します。これを選択して一時的にエラー修正を止めることができます。

<img src="./images/recall.png">

`RECALL` `RECALL n` を選択することで、保存した設定を呼びだすことができます。

## 機器の設定

<img src="./images/config.png">

`CONFIG` 以下では機器の全般的な設定などを行うことができます。

### タッチパネルの較正とテスト

<img src="./images/touchcal.png">
<img src="./images/touchcal2.png">

`CONFIG` `TOUCH CAL` を選択すると、タッチパネルの較正を行うことができます。実際のタップ位置と、認識されるタップ位置に大きな差がある場合には、これを実行することで解決できます。`TOUCH CAL` を行ったあと、`TOUCH TEST` を行って正しく設定されていることを確認し、`SAVE` で設定を保存します。

<img src="./images/touchtest.png">

`CONFIG` `TOUCH TEST` を選択すると、タッチパネルのテストを行うことができます。タッチパネルをタップしている間は線がひかれます。タッチパネルから離すと元の状態に戻ります。

### 機器の設定の保存

`CONFIG` `SAVE` を選択すると機器の全般的な設定を保存できます。機器の全般的な設定とは、以下の情報を含むデータです。

 * タッチパネルの較正情報
 * グリッドカラー
 * トレースカラー
 * デフォルトでロードされる較正データ番号

 タッチパネルの較正情報以外は、現在設定する方法がありません。

### バージョンを表示する

<img src="./images/version.png">

`CONFIG` `VERSION` を選択すると、機器のバージョン情報を表示することができます。

### ファームウェアアップデート

<img src="./images/dfu.png">

`CONFIG` `→DFU` `RESET AND ENTER DFU` を選択すると、機器をリセットし、DFU (Device Firmware Update) モードに入ります。このモードでは USB 経由でファームウェアアップデートが可能です。

# ファームウェアの更新方法

## ファームウェアの入手方法

### ttrftech 版ファームウェア

オリジナルのファームウェアです。バージョン管理されており、頻繁に開発されています。

 * [GitHub releases](https://github.com/ttrftech/NanoVNA/releases)
 * [CircleCI build](https://cho45.stfuawsc.com/NanoVNA/dfu.html)

 GitHub releases にはある程度安定したリリース版のファームウェアがあります。

 CircleCI にはコミットごと全てのファームウェアがあります。最新の機能を試したい場合や、不具合を確認する場合はこちらを使用します。

### hugen79 版ファームウェア

 * [Google Drive](https://drive.google.com/drive/folders/1IRz6E1wlkRyV0u7sbqj0lhWST-GV1szY)
 
Google Drive に最新のファームウェアが配置されています。

### 自分でビルドする

github のレポジトリを clone して自分でビルドすることも簡単にできます。


## ファームウェアの書きこみかた

書きこみかたはいろいろな方法がありますが、ここでは [dfu-util](http://dfu-util.sourceforge.net/) を用いて説明します。
dfu-util はクロスプラットフォームツールであり、Windows ではバイナリも提供されていますので、ダウンロードするだけで使用することができます。

### dfu-util 起動まで (Windows 10)

releases から [dfu-util-0.9-win64.zip](http://dfu-util.sourceforge.net/releases/dfu-util-0.9-win64.zip) をダウンロードして展開します。

ファームウェアがあるフォルダでコマンドプロンプトを開き、dfu-util コマンドが使用可能であることを確認します。

```
dfu-util --version
```

### dfu-util 起動まで (macOS)

[homebrew](https://brew.sh/index_ja) を使ってインストールするのがお勧めです。

brew コマンドのインストール

```
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

dfu-util コマンドのインストール

```
brew install dfu-util
```

```
dfu-util --version
```

### dfu-util 起動まで (Ubuntu)

標準パッケージレポジトリに dfu-util があります。

```
sudo apt-get install dfu-util
dfu-util --version
```

### dfu-util を使った書きこみ

まずデバイスをDFUモードで起動します。以下のいずれかの方法でDFUモードになります。

 * PCB上のBOOT0ピンをジャンパーしながら電源を入れる。(電源を入れたあとはジャンパーを外します) 画面が真っ白になりますが正常です。
 * `CONFIG` `→DFU` `RESET AND ENTER DFU` を選択する


以下のコマンドを実行します。build/ch.bin はダウンロードしたファームウェアファイルの .bin までのパスを記述します。

```
dfu-util -d 0483:df11 -a 0 -s 0x08000000:leave -D build/ch.bin
```

## ファームウェアの書きこみかた (Windows GUI)

CUI になじみのないかた向けに、若干面倒な手順が必要ですが ST が提供する DfuSE Demo ツールを使った書きこみ方法も参考程度に紹介します。

[ST のサイトから STSW-STM32080](https://www.st.com/ja/development-tools/stsw-stm32080.html) をダウンロードします。

 * DFU File Manager: .bin または .hex から .dfu ファイルを作成するツール
 * DfuSe Demo: .dfu ファイルをデバイスに書きこむツール

が含まれています。

### DFU File Manager でファイル形式を変換する。

まず DFU File Manager を起動します。

<img src="images/dfu-file-manager-open.png">

`I want to GENERATE a DFU file from S19, HEX or BIN files` を選択します。

<img src="images/dfu-file-manager-from-hex.png">

`S19 or Hex...` ボタンをクリックします。`ch.hex` などファームウェアの .hex ファイルを選択します。

<img src="images/dfu-file-manager-generate.png">

`Generate...` ボタンをクリックして、適当な名前をつけて .dfu ファイルを作成します。

### DfuSe Demo でファームウェアを書きこみ

まずデバイスをDFUモードで起動します。以下のいずれかの方法でDFUモードになります。

 * PCB上のBOOT0ピンをジャンパーしながら電源を入れる。(電源を入れたあとはジャンパーを外します) 画面が真っ白になりますが正常です。
 * `CONFIG` `→DFU` `RESET AND ENTER DFU` を選択する

DfuSe Demo を起動します。Available DFU Devices に `STM Device in DFU Mode` があることを確認して、`Choose...` をクリックします。

<img src="images/dfuse-demo-choose.png">

先程保存した .dfu ファイルを選択します。

<img src="images/dfuse-upgrade.png">

`Upgrade` ボタンをクリックします。

<img src="images/dfuse-leave-dfu-mode.png">

書きこみが終わるとこの画面になるので、`Leave DFU mode` ボタンをクリックして DFU モードを抜けます。デバイスがリセットされて新しいファームウェアで起動します。

# ファームウェア開発の手引き

NanoVNA のファームウェアの開発の必要なものは以下の通りです

 * gcc-arm-none-eabi
 * make

既にこれらが手元に環境にあるなら、`make` するだけでファームウェアのビルドが可能です。

## Docker を使ったビルド

docker を使うとわずらわしいことなしにビルドすることができます。docker は無料で利用できるクロスプラットフォームのコンテナユーティリティです。特定の環境を素早く再現するために利用できます。

[docker](https://hub.docker.com/) をインストールした上で、以下のコマンドを実行するだけです。

```
docker run -it --rm -v $(PWD):/work edy555/arm-embedded:8.2 make
```

TODO

# 使用例

## バンドパスフィルタの調整
TODO

## アンテナの調整

NanoVNA をアンテナアナライザーとして利用する例を示します。

アンテナの調整において重要なのは以下の2点です

 * アンテナが同調・共振状態にあるか (すなわちリアクタンスが目的とする周波数で0に近いか)
 * アンテナのSWRが低いか (十分にマッチングがとれているか)

### トレースの設定

アンテナの調整では CH0 のみを使用しますので、`THRU` と `ISOLN` 以外の全ての項目について、較正を実施します。

トレース設定は以下のようにします。

 * トレース0: CH0 SWR
 * トレース1: CH0 REACTANCE
 * トレース2: CH0 SMITH
 * トレース3: OFF

アンテナの同調させたい周波数を `CENTER` に設定し、`SPAN` を適切に設定します。

リアクタンスを表示しているトレース1が0に近い周波数を探します。その周波数が同調点ですので、ずれていればアンテナを調整し、目的の周波数に同調点がくるようにします。

同調点が目的の周波数にあったら、SWR を表示しているトレース0が十分に低い (1に近い) SWR を表示しているかを確認します。もし十分に (2以下の) SWR を示していない場合、スミスチャートを使ってマッチングを行います。
この際、マッチングはアンテナ直下のアンテナチューナーなどを使ってもかまいません。

これによって SWR が落ちれば、目的の同調周波数で同調したうえで、SWR が低いアンテナの調整は終わりです。

## ケーブルの確認

時間ドメインのローパスモードを使うことで TDR をシミュレートできます。TDR を使うことで、伝送路の不具合を発見することができます。

TODO

## コモンモードフィルタの測定

TODO