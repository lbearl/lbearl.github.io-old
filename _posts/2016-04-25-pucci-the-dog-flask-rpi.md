---
layout: post
title: PucciThe.Dog - Python/Flask on an RPI
---

## Overview
Flask is an amazing little Python web micro-framework for those who aren't familiar with it. It allowed me to build out this entire application in about 8 hours of total dev time (including a whole bunch of time just not quite grokking flask-login). The minimal goals I had was to expose a very basic web presence for Pucci (the dog), along with building a very basic puppy cam. Since I had an old Raspberry Pi laying around, I figured that this might be the ideal project to use it for. 

## Bill of Materials
1. [Raspberry Pi](http://www.amazon.com/Raspberry-Pi-756-8308-Motherboard-RASPBRRYPCBA512/dp/B009SQQF9C)
2. [Microsoft LifeCam NX-3000](http://www.amazon.com/Microsoft-LifeCam-NX-3000-Webcam-Gray/dp/B000TGAVSQ)
3. [Sweetbox Case](http://www.amazon.com/Sweetbox-Raspberry-Pi-Case-Heatsinks/dp/B00IF9LIHC) (optional)

## The Plumbing
In the interest of making this work as quickly as possible, the actual picture taking logic is just a [shell script](https://github.com/lbearl/puccithe.dog/blob/master/picturetaker.sh) running in cron (specifically once every 5 minutes). It likewise will examine all of the files in the directory and delete any that are more than a day old. The real magic is actually done by fswebcam as documented [here](https://www.raspberrypi.org/documentation/usage/webcams/). 

{% highlight shell %}
fswebcam -r 320x240 --jpeg 80 -D 3 -S 13 /home/pi/poochpics/$DATE.jpg
{% endhighlight %}

Technically the webcam should support higher resolution pictures, but I suspect that it isn't quite as compatible as I was led to believe it was. The `-D 3 -S 13` are very important for me as the camera was corrupting 70+% of the images that it was capturing. These arguments will first delay the capture for 3 seconds and then skip the first 13 frames captured, finally generating a photo based on the 14th frame captured. This numbers were very scientfically found by simply playing with the webcam until it was returning reliable results 100% of the time (it is possible that others will be able to operate the webcam without any delay or skipping any frames). 

Now that all of the photo generation and automatic purging of old images is handled the actual responsibilities for the web application are pretty slim: basically just a regular old mostly static web page with the ability to login to a secure area which will have the actual photos on display. 

I made the jump to almost 100% Windows at home (since I have been working on Windows machine for pretty much my entire career), and decided to use this project as an excuse to try out the Python Tools now available in Visual Studio (spoiler alert: they are awesome). I can't say that I have ever really used an IDE for Python development before (traditionally I've done it in a mix of Vim and Sublime Text), so I can't compare it to some of the other Python IDEs out there, but as someone who gets paid to do C# in Visual Studio 2015, the experience was very nice.

## Implementing the Web Application
In order to actually get things working I just started with the default template that comes with VS for Flask + Jinja2 templating (I contemplated doing this project as an Angular app with a Flask RESTful backend, but decided against it). The code is pretty basic especially considering the entire application only consists of 4 routes and doesn't really do any magic (there isn't even a CRUD component to it). The one thing that isn't exactly standard was my choice for authentication. As I mentioned earlier, this application does use flask-login, and while my original thought was to just hardcode the credentials, I ended up not going down that path as I couldn't come up with a non-hacky way to persist the credentials that wasn't less complex than just adding a SQLite database with some credentials tossed into it. To that end if you look at `__init__.py` you'll notice the need to have a SQLite db called `test.db`. This database just has a single table called "user" which will store ids, usernames, emails, and bcrypt hashed passwords:

![sqlpicture]

## Deployment
It really is an exciting time to be alive when I can build something on a Core i7 desktop with 32 GB of RAM, and then relatively effortlessly deploy it onto a computer the size of a microcontroller with a whopping 512MB of RAM and a sub-1Ghz ARM processor. The deployment is actually pretty standard: its just Apache2 with mod_wsgi and the appropriate wiring as descibed [here](http://flask.pocoo.org/docs/0.10/deploying/mod_wsgi/). The most interesting part of all this is probably the way that DNS is being handled: Namecheap now offers Dynamic DNS, so I have ddclient running on the Pi, automatically updating my home IP address to Namecheap's DNS servers. If you want to try it out for yourself and give me a little boost in the wallet as well sign up [here](https://www.namecheap.com/?aff=94946).

The only thing I'm a little nervous about with this setup is whether or not the relatively underpowered RPi really is going to do that well connected to the public internet. That being said, it is pretty heavily firewalled, with only port 80 exposed so its a fairly limited attack surface for any of those internet hooligans.

I hope you enjoyed reading about my very small flask application on a RPi, please reach out to me on [Twitter @lukebearl](https://twitter.com/lukebearl).

[sqlpicture]: /public/images/puccithedog/sql.png