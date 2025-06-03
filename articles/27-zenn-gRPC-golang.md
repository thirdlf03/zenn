---
id: 27-zenn-gRPC-golang
aliases: 
tags: 
autonotemover: disable
cssclasses:
  - zenn
date: 2025-06-03
emoji: 🐀
published: false
title: Connect-goとBufを使って、GoでgRPC入門してみよう
type: tech
url: https://zenn.dev/thirdlf/articles/27-zenn-gRPC-golang
---
# この記事の目的
タイトル通り、gRPC入門してみようという趣旨の記事です。
ただgRPCを学ぶだけでは味気ないので、開発体験をよくしてくれるConnectやBufを使っていきます。

# 対象読者
Goの基本的な書き方を知っている方
gRPC入門者

# そもそもgRPCとは?
[gRPC](http://www.grpc.io/) は、Google が開発した高性能で汎用的なオープンソース RPC フレームワークです。gRPC を使用すると、クライアント アプリケーションは、別のマシンのサーバー アプリケーション上のメソッドを、ローカル オブジェクトと同様に直接呼び出すことができます。これにより、分散アプリケーションや分散サービスを簡単に作成できます。[^1]

[^1]https://cloud.google.com/api-gateway/docs/grpc-overview?hl=ja

## 使うメリット

### コードの自動生成
Protocol Buffers（protoファイル）でAPIを定義すると、各言語に対応したクライアントやサーバーサイドのコードを自動生成してくれる。

### RESTよりも高速
HTTP/2を使用し、バイナリプロトコルとして Protocol Buffersするためデータのシリアライズとデシリアライズが高速

### 型チェック
Protocol Buffersのおかげで、型チェックできる。

## 使うデメリット
### RESTよりも実装が複雑


詳しくはこちらの本が参考になります。
https://zenn.dev/hsaki/books/golang-grpc-starting

# 早速実装してみよう
## 環境構築

connectのgetting-startedやってみる。
https://connectrpc.com/docs/go/getting-started

まずはディレクトリ作成
```sh
mkdir connect-go-example
cd connect-go-example
```

init
```sh
go mod init example
```

install
```sh
go install github.com/bufbuild/buf/cmd/buf@latest
go install github.com/fullstorydev/grpcurl/cmd/grpcurl@latest
go install google.golang.org/protobuf/cmd/protoc-gen-go@latest
go install connectrpc.com/connect/cmd/protoc-gen-connect-go@latest
go get golang.org/x/net/http2  
go get connectrpc.com/connect
```

パスを通してあげる必要があります
```sh
[ -n "$(go env GOBIN)" ] && export PATH="$(go env GOBIN):${PATH}"
[ -n "$(go env GOPATH)" ] && export PATH="$(go env GOPATH)/bin:${PATH}"
```

これで、必要な環境は整いました。

## サービス定義
protoファイルに、サービスを定義していきます。

protoファイル作成
```sh
mkdir -p greet/v1
touch greet/v1/greet.proto
```


```proto:greet.proto
syntax = "proto3";

package greet.v1;

option go_package = "example/gen/greet/v1;greetv1";

message GreetRequest {
  string name = 1;
}

message GreetResponse {
  string greeting = 1;
}

service GreetService {
  rpc Greet(GreetRequest) returns (GreetResponse) {}
}
```

書き方

まず、バージョン定義します
```proto:greet.proto
syntax = "proto3";
```

生成されたパッケージが名前衝突しないように、packageにpackage名を指定できる

```
package greet.v1;
```

optionでコードが生成される場所を指定できます
```
option go_package = "example/gen/greet/v1;greetv1";
```

型定義
```
message GreetRequest {
  string name = 1;
}

message GreetResponse {
  string greeting = 1;
}
```
messageは、複数のフィールドを持つことができるもので、フィールドは、型、名前、および一意のフィールド番号を持ちます。

```proto
message MyMessage {
  int32 id = 1;
  string name = 2;
  bool is_active = 3;
}
```

上記の例では、`MyMessage` という名前の `message` を定義していて、この `message` は、`id`（int32型）、`name`（string型）、および `is_active`（bool型）の3つのフィールドを持っています。

Enum型
```proto
enum Status {
  UNKNOWN = 0;
  IN_PROGRESS = 1;
  COMPLETED = 2;
}

message Task {
  int32 id = 1;
  string description = 2;
  Status status = 3;
}
```
`enum` を使用すると、一連の定義済み定数の中から1つを選択できます。上記の例では、`Status` という名前の `enum` を定義し、`Task` messageの中で使用しています。

repeated
```proto
message TaskList {
  repeated Task tasks = 1;
}
```
`repeated` キーワードは、フィールドが配列であることを示します。
上記の例では、`TaskList` messageは、複数の `Task` を持つことができます。

# 使用できるデータ型
| Proto Type | 説明                                   |
| ---------- | ------------------------------------ |
| int32      | 符号付き32ビット整数                          |
| int64      | 符号付き64ビット整数                          |
| uint32     | 符号なし32ビット整数                          |
| uint64     | 符号なし64ビット整数                          |
| sint32     | 符号付き32ビット整数。ZigZagエンコーディングを使用        |
| sint64     | 符号付き64ビット整数。ZigZagエンコーディングを使用        |
| fixed32    | 常に4バイトの符号なし32ビット整数                   |
| fixed64    | 常に8バイトの符号なし64ビット整数                   |
| sfixed32   | 常に4バイトの符号付き32ビット整数。ZigZagエンコーディングを使用 |
| sfixed64   | 常に8バイトの符号付き64ビット整数。ZigZagエンコーディングを使用 |
| float      | 32ビット浮動小数点数                          |
| double     | 64ビット浮動小数点数                          |
| bool       | 真偽値 (true または false)                 |
| string     | UTF-8文字列                             |
| bytes      | バイト列                                 |

## Bufの用意

buf config initでbuf.yamlを作る
```sh
buf config init
```

buf.gen.yamlを定義する

```sh
touch buf.gen.yaml
```

```yaml:buf.gen.yaml
version: v2
plugins:
  - local: protoc-gen-go
    out: gen
    opt: paths=source_relative
  - local: protoc-gen-connect-go
    out: gen
    opt: paths=source_relative
```

ここまでセットアップできたら、protoファイルからコードを生成してみる。

```sh
buf lint
buf generate
```

すると、genの中にコードが生成される。
```
.
├── buf.gen.yaml
├── buf.yaml
├── gen
│   └── greet
│       └── v1
│           ├── greet.pb.go
│           └── greetv1connect
│               └── greet.connect.go
├── go.mod
└── greet
    └── v1
        └── greet.proto
```

生成されたコードをgoで使ってみる。

## サーバー側
必要なもの作成する
```sh
mkdir -p cmd/server
touch cmd/server/main.go
```

```go:cmd/server/main.go
package main

import (
	"context"
	"fmt"
	"log"
	"net/http"

	"connectrpc.com/connect"
	"golang.org/x/net/http2"
	"golang.org/x/net/http2/h2c"

	greetv1 "example/gen/greet/v1"        // generated by protoc-gen-go
	"example/gen/greet/v1/greetv1connect" // generated by protoc-gen-connect-go
)

type GreetServer struct{}

func (s *GreetServer) Greet(
	ctx context.Context,
	req *connect.Request[greetv1.GreetRequest],
) (*connect.Response[greetv1.GreetResponse], error) {
	log.Println("Request headers: ", req.Header())
	res := connect.NewResponse(&greetv1.GreetResponse{
		Greeting: fmt.Sprintf("Hello, %s!", req.Msg.Name),
	})
	res.Header().Set("Greet-Version", "v1")
	return res, nil
}

func main() {
	greeter := &GreetServer{}
	mux := http.NewServeMux()
	path, handler := greetv1connect.NewGreetServiceHandler(greeter)
	mux.Handle(path, handler)
	err := http.ListenAndServe(
		"localhost:8080",
		h2c.NewHandler(mux, &http2.Server{}),
	)
	if err != nil {
		return
	}
}
```

### コード解説

#### パッケージのインポート
```go:cmd/server/main.go
import (
	"context"
	"fmt"
	"log"
	"net/http"

	"connectrpc.com/connect"
	"golang.org/x/net/http2"
	"golang.org/x/net/http2/h2c"

	greetv1 "example/gen/greet/v1"        // generated by protoc-gen-go
	"example/gen/greet/v1/greetv1connect" // generated by protoc-gen-connect-go
)
```

- 標準ライブラリ（`context`, `fmt`, `log`, `net/http`）をインポート
- Connect関連のパッケージ（`connectrpc.com/connect`）をインポート
- HTTP/2サポートのパッケージ（`golang.org/x/net/http2`, `golang.org/x/net/http2/h2c`）をインポート
- Bufで生成されたコード（`greetv1`と`greetv1connect`）をインポート

#### 構造体の定義
```go:cmd/server/main.go
type GreetServer struct{}
```

#### Greetメソッドの実装
protoファイルから生成されたコードを元に、Greetメゾットを実装していく。

```go:cmd/server/main.go
func (s *GreetServer) Greet(
	ctx context.Context,
	req *connect.Request[greetv1.GreetRequest],
) (*connect.Response[greetv1.GreetResponse], error) {
	log.Println("Request headers: ", req.Header())
	res := connect.NewResponse(&greetv1.GreetResponse{
		Greeting: fmt.Sprintf("Hello, %s!", req.Msg.Name),
	})
	res.Header().Set("Greet-Version", "v1")
	return res, nil
}
```

#### main
```go:cmd/server/main.go
func main() {
	greeter := &GreetServer{}
	mux := http.NewServeMux()
	
	path, handler := greetv1connect.NewGreetServiceHandler(greeter)
	mux.Handle(path, handler)
	err := http.ListenAndServe(
		"localhost:8080",
		h2c.NewHandler(mux, &http2.Server{}),
	)
	if err != nil {
		return
	}
}
```

まず、GreetServerのインスタンス化とマルチプレクサ(multiplexer)の作成を行う
```go:cmd/server/main.go
greeter := &GreetServer{}
mux := http.NewServeMux()
```

:::details マルチプレクサとは
複数のハンドラの中から、特定のリクエストパスに合致するハンドラを一つ選んで実行する仕組み

例)
path: / だったら hander: log.Println("Root")
path:/greet だったら hander: log.Println("Hello")

みたいなことをやってくれるやつ
:::

greetv1connect.NewGreetServiceHandlerを使い、pathとhandlerを取得
```go:cmd/server/main.go
path, handler := greetv1connect.NewGreetServiceHandler(greeter)
```

mux.Handleで、pathとhanderを登録
```go:cmd/server/main.go
mux.Handle(path, handler)
```

サーバー起動する。h2cを使えば、TLSなしでHTTP/2を起動できるみたい
```go:cmd/server/main.go
err := http.ListenAndServe(
	"localhost:8080",
	h2c.NewHandler(mux, &http2.Server{}),
)
if err != nil {
	return
}
```

### サーバーを起動してみる
```sh
go run ./cmd/server/main.go
```

別のターミナルを開き、テストしてみる。

まずはcurl
```sh
curl \
    --header "Content-Type: application/json" \
    --data '{"name": "Jane"}' \
    http://localhost:8080/greet.v1.GreetService/Greet
```

すると、以下のようなレスポンスが返ってくる
```sh
{"greeting":"Hello, Jane!"}%  
```

connectのいいところは、grpcurl使わなくてもテストできるところですね

次は、grpcurl
```sh
grpcurl \
    -protoset <(buf build -o -) -plaintext \
    -d '{"name": "Jane"}' \
    localhost:8080 greet.v1.GreetService/Greet
```

すると、以下のようなレスポンスが返ってくる
```sh
{
  "greeting": "Hello, Jane!"
}
```

## クライアント側
```sh
mkdir -p cmd/client
touch cmd/client/main.go
```

完成系のコード
```go:cmd/client/main.go
package main

import (
	"context"
	"log"
	"net/http"

	greetv1 "example/gen/greet/v1"
	"example/gen/greet/v1/greetv1connect"

	"connectrpc.com/connect"
)

func main() {
	client := greetv1connect.NewGreetServiceClient(
		http.DefaultClient,
		"http://localhost:8080",
	)

	res, err := client.Greet(
		context.Background(),
		connect.NewRequest(&greetv1.GreetRequest{Name: "Jane"}),
	)

	if err != nil {
		log.Println(err)
	}
	log.Println(res)
	log.Println(res.Msg.Greeting)
}

```

### コード解説

#### パッケージのインポート
```go:cmd/client/main.go
import (
	"context"
	"log"
	"net/http"

	greetv1 "example/gen/greet/v1"
	"example/gen/greet/v1/greetv1connect"

	"connectrpc.com/connect"
)
```

#### main
gRPCサーバーにリクエストを送るためのクライアントを作成
```go:cmd/client/main.go
client := greetv1connect.NewGreetServiceClient(  
    http.DefaultClient,  
    "http://localhost:8080",  
)
```

サーバー側のGreetメゾットを呼び出す
context.Background()で空のコンテキストを作成
connect.NewRequestでリクエスト
```go:cmd/client/main.go
res, err := client.Greet(  
    context.Background(),  
    connect.NewRequest(&greetv1.GreetRequest{Name: "Jane"}),  
)
```

レスポンスの処理
```go:cmd/client/main.go
if err != nil {  
    log.Println(err)  
}  
log.Println(res.Msg.Greeting)
```

### クライアントを使ってみる
サーバーを起動した状態で、clientを実行
```sh
go run ./cmd/client/main.go
```


