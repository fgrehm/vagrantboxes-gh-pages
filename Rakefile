require "rubygems"
require 'rake'
require 'yaml'
require 'time'

SOURCE = "."
CONFIG = {
  'layouts' => File.join(SOURCE, "_layouts"),
  'posts' => File.join(SOURCE, "_posts"),
  'post_ext' => "md",
}

# Usage: rake post name="A name" [date="2014-02-15"]
desc "Begin a new post in #{CONFIG['posts']}"
task :post do
  abort("rake aborted: '#{CONFIG['posts']}' directory not found.") unless FileTest.directory?(CONFIG['posts'])
  name = ENV["name"] || "new box"
  category = 'boxes'
  provider = ask("supported provider?[Virtualbox,vMware,Lxc,Kvm,libvIrt]",['v','m','l','k','i'])
  provider = provider.downcase.strip
  case provider
  when 'v'
    provider = 'VirtualBox'
  when 'm'
    provider = 'VMWare'
  when 'l'
    provider = 'Vagrant-LXC'
  when 'k'
    provider = 'Vagrant-KVM'
  when 'i'
    provider = 'Vagrant-Libvirt'
  else
    provider = 'Unknown'
  end
  desc = get_stdin("Description?")
  size = get_stdin("Size?(eg. 100MB)")
  taglist = get_stdin("Tags?(eg. tag1,tag2)")
  slug = name.downcase.strip.gsub(' ', '-').gsub(/[^\w-]/, '')
  tags = taglist.downcase.strip.split(',')
  begin
    date = (ENV['date'] ? Time.parse(ENV['date']) : Time.now).strftime('%Y-%m-%d')
  rescue => e
    puts "Error - date format must be YYYY-MM-DD, please check you typed it correctly!"
    exit -1
  end
  filename = File.join(CONFIG['posts'], "#{category}", "#{date}-#{slug}.#{CONFIG['post_ext']}")
  if File.exist?(filename)
    abort("rake aborted!") if ask("#{filename} already exists. Do you want to overwrite?", ['y', 'n']) == 'n'
  end
  link = get_stdin("URL? (eg. https://example.com/)")
  puts "Creating new post: #{filename}"
  open(filename, 'w') do |post|
    post.puts "---"
    post.puts "name: \"#{name}\""
    post.puts "description: #{desc}"
    post.puts "provider: #{provider}"
    post.puts "link: #{link}"
    post.puts "size: #{size}"
    post.puts "category: boxes"
    post.puts "tags:"
    tags.each do |t|
      post.puts" - #{t}"
    end
    post.puts "---"
  end
end # task :post

desc "Launch preview environment"
task :preview do
  system "jekyll serve -w"
end # task :preview

def ask(message, valid_options)
  if valid_options
    answer = get_stdin("#{message} #{valid_options.to_s.gsub(/"/, '').gsub(/, /,'/')} ") while !valid_options.include?(answer)
  else
    answer = get_stdin(message)
  end
  answer
end

def get_stdin(message)
  print message
  STDIN.gets.chomp
end
