require 'rubygems'
require 'rake'

begin
  require 'jeweler'
  Jeweler::Tasks.new do |gem|
    gem.name = 'blather'
    gem.summary = 'An evented XMPP library written on EventMachine and libxml-ruby'

    gem.email = 'sprsquish@gmail.com'
    gem.homepage = 'http://github.com/sprsquish/blather'
    gem.authors = ['Jeff Smick']

    gem.rubyforge_project = 'squishtech'

    gem.extensions = %w[ext/extconf.rb]

    gem.add_dependency 'eventmachine', '>= 0.12.6'
    gem.add_dependency 'libxml-ruby', '>= 1.1.3'

    # gem is a Gem::Specification... see http://www.rubygems.org/read/chapter/20 for additional settings
  end
rescue LoadError
  puts "Jeweler (or a dependency) not available. Install it with: sudo gem install jeweler"
end

require 'rake/testtask'
Rake::TestTask.new(:test) do |test|
  test.libs << 'lib' << 'spec'
  test.pattern = 'spec/**/*_spec.rb'
  test.verbose = true
end

begin
  require 'rcov/rcovtask'
  Rcov::RcovTask.new do |test|
    test.libs << 'spec'
    test.pattern = 'spec/**/*_spec.rb'
    test.rcov_opts += ['--exclude \/Library\/Ruby\/Gems,spec\/', '--xrefs']
    test.verbose = true
  end
rescue LoadError
  task :rcov do
    abort "RCov is not available. In order to run rcov, you must: sudo gem install spicycode-rcov"
  end
end


task :default => :test

begin
  require 'hanna/rdoctask'

  Rake::RDocTask.new do |rdoc|
    if File.exist?('VERSION.yml')
      config = YAML.load(File.read('VERSION.yml'))
      version = "#{config[:major]}.#{config[:minor]}.#{config[:patch]}"
    else
      version = ""
    end

    rdoc.rdoc_dir = 'rdoc'
    rdoc.title = "blather #{version}"
    rdoc.rdoc_files.include('README*')
    rdoc.rdoc_files.include('lib/**/*.rb')
    rdoc.options += %w[-S -T hanna --main README.rdoc --exclude autotest --exclude vendor]
  end
rescue LoadError
  task :rdoc do
    abort "Hanna is not available. In order to use the Hanna, you must: sudo gem install mislav-hanna"
  end
end

desc 'Build extensions'
task :build => :extensions
task :extension => :build

ext = Config::CONFIG["DLEXT"]
task :extensions => ["ext/push_parser.#{ext}"]
file "ext/push_parser.#{ext}" =>
  ["Makefile"] + FileList["ext/*.{c,h}"].to_a do |t|
  Dir.chdir("ext") { sh "make" }
end

namespace :extensions do
  desc 'Clean extensions'
  task :clean do
    Dir.chdir("ext") do
      sh "rm -f Makefile"
      sh "rm -f *.{o,so,bundle,log}"
    end
  end
end

file "Makefile" => %w[ext/extconf.rb] do
  command = ["ruby"] + $:.map{|dir| "-I#{File.expand_path dir}"} + ["extconf.rb"]
  Dir.chdir("ext") { sh(*command) }
end
