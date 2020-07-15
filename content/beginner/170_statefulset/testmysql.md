---
title: "MySQLのテスト"
date: 2018-08-07T08:30:11-07:00
weight: 25
---
<!--
You can use **mysql-client** to send some data to the leader, **mysql-0.mysql**
by following command.
-->
次のコマンドで **mysql-client** を使ってリーダー **mysql-0.mysql** にデータを送ることができます。
```sh
kubectl -n mysql run mysql-client --image=mysql:5.7 -i --rm --restart=Never --\
  mysql -h mysql-0.mysql <<EOF
CREATE DATABASE test;
CREATE TABLE test.messages (message VARCHAR(250));
INSERT INTO test.messages VALUES ('hello, from mysql-client');
EOF

```

<!--
Run the following to test followers (mysql-read) received the data.
-->
次を実行して、フォロワ(mysql-read)がデータを受け取ったことを確認します。
```
kubectl -n mysql run mysql-client --image=mysql:5.7 -it --rm --restart=Never --\
  mysql -h mysql-read -e "SELECT * FROM test.messages"
```
<!--
The output should look like this.
-->
このように表示されるはずです。
{{< output >}}
+--------------------------+
| message                  |
+--------------------------+
| hello, from mysql-client |
+--------------------------+
{{< /output >}}

<!--
To test load balancing across followers, run the following command.
-->
フォロワ間でのロードバランスをテストするには、次のコマンドを実行します。
```sh
kubectl -n mysql run mysql-client-loop --image=mysql:5.7 -i -t --rm --restart=Never --\
   bash -ic "while sleep 1; do mysql -h mysql-read -e 'SELECT @@server_id,NOW()'; done"
```

<!--
Each MySQL instance is assigned a unique identifier, and it can be retrieved using `@@server_id`. It will print the server id serving the request and the timestamp.
-->
それぞれのMySQLインスタンスは一意の識別子が割り当てられ、 `@@servr_id` で確認することができます。これはリクエストを返しているサーバのIDとタイムスタンプを表示しています。
{{< output >}}
+-------------+---------------------+
| @@server_id | NOW()               |
+-------------+---------------------+
|         101 | 2020-01-25 17:29:09 |
+-------------+---------------------+
+-------------+---------------------+
| @@server_id | NOW()               |
+-------------+---------------------+
|         100 | 2020-01-25 17:29:13 |
+-------------+---------------------+
+-------------+---------------------+
| @@server_id | NOW()               |
+-------------+---------------------+
|         102 | 2020-01-25 17:29:14 |
+-------------+---------------------+
+-------------+---------------------+
| @@server_id | NOW()               |
+-------------+---------------------+
|         101 | 2020-01-25 17:29:15 |
+-------------+---------------------+
+-------------+---------------------+
| @@server_id | NOW()               |
+-------------+---------------------+
|         101 | 2020-01-25 17:29:16 |
+-------------+---------------------+
{{< /output >}}

<!--
Leave this open in a separate window while you test failure in the next section.
-->
これは開いたままにしておき、次のセクションで障害のテストをします。