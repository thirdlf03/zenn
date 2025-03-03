---
published: true
cssclasses:
  - zenn
type: tech
emoji: 📝
title: "[wip]Rust勉強メモ"
topics: 
date: 2025-03-04
AutoNoteMover: disable
url: https://zenn.dev/thirdlf/articles/18-zenn-rust-benkyo-memo
tags: 
aliases:
---

# はじめに
これは自分のメモように書いたものです。
ざっくりなので、詳しくは以下を参照
https://doc.rust-jp.rs/book-ja/

# 変数
デフォルトは、immu

```rust
let apples = 5
let mut banaba = 5
```

# Result
stdでResult(enum)が帰ってきてる
OK, errが帰ってきてて、errの時の挙動をexceptで書ける

# Trim
入力の時enter押すと\nが入るのを削除

# Parse
```rust
let guess: u32 = guess.trim().parse()
```
みたいにparseできる

# 定数
constで定義するのと、型推論が必要

命名きそくは大文字でアンダースコア区切り
```rust
const MAX_POINTS = 10000;
```

# シャドーイング
前に定義した名前と同じ名前で変数を新しく定義できる
```rust
fn main() {
	let x = 5;
	let x = x + 1;
	{
		let x = x * 2; //こいつのこと
		println!("{}", x)
	}
	println!("{}", x)
}

// 12
// 6
```

また、
```rust
let spaces = " "; 
let spaces = spaces.len();
```

これはok

```rust
let mut spaces = " ";
spaces = spaces.len();
```
mutだとだめ

再代入しようとしている判定になるから

# データ型
標準で搭載されている方は、スタックに保管される。
逆に、標準ライブラリや自作の方は、ヒープに保管
## 整数型

|大きさ|符号付き|符号なし|
|---|---|---|
|8-bit|`i8`|`u8`|
|16-bit|`i16`|`u16`|
|32-bit|`i32`|`u32`|
|64-bit|`i64`|`u64`|
|arch|`isize`|`usize`|
## 整数リテラル
|数値リテラル|例|
|---|---|
|10進数|`98_222`|
|16進数|`0xff`|
|8進数|`0o77`|
|2進数|`0b1111_0000`|
|バイト (`u8`だけ)|`b'A'`|
## 浮動小数点
デフォルト
f64

f32もいける

## Char
''で囲むとchar

## 文字列リテラル

これは、immu

数値とは違い、文字列はメモリの管理がめんどいから文字列リテラルは不変になってるイメージ

逆に、ヒープに時はStringを使う。詳しくは後述
```rust
let s = "hello";
```


## Tuple
()の中に
```rust
fn main() { 
	let tup: (i32, f64, u8) = (500, 6.4, 1); 
}
```

参照方法は
```rust
fn main() { 
	let tup = (500, 6.4, 1); 
	let (x, y, z) = tup; 
	println!("The value of y is: {}", y);
}
```
もしくは
```rust
fn main() { 
	let x: (i32, f64, u8) = (500, 6.4, 1); 
	let five_hundred = x.0; 
	let six_point_four = x.1; 
	let one = x.2; 
}
```

## 配列
固定長かつ同じ型である必要がある
ヒープより吸ったくに書く方したい場合

標準ライブラリにvec型があり、困ったらそっち
```rust
let months = ["ja", "Febut"...]
```

注釈は
```rust
let a: [i32; 5] = [1, 2, 3, 4, 5]
```

初期値３　ながさ5の実装するなら
```rust
let a = [3; 5];
```

添え字でアクセスできる

```rust
fn main() {
	let a = [1, 2, 3, 4, 5];
	let first = a[0];
	let second = a[1];
}
```

# 関数
Rustの関数と変数の命名規則は　スネークケースが一般的

```rust
fn main() {
    print_labeled_measurement(5, 'h');
}

fn print_labeled_measurement(value: i32, unit_label: char) {
    println!("The measurement is: {}{}", value, unit_label);
}
```

## 戻り値がある関数

->で戻り値型を宣言する

```rust
fn main() {
    let x = plus_one(5);

    println!("The value of x is: {}", x);
}

fn plus_one(x: i32) -> i32 {
    x + 1
}
```

ただ、この戻り値関数plus_oneに;をつけると
```rust
fn main() {
    let x = plus_one(5);

    println!("The value of x is: {}", x);
}

fn plus_one(x: i32) -> i32 {
    x + 1;
}
```

これだと文になってしまうためエラーになる

# 式指向言語
rustは式指向言語である.
文の後ろには;
式にはつけない


文と式がある
```rust
fn main() {
    let y = 6;
}
```

let y = 6の部分は文、そもそも関数全体が文

文は値を返さないため以下のような記述はむり
```rust
fn main() { 
	let x = (let y = 6);
}
```

let y = 6は代入値を返さない分のため、 xに束縛するものがない

結果コンパイルエラーになる

このような場合
```rust
fn main() {
    let x = {
        let x = 3;
        x + 1
    };

    println!("{}", x);
}
```

文　x = 3で定義した後
式  x + 1で評価されている

# コメント
// でできる
```rust
fn main() {
	println!("hello") //hello
}
```

# if式
ifは式

```rust
fn main() {
	let number = 3;
	if number < 5 {
		println!("hello");
	} else {
		println!("hello");
	}
}
```

if式の条件式と紐付けられるコードは
アームとも呼ばれる

Rustでは、ifには条件式として、論理値を与える必要がある。

これではコンパイルエラーになる
```rust
fn main() {
	let number = 1;

	if number {
		println!("hello");
	}
}
```

これはok
```rust
fn main() {
	let number = 1;

	if number == 1{
		println!("hello");
	}
}
```

else や　else ifが使える
```rust
fn main() {
	let number = 1;

	if number == 1{
		println!("hello");
	} else if number == 2 {
		println!("he");
	} else {
		println!("hee");
	}
}
```

## let文内でif式を使う

これはできる
```rust
fn main() {
    let condition = true;

    let number = if condition { 5 } else { 6 };

    println!("the values {}", number);
}
```

これはできない
```rust
fn main() {
    let condition = true;

    let number = if condition { 5 } else { "six" };

    println!("the values {}", number);
}
```

ifアームとelseアーム、お互い同じ型じゃないといけない

# ループ
Rustには3種類のループが用意されている。

loop, while for

## loop
無限ループ
break, continueが使える
```rust
fn main() {
	loop {
		println!("hello");
	}
}
```

## while
条件が真の間、ループが走る

```rust
fn main() {
    let mut number = 3;

    while number != 0 {
        println!("{}!", number);

        number -= 1;
    }

    // 発射！
    println!("LIFTOFF!!!");
}
```

## for
配列の中身を取り出せる
```rust
fn main() {
    let a = [10, 20, 30, 40, 50];

    for element in a {
        println!("the value is: {}", element);
    }
}
```


Range型を使って、ループもできる
```rust
fn main() {
    for number in 1..4 {
        println!("{}", number);
    }
    println!("hello");
}
```

# 所有権
Rustといえばの概念

## スタックとヒープ
LIFOで、データを探す場所が固定なので早い
コンパイル時に、サイズがわからないものや、サイズがか可変の場合はヒープに格納

OSはヒープ上に十分な大きさの空の領域を見つけ、使用中にし、_ポインタ_を返します。ポインタとは、その場所へのアドレスです。 この過程は、ヒープに領域を確保する(allocating on the heap)_と呼ばれ、時としてそのフレーズを単に_allocateする_などと省略したりします。

コードが関数を呼び出すと、関数に渡された値(ヒープのデータへのポインタも含まれる可能性あり)と、 関数のローカル変数がスタックに載ります。関数の実行が終了すると、それらの値はスタックから取り除かれます。

## 所有権規則
- Rustの各値は、所有者と呼ばれる変数と対応している
- いかなる時も所有者は一つである
- 所有者がスコープから外れたら、値は破棄される。

## 変数スコープ
sがスコープに入ると有効になる。
逆にスコープから抜けると無効

```rust
fn main() {
	let s = "hello"; //sはここから有効
	
}//このスコープで無効
```


## String型
複雑な方は、ヒープに

stringもそう

文字リテラルからString型を生成できる。
```rust
let s = String::from("hello");
```

String型はmutにできる

```rust
fn main() {
	let mut s = String::from("hello");

	s.push_str(", world!"); // push_str()関数は、リテラルをStringに付け加える
	
	println!("{}", s); // これは`hello, world!`と出力する

}
```

GC付きの言語は、使用されないメモリを検知して片付けるため配慮しなくていい。
GCがない言語は、プログラマ自身がやる必要あるが解放するタイミングを正確にするのが大変

Rustは、メモリを所有している変数がスコープを抜けたら自動的に変換される

```rust
{
    let s = String::from("hello"); 
} //koko
```

スコープを抜けるとこに、dropと呼ばれる関数が呼ばれる

## ムーブ
例えば
```rust
let s1 = String::from("hello");
let s2 = s1;
```
とある時

s1は
![](/images/article-18/スクリーンショット%202025-03-03%2011.03.42.png)


こんな感じ

s2は、
![](/images/article-18/スクリーンショット%202025-03-03%2011.03.48.png)

こうなっている。

もし、s2がヒープデータをコピーしている場合
![](/images/article-18/スクリーンショット%202025-03-03%2011.03.53.png)
こんな感じになるが、これはs1が莫大なサイズの時に効率が悪すぎる。

そこでs2はポインタやlen, capacityをコピーしているが
![](/images/article-18/スクリーンショット%202025-03-03%2011.03.48.png)

メモリ解放の時、s2とs1がスコープを抜けたら同じメモリを解放してしまう。

二重解放エラー、意図しないメモリの書き換えが行われてしまう危険性がある。

これだと、
```rust
fn main() {
	let s1 = String::from("hello");
	let s2 = s1;
	
	println!("{}, world!", s1);
}
```

これだとムーブの後に使われているとエラーが出る。

つまり所有権が、s2に移る(moveしている)ってことかな

他の言語を触っている間に"shallow copy"と"deep copy"という用語を耳にしたことがあるなら、 データのコピーなしにポインタと長さ、許容量をコピーするという概念は、shallow copyのように思えるかもしれません。 ですが、コンパイラは最初の変数をも無効化するので、shallow copyと呼ばれる代わりに、 _ムーブ_として知られているわけです。

![](/images/article-18/スクリーンショット%202025-03-03%2011.12.34.png)

## クローン
もし、本当にs1のヒープデータが必要な場合(deep copy)はcloneと呼ばれるメゾットでできる。

```rust
fn main() {
	let s1 = String::from("hello");
	let s2 = s1.clone();
	
	println!("{}, {}, world!", s1, s2);
}
```

## 矛盾

```rust
fn main() {
	let x = 5;
	let y = x;
	
	println!("x = {}, y = {}", x, y);
}
```

これは、cloneしてないがxが有効で、yにyムーブされていない.。
余は、スタックに入るような軽量のデータはコピーするのも高速なため問題ない

Copyトレイトに適合している場合はok

ドキュメントで確認しよう。

例
- あらゆる整数型。`u32`など。
- 論理値型である`bool`。`true`と`false`という値がある。
- あらゆる浮動小数点型、`f64`など。
- 文字型である`char`。
- タプル。ただ、`Copy`の型だけを含む場合。例えば、`(i32, i32)`は`Copy`だが、 `(i32, String)`は違う。

## 所有権と関数

関数使う場合、
```rust
fn main() {
    let s = String::from("Hello"); //sがスコープ

    takes_ownership(s); //sが関数にムーブ
						//sは有効でない
						
    println!("{}", s); //これはだめ
    
	let x = 5; //xがスコープ
    makes_copy(x); //xも関数にムーブされるが、
				    //i32はcopy

    println!("{}", x) //これはok
}

fn takes_ownership(some_string: String) {
    println!("{}", some_string);
} 

fn makes_copy(some_integer: i32) {
    println!("{}", some_integer);
}
```

## 戻り値とスコープ

戻りがムーブされる。

```rust
fn main() {
    let s1 = gives_ownership();         // gives_ownershipは、戻り値をs1に
                                        // ムーブする

    let s2 = String::from("hello");     // s2がスコープに入る

    let s3 = takes_and_gives_back(s2);  // s2はtakes_and_gives_backにムーブされ
                                        // 戻り値もs3にムーブされる
} // ここで、s3はスコープを抜け、ドロップされる。s2もスコープを抜けるが、ムーブされているので、
  // 何も起きない。s1もスコープを抜け、ドロップされる。

fn gives_ownership() -> String {             // gives_ownershipは、戻り値を
                                             // 呼び出した関数にムーブする

    let some_string = String::from("hello"); // some_stringがスコープに入る

    some_string                              // some_stringが返され、呼び出し元関数に
                                             // ムーブされる
}

// takes_and_gives_backは、Stringを一つ受け取り、返す。
fn takes_and_gives_back(a_string: String) -> String { // a_stringがスコープに入る。

    a_string  // a_stringが返され、呼び出し元関数にムーブされる
}
```

タプルで、こんな感じに返したりできる
```rust
fn main() {
    let s1 = String::from("hello");

    let (s2, len) = calculate_length(s1);

    //'{}'の長さは、{}です
    println!("The length of '{}' is {}.", s2, len);
}

fn calculate_length(s: String) -> (String, usize) {
    let length = s.len(); // len()メソッドは、Stringの長さを返します

    (s, length)
}
```


しかし、関数に毎回ムーブされていてはらちが開かないため、Rustでは参照と呼ばれる概念がある。

# 参照と借用
値の所有権をもらう代わりに引数として、オブジェクトへの参照を取る
&で渡して、引数には&Stringで書く

```rust
fn main() {
	let s1 = String::from("hello");
	let len = calculate_length(&s1);

	println!("The length of '{}', is {}.", s1, len);
}

fn calculate_length(s: &String) -> usize (
	s.len()
)
```

![](/images/article-18/スクリーンショット%202025-03-03%2013.56.43.png)

借用した値を変更しようとすると

```rust
fn main() {
	let s1 = String::from("hello");
	let len = calculate_length(&s1);

	println!("The length of '{}', is {}.", s1, len);
}

fn calculate_length(s: &String) -> usize (
	s.push_str(", world");
	s.len()
)
```

エラーが出る。
これは借用先がmutではないため

可変な参照にするには、以下のようにする
```rust
fn main() {
	let mut s1 = String::from("hello");
	let len = calculate_length(&mut s1);

	println!("The length of '{}', is {}.", s1, len);
}

fn calculate_length(s: &mut String) -> usize (
	s.push_str(", world");
	s.len()
)
```

ただし、可変な参照には大きな制約がある

 特定のスコープで、ある特定のデータに対しては、 一つしか可変な参照を持てないことです。こちらのコードは失敗します

例えば、これはだめ
```rust
    let mut s = String::from("hello");

    let r1 = &mut s;
    let r2 = &mut s;

    println!("{}, {}", r1, r2);
```

これは、新しいスコープを作っているためOK
```rust
let mut s = String::from("hello");

{
    let r1 = &mut s;

} // r1はここでスコープを抜けるので、問題なく新しい参照を作ることができる

let r2 = &mut s;

```

可変と不変な参照を組み合わせることにも規則がある

これは、そりゃそうじゃって感じ
不変で使うこと見越してるのにmutで値変わるのは変だよね
```rust
let mut s = String::from("hello");

let r1 = &s; // 問題なし
let r2 = &s; // 問題なし
let r3 = &mut s; // 大問題！
```

## 宙に浮いた参照
ダングリングポインタを生成してしまいやすい。
ダングリングポインタは、他人に渡されてしまった可能性のあるメモリを指すポインタ

例
```rust
fn main() {
    let reference_to_nothing = dangle();
}

fn dangle() -> &String {
    let s = String::from("hello");

    &s
}
```

sのポインタを返しているが、sはスコープから抜けるため無効な参照先になる。
この場合は、Stringで所有権をムーブする必要がある。

# スライス
所有権のない別のデータ型は、スライス

例えば、文字列を受け取ってその文字列中の最初の単語を返す関数を書くとする。

```rust
fn first_word(s: &String) -> usize {
    let bytes = s.as_bytes();

    for (i, &item) in bytes.iter().enumerate() {
        if item == b' ' {
            return i;
        }
    }

    s.len()
}

```

```rust
fn main() {
    let mut s = String::from("hello world");

    let word = first_word(&s); // word will get the value 5
                               // wordの中身は、値5になる

    s.clear(); // this empties the String, making it equal to ""
               // Stringを空にする。つまり、""と等しくする

    // word still has the value 5 here, but there's no more string that
    // we could meaningfully use the value 5 with. word is now totally invalid!
    // wordはまだ値5を保持しているが、もうこの値を正しい意味で使用できる文字列は存在しない。
    // wordは今や完全に無効なのだ！
}
```

wordはsと紐づいていたのに、sがclearされた後も
wordは5となるのは困る

そこで、Rustには文字列スライスがある

## 文字列スライス

こんな見た目
```rust
    let s = String::from("hello world");

    let hello = &s[0..5];
    let world = &s[6..11];

```
イメージとしてはこれ
![](/images/article-18/スクリーンショット%202025-03-03%2014.24.36.png)
0から始めたい時は
```rust
let s = String::from("hello");

let slice = &s[0..2];
let slice = &s[..2];

```

最後の値を取る場合は
```rust
let s = String::from("hello");

let len = s.len();

let slice = &s[3..len];
let slice = &s[3..];

```

0..last
```rust
let s = String::from("hello");

let len = s.len();

let slice = &s[0..len];
let slice = &s[..];

```

これを知った上で、単語取り出しをやると
```rust
fn first_word(s: &String) -> &str {
    let bytes = s.as_bytes();

    for (i, &item) in bytes.iter().enumerate() {
        if item == b' ' {
            return &s[0..i];
        }
    }

    &s[..]
}

```

これですむ

ただし、これはだめ
```rust
fn main() {
    let mut s = String::from("hello world");

    let word = first_word(&s);

    s.clear(); // error! （エラー！）

    println!("the first word is: {}", word);
}
```

s.clearは値をclear、つまり可変参照しようとしている。 wordでは、&sと不変参照しているため無理

ここまでの話から、
### 文字列リテラルはスライス
```rust
let s = "Hello world!";
```
sの型は、&str　つまりバイナリの特定の位置を指すスライスである。そのため不変となっている。


first_wordの引数を文字列スライスにすれば
```rust
fn first_word(s: &str) -> &str {
```

String型も文字列リテラルも使えるようになる。
嬉しいね
```rust
fn main() {
    let my_string = String::from("hello world");

    // first_word works on slices of `String`s
    // first_wordは`String`のスライスに対して機能する
    let word = first_word(&my_string[..]);

    let my_string_literal = "hello world";

    // first_word works on slices of string literals
    // first_wordは文字列リテラルのスライスに対して機能する
    let word = first_word(&my_string_literal[..]);

    // Because string literals *are* string slices already,
    // this works too, without the slice syntax!
    // 文字列リテラルは「それ自体すでに文字列スライスなので」、
    // スライス記法なしでも機能するのだ！
    let word = first_word(my_string_literal);
}
```

もちろん配列のスライスもできる。
```rust
let a = [1, 2, 3, 4, 5];

let slice = &a[1..3];

```

このスライスは &[i32]となる

# 構造体
よくあるやつ

定義の仕方としては
```rust
struct User {
	username: String,
	email: String,
	sign_in_count: u64,
	active: bool,
}
```

このデータ片の名前と型を定義するところは、フィールドと呼ばれる。

構造体を使用するには、各フィールドに対して具体的な値を指定して、構造体のインスタンスを生成する。

```rust
let user1 = User {
    email: String::from("someone@example.com"),
    username: String::from("someusername123"),
    active: true,
    sign_in_count: 1,
};

```

特定の値を取得するには

```rust
let mut user1 = User {
    email: String::from("someone@example.com"),
    username: String::from("someusername123"),
    active: true,
    sign_in_count: 1,
};

user1.email = String::from("anotheremail@example.com");

```

インスタンス全体が可変ではなければならない。
一部のフィールドのみ可変などはできない。

また、構造体の新規インスタンスを関数本体の最後の式として生成して、そのインスタンスを返すことを明示できる

```rust
fn build_user(email: String, username: String) -> User {
    User {
        email: email,
        username: username,
        active: true,
        sign_in_count: 1,
    }
}
```

## 構造体初期化記法
フィールドと変数が同名の時に、フィールド初期化を省略記法でかける。
```rust
fn build_user(email: String, username: String) -> User {
    User {
        email,
        username,
        active: true,
        sign_in_count: 1,
    }
}
```

## 構造体更新記法

他のインスタンスの値を参照したい場合は、

```rust
let user2 = User {
    email: String::from("another@example.com"),
    username: String::from("anotherusername567"),
    active: user1.active,
    sign_in_count: user1.sign_in_count,
};

```

また、こんな書き方もできる。
```rust
let user2 = User {
    email: String::from("another@example.com"),
    username: String::from("anotherusername567"),
    ..user1
};

```

## タプル構造体
フィールド型だけのタプル構造体と呼ばれるものがある。

```rust
struct Color(i32, i32, i32);
struct Point(i32, i32, i32);

let black = Color(0, 0, 0);
let origin = Point(0, 0, 0);

```

一見ColorとPointとは同じ型だが、フィールド内が同じ型であっても別の型扱いになる。

## ユニット様構造体
一切フィールドのない構造体を定義することもできる。
ある型にトレイトを実装するが、型自体に保持させるデータは一切ない場面に有効。

## 構造体を使ったプログラム例
例えば、長方形の面積を求める関数を書くとき

```rust
fn main() {
    let width1 = 30;
    let height1 = 50;

    println!(
        // 長方形の面積は、{}平方ピクセルです
        "The area of the rectangle is {} square pixels.",
        area(width1, height1)
    );
}

fn area(width: u32, height: u32) -> u32 {
    width * height
}```

area関数の引数は2つあるが、引数は関連性があるのに関連性があることが明示されていない。

ここでタプルを使う。
```rust
fn main() {
    let rect1 = (30, 50);

    println!(
        "The area of the rectangle is {} square pixels.",
        area(rect1)
    );
}

fn area(dimensions: (u32, u32)) -> u32 {
    dimensions.0 * dimensions.1
}
```

ただし、これは明確性を失っている。

タプルを参照しているが、0, 1だとどっちが縦でどっちが横かわからない

そこで構造体でリファクタリングする

```rust
struct Rectangle {
    width: u32,
    height: u32,
}

fn main() {
    let rect1 = Rectangle { width: 30, height: 50 };

    println!(
        "The area of the rectangle is {} square pixels.",
        area(&rect1)
    );
}

fn area(rectangle: &Rectangle) -> u32 {
    rectangle.width * rectangle.height
}
```
これでより、明確になる。

## トレイトの導出で有用な機能を追加
デバッグ中に、インスタンスを出力したい
```rust
struct Rectangle {
    width: u32,
    height: u32,
}

fn main() {
    let rect1 = Rectangle { width: 30, height: 50 };

    // rect1は{}です
    println!("rect1 is {}", rect1);
}
```

error[E0277]: the trait bound `Rectangle: std::fmt::Display` is not satisfied
(エラー: トレイト境界`Rectangle: std::fmt::Display`が満たされていません)

基本の型は、標準でDisplayが実装されている。
しかし、構造体は、println!が出力を整形する方法が自明じゃなくなる。つまり、Display実装が提供されていない。

エラーをさらに読むと
`Rectangle` cannot be formatted with the default formatter; try using
`:?` instead if you are using a format string
(注釈: `Rectangle`は、デフォルト整形機では、整形できません; フォーマット文字列を使うのなら
代わりに`:?`を試してみてください)

これで実行してみると
```rust
println!("rect1 is {:?}", rect1);
```

`Rectangle` cannot be formatted using `:?`; if it is defined in your crate, add `#[derive(Debug)]` or manually implement it (注釈: `Rectangle`は`:?`を使って整形できません; 自分のクレートで定義しているのなら `#[derive(Debug)]`を追加するか、手動で実装してください)

となる。構造体で使えるようには、明示的な選択をしなければならない。
つまり、構造体定義前に注釈をつける。

```rust
#[derive(Debug)]
struct Rectangle {
    width: u32,
    height: u32,
}

fn main() {
    let rect1 = Rectangle { width: 30, height: 50 };

    println!("rect1 is {:?}", rect1);
}
```

これで出力すると、
rect1 is Rectangle { width: 30, height: 50 }
となる。

長方形の面積を求める関数は、rectangle構造体とより緊密に結びつけたい。

なので、area関数をareaメゾットにリファクタしてく。

## メゾット記法
implと宣言して書く
```rust
#[derive(Debug)]
struct Rectangle {
    width: u32,
    height: u32,
}

impl Rectangle {
    fn area(&self) -> u32 {
        self.width * self.height
    }
}

fn main() {
    let rect1 = Rectangle { width: 30, height: 50 };

    println!(
        "The area of the rectangle is {} square pixels.",
        rect1.area()
    );
}
```

areaのシグニチャでは、`rectangle: &Rectangle`の代わりに`&self`を使用している。
文脈で、selfがRectangleであると把握しているためselfで良い。

今回、selfの値は不変で良かったがもし可変がいい場合は、&mut selfにしてあげれば良い

## 参照及び参照はずし
Rustには、自動参照および参照外しがある。
これにより、通常のオブジェクトとポインタのオブジェクトを同じ書き方でメゾット呼び出せる。

## より引数の多いメゾット

大きさ比較するメゾット定義する。

```rust
fn main() {
    let rect1 = Rectangle { width: 30, height: 50 };
    let rect2 = Rectangle { width: 10, height: 40 };
    let rect3 = Rectangle { width: 60, height: 45 };

    // rect1にrect2ははまり込む？
    println!("Can rect1 hold rect2? {}", rect1.can_hold(&rect2));
    println!("Can rect1 hold rect3? {}", rect1.can_hold(&rect3));
}
```

```rust
impl Rectangle {
    fn area(&self) -> u32 {
        self.width * self.height
    }

    fn can_hold(&self, other: &Rectangle) -> bool {
        self.width > other.width && self.height > other.height
    }
}

```

or 
```rust
impl Rectangle {
    fn area(&self) -> u32 {
        self.width * self.height
    }

    fn can_hold(&self, other: &Rectangle) -> bool {
	    self.area > other.area()
    }
}
```

## 関連関数
implブロック内にselfを引数に取らない関数を定義できる。これは構造体に紐づけられているので、関連関数と呼ばれる。

例えば、String::fromも関連関数

関連関数は、構造体の新規インスタンスを返すコンストラクタによく使用される。
例えば、正方形を返すとなった時に
```rust
impl Rectangle {
    fn square(size: u32) -> Rectangle {
        Rectangle { width: size, height: size }
    }
}

```

この関連関数を呼び出すためには、
```rust
let sq = Rectangle::square(3);
```

とかく。この関数は、構造体によって名前空間分けがされている。
::という記法は、関連関数とモジュールによって作り出される名前空間両方に使用。

## 複数のimplブロック

これもok
```rust
impl Rectangle {
    fn area(&self) -> u32 {
        self.width * self.height
    }
}

impl Rectangle {
    fn can_hold(&self, other: &Rectangle) -> bool {
        self.width > other.width && self.height > other.height
    }
}

```

# Enum
列挙型で、取りうる値を列挙できる。

RustのenumはF#, OCal, Haskellなどの関数型言語に存在する代数的データ型に酷似している。

## Enumを定義する
例えば、IPアドレスを扱う様な場面。
IPアドレスの規格は2つあり、どんなIPアドレスもv4かv6になる。

enumで定義すると
```rust
enum IpAddrKind {
    V4,
    V6,
}
```

## Enumの値
各列挙子のインスタンスは以下のように生成できる
```rust
let four = IpAddrKind::V4;
let six = IpAddrKind::V6;
```


どんなIpAddrKindも取れる関数を定義できる
```rust
fn route(ip_type: IpAddrKind) { }
```

```rust
route(IpAddrKind::V4);
route(IpAddrKind::V6);
```

現状、このIPアドレス型はデータを保持する方法がない。
愚直にやろうとすると
```rust
enum IpAddrKind {
    V4,
    V6,
}

struct IpAddr {
    kind: IpAddrKind,
    address: String,
}

let home = IpAddr {
    kind: IpAddrKind::V4,
    address: String::from("127.0.0.1"),
};

let loopback = IpAddr {
    kind: IpAddrKind::V6,
    address: String::from("::1"),
};

```

しかし、enumには直接データを格納できる
```rust
enum IpAddr {
    V4(String),
    V6(String),
}

let home = IpAddr::V4(String::from("127.0.0.1"));

let loopback = IpAddr::V6(String::from("::1"));

```

便利

さらにEnumではstructではできない表現ができる。
```rust
enum IpAddr {
    V4(u8, u8, u8, u8),
    V6(String),
}

let home = IpAddr::V4(127, 0, 0, 1);

let loopback = IpAddr::V6(String::from("::1"));

```

こんなこともできる。
```rust
enum Message {
    Quit,
    Move { x: i32, y: i32 },
    Write(String),
    ChangeColor(i32, i32, i32),
}
```

Structでやろうとすると
```rust
struct QuitMessage; // ユニット構造体
struct MoveMessage {
    x: i32,
    y: i32,
}
struct WriteMessage(String); // タプル構造体
struct ChangeColorMessage(i32, i32, i32); // タプル構造体
```

こうなってしまう。

また、enumはimplが使える
```rust
impl Message {
    fn call(&self) {
        // method body would be defined here
        // メソッド本体はここに定義される
    }
}

let m = Message::Write(String::from("hello"));
m.call();
```

## Option
Rustにはnullがない。ふむ

nullの開発者による、nullの誘惑に負けて導入したがこれが10億ドルの苦痛や存在を引き起こしたであろうとのこと

Rustにはnullがないが、値が存在するか不在かという概念をコード化するenumがある。

```rust
enum Option<T> {
    Some(T),
    None,
}
```

Noneを使う時は型指定がいる
```rust
let some_number = Some(5);
let some_string = Some("a string");

let absent_number: Option<i32> = None;
```

なぜ、
```rust
Option<T>
```
の方がnullよりも好ましいのか

例えば、

```rust
let x: i8 = 5;
let y: Option<i8> = Some(5);

let sum = x + y;
```

これはエラーになる。

error[E0277]: the trait bound `i8: std::ops::Add<std::option::Option<i8>>` is not satisfied (エラー: `i8: std::ops::Add<std::option::Option<i8>>`というトレイト境界が満たされていません)

^ no implementation for `i8 + std::option::Option<i8>`

つまり、i8と
```rust
Option<i8>
```
は違う型なのでエラー。

T型の処理をしなければならないが、とりあえず型が入ってるためnullだが、そうでないように想定することができる。

値があるかどうかを型レベルで区別できるってのが良い

Some列挙子から値を取り出す方法は後述

## match制御フロー演算子

アメリカのコインを判定して、セントで返す関数を定義してみる
```rust
enum Coin {
    Penny,
    Nickel,
    Dime,
    Quarter,
}

fn value_in_cents(coin: Coin) -> u32 {
    match coin {
        Coin::Penny => 1,
        Coin::Nickel => 5,
        Coin::Dime => 10,
        Coin::Quarter => 25,
    }
}
```

if指揮と似ているが、ifでは式は論理値を返す必要が
あるがmatchはどんな型でも良い
また、matchアームには一本のアームに対して2つ部品がある。

パターンと何らかのコード
```rust
Coin::Penny => 1
```

複数行の場合は、
```rust
fn value_in_cents(coin: Coin) -> u32 {
    match coin {
        Coin::Penny => {
            println!("Lucky penny!");
            1
        },
        Coin::Nickel => 5,
        Coin::Dime => 10,
        Coin::Quarter => 25,
    }
}
```

## 値に束縛されるパターン
Quarterに複数種類がある場合
```rust
#[derive(Debug)] // すぐに州を点検できるように
enum UsState {
    Alabama,
    Alaska,
    // ... などなど
}

enum Coin {
    Penny,
    Nickel,
    Dime,
    Quarter(UsState),
}
```

クォーターコインに関連した州の名前を表示する
```rust
fn value_in_cents(coin: Coin) -> u32 {
    match coin {
        Coin::Penny => 1,
        Coin::Nickel => 5,
        Coin::Dime => 10,
        Coin::Quarter(state) => {
            println!("State quarter from {:?}!", state);
            25
        },
    }
}
```

`value_in_cents(Coin::Quarter(UsState::Alaska))`と呼び出すつもりだったなら、`coin`は `Coin::Quarter(UsState::Alaska)`になります。その値をmatchの各アームと比較すると、 `Coin::Quarter(state)`に到達するまで、どれにもマッチしません。その時に、`state`に束縛されるのは、 `UsState::Alaska`という値です。そして、`println!`式でその束縛を使用することができ、 そのため、`Coin` enumの列挙子から`Quarter`に対する中身のstateの値を取得できたわけです。

## OptionTとのマッチ
optionTを使用するとき、Someケースから中身のTの値を取得したい。
```rust
fn plus_one(x: Option<i32>) -> Option<i32> {
    match x {
        None => None,
        Some(i) => Some(i + 1),
    }
}

let five = Some(5);
let six = plus_one(five);
let none = plus_one(None);

```

matchとenumの組み合わせは有用
確かに

## マッチは包括的
バグがあるバージョンのplus_one
```rust
fn plus_one(x: Option<i32>) -> Option<i32> {
	match x {
		Some(i) => Some(i + 1),
	}
}
```

これはNoneの場合を扱っていないバグ

このようにRustにおけるマッチは、包括的

## というプレースホルダー
例えば、0から255を取る値で1, 3, 5, 7しか興味がない場合は
```rust
let some_u8_value = 0u8;
match some_u8_value {
    1 => println!("one"),
    3 => println!("three"),
    5 => println!("five"),
    7 => println!("seven"),
    _ => (),
}
```

この_というプレースホルダーを使えば、どんな値にもマッチする。
()の場合は何も起きない。

しかし、一つのケースしか興味がない場面ではif letが有用
## if let
例えば、matchを使って１つのパターンにマッチするとき

```rust
let some_u8_value = Some(0u8);
match some_u8_value {
    Some(3) => println!("three"),
    _ => (),
}
```

if letで一行で書ける
```rust
if let Some(3) = some_u8_value {
    println!("three");
}
```

また、elseも使える
```rust
let mut count = 0;
if let Coin::Quarter(state) = coin {
    println!("State quarter from {:?}!", state);
} else {
    count += 1;
}
```

# モジュール
私達がこれまでに書いてきたプログラムは、一つのファイル内の一つのモジュール内にありました。 プロジェクトが大きくなるにつれて、これを複数のモジュールに、ついで複数のファイルに分割することで、プログラムを整理することができます。 パッケージは複数のバイナリクレートからなり、またライブラリクレートを1つもつこともできます。 パッケージが大きくなるにつれて、その一部を抜き出して分離したクレートにし、外部依存とするのもよいでしょう。

- **パッケージ:** クレートをビルドし、テストし、共有することができるCargoの機能
- **クレート:** ライブラリか実行可能ファイルを生成する、木構造をしたモジュール群
- **モジュール** と **use:** これを使うことで、パスの構成、スコープ、公開するか否かを決定できます
- **パス:** 要素（例えば構造体や関数やモジュール）に名前をつける方法

## パッケージとクレート
クレートはバイナリかライブラリのどちらかです。 _クレートルート (crate root)_ とは、Rustコンパイラの開始点となり、クレートのルートモジュールを作るソースファイル

_パッケージ_ はある機能群を提供する1つ以上のクレートです。 パッケージは _Cargo.toml_ という、それらのクレートをどのようにビルドするかを説明するファイルを持っている。

cargo newでCargo.tomlが作られる。つまり、パッケージを作る

Cargoは _src/main.rs_ が、パッケージと同じ名前を持つバイナリクレートのクレートルートであるという慣習に従っているためです。 同じように、Cargoはパッケージディレクトリに _src/lib.rs_ が含まれていたら、パッケージにはパッケージと同じ名前のライブラリクレートが含まれており、_src/lib.rs_ がそのクレートルートなのだと判断します。 Cargoはクレートルートファイルを `rustc`に渡し、ライブラリやバイナリをビルドします。

今、パッケージは`my-project`という名前のバイナリクレートを持っている。
 もしパッケージが _src/main.rs_ と _src/lib.rs_ を持っていたら、クレートは2つになります

r`and`クレートが提供する機能にはすべて、クレートの名前`rand`を使ってアクセスできます。

_モジュール_ はクレート内のコードをグループ化し、可読性と再利用性を上げる

モジュールはmodでかく

```rust
mod front_of_house {
    mod hosting {
        fn add_to_waitlist() {}

        fn seat_at_table() {}
    }

    mod serving {
        fn take_order() {}

        fn serve_order() {}

        fn take_payment() {}
    }
}

```

ツリーとしては
```rust
crate
 └── front_of_house
     ├── hosting
     │   ├── add_to_waitlist
     │   └── seat_at_table
     └── serving
         ├── take_order
         ├── serve_order
         └── take_payment

```

こんな感じ

モジュールツリーの要素を示すためのパスは絶対パスと相対パスがある。

```rust
mod front_of_house {
    mod hosting {
        fn add_to_waitlist() {}
    }
}

pub fn eat_at_restaurant() {
    // Absolute path
    // 絶対パス
    crate::front_of_house::hosting::add_to_waitlist();

    // Relative path
    // 相対パス
    front_of_house::hosting::add_to_waitlist();
}
```

ただ、このままだと動かない。Rustは標準で全てprivateになっている。

そこでhostingモジュールにpubをつけてみる
```rust
mod front_of_house {
    pub mod hosting {
        fn add_to_waitlist() {}
    }
}

pub fn eat_at_restaurant() {
    // Absolute path
    // 絶対パス
    crate::front_of_house::hosting::add_to_waitlist();

    // Relative path
    // 相対パス
    front_of_house::hosting::add_to_waitlist();
}
```

しかし、これもエラーが吐く。結局fnがpubじゃないよねって話

```rust
mod front_of_house {
    pub mod hosting {
        pub fn add_to_waitlist() {}
    }
}

pub fn eat_at_restaurant() {
    // Absolute path
    // 絶対パス
    crate::front_of_house::hosting::add_to_waitlist();

    // Relative path
    // 相対パス
    front_of_house::hosting::add_to_waitlist();
}

```

これだとok


superで始まる相対パスを使って呼び出す
```rust
fn serve_order() {}

mod back_of_house {
    fn fix_incorrect_order() {
        cook_order();
        super::serve_order();
    }

    fn cook_order() {}
}
```

## 構造体とenumを公開
```rust
mod back_of_house {
    pub struct Breakfast {
        pub toast: String,
        seasonal_fruit: String,
    }

    impl Breakfast {
        pub fn summer(toast: &str) -> Breakfast {
            Breakfast {
                toast: String::from(toast),
                seasonal_fruit: String::from("peaches"),
            }
        }
    }
}

pub fn eat_at_restaurant() {
    // Order a breakfast in the summer with Rye toast
    // 夏 (summer) にライ麦 (Rye) パン付き朝食を注文
    let mut meal = back_of_house::Breakfast::summer("Rye");
    // Change our mind about what bread we'd like
    // やっぱり別のパンにする
    meal.toast = String::from("Wheat");
    println!("I'd like {} toast please", meal.toast);

    // The next line won't compile if we uncomment it; we're not allowed
    // to see or modify the seasonal fruit that comes with the meal
    // 下の行のコメントを外すとコンパイルできない。食事についてくる
    // 季節のフルーツを知ることも修正することも許されていないので
    // meal.seasonal_fruit = String::from("blueberries");
}
```

もしフィールドがenumだったら、これで行ける
```rust
mod back_of_house {
    pub enum Appetizer {
        Soup,
        Salad,
    }
}

pub fn eat_at_restaurant() {
    let order1 = back_of_house::Appetizer::Soup;
    let order2 = back_of_house::Appetizer::Salad;
}

```

## use
useを使えば、簡単になる

こんな感じ
```rust
mod front_of_house {
    pub mod hosting {
        pub fn add_to_waitlist() {}
    }
}

use crate::front_of_house::hosting;

pub fn eat_at_restaurant() {
    hosting::add_to_waitlist();
    hosting::add_to_waitlist();
    hosting::add_to_waitlist();
}
```

`use crate::front_of_house::hosting`をクレートルートに追加することで、`hosting`はこのスコープで有効な名前となる

相対パス使ってスコープに持ち込む
```rust
mod front_of_house {
    pub mod hosting {
        pub fn add_to_waitlist() {}
    }
}

use self::front_of_house::hosting;

pub fn eat_at_restaurant() {
    hosting::add_to_waitlist();
    hosting::add_to_waitlist();
    hosting::add_to_waitlist();
}

```

## useの慣例
なぜ、add_to_waitlistをuseパスにしないのか
```rust
mod front_of_house {
    pub mod hosting {
        pub fn add_to_waitlist() {}
    }
}

use crate::front_of_house::hosting::add_to_waitlist;

pub fn eat_at_restaurant() {
    add_to_waitlist();
    add_to_waitlist();
    add_to_waitlist();
}
```

これは、add_to_waitlistがどこで定義されているかが不明瞭。慣例としては親モジュールを`use`で持ち込む

一方、構造体やenumその他の要素をuseで持ち込むときは、フルパスを書くのが慣例的

```rust
use std::collections::HashMap;

fn main() {
    let mut map = HashMap::new();
    map.insert(1, 2);
}
```

理由は特にない。

同じ名前のものは呼び出せないため、親モジュールを指定する
```rust
use std::fmt;
use std::io;

fn function1() -> fmt::Result {
    // --snip--
    // （略）
}

fn function2() -> io::Result<()> {
    // --snip--
    // （略）
}
```

## as
asを使えば、名前問題を解決できる
```rust
use std::fmt::Result;
use std::io::Result as IoResult;

fn function1() -> Result {
    // --snip--
}

fn function2() -> IoResult<()> {
    // --snip--
}
```

## 名前の再公開
pub useを使うと名前を再公開できる
```rust
mod front_of_house {
    pub mod hosting {
        pub fn add_to_waitlist() {}
    }
}

pub use crate::front_of_house::hosting;

pub fn eat_at_restaurant() {
    hosting::add_to_waitlist();
    hosting::add_to_waitlist();
    hosting::add_to_waitlist();
}

```

## 巨大なuseのリストをネストしたパスで整理
これを
```rust
// --snip--
// （略）
use std::cmp::Ordering;
use std::io;
// --snip--
// （略）
```


こうできる
```rust
// --snip--
// （略）
use std::{cmp::Ordering, io};
// --snip--
// （略）
```

また、
```rust
use std::io;
use std::io::Write;
```

これは
```rust
use std::io::{self, Write};
```


## glob演算子
全ての公開要素持ち込みたい時は* でできるが、わかりにくくなるため注意
```rust
use std::collections::*;
```

テストの時に使う

## モジュールを複数のファイルに分割する

今まで同一ファイルで書いていたものを分割する

src/lib.rs
```rust
mod front_of_house;

pub use crate::front_of_house::hosting;

pub fn eat_at_restaurant() {
    hosting::add_to_waitlist();
    hosting::add_to_waitlist();
    hosting::add_to_waitlist();
}
```

src/front_of_house.rs
```rust
pub mod hosting;
```

src/front_of_house/hosting.rs

```rust
pub fn add_to_waitlist() {}
```

# コレクション
コレクションの値はヒープに確保される。

# ベクタ
Vect<>である。単体のデータ構造ながら、複数の値を保持できる。ベクタは同じ型の値しか保持できない。

## 新しいベクタを生成

```rust
let v: Vec<i32> = Vec::new();
```

ベクタはジェネリクスを使用して実装されている。どんな型でも保持できる。

これもできる
```rust
let v = vec![1, 2, 3];
```

扱いは上と同じ

## ベクタを更新する
要素追加する時はmut つけて、push

```rust
let mut v = Vec::new();

v.push(5);
v.push(6);
v.push(7);
v.push(8);
```

## ベクタをドロップ、要素もドロップする
こんな感じ
```rust
{
	let v = vec![1, 2, 3, 4];

	// vで作業をする
} // <- vはここでスコープを抜け、解放される
```

## ベクタの要素を読む
中身を読むには添え字かgetで行ける
```rust
let v = vec![1, 2, 3, 4, 5];

let third: &i32 = &v[2];
println!("The third element is {}", third);

match v.get(2) {
	//                      "3つ目の要素は{}です"
	Some(third) => println!("The third element is {}", third),
	//               "3つ目の要素はありません。"
	None => println!("There is no third element."),
}
```

```rust
let v = vec![1, 2, 3, 4, 5];

let does_not_exist = &v[100];
let does_not_exist = v.get(100);
```
getの場合は、パニックすることなくNoneを返す。


もし、pushで可変借用が発生すると不変じゃなくなるため無理
```rust
let mut v = vec![1, 2, 3, 4, 5];

let first = &v[0];

v.push(6);

println!("The first element is: {}", first);
```

これはベクタ型が全ての要素を隣り合わせに配置しようとして、もし空きスペースがなかった場合新しいメモリを割り当てるという使用のため

## ベクタ内の値を順に処理する
順番に要素にアクセスする場合はforループ
```rust
let v = vec![100, 32, 57];
for i in &v {
	println!("{}", i);
}
```

また、全要素に変更を加えるために可変なベクタへの参照もできる
```rust
let mut v = vec![100, 32, 57];
for i in &mut v {
	*i += 10;
}
```

その場合は、参照外し演算子が必要

## Enumを使って複数の型を保持
もし、異なる型のvecが作りたいときはenumを使う
```rust
enum SpreadsheetCell {
	Int(i32),
	Float(f64),
	Text(String),
}

let row = vec![
	SpreadsheetCell::Int(3),
	SpreadsheetCell::Text(String::from("blue")),
	SpreadsheetCell::Float(10.12),
];
```

## pop
最後の要素を削除して返す。

# String




































































































