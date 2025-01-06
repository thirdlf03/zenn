---
published: true
cssclasses:
  - zenn
type: tech
emoji: 🔥
title: Hono + Cloudflare D1 + Cloudflare Workersを触ってみる。
topics: 
date: 2025-01-06
AutoNoteMover: disable
url: https://zenn.dev/thirdlf/articles/07-zenn-hono-cloudflared1
tags:
  - hono
  - cloudflare
  - 初心者
aliases:
---
# 概要
コストの問題で元々、AWSのRDS + Lambdaで構築していたREST APIをCloudflareのD1 + workersに移植しようと思ったので触ってみました。

リポ
https://github.com/thirdlf03/hono-d1-workers

# 技術
Cloudflare Workers使うならHonoが良さそうだったのでHonoを選択

# やってみる

## 環境構築
bunを使います
```bash
bun create hono@latest tutorial --template=cloudflare-workers
cd tutorial
bunx wrangler dev
```

## Cloudflare D1と繋ぐ

### Database作成

まず、databaseの作成

```bash
bunx wrangler d1 create tutorial
```

するとコンソールに作成したdbの情報が出力されるので、wrangler.tomlに書く

```
[[d1_databases]]
binding = "DB"
database_name = "tutorial"
database_id = id
```


wrangler.toml
```toml
name = "tutorial"  
main = "src/index.ts"  
compatibility_date = "2025-01-06"  
  
# compatibility_flags = [ "nodejs_compat" ]  
  
# [vars]  
# MY_VAR = "my-variable"  
  
# [[kv_namespaces]]  
# binding = "MY_KV_NAMESPACE"  
# id = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"  
  
# [[r2_buckets]]  
# binding = "MY_BUCKET"  
# bucket_name = "my-bucket"  
  
[[d1_databases]]
binding = "DB"
database_name = "tutorial"
database_id = id
  
# [ai]  
# binding = "AI"  
  
# [observability]  
# enabled = true  
# head_sampling_rate = 1

```

### スキーマ定義
schema.sqlをファイルを作り、スキーマ定義する

```bash
touch schema.sql
```

schema.sql
```sql
DROP TABLE IF EXISTS Users;
CREATE TABLE IF NOT EXISTS Users (UserID INTEGER PRIMARY KEY, Name TEXT);
INSERT INTO Users (UserID, Name) VALUES (1, "hoge");
```

これをローカルで反映させる。
```bash
bunx wrangler d1 execute tutorial --local --file=./schema.sql
```

データ確認
```bash
bunx wrangler d1 execute tutorial --local --command="SELECT * FROM Users"
```

![](/images/article-7/07-sql.png)

## Honoで呼び出す

index.ts
```ts
import { Hono } from "hono";  
  
type Bindings = {  
  DB: D1Database;  
};  
  
const app = new Hono<{ Bindings: Bindings }>();  
  
app.get("/", (c) => {  
  return c.json({message: "Hello, World!"});  
});  
  
app.get("/users/:id", async (c) => {  
  const userID = c.req.param("id");  
  console.log(userID);  
  try {  
    let { results } = await c.env.DB.prepare(  
        "SELECT * FROM Users WHERE UserID = ?",  
    )  
        .bind(userID)  
        .all();  
    return c.json(results);  
  } catch (e: any) {  
    return c.json({ err: e.message }, 500);  
  }  
});  
  
  
export default app;
```


これでおk

この状態で
http://localhost:8787/users/1
を叩くとテーブル情報が返ってくる

## デプロイ
一旦devを閉じてリモートにもテーブル作成
```bash
bunx wrangler d1 execute tutorial --remote --file=./schema.sql
```

確認してみる
```bash
bunx wrangler d1 execute tutorial --remote --command="SELECT * FROM Users"
```

デプロイ
```bash
bunx wrangler deploy
```

ターミナルにデプロイ先のurl出るので確認

# 感想
お手軽に作れて便利。今後、何かREST API作るならこれでいいなーって感じ。