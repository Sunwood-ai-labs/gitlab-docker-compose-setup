# GitLab Docker Compose Setup

## Overview
A Docker Compose configuration for self-hosting GitLab CE with GitLab Runner.

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

## バックアップとリストア

### 自動バックアップの設定（cron使用）
1. cronのインストール:
```bash
sudo apt-get update && sudo apt-get install -y cron
```

2. バックアップ用のcronジョブを設定:
```bash
echo "0 6 * * * root docker-compose exec -T gitlab gitlab-backup create >> /var/log/gitlab/backup.log 2>&1" | sudo tee /etc/cron.d/gitlab-backup
sudo chmod 0644 /etc/cron.d/gitlab-backup
```
※ 上記の設定では毎朝6時にバックアップを実行します。

### 手動バックアップの実行
```bash
# バックアップの作成
docker-compose exec gitlab gitlab-backup create

# バックアップファイルの確認（ホスト側）
ls -la ./backups/

# バックアップファイルの確認（コンテナ内）
docker-compose exec gitlab ls -la /var/opt/gitlab/backups/
```

### バックアップの復元
1. GitLabサービスを停止:
```bash
docker-compose down
```

2. バックアップファイルの権限を設定:
```bash
sudo chmod 777 -R ./backups/
```

3. GitLabサービスを起動:
```bash
docker-compose up -d
```

4. バックアップを復元:
```bash
# BACKUP=のあとにはバックアップファイル名から拡張子を除いた部分を指定
docker-compose exec gitlab gitlab-backup restore BACKUP=1732528314_2024_11_25_17.4.2
```

### 注意事項
- バックアップファイルは `./backups/` ディレクトリに保存されます
- バックアップには設定ファイルは含まれません。必要に応じて `/etc/gitlab` もバックアップしてください
- リストア時は、バックアップ時と同じバージョンのGitLabを使用することを推奨します

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
