# Deploy Rails App With Capistrano and Puma

Pedro Dousseau

May 8th, 2017

### 1. Install Gems and start Capistrano

__Gemfile:__

```
gem 'figaro'
gem 'puma'

group :development do
  gem 'capistrano'
  gem 'capistrano-rails', require: false
  gem 'capistrano-bundler', require: false
  gem 'capistrano-rbenv'
end
```

__Run:__

```
$ bundle install
$ cap install STAGES=production
```

### 2. Edit capistrano files

__Add the following to /Capfile:__

```
require 'capistrano/bundler'
require 'capistrano/rbenv'
require 'capistrano/rails/assets' # for asset handling add
require 'capistrano/rails/migrations' # for running migrations
require 'capistrano/puma'
```

__Edit /config/deploy.rb to:__

```
lock '3.4.0'

set :application, 'app_name'
set :repo_url, 'git@github.com:devdatta/app_name.git' # Edit this to match your repository
set :branch, :master
set :deploy_to, '/var/www/app_name'
set :pty, true
set :linked_files, %w{config/database.yml config/application.yml}
set :linked_dirs, %w{bin log tmp/pids tmp/cache tmp/sockets vendor/bundle public/system public/uploads}
set :keep_releases, 5
set :rbenv_type, :user # or :system, depends on your rbenv setup
set :rbenv_ruby, '2.4.0'
set :rbenv_prefix, "RBENV_ROOT=#{fetch(:rbenv_path)} RBENV_VERSION=#{fetch(:rbenv_ruby)} #{fetch(:rbenv_path)}/bin/rbenv exec"
set :rbenv_map_bins, %w{rake gem bundle ruby rails}
set :rbenv_roles, :all
```


### 3. Configure server:

Do the following on your server.

__Create the application folders and files:__

```
$ mkdir -p /var/www/app_name/shared/config
$ nano     /var/www/app_name/shared/config/database.yml
$ nano     /var/www/app_name/shared/config/application.yml
```

__Add the following to database.yml:__

```
production:
  adapter: mysql2
  encoding: utf8
  socket: /var/run/mysqld/mysqld.sock
  database: production_db
  username: my_user
  password: my_pass
  host: localhost
```

__Add the following to application.yml:__

```
SECRET_KEY_BASE: "8a2ff74119cb2b8f14a85dd6e213fa24d8540fc34dcaa7ef8a35c246ae452bfa8702767d19086461ac911e1435481c22663fbd65c97f21f6a91b3fce7687ce63"
```

Run `$ rake secret` to create a new one.

### 4. Deploy from local machine

__Add the following to config/deploy/production.rb:__

```
server 'my_ip_or_hostname', user: 'my_user', roles: %w{web app db}
```

__Run:__

```
$ cap production deploy
```

### 5. Start puma on server

__On the server, run:__

```
$ cd /app_path/current/
$ puma -e production --daemon
```

This will start puma web server on production enviroment and run in background.

### 6. Mantain

Every time that you want to deploy a new version, push it to  git and run:

```
$ cap production deploy
```

### 7. Extra:

- __Use Apache:__ Create a reverse proxy to puma port.
- __Get Puma pid:__ `sudo lsof -n -i :3000`
