+++
date = '2025-04-11T11:31:52-07:00'
draft = false
title = 'Hugo+GitHub Pages搭建博客'

+++

# linux使用Rclone挂载onedrive
<!--more-->

[Rclone官方文档](https://rclone.org/docs/)

## 安装Rclone

```
curl https://rclone.org/install.sh | sudo bash
```

## 安装 FUSE 工具

`fusermount` 是 FUSE（Filesystem in Userspace）的一个组件,是 Rclone 挂载功能所依赖的工具之一

```
sudo apt update
sudo apt install fuse3
```



## 配置 Rclone

- 打开终端，运行以下命令启动 `rclone` 配置向导

```
rclone config
```

- 按照向导进行配置

  选择Microsoft OneDrive而不是OneDrive
  Client Id,Client Secret默认为空，不需要高级配置(advanced config)

```
e) Edit existing remote
n) New remote
d) Delete remote
r) Rename remote
c) Copy remote
s) Set configuration password
q) Quit config
e/n/d/r/c/s/q> n
name> remote
Type of storage to configure.
Enter a string value. Press Enter for the default ("").
Choose a number from below, or type in your own value
[snip]
XX / Microsoft OneDrive
   \ "onedrive"
[snip]
Storage> onedrive
Microsoft App Client Id
Leave blank normally.
Enter a string value. Press Enter for the default ("").
client_id>
Microsoft App Client Secret
Leave blank normally.
Enter a string value. Press Enter for the default ("").
client_secret>
Edit advanced config? (y/n)
y) Yes
n) No
y/n> n
Remote config
Use web browser to automatically authenticate rclone with remote?
 * Say Y if the machine running rclone has a web browser you can use
 * Say N if running rclone on a (remote) machine without web browser access
If not sure try Y. If Y failed, try N.
y) Yes
n) No
y/n> y
If your browser doesn't open automatically go to the following link: http://127.0.0.1:53682/auth
Log in and authorize rclone for access
Waiting for code...
Got code
Choose a number from below, or type in an existing value
 1 / OneDrive Personal or Business
   \ "onedrive"
 2 / Sharepoint site
   \ "sharepoint"
 3 / Type in driveID
   \ "driveid"
 4 / Type in SiteID
   \ "siteid"
 5 / Search a Sharepoint site
   \ "search"
Your choice> 1
Found 1 drives, please select the one you want to use:
0: OneDrive (business) id=b!Eqwertyuiopasdfghjklzxcvbnm-7mnbvcxzlkjhgfdsapoiuytrewqk
Chose drive to use:> 0
Found drive 'root' of type 'business', URL: https://org-my.sharepoint.com/personal/you/Documents
Is that okay?
y) Yes
n) No
y/n> y
Configuration complete.
Options:
- type: onedrive
- token: {"access_token":"youraccesstoken","token_type":"Bearer","refresh_token":"yourrefreshtoken","expiry":"2018-08-26T22:39:52.486512262+08:00"}
- drive_id: b!Eqwertyuiopasdfghjklzxcvbnm-7mnbvcxzlkjhgfdsapoiuytrewqk
- drive_type: business
Keep this "remote" remote?
y) Yes this is OK
e) Edit this remote
d) Delete this remote
y/e/d> y
```

- 测试配置是否成功

  onedrive为远程存储名

```
 rclone lsd onedrive:
```

如果配置正确，应该会看到OneDrive 文件夹列表。



## **使用 Rclone 管理 OneDrive 文件**

以下是一些常用的命令：

1. **列出 OneDrive 根目录中的文件和文件夹：**

   ```
   rclone ls onedrive:
   ```

2. **上传文件到 OneDrive：**

   ```
   rclone copy /path/to/local/file onedrive:/destination/folder
   ```

3. **下载文件到本地：**

   ```
   rclone copy onedrive:/source/folder /path/to/local/destination
   ```

4. **同步本地和 OneDrive 文件夹：**

   ```
   rclone sync /path/to/local/folder onedrive:/remote/folder
   ```

   

5. **挂载 OneDrive 为本地目录：**

   [Rclone挂载参考文档](https://rclone.org/commands/rclone_mount/)

   ```
   rclone mount onedrive:/ /path/to/mountpoint --vfs-cache-mode writes --allow-other -vv
   ```

   `--vfs-cache-mode writes`：启用写入缓存

   `--allow-other `:允许其他用户访问挂载点
   
   `-vv`:显示详细信息

## 让Rclone挂载持续运行

配置 Rclone 挂载为系统服务，在启动时自动运行

创建一个服务文件并编辑

```
sudo vi /etc/systemd/system/rclone-onedrive.service
```

内容如下：

```
[Unit]
Description=Rclone Mount OneDrive
After=network-online.target

[Service]
Type=simple
ExecStart=/usr/bin/rclone mount onedrive:/ /path/to/mountpoint \
    --vfs-cache-mode writes \
    --allow-other
ExecStop=/bin/fusermount -u /path/to/mountpoint
Restart=always
User=youruser

[Install]
WantedBy=multi-user.target
```

配置文件内容解释

 **[Unit]**

- **`Description=Rclone Mount OneDrive`**
  - 描述该服务的用途。在这里，它表示此服务用于挂载 OneDrive。
- **`After=network-online.target`**
  - 定义服务启动的先决条件。
    - 这里表示服务将在网络连接建立后启动（因为 Rclone 挂载 OneDrive需要网络连接）。

------

**[Service]**

- **`Type=notify`**
  - 指定服务的类型：
    - `notify` 表示服务会向 `systemd` 通知其启动进程何时完成。这是挂载服务的推荐类型。
- **`ExecStart=/usr/bin/rclone mount onedrive:/ /path/to/mountpoint \`**
  - 定义服务启动时执行的命令：
    - `/usr/bin/rclone` 是 Rclone 可执行文件的位置。
    - `mount` 是 Rclone 挂载的子命令。
    - `onedrive:/` 表示 Rclone 配置中名为 `onedrive` 的远程存储。
    - `/path/to/mountpoint` 是挂载点路径（本地文件夹，挂载后的内容会显示在这个目录下）。
    - `\` 是换行符，用于让命令在下一行继续。
- **`--vfs-cache-mode writes`**
  - 设置 VFS（虚拟文件系统）缓存模式：
    - `writes` 表示对写入的文件进行缓存，通常适合挂载云存储。
- **`--allow-other`**
  - 允许系统中的其他用户访问挂载点（需要在 `/etc/fuse.conf` 中启用 `user_allow_other`）。
- **`ExecStop=/bin/fusermount -u /path/to/mountpoint`**
  - 定义服务停止时执行的命令：
    - 使用 `fusermount -u` 卸载挂载点 `/path/to/mountpoint`。
- **`Restart=always`**
  - 定义服务在何时重新启动：
    - `always` 表示如果服务退出或失败，`systemd` 会自动重新启动服务。
- **`User=youruser`**
  - 指定运行此服务的用户：
    - 将 `youruser` 替换为实际的 Linux 用户名。确保该用户对挂载点目录有读写权限。

------

 **[Install]**

- `WantedBy=multi-user.target`
  - 定义服务的安装目标：
    - `multi-user.target` 表示该服务适用于多用户系统的运行级别



编辑/etc/fuse.conf文件,取消`user_allow_other`的注释



配置之后执行以下

```
# 刷新

# 开启
systemctl start onedrive
# 设置自启动
systemctl enable onedrive
# 重启
systemctl restart onedrive
# 停止
systemctl stop onedrive
# 移除开机自启动
systemctl disable onedrive
# 验证服务是否正常运行
systemctl status onedrive
```

