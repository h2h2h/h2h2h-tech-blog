# h2h2h-tech-blog

使用 Hugo 和 PaperMod 构建的技术博客。

## 本地预览

首次克隆后初始化主题：

```sh
git submodule update --init --recursive
```

预览时将页面保存在内存中，避免把带有 localhost 链接的预览文件写入用于部署的 `public/`：

```sh
hugo server --renderToMemory
```

## Cloudflare Workers 部署

生产地址配置在 `hugo.yaml` 的 `baseURL` 中。修改配置或文章后，必须重新构建，配置不会自动修改已有的 HTML：

```sh
hugo --minify --cleanDestinationDir
```

将生成的 `public/` 目录部署到 Cloudflare Workers 静态资源。

- 如果直接上传静态文件，每次都上传重新构建后的 `public/`。
- 如果从 Git 仓库部署已提交的静态文件，提交并推送更新后的 `public/`。
- 如果由 Cloudflare 自动构建，构建命令设为 `hugo --minify --cleanDestinationDir`，静态资源目录设为 `public`，并确保构建环境安装了 Hugo 且初始化了主题子模块。

部署前可检查是否混入本地预览链接或 LiveReload 脚本（正常情况没有输出）：

```sh
rg -n 'localhost:1313|127\.0\.0\.1:1313|livereload\.js' public
```

部署后检查首页的 `article` 链接是否指向线上 `/posts/`，并打开 `/posts/hello-blog/` 验证文章内容。
