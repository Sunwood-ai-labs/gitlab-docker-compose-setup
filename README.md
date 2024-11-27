# Docker Compose による GitLab 自己ホスティング

## 概要
このリポジトリは、Docker Composeを使用してGitLab CEとGitLab Runnerを簡単にセットアップするための設定を提供します。

## 必要条件
- Docker Engine 20.10以上
- Docker Compose v2.0以上
- 最小システム要件:
  - CPU: 4コア
  - メモリ: 8GB以上
  - ストレージ: 50GB以上（推奨）

## セットアップ手順

### 1. リポジトリのクローン
```bash
git clone <repository-url>
cd <repository-name>
```

### 2. 環境変数の設定
1. `.env.example` ファイルを `.env` にコピーします：
```bash
cp .env.example .env
```
2. `.env` ファイルを編集し、必要な設定を行います：
- `GITLAB_ROOT_PASSWORD`: GitLab管理者のパスワードを設定
- `RUNNER_REGISTRATION_TOKEN`: GitLab Runnerの登録トークンを設定

### 3. Docker Composeの起動
```bash
docker compose up -d
```

### 4. 初期アクセス
1. ブラウザで `http://your-server-ip` にアクセス
2. 以下の認証情報でログイン：
   - ユーザー名: root
   - パスワード: `.env`で設定したGITLAB_ROOT_PASSWORD

### 5. GitLab Runnerの登録
1. GitLabの管理者画面からRunner登録トークンを取得
2. `.env`ファイルの`RUNNER_REGISTRATION_TOKEN`を更新
3. Runnerを再起動：
```bash
docker compose restart gitlab-runner
```

## バックアップとメンテナンス

### バックアップの実行
```bash
docker compose exec gitlab gitlab-backup create
```

### バックアップの復元
```bash
docker compose exec gitlab gitlab-backup restore
```

## トラブルシューティング

### ログの確認
```bash
docker compose logs gitlab
docker compose logs gitlab-runner
```

### システムステータスの確認
```bash
docker compose ps
```

## 注意事項
- 初回起動時は、GitLabの初期化に数分かかる場合があります
- システムリソースの使用状況を定期的に監視することをお勧めします
- 定期的なバックアップの実行を推奨します
