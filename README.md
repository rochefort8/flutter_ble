# MultipleSensorLogger ユーザーズマニュアル

- 1. MultipleSensorLoggerアプリケーション概要
  - 1-1. アプリ概要
  - 1-2. 機能一覧
- 2. Spresense Cube使用環境準備
  - 2-1. 必要機材
  - 2-2. Windows PC に必要なソフトウェア
  - 2-3. Windows PC に用意が必要なファイル
  - 2-4. 機材準備
  - 2-5. MultipleSensorLogger アプリケーションの書き込み
- 3. MultipleSensorLogger使用環境準備
  - 3-1. Spresense CubeのMultipleSensorLoggerアプリケーション用準備
    - 3-1-1. 設定ファイル記述
      - 全センサで共通のパラメータ
      - センサーごとに設定するパラメータ
    - 3-1-2. 設定ファイルパラメータの設定範囲
    - 3-1-3. 設定ファイルの記述例
    - 3-1-4. 設定ファイル記述のエラー
    - 3-1-5. 設定ファイル記述のワーニング
- 4. センサー値ロギング機能
- 5. ログファイル定期切り替え機能
- 6. UTCタイムゾーン時差設定機能
- 7. 自動センサーロギング開始機能
- 8. 起動状況LED通知機能
- 9. USB MSCモード起動機能
- 10. コマンドコンソール機能
  - 10-1. sensor start
  - 10-2. sensor stop
  - 10-3. show start time
  - 10-4. show elapsed time
  - 10-5. show config
  - 10-6. show info
  - 10-7. fw update
  - 10-8. config update
  - 10-9. config file update
  - 10-10. show log list
  - 10-11. date
  - 10-12. help

# 1. MultipleSensorLoggerアプリケーション概要

## 1-1. アプリ概要

本アプリケーションは、Spresense Cube で利用可能なセンサの出力値（測定値）を、CSV ファイルに記録するアプリケーションです。測定パラメータ（センサの種類やサンプリングレート等）は、JSON 形式の設定ファイルによりカスタマイズすることができます。

## 1-2. 機能一覧

本アプリケーションは以下の機能を持ちます。

- センサー値ロギング機能
- ログファイル定期切り替え機能
- UTCタイムゾーン時差設定機能
- 自動センサーロギング開始機能
- 起動状況LED通知機能
- コマンドコンソール機能
- USB MSCモード起動機能

# 2. Spresense Cube使用環境準備

## 2-1. 必要機材

- Spresense Cube（初期設定や記録されたログを取り出すために、別途デバッグボードが必要です）
- Windows PC（コンソール接続、USB接続によるファイル転送に使用します）
- USBケーブル Micro B（2 本）

## 2-2. Windows PC に必要なソフトウェア

- シリアルポートを利用可能で、XMODEM 形式でファイル転送機能を有するターミナルエミュレータ（Tera Term等）
- テキストエディタ
- Spresense Cube デバッグボード用 USB ドライバ（mbedWinSerial\_16466.exe）

## 2-3. Windows PC に用意が必要なファイル

- Spresense Cube に書き込む MultipleSensorLogger ファームウェア（アプリケーションパッケージ, 拡張子：.spk）

## 2-4. 機材準備

以下の組み合わせで接続し、Spresense Cubeの電源スイッチをONに切り替えます。

- Spresense Cube 本体の microUSB ポート と Windows PC の USB ポート
- デバッグボードのmicroUSBポート と Windows PCのUSBポート

ターミナルソフト (Tera Term 等）のシリアルポート設定において、以下の設定をします。

- ボーレート　　：115200
- データサイズ　：8 bit
- パリティ　　　：無し
- ストップビット：1 bit
- フロー制御　　：無し

## 2-5. MultipleSensorLogger アプリケーションの書き込み

以下の手順で MultipleSensorLogger アプリケーションを Spresense Cube へ転送します。

1. WindowsPCからSpresense Cubeに接続しているコンソールで[r]キーを入力しながら、以下の a, b のいずれかの操作を実施して updater を起動します。
  1. デバッグボードのリセットボタン押下
  2. Spresense Cubeの電源スイッチをOFF→ON
2. Updaterで以下コマンドを入力します。

updater\> install

1. ターミナルソフトでXMODEM送信を選択し、Spresense Cube に書き込む Firmwareファイル（拡張子：.spk）を選択します。
※ターミナルソフトにTera Termを使用している場合、「ファイル」→「転送」→「XMODEM」→「送信」を選択し、「Tera Term : XMODEM 送信」ダイアログが表示されたら、対象のFirmwareファイル（拡張子：.spk）を選択します。

# 3. MultipleSensorLogger使用環境準備

## 3-1. Spresense CubeのMultipleSensorLoggerアプリケーション用準備

MultipleSensorLogger を動作させるためには、設定ファイルとログファイルを格納するディレクトリが必要です。

- 設定ファイル
/mnt/emmc/SensorConfig.json （※変更するためには、MultipleSensorLogger のソースコードを修正する必要があります）
- ログファイル格納先ディレクトリ
設定ファイル内に "LogFilePath" パラメータとして設定します。
ここで指定した格納先ディレクトリは、MultipleSensorLogger を使用する前にあらかじめ作成しておく必要があります。

### 3-1-1. 設定ファイル記述

各センサーを動作させるパラメータ設定を設定ファイル「SensorConfig.json」に記述します。設定ファイルに、測定したいセンサの一覧と、センサごとの測定パラメータを記述します。設定ファイルで設定する内容は、全センサで共通のパラメータと、センサごとに設定されるパラメータの 2 種類があります。

#### 全センサで共通のパラメータ

| **パラメータ名** | **概要** |
| --- | --- |
| LogRotationIntervalMin | ログファイル定期切り替え機能の切り替え時間（分単位）を記載します。 |
| TimezoneOffset | UTCタイムゾーン時差設定機能の時差（時間単位）を記載します。 |
|  AutoStart | 自動センサーロギング開始機能の設定を記載します。 |
|  LogFilePath | ログを書き込むファイルの格納先ディレクトリパスの設定を記載します。 |

####
センサーごとに設定するパラメータ

| **パラメータ名** | **概要** |
| --- | --- |
| Name | パラメータを反映するセンサの種類（例：加速度センサーであれば accel0 とします） |
| Frequency | センサーの測定値を記録する間隔（サンプリング周波数） |
| Watermark | センサーから 1 度に取得する測定値の数 |
| LogFileName | ログを書き込むファイル名の prefix※実際のファイル名は、ここで設定した文字列に、システム時刻を付加したものとなります。　　例：LogFileNameに"Temporary"と設定され、システム時刻が2019/08/01 12:00:00の場合、　　　　ファイル名は以下となります。　　　　　　Temporary-20190801\_120000.csv |
| OperationMode | （GNSS専用）GNSSのオペレーションモードを指定します。 |
| Cycle　 | （GNSS専用）GNSSのセンサー値取得周期を指定します。 |
| SatelliteSystems | （GNSS専用）GNSSの使用する衛星システムを指定します。 |
| Codec | （AcaPulco mic専用） 記録する音声データの音声フォーマットを指定します。 |
| SamplingRate | （SCU mic, AcaPulco mic専用）記録する音声データのサンプリング周波数を指定します。 |
| Channel | （AcaPulco mic専用）記録する音声データのチャンネル数を指定します。 |
| Format | （AcaPulco mic専用）記録する音声データのファイルフォーマットを指定します。 |
| Bitwidth | （AcaPulco mic専用）記録する音声データの1サンプルに必要なビット数を指定します。 |

### 3-1-2. 設定ファイルパラメータの設定範囲

各センサーのパラメータ毎の設定範囲はセンサーによって異なり、以下の表に示します。

※ハードウェアに構成されていないセンサー使用することができません。

**GNSS、mic以外のパラメータ設定範囲**

| **センサー** | **Name** | **Frequency** | **Watermark** | **LogFileName** |
| --- | --- | --- | --- | --- |
| 加速度センサー | accel0 | 1~128 | 1~128 | 半角44文字まで ※1 |
| ジャイロセンサー | gyro0 | 1~128 | 1~128 | 半角44文字まで ※1 |
| 地磁気センサー | mag0 | 1~64 | 1~64 | 半角44文字まで ※1 |
| 気圧センサー | press0 | 1~128 | 1~1024 | 半角44文字まで ※1 |
| 気温センサー | temp0 | 1~128 ※2 | 1-1024 ※2 | 半角44文字まで ※1 |

- ※1　\ / : \* ? " \&lt; \> |などの特殊文字等は使用しないでください。
- ※2　気圧センサーと気温センサーを併用する場合、気圧センサーのWatermark/Frequencyの値が気温センサーのWatermark/Frequencyより著しく小さい場合、気圧センサーの計測値が計測開始よりしばらく不正確な値となることがあります。

**GNSSのパラメータ設定範囲**

| **センサー** | **Name** | **LogFileName** | **OperationMode** | **Cycle** | ** SatelliteSystems** |
| --- | --- | --- | --- | --- | --- |
| GNSS 　 |  gnss | 半角44文字まで ※1 | "Normal" or "NoChange"  | 1 ~ 4294967  | 以下の衛星システムを配列で記述します。 ※2 "GPS" "GLONASS" "SBAS" "QZSS\_L1CA" "IMES" "QZSS\_L1SAIF" "BeiDou" "Galileo"   |

- ※1　\ / : \* ? " \&lt; \> |などの特殊文字等は使用しないでください。
- ※2　大文字小文字は区別されません。

**micのパラメータ設定範囲**

| ** センサー** | **Name** | **LogFileName** | **Codec** | **SamplingRate** | **Channel ** | **Format** | **Bitwidth** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| SCU mic | scumic |  半角44文字まで ※1 | ※2 | 16000 | ※2 | ※2 | ※2 |
| AcaPulco mic | acapulcomic |  半角44文字まで ※1 | "lpcm"  | 16000 or48000 or192000  | 1(MONO)2(STEREO) | "wav" | 16 or 24※3 |

- ※1　\ / : \* ? " \&lt; \> |などの特殊文字等は使用しないでください。
- ※2　SCU micはCodec="lpcm", Channel=1, Format="wav", Bitwidth=16固定で動作するため、Codec, Channel, Format, Bitwidthは記述しないでください。
　　  Codec, Channel, Format, Bitwidthパラメータを指定していても記述されたパラメータは無視されます。
- ※3　Bitwidth=24と指定する場合、SamplingRate=16000の使用はできません。

センサー共通のパラメータの設定範囲は以下の表に示します。

| **設定項目** | **設定可能範囲** |
| --- | --- |
| LogRotationIntervalMin | 1 ~ 2147483647 |
| TimezoneOffset | -12 ~ 14 |
| AutoStart | true or false |
| LogFilePath | 半角256文字まで ※1 |

- ※1　\ / : \* ? " \&lt; \> |などの特殊文字等は使用しないでください。

### 3-1-3. 設定ファイルの記述例

例1： 加速度センサーを以下の設定で動作させる際の設定ファイルの例を示します。

1. 加速度センサー
  - サンプリング周波数　　　　　　　　　　　：128
  - ログ書き込みをする数　　　　　　　　　　：128
  - ログファイル名　　　　　　　　　　　　　：Accel-YYYYMMDD\_hhmmss.csv ( YYYY, MM, DD, hh, mm, ssはシステム時刻によって決定します）
2. 共通
  - ログファイル定期切り替え機能切り替え時間：30 分
  - 自動センサーロギング開始　　　　　　　　：有効
  - ログファイル格納パス　　　　　　　　　　：/mnt/emmc/log

{

     "Sensor":   [{

             "Name": "accel0",

             "Frequency":    128,

             "Watermark":    128,

             "LogFileName":  "Accel"

         }],

     "LogRotationIntervalMin":  30,

     "AutoStart": true,

     "LogFilePath":  "/mnt/emmc/log"

}

例2：加速度センサー、ジャイロセンサー、気圧センサー、気温センサー、GNSS、SCU micを以下の設定で動作させる際の設定ファイルの例を示します。

1. 加速度センサー
  - サンプリング周波数　　　　　　　　　　　：128
  - ログ書き込みをする数　　　　　　　　　　：128
  - ログファイル名　　　　　　　　　　　　　：Accel-YYYYMMDD\_hhmmss.csv ( YYYY, MM, DD, hh, mm, ssはシステム時刻によって決定します）
2. ジャイロセンサー
  - サンプリング周波数　　　　　　　　　　　：128
  - ログ書き込みをする数　　　　　　　　　　：128
  - ログファイル名　　　　　　　　　　　　　：Gyro-YYYYMMDD\_hhmmss.csv ( YYYY, MM, DD, hh, mm, ssはシステム時刻によって決定します）
3. 気圧センサー
  - サンプリング周波数　　　　　　　　　　　：128
  - ログ書き込みをする数　　　　　　　　　　：128
  - ログファイル名　　　　　　　　　　　　　：Press-YYYYMMDD\_hhmmss.csv ( YYYY, MM, DD, hh, mm, ssはシステム時刻によって決定します）
4. 気温センサー
  - サンプリング周波数　　　　　　　　　　　：128
  - ログ書き込みをする数　　　　　　　　　　：128
  - ログファイル名　　　　　　　　　　　　　：Temp-YYYYMMDD\_hhmmss.csv ( YYYY, MM, DD, hh, mm, ssはシステム時刻によって決定します）
5. GNSS
  - ログファイル名　　　　　　　　　　　　　：Gnss-YYYYMMDD\_hhmmss.csv ( YYYY, MM, DD, hh, mm, ssはシステム時刻によって決定します）
  - オペレーションモード　　　　　　　　　　：ノーマル
  - Cycle　　　　　　　　　　　　　　　　　  ：1 秒
  - 衛星システム　　　　　　　　　　　　　　：GPS、GLONASS、IMES
6. 共通
  - ログファイル定期切り替え機能切り替え時間：120 分
  - UTCタイムゾーン時差設定　　　　　　　　：9
  - 自動センサーロギング開始　　　　　　　　：有効
  - ログファイル格納パス　　　　　　　　　　：/mnt/emmc/log

{

     "Sensor":   [{

             "Name": "accel0",

             "Frequency":    128,

             "Watermark":    128,

             "LogFileName":  "Accel"

         }, {

             "Name": "gyro0",

             "Frequency":    128,

             "Watermark":    128,

             "LogFileName":  "Gyro"

         }, {

             "Name": "press0",

             "Frequency":    128,

             "Watermark":    128,

             "LogFileName":  "Press"

         }, {

             "Name": "temp0",

             "Frequency":    128,

             "Watermark":    128,

             "LogFileName":  "Temp"

         }, {

             "Name": "gnss",

             "LogFileName":  "Gnss",

             "OperationMode":    "Normal",

             "Cycle":    1,

             "SatelliteSystems": [

                                       "GPS",

                                       "GLONASS",

                                       "IMES"

                                 ]

         }],

    "LogRotationIntervalMin":  120,

    "TImezoneOffset": 9,

    "AutoStart": true,

    "LogFilePath":  "/mnt/emmc/log"

}

例3：加速度センサー、地磁気センサー、気圧センサー、SCU micを以下の設定で動作させる際の設定ファイルの例を示します。

1. 加速度センサー
  - サンプリング周波数　　　　　　　　　　　：128
  - ログ書き込みをする数　　　　　　　　　　：128
  - ログファイル名　　　　　　　　　　　　　：Accel-YYYYMMDD\_hhmmss.csv ( YYYY, MM, DD, hh, mm, ssはシステム時刻によって決定します）
2. 地磁気センサー
  - サンプリング周波数　　　　　　　　　　　：64
  - ログ書き込みをする数　　　　　　　　　　：64
  - ログファイル名　　　　　　　　　　　　　：Mag-YYYYMMDD\_hhmmss.csv ( YYYY, MM, DD, hh, mm, ssはシステム時刻によって決定します）
3. 気圧センサー
  - サンプリング周波数　　　　　　　　　　　：128
  - ログ書き込みをする数　　　　　　　　　　：128
  - ログファイル名　　　　　　　　　　　　　：Press-YYYYMMDD\_hhmmss.csv ( YYYY, MM, DD, hh, mm, ssはシステム時刻によって決定します）
4. SCU mic
  - ログファイル名　　　　　　　　　　　　　：Mic-YYYYMMDD\_hhmmss.csv ( YYYY, MM, DD, hh, mm, ssはシステム時刻によって決定します）
  - サンプリング周波数　　　　　　　　　　　：16kHz
5. 共通
  - ログファイル定期切り替え機能切り替え時間：60 分
  - UTCタイムゾーン時差設定　　　　　　　　：9
  - 自動センサーロギング開始　　　　　　　　：無効
  - ログファイル格納パス　　　　　　　　　　：/mnt/emmc/log

{

     "Sensor":   [{

             "Name": "accel0",

             "Frequency":    128,

             "Watermark":    128,

             "LogFileName":  "Accel"

         }, {

             "Name": "mag0",

             "Frequency":    64,

             "Watermark":    64,

             "LogFileName":  "Mag"

         }, {

             "Name": "press0",

             "Frequency":    128,

             "Watermark":    128,

             "LogFileName":  "Press"

         }, {

             "Name": "scumic",

             "LogFileName":  "Mic",

             "SamplingRate":    16000

         }],

    "LogRotationIntervalMin":  60,

    "TImezoneOffset": 9,

    "AutoStart": false,

    "LogFilePath":  "/mnt/emmc/log"

}

例4：加速度センサー、ジャイロセンサー、気圧センサー、AcaPulco micを以下の設定で動作させる際の設定ファイルの例を示します。

1. 加速度センサー
  - サンプリング周波数　　　　　　　　　　　：128
  - ログ書き込みをする数　　　　　　　　　　：128
  - ログファイル名　　　　　　　　　　　　　：Accel-YYYYMMDD\_hhmmss.csv ( YYYY, MM, DD, hh, mm, ssはシステム時刻によって決定します）
2. ジャイロセンサー
  - サンプリング周波数　　　　　　　　　　　：128
  - ログ書き込みをする数　　　　　　　　　　：128
  - ログファイル名　　　　　　　　　　　　　：Gyro-YYYYMMDD\_hhmmss.csv ( YYYY, MM, DD, hh, mm, ssはシステム時刻によって決定します）
3. 気圧センサー
  - サンプリング周波数　　　　　　　　　　　：128
  - ログ書き込みをする数　　　　　　　　　　：128
  - ログファイル名　　　　　　　　　　　　　：Press-YYYYMMDD\_hhmmss.csv ( YYYY, MM, DD, hh, mm, ssはシステム時刻によって決定します）
4. AcaPulco mic
  - ログファイル名　　　　　　　　　　　　　：Mic-YYYYMMDD\_hhmmss.csv ( YYYY, MM, DD, hh, mm, ssはシステム時刻によって決定します）
  - コーデック　　　　　　　　　　　　　　　：LPCM
  - サンプリング周波数　　　　　　　　　　　：48kHz
  - チャンネル数　　　　　　　　　　　　　　：2（STEREO)
  - ファイルフォーマット　　　　　　　　　　：WAV形式
  - 1サンプルに必要なビット数 　　　　　　　：16
5. 共通
  - ログファイル定期切り替え機能切り替え時間：60 分
  - UTCタイムゾーン時差設定　　　　　　　　：9
  - 自動センサーロギング開始　　　　　　　　：無効
  - ログファイル格納パス　　　　　　　　　　：/mnt/sd0/log

{

     "Sensor":   [{

             "Name": "accel0",

             "Frequency":    128,

             "Watermark":    128,

             "LogFileName":  "Accel"

         }, {

             "Name": "gyro0",

             "Frequency":    128,

             "Watermark":    128,

             "LogFileName":  "Gyro"

         }, {

             "Name": "press0",

             "Frequency":    128,

             "Watermark":    128,

             "LogFileName":  "Press"

         }, {

             "Name": "acapulcomic",

             "LogFileName":  "Mic",

             "Codec":  "lpcm",

             "SamplingRate":    48000

             "Channel":    2

             "Format":    "wav"

             "Bitwidth":    16

         }],

    "LogRotationIntervalMin":  60,

    "TImezoneOffset": 9,

    "AutoStart": false,

    "LogFilePath":  "/mnt/sd0/log"

}

### 3-1-4. 設定ファイル記述のエラー

以下のエラー以外は設定範囲外であっても検出できず、動作の保証はできません。

- 文法エラー

 　　　設定ファイルのJSON形式に問題がある場合、以下のエラーコードが表示されます。
 　　　     　「"Error : Parse JSON file(SensorConfig.json) failed.」

- パラメータエラー

 　　   設定ファイルにおけるパラメータエラーはLogFileNameの文字数制限オーバー（65文字以上）、
   　　 及びLogFilePathの文字数制限オーバー（256文字以上）のみ検出します。

    　　　LogFileNameが文字数制限オーバーの場合、以下のエラーコードが表示されます。
      　　　　 「Error : File name ("\&lt;LogFileName\>") size over (\&lt;LogFileNameStringSize\>/65)」

    　　　LogFilePathが文字数制限オーバーの場合、以下のエラーコードが表示されます。
       　　　　「Error : Path name ("\&lt;LogFilePath\>") size over (\&lt;LogFilePathStringSize\>/256)」

### 3-1-5. 設定ファイル記述のワーニング

以下の設定は、設定可能範囲外、または設定が記述されていない場合、

ワーニング文を出力してデフォルト値で動作します。

|   | **LogRotationIntervalMin** | **TimezoneOffset** |
| --- | --- | --- |
| デフォルト値 | 1440（1日） | 0 |

# 4. センサー値ロギング機能

センサーの計測を設定ファイルに記述された設定で実施し、周期的にログファイルに書き込む機能です。

センサー計測値の書き込み先ログファイルは、設定ファイルのLogFilePathで指定したディレクトリに
LogFileNameとシステム時刻を付加したファイル名にてmicはwav形式、それ以外はcsv形式で生成します。

以下のLogFileNameとシステム時刻の例では、ログファイル名は "Temporary-20190801\_120000.csv" となります。

- LogFileName　　　　 ：Temporary
- ログファイル生成時刻 ：2019 / 8 / 1 12:00:00

**センサーの計測周期**

　センサーの計測周期は設定ファイルで指定したパラメータを使用して以下で計算します

- GNSS　　　　　  ：Cycle 秒
- mic　　　　　　   ：1 / SamplingRate 秒
- 上記以外 　　　    ：1 / Frequency 秒

**センサーの計測値単位**

　センサーの計測値はそれぞれ物理単位に補正してロギングします。

| **センサー名** | **Name** | **計測値単位** | ** 計測値単位概略** |
| --- | --- | --- | --- |
| 加速度センサー | accel0 | g |  秒間辺りの加速度。1gは重力加速度と同等（9.8 [m/s^2]） |
| ジャイロセンサー | gyro0 | dps |  秒間辺りの回転角（degree / sec） |
| 地磁気センサー | mag0 | μT  |  磁束密度（マイクロテスラ） |
| 気圧センサー | press0 | Pa |  気圧（パスカル） |
| 気温センサー | temp0 | ℃ |  温度（セルシウス度） |
| GNSS | gnss | HH:MM:SS.SSS | 時刻（HH=時, MM=分, SS.SSS=秒）※1 |
| DD.MM.MMMM | 緯度・経度（DD=度, MM.MMMM=分）※2 |

- 出力値は小数第3位以下切り捨て
- ※1　出力値は小数第4位以下切り捨て
- ※2　出力値は小数第5位以下切り捨て

**出力フォーマット**

　csv形式のログファイルは以下のフォーマットで出力します。

| **センサー名** | **Name** | **出力フォーマット** |
| --- | --- | --- |
| 1カラム目 | 2カラム目 | 3カラム目 |
| 加速度センサー | accel0 | X軸方向秒間辺りの加速度 | Y軸方向秒間辺りの加速度 | Z軸方向秒間辺りの加速度 |
| ジャイロセンサー | gyro0 | X軸方向秒間辺りの回転角 | Y軸方向秒間辺りの回転角 | Z軸方向秒間辺りの回転角 |
| 地磁気センサー | mag0 | X軸方向磁束密度 | Y軸方向磁束密度 | Z軸方向磁束密度 |
| 気圧センサー | press0 | 気圧 | - | - |
| 気温センサー | temp0 | 温度 | - | - |
| GNSS | gnss | 時刻 | 緯度 | 経度 |



# 5. ログファイル定期切り替え機能

センサー値ロギング機能において生成するログファイルを一定時間毎に切り替える機能です。

設定ファイル内の以下パラメータによって指定された時間（分）がログファイルの生成から経過すると、
ログファイルを切り替えます。

- LogRotationIntervalMin

本機能は、センサー値ロギングが開始している時のみ動作します。

# 6. UTCタイムゾーン時差設定機能

GNSSによるシステム時刻設定の際に加算する時差を設定する機能です。

設定ファイルの以下パラメータによって指定された時間（時間）がGNSSにより取得されたUTC時刻に加算されます。

- TimezoneOffset

本機能は、GNSSをセンサー値ロギング対象としたときのみ動作します。

# 7. 自動センサーロギング開始機能

電源投入時、リセット時に自動でセンサー値ロギングを開始する機能です。

設定ファイルの以下パラメータが true である場合、電源投入時、リセット時に自動でセンサー値ロギングを開始します。

- AutoStart

# 8. 起動状況LED通知機能

センサー値ロギングを開始した際に状況をLEDの点灯色で通知する機能です。

LEDの点灯色は以下の状況と紐づきます。

- 青色LED点灯：センサー値ロギング開始
- 緑色LED点灯：GNSS電波受信による時刻設定
- 赤色LED点灯：エラー発生
- LED消灯　　：センサー値ロギング停止

# 9. USB MSCモード起動機能

Spresense Cube本体とmicroUSB接続により、Spresense Cubeを

USBストレージとしてアクセスできるようにする機能です。

Spresense Cubeのカメラ横ボタンを押しながら電源投入、またはリセットすることによって、
USB MSCモードで起動します。

USBストレージとしてアクセスできるディレクトリは"/mnt/emmc"配下です。

USB MSCモードで起動すると、3 秒間隔で赤色LEDが 2 回点滅します。

# 10. コマンドコンソール機能

MultipleSensorLoggerをコマンドで制御するコンソール機能です。

コマンド部、スペースを含めて 99 文字以内で実行してください。※ 100 文字になると自動で改行、実行されます。

MultipleSensorLoggerは以下のコマンドを実装しています。

## 10-1. sensor start

本コマンドはセンサー値ロギングを開始します。

コマンドプロンプトで以下のように実行してください。

\> sensor start

正常に終了したときのみ、以下のログが出力されます。

Sensing start...

異常終了した時はエラーログが出力されます。

## 10-2. sensor stop

本コマンドは動作中のセンサー値ロギングを停止します。

コマンドプロンプトで以下のように実行してください。

\> sensor stop

異常終了した時はエラーログが出力されます。

## 10-3. show start time

本コマンドはセンサーの計測を開始した時刻を以下のように表示します。

コマンドプロンプトで以下のように実行してください。

\> show start time

センサーの計測を開始した時刻は以下のように表示します。

Sensing Start Time : "[Month] [Day] [Hours]:[Minutes]:[Seconds] [Year]"

## 10-4. show elapsed time

本コマンドはセンサーの計測開始からの経過時間を以下のように表示します。

コマンドプロンプトで以下のように実行してください。

\> show elapsed time

センサーの計測開始からの経過時間を以下のように表示します。

"Elapsed Time" : "[Hours]h [Minutes]m [Seconds]s"

## 10-5. show config

本コマンドは各センサーの設定を以下のように表示します。 [] 内の値については「3-1-1. 設定ファイル記述」を参照してください。

コマンドプロンプトで以下のように実行してください。

\> show config

各センサーの設定は以下のように表示します。

 "[sensor1 name]": "frequency"=[sensor1 freuency] Hz

               : "watermark"=[sensor1 watermark]

               : "logfilename"="[sensor1 logfilename]"

 "[sensor2 name]": "frequency"=[sensor2 freuency] Hz

               : "watermark"=[sensor2 watermark]

               : "logfilename"="[sensor2 logfilename]"

                     ~~~~~~

 "[sensorX name]": "frequency"=[sensorX freuency] Hz

               : "watermark"=[sensorX watermark]

               : "logfilename"="[sensorX logfilename]"

  "LogFilePath": = "[sensors log file container path]"

## 10-6. show info

本コマンドはアプリケーションの現在の情報を以下のように表示します。

コマンドプロンプトで以下のように実行してください。

\> show info

アプリケーションの現在の情報は以下のように表示します。

"[sensor1 name] ": "frequency"=[sensor1 freuency] Hz

  　　　　　　　　: "watermark"=[sensor1 watermark]

 　　　　　　　　 : "logfilename"="[sensor1 logfilename]"

 　　　　　　　　 : "CurrentLogFileStartDate"="[Month] [Day] [Hours]:[Minutes]:[Seconds] [Year]"

"[sensor2 name] ": "frequency"=[sensor2 freuency] Hz

    　　　　　　　: "watermark"=[sensor2 watermark]

  　　　　　　　　: "logfilename"="[sensor2 logfilename]"

 　　　　　　　　 : "CurrentLogFileStartDate"="[Month] [Day] [Hours]:[Minutes]:[Seconds] [Year]"

                   ~~~~~~

"[sensorX name] ": "frequency"=[sensorX freuency] Hz

  　　　　　　　　: "watermark"=[sensorX watermark]

  　　　　　　　　: "logfilename"="[sensorX logfilename]"

  　　　　　　　　: "CurrentLogFileStartDate"="[Month] [Day] [Hours]:[Minutes]:[Seconds] [Year]"

"Common         ": "SensorStartTime"="[sensors log file container path]"

  　　　　　　　　: "SensorElapsedTime"="[sensors log file container path]"

  　　　　　　　　: "LogFilePath"="[sensors log file container path]"

## 10-7. fw update

本コマンドはアプリケーションをアップデートして再起動します。

コマンド実行前に".spk"形式のファイルをストレージ内に格納してください。（以降ファイル名を含む格納先パスを [fw file path] と表記します）

コマンドプロンプトで以下のように実行してください。

\> fw update -f [fw file path]

本コマンドが正常終了すると、自動的にSpresense Cubeが再起動します。

## 10-8. config update

本コマンドはセンサーの設定を 1 項目更新します。

センサー動作中の場合は、一度全センサーを停止させてから更新した設定を反映して再開します。

コマンドプロンプトで以下のように実行してください。

\> config update [sensor name] [config name] [value]

sensor name, config name, valueについては「3-1-1. 設定ファイル記述」を参照してください。

    ※ GNSSのSatelliteSystemsは配列設定となりますので、衛星システムが追加されます。

 衛星システムを削除したい場合は、コマンドプロンプトで以下のように実行してください。

\> config update gnss SatelliteSystems [value] -d

ただし、SatelliteSystemsに衛星システムが 1 つしか無い場合は削除できません。

 また、SatelliteSystemsが空の設定ファイルを使用する場合、本コマンドによる追加もできません。

本コマンド正常終了時のログ出力はなく、異常終了時にエラーログが出力されます。

## 10-9. config file update

本コマンドはセンサーの設定ファイルを指定したファイルの内容で更新します。

コマンド実行前にJSON形式のファイルをストレージ内に格納してください。（以降ファイル名を含む格納先パスを [jsoon file path] と表記します）

コマンドプロンプトで以下のように実行してください。

\> config file update [json file path]

本コマンド正常終了時のログ出力はなく、異常終了時にエラーログが出力されます。

## 10-10. show log list

本コマンドはセンサーログを保存したファイルの一覧を表示します。

コマンドプロンプトで以下のように実行してください。

\> show log list

また、特定のセンサーのログファイルのみ表示させることもできます。

この場合、「3-1-2. 設定ファイルパラメータの設定範囲」に記載している Nameパラメータでセンサーを指定することができます。

\> show log list [Name]

## 10-11. date

本コマンドはシステム時刻を表示、設定します。

時刻を表示させる場合、コマンドプロンプトで以下のように実行してください。

\> date

時刻は以下のように表示されます。

"MMM DD hh:mm:ss YYYY

- MMM ：月（英語 3文字の省略系　例：Jan）
- DD　 ：日（1～31）
- hh　  ：時（0～23）
- mm　：分（0～59）
- ss 　  ：秒（0～59）
- YYYY ：年（西暦1900 ~ 2100）

時刻を設定する場合、コマンドプロンプトで以下のように実行してください。

\> date -s YYYY/MM/DD hh:mm:ss

- YYYY ：年（西暦1900 ~ 2100）
- MM　 ：月（1～12）
- DD　  ：日（1～31）
- hh　   ：時（0～23）
- mm 　：分（0～59）
- ss　   ：秒（0～59）

## 10-12. help

本コマンドは使用可能なコマンドのシンタックスに加えてコマンド動作の概要と引数の説明を表示します。

コマンドプロンプトで以下のように実行してください。

\> help
