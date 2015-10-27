---
layout: post
title: "Install Ruby on Rails on Ubuntu server"
date:   2015-05-12 10:28:30
category: "recipe"
tags: 
- devops
- ruby-on-rails
- ruby

---

This article shows how to install ```ruby``` and ```rails``` on Ubuntu through ```rbenv``` 

## Install ruby

We are going to install Ruby through ```rbenv```.

### Install some dependencies for Ruby

The following set of dependencies just below contains everything you need to run Rails application on Ubuntu server.
Look at them carefully.

{% highlight bash %}
$> sudo apt-get install git-core curl zlib1g-dev build-essential libssl-dev libreadline-dev libyaml-dev libsqlite3-dev sqlite3 libxml2-dev libxslt1-dev libcurl4-openssl-dev python-software-properties
{% endhighlight %}


### Install rbenv

We prefer here to use ```rbenv``` instead of ```RVM``` to its permissions management. 
Let's clone rbenv in ```home_dir```

{% highlight bash %}
$> git clone https://github.com/sstephenson/rbenv.git ~/.rbenv
{% endhighlight %}


Now we will add ```~/.rbenv/bin``` to our ```$PATH``` for access to the rbenv command-line utility.

{% highlight bash %}
$> echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bashrc
{% endhighlight %}


Add rbenv init to your shell to enable shims and autocompletion.

{% highlight bash %}
$> echo 'eval "$(rbenv init -)"' >> ~/.bashrc
{% endhighlight %}

### Install ruby-build

Installing ruby-build as an rbenv plugin will give you access to the rbenv install command.

{% highlight bash %}
$> git clone https://github.com/sstephenson/ruby-build.git ~/.rbenv/plugins/ruby-build
{% endhighlight %}

### Install Ruby versions

rbenv offers some useful command like ```list``` or ```install```. Install the ruby version that suits you as simple 
as that :

{% highlight bash %}
# list all available versions:
$> rbenv install -l
# install a Ruby version:
$> rbenv install 2.1.5
{% endhighlight %}


### Set the global version of Ruby 

{% highlight bash %}
$> rbenv global 2.1.5
{% endhighlight %}

Installs shims for all Ruby executables known to rbenv (i.e., ~/.rbenv/versions/*/bin/*). 
Run this command after you install a new version of Ruby, or install a gem that provides commands.

```$> rbenv rehash```


## Install rails

Now Ruby is installed, you can install rails by typing :

{% highlight bash %}
# rails installation
$> gem install rails

# Or installing a specific version
$> gem install rails --version 4.2.0

# Activate rails executable
$> rbenv rehash

# Check rails installation (and version)
$> rails -v
Rails 4.2.0
{% endhighlight %}


### Note
> You don't need to specify ```--no-ri``` ```--no-rdoc``` every time you install a gem in production: just 
> add ```gem: --no-rdoc --no-ri``` to ```~/.gemrc``` (create that file if it doesn't already exist) and don't worry about ri or rdoc again in production.
