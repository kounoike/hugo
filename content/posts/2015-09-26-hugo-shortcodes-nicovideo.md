---
date: 2015-09-26T00:53:15+09:00
draft: false
slug: ""
tags:
- "Hugo"
- "nicovideo"
title: Hugoのshortcodesでnicovideoの埋め込みを簡単にする
socialsharing: true
---
Hugoには[shortcodes](https://gohugo.io/extras/shortcodes/)という仕組みで、カスタムタグみたいなことを実現できるみたいです。
そこで、ニコニコ動画の動画サムネイルやプレイヤーの埋め込みを行うshortcodesを書いてみました。

<!--more-->

# 動画サムネイル

layouts/shortcodes/nicovideo.htmlとして、以下をおきます。

```html
<iframe width="312" height="200" src="http://ext.nicovideo.jp/thumb/{{ .Get 0 }}" scrolling="no" style="border:solid 1px #CCC;" frameborder="0">
</iframe>
```

記事のMarkdownでは以下のように書きます。

```markdown
｛｛<nicovideo sm9 >｝｝
```

・・・code fenceの中でさえshortcodesの置き換えが利いちゃうので、全角｛を半角に書き換えてください。

こうなります。
{{< nicovideo sm9 >}}

# 動画プレイヤー

同じnicovideoにして、ifで分岐させようと思ったのですが、色々やってもうまくいかなかったので別の名前のshortcodesにしちゃいました。

```html
{{ $sm := .Get 0 }}
{{ $url := printf "http://api.ce.nicovideo.jp/nicoapi/v1/video.info?v=%s&__format=json" $sm }}
{{ $dataJ := getJSON $url }}
{{ $title := $dataJ.nicovideo_video_response.video.title }}
<script type="text/javascript" src="http://ext.nicovideo.jp/thumb_watch/{{ $sm }}?w=490&amp;h=307"></script>
<noscript><a href="http://www.nicovideo.jp/watch/{{ $sm }}">{{ $title }}</a></noscript>
```

使い方はこう。

```markdown
｛｛<nicovideoplayer sm9 >｝｝
```

こうなります。
{{<nicovideoplayer sm9>}}
