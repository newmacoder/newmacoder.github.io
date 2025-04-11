### **1. 创建专属仓库**

- 登录后，点击右上角 **+** → **New repository**。
- **仓库名格式**：必须为 `你的用户名.github.io`（如用户名为 `john`，则仓库名为 `john.github.io`）。
- 勾选 **“Add a README file”**（可选，便于初始化仓库）。
- 点击 **Create repository**。

---

### **2. 创建并上传网页文件**

#### **方法一：直接通过GitHub网页端**

1. 进入刚创建的仓库，点击 **Add file** → **Create new file**。

2. 输入文件名 `index.html`。

3. 在编辑器中编写HTML代码（示例）：

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>我的第一个网站</title>
   </head>
   <body>
       <h1>欢迎来到我的GitHub Pages！</h1>
   </body>
   </html>
   ```

4. 点击 **Commit changes** 保存。

#### **方法二：通过Git命令行（需本地安装Git）**

1. 克隆仓库到本地：

   ```bash
   git clone https://github.com/你的用户名/你的用户名.github.io.git
   ```

2. 进入仓库文件夹，创建 `index.html` 文件。

3. 提交并推送代码：

   ```bash
   git add index.html
   git commit -m "首次提交"
   git push origin main
   ```

---

### **3. 访问你的网站**

- 打开浏览器，输入 `https://你的用户名.github.io`（如 `https://john.github.io`）。
- 若未立即生效，等待1-2分钟（首次部署可能需要更久）。

---

### **4. 进阶操作**

#### **使用Jekyll模板（无需本地安装）**

1. 在仓库中创建 `_config.yml` 文件，添加内容：

   ```yaml
   theme: minima
   ```

2. 创建 `_posts` 文件夹存放博客文章（Markdown格式）。

3. GitHub会自动构建并生成静态网站。

#### **自定义域名**

1. 在仓库中创建 `CNAME` 文件，内容为你的域名（如 `example.com`）。
2. 在域名注册商处添加DNS记录：
   - **类型**：CNAME → **值**：`你的用户名.github.io`。
   - 或 **A记录** → **IP**：`185.199.108.153`（GitHub提供的IP）。

---

### **常见问题解决**

- **页面未更新**：清除浏览器缓存或等待GitHub构建完成（约1-5分钟）。
- **404错误**：检查仓库名是否为 `用户名.github.io`，且文件在 `main` 分支的根目录。
- **构建失败**：在仓库的 **Settings → Pages → Build and deployment** 查看错误日志。

---

### **学习资源**

- **官方文档**：[GitHub Pages指南](https://pages.github.com/)
- **Jekyll教程**：[Jekyll官方文档](https://jekyllrb.com/)
- **Git入门**：[Pro Git电子书](https://git-scm.com/book/zh/v2)

---

按照以上步骤操作，你可以在10分钟内快速搭建一个基础网站。遇到问题时，优先检查仓库命名和文件路径是否正确，逐步排查即可。
