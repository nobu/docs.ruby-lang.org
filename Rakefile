require 'rdoc/task'

versions = {
  "master" => "master",
  "2.7.0" => "ruby_2_7",
  "2.6.0" => "ruby_2_6",
  "2.5.0" => "ruby_2_5",
}

versions.each do |version, branch_name|
  source_dir = "sources/#{version}"

  directory source_dir do
    sh "git clone --depth=1 --branch=#{branch_name} git://github.com/ruby/ruby.git #{source_dir}"
  end

  desc "Checks out source for #{version}"
  task "source:#{version}" => source_dir

  desc "Updates source for #{version}"
  task "update:#{version}" => source_dir do
    Dir.chdir source_dir do
      sh "git fetch origin"
      sh "git reset origin/#{branch_name} --hard"
    end
  end

  desc "Compile source for #{version}"
  task "compile:#{version}" => source_dir do
    Dir.chdir source_dir do
      sh "make clean" if File.exists?("Makefile")
      sh "autoconf && ./configure && make"
    end
  end

  namespace :rdoc do
    lang_version = File.join("en", version)
    task lang_version do
      sh(
        "rdoc",
        "--title", "Documentation for Ruby #{version}",
        "--main", "README.md",
        "--output", "#{Dir.pwd}/#{version}",
        "-U", "--all", "--encoding=UTF-8",
        ".",
        chdir: source_dir
      )
    end
  end
  task "rdoc:#{version}" => ["update:#{version}", "compile:#{version}"]
end

desc "Checks out sources for all versions"
task "source" => versions.keys.map { |version| "source:#{version}" }

desc "Updates sources for all versions"
task "update" => versions.keys.map { |version| "update:#{version}" }

desc "Build RDoc HTML files for all versions"
task "rdoc" => versions.keys.map { |version| "rdoc:#{version}" }
