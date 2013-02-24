---
layout: post
title: "Ruby On Rails Application Template Oluşturma"
date: 2012-12-23 17:00
author: Muhammet DİLEK
comments: true
categories: ruby-on-rails application-template template
---

###Template nedir ?

Yeni bir `rails` uygulaması oluştururken genel görevleri otomatikleştirmek için yardımcı `ruby` script. Örneğin her `rails` projesinde `devise` gemini kullanıyorsak bunu her seferinde eklemek yerine template' e ekleyip aynı işlemleri tekrarlamaktan kurtulabiliriz.

###Template Oluşturma
Template oluşturmak için ne bir `generator` nede bir `gem` kullancağız. İçerisine bazı metodlar yazabileceğimiz bir `ruby` dosyası yeterli :)
<!-- more -->
###Template Kullanımı
Yeni bir uygulama oluştururken `-m` parametresinden sonra template yolunu yazıyoruz.

```bash
$ rails new blog -m ~/template.rb
$ rails new APP_NAME -m https://raw.github.com/lab2023/rails-template/master/rails_template.rb

```

Yada `rails:tempalte` rake' ini kullanabiliriz.

```bash
$ rake rails:template LOCATION=~/template.rb
$ rake rails:template LOCATION=http://example.com/template.rb
```
###Template metodları:

####gem(name, options = {})
`gem` metodu Gemfile dosyasın gem eklenmesini sağlıyor.

```ruby
gem 'will_paginate'
gem 'bootstrap-will_paginate'
gem "will-paginate-i18n", "~> 0.1.7"
```

>NOT: `gem` metodu gemi ekliyor ama kurmuyor. Yani biz gemleri ekledikten sonra `bundle install` metodunu çalıştırmalıyız.

####gem_group(*names, &block)

Grup içerisine gem yazmamızı sağlar.

```ruby
group :assets do
  gem "bootstrap-sass"
  gem "compass-rails"
  gem "jquery-ui-rails"
end
```

####add_source(source, options = {})
Gemler için source eklememizi sağlar.

```ruby
add_source "http://code.whytheluckystiff.net"
```

####initializer(filename, data = nil, &block)
`config/initializers` klasörü altına  initializer dosyası eklememizi sağlar.

```ruby
initializer 'resque_mailer.rb', <<-CODE
class AsyncMailer < ActionMailer::Base
  include Resque::Mailer
  layout 'email'
end
CODE
```
####rakefile(filename, data = nil, &block)
`lib/tasks` altına rake oluşturuyor

```ruby
rakefile("bootstrap.rake") do
  <<-TASK
    require "resque/tasks"
    task "resque:setup" => :environment
  TASK
end
```
####generate(what, args)
`generate` metodu `rails generator`' lerini çalıştırmamızı sağlıyor.

```ruby
generate(:scaffold, "person", "name:string", "address:text", "age:number")
generate 'simple_form:install --bootstrap'
```

#### run(command, config={})
`run` metodu ismindende anlaşılcağı gibi sistem komutlarını çalıştırmaya yarıyor.

```ruby
run 'bundle install' # bundle install komutunu çalıştırıyor
run 'touch README.md' # README.md isminde dosya oluturyor
```
####rake(command, options = {})

`rake` çalıştırmamızı sağlıyor.

```ruby
rake 'db:drop'
rake 'db:create'
rake 'db:migrate'
rake "db:migrate", :env => 'production'
```
####route(routing_code)
Route ` config/routes.rb` dosyasına routing entry eklememizi sağlar.

```ruby
route "root to: 'person#index'"
```
####inside(dir)
Klasör içerinde komut çalıştırmamıza yarar.

```ruby
path = "https://raw.github.com/lab2023/rails-template/master/files/"
inside 'app/views/devise' do
  get path + 'app/views/devise/confirmations/new.html.haml', 'confirmations/new.html.haml'
end
```

####ask(question)

Kullanıcıdan bilgi alınmasını sağlar.

```ruby
lib_name = ask("What do you want to call the shiny library ?")
lib_name << ".rb" unless lib_name.index(".rb")
 
lib lib_name, <<-CODE
class Shiny
end
CODE
```

####yes?(question) or no?(question)

Kullanıcya soru sormamızı sağlarlar.

```ruby
if yes?('Would you like to use the Sphinx search system? (yes/no)')
  sphinx_flag = true
else
  sphinx_flag = false
end
```

####git(:command)
Git komutlarını kullanmamızı sağlıyor.

```ruby
git :init
git :add => '.'
git :commit => '-m "Issue #1 Install Rails "'
```


####create_file(destination, data=nil, config={}, &block)
`create_file` metodu yeni bir dosya oluşturur.

```ruby
create_file 'lib/tasks/resque.rake', <<-CODE
require "resque/tasks"
task "resque:setup" => :environment
CODE
```

####gsub_file(path, flag, *args, &block)

Dosya içerisinde text değiştirmemizi sağlar. `flag` parametresi string ve regex bir ifade olabilir.

```ruby
gsub_file 'config/application.rb', /:password/, ':password, :password_confirmation'
```

####remove_file(path, config={})
Dosya siler.

```ruby
remove_file 'app/models/user.rb'
remove_file 'app/models/admin.rb'
```

####inject_into_file(destination, *args, &block)

Dosya içerisine text eklememeizi sağlar. `:after` ve `:before` parametreleri ile nereye ekleme yapılacağını belirtebiliriz.

```ruby
inject_into_file 'app/controllers/pages_controller.rb', :before => 'end' do <<-RUBY
skip_before_filter :authenticate_user!, :only => :index

def index

end


RUBY
end
```

####append_file(path, *args, &block)
Dosyaya text eklememizi sağlar.

```ruby
append_file '.gitignore' do <<-FILE
.idea
.idea/**/*
FILE
end
```

####get(source, destination=nil, config={}, &block)

Verilen adresteki içeriği alır ve hedef kaynağa yerleştirir. Yani hedef doyaya yazar.

```ruby
path = "https://raw.github.com/lab2023/rails-template/master/files/"
get path + 'app/controllers/admins/application_controller.rb', 'app/controllers/admins/application_controller.rb'
```

Genel olarak sık kullandığımız metodları yazamaya çalıştım. Aşağıdaki linklerden daha fazla kaynağa ulşabilirsiniz. Dilerseniz [lab2023 rails temaplate](https://github.com/lab2023/rails-template)' i inceleyebilrsiniz.

Kaynaklar:
http://guides.rubyonrails.org/rails_application_templates.html
https://github.com/wycats/thor/wiki/Actions
http://textmate.rubyforge.org/thor/Thor/Actions.html
http://rdoc.info/github/wycats/thor/master/Thor/Actions

İyi çalışmalar...
