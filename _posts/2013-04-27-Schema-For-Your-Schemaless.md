---
layout: post
title: "Schema For Your Schemaless"
description: "If you're not designing your schemaless DB, then you're doing it wrong"
category: 
tags: [DB, Schema, Schemaless, MongoDB]
---
{% include JB/setup %}

I love databases. Most developers couldn't give two hoots though. You love databases too? Good. Thanks. 

Do you use schemaless databases like MongoDB_\*_ or one of the many others? I'll bet that you enjoy the freedom those NoSQL databases offer?

But, do you still plan a design for the data's structure even though you are using a schemaless database? No? Well, that's a little daft.


<!--more-->

Heh, I just called you daft. Sorry about that. I should probably back up my assertion with an example. And, since I have no imagination lately due in no small part by 17 month old twins keeping me awake, I'll be using a real world example I've recently encountered.

## The Example
This is a simple problem: store unique user visit counts for day, week, and month periods.

If you were to store this data in a relational database you could get away with a single table. With clever use of fields and indexes this approach is probably the simplest of all the ways you could solve this problem. It wouldn't even use that much storage, but some simple aggregation could simplify the counts down for any period.

The solution I was tasked to refactor was implemented in MongoDB and had some unforeseen design limitations. The design made use of a single MongoDB database that holds all of the period collections. This keeps things quite tidy, especially considering that a new collection was being created for every new period. 

That is, a new collection every day for the daily uniques, a new one every week for the weekly uniques, and a new one every month for the monthly uniques. And each of these collections would store the user ID and give this ID a unique index to ensure only one document is created for every user in each of these collections.

To query a period's count, just programatically produce the period's collection name and count the documents in the collection.

## Why Refactor
The lack of performance involved with counting documents was not the primary concern. However, disk usage was a problem. The number of unique types and periods being tracked meant that this simple problem was using almost 12GB of disk space in well over a thousand collections by the time I was tasked with redesigning it.

## My Solution
Initially, it is easy to forget that you have to track a user identifier in order to ensure tracking of uniques. Understanding that you have to keep an account of the user identifiers for the current period is important and meant that the existing code and data formed the basis for the refactored solution. This also meant limited refactoring of the underlying codebase instead of a complete rewrite that could introduce severe bugs.

To start this kind of refactor, you must understand the database's features and limitations. For example, why is the user's identifier stored as an element in a document with its own unique index when the document's MongoID, the "_id", can be used instead.

This simple reuse of the "_id" to hold the user's identifier instead would result in a drastic reduction in the required storage space as the document would be half the size and would not need the extra index.

The next step was to summarise the existing data into a special summary counts collection. The existing programmatic collection names were used as these document's MongoIDs to again reuse the existing index. A cron job runs the script after midnight to tally any old period collections and drop them.

The end result is a dramatic reduction in disk usage and an improvement in performance when viewing old data. 

## Conclusion
Not planning for the eventual growth of your data is going to be devastating for your application. Always understand how your data will grow and what the implications of that growth will be.

It helps knowing the features and limitations of the data store you are using. For example, trying to use MongoDB to perform real-time aggregate queries would be like trying to use MySQL to hold blobs of Json data for your web app.

Finally, always plan ahead, plan in pencil, and have an eraser handy.

_\* [MongoDB](http://www.mongodb.org/) is more accurately described as having a dynamic schema and is loads of fun to play with._