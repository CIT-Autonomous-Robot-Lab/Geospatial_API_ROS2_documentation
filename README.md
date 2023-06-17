# VPS Documentation

## 概要

ARCore Geospatial APIから取得した位置情報を ROS 2の`sensor_msgs/NavSatFix`型でPublishし、指定のIPアドレスに送信

## 使用環境

* スマートフォン（ARCore 対応デバイスは[こちら](https://developers.google.com/ar/devices?hl=ja)）
  * Google Pixel 6
    * Android 13

* ノートPC
  * Ubuntu 22.04
    * Unity Hub 3.4.2  
    * Unity 2021.3.26.f1  
  
## アプリ作成（スマートフォン側の準備）

### GCP（Google Cloud Platform）の設定

1. GCP にログイン
2. [プロジェクトの作成](https://console.cloud.google.com/projectcreate?hl=ja)
3. サイドバーで`API とサービス` -> `ライブラリ` の順に選択
4. ARCore API を検索し、有効にするをクリック。ARCore API の管理ページに遷移  
   <img src="https://github.com/CIT-Autonomous-Robot-Lab/VPS_documentation/blob/main/images/arcoreapi.png" width="720px">

5. サイドバーで`API とサービス` -> `認証情報` の順に選択し、`認証情報を作成` -> `APIキーを選択`  
   <img src="https://github.com/CIT-Autonomous-Robot-Lab/VPS_documentation/blob/main/images/authentication.png" width="720px">

6. API キーが作成されるのでコピー

### Unity の準備

1. [Unity Hub](https://unity.com/ja/download)をダウンロードしインストール
   （Linux にインストールする方法は[こちら](https://docs.unity3d.com/hub/manual/InstallHub.html#install-hub-linux)を参照してください。）
2. [こちら](https://github.com/devemin/Geospatial2ros)のリポジトリをダウンロードし Unity で開く
3. API Key の設定  
   `Unity` -> `編集` -> `プロジェクト設定` -> `XR Plug-in Management` -> `ARCore Extensions` -> `Android API Key` に先ほどの API キーを入力  
   <img src="https://github.com/CIT-Autonomous-Robot-Lab/VPS_documentation/blob/main/images/apikey.png" width="720px">

4. ROS の設定  
   `Unity` -> `Rbotics` -> `ROS Settings` で ROS のバージョンを ROS 2 に変更し、IP アドレスを入力（IP アドレスは後で変更可）
   <img src="https://github.com/CIT-Autonomous-Robot-Lab/VPS_documentation/blob/main/images/rossettings.png" width="720px">

5. API Level の設定  
   `Unity` -> `編集` -> `プロジェクト設定` -> `Project` -> `プレイヤー` -> `その他の設定` -> `識別` の API レベルを 2 ヵ所設定  
   ※API Level については [Android Developer](https://developer.android.com/guide/topics/manifest/uses-sdk-element?hl=ja#ApiLevels) を参照してください。
   <img src="https://github.com/CIT-Autonomous-Robot-Lab/VPS_documentation/blob/main/images/apilevel.png" width="720px">
6. プラットフォームの変更とビルド設定  
   `Unity` -> `File` -> `Build Settings` -> （プラットフォームを Android に変更）-> `シーンを追加` -> Geospatial2ros.unity を追加 -> `Build`  
   <img src="https://github.com/CIT-Autonomous-Robot-Lab/VPS_documentation/blob/main/images/buildsettings.png" width="720px">

7. apk ファイルが作成させるので、Android 端末に転送しインストール

## ROS 2のワークスペースの作成（ノートPC側の準備）

### パッケージのインストール & ビルド

1. [ROS-TCP-Endpoint](https://github.com/Unity-Technologies/ROS-TCP-Endpoint) パッケージのインストール
   ```
   mkdir -p ~/ros-tcp-endpoint_ws/src
   cd ros-tcp-endpoint_ws/src
   git clone -b main-ros2 https://github.com/Unity-Technologies/ROS-TCP-Endpoint
   cd ..
   colcon build
   source ~/ros-tcp-endpoint_ws/install/setup.bash
   ```
2. 実行
   ```
   ros2 run ros_tcp_endpoint default_server_endpoint --ros-args -p ROS_IP:=0.0.0.0
   ```
   ※ROS_IPはスマートフォンのIPアドレスを設定してください。  
   ※ノートPCとスマートフォンは同一のネットワークで接続されている必要があります。

## おまけ

### rosbagによるデータ収集

1. rosbag の録画  
   すべてのトピックを記録する場合（-a オプション）
   ```
   ros2 bag record -a
   ```
   指定するトピックのみを記録する場合（-o オプション）
   ```
   ros2 bag record -o <ファイル名>.db3 <トピック名>
   ```
2. rosbag の停止  
   録画を停止するには、以下のコマンドを実行  
   `Ctrl + C`
3. rosbag の再生
   ```
   ros2 bag play <ファイル名>.db3
   ```


### Foxgloveでの可視化

1. [Foxglove](https://console.foxglove.dev/) にアクセスしログイン  
   <img src="https://github.com/CIT-Autonomous-Robot-Lab/VPS_documentation/blob/main/images/foxglovelogin.png" width="720px">
2. `Visualize Data` を選択  
   <img src="https://github.com/CIT-Autonomous-Robot-Lab/VPS_documentation/blob/main/images/foxglovedashboard.png" width="720px">
3. `ローカルファイルを開く`を選択し、録画した bag file を開く  
   <img src="https://github.com/CIT-Autonomous-Robot-Lab/VPS_documentation/blob/main/images/foxglovefile.png" width="720px">
4. `Add Panel` から地図を選択（既存のパネルがすでにある場合は削除してから追加）  
   <img src="https://github.com/CIT-Autonomous-Robot-Lab/VPS_documentation/blob/main/images/foxglovepanel.png" width="720px">
5. 録画した bag file が可視化される  
   <img src="https://github.com/CIT-Autonomous-Robot-Lab/VPS_documentation/blob/main/images/foxglovevisualized.png" width="720px">
