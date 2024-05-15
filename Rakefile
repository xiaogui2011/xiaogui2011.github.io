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
