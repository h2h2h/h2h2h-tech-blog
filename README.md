# h2h2h-tech-blog

使用 Hugo 和 PaperMod 构建的技术博客。

## 本地预览

首次克隆后初始化主题：

```sh
git submodule update --init --recursive
```

预览草稿文章：

```sh
hugo server -D --renderToMemory
```

`config/development/hugo.yaml` 将开发环境的输出目录设为 `.hugo-dev/`（已加入 `.gitignore`）。即使直接运行 `hugo server`，也不会覆盖用于部署的 `public/`。添加此配置后，如果原先有预览服务在运行，请停止并重新启动。

## Cloudflare Workers 部署

生产地址配置在 `hugo.yaml` 的 `baseURL` 中。修改配置或文章后，必须重新构建，配置不会自动修改已有的 HTML：

```sh
hugo --environment production --minify --cleanDestinationDir
```

将生成的 `public/` 目录部署到 Cloudflare Workers 静态资源。

- 如果直接上传静态文件，每次都上传重新构建后的 `public/`。
- 如果从 Git 仓库部署已提交的静态文件，提交并推送更新后的 `public/`。
- 如果由 Cloudflare 自动构建，构建命令设为 `hugo --environment production --minify --cleanDestinationDir`，静态资源目录设为 `public`，并确保构建环境安装了 Hugo 且初始化了主题子模块。

部署前可检查是否混入本地预览链接或 LiveReload 脚本（正常情况没有输出）：

```sh
rg -n 'localhost:1313|127\.0\.0\.1:1313|livereload\.js' public
```

部署后检查首页的 `article` 链接是否指向线上 `/posts/`，并打开 `/posts/hello-blog/` 验证文章内容。

## 每次发布新文章

1. 执行 `hugo new content posts/文章英文名.md`，编辑生成的 Markdown 文件。
2. 使用上面的预览命令检查正文。
3. 将文章的 `draft` 改为 `false`，确认 `date` 不是未来时间。
4. 执行生产构建命令，更新 `public/`。
5. 执行 `git status` 检查变更，将文章源文件和 `public/` 一起提交并推送到 Cloudflare 监听的分支。
6. 在 Cloudflare 确认对应 Git 提交部署成功，再检查线上文章。

日常发布命令示例：

```sh
hugo --environment production --minify --cleanDestinationDir
git add content public static
git commit -m "post: publish new article"
git push
```
