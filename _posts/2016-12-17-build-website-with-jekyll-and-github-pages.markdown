---
title: "Build Your Personal Websites with Jekyll & GitHub Pages"
layout: post
date: 2016-12-17 02:04
image: /assets/images/posts/jekyll_and_github.png
headerImage: true
tag:
- Website
- GitHub Pages
- Jekyll
- Blog
blog: true
draft: false
star: true
author: johndoe
description: Build Your Personal Websites For Free with Jekyll & GitHub Pages
---

## Introduction

Having a personal website is a trend these days. It also becomes an important tool if you belong to a profession that requires a showcase for your portfolio items e.g Designer, Developer, Artist etc. Even if you are not one of them, you might still want to spread your ideas through a personal blog. However, most of us never bother trying to get one. Mainly due to one of the following reasons:

* **Hosting cost a few bucks** and paying for a *'not-so-necessary'* thing might feel like an overkill to you.
* Or may be, you **don't know how to build a website** (and don't want to pay someone for it). 

Well, seems like those are the days of past now. In this post, we'll learn how to setup a fully featured website without any cost using [GitHub Pages](https://pages.github.com/){:target="_blank"} and [Jekyll](https://jekyllrb.com){:target="_blank"}.

---

* [Getting Started with GitHub Pages](#getting-started-with-github-pages)
    * [What is GitHub Pages](#what-is-github-pages)
    * [Setting up GitHub repo](#setting-up-github-repo)
* [Setting Up Your Blog/Website](#setting-up-your-blogwebsite)
    * [What is Jekyll](#what-is-jekyll)
    * [Give Your Site Personal Touch With Themes](#give-your-site-personal-touch-with-themes)
    * [Customizing & Testing Your Website](#customizing--testing-your-website)
* [(Optional) Configuring Jekyll For Local Testing](#optional-configuring-jekyll-for-local-testing)
* [(Optional) Redirect Your Custom Domain To Your Jekyll Site](#optional-redirect-your-custom-domain-to-your-jekyll-site)

---

## Getting Started with GitHub Pages

[GitHub](https://github.com/){:target="_blank"} is a web-based Git repository hosting service. It offers all of the [distributed version control](https://en.wikipedia.org/wiki/Distributed_version_control){:target="_blank"} and [source code management (SCM)](https://en.wikipedia.org/wiki/Version_control){:target="_blank"} functionality of [Git](https://en.wikipedia.org/wiki/Git){:target="_blank"} as well as adding its own features. After it's launch in April 2008, the company continued to make improvements to the existing service as well as introducing new features. [GitHub Pages](https://pages.github.com/){:target="_blank"} is one of those additions.

### What is GitHub Pages

[GitHub Pages](https://pages.github.com/){:target="_blank"} is designed to host your personal, organization, or project pages directly from a GitHub repository. In order to get started with GitHub Pages, you'll need to set up a GitHub repository. This is where your content will reside and be served from. 

### Setting up a GitHub Repository

Let's start by creating a new GitHub repository. If you already have a GitHub account, skip to *<span class="evidence">step 4</span>*:

* First, create a free [GitHub account](https://github.com/join){:target="_blank"}.
* Make sure you pick a proper username (even better if you could find your own name available).
* Download and install [GitHub desktop](https://desktop.github.com/){:target="_blank"}, especially if you are not comfortable working with [CLI](https://en.wikipedia.org/wiki/Command-line_interface){:target="_blank"}.
* Now that you have your GitHub account set up. Go to your GitHub account and create a *<span class="evidence">new repository</span>* and name it in this format: *<span class="evidence">yourusername.github.io</span>*.

{:refdef: style="text-align: center;"}
![New Repository](/assets/images/posts/new_repo_name.png)
{: refdef}

* Next, create a copy of your empty repository in your local GitHub repositories folder. To do this:
    * If you are using GitHub desktop, click the *<span class="evidence">Set up in Desktop</span>* button on your repository page.
    * or if you are using CLI, navigate to the folder where you want to store your project, and clone the new repository: 

{% highlight sh %}
git clone https://github.com/username/yourusername.github.io
{% endhighlight %}

* Go to the folder where you have stored your project and create a new file *<span class="evidence">index.html</span>*.
* Open the file in text editor of your choice and paste the following `HTML` in it, this is just to indicate that our site is working expectedly.

{% highlight html %}
<html lang="en">
    <head>
        <title>My Website</title>
    </head>
    <body>
        <h1>My Website Hosted by GitHub Pages</h1>
    </body>
</html>
{% endhighlight %}

* Finally, commit and push changes by:
    * If you are using GitHub Desktop, first write a message in summary field and press *<span class="evidence">Commit To Master</span>* and then pressing the *<span class="evidence">Sync</span>* button.
    * Or if you are on CLI, use the following commands

{% highlight sh %}
git add --all
git commit -m "Initial commit"
git push -u origin master
{% endhighlight %}

* Once the changes have been pushed, type in the url *<span class="evidence">yourusername.github.io</span>* in your browser and you should see your page. 

Congratulations! You've successfully created your website, now is the time give it a pleasing look and feel.

---

## Setting Up Your Blog/Website

Now that your site basic website is up and running, it's time to make it look like an *actual* website with the help of [Jekyll](https://jekyllrb.com){:target="_blank"}. 

### What is Jekyll

At this point you might be wondering *`'so what is Jekyll anyway?'`*. It is an awesome blog-aware website generator that is designed for building minimal, static sites to be hosted on GitHub Pages. I recently migrated my personal site from Wordpress to Jekyll and got to admit that I'm loving it.

> So how does Jekyll differs from Wordpress?

Firstly, if you have used [Wordpress](www.wordpress.com/){:target="_blank"}, you can tell that it has it's flaws, plus it's sort of an overkill if you want to put only simple static content. Also, it has been a hot target for hackers in recent years.

>Oh! and did I tell you that it's waaaay faster than Wordpress? 

Wordpress suffers badly as the load grows on the site. However, Jekyll being static-website-centric, completely ends that problem as static files put very little load on the server.

Another important point for me was [Markdown](https://en.wikipedia.org/wiki/Markdown){:target="_blank"} support, if you are like me and hate writing `HTML` then Jekyll is your friend. Jekyll supports Markdown out of the box, which is a very simple and intuitive text-to-HTML conversion tool. It allows you to write using an easy-to-read, easy-to-write plain text format, then converts it to structurally valid XHTML (or HTML).

### Give Your Site Personal Touch With Themes

Just like Wordpress, Jekyll supports customizable themes. You can find plenty of free themes at [JekyllThemes.org](http://jekyllthemes.org/){:target="_blank"}, [Themes.JekyllRC.org](http://themes.jekyllrc.org/){:target="_blank"}, [JekyllThemes.io](https://jekyllthemes.io/){:target="_blank"} or at this GitHub [repository](https://github.com/jekyll/jekyll/wiki/Themes){:target="_blank"}. Our next step will be to apply a theme to our website and then customize it. So go to these sites, play with demo and pick one that you like.

Once you have found the right theme for your website, download the zip file and extract all of its content to your local repository folder.

> If the archive contains a top level folder and has all the files in it. Then exclude the folder and copy all contents from it to your repository containing folder.

Finally, once again commit and push your changes and open your browser to see your newly furnished site in action.

### Customizing & Testing Your Website

Jekyll themes come with a *<span class="evidence">_config.yml</span>* file which contains all the meta data about the website such as title, description, favicon, author info, pages etc. Edit this file with your data e.g.

{% highlight yml %}
# Website settings
title: "John Doe"
description: "John's blog powered by Jekyll and GitHub Pages."
keywords: "John,blog,Jekyll,github,gh-pages"
baseurl: "/"
url: "http://www.JohnDoe.com"
# url: "http://127.0.0.1:4000"

# author
author:
  name: 'John'
  first_name: 'John'
  last_name: 'Doe'
  email: 'mail@JohnDoe.com'
  facebook_username: 'JohnDoe'
  github_username: 'JohnDoe'
  head_img: 'static/img/landing/JohnDoe.jpg'
  desc: 'I am a Software Engineer with 5 year experience. I like 
  coding, swimming and potato.'

# sections
sections:
  - id: 'about-me'
    i18n: 'nav.about_me'
    name: 'About'
    tpl: 'about.html'
    css: ''

  - id: 'career'
    i18n: 'nav.career'
    name: 'Career'
    tpl: 'career.html'
    css: 'timeline'

  - id: 'skills'
    i18n: 'nav.skills'
    name: 'Skills'
    tpl: 'skills.html'
    css: 'team'
{% endhighlight %}

> Contents of file may differ based on the theme you choose

Once again commit and push your changes and open your browser to see the changes. That's it! You have your own website up and running totally free of cost. You can continue customizing your theme or learn about writing posts on Jekyll's [official documentation](https://jekyllrb.com/docs/posts/){:target="_blank"}.

---

## (Optional) Configuring Jekyll For Local Testing

At this point, you're all set to start customizing and publishing your content, but isn't it tedious to commit and push every time to preview even the slightest of changes?

> **Confession:** I had to push the code over 200 times while customizing my site without knowing there's an easy way. Make sure you don't do something as silly as that.

Well good news is that you can set up a local version of your Jekyll GitHub Pages site to test changes to your site locally. It is highly recommended by GitHub to install Jekyll in order to preview your site and troubleshoot failed Jekyll builds.

### Installing Bundler for Ruby

* Open Git Bash.
* Check whether you have Ruby 2.1.0 or higher installed:

{% highlight sh %}
ruby --version
ruby 2.X.X
{% endhighlight %}

* If you don't have Ruby installed, [install Ruby 2.1.0 or higher](https://www.ruby-lang.org/en/downloads/).
* After that, install Bundler:

{% highlight sh %}
gem install bundler
{% endhighlight %}

### Installing Jekyll using Bundler

* Check to see if you have a *<span class="evidence">Gemfile</span>* in your local Jekyll site repository. If you have a Gemfile, skip to *<span class="evidence">step 4</span>*.

* If you don't have a Gemfile, open a text editor of your choice, and add these lines to a new file:

{% highlight raw %}
source 'https://rubygems.org'
gem 'github-pages', group: :jekyll_plugins
{% endhighlight %}

* Name the file *<span class="evidence">Gemfile</span>* and save it to the root directory of your local Jekyll site repository. Skip to *<span class="evidence">step 5</span>* to install Jekyll.

* If you already have a *<span class="evidence">Gemfile</span>*, open it in a text editor of your choice, and add these lines:

{% highlight raw %}
source 'https://rubygems.org'
gem 'github-pages', group: :jekyll_plugins
{% endhighlight %}

* Finally, install Jekyll and other dependencies from the GitHub Pages gem:

{% highlight sh %}
bundle install
{% endhighlight %}

### Build your local Jekyll site

* Navigate into the root directory of your local Jekyll site repository.
* Run your Jekyll site locally:

{% highlight sh %}
bundle exec jekyll serve
{% endhighlight %}

* Preview your local Jekyll site in your web browser at *<span class="evidence">http://localhost:4000</span>*.

---

## (Optional) Redirect Your Custom Domain To Your Jekyll Site

If you own a custom domain (say `yourusername.com`) then you can redirect this existing domain to your GitHub Pages website so it replaces the yourusername.github.io address with this more personal one.

* First of all create a new file named *<span class="evidence">CNAME</span>* (all caps, without any spaces or extension)
* Open this file in a text editor of your choice and add your domain name. e.g.

{% highlight raw %}
yourusername.com
{% endhighlight %}

* Save the file and once again commit and push!
* Next, go to your hosting provider and find the *<span class="evidence">Manage DNS</span>* section. Add two records there.
* Add a *<span class="evidence">A record</span>* that points to `192.30.252.153`.

{:refdef: style="text-align: center;"}
![A Record](/assets/images/posts/a_record.png)
{: refdef}
* Add a *<span class="evidence">CNAME record</span>* that points to your GitHub Pages site:

{:refdef: style="text-align: center;"}
![New Repository](/assets/images/posts/cname_record.png)
{: refdef}
* And that's it, wait for changes to propagate and you'll be able to reach your Jekyll site using your custom domain address.