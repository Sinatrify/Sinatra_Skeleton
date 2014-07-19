require 'rake'
require 'rspec/core/rake_task'


require ::File.expand_path('../config/environment', __FILE__)

# Include all of ActiveSupport's core class extensions, e.g., String#camelize
require 'active_support/core_ext'

namespace :generate do
  desc "Create an empty model in app/models, e.g., rake generate:model NAME=appname"
  task :model do
    unless ENV.has_key?('NAME')
      raise "Must specificy model name, e.g., rake generate:model NAME=appname"
    end

    model_name     = ENV['NAME'].camelize
    model_filename = ENV['NAME'].underscore + '.rb'
    model_path = APP_ROOT.join('app', 'models', model_filename)

    if File.exist?(model_path)
      raise "ERROR: Model file '#{model_path}' already exists"
    end

    puts "Creating #{model_path}"
    File.open(model_path, 'w+') do |f|
      f.write(<<-EOF.strip_heredoc)
      class #{model_name} < ActiveRecord::Base
        # Remember to create a migration!
      end
      EOF
    end
  end
#Controllers
desc "Create an empty controller in app/controllers, e.g., rake generate:controller NAME=appname"
task :controller do
  unless ENV.has_key?('NAME')
    raise "Must specificy controller name, e.g., rake generate:controller NAME=appname"
  end
  name = ENV['NAME']
  controller_name     = (name + 's_controller').camelize
  controller_filename = name.underscore + '.rb'
  controller_path = APP_ROOT.join('app', 'controllers', controller_filename)

  if File.exist?(controller_path)
    raise "ERROR: controller file '#{controller_path}' already exists"
  end

  puts "Creating #{controller_path}"
  File.open(controller_path, 'w+') do |f|
    f.write(<<-EOF.strip_heredoc)
    get '/' do
      "Hello world"
      erb:"index"
    end
    EOF
  end
end
#VIEW
desc "Create an empty view in app/views, e.g., rake generate:view NAME=appname"
task :view do
  unless ENV.has_key?('NAME')
    raise "Must specificy view name, e.g., rake generate:view NAME=appname"
  end
  name = ENV['NAME']
  view_name     = (name).camelize
  view_filename = name.underscore + '.erb'
  view_path = APP_ROOT.join('app', 'views', "#{name}", view_filename)

  if File.exist?(view_path)
    raise "ERROR: view file '#{view_path}' already exists"
  end

  puts "Creating #{view_path}"
  `mkdir app/views/#{name}`
  File.open(view_path, 'w+') do |f|
    f.write(<<-EOF.strip_heredoc)
    <h1>#{view_name}</h1>
    EOF
  end
end
#Migration
desc "Create an empty migration in db/migrate, e.g., rake generate:migration NAME=create_tasks"
task :migration do
  unless ENV.has_key?('NAME')
    raise "Must specificy migration name, e.g., rake generate:migration NAME=create_tasks"
  end

  name     = ENV['NAME'].camelize
  filename = "%s_%s.rb" % [Time.now.strftime('%Y%m%d%H%M%S'), ENV['NAME'].underscore]
  path     = APP_ROOT.join('db', 'migrate', filename)

  if File.exist?(path)
    raise "ERROR: File '#{path}' already exists"
  end

  puts "Creating #{path}"
  File.open(path, 'w+') do |f|
    f.write(<<-EOF.strip_heredoc)
    class #{name} < ActiveRecord::Migration
      def change
      end
    end
    EOF
  end
end

desc "Create an empty model spec in spec, e.g., rake generate:spec NAME=appname"
task :spec do
  unless ENV.has_key?('NAME')
    raise "Must specificy migration name, e.g., rake generate:spec NAME=appname"
  end

  name     = ENV['NAME'].camelize
  filename = "%s_spec.rb" % ENV['NAME'].underscore
  path     = APP_ROOT.join('spec', filename)

  if File.exist?(path)
    raise "ERROR: File '#{path}' already exists"
  end

  puts "Creating #{path}"
  File.open(path, 'w+') do |f|
    f.write(<<-EOF.strip_heredoc)
    require 'spec_helper'

    describe #{name} do
    pending "add some examples to (or delete) #{__FILE__}"
  end
  EOF
end
end

end

namespace :db do
  desc "Drop, create, and migrate the database"
  task :reset => [:drop, :create, :migrate]

  desc "Create the databases at #{DB_NAME}"
  task :create do
    puts "Creating development and test databases if they don't exist..."
    system("createdb #{APP_NAME}_development && createdb #{APP_NAME}_test")
  end

  desc "Drop the database at #{DB_NAME}"
  task :drop do
    puts "Dropping development and test databases..."
    system("dropdb #{APP_NAME}_development && dropdb #{APP_NAME}_test")
  end

  desc "Migrate the database (options: VERSION=x, VERBOSE=false, SCOPE=blog)."
  task :migrate do
    ActiveRecord::Migrator.migrations_paths << File.dirname(__FILE__) + 'db/migrate'
    ActiveRecord::Migration.verbose = ENV["VERBOSE"] ? ENV["VERBOSE"] == "true" : true
    ActiveRecord::Migrator.migrate(ActiveRecord::Migrator.migrations_paths, ENV["VERSION"] ? ENV["VERSION"].to_i : nil) do |migration|
      ENV["SCOPE"].blank? || (ENV["SCOPE"] == migration.scope)
    end
  end

  desc "Populate the database with dummy data by running db/seeds.rb"
  task :seed do
    require APP_ROOT.join('db', 'seeds.rb')
  end

  desc "Returns the current schema version number"
  task :version do
    puts "Current version: #{ActiveRecord::Migrator.current_version}"
  end

  namespace :test do
    desc "Migrate test database"
    task :prepare do
      system "rake db:migrate RACK_ENV=test"
    end
  end
end

desc 'Start IRB with application environment loaded'
task "console" do
  exec "irb -r./config/environment"
end

desc "Run the specs"
RSpec::Core::RakeTask.new(:spec)

task :default  => :spec
