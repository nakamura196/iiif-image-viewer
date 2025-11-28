# IIIF Image Viewer：IIIF Image API専用のシンプルなビューア

## はじめに

IIIF Image APIのinfo.jsonを直接指定して画像を閲覧できるシンプルなWebビューアを作成しました。

**デモ**: https://nakamura196.github.io/iiif-image-viewer/

**リポジトリ**: https://github.com/nakamura196/iiif-image-viewer

## 作った理由

IIIFには主に2つのAPIがあります：

- **IIIF Image API**: 画像の配信に関する仕様（info.json）
- **IIIF Presentation API**: 画像の構造化・メタデータに関する仕様（manifest.json）

IIIF Presentation API（manifest.json）に対応したビューアは[Mirador](https://projectmirador.org/)や[Universal Viewer](https://universalviewer.io/)など豊富に存在します。一方、IIIF Image API（info.json）を直接指定して閲覧できるビューアは意外と少ないのが現状です。

そこで、**IIIF Image APIのinfo.jsonを直接指定して閲覧できる**シンプルなビューアを作成しました。

オプションとして、以下の機能も用意しています：

- 領域（xywh）を指定してフォーカス表示
- 任意の角度で回転表示
- すべての設定をURLパラメータで共有

## 機能

### 基本機能：IIIF Image APIの表示

info.jsonのURLを入力するだけで画像を表示できます。

```
?iiif=https://ids.lib.harvard.edu/ids/iiif/47174896/info.json
```

### オプション機能

#### 領域指定（xywh）

画像の特定領域にフォーカスを当てて表示できます。座標は`x,y,width,height`の形式で指定します。

```
?iiif=https://example.com/info.json&xywh=6500,17222,240,316
```

古文書の特定の文字や絵図の一部分を共有したいときに便利です。

#### 回転表示

任意の角度で画像を回転表示できます。縦書きの文書を横向きに表示したい場合などに使えます。

```
?iiif=https://example.com/info.json&rotation=90
```

#### 組み合わせ

これらのパラメータは組み合わせて使用できます。

```
?iiif=https://www.digital.archives.go.jp/api/iiif/001891947.tif/info.json&xywh=6500,17222,240,316&rotation=90
```

## 技術スタック

### OpenSeadragon

画像ビューアのコアには[OpenSeadragon](https://openseadragon.github.io/)を使用しています。OpenSeadragonは高解像度のタイル画像を効率的に表示できるJavaScriptライブラリで、IIIF Image APIにも対応しています。

```javascript
viewer = OpenSeadragon({
    id: "viewer",
    tileSources: tileSource,  // info.jsonのURL
    degrees: rotation || 0,   // 回転角度
});
```

### 領域指定の実装

領域指定は、画像座標をOpenSeadragonのビューポート座標に変換してフォーカスを当てています。

```javascript
viewer.addHandler('open', function() {
    const [x, y, w, h] = xywh.split(',').map(Number);
    const tiledImage = viewer.world.getItemAt(0);
    const imageSize = tiledImage.getContentSize();

    // 画像座標をビューポート座標に変換
    const rect = new OpenSeadragon.Rect(
        x / imageSize.x,
        y / imageSize.x,
        w / imageSize.x,
        h / imageSize.x
    );
    viewer.viewport.fitBounds(rect, true);
});
```

### URLパラメータの同期

読み込みボタンを押すと、現在の設定がURLパラメータに反映されます。これにより、ブラウザの履歴機能で前の状態に戻ったり、URLをコピーして他の人と共有したりできます。

```javascript
const newUrl = new URL(window.location.href);
newUrl.searchParams.set('iiif', url);
newUrl.searchParams.set('rotation', rotation);
if (xywh) {
    newUrl.searchParams.set('xywh', xywh);
}
history.pushState(null, '', newUrl);
```

## 使い方

### 基本的な操作

| 操作 | 動作 |
|-----|------|
| ドラッグ | 画像を移動 |
| スクロール | ズームイン/アウト |
| ダブルクリック | 拡大 |

### URLパラメータ

| パラメータ | 説明 | 例 |
|-----------|------|-----|
| `iiif` | IIIF画像のinfo.json URL | `?iiif=https://example.com/info.json` |
| `xywh` | フォーカス領域 (x,y,w,h) | `&xywh=1000,2000,500,300` |
| `rotation` | 回転角度（度） | `&rotation=90` |

## 活用例

### 古文書の特定箇所を共有

国立公文書館デジタルアーカイブの絵図から、特定の村名を90度回転させて表示：

```
https://nakamura196.github.io/iiif-image-viewer/?iiif=https://www.digital.archives.go.jp/api/iiif/001891947.tif/info.json&xywh=6500,17222,240,316&rotation=90
```

### 美術作品の細部を共有

Harvard Art Museumsの作品から特定の部分をフォーカス表示：

```
https://nakamura196.github.io/iiif-image-viewer/?iiif=https://ids.lib.harvard.edu/ids/iiif/47174896/info.json&xywh=500,500,300,300
```

## まとめ

シンプルな機能に絞ったIIIF画像ビューアを作成しました。URLパラメータで状態を共有できるため、研究や教育の場面で「この画像のこの部分を見てほしい」というコミュニケーションが簡単になります。

ソースコードはGitHubで公開しています。フィードバックやプルリクエストをお待ちしています。

## 参考リンク

- [IIIF Image Viewer デモ](https://nakamura196.github.io/iiif-image-viewer/)
- [GitHub リポジトリ](https://github.com/nakamura196/iiif-image-viewer)
- [OpenSeadragon](https://openseadragon.github.io/)
- [IIIF Image API](https://iiif.io/api/image/3.0/)
- [国立公文書館デジタルアーカイブ](https://www.digital.archives.go.jp/)
