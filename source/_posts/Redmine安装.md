---
title: Redmine安装
date: 2017-08-04 10:46:58
categories: 项目管理
tags:
- Redmine
- Rails
- Shell
---

[Redmine](http://www.redmine.org)是一个开源的、基于Web的项目管理和缺陷跟踪工具。它用日历和甘特图辅助项目及进度可视化显示。同时它又支持多项目管理。Redmine是一个自由开放 源码软件解决方案，它提供集成的项目管理功能，问题跟踪，并为多个版本控制选项的支持。虽说像IBM Rational Team Concert的商业项目调查工具已经很强大了，但想坚持一个自由和开放源码的解决方案，可能会发现Redmine是一个有用的Scrum和敏捷的选择。 由于Redmine的设计受到Rrac的较大影响，所以它们的软件包有很多相似的特征。Redmine建立在Ruby on Rails的框架之上，他可以夸平台和数据库。

## 准备工作

### Ruby

推荐使用rvm安装ruby，具体安装教程，参见[centos下rvm安装](http://blog.csdn.net/yangcs2009/article/details/50634424)

### 数据库

~~~SQL
CREATE DATABASE redmine CHARACTER SET utf8;
CREATE USER 'redmine'@'localhost' IDENTIFIED BY 'my_password';
GRANT ALL PRIVILEGES ON redmine.* TO 'redmine'@'localhost';
~~~

## 下载

访问[Redmine官网](http://www.redmine.org/projects/redmine/wiki/Download)下载。

这里我以下载 [redmine-3.4.2.zip](http://www.redmine.org/releases/redmine-3.4.2.zip)为例子。

~~~Shell
$ cd /home/redmine
$ wget http://www.redmine.org/releases/redmine-3.4.2.zip
$ unzip redmine-3.4.2
$ cd redmine-3.4.2
~~~

## 安装依赖包

为了下载依赖包速度快，可以使用[淘宝的gems源](https://ruby.taobao.org/)。

~~~Shell
$ gem install bundler
$ bundle install --without development test //这里注意在redmine根目录下执行(本例是/home/redmine/redmine-3.4.2)
~~~

### 可能遇到的两个坑

1. rmagick 安装失败

需要安装rmagick所依赖的开发库，然后再继续执行bundle install

~~~Shell
$ sudo apt-get install libmagickwand-dev imagemagick //Ubuntu
~~~

~~~Shell
$ sudo yum install ImageMagick-devel ImageMagick //Centos
~~~

2. mysql2 安装失败

需要安装mysql2所依赖的开发库，然后再继续执行bundle install

~~~Shell
$sudo apt-get install libmysqld-dev //Ubuntu
~~~

~~~Shell
$sudo yum install mysql-devel //Centos
~~~

## 秘钥生成

~~~Shell
$ bundle exec rake generate_secret_token
~~~

## 数据库迁移，生成redmine需要的表数据

~~~Shell
$ cp config/database.yml.example config/database.yml
~~~

编辑 config/databaase.yml

~~~YAML
production:
  adapter: mysql2
  database: redmine
  host: localhost
  username: redmine
  password: my_password
~~~

执行迁移

~~~Shell
$ RAILS_ENV=production bundle exec rake db:migrate
$ RAILS_ENV=production bundle exec rake redmine:load_default_data //可选操作，导入默认数据。这里不操作，首次访问admin界面时，web页面也有同样的操作按钮
~~~

## 启动Redmine

检测测试一下，可以使用Redmine自带的Webrick服务器

~~~Shell
$ bundle exec rails server webrick -e production
~~~

生产使用的话，这里演示unicorn

### 1. 编辑Gemfile，增加unicorn配置，将下面代码插入文件中。

~~~YAML
# Application server add by armink
group :unicorn do
  gem "unicorn", '~> 4.6.3'
  gem 'unicorn-worker-killer'
end
~~~

### 2. 通过bundle更新Gem依赖，此时会看到unicorn被安装

~~~Shell
sudo bundle install --without development test
~~~

### 3. 增加unicorn配置文件config/unicorn.rb

~~~ruby
# Sample verbose configuration file for Unicorn (not Rack)
#
# This configuration file documents many features of Unicorn
# that may not be needed for some applications. See
# http://unicorn.bogomips.org/examples/unicorn.conf.minimal.rb
# for a much simpler configuration file.
#
# See http://unicorn.bogomips.org/Unicorn/Configurator.html for complete
# documentation.
# Use at least one worker per core if you're on a dedicated server,
# more will usually help for _short_ waits on databases/caches.
worker_processes 2
# Since Unicorn is never exposed to outside clients, it does not need to
# run on the standard HTTP port (80), there is no reason to start Unicorn
# as root unless it's from system init scripts.
# If running the master process as root and the workers as an unprivileged
# user, do this to switch euid/egid in the workers (also chowns logs):
# user "unprivileged_user", "unprivileged_group"
# Help ensure your application will always spawn in the symlinked
# "current" directory that Capistrano sets up.
working_directory "/home/redmine/redmine" # available in 0.94.0+
# listen on both a Unix domain socket and a TCP port,
# we use a shorter backlog for quicker failover when busy
listen "/home/redmine/redmine/tmp/sockets/redmine.socket", :backlog => 64
listen 3000, :tcp_nopush => true
# nuke workers after 30 seconds instead of 60 seconds (the default)
timeout 30
# feel free to point this anywhere accessible on the filesystem
pid "/home/redmine/redmine/tmp/pids/unicorn.pid"
# By default, the Unicorn logger will write to stderr.
# Additionally, some applications/frameworks log to stderr or stdout,
# so prevent them from going to /dev/null when daemonized here:
stderr_path "/home/redmine/redmine/log/unicorn.stderr.log"
stdout_path "/home/redmine/redmine/log/unicorn.stdout.log"
# combine Ruby 2.0.0dev or REE with "preload_app true" for memory savings
# http://rubyenterpriseedition.com/faq.html#adapt_apps_for_cow
preload_app true
GC.respond_to?(:copy_on_write_friendly=) and
  GC.copy_on_write_friendly = true
# Enable this flag to have unicorn test client connections by writing the
# beginning of the HTTP headers before calling the application.  This
# prevents calling the application for connections that have disconnected
# while queued.  This is only guaranteed to detect clients on the same
# host unicorn runs on, and unlikely to detect disconnects even on a
# fast LAN.
check_client_connection false
before_fork do |server, worker|
  # the following is highly recomended for Rails + "preload_app true"
  # as there's no need for the master process to hold a connection
  defined?(ActiveRecord::Base) and
    ActiveRecord::Base.connection.disconnect!
  # The following is only recommended for memory/DB-constrained
  # installations.  It is not needed if your system can house
  # twice as many worker_processes as you have configured.
  #
  # This allows a new master process to incrementally
  # phase out the old master process with SIGTTOU to avoid a
  # thundering herd (especially in the "preload_app false" case)
  # when doing a transparent upgrade.  The last worker spawned
  # will then kill off the old master process with a SIGQUIT.
  old_pid = "#{server.config[:pid]}.oldbin"
  if old_pid != server.pid
    begin
      sig = (worker.nr + 1) >= server.worker_processes ? :QUIT : :TTOU
      Process.kill(sig, File.read(old_pid).to_i)
    rescue Errno::ENOENT, Errno::ESRCH
    end
  end
  #
  # Throttle the master from forking too quickly by sleeping.  Due
  # to the implementation of standard Unix signal handlers, this
  # helps (but does not completely) prevent identical, repeated signals
  # from being lost when the receiving process is busy.
  # sleep 1
end
after_fork do |server, worker|
  # per-process listener ports for debugging/admin/migrations
  # addr = "127.0.0.1:#{9293 + worker.nr}"
  # server.listen(addr, :tries => -1, :delay => 5, :tcp_nopush => true)
  # the following is *required* for Rails + "preload_app true",
  defined?(ActiveRecord::Base) and
    ActiveRecord::Base.establish_connection
  # if preload_app is true, then you may also want to check and
  # restart any other shared sockets/descriptors such as Memcached,
  # and Redis.  TokyoCabinet file handles are safe to reuse
  # between any number of forked children (assuming your kernel
  # correctly implements pread()/pwrite() system calls)
end
~~~

### 4. 添加启动脚本script/web，包含对unicorn的启动、停止等命令控制

~~~Shell
#!/usr/bin/env bash

cd $(dirname $0)/..
app_root=$(pwd)
unicorn_pidfile="$app_root/tmp/pids/unicorn.pid"
unicorn_config="$app_root/config/unicorn.rb"
function get_unicorn_pid
{
  local pid=$(cat $unicorn_pidfile)
  if [ -z $pid ] ; then
    echo "Could not find a PID in $unicorn_pidfile"
    exit 1
  fi
  unicorn_pid=$pid
}
function start
{
  bundle exec unicorn_rails -D -c $unicorn_config -E $RAILS_ENV
}
function stop
{
  get_unicorn_pid
  kill -QUIT $unicorn_pid
}
function reload
{
  get_unicorn_pid
  kill -USR2 $unicorn_pid
}
case "$1" in
  start)
    start
    ;;
  stop)
    stop
    ;;
  reload)
    reload
    ;;
  *)
    echo "Usage: RAILS_ENV=your_env $0 {start|stop|reload}"
    ;;
esac
~~~

### 5. 添加开机启动脚本/etc/init.d/redmine

~~~Shell
#! /bin/sh
# REDMINE
# Maintainer: @armink
# Authors: armink.ztl@gmail.com, @armink
### BEGIN INIT INFO
# Provides:          redmine
# Required-Start:    $local_fs $remote_fs $network $syslog redis-server
# Required-Stop:     $local_fs $remote_fs $network $syslog
# Default-Start:     2 3 4 5
# Default-Stop:      0 1 6
### END INIT INFO
###
# DO NOT EDIT THIS FILE!
# This file will be overwritten on update.
# Instead add/change your variables in /etc/default/redmine
# An example defaults file can be found in lib/support/init.d/redmine.default.example
###
### Environment variables
RAILS_ENV="production"
# Script variable names should be lower-case not to conflict with
# internal /bin/sh variables such as PATH, EDITOR or SHELL.
app_user="redmine"
app_root="/home/$app_user/redmine-3.4.2"
pid_path="$app_root/tmp/pids"
socket_path="$app_root/tmp/sockets"
web_server_pid_path="$pid_path/unicorn.pid"
# Read configuration variable file if it is present
test -f /etc/default/redmine && . /etc/default/redmine
# Switch to the app_user if it is not he/she who is running the script.
if [ "$USER" != "$app_user" ]; then
  sudo -u "$app_user" -H -i $0 "$@"; exit;
fi
# Switch to the redmine path, exit on failure.
if ! cd "$app_root" ; then
 echo "Failed to cd into $app_root, exiting!";  exit 1
fi
### Init Script functions
## Gets the pids from the files
check_pids(){
  if ! mkdir -p "$pid_path"; then
    echo "Could not create the path $pid_path needed to store the pids."
    exit 1
  fi
  # If there exists a file which should hold the value of the Unicorn pid: read it.
  if [ -f "$web_server_pid_path" ]; then
    wpid=$(cat "$web_server_pid_path")
  else
    wpid=0
  fi
}
## Called when we have started the two processes and are waiting for their pid files.
wait_for_pids(){
  i=0;
  while [ ! -f $web_server_pid_path ]; do
    sleep 0.1;
    i=$((i+1))
    if [ $((i%10)) = 0 ]; then
      echo -n "."
    elif [ $((i)) = 301 ]; then
      echo "Waited 30s for the processes to write their pids, something probably went wrong."
      exit 1;
    fi
  done
  echo
}
# We use the pids in so many parts of the script it makes sense to always check them.
# Only after start() is run should the pids change.
check_pids
## Checks whether the different parts of the service are already running or not.
check_status(){
  check_pids
  # If the web server is running kill -0 $wpid returns true, or rather 0.
  # Checks of *_status should only check for == 0 or != 0, never anything else.
  if [ $wpid -ne 0 ]; then
    kill -0 "$wpid" 2>/dev/null
    web_status="$?"
  else
    web_status="-1"
  fi
  if [ $web_status = 0 ]; then
    redmine_status=0
  else
    # http://refspecs.linuxbase.org/LSB_4.1.0/LSB-Core-generic/LSB-Core-generic/iniscrptact.html
    # code 3 means 'program is not running'
    redmine_status=3
  fi
}
## Check for stale pids and remove them if necessary.
check_stale_pids(){
  check_status
  # If there is a pid it is something else than 0, the service is running if
  # *_status is == 0.
  if [ "$wpid" != "0" -a "$web_status" != "0" ]; then
    echo "Removing stale Unicorn web server pid. This is most likely caused by the web server crashing the last time it ran."
    if ! rm "$web_server_pid_path"; then
      echo "Unable to remove stale pid, exiting."
      exit 1
    fi
  fi
}
## If no parts of the service is running, bail out.
exit_if_not_running(){
  check_stale_pids
  if [ "$web_status" != "0" ]; then
    echo "Redmine is not running."
    exit
  fi
}
## Starts Unicorn if it's not running.
start() {
  check_stale_pids
  if [ "$web_status" != "0" ]; then
    echo -n "Starting the Redmine Unicorn."
  fi
  # Then check if the service is running. If it is: don't start again.
  if [ "$web_status" = "0" ]; then
    echo "The Unicorn web server already running with pid $wpid, not restarting."
  else
    # Remove old socket if it exists
    rm -f "$socket_path"/redmine.socket 2>/dev/null
    # Start the web server
    RAILS_ENV=$RAILS_ENV script/web start &
  fi
  # Wait for the pids to be planted
  wait_for_pids
  # Finally check the status to tell wether or not Redmine is running
  print_status
}
## Asks the Unicorn if it would be so kind as to stop, if not kills it.
stop() {
  exit_if_not_running
  if [ "$web_status" = "0" ]; then
    echo -n "Shutting down the Redmine Unicorn"
  fi
  # If the Unicorn web server is running, tell it to stop;
  if [ "$web_status" = "0" ]; then
     RAILS_ENV=$RAILS_ENV script/web stop
  fi
  # If something needs to be stopped, lets wait for it to stop. Never use SIGKILL in a script.
  while [ "$web_status" = "0" ]; do
    sleep 1
    check_status
    printf "."
    if [ "$web_status" != "0" ]; then
      printf "\n"
      break
    fi
  done
  sleep 1
  # Cleaning up unused pids
  rm "$web_server_pid_path" 2>/dev/null
  print_status
}
## Prints the status of Redmine and it's components.
print_status() {
  check_status
  if [ "$web_status" = "0" ]; then
      echo "The Redmine is \033[32mrunning\033[0m.\nThe Redmine Unicorn web server pid is $wpid."
  else
      printf "The Redmine is \033[31mnot running\033[0m.\n"
  fi
}
## Tells unicorn to reload it's config 
reload(){
  exit_if_not_running
  if [ "$wpid" = "0" ];then
    echo "The Redmine Unicorn Web server is not running thus its configuration can't be reloaded."
    exit 1
  fi
  printf "Reloading Redmine Unicorn configuration... "
  RAILS_ENV=$RAILS_ENV script/web reload
  echo "Done."
  wait_for_pids
  print_status
}
## Restarts Unicorn.
restart(){
  check_status
  if [ "$web_status" = "0" ]; then
    stop
  fi
  start
}
### Finally the input handling.
case "$1" in
  start)
        start
        ;;
  stop)
        stop
        ;;
  restart)
        restart
        ;;
  reload|force-reload)
    reload
        ;;
  status)
        print_status
        exit $redmine_status
        ;;
  *)
        echo "Usage: service redmine {start|stop|restart|reload|status}"
        exit 1
        ;;
esac
exit
~~~

设置该脚本文件属性为可执行文件

~~~Shell
$ chmod a+x /etc/init.d/redmine
~~~

### 6. 加入开机启动序列(以centos 7.x为例)

由于centos7默认降低了/etc/rc.d/rc.local的权限，所以需要手动添加可执行权限

~~~Shell
$ chmod a+x /etc/rc.d/rc.local
~~~

编辑/etc/rc.d/rc.local，将开机时需要执行的指令追加进去

~~~
....
/etc/init.d/redmine start
~~~

现在可以reboot试下是否能直接访问 http://127.0.0.1:3000。

这里也有可能遇到防火墙的坑，以[centos7.x](https://xiaoguo.net/wiki/centos-7-firewalld.html)为例，可以这么执行;

~~~Shell
$ firewall-cmd --permanent --add-port=3000/tcp
$ systemctl restart firewalld
~~~

## 邮件配置

~~~Shell
$ cp config/configuration.yml.example config/configuration.yml
~~~

编辑config/configuration.yml, 以腾讯企业邮箱为例:

~~~YAML
email_delivery:
    delivery_method: :async_smtp
    smtp_settings:
      address: "smtp.exmail.qq.com"
      port: 465
      ssl: true
      domain: "smtp.exmail.qq.com"
      authentication: :login
      user_name: "foo@bar.com"
      password: "your_pwd"
~~~

