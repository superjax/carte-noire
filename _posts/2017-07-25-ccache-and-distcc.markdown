---
layout: post
title:  "Speeding Up Compilation with ccache and distcc"
date:   2017-07-25 12:10:55 -0600
categories: gcc ccache distcc compiling C++
---

As a ROS developer, I find myself wanting to do clean builds of my ROS workspace
pretty frequently.  This can take a really long time if I'm compiling things like
GTSAM and g2o or visual odometry algorithms.  ccache is a compiler cache which
hashes the source files to determine if it can use a cached version of the object
file when doing compilation.  The authors claim that it produces identical results
to normal compilation.  distcc is a way to distribute compilation across several
computers simultaneously, so I can do things like `make -j12 -l12` with my laptop, using my desktop computer as a compilation server

## Installing ccache and distcc

This is easy
{% highlight bash %}
sudo apt install ccache distcc
{% endhighlight %}
## Setting up ccache

So, what you end up doing is pointing to ccache itself as your compiler.  It in
turn knows where the actual gcc and g++ binaries are, and will use them if it
needs to.  All you do is _prepend_ `/usr/lib/ccache` to your `PATH` variable.

Do this by adding the following line to the _bottom_ of your `~/.bashrc` file

{% highlight bash %}
export PATH=/usr/lib/ccache:$PATH
{% endhighlight %}

Next, source your `~/.bashrc` and check that it worked with `echo $PATH`.  You
should see something like this

{% highlight bash %}
superjax ~ $ echo $PATH
/usr/lib/ccache:/opt/ros/kinetic/bin:/home/superjax/bin:/home/superjax/.local/bin:
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:
/snap/bin:/opt/sublime_text:/opt/gcc-arm-none-eabi-5_4-2016q3/bin:/opt/sublime-text
{% endhighlight %}

where `\usr\lib\ccache` is at the start.

Another check you can do is `which gcc` which should return the following:

{% highlight bash %}
superjax ~ $ which gcc
/usr/lib/ccache/gcc
{% endhighlight %}

## Setting up distcc (server)
So, go ahead and install distcc on the server computer (in my case, the desktop)
{% highlight bash %}
sudo apt install distcc
{% endhighlight %}
And then spin up the server.  (The following assumes I'm on the 10.8.33.0 subnet,
and I'm allowing all hosts on that subnet to send jobs)

{% highlight bash %}
sudo distccd --log-file=/tmp/distccd.log --daemon -a 10.8.33.0/24
{% endhighlight %}

## Setting up distcc (client)
So, now you have to tell ccache to use the distcc distributed compilation servers.
To do this, just add the following line to your `~/.bashrc` file.

{% highlight bash %}
export CCACHE_PREFIX=distcc
{% endhighlight %}

Next, we have to tell distcc where to go to find the server.  This also, just add
to the bottom of your `~/.bashrc`  (my desktop is at 10.8.33.182 and it has 8 cores,
  my laptop is at localhost and has 4)

{% highlight bash %}
export DISTCC_HOSTS='localhost/4 10.8.33.182/8'
{% endhighlight %}

Another useful thing if you're using ROS, is to tell ROS automatically how many
jobs it has available during a `catkin_make`.  This is easily done by adding the
following to your `~/.bashrc`.

{% highlight bash %}
export ROS_PARALLEL_JOBS='-j'$(distcc -j)'  -l'$(distcc -j)
{% endhighlight %}


## Test Configuration
So, I just went ahead and compiled g2o from source.  To check that ccache is working
just run

{% highlight bash %}
watch -n1 'ccache -s'
{% endhighlight %}

in a separate terminal, and you should see the statistics change.  You can also
ssh into your build server, and run `top` or `htop` to see if the distcc process
starts spooling up its threads.  If all goes well, you should have vastly
improved compile times.
