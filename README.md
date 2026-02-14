# 概要

お問い合わせフォーム用のサーバーレス API

## 構成

- API Gateway (REST) + Lambda + DynamoDB
- tfstateファイルは、S3リモートバックエンドへ設定
<img width="1162" height="624" alt="terraform-handson (1)" src="https://github.com/user-attachments/assets/c281e5b0-1336-4058-80d0-43617eae16fb" />


## セットアップ

```bash
# 1. terraform.tfvarsを作成
cp tf/terraform.tfvars.example tf/terraform.tfvars

# 2. デプロイ
cd tf/
terraform init
terraform apply
```

## 使用方法

```bash
# API URL取得
terraform output api_invoke_url

# テスト実行
curl -X POST $(terraform output -raw api_invoke_url) \
  -H "Content-Type: application/json" \
  -d '{
    "mailAddress": "test@example.com",
    "userName": "テストユーザー",
    "reviewText": "テスト"
  }'
```

## 設定変数

| 変数 | デフォルト |
|------|-----------|
| project_name | inquiry |
| environment | dev |
| region | ap-northeast-1 |


## .gitugnore内のファイルについて
- **.tfstate**、**.tfstate.**：Terraform の状態管理ファイル<br>
リソース構成の詳細や秘密情報（平文）が含まれるため、セキュリティの観点から除外。本プロジェクトでは S3 リモートバックエンドを使用して共有

- **.terraform/**：Terraform 作業ディレクトリ<br>
terraform init 時にダウンロードされるプロバイダー等のバイナリ。環境依存であり、容量も大きいため除外

- **.terraform.lock.hcl**：依存関係ロックファイル<br>
使用するプロバイダーのバージョンを固定するものだが、個人の実行環境に合わせて再生成させるため、本構成では管理対象外

- **terraform.tfvars**：機密情報の定義ファイル<br>
本番環境のドメイン名や API キー、パスワード等の「生の値」を記述するファイルです。流出を防ぐため必ず除外

- **.zip**：デプロイ用パッケージ<br>
Lambda へアップロードするために生成したバイナリ。Git には「コード（ソース）」を保存すべきであり、成果物は管理不要なため除外
