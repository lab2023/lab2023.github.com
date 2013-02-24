---
layout: post
title: "Ruby On Rails ile Rails::API Gem'ini Kullanarak API Geliştirme"
date: 2012-11-24 17:25
author: Muhammet DİLEK
comments: true
categories: rails api rails-api rest ruby-on-rails
---

Daha önce API geliştirme ile ilgili [Ruby On Rails’ te API Yazalım](http://www.gelistiricigunlugu.com/ruby-on-rails-te-api-yazalim/) başlıklı bir yazı yazmıştım. Bu yazımda ise `Rails::API` gem' ini kullanarak `API` geliştirmeyi anlatacağım.

`Rails::API` bir `API` için gerekli olan kütüphaneler dışındaki kütüphaneleri çıkararak(view, assests ...), normal bir rails projesine göre daha hafif bir proje oluşturabilmemizi sağlayan gem.

Aşağıdaki komutu çalıştırarak gem' i yükleyelim

`gem install rails-api`

<!-- more -->

Yeni proje oluşturmak için aşağıdaki komutu kullanacağız. 

`rails-api new pigon`

Komutu çalışıtrdıktan sonra projenin dosyalarına baktığımızda `app` dizininin çok sade olduğunu görüyoruz. API geliştirme ihtiyacı dışındaki şeyler(views, helpers, assets) atılmıştır. `ApplicationController`' a baktığımızda `ActionController::Base` yerine daha hafif olan `ActionController::API`' den türetildiğini görüyoruz.

```ruby
class ApplicationController < ActionController::API
end
```

En büyük değişiklik ise `Gemfile` dosyamızda. `rails-api` gem' ini buraya eklenmiş olarak görüyoruz fakat `assets` grubundaki gem' lerin ve `jquery-rails` gem' inin eklenmediğini görüyoruz.

```ruby
source 'https://rubygems.org'

gem 'rails', '3.2.8'

# Bundle edge Rails instead:
# gem 'rails', :git => 'git://github.com/rails/rails.git'

gem 'rails-api'

gem 'sqlite3'
```

Peki generator' larda nasıl bir değişiklik oluyor?. `scaffold` ile `Product` modeli oluşturduğumuzda `assets`, `views` ve `helpers` dosyalarının oluşturulmadığını görüyoruz. 

```bash
Muhammets-iMac:pigon muhammetdilek$ rails g scaffold Product title:string price:decimal
      invoke  active_record
      create    db/migrate/20121003130313_create_products.rb
      create    app/models/product.rb
      invoke    test_unit
      create      test/unit/product_test.rb
      create      test/fixtures/products.yml
      invoke  api_resource_route
       route    resources :products, except: :edit
      invoke  scaffold_controller
      create    app/controllers/products_controller.rb
      invoke    test_unit
      create      test/functional/products_controller_test.rb
```

Gördüğünüz gibi `model`, `controller` ve `test` dosyalarından oluşan bir yapı oluşturuldu. Şimdi migration' ı çalıştıralım.

```bash
Muhammets-iMac:pigon muhammetdilek$ rake db:migrate
```

`scaffold` tarafından generate edilen `ProductsController`' ına baktığımızda sadece `JSON`formatında veri döndürüldüğünü göreceksiniz.

```ruby
class ProductsController < ApplicationController
  # GET /products
  # GET /products.json
  def index
    @products = Product.all

    render json: @products
  end

  # GET /products/1
  # GET /products/1.json
  def show
    @product = Product.find(params[:id])

    render json: @product
  end

  # GET /products/new
  # GET /products/new.json
  def new
    @product = Product.new

    render json: @product
  end

  # POST /products
  # POST /products.json
  def create
    @product = Product.new(params[:product])

    if @product.save
      render json: @product, status: :created, location: @product
    else
      render json: @product.errors, status: :unprocessable_entity
    end
  end

  # PATCH/PUT /products/1
  # PATCH/PUT /products/1.json
  def update
    @product = Product.find(params[:id])

    if @product.update_attributes(params[:product])
      head :no_content
    else
      render json: @product.errors, status: :unprocessable_entity
    end
  end

  # DELETE /products/1
  # DELETE /products/1.json
  def destroy
    @product = Product.find(params[:id])
    @product.destroy

    head :no_content
  end
end
```

Uygulamamızın hiç `HTML` view renderlemediğini sadece `JSON` renderlediğini görüyoruz. Tarayıcımızda `/products` yolunu ziyaret ettiğimizde `JSON` formatında henüz ürün eklemediğimiz için boş bir `array` renderlediğini göreceğiz.

`curl` ile uygulamamıza product ekleyelim.

```bash
Muhammets-iMac:pigon muhammetdilek$ curl -d "product[title]=Product1&product[price]=3.3" http://pigon.dev/products
{"created_at":"2012-10-03T13:27:10Z","id":3,"price":"3.3","title":"Product1","updated_at":"2012-10-03T13:27:10Z"}
```

`/products` adresini ziyeret ettiğimizde

```json
[
    {
        created_at: "2012-10-03T13:28:27Z",
        id: 1,
        price: "3.3",
        title: "Product1",
        updated_at: "2012-10-03T13:28:27Z"
    }
]
```

`JSON` formatında eklediğimiz ürünü görüyoruz.

Şimdide uygulamamıza `XML` desteği ekleyelim. 

Burada `respond_to` metodunu kullanacağız.

```ruby
  def index
    @products = Product.all

    respond_to do |format|
      format.json { render json: @products }
      format.xml { render xml: @products }
    end

  end
```

`/products.xml` sayfasını ziyaret ettiğimizde aşağıdaki hatayı alacağız. 

```bash
NoMethodError in ProductsController#index

undefined method `respond_to' for #<ProductsController:0x007fbc096e1438>
```

`Rails::API` de uygulamayı daha hafif hale getirmek için bir dizi özellik çıkarılmış durumda ama biz istersek bu özellikleri kolayca uygulamalarımza geri ekleyebiliriz. Yukarıdaki aldığımız hata da `ActionController::MimeResponds` modülü etkin olmadığından kaynaklanıyor. `ProductController`' a `ActionController::MimeResponds` modülünü ekleyelim.

```ruby
class ProductsController < ApplicationController
  include ActionController::MimeResponds
  #Actions
end
```

![xml response](https://dl.dropbox.com/u/44821222/xml-response.png)

`Rails::API` README dosyasında ihtiyacımız çerçevesinde hangi [modulleri](https://github.com/spastorino/rails-api/blob/master/README.md) kullanabilecegimiz listelenmiştir. Ancak bu liste modüllerin tam listesi değil. `Rails` [kodları](https://github.com/rails/rails/blob/master/actionpack/lib/action_controller/base.rb#L201) ile `Rails::API`nin [kodlarını](https://github.com/spastorino/rails-api/blob/master/lib/rails-api/action_controller/api.rb#L124) karşılaştırdığımızda bu listedeki eksik olanları görebiliyoruz.

Bazı modülleri kullanmak istediğimizde  modülü sadece controller' da çağırmak yetmeyebibilir. `Rails::API` `Controller`' ları azalttığı gibi aynı zamanda `Rack middleware` katmanınıda azaltmıştır. Normal bir rails uygulamasındaki middleware' lar ile `Rails::API` gem' ini kullanarak olulturduğumuz bir uygulamadaki middleware' ları karşılaştırırsak aşağıdaki middleware' ların çıkarılmış olduğunu görüyoruz.


```bash
use Rack::MethodOverride
use ActionDispatch::Cookies
use ActionDispatch::Session::CookieStore
use ActionDispatch::Flash
use ActionDispatch::BestStandardsSupport
```

İhtiyacımız çerçevesinde bu middleware' ları `application.rb` ye ekleyerek kullanabiliriz.

`Rails::API` kullanırken karşılaşılan sorunların birçoğu issuelarda çözülmüş. https://github.com/rails-api/rails-api/issues?page=1&state=closed   Gözatmakta fayda var.

İyi çalışmalar...

