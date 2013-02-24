---
layout: post
title: "Ruby On Rails REST API'de Access Token Kullanarak Kullanıcı Doğrulama"
date: 2012-11-27 17:23
author: Muhammet DİLEK
comments: true
categories: rails api rails-api authorization authentication ruby-on-rails
---
Öncelikle; 

* `HTTPS` kullanın. Çünkü `HTTP` kullanıldığında  parametreler plain text olarak iletiliyor. `Http Request`' leri dinleyen birisi keylerinize çok rahat ulaşabilir.

* `access_token`' ları url den parametre olarak almak yerine `Http Header`' dan almak daha uygundur. Çünkü kullanıcı alışkanlıkları  (kopyala-yapıştır-test et) yönündedir. URL de unutulan tokenlar güvenlik için tehlikeli olabilir.

Kullanıcı doğrulama ile ilgili daha önce yazdığım [Ruby On Rails REST API' de HTTP Basic Authentication Kullanarak Kullanıcı Doğrulama
](http://muhammetdilek.dudupress.com/articles/ruby-on-rails-rest-api-de-http-basic-authentication-kullanarak-kullanici-dogrulama) yazısını okuyabilirsiniz.
<!-- more -->
`API`' de güvenliği sağlamanın diğer bir yolu ise `API`' yi kullanacak client' lara bir `API_KEY` verip o `API_KEY` ile authenticate yapmak. Bu yöntemi kullanırsak `API_KEY` leri saklamak için bir modele ihtiyacımız olacak. 
Bu modelde;

* `user_id`
* `access_token`
* `expires_at` 
alanlarını tutacağız. Eğer `API_KEY` ömrünü kısıtlamak istemiyorsanız `expires_at` alanına ihtiyacınız yok.

Ben burada örneği kısa tutmak için `user_id` ve `expires_at` alanlarını kullanmayacağım.

```bash
Muhammets-iMac:pigon muhammetdilek$ rails g model api_key access_token:string
      invoke  active_record
      create    db/migrate/20121006130334_create_api_keys.rb
      create    app/models/api_key.rb
      invoke    test_unit
      create      test/unit/api_key_test.rb
      create      test/fixtures/api_keys.yml
```

```ruby
class ApiKey < ActiveRecord::Base
  before_create :generate_access_token

  private

  def generate_access_token
    begin
      self.access_token = SecureRandom.hex
    end while self.class.exists?(access_token: access_token)
  end
end
```

Burada `access_token` üretmek için `ruby 1.9`' la gelen `SecureRandom.hex` metodunu kullanarak 16 haneden oluşan `string` bir `access_token` oluşturuyoruz. Eğer oluşturulan bu `access_token` daha önceden oluşturulmuş ise kod daha önceden oluşturulmayan bir `access_token` üretinceye kadar tekrar çalışıyor.

Şimdi bir ApiKey oluşturup devam edelim.

```bash
Muhammets-iMac:pigon muhammetdilek$ rails c
Loading development environment (Rails 3.2.8)
1.9.3p194 :001 > ApiKey.create
   (0.0ms)  begin transaction
  ApiKey Exists (0.1ms)  SELECT 1 AS one FROM "api_keys" WHERE "api_keys"."access_token" = '765582c8bf829e682abe70618957a42a' LIMIT 1
Binary data inserted for `string` type on column `access_token`
  SQL (35.5ms)  INSERT INTO "api_keys" ("access_token", "created_at", "updated_at") VALUES (?, ?, ?)  [["access_token", "765582c8bf829e682abe70618957a42a"], ["created_at", Sat, 06 Oct 2012 13:11:59 UTC +00:00], ["updated_at", Sat, 06 Oct 2012 13:11:59 UTC +00:00]]
   (2.0ms)  commit transaction
 => #<ApiKey id: 1, access_token: "765582c8bf829e682abe70618957a42a", created_at: "2012-10-06 13:11:59", updated_at: "2012-10-06 13:11:59"> 
1.9.3p194 :002 >
```

`Controller`' a `access_token` kontrolü yapan bir metod yazacağız;

```ruby
module Api
  module V2
    class ProductsController < ApplicationController
      include ActionController::MimeResponds
      include ActionController::HttpAuthentication::Basic::ControllerMethods
      #http_basic_authenticate_with name: 'admin', password: 'secret'
      before_filter :restrict_access

      respond_to :json, :xml

      #Actions

      private

      def restrict_access
        api_key = ApiKey.find_by_access_token(params[:access_token])
        head :unauthorized unless api_key
      end
    end
  end
end
```

`http://pigon.dev/api/products` adresini açtığımızda bize hiçbir datanın dönmediğini görüyoruz. Birde query olarak `access_token` ekleyerek bakalım;
![API_KEY](https://dl.dropbox.com/u/44821222/access-token.png)
Evet gördüğünüz gibi urlden `acces_token` alarak datalara erişim kısıtlaması getirebiliyoruz. `access_token`' bilgilisini `URL`' den almak yerine `HTTP Header` dan alalım.

`Rails`' te `HTTP header`' dan `access_token` almak için `authenticate_or_request_with_http_token ` metodunu kullancağız. 

```ruby
def restrict_access
  #api_key = ApiKey.find_by_access_token(params[:access_token])
  #head :unauthorized unless api_key
  authenticate_or_request_with_http_token do |token, options|
    ApiKey.exists?(access_token: token)
  end
end
```

Eğer [`Rails::API`](https://github.com/rails-api/rails-api) gem' ini kullanıyorsak `ActionController::HttpAuthentication::Token::ControllerMethods` modülünü `Controller`' a eklememiz gerekiyor.

```bash
Muhammets-iMac:pigon muhammetdilek$ curl http://pigon.dev/api/products -H 'Authorization: Token token="xyz"'
HTTP Token: Access denied.
Muhammets-iMac:pigon muhammetdilek$ 
```

```bash
Muhammets-iMac:pigon muhammetdilek$ curl http://pigon.dev/api/products -H 'Authorization: Token token="765582c8bf829e682abe70618957a42a"'
[{"amount":"4.3","created_at":"2012-10-06T09:58:47Z","id":2,"title":"Product2","updated_at":"2012-10-06T09:58:47Z"},{"amount":"15.0","created_at":"2012-10-06T09:59:14Z","id":3,"title":"Product3Update","updated_at":"2012-10-06T09:59:24Z"}]Muhammets-iMac:pigon muhammetdilek$ 
```
Komut satırından baktığımızda kodlarımızın çalıştığını görüyoruz.

İyi çalışmalar.

Kaynak: [RailsCasts](http://railscasts.com/episodes/352-securing-an-api)
