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

## 开启评论区

当然可以开启评论区。根据你提供的配置文件，你可以选择使用三种评论系统：Disqus、Utterances 或 Giscus。以下是每种评论系统的配置步骤：

### 使用 Disqus
Disqus 是一个流行的评论系统，可以轻松集成到 Jekyll 网站中。需要在 Disqus 上注册并获取一个 shortname。

1. 在 Disqus 网站上注册并创建一个站点（如果你还没有）。
2. 获取你的 Disqus shortname。
3. 在你的配置文件中找到 `comments` 部分，填写如下：

```yaml
comments:
  provider: disqus
  disqus:
    shortname: your_disqus_shortname
```

将 `your_disqus_shortname` 替换为你从 Disqus 获取的 shortname。

### 使用 Utterances
Utterances 使用 GitHub issues 作为评论系统，需要你有一个 GitHub 仓库来存储评论。

1. 创建一个 GitHub 仓库（如果你还没有）。
2. 在配置文件中找到 `comments` 部分，填写如下：

```yaml
comments:
  provider: utterances
  utterances:
    repo: your_github_username/your_repo_name
    issue_term: pathname
```

将 `your_github_username/your_repo_name` 替换为你的 GitHub 用户名和仓库名称。

### 使用 Giscus
Giscus 也是基于 GitHub issues 的评论系统，配置稍微复杂一些。

1. 访问 [Giscus](https://giscus.app/) 并按照步骤设置。
2. 获取必要的 `repo`、`repo_id`、`category` 和 `category_id` 信息。
3. 在配置文件中找到 `comments` 部分，填写如下：

```yaml
comments:
  provider: giscus
  giscus:
    repo: your_github_username/your_repo_name
    repo_id: your_repo_id
    category: your_category_name
    category_id: your_category_id
    mapping: pathname
    strict: 0
    input_position: bottom
    lang: en
    reactions_enabled: 1
```

将 `your_github_username/your_repo_name`、`your_repo_id`、`your_category_name` 和 `your_category_id` 替换为从 Giscus 获取的信息。

完成以上步骤后，保存配置文件并重新构建你的 Jekyll 网站。这样就可以在文章页面中启用评论区了。
