require 'pathname'

BUILD_DIR   = Pathname('build').freeze
OUTPUT_DIR  = Pathname('output').freeze
SRC_DIR     = Pathname('src').freeze
SRC_FILE    = Pathname('main.latex').freeze
PDF_FILE    = SRC_FILE.sub_ext('.pdf').freeze
TEX         = 'xelatex'.freeze
VIEWER      = 'xpdf -fullscreen'.freeze

LATEX_2_PDF = proc do |t|
  t.sub(BUILD_DIR.to_s, SRC_DIR.to_s).sub('.pdf', '.latex')
end


rule '.pdf' => LATEX_2_PDF do |t|
  2.times { sh "#{TEX} -output-directory #{BUILD_DIR} #{t.source}" }
end


task default: :build
task all: %i[build install]

desc 'Build PDF output from LaTeX sources'
task build: BUILD_DIR + PDF_FILE

desc 'Install built files in `output\' directory'
task install: :build do
  cp Pathname.glob("#{BUILD_DIR}/*.pdf"), OUTPUT_DIR
end

desc 'Clean temporary build files'
task :clean do
  BUILD_DIR.each_child &:unlink
end

desc 'View PDF output with xpdf'
task view: :install do
  sh "#{VIEWER} #{OUTPUT_DIR}/#{PDF_FILE} > /dev/null 2>&1"
end
