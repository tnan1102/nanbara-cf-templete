# 設計
## S3設計
* 「nanbara-cf-templete」というバケットにテンプレートをアップロードする。
* 共通のリソースはcmnディレクトリにテンプレートを置く。
* システム単位でS3バケットにディレクトリを作成し、テンプレートを置く。
* nanbara-cf-templeteのフォルダ構成は以下の通り。
```
nanbara-cf-templete
├── cmn       # 共通環境テンプレート
├── system-A  # 以下、システム毎のテンプレート
├── system-B
└── system-C
```

## CFn tenplete設計
* VPC,Subnet,SG,iamは共通環境テンプレートで管理する。
* テンプレートはネスト構造にする。
```
nanbara-cf-templete
└── cmn  # 共通環境テンプレート
    ├── main.yml        # mainテンプレート
    ├── cmn-vpc.yml     # VPC
    ├── cmn-public-subnet.yml   # パブリックサブネット
    ├── cmn-private-subnet.yml  # プライベートサブネット
    ├── cmn-sg.yml      # セキュリティグループ
    ├── cmn-iam.yml     # IAM
    └── cmn-bastion.yml # 踏み台サーバ
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