# summer04 - 今風 OpenGL の使い方（第７回 カメラパラメータ）サンプルプログラム

## 1. 概要

このプログラムは、OpenGL の **GLSL バーテックスシェーダ** と **uniform 変数** を用いて、画角（`fovy`）、アスペクト比（`aspect`）、前方面および後方面の距離（`near`, `far`）から透視投影変換行列（`cameraMatrix`）を算出し、ビュー変換行列（`lookAt`）と合成して図形を描画する手順を学ぶための、学生向けのサンプルプログラムです。本プログラムは、以下のブログ記事の解説に沿って学習を進めるための雛形として提供されています。

- [第７回 カメラパラメータ](https://tokoik.github.io/blog/glsl/2009/09/07/glsl.html)

従来の `gluPerspective()` を使わず、カメラの焦点距離や画角から前方面の表示領域（left, right, bottom, top）を求め、透視投影変換行列を生成する手法を学習します。

## 2. ビルド方法

このプログラムは [CMake](https://cmake.org/) を用いてビルド環境を整備します。各OSとも、ソースコードが置かれているディレクトリにターミナル（またはコマンドプロンプト）で移動してから、以下の手順を実行してください。なお、プログラムをビルドするためのバイナリディレクトリは、バージョン管理ファイル（.gitignore）の設定に合わせて **build** という名前にします。

### 2.1 Windows (Visual Studio 2022 の場合)

1. コマンドプロンプトまたは PowerShell を開き、このプロジェクトのディレクトリに移動します。
2. 以下のコマンドを実行してビルドディレクトリを作成し、CMake で構成を行います。

   ```bat
   mkdir build
   cd build
   cmake .. -G "Visual Studio 17 2022"
   ```

3. 生成された build フォルダ内の summer04.sln を Visual Studio で開きます。
4. ソリューションエクスプローラーで **summer04** プロジェクトを右クリックし、「スタートアップ プロジェクトに設定」を選択します。
5. 「ローカル Windows デバッガー」をクリックするか、F5 キーを押してビルドおよび実行します。

### 2.2 macOS (Xcode の場合)

1. ターミナルを開き、このプロジェクトのディレクトリに移動します。
2. 以下のコマンドを実行してビルドディレクトリを作成し、Xcode 用のプロジェクトを生成します。

   ```sh
   mkdir build
   cd build
   cmake .. -G Xcode
   ```

3. 生成された build/summer04.xcodeproj を Xcode で開きます。
4. 左上のスキーム選択（再生ボタンの横）が **summer04** になっていることを確認します。
5. 「Run」ボタン（再生ボタン）をクリックするか、Command + R を押してビルドおよび実行します。

### 2.3 Ubuntu Linux

1. ターミナルを開き、このプロジェクトのディレクトリに移動します。
2. 必要なパッケージ（freeglut3-dev など）がインストールされていることを確認し、以下のコマンドでビルドします。

   ```sh
   mkdir build
   cd build
   cmake ..
   make
   ```

## 3. 使い方

### 3.1 プログラムの起動方法

- **Windows**: `build\Debug\summer04.exe`
- **macOS**: `open build/Debug/summer04.app` または Xcode 上で Run
- **Ubuntu Linux**: `cd build && ./summer04`

### 3.2 操作方法

- 画角 30 度、アスペクト比 1.0 で透視投影変換された折れ線図形が表示されます。

## 4. 解説

### 4.1 画角からの透視投影変換行列の算出 (cameraMatrix)

```cpp
void cameraMatrix(float fovy, float aspect, float near, float far, GLfloat *matrix)
{
  float f = 1.0f / tanf(fovy * 0.5f * 3.14159265358979323846f / 180.0f);
  float dz = far - near;

  matrix[ 0] = f / aspect;
  matrix[ 5] = f;
  matrix[10] = -(far + near) / dz;
  matrix[11] = -1.0f;
  matrix[14] = -2.0f * far * near / dz;
  matrix[ 1] = matrix[ 2] = matrix[ 3] = matrix[ 4] =
  matrix[ 6] = matrix[ 7] = matrix[ 8] = matrix[ 9] =
  matrix[12] = matrix[13] = matrix[15] = 0.0f;
}
```
