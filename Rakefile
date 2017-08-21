# -*- coding: utf-8 -*-
require 'colorize'
require 'fileutils'

task :default do
  system 'rake -T'
end

desc 'convert SIZE, all figs with smaller size'
task :convert do
  size = ARGV[1] || '50%'
  Dir.entries('figs').each do |file|
    convert_eps(file) if file =~ /.eps$/
    next unless file =~ /.jpg$|.jpeg$|.png$/
    split = file.split('.')
    next if split[-2] =~ /small/
    base = split[0..-2].join('.')
    ext = file.split('.')[-1]
    new_name = [base, 'small', ext].join('.')
    p command = "convert figs/#{file} -scale #{size} figs/#{new_name}"
    system command
  end
end

def convert_eps(file)
  base = file.split('.')[0..-2].join('.')
  new_name = [base, 'jpg'].join('.')
  p command = "convert figs/#{file} -scale 50% figs/#{new_name}"
  system command
end

desc 'mk whole size dependency plot'
task :mk_whole do
  p pwd = Dir.getwd
  Dir.chdir('../data/find_min')
  system './find_min gnuplot_svg all'
  FileUtils.cp('tmp.svg', pwd)
end

RE_FIGS_EXT = /(.+\.jpg)|(.+\.jpeg)|(.+\.png)/
desc 'mk latex FILE, stored in latex dir'
task :mk_latex do
  p target = ARGV[1]
  p tex_src = target.sub('.ipynb', '.tex')
  system "jupyter nbconvert --to latex #{target}"
  lines = File.readlines(tex_src)
  lines.each_with_index do |line,i|
    line.sub!("\documentclass[11pt]{article}",
              "\documentclass[11pt,dvipdfmx]{jsarticle}")
    line.sub!('./figs/', '../figs/')
    # change_eq_exp(line)
    print line.red if line =~ RE_FIGS_EXT
    line.sub!(line, '%' + line) if line.include?('.svg')
  end
  File.open(tex_src, 'w') { |file| file.print lines.join }
  FileUtils.mkdir_p('latex')
  FileUtils.mv(tex_src, 'latex')

  mk_xbb
  exit
end

def mk_xbb
  FileUtils.cd('./figs')
  Dir.entries('.').each do |file|
    next unless file =~ RE_FIGS_EXT
    p m = file.split('.')[0..-2]
    next if File.exists?(m.join('.')+'.xbb')
    command = "extractbb #{file}"
    puts command.light_blue
    system command
  end
end

def change_eq_exp(line)
  while m = line.match(/(\\\(.*?\\\))/) 
    print i.to_s+" : "
    p subs = '$'+ m[1].match(/\\\((.*?)\\\)/)[1] + '$' 
    line.sub!(m[1], subs)
  end
  line
end
