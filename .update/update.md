# Dockerfile ベースイメージ更新手順

## 概要
docker-nodenv/24.2.0-bookworm/Dockerfileのベースイメージを `node:X.X.X-bookworm-slim` (Debian 12) から `node:X.X.X-trixie-slim` (Debian 13) に更新する。
システムのデフォルトのnode versionは 最新のLTSとする。(https://nodejs.org/ja/about/previous-releases)

## 更新手順

### 1. ベースイメージの更新
Dockerfileの1-7行目を以下のように変更する：

**変更前:**
```dockerfile
FROM node:18.20.8-bookworm-slim AS node18
FROM node:20.19.2-bookworm-slim AS node20
FROM node:22.16.0-bookworm-slim AS node22
FROM node:23.11.1-bookworm-slim AS node23
FROM node:24.2.0-bookworm-slim AS node24

FROM node:24.2.0-bookworm-slim
```

**変更後:**
```dockerfile
FROM node:18.20.8-trixie-slim AS node18
FROM node:20.19.2-trixie-slim AS node20
FROM node:22.16.0-trixie-slim AS node22
FROM node:23.11.1-trixie-slim AS node23
FROM node:24.2.0-trixie-slim AS node24

FROM node:24.2.0-trixie-slim
```

### 2. 動作確認
以下のコマンドでDockerイメージをビルドし、正常に動作することを確認する：

```bash
cd docker-nodenv
docker build -t conchoid/docker-nodenv:v1.5.0-1-24.2.0-trixie -f 24.2.0-trixie/Dockerfile .
```

### 3. 互換性チェック
Debian 13への更新により、以下の点を確認する：

- パッケージの互換性（apt-getでインストールしているパッケージが利用可能か）
- nodenvの動作確認
- node-buildの動作確認
- Node.js各バージョン（18.20.8, 20.19.2, 22.16.0, 23.11.1, 24.2.0）のインストール確認
- npmの動作確認
- pnpmのインストール確認
- ロケール設定の確認

### 4. テスト実行
実際のNode.jsプロジェクトでイメージを使用し、以下を確認する：

- ビルドが正常に完了するか
- 依存関係の解決が正常に行われるか
- 実行時エラーが発生しないか
- nodenvによるNode.jsバージョン切り替えが正常に動作するか

## 注意事項
- Debian 13 (trixie) は比較的新しいリリースのため、一部のパッケージやツールのバージョンが変更されている可能性がある
- 問題が発生した場合は、パッケージのバージョン指定や代替パッケージの検討が必要になる場合がある
- **apt-getでインストールしているライブラリは必要なライブラリなので、trixieでもインストールを行う必要がある**
- ビルド後は、nodenv、node-build、各Node.jsバージョン、npm、pnpmが正常にインストールされていることを確認すること

