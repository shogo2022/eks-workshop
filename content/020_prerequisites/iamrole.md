---
title: "ワークスペース用のIAMロール作成"
chapter: false
weight: 16
---

<!--
1. Follow [this deep link to create an IAM role with Administrator access.](https://console.aws.amazon.com/iam/home#/roles$new?step=review&commonUseCase=EC2%2BEC2&selectedUseCase=EC2&policies=arn:aws:iam::aws:policy%2FAdministratorAccess)
1. Confirm that **AWS service** and **EC2** are selected, then click **Next** to view permissions.
1. Confirm that **AdministratorAccess** is checked, then click **Next: Tags** to assign tags.
1. Take the defaults, and click **Next: Review** to review.
1. Enter **eksworkshop-admin** for the Name, and click **Create role**.
![createrole](/images/createrole.png)
-->
1. こちらに沿って[管理者アクセスを持ったIAMロールを作成します。](https://console.aws.amazon.com/iam/home#/roles$new?step=review&commonUseCase=EC2%2BEC2&selectedUseCase=EC2&policies=arn:aws:iam::aws:policy%2FAdministratorAccess)
1. **AWS service** と **EC2** が選択されていることを確認し、 **Next** をクリックし権限を確認します。
1. **AdministratorAccess** がチェックされていることを確認し、 **Next: Tags** をクリックしタグを付けます。
1. デフォルトのままで **Next: Review** をクリックしレビューします。
1. **eksworkshop-admin** と名前をつけ、 **Create role** をクリックします。
![createrole](/images/createrole.png)