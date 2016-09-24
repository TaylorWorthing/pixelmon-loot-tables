require 'rake/clean'
require 'rake/packagetask'
require 'net/http'
require 'fileutils'
require 'pathname'
require 'rbconfig'
require 'yaml'
require 'json'

WORLD_NAME = ENV['WORLD_NAME'] || 'world'

BUILD_DIR = 'tmp'
DATA_DIR = File.join('data', 'loot_tables', 'minecraft', 'chests')

MINECRAFT_URI = 'https://s3.amazonaws.com/Minecraft.Download/versions/1.10.2/minecraft_server.1.10.2.jar'
MINECRAFT_JAR = File.join(BUILD_DIR, File.basename(MINECRAFT_URI))
MINECRAFT_CHESTS = File.join(BUILD_DIR, 'chests')

directory MINECRAFT_CHESTS
directory DATA_DIR

CLEAN.include(BUILD_DIR)
CLOBBER.include(Pathname(DATA_DIR).each_filename.to_a.first)

# Default loot tables need to be extracted from a downloaded server jar.
desc 'Prepare files needed in order to build'
task :prepare => MINECRAFT_CHESTS do
  unless File.file?(MINECRAFT_JAR)
    File.open(MINECRAFT_JAR, 'wb') do |file|
      file.write(Net::HTTP.get(URI(MINECRAFT_URI))) 
    end
  end

  # Extract the loot tables from the server jar.
  # Using rubyzip is needlessly over complicated for this task. The default
  # packaging tasks already use binaries on the system anyway.
  system('unzip', '-jnq', MINECRAFT_JAR,
         File.join('assets', 'minecraft', 'loot_tables', 'chests', '*'),
         '-d', MINECRAFT_CHESTS)
end

desc 'Build loot tables'
task :build => [:prepare, DATA_DIR] do
  config = YAML.load_file('config.yml')
  loot_tables = config['loot_tables']
  loot_tables.each do |loot_table, items|
    next if items.nil? || items.empty?

    source_table = File.join(BUILD_DIR, 'chests', loot_table + '.json')
    new_table = JSON.parse(File.read(source_table))

    # Pixelmon items are added as a new pool to each table.
    new_pool = {}
    new_pool['rolls'] = config['rolls']
    new_pool['entries'] = []

    items.each do |item|
      new_item = JSON.parse(File.read(File.join('lib', item + '.json')))
      new_pool['entries'].push(new_item)
    end

    new_table['pools'].push(new_pool)

    File.open(File.join(DATA_DIR, loot_table + '.json'), 'w') do |file|
      file.write(JSON.pretty_generate(new_table))
    end
  end
end

# Packages are blank world folders with the loot tables added. Really useful for
# deploying to a server.
Rake::PackageTask.new(WORLD_NAME, :noversion) do |package|
  package.need_zip = true
  files = Dir.glob(File.join(DATA_DIR, '*.json'))
  package.package_files.include(files)
end

desc "Install loot tables to a world folder"
task :install do
  # Different systems have different install locations.
  # TODO Windows support.
  os = RbConfig::CONFIG['host_os']
  case
  when os.downcase.include?('linux')
    world_dir = File.join(Dir.home, '.minecraft', 'saves', WORLD_NAME)
  when os.downcase.include?('darwin')
    world_dir = File.join(Dir.home, 'Library', 'Application Support',
                          'minecraft', 'saves', WORLD_NAME)
  else
    puts 'Install task not supported on your platform.'
    exit
  end

  install_dir = File.join(world_dir, DATA_DIR)
  mkdir_p install_dir
  cp_r File.join(DATA_DIR, '.'), install_dir, :remove_destination => true
end

task :default => [:build]
