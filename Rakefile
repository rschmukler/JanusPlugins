require 'fileutils'

ROOT_PATH = '~/.janus'

desc "link VIM configuration files."
task :link_vim_conf_files do
  %w[ vimrc.before vimrc.after gvimrc.before gvimrc.after ].each do |file|
    dest = File.expand_path("~/.#{file}")
    if File.exist?(dest)
      puts "\tReplacing Old File: .#{file} and moving it to .#{file}.old"
      FileUtils.mv(dest, File.expand_path("~/.#{file}.old"))
    end
    File.symlink(File.expand_path("#{ROOT_PATH}/config/#{file}"), dest)
  end
end

desc "Move existing vim files to janus config dir"
task :move_existing_vim_files do
  %w[ vimrc.before vimrc.after gvimrc.before gvimrc.after ].each do |file|
    src = File.expand_path("~/.#{file}")
    dst = File.expand_path("#{ROOT_PATH}/config/#{file}")
    if File.exist?(src)
      puts "\tMoving Existing File: .#{file} to #{ROOT_PATH}/config/#{file}"
      FileUtils.mv(src, dst)
    else
      puts "Creating empty config file: #{ROOT_PATH}/config/#{file}"
      FileUtils.touch(dst)
    end
  end
end

desc "Unlinks the config files and moves them back"
task :uninstall do
  %w[ vimrc.before vimrc.after gvimrc.before gvimrc.after ].each do |file|
    dst = File.expand_path("~/.#{file}")
    src = File.expand_path("#{ROOT_PATH}/config/#{file}")
    if File.exist?(dst)
      FileUtils.rm(dst)
    end
    FileUtils.cp(src, dst)
  end

end

desc "Create necessary folders"
task :folders do
  folders = %w[ config ]
  folders.each do |f|
    Dir.mkdir(File.expand_path("#{ROOT_PATH}/#{f}"))
  end
end

desc "Setup Git Repo"
task :git_setup do
  unless File.exist?('.git')
    puts "Creating initial git repository"
    `git init > /dev/null`
    `touch README`
    `git add .`
    `git commit -m 'Newly created JanusConfigurationSyncer Install'`
    repo_path = STDIN::gets
    `git remote add origin #{repo_path}`
    `git push -u origin master`
  end
end


task :update do
  puts "Pulling the latest configuration"
  `git pull > /dev/null`
  puts "Updating Submodules"
  sh "git submodule foreach git pull origin master"
end

task :install_copy do
  Rake::Task[:git_setup].invoke
  puts "Installing Vimrc files from config directory"
  Rake::Task[:link_vim_conf_files].invoke
end

task :install_create, :repo_path do
  Rake::Task[:folders].invoke
  Rake::Task[:move_existing_vim_files].invoke
  puts "Creating necessary folders"
  Rake::Task[:folders].invoke
  Rake::Task[:install_copy].invoke
end

desc "Update Janus Config"
task :default do
  sh "rake update"
end
