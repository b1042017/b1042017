# Gitlab 伺服器架設

## 組員

B1042017 林冠達

B1042019 楊博凱

<!-- vim-markdown-toc GFM -->

* Gitlab是甚麼?
* 更新系統並安裝依賴項
* 添加 GitLab CE 存儲庫
* 在 Ubuntu 22.04上安裝 GitLab CE
* 訪問 GitLab CE Web 界面
* 參考資料

<!-- vim-markdown-toc -->

## Gutlab是甚麼?

- GitLab 是由 GitLab Inc.開發，一款基於 Git 的完全整合的軟體開發平台（fully 整合軟體 development platform）。 另外，GitLab 且具有wiki以及線上編輯、issue跟蹤功能、CI/CD 等功能。

## 第 1 步：更新系統並安裝依賴項

- 通過確保您的系統已更新來開始安裝

```shell
sudo apt update
sudo apt upgrade -y
```

- 在下面安裝 GitLab 依賴項

```
sudo apt install -y ca-certificates curl openssh-server tzdata
```

## 第 2 步：添加 GitLab CE 存儲庫

- 安裝完所有先決條件後，通過運行以下命令繼續將 GitLab 存儲庫添加到您的 Ubuntu 22.04

* Ubuntu 22.04：

- 在更新本文時，適用於 Ubuntu 22.04 的 GitLab APT 存儲庫尚未準備好。我們要做的是在 22.04 上為 Ubuntu 20.04 配置存儲庫。一旦 22.04 的存儲庫可用，本文將更新。

- 安裝需要的依賴包：

```shell
sudo apt update
sudo apt install curl debian-archive-keyring lsb-release ca-certificates apt-transport-https software-properties-common -y
```

- 導入 GitLab 存儲庫 GPG 密鑰

```
gpg_key_url="https://packages.gitlab.com/gitlab/gitlab-ce/gpgkey"
curl -fsSL $gpg_key_url| sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/gitlab.gpg
```

- 將存儲庫內容添加到/etc/apt/sources.list.d/gitlab_gitlab-ce.list文件。

```
sudo tee /etc/apt/sources.list.d/gitlab_gitlab-ce.list<<EOF
deb https://packages.gitlab.com/gitlab/gitlab-ce/ubuntu/ focal main
deb-src https://packages.gitlab.com/gitlab/gitlab-ce/ubuntu/ focal main
EOF
```
- 通過更新 APT 包索引確認配置的存儲庫正在運行。

```
$ sudo apt update
Hit:1 http://security.ubuntu.com/ubuntu jammy-security InRelease
Hit:3 http://ke.archive.ubuntu.com/ubuntu jammy InRelease
Hit:4 http://ke.archive.ubuntu.com/ubuntu jammy-updates InRelease
Get:2 https://packages.gitlab.com/gitlab/gitlab-ce/ubuntu focal InRelease [23.3 kB]
Hit:5 http://ke.archive.ubuntu.com/ubuntu jammy-backports InRelease
Get:6 https://packages.gitlab.com/gitlab/gitlab-ce/ubuntu focal/main amd64 Packages [27.8 kB]
Fetched 51.1 kB in 2s (32.6 kB/s)
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
```

- 添加gitlab-ce安裝源

```
curl -sS https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.deb.sh | sudo bash
```

- 存儲庫內容添加到：

```
cat /etc/apt/sources.list.d/gitlab_gitlab-ce.list
```

## 第 3 步：在 Ubuntu 22.04上安裝 GitLab CE

- apt 添加存儲庫後，使用包管理器命令在 Ubuntu 22.04|20.04|18.04 上安裝 GitLab CE ：

```
sudo apt update
sudo apt install gitlab-ce
```

- 安裝輸出：

```
It looks like GitLab has not been configured yet; skipping the upgrade script.

       *.                  *.
      ***                 ***
     *****               *****
    .******             *******
    ********            ********
   ,,,,,,,,,***********,,,,,,,,,
  ,,,,,,,,,,,*********,,,,,,,,,,,
  .,,,,,,,,,,,*******,,,,,,,,,,,,
      ,,,,,,,,,*****,,,,,,,,,.
         ,,,,,,,****,,,,,,
            .,,,***,,,,
                ,*,.
  


     _______ __  __          __
    / ____(_) /_/ /   ____ _/ /_
   / / __/ / __/ /   / __ `/ __ \
  / /_/ / / /_/ /___/ /_/ / /_/ /
  \____/_/\__/_____/\__,_/_.___/
  

Thank you for installing GitLab!
```

- 編輯 GitLab 配置文件以設置主機名和其他參數：

```
$ sudo vim /etc/gitlab/gitlab.rb
external_url 'http://gitlab.example.com'
```

- 代替gitlab.example.com具有 GitLab 服務器的有效域。

- 完成後，通過運行以下命令啟動 GitLab 實例：

```
sudo gitlab-ctl reconfigure
```

- 所有 GitLab 服務都應在配置後啟動。

```
$ sudo gitlab-ctl status
run: alertmanager: (pid 92581) 18s; run: log: (pid 92343) 80s
run: gitaly: (pid 92590) 18s; run: log: (pid 91561) 189s
run: gitlab-exporter: (pid 92551) 20s; run: log: (pid 92078) 98s
run: gitlab-kas: (pid 92520) 22s; run: log: (pid 91845) 175s
run: gitlab-workhorse: (pid 92531) 21s; run: log: (pid 91985) 117s
run: grafana: (pid 92610) 17s; run: log: (pid 92471) 38s
run: logrotate: (pid 91486) 202s; run: log: (pid 91494) 201s
run: nginx: (pid 91993) 114s; run: log: (pid 92013) 110s
run: node-exporter: (pid 92540) 21s; run: log: (pid 92049) 104s
run: postgres-exporter: (pid 92601) 18s; run: log: (pid 92367) 76s
run: postgresql: (pid 91693) 184s; run: log: (pid 91704) 183s
run: prometheus: (pid 92560) 20s; run: log: (pid 92297) 88s
run: puma: (pid 91904) 132s; run: log: (pid 91917) 129s
run: redis: (pid 91521) 196s; run: log: (pid 91538) 193s
run: redis-exporter: (pid 92553) 20s; run: log: (pid 92217) 94s
run: sidekiq: (pid 91922) 126s; run: log: (pid 91934) 122s
```

### 第 4 步：訪問 GitLab CE Web 界面

- 在 Ubuntu 22.04|20.04|18.04 上安裝 GitLab CE 後， http://gitlab.example.com 在瀏覽器上打開 URL 以完成 Gitlab 的安裝。

- root 用戶的密碼是隨機生成的，並在/etc/gitlab/initial_root_password. 您可以使用以下命令檢查密碼：

```shell
$ cat /etc/gitlab/initial_root_password
# WARNING: This value is valid only in the following conditions
#          1. If provided manually (either via `GITLAB_ROOT_PASSWORD` environment variable or via `gitlab_rails['initial_root_password']` setting in `gitlab.rb`, it was provided before database was seeded for the first time (usually, the first reconfigure run).
#          2. Password hasn't been changed manually, either via UI or via command line.
#
#          If the password shown here doesn't work, you must reset the admin password following https://docs.gitlab.com/ee/security/reset_user_password.html#reset-your-root-password.

Password: kOtOjWp7v70OjkjtadnSJAhcDbCNo9nTNGVC5UoSCyE=

# NOTE: This file will be automatically deleted in the first reconfigure run after 24 hours.
```

- 使用此密碼和用戶名root登錄。

![GiltLab-Login-1105x420](https://user-images.githubusercontent.com/107233342/172988666-7e956180-ae05-4371-ba14-908c08ee7e4a.png)

- GitLab 儀表板應如下所示。

![install-gitlab-ubuntu-18 04-debian-9-interface-1068x314](https://user-images.githubusercontent.com/107233342/172988762-71812c53-45b3-4a91-ae4d-f70a4857a02b.png)

###重置root用戶密碼

- 轉到根用戶配置文件 >首選項

![gitlab-change-root-password-01](https://user-images.githubusercontent.com/107233342/172988859-b66ec841-c134-4399-8eba-acf9e42edce8.png)

- 然後密碼部分

![gitlab-change-root-password-02-1203x420](https://user-images.githubusercontent.com/107233342/172988899-bf9a983c-1183-4ba9-9e97-6d6f80407948.png)

- 輸入當前密碼，設置新密碼。

![gitlab-change-root-password-03-1068x458](https://user-images.githubusercontent.com/107233342/172988935-e47d00d0-a3a7-413e-b19d-9f63204fb06f.png)

## 第5步創建測試專案

- 設置全局簽名

```shell
git config --global user.name <User>
git config --global user.email <Email>
```

- 複製遠端庫到本地

```
git clone <網址>
```

![Snipaste_2022-06-10_13-31-28](https://user-images.githubusercontent.com/107233342/172996542-9dad7933-c5fa-4560-9fc7-2b603e227357.png)

- 初始化git

```
git init --initial-branch-main
```

- 創建remand.me文件

```
touch READMD.md
```

- 編輯readme文件

```
vim README.md
```

- 添加 readme 文件到暫存區

```
git add README.md
```

- 提交修改到歷史紀錄中

```
git commit -m "Add README.md"
```

```
git push -u origin main
```
## 參考資料

[如何在 Ubuntu 22.04|20.04|18.04 上安裝 GitLab CE](https://www.theserverside.com/blog/Coffee-Talk-Java-News-Stories-and-Opinions/Fix-for-the-GitLab-Unable-to-locate-package-gitlab-ee-on-Ubuntu-20)

[Fix for the GitLab "Unable to locate package gitlab-ee" on Ubuntu 20](https://www.theserverside.com/blog/Coffee-Talk-Java-News-Stories-and-Opinions/Fix-for-the-GitLab-Unable-to-locate-package-gitlab-ee-on-Ubuntu-20)  
