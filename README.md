# aws-notes

AWS に関する個人的なメモです。

📖 https://hiro1202.github.io/aws-notes/

## ローカルでプレビューする

```sh
pip install -r requirements.txt
mkdocs serve --open
```

http://127.0.0.1:8000 が立ち上がり、ファイルを保存すると自動でリロードされる（ライブリロードはデフォルトで有効。`--open` はブラウザの自動起動）。

ページ数が増えて再ビルドが遅くなってきたら、変更したファイルだけ再ビルドする `--dirty` を付ける。

```sh
mkdocs serve --open --dirty
```
