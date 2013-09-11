---
layout: post
title: "Laravel 4 and ElastiCache"
description: "Extend Laravel 4 to use Amazon's ElastiCache"
category: 
tags: [Amazon, AWS, ElastiCache, Laravel4, development]
---
{% include JB/setup %}

My team uses Amazon's Web services (AWS) to meet our day-to-day Internet and Web needs. Our hosting stack is very straightforward and we try to use many of the services offered by AWS. These include CloudFormation, Elastic Beanstalk, as well as ElastiCache.

Therein lies our problem. Laravel does not support ElastiCache without custom extensions. I'm going to share how I have implemented this custom cache extension.

<!--more-->

## Problems

ElastiCache is the reason for this post so I will describe it quickly.

Imagine a flexible cluster of Memcached instances each running on a server and your Web application can discover them all through a connection to a master instance.

It is simple and easy and all you have to do is enable some freaky options in the Memcached driver: OPT_CLIENT_MODE & DYNAMIC_CLIENT_MODE.

However, that is a problem with a few parts. Firstly, you do not have the AWS Memcached driver when running things locally so trying to set these options will crash your script.

Secondly, we use the [Laravel](http://laravel.com/) PHP framework and it has no concept of ElastiCache in their default Memcached caching driver. Which means you have to extend the cache driver with one of your own devising.


## Solution

First we need to create our own version of the Memcached Connector (MemcachedConnector.php). I placed this one in /app/libraries/ElasticacheConnector.php:

<script src="https://gist.github.com/sperelson/6528932.js">
</script>

I then included a PHP file in app/start named for the environment that will be running with ElastiCache (prod.php) with the following code:

<script src="https://gist.github.com/sperelson/6528976.js">
</script>

Set the cache.driver config value to 'elasticache' to ensure that this custom cache driver is used.

## Conclusion

What is interesting here is that I am not writing a new driver for ElastiCache. I am only modifying the connection to make use of the required options that enables the dynamic discovery of the ElastiCache servers. I have also done this in such a way that you could use this custom cache driver for local development if you wanted to not worry about the different configs. This works because the options are only applied if they exist.

I have used the equivalent of this technique for Laravel 3.x in another project but I have yet to test this particular code in production. So, your mileage may vary and I'll add the disclaimer that you use any code or methods presented here at your own risk. Please let me know if you encounter any issues - I will update the post as necessary.
