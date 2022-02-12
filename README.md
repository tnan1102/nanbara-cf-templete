# 設計
## S3設計
* 「nanbara-cf-templete」というバケットにテンプレートをアップロードする。
* システム単位でS3バケットにディレクトリを作成し、テンプレートをアップロードする。
* nanbara-cf-templeteのフォルダ構成は以下の通り
```
nanbara-cf-templete
├── cmn-vpc      # 共通のVPCテンプレート
├── cmn-sg       # 共通のセキュリティグループテンプレート
├── cmn-bastion  # 共通の踏み台インスタンスのテンプレート
├── system-A     # 以下、システム毎のテンプレート
├── system-B
└── system-C
```

## CFn tenplete設計
* VPCとSGは共通のものを利用する、その他は基本的にシステム毎に構築する。
* テンプレートはネスト構造にする。具体的には以下の通り
```
main.yml          # mainテンプレート
├── subnet.yml    # サブネットテンプレート
├── instance.yml  # インスタンステンプレート
├── iam.yml       # iamユーザテンプレート
└── s3.yml        # S3テンプレート
```

# 使い方
1. テンプレートを作成
2. S3にアップロード
```
cd nanbara-cf-templete
templete="{ディレクトリ名/ファイル名}"
bucket="nanbara-cf-templete/"

#S3にアップロード
aws s3 cp ${templete} s3://${bucket}${templete}
```
3. 構文チェック
```
#CloudFormationの構文チェック
aws cloudformation validate-template --template-url https://s3.ap-northeast-1.amazonaws.com/${bucket}${templete}

#テンプレートURLを確認
echo https://s3.ap-northeast-1.amazonaws.com/${bucket}${templete}
```
4. マネジメントコンソールからスタック作成
* スタック名はディレクトリ名とする