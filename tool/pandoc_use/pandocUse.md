# 使用手册

## 安装 pandoc 

windows 下安装 [链接](https://github.com/jgm/pandoc/releases/tag/1.19.1)

### 执行下面命令测试 pandoc

 
````
  pandoc -v
  
```` 

###进入你的mk 文件目录执行以下目录

````
 
pandoc xx.md -o xx.html

```` 
 
 


## 参数说明

 -  -s 输出独立文档
 -  -o 输出到那个文件(文件不存在创建这个文件)
 -  --toc  在页面中生成索引目录
 -  --number-sections  根据文档索引来生成索引值
 -  --self-contained 将外部资源转化为base64 文件,放入html, 访问时不需要引入外部文件
 
```js
 
  --toc-depth= 3   // 制定# ##  ### 索引三级  默认为3级
  
```
 

 
## For example
 
 
 ```js
 
	pandoc 2.1.http.md -o 2http.html --template tp.html --list-highlight-styles --css tp.css --self-contained --toc --toc-depth 2

	--number-sections
	pandoc  --number-sections --template tp.html --list-highlight-styles --css tp.css --self-contained --toc --toc-depth 2 *.md -o all.html

	pandoc  --number-sections --template tp.html --css tp.css --self-contained --toc --toc-depth 2 *.md -o all.html

	pandoc  2.1.http.md -o all.html --number-sections --template tp.html --highlight-style pygments -S --css tp.css --self-contained --toc 
	 
```
 
## 参考链接
 
 [参考链接](http://www.ituring.com.cn/article/746)

     
