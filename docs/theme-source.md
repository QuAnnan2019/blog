# LoveIt Theme Source

The first local preview uses the LoveIt theme source from commit
`23bc60506c5632547bdc7b56b927ae154a321c27` of
`https://github.com/dillonzq/LoveIt`.

It was downloaded through GitHub's repository archive API because this
machine could reach the API but could not establish a Git HTTPS connection to
`github.com:443`. The theme therefore lives as a vendored copy at
`themes/LoveIt`, rather than a Git submodule. When Git HTTPS access is
available, replace it with the planned pinned submodule.

## 首页文章封面

首页最近更新卡片会读取文章或笔记 Front Matter 的 `featuredImage`：

```yaml
featuredImage: "images/posts/your-cover.jpg"
```

图片放在 `static/images/posts/` 后，首页会生成
`/blog/images/posts/your-cover.jpg`。未填写 `featuredImage` 时，博客和笔记
会显示本地 CSS 占位封面，不请求外部图片。
