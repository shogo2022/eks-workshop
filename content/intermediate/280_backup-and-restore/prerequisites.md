---
title: "Velero用のS3バケットとIAMロールの作成"
weight: 10
draft: false
---

<!--
#### Create an S3 bucket to backup cluster
-->
#### クラスタバックアップのためのS3バケット作成

<!--
Velero uses AWS S3 bucket to backup EKS cluster. It uploads a tarball of copied Kubernetes objects into S3 bucket. Let's create an S3 bucket to backup our EKS cluster.
-->
VeleroはEKSクラスタのバックアップにS3バケットを使います。コピーされたKubernetesのオブジェクトがtarballとしてs3バケットにアップロードされます。EKSクラスタのバックアップ用のS3バケットを作成しましょう。

<!--
We set the VELERO_BUCKET environment variable with the bucket name (*example: eksworkshop-backup-1586914410-22480*) created to make it easier to refer to the S3 Bucket later.
-->
あとで参照しやすいように、ここではVELERO_BUCKETという環境変数 (*例: eksworkshop-backup-1586914419-22480*) をセットしています。

{{% notice info %}}
<!--
If you are running this workshop in a **region other than us-east-1**, use the command below to create S3 bucket. Regions outside of us-east-1 require the appropriate LocationConstraint to be specified in order to create the bucket in the desired region.
-->
ワークショップを **us-east-1以外** で使っている場合は、S3バケットの作成に次のコマンドを使ってください。us-east-1以外のリージョンでバケットを作成するには、適切なLocationConstraintを指定する必要があります。

```
export VELERO_BUCKET=$(aws s3api create-bucket \
--bucket eksworkshop-backup-$(date +%s)-$RANDOM \
--region $AWS_REGION \
--create-bucket-configuration LocationConstraint=$AWS_REGION \
--| jq -r '.Location' \
--| cut -d'/' -f3 \
--| cut -d'.' -f1)
```
{{% /notice %}}

{{% notice info %}}
<!--
For **us-east-1**, use the command below to create S3 bucket. 
-->
**us-east-1** を使っていた場合は、次のコマンドでS3バケットを作成します。

```
export VELERO_BUCKET=$(aws s3api create-bucket \
--bucket eksworkshop-backup-$(date +%s)-$RANDOM \
--region $AWS_REGION \
--| jq -r '.Location' \
--| tr -d /)
```
{{% /notice %}}

<!--
Now, let’s save the VELERO_BUCKET environment variable into the bash_profile
-->
では、bash_profileにVELERO_BUCKET環境変数を保存しましょう
```
echo "export VELERO_BUCKET=${VELERO_BUCKET}" | tee -a ~/.bash_profile
```

<!--
#### Create an IAM role Velero:
-->
#### VeleroのAMロール作成

<!--
Create an IAM user for Velero:
-->
Velero用のIAMユーザ作成:

```
aws iam create-user --user-name velero
```

<!--
Attach policies to give velero the necessary permissions:
-->
Veleroに必要な権限ポリシーのアタッチ

```
cat > velero-policy.json <<EOF
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ec2:DescribeVolumes",
                "ec2:DescribeSnapshots",
                "ec2:CreateTags",
                "ec2:CreateVolume",
                "ec2:CreateSnapshot",
                "ec2:DeleteSnapshot"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:DeleteObject",
                "s3:PutObject",
                "s3:AbortMultipartUpload",
                "s3:ListMultipartUploadParts"
            ],
            "Resource": [
                "arn:aws:s3:::${VELERO_BUCKET}/*"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:ListBucket"
            ],
            "Resource": [
                "arn:aws:s3:::${VELERO_BUCKET}"
            ]
        }
    ]
}
EOF
```

<!--
Attach policy to velero IAM User
-->
Velero IAMユーザにポリシーのアタッチ
```
aws iam put-user-policy \
  --user-name velero \
  --policy-name velero \
  --policy-document file://velero-policy.json
```

<!--
Create an access key for the user:
-->
ユーザのアクセスキーの作成:
```
aws iam create-access-key --user-name velero > velero-access-key.json
```
<!--
Verify the access key created
-->
アクセスキーの確認
```
cat velero-access-key.json
```
<!--
The result should look like below. 
-->
結果は次の通り。
```
{
  "AccessKey": {
        "UserName": "velero",
        "Status": "Active",
        "CreateDate": "2020-03-18T22:24:41.576Z",
        "SecretAccessKey": <AWS_SECRET_ACCESS_KEY>,
        "AccessKeyId": <AWS_ACCESS_KEY_ID>
  }
}
```
<!--
Now, let’s set the VELERO_ACCESS_KEY_ID and VELERO_SECRET_ACCESS_KEY environment variables and save them to bash_profile.
-->
環境変数にVELERO_ACCESS_KEYとVELERO_SECRET_ACCESS_KEYをセットし、bash_profileに保存します。
```
export VELERO_ACCESS_KEY_ID=$(cat velero-access-key.json | jq -r '.AccessKey.AccessKeyId')
export VELERO_SECRET_ACCESS_KEY=$(cat velero-access-key.json | jq -r '.AccessKey.SecretAccessKey')
echo "export VELERO_ACCESS_KEY_ID=${VELERO_ACCESS_KEY_ID}" | tee -a ~/.bash_profile
echo "export VELERO_SECRET_ACCESS_KEY=${VELERO_SECRET_ACCESS_KEY}" | tee -a ~/.bash_profile
```

<!--
Create a credentials file (velero-credentials) specfic to velero user in your local directory (~/environment). We will need this file when we install velero on EKS
-->
ローカルディレクトリ(~/environment)に、veleroユーザ用のクレデンシャルファイル(velero-credentials)を作成します。これはEKSにVeleroをインストールするときに使われます。

```
cat > velero-credentials <<EOF
[default]
aws_access_key_id=$VELERO_ACCESS_KEY_ID
aws_secret_access_key=$VELERO_SECRET_ACCESS_KEY
EOF
```
