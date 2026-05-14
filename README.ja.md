# MixSoda-util

[MixSoda](https://mixsoda.shizentai.jp/) 用のユーティリティです。

## 機能
- MixSodaの日次データのバックアップ
- 各ICCIDごとに全データを含むバックアップファイル（`all.csv`）を生成

## 使い方

### 昨日のデータのバックアップ
```sh
deno run -A https://code4fukui.github.io/MixSoda-util/backup.js [Store Token] (all)
```
これにより、`data/` ディレクトリに日次バックアップファイルが作成されます。

### `all.csv` ファイルの生成
```sh
deno run -A https://code4fukui.github.io/MixSoda-util/makeBackupAll.js
```
これにより、各ICCIDごとにすべてのデータを含む `all.csv` ファイルが生成されます。

### 定期バックアップの設定
以下の内容でGitHub Actionsのワークフローファイル `.github/workflows/scheduled-backup.yml` を作成してください：

```yml
name: Scheduled 

on:
  schedule:
    # JSTで午前1時31分（UTCで16時31分）に実行
    - cron: '31 16 * * *'

jobs:
  build:
    name: build
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - uses: denoland/setup-deno@v1
        with:
          deno-version: v1.x
      - name: make
        env:
          SECRET_TOKEN: ${{ secrets.token }}
        run: |
          deno run -A https://code4fukui.github.io/MixSoda-util/backup.js $SECRET_TOKEN
      - name: commit and push
        run: |
          git config --global user.email "workflow@example.com"
          git config --global user.name "workflow user"
          git add .
          git commit -m 'update data' && git push ${REPO} HEAD:${{github.event.pull_request.head.ref}} || true
          git push
```

GitHubリポジトリの設定で `SECRET_TOKEN` シークレットを設定することを忘れないでください。

## ライセンス
MIT License — 詳細は [LICENSE](LICENSE) を参照してください。
