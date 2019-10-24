# 利用node 的hexo 框架在github上搭建博客 [@MyBlog](http://jasonzeng.top)  
  - http://zengxp0605.github.io 的资源, markdown 格式文件
  - [http://jason-zi.coding.me/](http://jason-zi.coding.me/)

  - See [About Hexo](http://jason-zi.coding.me/2016/04/03/0-how-to-use-hexo/)


# 使用说明

- 文章原稿位于`source/_posts` 目录下

- 更新`jasonzeng.top`
  - 文章编辑完成后执行`npm run update`
  - 或者 `./bin/update.sh`
  - 执行后会更新`coding.net`源码,并且登陆阿里云服务器拉取`html`文件,从而更新站点


## 本地开发备注
```js
// 需要初始化目录
# 主题(_config.yml需要用自己的)
git clone https://github.com/litten/hexo-theme-yilia.git themes/yilia

# html仓库1
git clone https://git.dev.tencent.com/jason-zi/jason-zi.git

# html仓库2
mkdir _github && cd _github
git clone https://github.com/zengxp0605/blog-source.git

# ssh_pull 目录拉取阿里云服务器的html代码
ssh_pull/config.js 示例
module.exports = {
	ali: {
		host: '106.15.21.01',
		user: 'root',
		password: 'my.pass',
	},
};
```