---
title:  "How to build a blog by Jekyll and GitHub Actions"
date:   2022-04-24
categories: [Tutorial]
tags: [Jekyll]
render_with_liquid: false
---

This tutorial will teach you how to build a website based on Jekyll and GitHub Actions step by step in *Windows*

## Install prerequisites

Install jekyll and bundler

1. Download and install a Ruby+Devkit version from [RubyInstaller Downloads](https://rubyinstaller.org/downloads/). Use default options for installation
2. Run the `ridk install` step on the last stage of the installation wizard (actually it's automated, just click all the yes button)
3. Open a new command prompt window from the start menu (`windows + r`, followed by `cmd`). Install Jekyll and Bundler using `gem install jekyll bundler`.
4. Check if Jekyll has been installed properly: `jekyll -v`

Install Git

Just follow the instructions of [Git](https://git-scm.com/)

## Create a repository in GitHub

1. Create a new repository in GitHub, its name should be `USERNAME.github.io` (for me, its sunjh22.github.io)

Detailed procedures about this step can refer to the first few steps of [Setting up a github pages site with jekyll](https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/)

## Create a jekyll site in local computer

1. Open Git Bash
2. Make a directory (such as ./MyBlog) and enter that directory
3. Initialize a local Git repository (the name should be the same as previously created GitHub repository) (`git init sunjh22.github.io`), enter that repository
4. Create a new jekyll site using `jekyll new --skip-bundle`, some basic files are produced, including `Gemfile`, `config.yml` etc.
5. The default theme is *minima*, you can change the theme (for example, I used [jekyll-theme-chirpy](http://jekyllthemes.org/themes/jekyll-theme-chirpy/)). Open `Gemfile`, change gem `"minima", "~> 2.5"` to `gem "jekyll-theme-chirpy", "~> 3.3", ">= 3.3.0"`, and change `theme: minima`  to `theme: jekyll-theme-chirpy` in `config.yml` (Details can refer to [this](https://jekyllrb.com/docs/themes/))
6. From the command line, run `bundle install`
7. Test site locally, if it works well, you should see a website in `http://127.0.0.1:4000/`

	```bash
	$ bundle add webrick
	$ bundle exec jekyll serve
	```
	{: .nolineno}

## Deploy your website to GitHub

1. Add a GitHub workflow file (e.g. `.github/workflows/build-jekyll.yml)` with the content as [here](https://github.com/jeffreytse/jekyll-deploy-action), remember change `master` branch to your source code branch if it do not match (such as `main`). (if your default branch is master, you can change that to main by `git checkout -b main`)

2. Add your repository on GitHub.com as a remote, replacing USER with the account that owns the repository and REPOSITORY with the name of the repository (e.g. mine is `git remote add origin https://github.com/sunjh22/sunjh22.github.io.git`)
	```bash
	$ git remote add origin https://github.com/USER/REPOSITORY
	```
	{: .nolineno}
		
3. Add and commit your work
	```bash
	$ git add .
	$ git commit -m 'Initial GitHub pages site with Jekyll'
	```
	{: .nolineno}

4. Push the repository to GitHub
	```bash
	$ git push -u origin main
	```
	{: .nolineno}
		
5. On GitHub, the `Build and Deploy to Github Pages` workflow will build the site, after it was finished, a `gh-pages` branch will be created, navigate Settings, click Pages, then select `gh-pages/root` as publishing source. After a while, you can access your blog! (Mine is https://sunjh22.github.io/)
6. Complete the content under Chirpy theme. Download Chirpy [source code](https://github.com/cotes2020/jekyll-theme-chirpy/archive/master.zip), unzip and copy directories `_data`, `_includes`, `_javascript`, `_layouts`, `_plugins`, `_sass`, `_tabs` and `_assets` to your website directory.
7. Just revise the content and start writing your own post!
