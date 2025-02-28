---
published: false
cssclasses:
  - zenn
type: tech
emoji: 🔰
title: ハンズオン形式で学ぶバックエンド (FastAPI) 入門
topics: 
date: 2025-02-28
AutoNoteMover: disable
url: https://zenn.dev/thirdlf/articles/17-zenn-backend-fastapi-begin
tags: 
aliases:
---
# 注意点
この記事はバックエンド初心者向けに書いています。  
理解しやすいよう、一部説明を簡略化している点をご了承ください。

# はじめに
この記事では、そもそもバックエンドとはどんなようなものなのかというところから始まり、基本的な機能を持ったバックエンドをハンズオン形式で作りながら学ぶことができるようになっています。

使う言語はPythonなので、プログラミング初心者も臆せず、ぜひ挑戦してみてください！

# バックエンドとは
おそらく皆さんが一度は触れたことがあるであろう HTML、CSS、JavaScript などは、フロントエンドと呼ばれる領域になります。  
フロントエンドとは、WebサービスやWebアプリの画面部分など、**ユーザーが見る部分**のことを指します。  
一方、バックエンドはログイン処理やデータの管理・処理を行うなど、**ユーザーからは見えない部分**のことを指します。

本格的なWebアプリを作成する場合は、フロントエンドとバックエンドを組み合わせて開発するのが一般的です。

# どんな場面でバックエンドが必要なのか
主に、データを保存する必要がある場合に求められます。データベースというものにデータを保存していきます。

例えば、Todoアプリを作ったとします。バックエンドがない状態だと、作成したタスクが保存されていないので、ページをリロードすると消えてしまいます。
もちろん、バックエンドがなくても保存する方法はありますが、データを保存するとなるとデータベースというものを使うのが一般的です。

# データベースとは
説明すると長くなるのでざっくりとしか説明しませんが、データベースとは、大量のデータを効率よく保存・管理するためのシステムです。

# 実際に作ってみる
今回は、FastAPIというPythonのライブラリを使って、Todoアプリのデータを管理するバックエンドを作成していきます。

もし、詰まった場合は
https://github.com/thirdlf03/fastapi-handson/tree/main/samples

サンプルコードを参照してください。

## 環境の準備
GitHubのCodespacesを使っていくので、もしGitHubに登録していない場合は登録してください。
https://github.com/

ここにアクセスしてください。
https://github.com/thirdlf03/fastapi-handson

アクセスすると、Codeという緑のボタンがあるので
![](/images/article-17/スクリーンショット%202025-03-01%201.26.43.png)

真ん中のCodespacesを選択し、Create codespace on mainをクリックしてください。
![](/images/article-17/スクリーンショット%202025-03-01%201.26.59.png)

こんな画面に飛んだらOKです！
![](/images/article-17/スクリーンショット%202025-03-01%201.29.29.png)

## 必要なファイルの作成
必要なファイルの作成をしていきます。

ターミナルで以下のコマンドを実行してください。
```bash
touch main.py todo.db db.py init.py models.py schemas.py
```

![](/images/article-17/スクリーンショット%202025-03-01%201.35.02.png)

するとエクスプローラーのところがこのようになるはずです。
こうなってたらコマンド実行できてます！

![](/images/article-17/スクリーンショット%202025-03-01%201.33.23.png)


## Step1 とりあえずFastAPIを触ってみる

### 仮想環境の用意
まず、pythonの仮想環境を用意していきます。
今回は、uvを使って仮想環境を作成します。

ターミナルで以下のコマンドを実行していきます。

仮想環境作成
```bash
uv venv --python=3.12
```

![](/images/article-17/スクリーンショット%202025-02-28%2023.32.25.png)

仮想環境有効化
```bash
source .venv/bin/activate
```

![](/images/article-17/スクリーンショット%202025-02-28%2023.32.41.png)

activateしたときに、右下にこのような表示が出るのではいを押してください
![](/images/article-17/スクリーンショット%202025-02-28%2023.51.58.png)

もし、設定し忘れた場合は

画面の下部にインタープリターの選択というボタンがあるのでクリックしてください。
![](/images/article-17/スクリーンショット%202025-03-01%201.50.04.png)

すると、画面上部にインタープリターの選択というポップアップが出て、一番したにおすすめというのが出るのでこれをクリックしてください。![](/images/article-17/スクリーンショット%202025-03-01%201.52.17.png)

FastAPIのインストール
```bash
uv pip install fastapi
```

![](/images/article-17/スクリーンショット%202025-02-28%2023.52.39.png)

### コードを書いていく
実際にmain.pyにコードを書いていきましょう。

main.py
```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
def hello():
	return {"message": "Hello World"}

```

Hello Worldの準備ができたので、動かしてみたいんですがこのままだと実行できないのでuvicornというものを入れていきます

### Uvicorn インストール
uvicornの説明は詳しくしません。FastAPIを動かすのに必要なものと思ってもらってokです

ターミナルで以下のコマンドを実行してください。

uvicornインストール
```bash
uv pip install uvicorn
```

![](/images/article-17/スクリーンショット%202025-03-01%200.04.47.png)

uvicornで起動してみる
```bash
uvicorn "main:app"
```

![](スクリーンショット%202025-03-01%200.05.57.png)

uvicornで起動すると、右下の方にブラウザーで開くというのが出るのでクリックしてください。

![](/images/article-17/スクリーンショット%202025-03-01%201.55.02.png)

すると、ブラウザーが開き以下のように表示されてるはずです。

![](/images/article-17/スクリーンショット%202025-03-01%201.55.56.png)
動作が確認できたら、Codespacesに戻りましょう。
uvicron "main:app"と入力したらところをクリックした後、

Windowsの方は
CTRL + C

Macの方は、
control + C

で動作を中断してください。

そうすると、またコマンドが入力できるようになっているはずのでそれを確認してください。
![](/images/article-17/スクリーンショット%202025-03-01%202.08.53.png)

## Step2 データベースとFastAPIを繋げてみる
前述した通り、バックエンドはデータベースと繋げてデータを保存することが多いです。
なので、実際にデータベースとFastAPIを繋げてみましょう。

今回は、Sqliteをデータベースとして選びました。
### sqlalchemyのインストール
sqlalchemyとは、FastAPIとデータベースを繋げるためによく使われるライブラリです。
ORMと呼ばれるものです。

インストールする
```bash
uv pip install sqlalchemy
```

![](スクリーンショット%202025-03-01%200.11.13.png)

### データベースを使えるようにする

db.pyとinit.pyに以下のようにコードを書いてください。

db.py
```python
from sqlalchemy import create_engine
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker

engine = create_engine("sqlite:///todo.db")
SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)
Base = declarative_base()
![](スクリーンショット%202025-03-01%202.10.48.png)
def get_db():
	db = SessionLocal()
	try:
		yield db
	finally:
		db.close()
```

init.py
```python
import sqlite3

sql_statements = [
	"""CREATE TABLE IF NOT EXISTS todos (
		id INTEGER PRIMARY KEY,
		title text NOT NULL,
		content text NOT NULL
	);""",

	"""INSERT INTO todos (title, content) 
			VALUES ("First Task", "Hello World")
			"""
]

try:
	with sqlite3.connect('todo.db') as conn:
		cursor = conn.cursor()
		
	for statement in sql_statements:
		cursor.execute(statement)
		
	conn.commit()
	
	print("Table Create")
except sqlite3.OperationalError as e:
	print("Failed to create tables:", e)
```

db.pyはFastAPIでデータベースを呼び出すために必要な関数 get_db()を定義しています。
init.pyでは、データベースを使うのにテーブルというものが必要なので、テーブルを作成するコードを書いています。

コードを書き終わったら下記のコマンドを実行してください。
```bash
python init.py
```

![](/images/article-17/スクリーンショット%202025-03-01%202.10.48.png)

### モデルを作成
sqlalchemyを使っていくのに、必要なものがあるのでmodels.pyに用意していきます。

models.py
```python
from sqlalchemy import Column, Text, Integer
from sqlalchemy.ext.declarative import declarative_base

Base = declarative_base()

class Todo(Base):
	__tablename__ = 'todos'
	
	id = Column(Integer, primary_key=True, autoincrement=True)
	title = Column(Text, nullable=False)
	content = Column(Text, nullable=False)
```

### 実際にデータベースを使ってみる
main.pyに以下のようにコードを書いてみましょう。

main.py
```python
from fastapi import FastAPI, Depends
from sqlalchemy.orm import Session
from db import get_db
from models import Todo

app = FastAPI()

@app.get("/")
def hello():
	return {"message": "Hello World"}
	
@app.get("/todos")
def get_todos(db: Session = Depends(get_db)):
	return db.query(Todo).all()
```

コード入力後、再度FastAPIを起動する必要があるのですが
```bash
uvicorn "main:app"
```

毎回このコマンド打つのは面倒ですよね
そこで便利なコマンドがあります。
それが、
```bash
uvicorn "main:app" --reload
```

--reloadというものです。
hotreloadといい、ファイルの変更を監視してくれて更新があった場合勝手にuvicornが再起動してくれます。

これを使うことで更新のたびに、CTRL + Cで中断してuvicornを起動するということをしなくて済むようになります。


話を戻して、入力したコードが動いてるかブラウザーで確認していきましょう。

URLの後ろに/todosとつけてみましょう。
![](/images/article-17/スクリーンショット%202025-03-01%202.19.16.png)


すると、このように表示されるはずです
![](/images/article-17/スクリーンショット%202025-03-01%202.20.56.png)

ただ、これ毎回/todos入力するのは面倒臭いですよね。
そこで、FastAPIには便利なものが用意されてます。
### Swagger 

試しにURLの後ろに/docsとつけてみてください。
![](/images/article-17/スクリーンショット%202025-03-01%202.24.01.png)

すると、このような画面が現れます。
![](/images/article-17/スクリーンショット%202025-03-01%200.09.37.png)
試しに / のところをクリックして、Try it outを押すと

![](スクリーンショット%202025-03-01%202.26.37.png)
このように結果が返ってきます。

今後、これを使って動作確認をしていきます。
もちろん、--reloadをつけていたらこのページをリロードするだけで簡単に動作確認できます。


Step3に入る前に、API、CRUDについて説明していきます。
## APIとは
APIとは、フロントエンドとバックエンドを通信するときに使うものです。
通信する時に、HTTPプロトコルというものを使うんですが、それにはざっくり4のメゾットがあいます。

- GET
- POST
- PUT
- DELETE
の4つです。

それぞれ
GET データの取得
POST データの保存
PUT データの更新
DELETE データの削除

で使われています。

FastAPIでは、
@app.
の後ろにgetやpost
()
の中にそれを使う時の名前を定義しています。

例 )
@app.get("/todos")

これはGETメゾットを使って、/todosという名前に接続できるようにするという意味です。

### CRUD
データベースを操作するときに、大まかに4つ操作があります。

それが、
- CREATE
- READ
- UPDATE
- DELETE

です。

それぞれ、
- CREATE データを保存
- READ データを読み取る
- UPDATE データを更新
- DELETE データを削除

この基本的な操作のことを頭文字を取ってCRUDと呼ばれています。

ここで、感がいい方は気づいているかもしれませんが
- GET = READ
- POST = CREATE
- PUT = UPDATE
- DELETE = DELETE

と紐づいています。

この後の実装で意識してみてください！
## Step3 Postを実装する
実際にデータを保存できるようにしていきます。

その前に、この後使うのでschemaというものを定義しておきます

schemas.py
```python
from pydantic import BaseModel

class TodoCreate(BaseModel):
	title: str
	content: str
```

実際にpostを定義してみましょう。

前のStepで実装したものとは違い、@app.のあとがpostになっていますね

main.py
```python
from fastapi import FastAPI, Depends
from sqlalchemy.orm import Session
from db import get_db
from models import Todo
from schemas import TodoCreate

app = FastAPI()

@app.get("/")
def hello():
	return {"message": "Hello World"}
	
@app.get("/todos")
def get_todos(db: Session = Depends(get_db)):
	return db.query(Todo).all()

@app.post("/todos")
def create_todo(todo: TodoCreate, db: Session = Depends(get_db)):
	new_todo = Todo(title = todo.title, content = todo.content)
	db.add(new_todo)
	db.commit()
	return db.query(Todo).all()
```

コードを書き終わったら、動作確認してみましょう！

POST /todosをクリックした後、 Try it outを押してみましょう。
すると、白いところが入力できるようになっているので好きな文字を入力してみましょう

![](/images/article-17/スクリーンショット%202025-03-01%203.05.45.png)
Executeを押すと
![](/images/article-17/スクリーンショット%202025-03-01%200.54.56.png)

データが追加されてますね！

## Step4 Putを実装する
Putを実装していきます。

まず、schemaを定義します。

schemas.py
```python
from pydantic import BaseModel
from typing import Optional

class TodoCreate(BaseModel):
	title: str
	content: str

class TodoUpdate(BaseModel):
	title: Optional[str] = None
	content: Optional[str] = None
```

その後、putを実装していきます。
![](/images/article-17/スクリーンショット%202025-03-01%201.05.10.png)
main.py
```python
from fastapi import FastAPI, Depends
from sqlalchemy.orm import Session
from db import get_db
from models import Todo
from schemas import TodoCreate, TodoUpdate

app = FastAPI()

@app.get("/")
def hello():
	return {"message": "Hello World"}
	
@app.get("/todos")
def get_todos(db: Session = Depends(get_db)):
	return db.query(Todo).all()

@app.post("/todos")
def create_todo(todo: TodoCreate, db: Session = Depends(get_db)):
	new_todo = Todo(title = todo.title, content = todo.content)
	db.add(new_todo)
	db.commit()
	return db.query(Todo).all()
	
@app.put("/todos/{todo_id}")
def update_todo(todo_id: int, todo: TodoUpdate, db: Session = Depends(get_db)):
	target_todo = db.query(Todo).filter(Todo.id == todo_id).first()
	target_todo.title = todo.title
	target_todo.content = todo.content
	return db.query(Todo).all()
```

コードを書き終わったら、確認しにいきましょう。
todo_idに更新したいidをした後、更新内容を書きましょう。

![](/images/article-17/スクリーンショット%202025-03-01%201.04.37.png)

すると、このように内容が変わっているのが確認できると思います。
![](/images/article-17/スクリーンショット%202025-03-01%201.04.48.png)

しかし、今のままだと欠陥があります。
このようにtitleかcontentどっちかだけ更新しようとすると

![](/images/article-17/スクリーンショット%202025-03-01%201.05.10.png)

contentにnullが入ってしまいましたね。
![](/images/article-17/スクリーンショット%202025-03-01%201.05.17.png)

これを直していきましょう！

main.py
```python
from fastapi import FastAPI, Depends
from sqlalchemy.orm import Session
from db import get_db
from models import Todo
from schemas import TodoCreate, TodoUpdate

app = FastAPI()

@app.get("/")
def hello():
	return {"message": "Hello World"}
	
@app.get("/todos")
def get_todos(db: Session = Depends(get_db)):
	return db.query(Todo).all()

@app.post("/todos")
def create_todo(todo: TodoCreate, db: Session = Depends(get_db)):
	new_todo = Todo(title = todo.title, content = todo.content)
	db.add(new_todo)
	db.commit()
	return db.query(Todo).all()
	
@app.put("/todos/{todo_id}")
def update_todo(todo_id: int, todo: TodoUpdate, db: Session = Depends(get_db)):
	target_todo = db.query(Todo).filter(Todo.id == todo_id).first()
	if todo.title != None:
		target_todo.title = todo.title

	if todo.content != None:
		target_todo.content = todo.content
	
	db.commit()
	return db.query(Todo).all()
```

やってることは簡単で、入力されたデータが空じゃない時に更新するようにしました。

試してみましょう
contentを消した状態で、excuteしてみると
![](/images/article-17/スクリーンショット%202025-03-01%204.00.41.png)

片方だけ更新されるようになりましたね
![](/images/article-17/スクリーンショット%202025-03-01%201.08.03.png)

Step5に入る前に、queryとbodyについて説明します。

まず、PostとPutの違いをみてください
![](/images/article-17/スクリーンショット%202025-03-01%203.27.13.png)

![](/images/article-17/スクリーンショット%202025-03-01%203.26.58.png)

Post は　/todos
Put は　/todos/1
となっていますね

この1の部分はクエリと呼ばれるものになります。

### query
今回のように、何かを指定するみたいな時に使います

実装例)
@app.put("/todos/{todo_id}")

### body
queryは/todos/の中に含まれてますが、bodyは含まれてませんよね？
このように、公に公開したくないデータをバックエンドに送る時はbodyを使います。

実装例)
schemaで定義したもの

```python
from pydantic import BaseModel

class TodoCreate(BaseModel):
	title: str
	content: str
```

これを関数の引数に指定することでbody扱いになります。

@app.post("/todos")
def create_todo(todo: TodoCreate, db: Session = Depends(get_db)):



## Step5 Deleteを実装してみる


main.py
```python

from fastapi import FastAPI, Depends
from sqlalchemy.orm import Session
from db import get_db
from models import Todo
from schemas import TodoCreate, TodoUpdate

app = FastAPI()

@app.get("/")
def hello():
	return {"message": "Hello World"}
	
@app.get("/todos")
def get_todos(db: Session = Depends(get_db)):
	return db.query(Todo).all()

@app.post("/todos")
def create_todo(todo: TodoCreate, db: Session = Depends(get_db)):
	new_todo = Todo(title = todo.title, content = todo.content)
	db.add(new_todo)
	db.commit()
	return db.query(Todo).all()
	
@app.put("/todos/{todo_id}")
def update_todo(todo_id: int, todo: TodoUpdate, db: Session = Depends(get_db)):
	target_todo = db.query(Todo).filter(Todo.id == todo_id).first()
	if todo.title != None:
		target_todo.title = todo.title

	if todo.content != None:
		target_todo.content = todo.content
	
	db.commit()
	return db.query(Todo).all()
	
@app.delete("/todos/{todo_id}")
def delete_todo(todo_id: int, db: Session = Depends(get_db)):
	target_todo = db.query(Todo).filter(Todo.id == todo_id).first()
	db.delete(target_todo)
	db.commit()
	return db.query(Todo).all()
```

実際に使ってみましょう

![](/images/article-17/スクリーンショット%202025-03-01%201.15.20.png)
![](/images/article-17/スクリーンショット%202025-03-01%201.15.15.png)

削除されてますね

# 最後に
お疲れ様です

ここまで実装してきたものを改良していくだけで、基本的なバックエンドを作成できるようになっています。

ただ、あくまで基本的なものなのでさらに発展したものを勉強したい方は以下のものがおすすめです

- https://fastapi.tiangolo.com/ja/learn/
- https://zenn.dev/sh0nk/books/537bb028709ab9

質問や要望あれば気軽にコメントしてください！