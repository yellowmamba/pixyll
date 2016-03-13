---
layout: post
title: Install Gitlab CE on Ubuntu 14.04
date: 2016-03-13
summary: Gitlab is a rather new kid on the block of version control systems. There are apprently plenty of good reasons to consider Gitlab as an enterprice solution.
categories: [Workflow]
---

Many companies (including mine) these days consider taking Gitlab, instead of ever popular Github or Bitbucket, as their primary tool to manage their source code. Some of the most important reasons being, I guess, free on-premises plan (aka. Gitlab Community Edition), built-in Continuous Intergration known as Gitlab CI, which is a very intriguing factor that allows non-tech-savvy companies to quickly implement IT automation. As my team has recently decided to move onto Gitlab and I have no prior experience with it, I will start by writing up some gotchas I ran into when installing Gitlab CE on Ubuntu 14.04. 

Please note, this article will only talk about installing Gitlab on your own server. If you use its SaaS product (gitlab.com), you can stop reading here.

## Hardware requirements
Gitlab CE does require a fair bit of hardware capacity for it to be run smoothly. From its [doc](http://doc.gitlab.com/ce/install/requirements.html#hardware-requirements), it is recommending at least 2 CPUs plus a 2GB memory. Your server can be a physical server, or a cloud-based virtual server like a EC2 instance. However, shared or semi-dedicated servers are not recommended in my opinion, as later you don't want to run CI on the same server where your application is being served. If you do worry about latency and stuff, using a VPC for example might be a solution.

## Ruby
Gitlab being a ruby application, you will surely need ruby runtime on your server. This is where it took most of my time to set it up. Ubuntu 14.04 comes with Ruby 1.9.3, the minimum version of Ruby required to run Gitlab is 2.1.\*, not 2.2, not 2.3. The tricky part here is that you **cannot** install Ruby via rvm or similar, because openssh won't work with that. You have to install Ruby from its standard implementation, namly via source. Here's the working commands on Ubuntu 14.04, it might differ on other servers.

~~~
> sudo apt-get remove ruby
> cd /tmp
> wget https://ftp.ruby-lang.org/pub/ruby/2.1/ruby-2.1.8.tar.gz
> tar -xvzf ruby-2.1.8.tar.gz
> cd ruby-2.1.8
> ./configure --prefix=/usr/local
> sudo make
> sudo make install
~~~

## Get URL right
An interesting issue came up when I initially attempted to browse it from the url. Looks like Gitlab installation takes the server's hostname as its FQDN, which makes perfect sense, as your server may not be public yet. You will most likely need to change a parameter called `external_url` in its default configuration file `/etc/gitlab/gitlab.rb`. Set the domain to be the FQDN and you should be ready to go. I tested it first in a vagrant box, in which case, the value of this parameter is likely to be `"http://localhost"`, then you should be able to access it from something like `http://localhost:8080` on your host machine.

## Bits and bobs
The installaton has Nginx built in to serve its content. You can also use a non-bundled web server, e.g. install an Apache server to run Gitlab. For details, please consult their doc on [using a different server](http://doc.gitlab.com/omnibus/settings/nginx.html#using-a-non-bundled-web-server)

## Conclusion
I will likely be writing more on this topic in the coming months as I explore how great it will be.