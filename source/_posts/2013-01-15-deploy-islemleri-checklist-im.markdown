---
layout: post
title: "Deploy İşlemleri Checklist'im"
date: 2012-12-20 17:05
author: Muhammet DİLEK
comments: true
categories: capistrano ruby-on-rails deploy
---
Deploy ederken birşeyleri atlamamak için kendime bir checklist oluşturdum. Deploy için [Capistrano](https://github.com/capistrano/capistrano) kullanıyoruz.

###Branch Merge işlemleri

**Merge işlemlerini kesinlikle komut satırından yapın.**

* Merge edilecek branch taki tüm kodların güncel olduğundan emin olun.

```ruby
git checkout develop
git pull
```
* Kodları test edin

* **Üçüncü part `javascript`,  `css` dosyaları varmı bakın.**

* **Varsa bunlar `environments/production.rb` ye eklenmişmi ona bakın. Assets precompile için.**

* Kendisine merge yapılcak branch' taki kodların güncel olduğundan emin olun.

```ruby
git checkout master
git pull
git merge --no-ff develop
```

* `config/deploy.rb` dosyasını kontrol edin. IP, user, port...
<!-- more -->
###Deploy

* Sunucuya bağlanıp veritabanı yedeği alınır. Ne olur ne olmaz. 
MySQL için [şu yazıyı](http://muhammetdilek.dudupress.com/articles/mysqldump-ile-veritabani-yedegi-alma) okuyabilirsiniz.  (Bunu otomatik yapan bir çalışma yapacağız)
* `cap deploy` komutu ile deploy edilir.
* Migration var ise `cap deploy:migrate`komutu çalıştırılır.
* Diğer cap task' lerine `cap --tasks`komutunu çalıştırarak bakabilirsiniz.

```ruby
Muhammets-iMac:dudupress muhammetdilek$ cap --tasks
[Deprecation Warning] This API has changed, please hook `deploy:create_symlink` instead of `deploy:symlink`.
cap bundle:install                       # Install the current Bundler enviro...
cap deploy                               # Deploys your project.
cap deploy:check                         # Test deployment dependencies.
cap deploy:check_revision                # Make sure local git is in sync wit...
cap deploy:cleanup                       # Clean up old releases.
cap deploy:cold                          # Deploys and starts a `cold' applic...
cap deploy:create_symlink                # Updates the symlink to the most re...
cap deploy:migrate                       # Run the migrate rake task.
cap deploy:migrations                    # Deploy and run pending migrations.
cap deploy:pending                       # Displays the commits since your la...
cap deploy:pending:diff                  # Displays the `diff' since your las...
cap deploy:resque_setup                  # Resque QUEUE Start
cap deploy:restart                       # restart unicorn server
cap deploy:rollback                      # Rolls back to a previous version a...
cap deploy:rollback:code                 # Rolls back to the previously deplo...
cap deploy:setup                         # Prepares one or more servers for d...
cap deploy:start                         # start unicorn server
cap deploy:stop                          # stop unicorn server
cap deploy:symlink                       # Deprecated API.
cap deploy:update                        # Copies your project and updates th...
cap deploy:update_code                   # Copies your project to the remote ...
cap deploy:update_crontab                # Update the crontab file
cap deploy:upload                        # Copy files to the currently deploy...
cap invoke                               # Invoke a single command on the rem...
cap shell                                # Begin an interactive Capistrano se...
cap thinking_sphinx:configure            # Generate the Sphinx configuration ...
cap thinking_sphinx:index                # Index data
cap thinking_sphinx:install:sphinx       # If Postgres is available, Sphinx w...
cap thinking_sphinx:install:ts           # Install Thinking Sphinx as a gem
cap thinking_sphinx:rebuild              # Stop, re-index and then start the ...
cap thinking_sphinx:restart              # Stop and then start the Sphinx daemon
cap thinking_sphinx:shared_sphinx_folder # Add the shared folder for sphinx f...
cap thinking_sphinx:start                # Start the Sphinx daemon
cap thinking_sphinx:stop                 # Stop the Sphinx daemon
cap whenever:clear_crontab               # Clear application's crontab entrie...
cap whenever:update_crontab              # Update application's crontab entri...
```

* Açıklamaları ile birlikte http://capitate.rubyforge.org/recipes/deploy.html
* Deploydan sonra `cap deploy:restart` komutu ile unicorn restart edilir.
* Test edilir.
* Test sonucu başarılı ise bilgilendirme maili atılır.
* Başarılı değilse başarılı olana kadar hotfix yapılır.

###Hotfix

* Hotfix branch oluştur

```ruby
git checkout -b hotfix-1.2.1 master
```
* Hataları düzelt commit et
* Master branch' a merge et
* Sonra develop branch' a merge et
* Sonra hotfix branch' ını sil

```ruby
git branch -d hotfix-1.2.1
git push origin :hotfix-1.2.1
```

İyi çalışmalar dilerim...
