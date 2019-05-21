# frozen_string_literal: true

# This file contains tasks with no namespace.
# All namespaced tasks are defined in the '../rakelib' directory.
# Each namespace is defined in a separate file.
# For example, 'preview:all' is defined in the '../rakelib/preview.rake' file.
# To see the list of tasks to use, run 'rake -T'.

require 'html-proofer'
require 'kramdown'
require 'launchy'
require 'colorator'

# Load ruby files with helper methods from the 'rakelib/' directory
require_relative 'rakelib/lib/link-checker.rb'
require_relative 'rakelib/lib/converter.rb'
require_relative 'rakelib/lib/double-slash-check.rb'
require_relative 'rakelib/lib/doc-config.rb'

desc "Same as 'rake', 'rake preview'"
task default: %w[preview]

desc "Same as 'test:report'"
task test: %w[test:report]

desc 'Preview the devdocs locally'
task preview: %w[install clean] do
  puts 'Generating devdocs locally ... '.magenta
  if File.exist?('_config.local.yml')
    print 'enabled the additional configuration parameters from _config.local.yml: $ '.magenta
    sh 'bundle exec jekyll serve --incremental \
                                 --open-url \
                                 --livereload \
                                 --trace \
                                 --config _config.yml,_config.local.yml \
                                 --plugins _plugins,_checks'
  else
    Rake::Task['preview:all'].invoke
  end
end

task :clean do
  print 'Cleaning after the last site generation: $ '.magenta
  sh 'bundle exec jekyll clean'
  puts 'Clean!'.green
end

task :install do
  print 'Install gems listed in the Gemfile: $ '.magenta
  sh 'bundle install'
  puts 'Installed!'.green
end

desc 'Build the entire website'
task build: %w[clean] do
  print 'Building the site with Jekyll: $ '.magenta
  sh 'bundle exec jekyll build --verbose --trace'
  puts 'Built!'.green
end

desc 'Pull docs from external repositories'
task init: %w[multirepo:init]

desc 'Check Markdown syntax in modified files or in a particular file or directory by path (e.g. path=mftf)'
task :lint do
  path = ENV['path']
  unless path
    staged_files = `git ls-files -m`.split("\n")
    staged_md_files = staged_files.select { |file| File.extname(file) == '.md' }
    abort 'Cannot find any modified .md files.' if staged_md_files.empty?
    path = staged_md_files.join(' ')
  end
  report = `bin/mdl #{path}`
  puts report.yellow
  puts 'The rules are defined in _checks/md_style'
end
