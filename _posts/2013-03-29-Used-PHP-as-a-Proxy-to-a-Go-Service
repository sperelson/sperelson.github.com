---
layout: post
title: "Used PHP as a Proxy to a Go Service"
description: "Improve reliability and availability of custom processes on a virtual hosting platform"
category: 
tags: [Go, GoLang, PHP, VPS, Webfaction]
---
{% include JB/setup %}

I love playing with new technology and that includes programming languages and environments. I've been wanting to use [Go](http://golang.org/) in a project for a while and the last time I tried I was not thrilled with the method my web host uses to run custom apps.

It isn't like they do it badly or wrong. It's just that if the process dies, it isn't trivial to get it going again. Minutes of dead website is minutes of dead website. Not something any web developer wants. But, I think I have a solution.

<!--more-->

## Current State
Webfaction is a seriously awesome web host. You get a virtual piece of a hardcore server that you can do almost anything with. If you haven't checked them out do yourself a favour and me a huge one and please use this affliate link: [Webfaction](http://www.webfaction.com?affiliate=sperelson).

Webfaction offer a huge list of preconfigured services that include options for Django, Wordpress, Rails, Drupal, and more. You also have the option of a custom application. Through a bit of effort I discovered that you can easily compile a Go program in a correctly configured CentOs 6.3 virtual machine and run the resulting executable in your Webfaction server account.

Unfortunately, the issue I have with this is that the default approach to setting up a custom application on Webfaction is to expose the service on an assigned port, which is then exposed through some magic via a virtual host via an Nginx front-end.

If the custom service dies nothing will restart it unless you set up a cron job to verify that it is running. And then you're stuck waiting a few minutes before it gets restarted at the worst end. Also, avoiding downtime during upgrades is very difficult.

## One Solution
I realised that I could easily use a standard scripting language, like PHP, as a proxy to the compiled custom service. This offers some interesting benefits:
- Quick restart of the custom service if it dies. Stampeding herds can be minimised with a locking file during the custom service restart.
- The custom service need only handle the tasks that require serious processing and maybe custom session & data management. Leaving common tasks to the scripting front-end.
- The ability to seamlessly upgrade to newer custom service executables is possible. This can be done by bringing up the new custom service on a different port and pointing the front-end script to the new version.

The bad:
- Another proxy handling things. All these layers make for a complex system. Not as easy to maintain.
- The development environment becomes tricky to set up. Although, I have dealt with this by creating a nicely set up Vagrant virtual machine.
- Won't calling the custom service from a front-end script be slow? Yes, slightly. But, you shouldn't be using the custom service for anything that wouldn't need what it does best -- fast processing and very cool in-memory stateful data in as much as its benefits outweigh using the front-end script only. Besides, the Webfaction setup means calling the custom service on the localhost. It would be about as fast as calling Memcached from a PHP only driver.

## Conclusion
This combined approach offers a good blend of flexibility and power and the right project should benefit greatly.

I am looking forward to trying this out in a real-world project. I have something in mind that would work really well. It has parts best handled by the front-end script and parts best handled by an always running custom service.

