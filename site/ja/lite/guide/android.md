# Androidクイックスタート

AndroidでTensorFlow Liteを始めるには、下記の例を調べてみることをおすすめします。

<a class="button button-primary" href="https://github.com/tensorflow/examples/tree/master/lite/examples/image_classification/android">Android
での画像区分の例</a>

ソースコードの解説には、
[TensorFlow Lite Androidによる画像区分](https://github.com/tensorflow/examples/blob/master/lite/examples/image_classification/android/EXPLORE_THE_CODE.md)
も読んでください。

この例アプリは
[画像区分](https://www.tensorflow.org/lite/models/image_classification/overview)
を使い、デバイスの後方向きのカメラに写るものを連続的に何でも区別します。
アプリケーションはデバイスでもエミュレータでも走ることができます。

推論はTensorFlow Lite Java APIと
[TensorFlow Lite Androidサポートライブラリ](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/lite/experimental/support/java/README.md)を使って
行われています。
デモアプリはリアルタイムにフレームを区分しながら、一番可能性のある区別を表示します。
ユーザは浮動小数点と
[量子化された](https://www.tensorflow.org/lite/performance/post_training_quantization)
モデルのどちらかを選ぶことができ、スレッドの数を指定し、CPU、GPU、または
[NNAPI](https://developer.android.com/ndk/guides/neuralnetworks)のどれで
走らせるかを決めます。

付記: 様々なユースケースでのTensorFlow Liteをデモする追加のアプリケーションは
[例](https://www.tensorflow.org/lite/examples)にあります。

## Android Studioでビルドする

例をAndroid Studioでビルドするには、
[README.md](https://github.com/tensorflow/examples/blob/master/lite/examples/image_classification/android/README.md)に記載の指示に従ってください。

## あなたのAndroidアプリを作る

Androidコードを書くことを早く始めるためには、
その最初の手がかりとして私たちの
[Androidの画像区別の例](https://github.com/tensorflow/examples/tree/master/lite/examples/image_classification/android)
をおすすめします。

以下の各セクションは
TensorFlow LiteをAndroid上で使う上で有益な情報を含んでいます。

### TensorFlow Lite Androidサポートライブラリを使う

TensorFlow Lite Androidサポートライブラリはモデルのあなたのアプリケーションへのインテグレーションを容易にします。
提供されるハイレベルのAPImの助けにより生の入力データをモデルが必要とする形式に変換し、
またモデルの出力を解釈し、
必要となるボイラープレートのコードを削減できます。

入力と出力には画像や配列を含む共通のデータ形式をサポートします、
さらに例えば画像のリサイズやクロップなどのタスクを行うための前処理と後処理のユニットを提供します。

始めるためには、
[TensorFlow Lite Android Support Library README.md](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/lite/experimental/support/java/README.md)
の手順に従ってください。

### JCenterにあるTensorFlow Lite AARを使う

あなたのAndroidアプリでTensorFlow Liteを使うには、[JCenterにホストされているTensorFlow Lite AAR](https://bintray.com/google/tensorflow/tensorflow-lite)を
使うことをお勧めします。

これをあなたの`build.gradle`のdependenciesで下記のように指定してください:

```build
dependencies {
    implementation 'org.tensorflow:tensorflow-lite:0.0.0-nightly'
}
```

このAARは[Android ABI](https://developer.android.com/ndk/guides/abis)の
全てのためのバイナリを含んでいます。
あなたはあなたのアプリケーションのバイナリサイズを、
サポートしたいABIだけを取り込むことにより、小さくできます。

ほとんどの開発者には`x86`、`x86_64`、そして`arm32` ABIを省くことをお勧めします。
これは次のGradleのコンフィグレーションを行うことにより達成できます、
これは特に
`armeabi-v7a`と`arm64-v8a`を含んでおりほとんどの現代的な
Androidデバイスを含んでいます。

```build
android {
    defaultConfig {
        ndk {
            abiFilters 'armeabi-v7a', 'arm64-v8a'
        }
    }
}
```

`abiFilters`についてもっと学ぶには、
Android Gradleドキュメントの
[`NdkOptions`](https://google.github.io/android-gradle-dsl/current/com.android.build.gradle.internal.dsl.NdkOptions.html)を見てください。

### TensorFlow Liteをローカルでビルドする

場合によっては、
ローカルビルドのTensorFlow Liteを使いたい場合があるでしょう。
例えば、
[TensorFlowから選んだオペレーション](https://www.tensorflow.org/lite/guide/ops_select)を
含むカスタムバイナリを作っていたり、
TensorFlow Liteにローカルの変更を行いたい場合です。

#### BazelとAndroidのPrerequisitesをインストールする

BazelはTensorFlowの主要なビルドシステムです。
Bazelでビルドするには、
それとAndroid NDKとSDKをあなたのシステムにインストールする必要があります。

1.  [Bazelのウェブサイトにある](https://bazel.build/versions/master/docs/install.html)
手順に沿って最新のBazelをインストールします。
2.   Android NDKがネイティブ(C/C++)のTensorFlow Liteコード
をビルドするために必要です。
    現在の推奨は17cで、
    [ここ](https://developer.android.com/ndk/downloads/older_releases.html#ndk-17c-downloads)にあります。
3.  Android SDKとビルドツールは
    [ここから](https://developer.android.com/tools/revisions/build-tools.html)または、
    [Android Studio](https://developer.android.com/studio/index.html)の一部として
    入手することができます。
    API >= 23のビルドツールがTensorFlow Liteをビルドする推奨のバージョンです。

#### WORKSPACEと.bazelrcをコンフィグする

TensorFlowチェックアウトディレクトリのルートで`./configure`スクリプトを走らせて、
`スクリプトに、Androidビルド用に/WORKSPACE`をインタラクティブにコンフィグするかと聞かれたら"Yes"と
答えます。
スクリプトは下記の環境変数を使って設定をコンフィグしようとします:

*   `ANDROID_SDK_HOME`
*   `ANDROID_SDK_API_LEVEL`
*   `ANDROID_NDK_HOME`
*   `ANDROID_NDK_API_LEVEL`

これらの変数が設定されていない時には、
スクリプトのプロンプトに対してインタラクティブに与えられなければいけません。
正しくコンフィグしたああいは次のようなエントリが
ルートフォルダにある`.tf_configure.bazelrc`ファイルに
現れるはずです:

```shell
build --action_env ANDROID_NDK_HOME="/usr/local/android/android-ndk-r17c"
build --action_env ANDROID_NDK_API_LEVEL="21"
build --action_env ANDROID_BUILD_TOOLS_VERSION="28.0.3"
build --action_env ANDROID_SDK_API_LEVEL="23"
build --action_env ANDROID_SDK_HOME="/usr/local/android/android-sdk-linux"
```

#### ビルドとインストール

Bazelを適切にコンフィグしたら、あなたはTensorFlow Lite AARを
チェックアウトディレクトリのルートから次のようにビルドします:

```sh
bazel build --cxxopt='-std=c++11' -c opt         \
  --fat_apk_cpu=x86,x86_64,arm64-v8a,armeabi-v7a \
  //tensorflow/lite/java:tensorflow-lite
```

これによりAARファイルが`bazel-bin/tensorflow/lite/java/`の中に生成されます。
これはいくつかのアーキテクチャを含んだ「太った」AARを作ることに注意してください、
その全部はいらないのであれば、
あなたのデプロイ環境に適したサブセットを使ってください。
そこから後で、その.aarをあなたの
Android Studioプロジェクトで使う方法はいくつかあります。

##### AARディレクトリをプロジェクトに追加する

`tensorflow-lite.aar`ファイルを
あなたのプロジェクトの中の`libs`というディレクトリにコピーします。
あなたのアプリの`build.gradle`ファイルを新しいディレクトリを参照するように変更し、
すでにあったTensorFlow Liteディペンデンシを新しいローカルライブラリに変更します。
例えば:

```
allprojects {
    repositories {
        jcenter()
        flatDir {
            dirs 'libs'
        }
    }
}

dependencies {
    compile(name:'tensorflow-lite', ext:'aar')
}
```

##### AARをローカルのMavenリポジトリにインストールする

下記をあなたのチェックアウトディレクトリのルートで実行してください:

```sh
mvn install:install-file \
  -Dfile=bazel-bin/tensorflow/lite/java/tensorflow-lite.aar \
  -DgroupId=org.tensorflow \
  -DartifactId=tensorflow-lite -Dversion=0.1.100 -Dpackaging=aar
```

あなたのアプリの`build.gradle`の中で、
`mavenLocal()`ディペンデンシがあることを確認し、
標準のTensorFlow Liteディペンデンシを
select TensorFlow opsのサポートのあるものに置き換えます。

```
allprojects {
    repositories {
        jcenter()
        mavenLocal()
    }
}

dependencies {
    implementation 'org.tensorflow:tensorflow-lite-with-select-tf-ops:0.1.100'
}
```

この`0.1.100`バージョンは単にテスト/開発のためのものであることに注意してください。
ローカルのAARをインストールすれば、あなたは標準の
[TensorFlow Lite JavaインタフェースAPI](inference.md)をあなたのアプリコードで使えます。
