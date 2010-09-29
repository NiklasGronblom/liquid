#!/usr/bin/env ruby
require 'rubygems'
require 'rake'
require 'rake/gempackagetask'

task :default => 'spec'

gemspec = eval(File.read('locomotive_liquid.gemspec'))
Rake::GemPackageTask.new(gemspec) do |pkg|
  pkg.gem_spec = gemspec
end

desc "build the gem and release it to rubygems.org"
task :release => :gem do
  sh "gem push pkg/locomotive_liquid-#{gemspec.version}.gem"
end

namespace :profile do

  task :default => [:run]

  desc "Run the liquid profile/perforamce coverage"
  task :run do

    ruby "performance/shopify.rb"

  end

  desc "Run KCacheGrind"
  task :grind => :run  do
    system "kcachegrind /tmp/liquid.rubyprof_calltreeprinter.txt"
  end
end

