---
layout: post
title: "Octopress nasıl kullanılır?"
date: 2013-02-24 13:13
comments: true
author: Onur Özgür ÖZKAN
categories: octopress
---
Belli bir süre Octopress'i mercek altına alacağız.

<!-- more -->

## Yeni post yazma

```
rake new_post["title"]
```

`source` brancheında `_post` dizininde yeni dosyayı görüyoruz. Buradan author, categories'i düzenliyoruz.

```
---
layout: post
title: "Octopress nasıl kullanılır?"
date: 2013-02-24 13:13
comments: true
author: Onur Özgür ÖZKAN
categories: octopress
---
```

Sonra markdown stadanartında makalemizi yazıyoruz.

## Generate ve Preview işlemleri

```
rake generate   # Generates posts and pages into the public directory
rake watch      # Watches source/ and sass/ for changes and regenerates
rake preview    # Watches, and mounts a webserver at http://localhost:4000
```

## Deploy etmeyi unutmayın

Publish için

```
rake generate
rake deploy
```

Source kodlarıda source branchına gönderiyoruz.

```
git add .
git commit -m 'your message'
git push origin source
```

Şimdilik bu kadar...