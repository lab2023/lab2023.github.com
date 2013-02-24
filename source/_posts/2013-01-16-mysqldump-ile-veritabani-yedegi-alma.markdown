---
layout: post
title: "mysqldump ile veritabanı yedeği alma"
date: 2012-10-22 15:07
author: Muhammet DİLEK
comments: true
categories: mysql mysqldump backup
---
Bugün sunucu üzerinde işlemler yaparken ne olur olmaz deyip mysql yedeğini alayım dedim. Aşağıdaki komut satırı ile veritabanı yedeğini alabiliyormuşuz.

`mysqldump -u username -p database> backup_20121022.sql`

`mysql` şifresini girdikten sonra bulunduğumuz dizine `backup_20121022.sql` isminde bir dosyaya veritabanı yedeğini alıyor.

İyi çalışmalar.
