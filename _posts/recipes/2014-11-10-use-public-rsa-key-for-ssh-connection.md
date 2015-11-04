---
layout: post
title: "Use public RSA key for SSH connection"
date:   2014-11-10 10:28:30
category: recipe
tags:
- devops

---

It is really useful to use a SSH connection through public key instead of having to type the password each time (especially if you are using a very complex password).

### Create a .ssh folder on your server

First of all, server side, you need to create an ```.ssh``` folder in your home dir.
This is where you will keep your public keys.

{% highlight bash %}
# server side
$> mkdir ~/.ssh
{% endhighlight %}

### Copy your ssh key on the server

Then, you need to copy your public RSA key on the server. Remember that this public key belongs to a machine.
If you plan on connecting to your server through several computers, you will need to reproduce this step for each of them.

{% highlight bash %}
# client side
$> cat ~/.ssh/id_rsa.pub | ssh bill@xxx.xxx.xxx.xxx 'cat - >> ~/.ssh/authorized_keys'
{% endhighlight %}

### Update rights for user to use your public key

Change the permissions of the ```authorized_keys```:

{% highlight bash %}
# server side
$> sudo chmod 600 ~/.ssh/authorized_keys && chmod 700 ~/.ssh/
{% endhighlight %}
