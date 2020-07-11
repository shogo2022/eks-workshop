---
title: "IAMグループの作成"
date: 2020-04-05T18:00:00-00:00
draft: false
weight: 21
---

<!--
We want to have different IAM users which will be added to specific IAM groups in order to have different rights in the kubernetes cluster.
-->
kubernetesクラスターに対して異なる権限を与えるために、異なるIAMユーザをそれぞれ別のIAMグループに加えたいと思います。

<!-->
We will define 3 groups:
-->
3つのグループを定義します:

<!-->
- **k8sAdmin** - users from this group will have admin rights on the kubernetes cluster
- **k8sDev** - users from this group will have full access only in the development namespace of the cluster
- **k8sInteg** - users from this group will have access to integration namespace. 
-->
- **k8sAdmin** - このグループのユーザはkubernetesクラスタに対して管理者権限を持つ
- **k8sDev** - このグループのユーザはクラスターの中のdevelopmentの名前空間にのみフルアクセス権限を持つ
- **k8sInteg** - このグループのユーザはintegrationの名前空間にアクセス権限を持つ

<!-->
> In fact, users from **k8sDev** and **k8sInteg** groups will only have access to namespaces where we will define kubernetes RBAC access for their associated kubernetes role.
We'll see this but first, let's creates the groups.
-->
**k8sDev**と**k8sInteg**グループのユーザは、後に定義するRBACで割り当てられた名前空間にのみアクセスが許されます。
これは後で実施するとして、まずはグループを作りましょう。

<!-->
#### Create k8sAdmin IAM Group
-->
#### k8sAdmin IAMグループの作成

<!-->
The **k8sAdmin** Group will be allowed to assume the **k8sAdmin** IAM Role.
-->
**k8sAdmin**グループは**k8sAdmin**のIAMロールを引き受け(assume)ることが許可されます。

<!-->
```
aws iam create-group --group-name k8sAdmin
```
Let's add a Policy on our group which will allow users from this group to assume our k8sAdmin Role:
-->
```
aws iam create-group --group-name k8sAdmin
```
このグループのユーザがk8sAdminロールを引き受けられるようにポリシーを追加しましょう:

```
ADMIN_GROUP_POLICY=$(echo -n '{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowAssumeOrganizationAccountRole",
      "Effect": "Allow",
      "Action": "sts:AssumeRole",
      "Resource": "arn:aws:iam::'; echo -n "$ACCOUNT_ID"; echo -n ':role/k8sAdmin"
    }
  ]
}')
echo ADMIN_GROUP_POLICY=$ADMIN_GROUP_POLICY

aws iam put-group-policy \
--group-name k8sAdmin \
--policy-name k8sAdmin-policy \
--policy-document "$ADMIN_GROUP_POLICY"
```

<!-->
#### Create k8sDev IAM Group
-->
#### k8sDev IAMグループの作成

<!-->
The **k8sDev** Group will be allowed to assume the **k8sDev** IAM Role.
-->
**k8sDev**グループは**k8sDev**IAMロールの引き受け(assume)が許可されます。

<!-->
```
aws iam create-group --group-name k8sDev
```
Let's add a Policy on our group which will allow users from this group to assume our k8sDev Role:
-->
```
aws iam create-group --group-name k8sDev
```
このグループのユーザがk8sDevロールを引き受けられるようにポリシーを追加しましょう:

```
DEV_GROUP_POLICY=$(echo -n '{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowAssumeOrganizationAccountRole",
      "Effect": "Allow",
      "Action": "sts:AssumeRole",
      "Resource": "arn:aws:iam::'; echo -n "$ACCOUNT_ID"; echo -n ':role/k8sDev"
    }
  ]
}')
echo DEV_GROUP_POLICY=$DEV_GROUP_POLICY

aws iam put-group-policy \
--group-name k8sDev \
--policy-name k8sDev-policy \
--policy-document "$DEV_GROUP_POLICY"
```

<!-->
#### Create k8sInteg IAM Group
-->
#### k8sInteg IAMグループの作成

<!-->
```
aws iam create-group --group-name k8sInteg
```
Let's add a Policy on our group which will allow users from this group to assume our k8sInteg Role:
-->
```
aws iam create-group --group-name k8sInteg
```
このグループのユーザがk8sIntegロールを引き受けられるようにポリシーを追加しましょう:


```
INTEG_GROUP_POLICY=$(echo -n '{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowAssumeOrganizationAccountRole",
      "Effect": "Allow",
      "Action": "sts:AssumeRole",
      "Resource": "arn:aws:iam::'; echo -n "$ACCOUNT_ID"; echo -n ':role/k8sInteg"
    }
  ]
}')
echo INTEG_GROUP_POLICY=$INTEG_GROUP_POLICY

aws iam put-group-policy \
--group-name k8sInteg \
--policy-name k8sInteg-policy \
--policy-document "$INTEG_GROUP_POLICY"
```

<!-->
You now should have your 3 groups
-->
これで3つのグループがあるはずです

```
aws iam list-groups
```

{{< output >}}
{
    "Groups": [
        {
            "Path": "/",
            "GroupName": "k8sAdmin",
            "GroupId": "AGPAZRV3OHPJZGT2JKVDV",
            "Arn": "arn:aws:iam::xxxxxxxxxx:group/k8sAdmin",
            "CreateDate": "2020-04-07T13:32:52Z"
        },
        {
            "Path": "/",
            "GroupName": "k8sDev",
            "GroupId": "AGPAZRV3OHPJUOBR375KI",
            "Arn": "arn:aws:iam::xxxxxxxxxx:group/k8sDev",
            "CreateDate": "2020-04-07T13:33:15Z"
        },
        {
            "Path": "/",
            "GroupName": "k8sInteg",
            "GroupId": "AGPAZRV3OHPJR6GM6PFDG",
            "Arn": "arn:aws:iam::xxxxxxxxxx:group/k8sInteg",
            "CreateDate": "2020-04-07T13:33:25Z"
        }
    ]
}
{{< /output >}}