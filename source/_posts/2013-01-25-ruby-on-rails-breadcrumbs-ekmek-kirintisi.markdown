---
layout: post
title: "Ruby On Rails Breadcrumbs(Ekmek kırıntısı)"
date: 2013-01-25 13:40
author: Muhammet DİLEK
comments: true
categories: ruby-on-rails
---
Breadcrumb için [breadcrumbs_on_rails](http://www.simonecarletti.com/code/breadcrumbs_on_rails/) gemini kullanıyoruz. Gemfile' a `gem 'breadcrumbs_on_rails'` ekledikten sonra `bundle:install` komutunu çalıştırarak gemi kuralım. Controller' da `add_breadcrumb ` metodunu çağırarak breadcrumb ekleyebiliyoruz. View katmanında ise `render_breadcrumbs` helper' ı kullanıyoruz.

```ruby
class Admins::ApplicationController < ActionController::Base
  add_breadcrumb "Dashboard", :admins_dashboard_index_path #I18n.t('nav.dashboard')
end
```
<!-- more -->
```ruby
class Admins::CountriesController < Admins::ApplicationController
  add_breadcrumb "Countries", :admins_countries_path #I18n.t('nav.countries')
  def index
    @search = Country.search(params[:q])
    @countries = @search.result(:distinct => true).paginate(:page => params[:page])
    respond_with(@countries)
  end

  def new
    @country = Country.new
    add_breadcrumb "New", new_admins_country_path
    respond_with(@country)
  end

  def edit
    add_breadcrumb "Edit", edit_admins_country_path
    @country = Country.find(params[:id])
  end
end
```
![Braeadcrumb-1](https://dl.dropbox.com/u/44821222/breadcrumb-1.png)
![Braeadcrumb-1](https://dl.dropbox.com/u/44821222/breadcrumb-2.png)
![Braeadcrumb-1](https://dl.dropbox.com/u/44821222/breadcrumb-3.png)

Dilerseniz [Dökümantasyon](http://www.simonecarletti.com/code/breadcrumbs_on_rails/) sayfasının ziyaret edebilirsiniz.

İyi çalışmalar...
