# 掃除アプリ（Android / Capacitor）

単一HTML版を Capacitor で包んだ Android アプリプロジェクトです。

- appId: `jp.tomimama.souji`
- appName: 掃除アプリ
- 本体: `www/index.html`（単一HTML版をそのままコピーし、末尾に Capacitor 専用の通知コードだけを追加したもの。ブラウザで開くと通知コードは何もせず、従来どおり動作します）

## 通知について

- `@capacitor/local-notifications` を使用。
- ゴミ種別（localStorage `souji-app-v1` の `gomiTypes`）ごとに、収集日の**前日 20:00** に「明日は『○○』の収集日です。今日のうちにまとめましょう。」という通知をスケジュールします。
- 今後4週間分を張り、アプリ起動時・データ変更時（localStorage への保存を監視）・復帰時に張り直します。
- ゴミ種別が1つも無い場合は、通知許可を求めません。

## ビルド方法（通常のPC環境）

前提: JDK 21、Android SDK（platform android-36 / build-tools 36.0.0）

```sh
npm install
npx cap sync android
cd android && ./gradlew assembleDebug
# 出力: android/app/build/outputs/apk/debug/app-debug.apk
```

このワークスペース内には `.toolchain/` に Temurin JDK 21 と Android SDK、Gradle 8.14.3 を用意してあります（合計約1.2GB）。

## このVMでビルドできなかった理由（2026-11-16 時点）

サンドボックスのネットワーク権限（Direct network protocols の `other_tcp` = Deny）により、Java プロセスの TCP 接続が遮断され、Gradle デーモンとクライアント間のローカル通信（127.0.0.1）が成立しませんでした。
Muse 設定 → Permissions → Direct network protocols で `other_tcp` を Ask に変えれば、この環境でもビルドできる見込みです。その場合も、Gradle 本体は `.toolchain/gradle-8.14.3/bin/gradle` を使うのが確実です（ラッパーの配布物ダウンロードは、このプロキシ環境では Java の CONNECT 処理が失敗するため）。
