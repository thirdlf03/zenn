---
published: false
cssclass: zenn
type: "<% await tp.system.suggester(['tech', 'idea'], ['tech', 'idea'], false, '記事のタイプを選択') %>"
emoji: "<% await tp.system.prompt('絵文字を１つ入力', '🔥') %>"
<%*
const title = await tp.system.prompt('記事タイトルを入力')
%>
title: "<%* tR += title  %>"
topics: []
date: <% tp.date.now("YYYY-MM-DD") %>
AutoNoteMover: disable
url: "https://zenn.dev/thirdlf/articles/<% tp.file.title %>"
tags: [" #type/zenn "]
aliases: 
---
