#!/usr/bin/env rake

require 'bundler/setup'
require 'rubocop/rake_task'
require 'foodcritic'
require 'kitchen'

namespace :style do
  desc 'Run Ruby style checks'
  RuboCop::RakeTask.new(:ruby)

  desc 'Run Chef style checks'
  FoodCritic::Rake::LintTask.new(:chef)
end

desc 'Run all style checks'
task style: ['style:chef', 'style:ruby']

namespace :integration do
  desc 'Run Test Kitchen with Vagrant'
  task :vagrant do
    Kitchen.logger = Kitchen.default_file_logger
    Kitchen::Config.new.instances.each do |instance|
      instance.test(:always)
    end
  end

  desc 'Run Test Kitchen with cloud plugins'
  task :cloud do
    run_kitchen = true
    if ENV['TRAVIS'] == 'true' && ENV['TRAVIS_PULL_REQUEST'] != 'false'
      run_kitchen = false
    end

    if run_kitchen
      Kitchen.logger = Kitchen.default_file_logger
      @loader = Kitchen::Loader::YAML.new(project_config: './.kitchen.cloud.yml')
      config = Kitchen::Config.new(loader: @loader, log_level: :debug)
      config.instances.each do |instance|
        instance.test(:always)
      end
    end
  end
end

desc 'Run all tests on Travis'
task travis: %w(style integration:cloud)

# Default
task default: %w(style integration:vagrant)
