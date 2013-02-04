require 'rake'
require 'rspec/core/rake_task'
require 'kitchen'


@instances = []
@config = Kitchen::Config.new('.kitchen.yml')
@names = %w{ default-app-server default-load-balancer }
@names.each {|name| @instances << @config.instances.get(name) }

# serial execution cuz virtualbox/vagrant can't parallelize this part
task :destroy do
  @names.each {|name| @config.instances.get(name).destroy }
end

task :create do
  # this happens serially because virualbox/vagrant can't handle
  # parallel vm creation
  @instances.each {|i| i.create }
end

task :converge => :create do
  futures = []
  # this happens in parallel
  @instances.each {|i| i.converge }
end

RSpec::Core::RakeTask.new(:spec)  do |t|
  t.pattern = "spec/*_spec.rb"
  t.rspec_opts = '--color'
end

task :test => [:create, :converge, :spec] 
