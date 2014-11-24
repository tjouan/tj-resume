require 'rake/clean'
require 'tmpdir'

BUILD_DIR   = 'build'.freeze
DIST_DIR    = 'dist'.freeze
SRC_DIR     = 'src'.freeze
SRCS        = FileList["#{SRC_DIR}/resume_*.latex"]
MAIN        = "#{SRC_DIR}/main.latex".freeze
PDF_FILES   = SRCS.pathmap "#{BUILD_DIR}/%n.pdf"
TEX         = 'xelatex'.freeze
VIEWER      = 'xpdf -fullscreen'.freeze

GITHUB_USERNAME = 'tjouan'.freeze
GITHUB_REPONAME = 'tj-resume'.freeze

LATEX_2_PDF = proc do |t|
  t.pathmap("%{^#{BUILD_DIR},#{SRC_DIR}}X.latex")
end

CLEAN.include "#{BUILD_DIR}/*"


rule '.pdf' => LATEX_2_PDF do |t|
  2.times { sh "#{TEX} -output-directory #{BUILD_DIR} #{t.source}" }
end

PDF_FILES.each { |e| file e => MAIN }

directory DIST_DIR


task default: :build
task all: %i[build install]

desc 'Build PDF documents from LaTeX sources'
multitask build: PDF_FILES

desc "Install built PDF in `#{DIST_DIR}' directory"
task install: [:build, DIST_DIR] do
  cp PDF_FILES, DIST_DIR
end

desc 'View PDF output with xpdf'
task :view, [:substring] do |t, args|
  file = PDF_FILES.find { |e| e.to_s.include? args.substring } if args.substring
  file ||= PDF_FILES.first
  Rake::Task[:build].prerequisites.clear
  Rake::Task[:build].prerequisites.replace [file]
  Rake::Task[:build].invoke
  sh "#{VIEWER} #{file} > /dev/null 2>&1"
end

namespace :publish do
  desc 'Publish to GitHub pages'
  task github: :build do
    Dir.mktmpdir('resume_gh-pages') do |dir|
      sh "git clone --branch gh-pages . #{dir}"
      cp PDF_FILES, "#{dir}/#{DIST_DIR}"
      write_github_pages_index "#{dir}/index.md", PDF_FILES
      sh "git -C #{dir} add ."
      sh "git -C #{dir} commit -m 'Publish generated PDF to GitHub pages'"
      sh "git -C #{dir} remote add github $(git config --get remote.github.url)"
      sh "git -C #{dir} push -f github gh-pages"
    end
  end
end


def write_github_pages_index(path, files)
  File.open(path, 'w') do |f|
    files.each do |e|
      path = e.pathmap "#{DIST_DIR}/%f"
      f.puts '[%s](https://%s.github.io/%s/%s)' % [
        path, GITHUB_USERNAME, GITHUB_REPONAME, path
      ]
    end
  end
end
