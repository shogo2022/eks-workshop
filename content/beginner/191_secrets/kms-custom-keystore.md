---
title: "AWS KMSとCustom Key Store"
date: 2019-04-09T00:00:00-03:00
weight: 5
pre: '<i class="fa fa-film" aria-hidden="true"></i>'
draft: false
---

<!--
#### Considerations for your AWS KMS CMK
-->
#### AWS KMS CMKでの検討事項

<!--
Before we get to the lab exercise, we wanted to take some time to discuss options for generating your AWS KMS CMK. AWS KMS provides you with two alternatives to store your CMK. Your security requirements may dictate which alternative is suitable for your workloads on Amazon EKS. 
-->
手を動かす前に、AWS KMS CMKを生成するにあたってのオプションについて話しましょう。AWS KMSではCMKを保存するのに2通りの方法があります。あなたのセキュリティ要件によって、適している方法を選んでください。

<!--
{{% notice info %}}
There is an [AWS Online Tech Talk on *Encrypting Secrets in Amazon EKS*](https://pages.awscloud.com/Encrypting-Secrets-in-Amazon-EKS_2020_0502-CON_OD.html?&trk=ep_card-el_a131L0000084iG3QAI&trkCampaign=NA-FY20-AWS-DIGMKT-WEBINAR-SERIES-May_2020_0502-CON&sc_channel=el&sc_campaign=pac_2018-2019_exlinks_ondemand_OTT_evergreen&sc_outcome=Product_Adoption_Campaigns&sc_geo=NAMER&sc_country=mult) that dives deep into this topic.
{{% /notice %}}
-->
{{% notice info %}}
このトピックについて深く知りたければ[AWS Online Tech Talk on *Encrypting Secrects in Amazon EKS*](https://pages.awscloud.com/Encrypting-Secrets-in-Amazon-EKS_2020_0502-CON_OD.html?&trk=ep_card-el_a131L0000084iG3QAI&trkCampaign=NA-FY20-AWS-DIGMKT-WEBINAR-SERIES-May_2020_0502-CON&sc_channel=el&sc_campaign=pac_2018-2019_exlinks_ondemand_OTT_evergreen&sc_outcome=Product_Adoption_Campaigns&sc_geo=NAMER&sc_country=mult)を参照してください。 
{{% /notice %}}
{{< youtube d21JrnszG7Y >}}
<!--

{{< youtube d21JrnszG7Y >}}

##### Custom Key Store (CMK stored within AWS CloudHSM)

For most users, the default AWS KMS key store, which is protected by FIPS 140-2 validated cryptographic modules, fulfills their security requirements.
-->
##### カスタムキーストア (AWS CloudHSM内のCMK)
ほとんどのユーザには、FIPS 140-2準拠の暗号化モジュールで保護されているデフォルトのAWSキーストアでセキュリティ要件は満たされます。

<!--
However, you might consider creating a custom key store if your organization has any of the following requirements:
-->
しかし、次の項目のうち一つでも当てはまるのであれば、カスタムキーストアの作成が必要かもしれません:

<!--
* The key material cannot be stored in a shared environment.
* The key material must be backed up in multiple AWS Regions.
* The key material must be subject to a secondary, independent audit path.
* The hardware security module (HSM) that generates and stores key material must be certified at [FIPS 140-2 Level 3](https://docs.aws.amazon.com/cloudhsm/latest/userguide/introduction.html).
-->
* キーを共有環境に保存してはいけない
* キーは複数のAWSリージョンにバックアップされていなければならない
* キーは分離され独立した監査下になければならない
* キーの生成と保存に使われるハードウェアセキュリティモジュール(HSM)は[FIPS 140-2 Level 3](https://docs.aws.amazon.com/cloudhsm/latest/userguide/introduction.html)準拠でなければならない

<!--
If any of these requirements apply to you, consider using AWS CloudHSM with AWS KMS to create a [custom key store](https://docs.aws.amazon.com/kms/latest/developerguide/custom-key-store-overview.html).
-->
これらのうちひとつでも当てはまるのであれば、AWS CloudHSM with AWS KMSを使って[custom key store](https://docs.aws.amazon.com/kms/latest/developerguide/custom-key-store-overview.html)を作成することを考えてください。

<!--
#### Challenge:
**What level of FIPS 140-2 cryptographic validation does the AWS KMS HSM hold?**
-->
#### チャレンジ:
**AWS KMS HSMは、どのレベルでFIPS 140-2暗号化に準拠しているでしょうか?**

<!--
{{%expand "Expand here to see the solution" %}}
The AWS KMS HSMs are validated at Level 2 overall. You can read more about that [here].(https://aws.amazon.com/blogs/security/aws-key-management-service-now-offers-fips-140-2-validated-cryptographic-modules-enabling-easier-adoption-of-the-service-for-regulated-workloads/)

FIPS 140-2 Level 2 validation is sufficient for many use cases, but check with your security and compliance teams to verify.
{{% /expand %}}
-->
{{%expand "Expand here to see the solution" %}}
AWS KMS HSMは全体としてはレベル2に準拠しています。詳細は[こちら](https://aws.amazon.com/blogs/security/aws-key-management-service-now-offers-fips-140-2-validated-cryptographic-modules-enabling-easier-adoption-of-the-service-for-regulated-workloads/)を参照してください。

多くのケースでFIPS 140-2 Level2準拠は十分ですが、自社のセキュリティおよびコンプライアンスチームと確認をしてください。
{{% /expand %}}

<!--
{{% notice info %}}
Keep in mind that the KMS Custom Key Store functionality makes use of a minimum of two AWS CloudHSM instances.
{{% /notice %}}
-->
{{% notice info %}}
KMS Custom Key Storeが機能するには最低2つのAWS CloudHSMインスタンスが必要です。
{{% /notice %}}

<!--
#### Cost

Aside from compliance considerations, your team will want to consider the cost of using this feature. For comparison, I will list the cost of using a CMK created with the default KMS functionality. Then, I will list of the cost of using a CMK created with the custom key store functionality.
-->
#### コスト
コンプライアンスでの検討事項の他に、費用についても触れます。比較のために、デフォルトのKMSで作成したCMKの費用リストとカスタムキーストアで作成したCMKの費用リストを記載します。

<!--
##### KMS Standard (Monthly Cost)
-->
##### KMS Standard (月額費用)

- 1 CMK = $1.00
- 90 requests = $0.00 (due to the free tier of 20,000 requests)
- **Total Cost = $1.00**

<!--
##### KMS Custom Key Store (Monthly Cost)
-->
##### KMS Custom Key Store (月額費用)

- 1 CMK = $1.00
- 90 requests = $0.00 (due to the free tier of 20,000 requests)
- 2 CloudHSM Instances = $2,380.80
- **Total Cost = $2,381.80**

<!--
Now that we have discussed AWS KMS support for custom key stores, let's move on to the exercise.
-->
ここではか明日タムキーストアのAWS KMSサポートについて話しました。さぁ、ハンズオンに移りましょう。