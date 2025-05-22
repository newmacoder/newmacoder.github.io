**alist项目地址**：[GitHub - AlistGo/alist一个支持多存储的文件列表/WebDAV程序。](https://github.com/AlistGo/alist)

## docker安装alist

### **运行容器**

```
docker run -d --restart=always -v /etc/alist:/opt/alist/data -p 5244:5244 -e PUID=0 -e PGID=0 -e UMASK=022 --name="alist" xhofe/alist:latest
```

## **使用cloudflare进行代理**

### cloudflare创建一个Tunnels隧道

- 在linux上运行对应的命令

- 安装cloudflared

  ```
  wget https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb
  sudo dpkg -i cloudflared-linux-amd64.deb
  ```

- 若cloudflared已安装,已执行过面板上的脚本，则

  - 输入命令进行登录:

    ```text
    cloudflared tunnel login
    ```

    输入命令后，会给出一个URL，打开浏览器访问这个URL，选择需要授权的网站。

  - 创建Tunnel

    创建一个Tunnel，名称可以自定义，比如'web':

    ```text
    cloudflared tunnel create <tunnel-name>
    # 例如
    cloudflared tunnel create web
    ```

    创建成功后，会返回一个Tunnel ID，类似于'f7b0c0e0-0c0e-0c0e-0c0e-0c0e0c0e0c0e'。

    生成隧道 ID 和配置文件（位于 `~/.cloudflared/`）
    
    ```
    Tunnel credentials written to /root/.cloudflared/f7b0c0e0-0c0e-0c0e-0c0e-0c0e0c0e0c0e.json. cloudflared chose this file based on wh
    ere your origin certificate was found. Keep this file secret. To revoke these credentials, delete the tunnel.   
    
    Created tunnel alist with id f7b0c0e0-0c0e-0c0e-0c0e-0c0e0c0e0c0e
    ```
    
    
    
    配置DNS记录
    
    使用如下命令配置DNS，其中的 `<tunnel-name>`是创建Tunnel时指定的名称，`<domain>`是自定义的域名，比如 `web.example.com`:
    
    ```text
    cloudflared tunnel route dns <tunnel-name> <domain>
    # 例如
    cloudflared tunnel route dns web web.example.com
    ```
    
    如果还有其他域名需要配置，可以继续执行上面的命令。配置完成后可以前往Cloudflare控制台查看到[CNAME记录](https://zhida.zhihu.com/search?content_id=226324823&content_type=Article&match_order=1&q=CNAME记录&zhida_source=entity)。
    
    
    
    Linux的配置文件路径为 `~/.cloudflared/config.yml`，Windows的配置文件路径为 `%USERPROFILE%\.cloudflared\config.yml`，如果文件不存在，可以手动创建。
    
    配置文件内容如下，注意根据注释修改配置：
    
    ```text
    # Tunnel UUID，就是同目录下的json文件的文件名
    
    tunnel: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    
    # 鉴权文件路径，注意根据自己的路径修改，下面的例子是Linux的路径
    
    credentials-file: /home/xxx/.cloudflared/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.json
    
    protocol: h2mux
    
      
    
    # 服务配置
    
    ingress:
    
      # hostname是刚才添加DNS记录时指定的域名
    
      - hostname: web.example.com
    
        # service是需要暴露的服务，比如这里反代了5244端口
    
        service: http://127.0.0.1:5244
    
      # 最后记得添加一个默认404
    
      - service: http_status:404
    ```
    
    更多配置方式可以参考[帮助文档](https://developers.cloudflare.com/cloudflare-one/connections/connect-networks/configure-tunnels/cloudflared-parameters/origin-parameters/)
    
    配置完成后，验证一下配置文件是否正确：
    
    ```text
    cloudflared tunnel ingress validate
    ```
    
    显示ok即代表配置正确
    
   -  测试运行
  
    输入如下命令启动Tunnel：
  
    ```text
    cloudflared tunnel --config ~/.cloudflared/config.yml run <tunnel-uuid>
    # 例如
    cloudflared tunnel --config ~/.cloudflared/config.yml run xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    ```
  
    访问刚才配置的域名，如果能正常访问，说明配置成功。或者前往Cloudflare Zero Trust的控制台查看Tunnel的状态。
  
  
  
  - 访问alist.lyphahaha.top
  
  - 设为系统服务（可选）
  
    `sudo cloudflared service install`
    `sudo systemctl start cloudflared`
    `sudo systemctl enable cloudflared`
    
  - 忘记密码可以，重设密码
  
    `docker exec -it alist ./alist admin random` 



## 在管理面板上挂载onedrive

[参考教程地址](https://alist-doc.nn.ci/docs/driver/webdav)

直接用alist官网默认提供的client去get refresh token，不需要自己创建应用的，因为个人版没有组织管理员批准这一步，所以alist提供的client_id和client_secret是可以直接用的
