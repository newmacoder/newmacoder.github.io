首先创建一个 Github 项目

```
git clone https://github.com/newmacoder/blog
cd blog/
```



进入项目文件夹，新建一个 posts 文件夹

```
mkdir posts/
```



在里面编写文章，内容一般用 .md 后缀即可，例如 helloworld.md



一切就绪后，使用 `git push` 命令将代码推送到仓库上。

然后修改 workers代码，

完整的 Cloudflare Workers 脚本
以下是完整的脚本，包含动态加载 list.json 和 Markdown 文件的逻辑：

```
export default {
  async fetch(request, env, ctx) {
    const url = new URL(request.url);
    const path = decodeURIComponent(url.pathname.replace(/^\/+|\/+$/g, "")); // 去掉首尾的 "/" 并解码中文路径

    // GitHub 文件基础路径
    const blogBase = "https://raw.githubusercontent.com/newmacoder/blog/main/posts/";

    // 如果路径为空或为 "/", 返回首页
    if (!path || path === "") {
      return new Response(await renderIndex(), { headers: { "Content-Type": "text/html; charset=UTF-8" } });
    }

    // 构造 Markdown 文件的完整 URL
    const markdownFileUrl = `${blogBase}${path}.md`;

    try {
      // 获取 Markdown 文件内容
      const markdownResponse = await fetch(markdownFileUrl);

      if (markdownResponse.status === 404) {
        return new Response("<h1>404: Page not found</h1>", { status: 404, headers: { "Content-Type": "text/html; charset=UTF-8" } });
      }

      const markdownContent = await markdownResponse.text();
      const htmlContent = await markdownToHtml(markdownContent);

      return new Response(htmlContent, { headers: { "Content-Type": "text/html; charset=UTF-8" } });
    } catch (err) {
      return new Response("<h1>500: Internal Server Error</h1>", { status: 500, headers: { "Content-Type": "text/html; charset=UTF-8" } });
    }
  },
};

// 使用 Marked.js 解析 Markdown
async function markdownToHtml(markdown) {
  const markedJsCDN = "https://cdn.jsdelivr.net/npm/marked/marked.min.js";

  // 使用 <script> 引入 marked.js 并调用它
  return `
    <!DOCTYPE html>
    <html>
    <head>
      <title>My Blog</title>
      <script src="${markedJsCDN}"></script>
      <style>
        body { font-family: Arial, sans-serif; line-height: 1.6; margin: 20px; padding: 0; }
        pre { background: #f4f4f4; padding: 10px; border-radius: 5px; }
      </style>
    </head>
    <body>
      <div id="content"></div>
      <script>
        const markdownContent = ${JSON.stringify(markdown)};
        document.getElementById('content').innerHTML = marked.parse(markdownContent);
      </script>
    </body>
    </html>
  `;
}

// 首页渲染函数
async function renderIndex() {
  return `
    <!DOCTYPE html>
    <html>
    <head>
      <title>My Blog</title>
      <style>
        body { font-family: Arial, sans-serif; line-height: 1.6; margin: 20px; padding: 0; }
        a { color: #3498db; text-decoration: none; }
      </style>
    </head>
    <body>
      <h1>Welcome to My Blog</h1>
      <ul>
        <li><a href="/linux系统常用快捷键">linux系统常用快捷键</a></li>
        <li><a href="/使用oendrive搭建alist">使用oendrive搭建alist</a></li>
        <li><a href="/防火墙操作">防火墙操作</a></li>
      </ul>
    </body>
    </html>
  `;
}

```



现在访问你的 Workers 即可看到文章。