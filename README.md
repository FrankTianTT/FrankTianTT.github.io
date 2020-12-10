
### 网站的npm依赖

- Local Search：`npm install hexo-generator-searchdb --save`
- NexT主题渲染：`npm install hexo-renderer-swig --save`
- git部署：`npm install hexo-deployer-git --save`

删除原装的marked，安装pandoc

```
npm uninstall hexo-renderer-marked --save
npm install hexo-renderer-pandoc --save
```

如果在ubuntu中安装，需要`sudo apt install pandoc`

在mac中别忘了卸载anaconda自带的pandoc，否则会因为版本过低而报错。

hexo的图片功能
npm install hexo-asset-image --save

支持数学公式
npm install hexo-renderer-mathjax --save