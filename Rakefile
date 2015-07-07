require "html/proofer"

task :default => [:build]

task :build do
	system("jekyll build")
end

task :test do
	HTML::Proofer.new("./_site", {
		:href_ignore => [
			"#"
		],
		:disable_external => true
	}).run
end

task :cibuild => [:build, :test] do
	
end

task :deploy => :build do

end