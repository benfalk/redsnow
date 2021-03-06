begin
  require 'bundler/gem_tasks'
rescue LoadError
  puts "Cannot load bundler/gem_tasks"
end

begin
  require 'rake/testtask'
rescue LoadError
  puts "Cannot load rake/testtask"
end

begin
  require 'ffi'
rescue LoadError
  puts "Cannot load ffi"
end

begin
  require 'yard'
rescue LoadError
  puts "Cannot load yard"
end

task :default => :compile

desc "Compile extension"
task :compile do
  prefix = "lib.target/"
  if FFI::Platform.mac?
    prefix = ""
  end
  # Path to compiled snowcrash library
  path = File.expand_path("ext/snowcrash/build/out/Release/#{prefix}libsnowcrash.#{FFI::Platform::LIBSUFFIX}", File.dirname(__FILE__))
  puts "Path to library #{path}"
  if !File.exists?(path) || ENV['RECOMPILE']
    unless File.directory?(File.expand_path("ext/snowcrash/src"))
      puts "Initializing submodules (if required)..."
      `git submodule update --init --recursive 2>/dev/null`
    end
    puts "Compiling extension..."
    `cd #{File.expand_path("ext/snowcrash/")} && ./configure --shared && make`
    status = $?.exitstatus
    if status == 0
      puts "Compiling done."
    else
      puts "Compiling error, exiting."
      next # If i'm using exit, abort I have some errors in rake install but gem can be installed
    end
  else
    puts "Extension already compiled. To recompile set env variable RECOMPILE=true."
  end
end

desc "Run tests"
Rake::TestTask.new(:test) do |test|
  Rake::Task["compile"].invoke

  test.libs << 'lib' << 'test'
  test.test_files = FileList['test/*_test.rb']
  test.verbose = true
end

# ----- Documentation tasks ---------------------------------------------------

YARD::Rake::YardocTask.new(:doc) do |t|
  t.options = %w| --embed-mixins --markup=markdown |
end
