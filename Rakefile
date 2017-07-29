require 'date'
require 'erb'

task default: :help

desc 'Help'
task :help do
  puts <<-Help
  Help - List of commands and examples
  
  1. Create new Post
    $ rake post:new [Title] [Options]
    Example:
      rake post:new 'New tutorial on React Native'
      # Will generate a file called '_posts/2017-08-02-new-tutorial-on-react-native.md'
    
    Example2:
      rake post:new 'New tutorial on React Native' dir='javascript'
      # Will generate a file called '_posts/javascript/2017-08-02-new-tutorial-on-react-native.md'
    
  2. Undo creation of a new Post | Delete posts created today
    $ rake post:delete [Title] [Options]
    Example:
      rake post:delete 'New tutorial on React Native'
      # Will delete a file called '_posts/2017-08-02-new-tutorial-on-react-native.md'
    
    Example2:
      rake post:delete 'New tutorial on React Native' dir='javascript'
      # Will delete a file called '_posts/javascript/2017-08-02-new-tutorial-on-react-native.md'
    
    OPTIONS
    dir=,  directory=       Parent directory of the post to manage posts
    desc=, description=     All about the article in short
    cat=,  category=        Category of the article
  Help
end

PARENT_DIR  = ENV['dir']
CATEGORY    = ENV['cat']
DESCRIPTION = ENV['desc']

namespace :post do
  desc 'Create new post: '
  task :new do
    puts "Creating a new post on title '#{ARGV[1]}'"
    post = Post.new(title: ARGV[1], description: DESCRIPTION, category: CATEGORY, options: {dir: PARENT_DIR})
    post.create
    exit
  end
  
  task :delete do
    puts "Deleting a post on title '#{ARGV[1]}'"
    post = Post.new(title: ARGV[1], options: {dir: PARENT_DIR})
    post.delete
    exit
  end
end

class Post
  attr_reader :title, :description, :category, :parent_dir
  def initialize(title:, description: nil, category: nil, options: {})
    @title       = title
    @description = description
    @category    = category
    @parent_dir  = options[:dir]
    
  end
  
  def create
    dir = File.expand_path(parent_dir, './_posts/')
    
    if !parent_dir.empty? && !File.exist?(dir)
      $stdout.puts "Creating the directory '#{dir}' ..."
      Dir.mkdir(dir)
    end
    
    file_name = File.expand_path("#{Date.today}-#{generate_slug}.md", dir)
    
    if File.exist?(file_name)
      $stderr.puts "File already exists '#{file_name}'"
      exit
    else
      File.write(file_name, ERB.new(DATA).result(binding))
      $stdout.puts "Post created, see '#{file_name}'"
    end
  end
  
  def delete
    dir = File.expand_path(parent_dir.to_s, './_posts/')
    file_name = File.expand_path("#{Date.today}-#{generate_slug}.md", dir)
    
    if !File.exist? file_name
      $stderr.puts "Post not found: '#{file_name}'"
       exit
    end
    
    File.delete(file_name)
    $stdout.puts "Post deleted! '#{file_name}'"
    exit
  end
  
  private
    def generate_slug
      @slug ||= title.gsub(%r{[^0-9a-zA-Z\s]}, '').gsub(/\s/, '-').downcase
    end
end

# 2017-07-02 23:46:59 +0545
DATA = <<-TEMPLATE
---
layout:             post
title:              <%= title || 'TODO Write a title here'%>
description:        <%= description || 'TODO Title of the post; appears on Top of page, in Search Engine and Title Bar' %>
date:               <%= Time.now %> 
category:           <%= category || 'TODO JavaScript' %>
tags:               TODO ReactJs,React,Ruby,Webpack,dev-server,setup,SPA,learn,single page app

image_url:          /assets/images/custom-banner.png 
                    # Some link to image which appears in Twitter Cards/FB Box
meta:
  title:            <%= title || 'TODO Has precedence over the `title` above in SEO'%>
  description:      <%= description || 'TODO Some description you see in Twitter Card, Facebook Share Box, and SEO' %>


top_banner_image:   'linear-gradient(120deg, #d04b4b, #f3f3f3)' # or url([Some URL])
banner_size:        contain # possible values are `cover`, `contain`, `banner`
banner_color:       '#156299'
banner_mode:        soft-light # possible vals are screen, overlay, darken, lighten, color-dodge, color-burn, 
                    # hard-light, soft-light, difference, exclusion, hue, saturation, color, and luminosity

# Define links as variable
#   And access them as page.var_name
default_screen:     /assets/images/create-react-app-default.png
react_logo:         /assets/images/learn-reactjs-banner.png                 
---
TEMPLATE