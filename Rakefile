require 'rake/clean'
require 'pathname'

BUILD_DIR   = Pathname('build').freeze
DIST_DIR    = Pathname('dist').freeze
SRC_DIR     = Pathname('src').freeze
SRCS        = Pathname.glob("#{SRC_DIR}/resume_*.latex").freeze
MAIN        = (SRC_DIR + 'main.latex').freeze
PDF_FILES   = SRCS.map { |e| BUILD_DIR + e.basename.sub_ext('.pdf') }.freeze
TEX         = 'xelatex'.freeze
VIEWER      = 'xpdf -fullscreen'.freeze

LATEX_2_PDF = proc do |t|
  t.sub(BUILD_DIR.to_s, SRC_DIR.to_s).sub('.pdf', '.latex')
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
task install: :build do
  cp Pathname.glob("#{BUILD_DIR}/*.pdf"), DIST_DIR
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
