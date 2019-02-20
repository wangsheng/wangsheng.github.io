---
title: GitLab重置管理员密码
date: 2019-02-20 10:34:01
categories: Git
tags: 
- GitLab
- git
---

自行安装GitLab后，默认会有一个超级管理员账号：

- username: root
- email: admin@example.com
- password: xxx

一般安装完，做完基本设置后，就创建了一个自己的账号来创建和管理group以及project了。时间一长，很容易忘记平台管理员的账号，再加上之前没有修改默认管理员的邮箱，就导致没有机会通过邮箱找回密码了。

那么遇到这种情况，如何找到管理员密码呢？难道需要重新安装一次GitLab么？

庆幸的是，GitLab使用的是Rails开发的，如果懂Rails开发的朋友可能想到了Rails的console了。对的，通过Rails的console可以很方便访问数据持久化层，从而完成账号修改工作。

这样一来，就可以有两种方法获取平台管理权限了：

- 方法一：修改root账号的密码

- 方法二：将自己的账号赋予admin权限

## 修改root账号密码

1. 登录GitLab服务器，切换到git用户
  ~~~Shell
  [root@localhost ~]# su - git
  上一次登录：二 2月 19 17:22:46 CST 2019pts/1 上
  -sh-4.2$
  ~~~

2. 进入Rails的控制台
  ~~~Shell
  -sh-4.2$ gitlab-rails console production
  Loading production environment (Rails 4.2.8)
  ~~~

3. 查找到root账号实体对象
  ~~~Shell
  irb(main):007:0> user = User.where(username:"root").first
  => #<User id: 1, email: "admin@example.com", created_at: "2017-08-04 05:13:25", updated_at: "2019-02-20 02:45:24", name: "Administrator", admin: true, projects_limit: 100000, skype: "", linkedin: "", twitter: "", authentication_token: "jd7ym2eFqtysemi3KJy9", bio: nil, username: "root", can_create_group: true, can_create_team: false, state: "active", color_scheme_id: 5, password_expires_at: nil, created_by_id: nil, last_credential_check_at: nil, avatar: nil, hide_no_ssh_key: false, website_url: "", notification_email: "admin@example.com", hide_no_password: false, password_automatically_set: false, location: nil, encrypted_otp_secret: nil, encrypted_otp_secret_iv: nil, encrypted_otp_secret_salt: nil, otp_required_for_login: false, otp_backup_codes: nil, public_email: "", dashboard: 0, project_view: 2, consumed_timestep: nil, layout: 0, hide_project_limit: false, otp_grace_period_started_at: nil, external: false, incoming_email_token: "2mozrbmg71dnrxiqd4p7tzgsx", organization: nil, require_two_factor_authentication_from_group: false, two_factor_grace_period: 48, ghost: nil, last_activity_on: "2017-08-21", notified_of_own_activity: false, preferred_language: "en", rss_token: "y8j52nca719HmzQfDQF4", external_email: false, email_provider: nil>
  ~~~

4. 修改root的密码

   ~~~shell
   irb(main):004:0> user.password = "11111111"
   => "11111111"
   irb(main):006:0> user.save!
   Enqueued ActionMailer::DeliveryJob (Job ID: 36fc27ef-8065-4f06-b56b-b389b5c2d3c4) to Sidekiq(mailers) with arguments: "DeviseMailer", "password_change", "deliver_now", gid://gitlab/User/1
   => true
   ~~~

   至此，就可以使用root和11111111登录GitLab了。

## 将自己的账号赋予admin权限

1. 登录GitLab服务器，切换到git用户

   ~~~~shell
   [root@localhost ~]# su - git
   上一次登录：二 2月 19 17:22:46 CST 2019pts/1 上
   -sh-4.2$
   ~~~~

2. 进入Rails的控制台

   ~~~shell
   -sh-4.2$ gitlab-rails console production
   Loading production environment (Rails 4.2.8)
   ~~~

3. 查找到自己账号实体对象

   ~~~shell
   irb(main):009:0> user = User.where(username:"wangsheng").first
   => #<User id: 2, email: "wangsheng@foo.bar", created_at: "2017-08-04 05:24:54", updated_at: "2019-02-20 02:39:07", name: "王胜", admin: true, projects_limit: 100000, skype: "", linkedin: "", twitter: "", authentication_token: "SvbRRSSSH_p_oJxEKXPL", bio: "", username: "wangsheng", can_create_group: true, can_create_team: false, state: "active", color_scheme_id: 5, password_expires_at: nil, created_by_id: nil, last_credential_check_at: nil, avatar: "avatar.png", hide_no_ssh_key: false, website_url: "", notification_email: "wangsheng@foo.bar", hide_no_password: false, password_automatically_set: false, location: "", encrypted_otp_secret: nil, encrypted_otp_secret_iv: nil, encrypted_otp_secret_salt: nil, otp_required_for_login: false, otp_backup_codes: nil, public_email: "", dashboard: 0, project_view: 2, consumed_timestep: nil, layout: 0, hide_project_limit: false, otp_grace_period_started_at: nil, external: false, incoming_email_token: "7cfayhmnjf5xlpp2fy8gdyjnh", organization: "", require_two_factor_authentication_from_group: false, two_factor_grace_period: 48, ghost: nil, last_activity_on: "2019-02-19", notified_of_own_activity: false, preferred_language: "en", rss_token: "Jbjx-wxfRxHErC4f_yNT", external_email: false, email_provider: nil>
   ~~~

4. 赋予admin权限

   ~~~shell
   irb(main):003:0> user.admin = true
   => true
   irb(main):004:0> user.save!
   => true
   ~~~

   至此，就可以使用自己的账号登录，并且登录上去后，会发现多了一个『admin area』平台管理的入口。

   ![](http://img.iaquam.com/image/png/gitlab-admin-area1.png)

