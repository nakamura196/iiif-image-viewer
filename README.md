# IIIF Image Viewer

IIIF (International Image Interoperability Framework) 画像を閲覧できるシンプルなWebビューアです。

## デモ

https://nakamura196.github.io/iiif-image-viewer/

## 機能

- IIIF Image API対応の画像を表示
- 領域指定（xywh）によるフォーカス表示
- 任意角度の回転表示
- URLパラメータによる状態共有
- ズーム・パン操作

## 使い方

### 基本

1. IIIF画像の `info.json` URLを入力
2. 「読み込み」ボタンをクリック（またはEnterキー）

### URLパラメータ

| パラメータ | 説明 | 例 |
|-----------|------|-----|
| `iiif` | IIIF画像のinfo.json URL | `?iiif=https://example.com/image/info.json` |
| `xywh` | フォーカス領域 (x,y,width,height) | `&xywh=1000,2000,500,300` |
| `rotation` | 回転角度（度） | `&rotation=90` |

### 使用例

```
# 基本
?iiif=https://ids.lib.harvard.edu/ids/iiif/47174896/info.json

# 領域指定 + 回転
?iiif=https://www.digital.archives.go.jp/api/iiif/001891947.tif/info.json&xywh=6500,17222,240,316&rotation=90
```

## 操作方法

| 操作 | 動作 |
|-----|------|
| ドラッグ | 画像を移動 |
| スクロール | ズームイン/アウト |
| ダブルクリック | 拡大 |

## 技術スタック

- [OpenSeadragon](https://openseadragon.github.io/) - 高解像度画像ビューアライブラリ
- Vanilla JavaScript
- GitHub Pages

## ローカル実行

```bash
# リポジトリをクローン
git clone https://github.com/nakamura196/iiif-image-viewer.git
cd iiif-image-viewer

# ローカルサーバーで実行
python3 -m http.server 8000 -d docs

# ブラウザで開く
open http://localhost:8000
```

## ライセンス

MIT License
