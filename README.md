# Git

## Changing all the commits with new author & email

Warning! Only do this if you're the sole author. It rewrites everything!

```
git filter-branch -f --env-filter "GIT_AUTHOR_NAME='Fadhli Rahim'; GIT_AUTHOR_EMAIL='fadhli@charaku.co'; GIT_COMMITTER_NAME='Fadhli Rahim'; GIT_COMMITTER_EMAIL='fadhli@charaku.co';" HEAD
```


# Sys Admin

## Logrotate

```
/opt/raynor/current/log/*.log {
  daily
  missingok
  rotate 7
  compress
  notifempty
  nocreate
  copytruncate
}
```

## Secure copy scp

scp ssh_username@remote_host:<file_in_remote_address> <your_local_file>

scp your_username@remotehost.edu:foobar.txt /some/local/directory

## Finding out which port is in use

Using lsof comamnd

```
sudo lsof -i :80

COMMAND  PID  USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
nginx   1618  root    6u  IPv4  10141      0t0  TCP *:http (LISTEN)
nginx   1620 nginx    6u  IPv4  10141      0t0  TCP *:http (LISTEN)
```

# Ruby

## Deep stringify keys (hash.deep_stringify_keys)

Convert all symbol keys to string. I found that it's useful for test purposes on rails controller. I use Oj to dump/load the objects into hash/json.

```ruby
{ :test => "this", :object => {:nested => "keys"} }.deep_stringify_keys
=> {"test"=>"this", "object"=>{"nested"=>"keys"}}
```

# AWS Elasticbeanstalk

## eb cli

http://docs.aws.amazon.com/elasticbeanstalk/latest/dg/eb-cli3-install.html

## Rails setup

### database.yml

For database.yml, don't include any sensitive information. Beanstalk allows ENV configuration that allows DATABASE_URL string e.g `postgres://username:password@url/dbname/`

All ENV can be configured inside aws beanstalk web app.

### YAML config

You can include a custom .config yaml file in `.ebextensions` folder. These are the minimum setup for me to successfully deploy a rails app
that has Gem source from rubygems.org & github.


```yaml
# These are things that make sense for any Ruby application
option_settings:
  - option_name: BUNDLE_DISABLE_SHARED_GEMS
    value: "1"
  - option_name: BUNDLE_PATH
    value: "vendor/bundle"

# Install git in order to be able to bundle gems from git
packages:
  yum:
    git: []
    patch: []
    postgresql93-devel: []

# http://agileleague.com/blog/deploying-elastic-beanstalk-using-symlinks-avoid-checking-database-yml-git/
# Symlink the ondeck database.yml to database.yml.example
files:
  "/opt/elasticbeanstalk/hooks/appdeploy/pre/01a_symlink_database_yml.sh":
    mode: "000777"
    content: |
      #!/bin/bash
      cd /var/app/ondeck/config
      ln -sf database.yml.example database.yml

```

### Restarting Puma in elasticbeanstalk

`initctl restart puma`

Location of `restart.sh` file

`/opt/elasticbeanstalk/hooks/restartappserver/enact/01_restart.sh`


### CORS setup for assets on Nginx

This mainly works in Rails /assets folder where web fonts compiled assets are

Include a map of domains to allow inside the `http { .. }` block
```
map $http_origin $cors_header {
  default     ""; #empty causes the Access-Control-Allow-Origin header to be empty
  ~*localhost:3000 "$http_origin";
  ~*.charaku.com   "$http_origin";
  ~*.charaku.co    "$http_origin";
}
```

Then in your `server` block, include the following
```
location /assets {
  alias /var/app/current/public/assets;
  gzip_static on;
  gzip on;
  expires max;
  add_header Cache-Control public;
  add_header Access-Control-Allow-Origin $cors_header;
}
```

