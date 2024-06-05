## git常用命令

```bash
# 查看用户
git config  user.name

# 查看邮箱
git config  user.email

# 查看配置
git config  --list

# 克隆仓库
git clone   地址

# 把当前的目录内的文件上传到暂存区,添加所有修改的文件，但不包含删除文件到暂存区
git add  .

# 把缓存区的内的文件上传到本地仓库
git commit -am "注释"    # 注释是可以随便写的 建议是README.MD

# 上传到git仓库
git push origin master   # 上传到master主分支上

# 初始化
git init

# 添加仓库,本地有仓库的情况下追加新的仓库
git remote add origin 仓库地址

# 拉取仓库
git pull origin master     

# 克隆远程已有的仓库至本地
git clone 远程仓库地址

# 更新本地代码
git pull  <shortname> <remote_branch>

# 添加所有修改的文件以及删除的文件到暂存区
$ git add -A (--all)

# 添加单个或者多个文件/目录到暂存区
$ git add file1/dir1 file2/dir2

# 创建分支
git branch 新分支名字

# 切换分支
git checkout 分支名字

# 创建分支饼切换到新分支
git checkout -b 分支名字

# 查看本地仓库所有分支
git branch           # 绿色代表当前所属分支

# 查看远程仓库的所有的分支
git branch -r

# 查看远程和本地所有分支
git branch -a

# 同步更新分支到远程仓库
git push origin 需要更新的分支名字

# 删除本地分支
git branch -d 需要删除的分支名字

# 强制删除本地分支
git branch -D 需要删除的分支名字

# 删除远程仓库分支
git push origin --delete 分支名字

# 合并分支
git merge 分支名字
# Git的merge命令用于将两个或多个分支的代码合并到一个新的或现有的分支中。合并操作将两个分支的历史记录集成到一个新的提交中，这使得不同开发者在不同分支上的工作能够被合并到一起

# 查看仓库做了哪些更改
git log

# 查看当前环境文件变更状态
git status

# 查看修改了什么内容
git diff 文件名字
```

## 配置全局用户名和邮箱

```shell
# 配置用户
$ git config --global user.name "你的名字"

# 配置邮箱
$ git config --global user.email 你的邮箱

# 配置ssh密钥
$ ssh-keygen.exe -t rsa -C "你的邮箱"

# 查看Key
$ cat ~/.ssh/
id_rsa       id_rsa.pub   known_hosts

```

## 部署gitlab  使用的是https

```bash
[root@localhost ~]# ls
anaconda-ks.cfg  gitlab-ce-15.1.2-ce.0.el7.x86_64.rpm
[root@localhost ~]# yum -y install gitlab-ce-15.1.2-ce.0.el7.x86_64.rpm
# 此处我使用的是rpm包也可以直接使用yum -y install gitlab

# 创建一个存放ssl证书存放路径,并且上传证书
[root@localhost ~]# ls /etc/gitlab/ssl/
jack.gxxyqj.top.key  jack.gxxyqj.top.pem

# 修改配置文件
[root@localhost ~]# egrep -v "^(#|$)" /etc/gitlab/gitlab.rb
# 修改以下信息
external_url 'https://jack.gxxyqj.top'          
gitlab_rails['time_zone'] = 'Asia/Shanghai'        
gitlab_rails['backup_path'] = "/var/opt/gitlab/backups"       
gitlab_rails['backup_archive_permissions'] = 0644
gitlab_rails['backup_keep_time'] = 604800
git_data_dirs({                                 
   "default" => {
     "path" => "/mnt/nfs-01/git-data"
    }
 })
gitlab_rails['gitlab_shell_ssh_port'] = 22           
nginx['enable'] = true                     
nginx['client_max_body_size'] = '250m'     
nginx['redirect_http_to_https'] = true     
nginx['redirect_http_to_https_port'] = 80
nginx['ssl_certificate'] = "/etc/gitlab/ssl/jack.gxxyqj.top.pem"
nginx['ssl_certificate_key'] = "/etc/gitlab/ssl/jack.gxxyqj.top.key"
nginx['ssl_ciphers'] = "ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384"
nginx['ssl_prefer_server_ciphers'] = "off"
nginx['ssl_protocols'] = "TLSv1.2 TLSv1.3"
nginx['ssl_session_cache'] = "shared:SSL:10m"
nginx['ssl_session_timeout'] = "1d"
nginx['gzip_enabled'] = true


==========================================
定义 GitLab 实例的外部访问 URL。用户通过此 URL 访问 GitLab 界面。
external_url 'https://gitlab.tanke.love'
# 设置 GitLab 的时区为 "Asia/Shanghai"。这影响到 GitLab 中时间显示的区域设置。
gitlab_rails['time_zone'] = 'Asia/Shanghai'
# 定义 Git 数据存储的路径。
git_data_dirs({
  "default" => {
    "path" => "/mnt/nfs-01/git-data"
   }
})
# 设置 GitLab Shell 使用的 SSH 端口。
gitlab_rails['gitlab_shell_ssh_port'] = 22
# 启用 Nginx，GitLab 使用 Nginx 作为 Web 服务器。
nginx['enable'] = true
# 设置最大请求体大小为 250MB
nginx['client_max_body_size'] = '250m'
# 启用 HTTP 到 HTTPS 的重定向，确保所有流量通过 HTTPS。
nginx['redirect_http_to_https'] = true
# 设置重定向端口为 80。
nginx['redirect_http_to_https_port'] = 80
# 指定 SSL 证书和密钥的位置，用于 HTTPS。
nginx['ssl_certificate'] = "/tmp/fullchain.pem"
nginx['ssl_certificate_key'] = "/tmp/privkey.pem"
nginx['ssl_ciphers'] = "ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384"
nginx['ssl_prefer_server_ciphers'] = "off"
# 定义支持的 SSL/TLS 协议版本。
nginx['ssl_protocols'] = "TLSv1.2 TLSv1.3"
# 配置 SSL 会话缓存，提高性能。
nginx['ssl_session_cache'] = "shared:SSL:10m"
# 超时时间。
nginx['ssl_session_timeout'] = "1d"
# 启用 Gzip 压缩，提高传输效率。
nginx['gzip_enabled'] = true
=============================================================

# 重新加载配置
gitlab-ctl reconfigure
# 查看密码
cat /etc/gitlab/initial_root_password
# 启动实例
gitlab-ctl start
```

## tag版本推送

大白话就是对一个数据做一个标签。

1. **版本标记**：

- - 标签可以标记代码库中的特定版本，如 v1.0.0、v2.1.3 等。这有助于开发者和其他利益相关者（如产品经理、测试人员、用户等）清楚地了解代码的当前状态和历史版本。

1. **发布管理**：

- - 当软件达到某个稳定状态并准备发布时，可以为该状态创建一个标签。这有助于在后续开发过程中追踪和比较不同版本之间的差异。
  - 通过标签，可以轻松地回滚到以前的版本，如果新版本出现严重问题或不符合预期。

1. **构建和部署**：

- - 在持续集成/持续部署（CI/CD）流程中，标签经常用于触发构建和部署操作。例如，当一个新的标签被推送到代码库时，CI/CD 系统可以自动构建和部署该版本的软件

- 

- ```bash
  echo  "1111" >>   1.txt
  git add .
  git commit -am "版本测试"
  git tag -a "v1.0.0"  -m "版本测试"
  git push origin main
  git push origin v1.0.0
  
  # 对此版本组做修改,可以创建一个新的文件做为第二版
  echo  "22222" >>   1.txt
  git add .
  git commit -am "版本测试"
  git tag -a "v1.1.0"  -m "版本测试"
  git push origin main
  git push origin v1.1.0
  ```

- 

## gitlab做数据备份以及数据恢复

```shell
# 先修改配置文件
gitlab_rails['backup_path'] = "/var/opt/gitlab/backups"	#备份的路径
gitlab_rails['backup_archive_permissions'] = 0644		#备份文件的默认权限
gitlab_rails['backup_keep_time'] = 604800

# 重新加载配置
gitlab-ctl reconfigure 
gitlab-ctl  restart

# 执行命令进行备份
gitlab-rake gitlab:backup:create

# 数据恢复
[root@localhost ~]#  cd /var/opt/gitlab/backups
[root@localhost backups]# ls
1717517821_2024_06_05_15.1.2_gitlab_backup.tar
[root@localhost ~]#  gitlab-rake gitlab:backup:restore BACKUP=/var/opt/gitlab/backups/1717517821_2024_06_05_15.1.2
# 注意恢复文件的名称
```

