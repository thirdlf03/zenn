---
published: true
cssclasses:
  - zenn
type: tech
emoji: 🐦
title: Discordのフォーラムチャンネルの投稿を通知してくれるbot作成
topics: 
date: 2025-01-07
AutoNoteMover: disable
url: https://zenn.dev/thirdlf/articles/08-zenn-discord-forum-notify
tags:
  - go
aliases:
---
# 概要
Discordのフォーラムチャンネル便利なんですが、欠点として投稿した時に通知がないため投稿されているのに気づかない事があります。
これを解決するためにbotに通知させようと思い作りました。

誤字ってて泣いた
![](/images/article-8/08-image.png)

# 技術
使用言語は、Goです。

使っているライブラリはこれです。
https://pkg.go.dev/github.com/bwmarrin/discordgo

# 書いたコード
```go

func forumNotify(s *discordgo.Session, c *discordgo.MessageCreate) {
	Channel, err := s.Channel(c.ChannelID)
	errors.Catch(err, "cannot get channel")

	if Channel.Type == discordgo.ChannelTypeGuildPublicThread && Channel.ParentID == "ForumID" {
		name := c.Author.Username
		url := fmt.Sprintf("https://discord.com/channels/%s/%s", c.ChannelID, c.Message.ID)
		emoji, err := s.State.Emoji(c.GuildID, EmojiID)
		errors.Catch(err, "cannot get emoji")

		messages, err := s.ChannelMessages(c.ChannelID, 100, "", "", "")
		errors.Catch(err, "cannot get messages")

		Forum, err := s.Channel(Channel.ParentID)
		errors.Catch(err, "cannot get forum channel")

		tagList := Forum.AvailableTags

		var tags string
		for _, tagID := range Channel.AppliedTags {
			for _, tag := range tagList {
				if tag.ID == tagID {
					tags += fmt.Sprintf("#%s ", tag.Name)
				}
			}
		}

		if len(messages) <= 1 {
			_, err = s.ChannelMessageSend(ChannelID, name+"さんが記事が投稿したよ! すごい！！！！"+emoji.MessageFormat()+"\n"+url+tags)
			errors.Catch(err, "cannot send message")
		}
	}
}
```

# 解説
## フォーラムのメッセージかどうかチェック

```go
if Channel.Type == discordgo.ChannelTypeGuildPublicThread && Channel.ParentID == "ForumID"
```
フォーラムの投稿は、メッセージ扱いでChannelTypeGuildPublicThreadを持っているので
ChannelTypeGuildPublicThreadで絞っています。
また、特定のフォーラムに投稿されたものだけ通知させたかったため、フォーラムのメッセージが持っている親IDとフォーラムのIDを比較しています。

## Tagの取得

```go
Forum, err := s.Channel(Channel.ParentID)  
errors.Catch(err, "cannot get forum channel")  
  
tagList := Forum.AvailableTags  
  
var tags string  
for _, tagID := range Channel.AppliedTags {  
    for _, tag := range tagList {  
       if tag.ID == tagID {  
          tags += fmt.Sprintf("#%s ", tag.Name)  
       }  
    }  
}
```
Channelが持っているAppliedTagsはtagIdを返すだけなので、
投稿に紐づいているTag名を取得するには、フォラームのAvailableTagsから参照する必要があります。

## 新規で作成しているか確認
```go
messages, err := s.ChannelMessages(c.ChannelID, 100, "", "", "")  
errors.Catch(err, "cannot get messages")  
  
if len(messages) <= 1 {  
    _, err = s.ChannelMessageSend("ChannelID", name+"さんが記事が投稿したよ! すごい！！！！"+emoji.MessageFormat()+"\n"+url+tags)  
    errors.Catch(err, "cannot send message")  
}
```
今回、フォーラムに新しいものが投稿された時に通知して欲しかったため、一つの目のメッセージかどうか確認しています。

## Emoji
```go
emoji, err := s.State.Emoji(c.GuildID, EmojiID)
errors.Catch(err, "cannot get emoji")

_, err = s.ChannelMessageSend(ChannelID, emoji.MessageFormat())
```
絵文字を投稿する



