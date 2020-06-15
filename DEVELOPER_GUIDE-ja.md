# Mini Tokyo 3D 開発者ガイド

English version is available [here](DEVELOPER_GUIDE-en.md).

本ドキュメントは、開発者が Mini Tokyo 3D を Web ページに埋め込んだり、自分のアプリケーションに組み込んで使うための方法を説明します。Mini Tokyo 3D 自体の使い方を知りたい場合は、[Mini Tokyo 3D ユーザーガイド](USER_GUIDE-ja.md)をご覧ください。

## Mini Tokyo 3D の使用

Mini Tokyo 3D を Web ページに埋め込んで利用する、もしくは API を使って操作する方法は非常にシンプルです。まずは、このセクションの説明に従って設定してください。

### 使用の準備

Mini Tokyo 3D は ES6 に対応した主要ブラウザで動作します。Internet Explorer には非対応です。 

#### Mapbox アクセストークンの入手

Mini Tokyo 3D は地図タイルに [Mapbox](https://www.mapbox.com) のサービスを利用しているため、利用には Mapbox のアクセストークンが必要です。[Map Loads for Web](https://www.mapbox.com/pricing/#maploads) セッションを利用しており、月間 50,000 接続までは無料です。下記の手順に従って、アクセストークンを入手してください。

1. [サインアップ](https://account.mapbox.com/auth/signup/)ページでユーザー情報を入力して、Mapbox アカウントを作成します。
2. Mapbox アカウントログイン後、画面上部のメニューから「Tokens」をクリックしてアクセストークン一覧を表示します。アカウント作成直後はデフォルトの「Default public token」のみが表示されます。
3. 画面右上の「Create a token」ボタンをクリックして、アクセストークン作成ページに進みます。
4. 「Token name」にはあなたの Web サイト名やアプリ名など、任意の名前を入力します。
5. 「Token scopes」はデフォルト設定（Public scopes にすべてチェックが入った状態）のままにします。
6. 「Token restrictions」の「URL」欄には、Mini Tokyo 3D を設置するサイトの URL を入力して「Add URL」ボタンをクリックします。URL の形式は、[URL restrictions](https://docs.mapbox.com/accounts/overview/tokens/#url-restrictions) を参考にしてください。この URL 制限を設定しておくことで、他のサイトからこのアクセストークンを利用されることを防ぎます。
7. 最後に画面下部の「Create token」ボタンをクリックすると、アクセストークン一覧に新たに作成されたトークンが表示されます。

### 直接 Web ページに組み込む

単純に Web ページに Mini Tokyo 3D のマップを表示するだけであれば、次のように HTML ファイルを編集するだけです。

まず、jsDelivr CDN のリンクを使用して、Mini Tokyo 3D のスタイルシートと JavaScript コードを HTML ファイルの `<head>` エレメント内で読み込みます。

```html
<head>
  <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/mini-tokyo-3d@latest/dist/mini-tokyo-3d.min.css" />
  <script src="https://cdn.jsdelivr.net/npm/mini-tokyo-3d@latest/dist/mini-tokyo-3d.min.js"></script>
</head>
```

同じ HTML ファイルの `<body>` エレメント内で、`id` のついた HTML エレメント（下の例では `<div>` エレメント）を追加し、`<script>` エレメントで Mini Tokyo 3D インスタンスを作成する JavaScript コードを記述します。コンストラクタに渡す `options` オブジェクトの `container` には HTML エレメントの `id` を指定します。また、`secrets.mapbox` には、上のステップで入手した Mapbox アクセストークンを指定します。

```html
<body>
  <div id="mini-tokyo-3d" style="width: 400px; height: 400px;"></div>

  <script>
    const options = {
      container: 'mini-tokyo-3d',
      secrets: {
        mapbox: '<Mapbox アクセストークン>'
      }
    };
    const mt3d = new MiniTokyo3D(options);
  </script>
</body>
```

### モジュールとしてアプリに組み込む

バンドラーを使って Mini Tokyo 3D を自分のアプリケーションのコードに組み込む場合には、次の手順に従ってください。

まず、Mini Tokyo 3D の npm モジュールをインストールし、あなたのアプリケーションの `package.json` に登録します。

```bash
npm install mini-tokyo-3d --save
```

CommomJS 形式でモジュールを読み込む場合は、コードの先頭で次のように記載します。

```js
const MiniTokyo3D = require('mini-tokyo-3d');
```

ES6 形式でモジュールを読み込む場合は、コードの先頭で次のように記載します。

```js
import MiniTokyo3D from 'mini-tokyo-3d';
```

アプリケーションのコード内で、次のようにして MiniTokyo3D オブジェクトを初期化します。`options` オブジェクトの `container` には Mini Tokyo 3D がマップを表示する HTML エレメントの ID を指定します。また、`secrets.mapbox` には、上のステップで入手した Mapbox アクセストークンを指定します。

```js
const options = {
  container: '<コンテナエレメントの ID>',
  secrets: {
    mapbox: '<Mapbox アクセストークン>'
  }
};
const mt3d = new MiniTokyo3D(options);
```

## Mini Tokyo 3D API

JavaScript で Mini Tokyo 3D API を使うことで、様々なカスタマイズを行うことが可能です。バージョン 2.1 の時点では、`MiniTokyo3D` オブジェクトのコンストラクタオプションのみがサポートされています。

**注意**: 現在 Mini Tokyo 3D API はベータ版です。API の変更の可能性があるため、バージョン間の互換性は保証されません。

### MiniTokyo3D

`MiniTokyo3D` オブジェクトは、Web ページ上の Mini Tokyo 3D マップを表しています。`MiniTokyo3D` を作るには `container` やその他のオプションを指定してコンストラクタを呼び出します。すると、Web ページ上のマップが初期化され、`MiniTokyo3D` が返されます。

```js
new MiniTokyo3D(options: Object)
```

#### パラメータ

**`options`** ([`Object`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object))

名前 | 説明
--- | ---
**`options.container`**<br>[`string`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String) | Mini Tokyo 3D がマップを表示する HTML エレメントの `id`
**`options.secrets`**<br>[`Secrets`](#secrets) | データ取得に使用するアクセストークンを格納するオブジェクト
**`options.lang`**<br>[`string`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String) | 言語を表す [IETF 言語タグ](https://ja.wikipedia.org/wiki/IETF言語タグ)。未指定の場合は、ブラウザのデフォルト言語が使われる。現在 `'ja'`, `'en'`, `'ko'`, `'zh-Hans'`, `'zh-Hant'`, `'th'`, `'ne'` がサポートされている。サポートしていない言語が指定された場合は `'en'` が使われる
**`options.dataUrl`**<br>[`string`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String) | Mini Tokyo 3D のデータ URL。未指定の場合は、`'https://minitokyo3d.com/data'` が使われる
**`options.clockControl`**<br>[`boolean`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Boolean)<br>default: `true` | `true` の場合、時刻表示をマップに追加する
**`options.searchControl`**<br>[`boolean`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Boolean)<br>default: `true` | `true` の場合、検索ボタンをマップに追加する
**`options.navigationControl`**<br>[`boolean`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Boolean)<br>default: `true` | `true` の場合、ナビゲーションボタンをマップに追加する
**`options.fullscreenControl`**<br>[`boolean`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Boolean)<br>default: `true` | `true` の場合、フルスクリーンボタンをマップに追加する
**`options.modeControl`**<br>[`boolean`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Boolean)<br>default: `true` | `true` の場合、表示モード切り替えボタンをマップに追加する
**`options.infoControl`**<br>[`boolean`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Boolean)<br>default: `true` | `true` の場合、アプリ情報ボタンをマップに追加する
**`options.trackingMode`**<br>[`string`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String)<br>default: `'helicopter'` | 初期の追跡モードを指定する。`'helicopter'` または `'train'` がサポートされている
**`options.center`**<br>[`LngLatLike`](https://docs.mapbox.com/mapbox-gl-js/api/#lnglatlike)<br>default: `[139.7670, 35.6814]` | 初期のマップ中心点の座標。未指定の場合は、東京駅付近（`[139.7670, 35.6814]`）に設定される。注: Mini Tokyo 3D では、GeoJSON と同様に経度、緯度の順で座標を指定する
**`options.zoom`**<br>[`number`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number)<br>default: `14` | 初期のマップのズームレベル。未指定の場合は、`14` に設定される
**`options.bearing`**<br>[`number`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number)<br>default: `0` | 初期のマップの方角。真北から反時計回りの角度で指定する。未指定の場合は、真北（`0`）に設定される
**`options.pitch`**<br>[`number`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number)<br>default: `60` | 初期のマップの傾き。画面に対する地表面の角度（0〜60）で指定する。未指定の場合は、`60` に設定される
**`options.frameRate`**<br>[`number`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number)<br>default: `60` | 列車や旅客機のアニメーションのフレームレート（1秒あたりのフレーム数）。1〜60 の間で指定する。数値を小さくすると、アニメーションの滑らかさが減少する一方で CPU リソースの使用も下がるため、モバイルデバイスでのバッテリー消費を抑えることができる。未指定の場合は、`60` に設定される

### Secrets

`Secrets` オブジェクトは、データ取得に使用するアクセストークンを格納するオブジェクトで、`MiniTokyo3D` のコンストラクタオプション `secrets` に指定します。

#### プロパティ

**`tokyochallenge`** ([`string`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String)) : [東京公共交通オープンデータチャレンジ](https://tokyochallenge.odpt.org)のアクセストークン。未指定の場合は、デフォルトのトークンが使われる

**`odpt`** ([`string`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)) : [公共交通オープンデータセンター](https://www.odpt.org)のアクセストークン。未指定の場合は、デフォルトのトークンが使われる

**`mapbox`** ([`string`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String)) : [Mapbox](https://www.mapbox.com) のアクセストークン。未指定の場合はマップのロード時にエラーが起きるため、必ず自分の Web サイト専用のアクセストークンを入手して指定する

## Mini Tokyo 3D のビルド

リリース前の最新版の機能を試したい、自分でコードを改造したい、Mini Tokyo 3D の開発にコントリビュートしたい、という場合には、本セクションの手順に従ってソースコードからプロジェクトをビルドすることができます。

### ビルド準備

次のソフトウェアが必要です。

- [Node.js](https://nodejs.org/ja/) 最新版
- [Git](https://git-scm.com) 最新版（リポジトリをクローンする場合）

Mini Tokyo 3D は次のデータソースを使用しており、ビルド時にそれぞれのデータソースに対するアクセストークンが必要です。下記の手順に従って、アクセストークンを入手してください。

データソース | サインアップ用 URL | アクセストークンの形式
--- | --- | ---
[東京公共交通オープンデータチャレンジ](https://tokyochallenge.odpt.org) | [リンク](https://developer-tokyochallenge.odpt.org/users/sign_up) | 数字と英小文字からなる文字列
[公共交通オープンデータセンター](https://www.odpt.org) | [リンク](https://developer.odpt.org/users/sign_up) | 数字と英小文字からなる文字列
[Mapbox](https://www.mapbox.com) | [リンク](https://account.mapbox.com/auth/signup/) | `pk.` で始まるピリオドを含む英数字文字列

#### 東京公共交通オープンデータチャレンジアクセストークンの入手

Mini Tokyo 3D は[東京公共交通オープンデータチャレンジ](https://tokyochallenge.odpt.org)で配信されている列車データや旅客機データを利用しています。データの入手には開発者としての登録が必要ですが、無料で利用可能です。

1. [開発者サイトへの登録](https://developer-tokyochallenge.odpt.org/users/sign_up)ページでユーザー情報を入力して、開発者登録をします。登録完了のメールが届くまでに数日かかる場合があります。
2. 開発者アカウントでログイン後、画面上部のメニューから「Account」をクリックして「アクセストークンの確認・追加」を選びます。
3. アクセストークン一覧が表示されます。アカウント作成直後はデフォルトの「DefaultApplication」のみが表示されます。「アクセストークンの追加発行」をクリックします。
4. 「名前」に任意のアプリケーション名を入力して、「Submit」ボタンをクリックします。
5. アクセストークン一覧に新たに作成されたトークンが表示されます。

#### 公共交通オープンデータセンターアクセストークンの入手

Mini Tokyo 3D は[公共交通オープンデータセンター](https://www.odpt.org)のデータも併せて利用しています。こちらも、データの入手には開発者としての登録が必要ですが、無料で利用可能です。

1. [開発者サイトへの登録](https://developer.odpt.org/users/sign_up)ページでユーザー情報を入力して、開発者登録をします。登録完了のメールが届くまでに数日かかる場合があります。
2. 開発者アカウントでログイン後、画面上部のメニューから「Account」をクリックして「アクセストークンの確認・追加」を選びます。
3. アクセストークン一覧が表示されます。アカウント作成直後はデフォルトの「DefaultApplication」のみが表示されます。「アクセストークンの追加発行」をクリックします。
4. 「名前」に任意のアプリケーション名を入力して、「Submit」ボタンをクリックします。
5. アクセストークン一覧に新たに作成されたトークンが表示されます。

#### Mapbox アクセストークンの入手

[Mapbox アクセストークンの入手](#mapbox-%E3%82%A2%E3%82%AF%E3%82%BB%E3%82%B9%E3%83%88%E3%83%BC%E3%82%AF%E3%83%B3%E3%81%AE%E5%85%A5%E6%89%8B) を参照してください。

### ビルド手順

#### 1. ファイルのダウンロード

Mini Tokyo 3D の [GitHub レポジトリ](https://github.com/nagix/mini-tokyo-3d)から `master` ブランチ最新版をダウンロードして、zipファイルを展開します。`mini-tokyo-3d-master` というディレクトリができますが、`mini-tokyo-3d` という名前に変更しておきます。

```bash
curl -LO https://github.com/nagix/mini-tokyo-3d/archive/master.zip
unzip master.zip
mv mini-tokyo-3d-master mini-tokyo-3d
```

もし Git をお使いでしたら、上記のコマンドの代わりに GitHub からリポジトリを直接クローンしても構いません。

```bash
git clone https://github.com/nagix/mini-tokyo-3d.git
```

#### 2. ビルド

Mini Tokyo 3D のトップディレクトリに移動します。

```bash
cd mini-tokyo-3d
```

ビルド準備のステップで取得したアクセストークンを記載した JSON ファイルを作成し、`secrets` というファイル名でこのディレクトリに保存します。

```json
{
    "tokyochallenge": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
    "odpt": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
    "mapbox": "pk.xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx.xxxxxxxxxxxxxxxxxxxxxx"
}
```

依存 npm モジュールをインストールします。

```bash
npm install
```

次のコマンドでプロジェクトをビルドします。

```bash
npm run build-all
```

ビルドが正常に完了すると、`dist` ディレクトリが作成されます。この中には配布用のスタイルシートおよび JavaScript ファイルが含まれています。また、同時に `build` ディレクトリも作成されます。この中には Web サイトへの設置に必要なすべてのファイルが含まれています。

#### 3. Web サイトへの設置

`build` ディレクトリに含まれる `index.html` は [https://minitokyo3d.com](http://minitokyo3d.com) 用の Web ページです。設置する Web サイトに合わせて編集した上で、`build` ディレクトリのファイル全てを Web サーバの公開ディレクトリに配置してください。