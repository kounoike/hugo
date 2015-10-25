---
date: 2015-10-25T14:21:02+09:00
draft: false
slug: ""
socialsharing: true
tags:
- "chinachu"
- "ubuntu"
title: Chinachuのサービス化を簡単に書く（Ubuntu/upstart限定）
---
upstartを使うとサービスの記述が簡単です。chinachu-wuiとchinachu-operatorの記述をしてみました。
<!--more-->
/etc/init/chinachu-wui.confが以下の通り。（YOUR NAME/YOUR EMAILは適当に置き換えて）
```
description "Chinachu - WUI"
author "YOUR NAME<YOUR EMAIL>"

start on runlevel [2345]
stop on runlevel [016]

setuid chinachu
setgid chinachu

chdir /home/chinachu/chinachu
exec ./chinachu service wui execute
respawn
```

/etc/init/chinachu-operator.confもほぼ同じ。
```
description "Chinachu - operator"
author "YOUR NAME <YOUR EMAIL>"

start on runlevel [2345]
stop on runlevel [016]

setuid chinachu
setgid chinachu

chdir /home/chinachu/chinachu
exec ./chinachu service operator execute
respawn
```

この2つのファイルを/etc/initに置いたら`sudo initctl reload-configuration`して認識させる。
で、再起動するか、`sudo start chinachu-wui`、`sudo start chinachu-operator`するだけ。

/etc/init.dに書くのに比べてこんなに簡単なんてびっくりだ。
