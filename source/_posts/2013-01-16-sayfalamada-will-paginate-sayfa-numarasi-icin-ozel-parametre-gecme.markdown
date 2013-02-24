---
layout: post
title: "Sayfalamada (will_paginate) Sayfa Numarası İçin Özel Parametre Geçme"
date: 2012-10-10 15:05
author: Muhammet DİLEK
comments: true
categories: rails paginate will_paginate ruby-on-rails sayfalama
---
Sayfalama için [`will_paginate`](https://github.com/mislav/will_paginate) gemini kullanıyoruz. Bazı durumlarda sayfada iki ayrı collection' a göre sayfala yapmamız gerekiyor. Dolayısıyla `page` parametresini her iki collection için kullanamıyoruz. Böyle durumlarda kendimiz parametre verebiliriz;

```ruby
# views
= will_paginate @credits, param_name: :credit_page, page_links: false

# Controller
@credits = @credits.paginate(:page => params[:credit_page])
```

İyi çalışmalar
