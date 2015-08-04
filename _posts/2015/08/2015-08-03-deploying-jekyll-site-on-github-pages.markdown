---
layout: post
title:  "Deploying Jekyll site to Github Pages with Travis CI"
date:   2015-08-03 16:30:00
author: "Taylor"
permalink: "/deploying-jekyll-to-gh-pages"
tags:
- Jekyll
- Travis CI
- continuous integration
- tests
---

### The problem

I want to set up continuous integration for this blog so that I can write posts, test that all links are valid, and then publish the changes.

### The solution

I borrowed most of this solution from [Ellis Michael's blog post](http://ellismichael.com/technical/2015/06/12/using-travis-ci-with-github-pages/) about using Travis CI with Github pages. Combined with [Jekyll's CI documentation](http://jekyllrb.com/docs/continuous-integration/), we were able to come up with a solution that allows us to build, test, and publish on each commit to master.

At a high level, Travis CI will check out the "master" branch, clean the site output, build the site using Jekyll, run tests to verify that all links are connected, and then push to the "gh-pages" branch.

The most difficult part about figuring out this problem was to combine all the .travis.yml snippets to create one script that worked. So, here are the important contents of my configuration files:

<figure>
	<figcaption>.travis.yml</figcaption>

	{% highlight yaml linenos %}
language: ruby
rvm:
- 2.1

install: bundle install
before_script:
- openssl aes-256-cbc -K ...... # replace with the line that travis encrypt-file printed out
- chmod go-rwx .travisdeploykey
- eval `ssh-agent -s`
- ssh-add .travisdeploykey
- git config user.name "Travis-CI"
- git config user.email "noreply@travis-ci.org"
- COMMIT_MESSAGE="Publishing site on `date "+%Y-%m-%d %H:%M:%S"` from `git log -n 1 --format='commit %h - %s'`"
script: 
- rake cibuild
- set -e
- rake build
- git checkout -b gh-pages
- echo "outofdeadlock.io" > _site/CNAME
- git add -f _site/
- 'git commit -m "${COMMIT_MESSAGE}"'
- git filter-branch -f --subdirectory-filter _site/
- git push -f git@github.com:tbrushwyler/outofdeadlock.git gh-pages:gh-pages

branches:
  only:
    - master

env:
- NOKOGIRI_USE_SYSTEM_LIBRARIES=true
	{% endhighlight %}
</figure>

<figure>
	<figcaption>Rakefile</figcaption>

	{% highlight ruby linenos %}

require "html/proofer"

task :default => [:build]

task :clean do
	sh 'rm -rf ./_site'
end

task :build do
	system("jekyll build")
end

task :rebuild => [:clean, :build] do

end

task :test do
	HTML::Proofer.new("./_site", {
		:href_ignore => [
			"#"
		],
		:disable_external => true
	}).run
end

task :cibuild => [:rebuild, :test] do
	
end
	{% endhighlight %}
</figure>