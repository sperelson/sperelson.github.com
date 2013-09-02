---
layout: post
title: "Tracking Mxit Apps"
description: "Methods for tracking the usage of Mxit portal applications"
category: 
tags: [Mxit, analytics, development, tracking]
---
{% include JB/setup %}

Tracking the usage of any application is pretty useful stuff. So useful in fact that there are a plethora of SAAS offerings out there for collecting data for analytics, some even free to use. It doesn't take much effort to ensure that any of your apps feed useful data back to one of these services to generate useful statistics.

But, certain environments make the default collection methods very difficult to use. One such environment would be Mxit's portal applications. These portal applications are parsed through a special proxy server and are displayed in a very constrained client application that offers no scripting. Meaning every bit of intelligence must be built into the application's server-based logic stack.

I recently implemented an image-based tracking method to enable tracking via Effective Measure for some Mxit portal application properties. This is how I did it.

<!--more-->

## Solution

1. Insert an image tag pointing to a handler server-side with a cache busting, dynamically generated unique nonce
2. Collect the data for stats submission and submit
3. Collect the data that would be handled by cookies and store server-side (Memcached works well)
4. Return a single pixel transparent gif

### Cache Busting Nonce?

We have to ensure that the Mxit proxy server does not cache the returned gif. If it does, this method of tracking will never work. But this method does require some planning.

I typically generate and cache all dynamic pages to ensure performance and this tracking image cannot be cached as well. So my solution is to leave off the closing HTML body tag from the cached page. When a page is requested, I simply assemble the generated or cached page with the tracking image and then slap on the closing HTML tags.

The beauty of this solution is that you could have multiple tracking tags for different services without affecting page response times. The image request is initiated from the Mxit client and means that the page is rendered on the handset very quickly.

### Store Server-Side?

The Mxit proxy server does not handle certain interactions very well. One example is that many HTTP status responses are not supported and do not fail gracefully. For example, if your app returns a 307 expecting the user to be redirected you would be mistaken and you would quickly learn to stick to a 302.

More importantly for this solution, the Mxit proxy server does not expect to receive cookies from an image file request. Cookies that your server sends in response to the image request are silently ignored.

The only solution is to store the session data on the server. AWS offers Elasticache which works very well for this purpose if you're hosted on AWS.

I store all the cookies related to a unique user and application in a single blob of data that is set to never expire. Each cookie stored within the blob should maintain the same values the normal cookie would: expiry, path, domain, and value. 

After reading the cookie, but before using the value stored therein, the expiry date and other values should be checked for validity. So, for example, if the cookie has expired then discard it. Then proceed to update the relevant cookie values with the newer data after a tracking image request.

### Return a gif?

If you don't return a valid image/gif then the Mxit client may display a rather ugly looking Missing Image placeholder. Examples of returning transparent gifs can be found easily enough online.

## Conclusion

I'm using this in production with an application that is receiving about 40,000 page views a day and each page has two different tracking images. It is working very well. This method can also be scaled very nicely from using a dedicated server to a cluster of servers.

The only failing of this solution is that Mxit clients that do not support images will not be tracked. Fortunately, these clients are a very small percentage of the total.
