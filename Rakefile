require 'rake/clean'

BUILD_DIR   = 'build'.freeze
DIST_DIR    = 'dist'.freeze
SRC_DIR     = 'src'.freeze
SRCS        = FileList["#{SRC_DIR}/resume_*.latex"]
MAIN        = "#{SRC_DIR}/main.latex".freeze
PDF_FILES   = SRCS.pathmap "#{BUILD_DIR}/%n.pdf"
TEX         = 'xelatex'.freeze
VIEWER      = 'xpdf -fullscreen'.freeze

LATEX_2_PDF = proc do |t|
  t.pathmap("%{^#{BUILD_DIR},#{SRC_DIR}}X.latex")
end

CLEAN.include "#{BUILD_DIR}/*"


rule '.pdf' => LATEX_2_PDF do |t|
  2.times { sh "#{TEX} -output-directory #{BUILD_DIR} #{t.source}" }
end

PDF_FILES.each { |e| file e => MAIN }


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
