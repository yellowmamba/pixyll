---
layout: post
title: First touch on Meteor
date: 2015-03-02
summary: Meteor, which has been out for a while, is a full-stack framework to build web apps purely in Javascript, in this article I will be talking through some of its key concepts, e.g. Meteor's reactive data source and and its publish/subscribe model.
categories: [Meteor]
---

I have heard of [Meteor](https://www.meteor.com/){:target="_blank"} a couple of times from various meetups and among my colleagues, so I had decided to take a look at it while I was taking some time off work. I should acknowledge upfront that I am not a pro in this framework (not even in Javascript!), I have only read through its excellent [tutorial](https://www.meteor.com/install){:target="_blank"}, that I would strongly recommend to everybody who is also interested in learning this framework. Therefore, the following discussion might be misleading. I am more than happy for you to get in touch with me if you find any of my points seriously wrong. ;)

#### Reactive data source
This is the most intriguing concept that Meteor has brought to me. One of the greatest features of Meteor is that it exposes direct collection (data source) access to the client side, and this is where **reactive** comes into play. The view that contains dynamic data will be updated automatically whenever the underlying data source changes. To make it easier to understand if you are familiar with MVC pattern, the **View** will be updated without needing to wait for the data returned from your **Model** class.

How is this implemented? Technically I am not sure. In Meteor docs, it says this:

> On the client (and on the server if you specify a connection), a Minimongo instance is created. Minimongo is essentially an in-memory, non-persistent implementation of Mongo in pure JavaScript. It serves as a local cache that stores just the subset of the database that this client is working with. Queries (find) on these collections are served directly out of this cache, without talking to the server.

By the way, Mongodb is the default database that Meteor works with. From the docs it's obvious that the data that the view/template works with is a locally cached data source, which is obviously **reactive** to the real, server side data source.

#### Publish/Subscribe model
By default, Meteor takes the entire database to the client side as a cached data source, and that brings a problem. How do I protect user sensitive data? For example, you don't want to show all customers' transaction records whenever a customer logs in. This is what publish/subscribe model is for. Basically, this model allows you to explicitly specify what data can be made a replica on the client side. A **publication** has to be defined on the **server side** that returns a subset of the collections in your database, then your client  **subscribes** to that publication on the **client side**, and this **subset** your client subscribes to is the "actual" database your client side code can work with.

The implementation of this model makes Meteor vastly different from most of traditional web frameworks. In a traditional framework, when the client requests a resource, the request gets sent to the server, where the server will authenticate it and check if the requested resource is the authorised content to the requester. However in Meteor, due to the **reactiveness**, the server has to set the perimeter of data beforehand because it rarely responds to client requests. For instance, in a bank system, each user will have a different subset of transaction collection because the server will only return transactions that are associated with that particular logged-in user to the client side.

#### A little incentive
As I am coming from PHP background, the first thing that I had in mind after I finished its tutorial was, does it work with MySQL? I have found a [package](https://github.com/drorm/meteor-sql){:target="_blank"} that seems to do the trick, but I haven't tested it myself yet.

That's pretty much everything I'd like to talk about in this post. Again, what I discussed here is just my own understanding of Meteor, if you want to get your hands dirty on it, I strongly suggest you take the tutorial I mentioned above.
