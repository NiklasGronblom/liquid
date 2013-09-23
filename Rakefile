#!/usr/bin/env ruby

require 'rubygems'
require 'bundler/setup'

require 'rake'
require 'rake/testtask'
require 'rspec'
require 'rspec/core/rake_task'
require 'rubygems/package_task'

RSpec::Core::RakeTask.new("spec") do |spec|
  spec.pattern = "spec/**/*_spec.rb"
end

desc "Run the Integration Specs (rendering)"
RSpec::Core::RakeTask.new("spec:integration") do |spec|
  spec.pattern = "spec/unit/*_spec.rb"
end

desc "Run the Unit Specs"
RSpec::Core::RakeTask.new("spec:unit") do |spec|
  spec.pattern = "spec/unit/*_spec.rb"
end

desc "Run all the specs without all the verbose spec output"
RSpec::Core::RakeTask.new('spec:progress') do |spec|
  spec.rspec_opts = %w(--format progress)
  spec.pattern = "spec/**/*_spec.rb"
end

desc 'run test suite with default parser'
Rake::TestTask.new(:base_test) do |t|
  t.libs << '.' << 'lib' << 'test'
  t.test_files = FileList['test/liquid/**/*_test.rb']
  t.verbose = false
end

task :default => [:spec, :test]

gemspec = eval(File.read('locomotivecms-liquid.gemspec'))

desc 'run test suite with warn error mode'
task :warn_test do
  ENV['LIQUID_PARSER_MODE'] = 'warn'
  Rake::Task['base_test'].invoke
end

desc 'runs test suite with both strict and lax parsers'
task :test do
  ENV['LIQUID_PARSER_MODE'] = 'lax'
  Rake::Task['base_test'].invoke
  ENV['LIQUID_PARSER_MODE'] = 'strict'
  Rake::Task['base_test'].reenable
  Rake::Task['base_test'].invoke
end

Gem::PackageTask.new(gemspec) do |pkg|
  pkg.gem_spec = gemspec
end

desc "Build the gem and release it to rubygems.org"
task :release => :gem do
  puts "Tagging #{gemspec.version}..."
  system "git tag -a #{gemspec.version} -m 'Tagging #{gemspec.version}'"
  puts "Pushing to Github..."
  system "git push --tags"
  puts "Pushing to rubygems.org..."
  system "gem push pkg/#{gemspec.name}-#{gemspec.version}.gem"
end

namespace :benchmark do

  desc "Run the liquid benchmark with lax parsing"
  task :run do
    ruby "./performance/benchmark.rb lax"
  end

  desc "Run the liquid benchmark with strict parsing"
  task :strict do
    ruby "./performance/benchmark.rb strict"
  end
end

namespace :profile do

  desc "Run the liquid profile/performance coverage"
  task :run do
    ruby "./performance/profile.rb"
  end

  desc "Run KCacheGrind"
  task :grind => :run  do
    system "qcachegrind /tmp/liquid.rubyprof_calltreeprinter.txt"
  end

end

desc "Run example"
task :example do
  ruby "-w -d -Ilib example/server/server.rb"
end
