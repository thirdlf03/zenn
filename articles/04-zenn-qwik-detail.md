---
published: false
cssclasses:
  - zenn
type: tech
emoji: 🔍
title: Qwikの書き方を学ぶ
topics: 
date: 2024-12-24
AutoNoteMover: disable
url: https://zenn.dev/thirdlf/articles/04-zenn-qwik-detail
tags:
  - "#type/zenn"
  - Qwik
aliases:
---
Qwikの速さに感動したので、よりパフォーマンスを出せるようにQwikの書き方を勉強したい。
ということで、ドキュメント読みながら勉強したことをまとめます。

https://qwik.dev/docs/

# 注意
- 使っているQwikのversionは1.12.0です
- 間違っている情報があるかもしれないので、気にせず指摘お願いします

それでは、let's go~

# コンポーネント編
---
## 基本的なコンポーネント

component$で、基本的なコンポーネントを書く。
```tsx
import { component$ } from '@builder.io/qwik';

export default component$(() => {
	return <div>Hello World!</div>;
});

```

## $
$がついているは、Lazy Loadingされるやつ。
これがQwikの速さを支えている。

## inline or $ ?
Inline componentというものがある。
```tsx
import { component$ } from '@builder.io/qwik'; 
// Inline component: declared using a standard function.
export const MyButton = (props: { text: string }) => {
	return <button>{props.text}</button>;
};
// Component: declared using `component$()`.
export default component$(() => {
	return (
		<p> 
			Some text:
			<MyButton text="Click me" />
		</p> 
	);
});

```
親コンポーネントのバンドルに含まれるので、軽量コンポーネントじゃないと
パフォーマンスに影響が出てしまう。

また、inline componentには制限があり
- Cannot use `use*` methods such as `useSignal` or `useStore`.
- Cannot project content with a `<Slot>`.
とのこと。

IconやButtonなどの軽量で状態管理が必要ないコンポーネントはinline。
状態管理が必要だったり、重たいコンポーネントはcomponent$で読み込むのが
良さそう。


