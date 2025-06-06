---
id: 27-zenn-grpc-golang
aliases: 
tags: 
autonotemover: disable
cssclasses:
  - zenn
date: 2025-06-03
emoji: 🛜
published: true
title: Connect-goとBufを使って、GoでgRPC入門してみよう
type: tech
url: https://zenn.dev/thirdlf/articles/27-zenn-grpc-golang
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

次のChatアプリを作ってみるまでやる場合、buf と grpcurlはhomebrew などでインストールしてください。
```sh
brew install buf
brew install grpcurl
```

go installでも可
```sh
go install github.com/bufbuild/buf/cmd/buf@latest
go install github.com/fullstorydev/grpcurl/cmd/grpcurl@latest
```

その他、使うやつインストール
```sh
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

## 使用できるデータ型
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

良さそうですね
```sh
❯ go run cmd/client/main.go                                                                                                             ✨   00:01
2025/06/05 00:01:30 Hello, Jane!
```

# Chatアプリ作ってみる
getting-startedだと、Unary RPCしかやってなかったので他も触っていきます

参考) 
https://zenn.dev/hsaki/books/golang-grpc-starting/viewer/stream

サーバー側をGo、クライアント側をGoにします。


新しくディレクトリ作成する
```sh
mkdir chat-example
```

```sh
mkdir -p chat/v1/
touch chat/v1/chat.proto
```

欲張って、Server streaming RPC、Client streaming RPC、Bidirectional streaming RPC使えるようにしてみましょう。

```proto:chat.proto
syntax = "proto3";  
  
package chat.v1;  
  
option go_package = "chat/gen/chat/v1;chatv1";  
  
message ChatMessageRequest {  
  string user = 1;  
  string message = 2;  
}  
  
message ChatMessageResponse {  
  string message = 1;  
}  
  
service ChatService {  
  rpc ClientChat(stream ChatMessageRequest) returns (ChatMessageResponse) {}  
  rpc ServerChat(ChatMessageRequest) returns (stream ChatMessageResponse) {}  
  rpc BidirectionalChat(stream ChatMessageRequest) returns (stream ChatMessageResponse) {}  
}
```

コンフィグ初期化して
```sh
buf config init
```

リント
```sh
buf lint
```

すると、すっごい怒られますね

```
❯ buf lint                                                                                                                         4m54s✨   00:28
chat/v1/chat.proto:17:3:"chat.v1.ChatMessageRequest" is used as the request or response type for multiple RPCs.
chat/v1/chat.proto:17:3:"chat.v1.ChatMessageResponse" is used as the request or response type for multiple RPCs.
chat/v1/chat.proto:17:25:RPC request type "ChatMessageRequest" should be named "ClientChatRequest" or "ChatServiceClientChatRequest".
chat/v1/chat.proto:17:54:RPC response type "ChatMessageResponse" should be named "ClientChatResponse" or "ChatServiceClientChatResponse".
chat/v1/chat.proto:18:3:"chat.v1.ChatMessageRequest" is used as the request or response type for multiple RPCs.
chat/v1/chat.proto:18:3:"chat.v1.ChatMessageResponse" is used as the request or response type for multiple RPCs.
chat/v1/chat.proto:18:18:RPC request type "ChatMessageRequest" should be named "ServerChatRequest" or "ChatServiceServerChatRequest".
chat/v1/chat.proto:18:54:RPC response type "ChatMessageResponse" should be named "ServerChatResponse" or "ChatServiceServerChatResponse".
chat/v1/chat.proto:19:3:"chat.v1.ChatMessageRequest" is used as the request or response type for multiple RPCs.
chat/v1/chat.proto:19:3:"chat.v1.ChatMessageResponse" is used as the request or response type for multiple RPCs.
chat/v1/chat.proto:19:32:RPC request type "ChatMessageRequest" should be named "BidirectionalChatRequest" or "ChatServiceBidirectionalChatRequest".
chat/v1/chat.proto:19:68:RPC response type "ChatMessageResponse" should be named "BidirectionalChatResponse" or "ChatServiceBidirectionalChatResponse".
```

言われた通りに直してみましょう。
```proto:chat.proto
syntax = "proto3";

package chat.v1;

option go_package = "chat/gen/chat/v1;chatv1";

message ClientChatRequest {
  string user = 1;
  string message = 2;
}

message ClientChatResponse {
  string message = 1;
}

message ServerChatRequest {
  string user = 1;
  string message = 2;
}

message ServerChatResponse {
  string message = 1;
}

message BidirectionalChatRequest {
  string user = 1;
  string message = 2;
}

message BidirectionalChatResponse {
  string message = 1;
}

service ChatService {
  rpc ClientChat(stream ClientChatRequest) returns (ClientChatResponse) {}
  rpc ServerChat(ServerChatRequest) returns (stream ServerChatResponse) {}
  rpc BidirectionalChat(stream BidirectionalChatRequest) returns (stream BidirectionalChatResponse) {}
}

```
リント
```sh
buf lint
```

すると、何も言われなくなりました
```
❯ buf lint                                                                                                                                                                   0s✨   00:29

```

基本的に、buf lintに従って直すしたほうが基本protoファイルが綺麗になるので、generateする前にbuf lintして確認してあげましょう。

generateするための準備する。
```sh
go mod init chat
```

```sh
go install google.golang.org/protobuf/cmd/protoc-gen-go@latest
go install connectrpc.com/connect/cmd/protoc-gen-connect-go@latest
go get golang.org/x/net/http2  
go get connectrpc.com/connect
```

```sh
[ -n "$(go env GOBIN)" ] && export PATH="$(go env GOBIN):${PATH}"
[ -n "$(go env GOPATH)" ] && export PATH="$(go env GOPATH)/bin:${PATH}"
```

```
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

## サーバー側
まずmain.goを用意
```sh
mkdir -p cmd/server/
touch cmd/server/main.go
```

コード
```go:cmd/server/main.go
package main

import (
	chatv1 "chat/gen/chat/v1"
	"chat/gen/chat/v1/chatv1connect"
	"connectrpc.com/connect"
	"context"
	"fmt"
	"io"
	"log"
	"net/http"
	"time"

	"golang.org/x/net/http2"
	"golang.org/x/net/http2/h2c"
)

type ChatServer struct{}

func (s *ChatServer) ClientChat(
	ctx context.Context,
	stream *connect.ClientStream[chatv1.ClientChatRequest],
) (*connect.Response[chatv1.ClientChatResponse], error) {
	log.Println("ClientChat開始")

	var messages []string
	for stream.Receive() {
		req := stream.Msg()
		log.Printf("受信: ユーザー=%s, メッセージ=%s", req.User, req.Message)
		messages = append(messages, fmt.Sprintf("%s: %s", req.User, req.Message))
	}

	if err := stream.Err(); err != nil {
		return nil, connect.NewError(connect.CodeInvalidArgument, err)
	}

	response := &chatv1.ClientChatResponse{
		Message: fmt.Sprintf("受信した%d件のメッセージ: %v", len(messages), messages),
	}

	log.Printf("ClientChatレスポンス: %s", response.Message)
	return connect.NewResponse(response), nil
}

func (s *ChatServer) ServerChat(
	ctx context.Context,
	req *connect.Request[chatv1.ServerChatRequest],
	stream *connect.ServerStream[chatv1.ServerChatResponse],
) error {
	log.Printf("ServerChat開始: ユーザー=%s, メッセージ=%s", req.Msg.User, req.Msg.Message)

	for i := 1; i <= 5; i++ {
		select {
		case <-ctx.Done():
			return ctx.Err()
		default:
		}

		response := &chatv1.ServerChatResponse{
			Message: fmt.Sprintf("サーバーからのメッセージ %d/5: %sさん、「%s」への応答です",
				i, req.Msg.User, req.Msg.Message),
		}

		if err := stream.Send(response); err != nil {
			return err
		}

		log.Printf("送信: %s", response.Message)
		time.Sleep(1 * time.Second)
	}

	log.Println("ServerChat完了")
	return nil
}

func (s *ChatServer) BidirectionalChat(
	ctx context.Context,
	stream *connect.BidiStream[chatv1.BidirectionalChatRequest, chatv1.BidirectionalChatResponse],
) error {
	log.Println("BidirectionalChat開始")

	for {
		select {
		case <-ctx.Done():
			return ctx.Err()
		default:
		}

		req, err := stream.Receive()
		if err != nil {
			if err == io.EOF {
				log.Println("BidirectionalChat正常終了")
				return nil
			}
			return err
		}

		log.Printf("双方向受信: ユーザー=%s, メッセージ=%s", req.User, req.Message)

		response := &chatv1.BidirectionalChatResponse{
			Message: fmt.Sprintf("エコー: %sさんから「%s」を受信しました", req.User, req.Message),
		}

		if err := stream.Send(response); err != nil {
			return err
		}

		log.Printf("双方向送信: %s", response.Message)
	}
}

func main() {
	chatServer := &ChatServer{}

	mux := http.NewServeMux()

	path, handler := chatv1connect.NewChatServiceHandler(chatServer)

	mux.Handle(path, handler)
	
	err := http.ListenAndServe(
		"localhost:8080",
		h2c.NewHandler(mux, &http2.Server{}),
	)
	if err != nil {
		log.Fatalf("サーバー起動エラー: %v", err)
	}

}
```

## クライアント側 (Go)
```sh
mkdir -p cmd/client/
touch cmd/client/main.go
```

```go:cmd/client/main.go
package main

import (
	chatv1 "chat/gen/chat/v1"
	"chat/gen/chat/v1/chatv1connect"
	"connectrpc.com/connect"
	"context"
	"crypto/tls"
	"io"
	"log"
	"net"
	"net/http"
	"time"
	
	"golang.org/x/net/http2"
)

func main() {
	httpClient := &http.Client{
		Transport: &http2.Transport{
			AllowHTTP: true,
			DialTLS: func(network, addr string, cfg *tls.Config) (net.Conn, error) {
				return net.Dial(network, addr)
			},
		},
	}

	client := chatv1connect.NewChatServiceClient(
		httpClient,
		"http://localhost:8080",
	)

	ctx := context.Background()


	log.Println("\n1. ClientChatをテスト中...")
	testClientChat(ctx, client)

	time.Sleep(2 * time.Second)

	log.Println("\n2. ServerChatをテスト中...")
	testServerChat(ctx, client)

	time.Sleep(2 * time.Second)

	log.Println("\n3. BidirectionalChatをテスト中...")
	testBidirectionalChat(ctx, client)

	log.Println("\n=== すべてのテスト完了 ===")
}


func testClientChat(ctx context.Context, client chatv1connect.ChatServiceClient) {
	stream := client.ClientChat(ctx)

	messages := []struct {
		user    string
		message string
	}{
		{"田中", "こんにちは！"},
		{"田中", "今日はいい天気ですね"},
		{"田中", "よろしくお願いします"},
	}

	for _, msg := range messages {
		req := &chatv1.ClientChatRequest{
			User:    msg.user,
			Message: msg.message,
		}
		
		if err := stream.Send(req); err != nil {
			log.Fatalf("ClientChat送信エラー: %v", err)
		}
		log.Printf("送信: %s: %s", msg.user, msg.message)
		time.Sleep(500 * time.Millisecond)
	}

	resp, err := stream.CloseAndReceive()
	if err != nil {
		log.Fatalf("ClientChatクローズエラー: %v", err)
	}

	log.Printf("ClientChatレスポンス: %s", resp.Msg.Message)
}

func testServerChat(ctx context.Context, client chatv1connect.ChatServiceClient) {
	req := connect.NewRequest(&chatv1.ServerChatRequest{
		User:    "佐藤",
		Message: "サーバーストリーミングのテストです",
	})

	stream, err := client.ServerChat(ctx, req)
	if err != nil {
		log.Fatalf("ServerChat開始エラー: %v", err)
	}

	log.Printf("ServerChatリクエスト送信: %s: %s", req.Msg.User, req.Msg.Message)

	for stream.Receive() {
		resp := stream.Msg()
		log.Printf("ServerChatレスポンス: %s", resp.Message)
	}

	if err := stream.Err(); err != nil {
		log.Fatalf("ServerChatストリームエラー: %v", err)
	}
	log.Println("ServerChat完了")
}

func testBidirectionalChat(ctx context.Context, client chatv1connect.ChatServiceClient) {
	stream := client.BidirectionalChat(ctx)

	go func() {
		messages := []struct {
			user    string
			message string
		}{
			{"山田", "双方向チャット開始"},
			{"山田", "メッセージ1"},
			{"山田", "メッセージ2"},
			{"山田", "メッセージ3"},
		}

		for _, msg := range messages {
			req := &chatv1.BidirectionalChatRequest{
				User:    msg.user,
				Message: msg.message,
			}

			if err := stream.Send(req); err != nil {
				log.Printf("BidirectionalChat送信エラー: %v", err)
				return
			}

			log.Printf("双方向送信: %s: %s", msg.user, msg.message)
			time.Sleep(1 * time.Second)
		}

		if err := stream.CloseRequest(); err != nil {
			log.Printf("BidirectionalChatクローズエラー: %v", err)
		}
	}()

	for {
		resp, err := stream.Receive()
		if err != nil {
			if err == io.EOF {
				log.Println("BidirectionalChat正常終了")
				break
			}
			log.Fatalf("BidirectionalChatストリームエラー: %v", err)
		}
		log.Printf("双方向受信: %s", resp.Message)
	}
	
	log.Println("BidirectionalChat完了")
}
```


## クライアント側 (Flutter) おまけ

flutterプロジェクト作成
```sh
flutter create chat_flutter
cd chat_flutter
```

protoファイル作成
```sh
mkdir -p chat/v1/
touch chat/v1/chat.proto
```

```proto:chat/v1/chat.proto
syntax = "proto3";

package chat.v1;

option go_package = "chat/gen/chat/v1;chatv1";

message ClientChatRequest {
  string user = 1;
  string message = 2;
}

message ClientChatResponse {
  string message = 1;
}

message ServerChatRequest {
  string user = 1;
  string message = 2;
}

message ServerChatResponse {
  string message = 1;
}

message BidirectionalChatRequest {
  string user = 1;
  string message = 2;
}

message BidirectionalChatResponse {
  string message = 1;
}

service ChatService {
  rpc ClientChat(stream ClientChatRequest) returns (ClientChatResponse) {}
  rpc ServerChat(ServerChatRequest) returns (stream ServerChatResponse) {}
  rpc BidirectionalChat(stream BidirectionalChatRequest) returns (stream BidirectionalChatResponse) {}
}
```

必要なもの追加
```sh
flutter pub add connectrpc
```

config init
```sh
buf config init
```

gen.yaml書く
```sh
touch buf.gen.yaml
```


```yaml:buf.gen.yaml
version: v2
plugins:
  - remote: buf.build/connectrpc/dart
    out: lib/gen
  - remote: buf.build/protocolbuffers/dart
    out: lib/gen
    include_wkt: true
    include_imports: true
```

generate
```
buf generate
```

main.dartにclientコードを書いていきます。

*なお、FlutterミリしらなのでClaudeに書かせています。
動作は確認しています。
```dart:/lib/main.dart
import 'package:flutter/material.dart';
import 'package:connectrpc/http2.dart' as http2;
import 'package:connectrpc/protocol/connect.dart' as connect;
import 'package:connectrpc/protobuf.dart' as protobuf;
import 'gen/chat/v1/chat.pb.dart';
import 'gen/chat/v1/chat.connect.client.dart';
import 'dart:async';

void main() {
  runApp(const MyApp());
}

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Chat Client',
      theme: ThemeData(
        colorScheme: ColorScheme.fromSeed(seedColor: Colors.blue),
        useMaterial3: true,
      ),
      home: const ChatPage(),
    );
  }
}

class ChatMessage {
  final String user;
  final String message;
  final DateTime timestamp;
  final bool isFromServer;

  ChatMessage({
    required this.user,
    required this.message,
    required this.timestamp,
    this.isFromServer = false,
  });
}

class ChatPage extends StatefulWidget {
  const ChatPage({super.key});

  @override
  State<ChatPage> createState() => _ChatPageState();
}

class _ChatPageState extends State<ChatPage> {
  final TextEditingController _messageInputController = TextEditingController();
  final TextEditingController _userController = TextEditingController();
  final List<ChatMessage> _messages = [];
  final ScrollController _scrollController = ScrollController();
  
  late ChatServiceClient _client;
  StreamController<ChatMessage>? _messageController;
  StreamController<ClientChatRequest>? _clientStreamController;
  Stream<BidirectionalChatResponse>? _bidirectionalStream;
  StreamController<BidirectionalChatRequest>? _bidirectionalController;
  int _clientMessageCount = 0;
  
  bool _isConnected = false;
  String _connectionStatus = "未接続";
  String _streamType = 'server'; // 'client', 'server', 'bidirectional'
  
  final String _host = 'localhost';  // 必要に応じて変更
  // 開発用の代替：
  // - エミュレータの場合: '10.0.2.2' 
  // - iOSシミュレータの場合: 'localhost' または '127.0.0.1'
  // - 実機の場合: PCのローカルIPアドレス
  final int _port = 8080;

  @override
  void initState() {
    super.initState();
    _initializeClient();
    _userController.text = 'User${DateTime.now().millisecondsSinceEpoch % 1000}';
  }

  void _initializeClient() async {
    try {
      _addDebugMessage("クライアント初期化開始: $_host:$_port");
      
      // HTTP/2対応のConnect Transportを作成
      final transport = connect.Transport(
        baseUrl: 'http://$_host:$_port',
        codec: protobuf.ProtoCodec(),
        httpClient: http2.createHttpClient(),
      );
      
      _client = ChatServiceClient(transport);
      
      _messageController = StreamController<ChatMessage>();
      
      // 接続テスト
      await _testConnection();
      
      if (_isConnected) {
        _startStreaming();
      }
    } catch (e) {
      _addDebugMessage("初期化エラー: $e");
      _updateConnectionStatus("初期化失敗: $e");
    }
  }

  void _addDebugMessage(String message) {
    _addMessage('Debug', message, isFromServer: true);
  }

  void _updateConnectionStatus(String status) {
    setState(() {
      _connectionStatus = status;
    });
  }

  Future<void> _testConnection() async {
    try {
      _updateConnectionStatus("接続テスト中...");
      
      final request = ServerChatRequest(
        user: 'system',
        message: 'connection_test',
      );
      
      final responseStream = _client.serverChat(request);
      final response = await responseStream.first.timeout(
        const Duration(seconds: 5),
      );
      
      setState(() {
        _isConnected = true;
        _connectionStatus = "接続成功";
      });
      _addDebugMessage("接続テスト成功: ${response.message}");
    } catch (e) {
      setState(() {
        _isConnected = false;
        _connectionStatus = "接続失敗: $e";
      });
      _addDebugMessage("接続テスト失敗: $e");
    }
  }

  void _onStreamTypeChanged() {
    _addDebugMessage("ストリームタイプを $_streamType に変更");
    _cleanupStreams();
    _clientMessageCount = 0;
    if (_isConnected) {
      _startStreaming();
    }
  }
  
  void _cleanupStreams() {
    _clientStreamController?.close();
    _clientStreamController = null;
    _bidirectionalController?.close();
    _bidirectionalController = null;
    _bidirectionalStream = null;
  }

  void _startStreaming() {
    _addDebugMessage("$_streamType ストリーミング開始");
    
    switch (_streamType) {
      case 'client':
        _startClientStreaming();
        break;
      case 'server':
        break;
      case 'bidirectional':
        _startBidirectionalStreaming();
        break;
    }
  }
  
  void _startClientStreaming() {
    _clientStreamController = StreamController<ClientChatRequest>();
    _clientMessageCount = 0;
    
    _client.clientChat(_clientStreamController!.stream).then((response) {
      _addMessage('Server', response.message, isFromServer: true);
      _addDebugMessage("Client stream完了");
    }).catchError((error) {
      _addMessage('System', 'Client stream error: $error', isFromServer: true);
    });
  }
  
  void _startBidirectionalStreaming() {
    _bidirectionalController = StreamController<BidirectionalChatRequest>();
    
    _bidirectionalStream = _client.bidirectionalChat(_bidirectionalController!.stream);
    _bidirectionalStream!.listen(
      (response) {
        _addMessage('Server', response.message, isFromServer: true);
      },
      onError: (error) {
        _addMessage('System', 'Bidirectional stream error: $error', isFromServer: true);
      },
      onDone: () {
        _addDebugMessage("Bidirectional stream終了");
      },
    );
  }

  void _scrollToBottom() {
    WidgetsBinding.instance.addPostFrameCallback((_) {
      if (_scrollController.hasClients) {
        _scrollController.animateTo(
          _scrollController.position.maxScrollExtent,
          duration: const Duration(milliseconds: 300),
          curve: Curves.easeOut,
        );
      }
    });
  }

  void _addMessage(String user, String message, {bool isFromServer = false}) {
    setState(() {
      _messages.add(ChatMessage(
        user: user,
        message: message,
        timestamp: DateTime.now(),
        isFromServer: isFromServer,
      ));
    });
    _scrollToBottom();
  }

  Future<void> _sendMessage() async {
    if (_messageInputController.text.trim().isEmpty || _userController.text.trim().isEmpty) {
      return;
    }

    final message = _messageInputController.text.trim();
    final user = _userController.text.trim();
    
    _addMessage(user, message);
    _messageInputController.clear();

    try {
      switch (_streamType) {
        case 'client':
          await _sendClientMessage(user, message);
          break;
        case 'server':
          await _sendServerMessage(user, message);
          break;
        case 'bidirectional':
          await _sendBidirectionalMessage(user, message);
          break;
      }
    } catch (e) {
      _addMessage('System', 'Error: $e', isFromServer: true);
    }
  }
  
  Future<void> _sendClientMessage(String user, String message) async {
    if (_clientStreamController == null) {
      _startClientStreaming();
    }
    
    final request = ClientChatRequest(
      user: user,
      message: message,
    );
    
    _clientStreamController!.add(request);
    _clientMessageCount++;
    
    if (_clientMessageCount >= 2) {
      _addDebugMessage("2つのメッセージを送信したため、client streamを閉じます");
      _clientStreamController!.close();
      _clientStreamController = null;
      _clientMessageCount = 0;
    }
  }
  
  Future<void> _sendServerMessage(String user, String message) async {
    final request = ServerChatRequest(
      user: user,
      message: message,
    );
    
    final responseStream = _client.serverChat(request);
    
    await for (final response in responseStream) {
      _addMessage('Server', response.message, isFromServer: true);
    }
  }
  
  Future<void> _sendBidirectionalMessage(String user, String message) async {
    if (_bidirectionalController == null) {
      _startBidirectionalStreaming();
    }
    
    final request = BidirectionalChatRequest(
      user: user,
      message: message,
    );
    
    _bidirectionalController!.add(request);
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Chat Client'),
        backgroundColor: Theme.of(context).colorScheme.inversePrimary,
        actions: [
          Padding(
            padding: const EdgeInsets.symmetric(horizontal: 16.0),
            child: Row(
              mainAxisSize: MainAxisSize.min,
              children: [
                Icon(
                  _isConnected ? Icons.circle : Icons.circle_outlined,
                  color: _isConnected ? Colors.green : Colors.red,
                  size: 12,
                ),
                const SizedBox(width: 4),
                Text(
                  _connectionStatus,
                  style: Theme.of(context).textTheme.bodySmall,
                ),
              ],
            ),
          ),
        ],
      ),
      body: Column(
        children: [
          Container(
            padding: const EdgeInsets.all(16.0),
            decoration: BoxDecoration(
              color: Theme.of(context).colorScheme.surface,
              border: Border(
                bottom: BorderSide(
                  color: Theme.of(context).colorScheme.outline,
                  width: 1,
                ),
              ),
            ),
            child: Column(
              children: [
                Row(
                  children: [
                    const Text('Username: '),
                    Expanded(
                      child: TextField(
                        controller: _userController,
                        decoration: const InputDecoration(
                          hintText: 'Enter your username',
                          isDense: true,
                          contentPadding: EdgeInsets.symmetric(horizontal: 8, vertical: 4),
                        ),
                      ),
                    ),
                  ],
                ),
                const SizedBox(height: 12),
                Row(
                  children: [
                    const Text('Stream Type: '),
                    Expanded(
                      child: SegmentedButton<String>(
                        segments: const [
                          ButtonSegment(
                            value: 'client',
                            label: Text('Client'),
                            icon: Icon(Icons.upload),
                          ),
                          ButtonSegment(
                            value: 'server',
                            label: Text('Server'),
                            icon: Icon(Icons.download),
                          ),
                          ButtonSegment(
                            value: 'bidirectional',
                            label: Text('Bidi'),
                            icon: Icon(Icons.sync),
                          ),
                        ],
                        selected: {_streamType},
                        onSelectionChanged: (Set<String> newSelection) {
                          setState(() {
                            _streamType = newSelection.first;
                          });
                          _onStreamTypeChanged();
                        },
                      ),
                    ),
                  ],
                ),
              ],
            ),
          ),
          Expanded(
            child: _messages.isEmpty
                ? const Center(
                    child: Text(
                      'No messages yet. Start chatting!',
                      style: TextStyle(
                        fontSize: 16,
                        color: Colors.grey,
                      ),
                    ),
                  )
                : ListView.builder(
                    controller: _scrollController,
                    padding: const EdgeInsets.all(16.0),
                    itemCount: _messages.length,
                    itemBuilder: (context, index) {
                      final message = _messages[index];
                      return Padding(
                        padding: const EdgeInsets.only(bottom: 8.0),
                        child: Card(
                          color: message.isFromServer
                              ? Theme.of(context).colorScheme.primaryContainer
                              : Theme.of(context).colorScheme.secondaryContainer,
                          child: Padding(
                            padding: const EdgeInsets.all(12.0),
                            child: Column(
                              crossAxisAlignment: CrossAxisAlignment.start,
                              children: [
                                Row(
                                  children: [
                                    Text(
                                      message.user,
                                      style: const TextStyle(
                                        fontWeight: FontWeight.bold,
                                      ),
                                    ),
                                    const Spacer(),
                                    Text(
                                      '${message.timestamp.hour.toString().padLeft(2, '0')}:'
                                      '${message.timestamp.minute.toString().padLeft(2, '0')}',
                                      style: Theme.of(context).textTheme.bodySmall,
                                    ),
                                  ],
                                ),
                                const SizedBox(height: 4),
                                Text(message.message),
                              ],
                            ),
                          ),
                        ),
                      );
                    },
                  ),
          ),
          Container(
            padding: const EdgeInsets.all(16.0),
            decoration: BoxDecoration(
              color: Theme.of(context).colorScheme.surface,
              border: Border(
                top: BorderSide(
                  color: Theme.of(context).colorScheme.outline,
                  width: 1,
                ),
              ),
            ),
            child: Row(
              children: [
                Expanded(
                  child: TextField(
                    controller: _messageInputController,
                    decoration: const InputDecoration(
                      hintText: 'Type a message...',
                      border: OutlineInputBorder(),
                    ),
                    onSubmitted: (_) => _sendMessage(),
                  ),
                ),
                const SizedBox(width: 8),
                FilledButton(
                  onPressed: _isConnected ? _sendMessage : null,
                  child: const Text('Send'),
                ),
              ],
            ),
          ),
        ],
      ),
    );
  }

  @override
  void dispose() {
    _messageInputController.dispose();
    _userController.dispose();
    _scrollController.dispose();
    _messageController?.close();
    _cleanupStreams();
    super.dispose();
  }
}
```

