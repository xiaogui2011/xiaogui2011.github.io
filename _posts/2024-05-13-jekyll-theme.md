---
title: jekyll-theme
date: 2024-05-13 22:05:56  +0800
author: john
categories: [博客搭建, 使用规范]
tags: Jekyll jekyll-theme 博客
layout: post
---


## 文章头部配置信息

xxx

## 推荐的主题
xxx

## 全局搜索
xxx



## 文章目录结构
[jekll + chirpy 打造个人博客](https://www.tangzhexuan.com/posts/jekyll+chirpy%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2/)

```markdown
  ---
title: Getting Started
author: Cotes Chung
date: 2019-08-09 20:55:00 +0800
categories: [Blogging, Tutorial]
tags: [getting started]
pin: true
  ---
```

[write-a-new-post.md]()


## 注意事项

文章的名称必须符合规范

[一小时内使用Jekyll搭建自己的GitHub博客](https://abekthink.github.io/website/write-blogs-using-github-and-jekyll/)

```markdown
2016-06-04-hello-world.md
2017-08-18-writing-blogs.markdown
```


## 注意事项二级目录
2222

## 自动生成文件的源数据

[Jekyll 自动生成文章](https://blog.csdn.net/freehyan/article/details/51879554)

[使用rake自动生成文件](https://jinguoxing.github.io/jekyll/rake/2015/06/01/jekyll-github-pages/)
```shell
(base) 0 10:41:13 z:/tmp $sudo gem install rake
Fetching rake-13.2.1.gem
Successfully installed rake-13.2.1
Parsing documentation for rake-13.2.1
Installing ri documentation for rake-13.2.1
Done installing documentation for rake after 1 seconds
1 gem installed
```

```shell
(base) 0 10:50:48 z:xiaogui2011.github.io $rake post title="追更"    
/System/Library/Frameworks/Ruby.framework/Versions/2.6/usr/lib/ruby/2.6.0/universal-darwin22/rbconfig.rb:21: warning: Insecure world writable dir /Users/zhangzhiyuan/go/pkg/mod/golang.org in PATH, mode 040777
rake aborted!
No Rakefile found (looking for: rakefile, Rakefile, rakefile.rb, Rakefile.rb)
/Library/Ruby/Gems/2.6.0/gems/rake-13.2.1/exe/rake:27:in `<top (required)>'
(See full trace by running task with --trace)
```

## 需要rakefile


[使用Rakefile实现文章生成器](https://lllovol.com/p/rake/)

```shell
touch Rakefile
```

```
task :default => :new

require 'fileutils'

desc "创建新 post"
task :new do
  puts "请输入要创建的文章文件文件名字："
    @url = STDIN.gets.chomp
    puts "请输入文章的标题："
    @name = STDIN.gets.chomp
    puts "请输入文章的子标题："
    @slug = STDIN.gets.chomp
    puts "请输入文章的分类，以空格分隔："
    @categories = STDIN.gets.chomp
    puts "请输入文章的标签："
    @tag = STDIN.gets.chomp
    @slug = "#{@url}"
    @slug = @slug.downcase.strip.gsub(' ', '-')
    @date = Time.now.strftime("%F")
    @post_name = "_posts/#{@date}-#{@slug}.md"
    if File.exist?(@post_name)
            abort("文件名已经存在！创建失败")
    end
    FileUtils.touch(@post_name)
    open(@post_name, 'a') do |file|
            file.puts "---"
            file.puts "layout: post"
            file.puts "title: #{@name}"
            file.puts "slug: #{@slug}"
            file.puts "author: ymkNK"
            file.puts "date: #{Time.now}"
            file.puts "categories: #{@categories}"
            file.puts "tags: #{@tag}"
            file.puts "img: 1.jpg"
            file.puts "---"
    end
    exec "vim #{@post_name}"
end
```


## 注释

```markdown
Sun
: the star around which the earth orbits
```

## 引用颜色框

[引用颜色框](https://raw.githubusercontent.com/cotes2020/jekyll-theme-chirpy/master/_posts/2019-08-08-text-and-typography.md)

[效果](https://chirpy.cotes.page/posts/text-and-typography/)

```
> An example showing the `tip` type prompt. 绿色
{: .prompt-tip }

> An example showing the `info` type prompt. 蓝色
{: .prompt-info }

> An example showing the `warning` type prompt. 黄色
{: .prompt-warning }

> An example showing the `danger` type prompt. 红色
{: .prompt-danger }
```
