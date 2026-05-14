# MixSoda-util

> 日本語のREADMEはこちらです: [README.ja.md](README.ja.md)

Utilities for [MixSoda](https://mixsoda.shizentai.jp/).

## Features
- Backup daily data for MixSoda
- Generate a comprehensive backup file (`all.csv`) for each ICCID

## Usage

### Backup yesterday's data
```sh
deno run -A https://code4fukui.github.io/MixSoda-util/backup.js [Store Token] (all)
```
This will create the daily backup files in the `data/` directory.

### Generate `all.csv` files
```sh
deno run -A https://code4fukui.github.io/MixSoda-util/makeBackupAll.js
```
This will generate an `all.csv` file for each ICCID, containing all the data.

### Set up scheduled backups
Create a GitHub Actions workflow file `.github/workflows/scheduled-backup.yml` with the following content:

```yml
name: Scheduled 

on:
  schedule:
    # Run at 1:31 AM JST (16:31 UTC)
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

Remember to set the `SECRET_TOKEN` secret in your GitHub repository settings.

## License
MIT License — see [LICENSE](LICENSE).