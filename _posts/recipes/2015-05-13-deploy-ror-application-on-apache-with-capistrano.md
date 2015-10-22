---
layout: post
title:  "Deploy RoR application on Apache with Capistrano"
date:   2015-05-13 10:28:30
category: recipe
tags: 
- devops
- ruby
- ruby-on-rails
- capistrano

---

This tutorial suppose that you already have a working installation of an Apache server.

### Install Phusion Passenger

Phusion Passenger has been by far the easiest way I've found for managing multiple Rails application instances on top of either Apache or Nginx. It comes as a gem and has custom modules for both major web servers.

{% highlight bash %}
# Install gem
$> gem install passenger
# Install passenger for apache2 (then follow the instructions)
$> passenger-install-apache2-module
{% endhighlight %}

Then you will need to update your apache configuration (has said in the passenger instructions).

First, create a file called ```/etc/apache2/mods-available/passenger.load``` and input the following code :

{% highlight bash %}
# /etc/apache2/mods-available/passenger.load
LoadModule passenger_module /home/boissonnat/.rbenv/versions/2.1.5/lib/ruby/gems/2.1.0/gems/passenger-4.0.59/buildout/apache2/mod_passenger.so
{% endhighlight %}

Second, create a file called ```/etc/apache2/mods-available/passenger.conf``` and input the following code :

{% highlight xml %}
# /etc/apache2/mods-available/passenger.conf
<IfModule mod_passenger.c>
  PassengerRoot /home/boissonnat/.rbenv/versions/2.1.5/lib/ruby/gems/2.1.0/gems/passenger-4.0.59
  PassengerDefaultRuby /home/boissonnat/.rbenv/versions/2.1.5/bin/ruby
</IfModule>
{% endhighlight %}

Then, enable the passenger module in Apache and restart the server.

{% highlight bash %}
$> sudo a2enmod passenger
$> sudo service apache2 restart
# Check passenger is activated :
$> apache2ctl -t -D DUMP_MODULES
{% endhighlight %}


### PostgreSQL

Last but not least, you will need a DB engine. I mainly use PostgreSQL. Here is the installation process.

{% highlight bash %}
# install postgreSQL
$> sudo apt-get install postgresql-9.3
# Set the default password
$> sudo -u postgres psql postgres
\password
{% endhighlight %}


### Capistrano

#### First create and init a new remote on your server

Capistrano needs git to update your code source. You can use any git repository as it is accessible from your server. Sometimes you could have a repository inside your local network and a server outside this network. In this case, your server will not be able to pull your code source. You can still create a git repository on your server, add it as a remote and push to it for capistrano. Here is how :

On server

{% highlight bash %}
# Server side
# Create a folder with the .git extension
$> mkdir myapp.git
# Init a bare git repository
$> cd myapp.git
$> git init --bare
{% endhighlight %}

On local

{% highlight bash %}
# Client side
# Note here we call this new remote 'preprod'
$> git remote add preprod bob@your.server:/somewhere/myapp.git
# Then push your branch (use to the preprod) to this new repository
$> git push preprod master
{% endhighlight %}

#### Capify your application

Add this following code into your ```Gemfile```

{% highlight bash %}
group :development do
    gem 'capistrano'
end
{% endhighlight %}

And then :

{% highlight bash %}
$> cd /path/to/your/app
$> bundle
{% endhighlight %}

And finally :

{% highlight bash %}
$> cd /path/to/your/app
$> cap install
{% endhighlight %}

#### Capistrano settings

Capistrano is very well documented. By following the comments, first edit your ```/config/deploy.rb``` and then your specifics environments settings in ```config/deploy/my_env.rb```

#### Setup Apache

You need to create and setup permissions for the folder which will be used to deploy your app.

{% highlight bash %}
# Create the folder
$> mkdir /var/www/myapp
# Set the group to www-data (the apache group)
$> sudo chgrp -R www-data /var/www/myapp
# Set the user to your deployer user
$> sudo chown -R yourdeployer /var/www/myapp
# Allow group to update the folder
$> sudo chmod -R 775 /var/www/myapp
{% endhighlight %}

Then you need to create a virtual host to your current folder. Create a new file called ```myapp.conf``` in ```/etc/apache2/site-available```

{% highlight bash %}
$> touch /etc/apache2/site-available/myapp.conf
$> nano /etc/apache2/site-available/myapp.conf
<VirtualHost *:80>
        ServerName www.yourhost.com
        # In case of several virtual host based on path :
          # ServerName IP
          # ServerPath /path/
        DocumentRoot /var/www/myapp/current/public
        <Directory /var/www/myapp/current/public>
                # This relaxes Apache security settings.
                AllowOverride all
                # MultiViews must be turned off.
                Options -MultiViews
                # Uncomment this if you're on Apache >= 2.4:
                Require all granted
        </Directory>
</VirtualHost>
{% endhighlight %}


Don't forget the ```.conf``` extension. Without this extension Apache is not able to treat it.

Finally, you need to activate your site :

{% highlight bash %}
$> sudo a2ensite myapp
# And reload apache
$> service apache2 reload
{% endhighlight %}


#### Deploy your application


{% highlight bash %}
$> cd /path/to/your/app
$> cap production deploy
{% endhighlight %}

