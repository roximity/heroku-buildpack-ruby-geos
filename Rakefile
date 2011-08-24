require "fileutils"

def plugin_base
  File.expand_path("../vendor/plugins", __FILE__)
end

def vendor_plugin(git_url)
  name = File.basename(git_url, File.extname(git_url))
  Dir.chdir(plugin_base) do
    FileUtils.rm_rf(name)
    sh "git clone #{git_url} #{name}"
    FileUtils.rm_rf("#{name}/.git")
  end
end

def gem_base
  File.expand_path("../vendor/gems", __FILE__)
end

def in_gem_env(&block)
  old_gem_home = ENV['GEM_HOME']
  old_gem_path = ENV['GEM_PATH']
  ENV['GEM_HOME'] = ENV['GEM_PATH'] = gem_base.to_s

  yield

  ENV['GEM_HOME'] = old_gem_home
  ENV['GEM_PATH'] = old_gem_path
end

def install_gem(gem, version)
  in_gem_env do
    cmd = "gem install #{gem} --version #{version} --no-ri --no-rdoc"
    puts(cmd)
    system(cmd)
  end
end

def uninstall_gem(gem)
  in_gem_env do
    cmd = "gem uninstall #{gem}"
    puts(cmd)
    system(cmd)
  end
end

def gem_detected?(gem)
  output = ''
  in_gem_env do
    output = `gem list #{gem}`
  end

  output.split("\n").each do |line|
    md = /^([\S]+)/.match(line)
    return true if md && md[1] == gem
  end

  false
end

desc "update plugins"
task "plugins:update" do
  vendor_plugin "http://github.com/ddollar/rails_log_stdout.git"
  vendor_plugin "http://github.com/pedro/rails3_serve_static_assets.git"
end

desc "install vendored gem"
task "gem:install", :gem, :version do |t, args|
  gem     = args[:gem]
  version = args[:version]

  uninstall_gem(gem) if gem_detected?(gem)
  install_gem(gem, version)
end

desc "update libyaml"
task "libyaml:update" do
  sh "curl -o yaml.tgz http://libyaml-compiler.herokuapp.com/yaml.tgz"
  sh "git rm -rf vendor/yaml-*" if Dir['vendor/yaml-*'].any?
  sh "cd vendor && tar -zxvf ../yaml.tgz"
  sh "git add vendor/yaml-*"
  sh "rm yaml.tgz" if File.exists?("yaml.tgz")
end

desc "install rubygems"
task "rubygems:install", :version do |t, args|
  rubygems_dir = "rubygems-#{args[:version]}"
  sh "curl -o rubygems.tgz http://production.cf.rubygems.org/rubygems/#{rubygems_dir}.tgz"
  sh "git rm vendor/rubygems-*" if Dir['vendor/rubygems-*'].any?
  sh "tar -zvxf rubygems.tgz"
  sh "cd #{rubygems_dir} && ruby setup.rb --prefix=../vendor/#{rubygems_dir}"
  sh "git add vendor/rubygems-*"
  sh "rm rubygems.tgz" if File.exists?("rubygems.tgz")
  sh "rm -rf #{rubygems_dir}" if File.exists?(rubygems_dir)
end
