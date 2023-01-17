# shaoyecheng.com

个人网站仓库。使用[hexo](https://hexo.io/zh-cn/)生成静态网页，使用[caddy](https://caddyserver.com/)作为服务器。使用[keep](https://github.com/XPoet/hexo-theme-keep)主题。使用[dogecloud](https://www.dogecloud.com/)作为视频云。

对整个仓库使用git做版本管理，通过git push更新服务器版本，在服务器上使用`auto_generate.sh`脚本监视修改并自动更新静态页面。`hexo`的`--watch`功能只能监视文章修改，无法监视`_config`等配置文件修改，因此修改配置文件后需要手动生成。

## 常用指令备忘

```
# 新建文章
# hexo new [layout] <title>
hexo new post "new post"

# 隐藏文章（不显示在主页/归档）
header中添加tag, 设置 hidden: true
```
