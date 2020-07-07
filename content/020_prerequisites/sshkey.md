---
title: "SSH鍵の作成"
chapter: false
weight: 31
---

<!--
{{% notice info %}}
Starting from here, when you see command to be entered such as below, you will enter these commands into Cloud9 IDE. You can use the **Copy to clipboard** feature (right hand upper corner) to simply copy and paste into Cloud9. In order to paste, you can use Ctrl + V for Windows or Command + V for Mac.
{{% /notice %}}
-->
{{% notice info %}}
ここから、以下のように表示されているときはCloud9のIDEに入力してください。コピーやペーストをするときには、(右上の)**クリップボードにコピー** が使用できます。ペーストをするには、WindowsではCtrl + V、もしくはMacではCommand + Vを使用してください。
{{% /notice %}}

<!--
Please run this command to generate SSH Key in Cloud9. This key will be used on the worker node instances to allow ssh access if necessary.
-->
このコマンドをCloud9で実行してSSH鍵を作成してください。この鍵は必要に応じてワーカーノードインスタンスにSSH接続をするときに使用されます。

```bash
ssh-keygen
```

<!--
{{% notice tip %}}
Press `enter` 3 times to take the default choices
{{% /notice %}}
-->
{{% notice tip %}}
デフォルトのまま`エンター`を3回押します
{{% /notice %}}

<!--
Upload the public key to your EC2 region:
-->
パブリックキーをEC2リージョンにアップロードします:

```bash
aws ec2 import-key-pair --key-name "eksworkshop" --public-key-material file://~/.ssh/id_rsa.pub
```

<!--
If you got an error similar to `An error occurred (InvalidKey.Format) when calling the ImportKeyPair operation: Key is not in valid OpenSSH public key format` then you can try this command instead:
-->
`An error occurred (InvalidKey.Format) when calling the ImportKeyPair operation: Key is not in valid OpenSSH public key format`のようなエラーが出たら、代わりにこのコマンドを実行します:

```bash
aws ec2 import-key-pair --key-name "eksworkshop" --public-key-material fileb://~/.ssh/id_rsa.pub
```