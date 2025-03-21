---
published: true
cssclasses:
  - zenn
type: tech
emoji: ⚙️
title: Cloudflare R2から取ってきた画像をwasm(Rust)で加工する
topics: 
date: 2025-03-20
AutoNoteMover: disable
url: https://zenn.dev/thirdlf/articles/19-zenn-wasm-image-process
tags:
  - rust
  - cloudflare
aliases:
---
# はじめに
タイトル通りのことをやろうとしたら、ちょっとつまづいたので自分のメモがてら記事を書きます。
wasmやRustについて詳しくは解説しません。

参考サイト
https://developer.mozilla.org/ja/docs/WebAssembly/Guides/Rust_to_Wasm

完成したやつ
https://image.thirdlf03.com

リポジトリ
https://github.com/thirdlf03/image-process-wasm

# 環境準備
まず、wasm-packをインストールしてない場合はインストール
```bash
cargo install wasm-pack
```

新しくライブラリプロジェクトを作成
```bash
cargo new --lib image-process
```

依存関係
```bash
cargo add image base64 wasm-bindgen
```

[lib]の部分を設定してあげる必要がある。

Cargo.toml
```toml
[package]
name = "image-process-wasm"
version = "0.1.0"
edition = "2021"

[lib]
crate-type = ["cdylib", "rlib"]

[dependencies]
base64 = "0.22.1"
image = "0.25.5"
wasm-bindgen = "0.2.100"
```

# 実装
まずは画像をグレースケールに変換する関数を書いていく。
引数にbase64を取り、戻り値でbase64を返す

画像の処理には、imageクレートを使用。photonなんかも良さそう

src/lib.rs
```rust
use std::io::Cursor;

use base64::prelude::*;
use image::load_from_memory;
use wasm_bindgen::prelude::*;

#[wasm_bindgen]
pub fn edit_image(base_img: &str) -> String {
    //base64 to image
    let img_buffer = BASE64_STANDARD.decode(base_img).unwrap();
    let mut img = load_from_memory(img_buffer.as_slice()).unwrap();

    //grayscale
    img = img.grayscale();

    //image to base64
    let mut buf: Vec<u8> = Vec::new();
    let _ = img.write_to(&mut Cursor::new(&mut buf), image::ImageFormat::Png);
    BASE64_STANDARD.encode(&buf)
}
```
base64からimageで使える形にしたり、その逆をする実装に時間がかかりました。

wasm-packでwebで使える形にビルドしてあげる
```bash
wasm-pack build --target web
```
するとpkgってディレクトリができて、そのなかにビルドされたファイルが入ってます。


これをhtml側で呼び出してみる。

index.html
```html
<body>
  <img id="base_img" crossorigin="annonymous" src="https://thirdlf03.com/IMG_2467.PNG" />
  <img id="output_img" src="" />
  <script type="module">
    import init, {edit_image} from "./pkg/image_process_wasm.js";

    function image() {
      const img = document.getElementById("base_img");
      let canvas = document.createElement("canvas");
      canvas.width = img.width;
      canvas.height = img.height;

      let ctx = canvas.getContext("2d");
      ctx.drawImage(img, 0, 0);

      let data_url = canvas.toDataURL("image/png");
      const base64 = data_url.match(/base64,(.*)/)?.[1];

      const convert_image = edit_image(base64);
      const output_image = "data:image/png;base64," + convert_image;
      document.getElementById("output_img").setAttribute("src", output_image);
    }

    async function start() {
      await init()
      console.log("Init");
      image();
    }

    start()

  </script>
</body>
```
実は、ここも詰まりポイントでR2のurlからbase64への変換ってどうやるのか分からず困っていました。試行錯誤した結果、このcanvasを使った実装であればいけることが判明。
ただし、
```html
<img id="base_img" crossorigin="annonymous" src="https://thirdlf03.com/IMG_2467.PNG" />
```
canvas.toDataURL("image/png");を使うには、imgのcrossorigin="annonymous" が必要でこれを許可するためにR2の設定をいじる必要があります。いい感じに設定してあげましょう。
![](/images/article-19/cors.png)


あと、wasmを使用する際にはinit()関数を実行する必要があります。
忘れずに実行するようにしましょう。
```js
    async function start() {
      await init()
      console.log("Init");
      image();
    }
```


準備ができたのでhttp-serverなどで確認してみます。
![](/images/article-19/neko.png)
いい感じですね！可愛いい


いろんな編集できるようにしてみる。

src/lib.rs
```rust
use std::io::Cursor;

use base64::prelude::*;
use image::{imageops::FilterType::Nearest, load_from_memory};
use wasm_bindgen::prelude::*;

#[wasm_bindgen]
pub fn edit_image(base_img: &str, select: &str) -> String {
    //base64 to image
    let img_buffer = BASE64_STANDARD.decode(base_img).unwrap();
    let mut img = load_from_memory(img_buffer.as_slice()).unwrap();

    img = match select {
        "half" => img.resize(img.width() / 2, img.height() / 2, Nearest),
        "gray" => img.grayscale(),
        "r90" => img.rotate90(),
        "r180" => img.rotate180(),
        _ => img,
    };

    //image to base64
    let mut buf: Vec<u8> = Vec::new();
    let _ = img.write_to(&mut Cursor::new(&mut buf), image::ImageFormat::Png);
    BASE64_STANDARD.encode(&buf)
}
```
画像を半分にしたり、なんなりできるようになった。

まずビルド。
```bash
wasm-pack build --target web
```

htmlも編集していく
index.html
```html
<body>
  <img id="base_img" crossorigin="annonymous" src="https://thirdlf03.com/IMG_2467.PNG" />
  <img id="output_img" src="" />
  <form id="check">
    <label><input type="checkbox" name="option" value="gray"> グレイスケール</label><br>
    <label><input type="checkbox" name="option" value="half"> 画像サイズを半分</label><br>
    <label><input type="checkbox" name="option" value="r90"> 90度回転</label><br>
    <label><input type="checkbox" name="option" value="r180">180度回転</label><br>

    <button type="submit">送信</button>
  </form>
  <script type="module">
    import init, {edit_image} from "./pkg/image_process_wasm.js";

    document.addEventListener("DOMContentLoaded", () => {
     const checkboxes = document.querySelectorAll('input[name="option"]');

      checkboxes.forEach(checkbox => {
        checkbox.addEventListener("change", function() {
          checkboxes.forEach(cb => {
            if (cb !== this) {
              cb.checked = false;
            }
          });
        });
      });
    });

    function image(select) {
      const img = document.getElementById("base_img");
      let canvas = document.createElement("canvas");
      canvas.width = img.width;
      canvas.height = img.height;

      let ctx = canvas.getContext("2d");
      ctx.drawImage(img, 0, 0);

      let data_url = canvas.toDataURL("image/png");
      const base64 = data_url.match(/base64,(.*)/)?.[1];

      const convert_image = edit_image(base64, select);
      const output_image = "data:image/png;base64," + convert_image;
      document.getElementById("output_img").setAttribute("src", output_image);
    }

    document.getElementById("check").addEventListener("submit", function(event) {
      event.preventDefault();
       const option = document.querySelector('input[name="option"]:checked');

      if (!option) {
        alert("1つ選択してください");
        return;
      }

      const value = option.value;
      image(value);
    });

    async function start() {
      await init()
      console.log("Init");
    }

    start()

  </script>
</body>
```

![](/images/article-19/neko2.png)
# 最後に
実は、ハッカソン中にこの実装をやろうとして数時間かかりました。
しかし、公式ドキュメントを見ながらこの実装ができた自分に成長を感じて嬉しかったです！