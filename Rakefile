require 'colorize'
require 'fileutils'
require 'rake'
require 'sass'
require 'sass/plugin'

# Ensure dir exist
def ensure_dir
  Dir.mkdir('dist') unless Dir.exist?('dist')
end

# Replace placeholder
def replace_placeholder(content)
  content.gsub!(/\bdash_color\b/, '/*[[dash_color]]*/')
  content.gsub!(/\bdash_color_focus\b/, '/*[[dash_color_focus]]*/')
end

# Sass options
SASS_OPTIONS = {
  style: :expanded,
  syntax: :scss,
  sourcemap: :none
}.freeze

# Default task
task default: :watch

# Clean task
task :clean do
  FileUtils.rm_rf('dist')
end

# Build task
task build: %i[clean] do
  ensure_dir

  src_files = Rake::FileList['src/*.scss']
  src_files.each do |file|
    src_content = File.read(file)
    engine = Sass::Engine.new(
      src_content,
      SASS_OPTIONS
    )
    dist_content = replace_placeholder(engine.render)
    dist_filename = File.basename(file).ext('css')
    File.write("dist/#{dist_filename}", dist_content)
  end

  puts 'Build succeed.'.colorize(:green)
end

# Watch task
task watch: %i[build] do
  ensure_dir

  compiler = Sass::Plugin::Compiler.new(
    SASS_OPTIONS.merge(
      template_location: 'src',
      css_location: 'dist'
    )
  )
  puts 'Watching source files...'.colorize(:yellow)

  compiler.watch do |modified|
    modified.each do |file|
      filename = File.basename(file)

      # Replacing placeholder
      css_filename = filename.ext('css')
      css_filepath = "dist/#{css_filename}"
      File.write(css_filepath, replace_placeholder(File.read(css_filepath)), mode: 'w')

      puts "#{File.basename(file).colorize(:cyan)} changed."
    end
  end
end
