---
layout:     post
title:      Automatic Tweet For S3 Websites With Lambda
date:       2016-04-12
summary:    Leverage AWS Lambda to publish tweets for your static blog site on S3 in a serverless architecture.
categories: [AWS]
---

Since I [moved my site to AWS S3](/posts/go-static-with-s3-and-cloudfront/) and decided to blog regularly, I'd like to publish a tweet each time after a new blog post goes live. To do this, I wanted to leverage AWS Lambda because it can respond to an S3 event in a real-time fashion and do stuff without provisioning any infrastructure like an EC2 instance. The overall architecture is like this:

<img src="/images/architecture.png">

## Choose a unique prefix for post objects
You only want to invoke the Lambda function on new posts, therefore you need to standardise the post url, so that when you configure the S3 event, you can specify the prefix of the new objects. In my case, I had to restructure my site a bit so all posts' URIs conform to `/posts/{title-slug}/`. To configure the s3 event, I could specify `posts/` as the prefix, so the Lambda function only gets called on objects created under that path.

<img src="/images/s3-event.png">

## Create your Lambda function
After you have properly structured your bucket and had s3 events configured, you can now worry about creating the Lambda function that actually does things for you, which in my case, is publishing a tweet regarding the new blog post to my status board.

My Lambda function is written in Node.js, which is one of three supported runtimes in Lambda. I won't go into details about implementation, my code is available in [Github](https://github.com/yellowmamba/lambda-tweet-example) if you are interested. No matter how you are going to implement the function, it's worth noting that Lambda is purely a pre-configured environment to run your code, so if your code has any dependencies that you need to run a command to install, you will need to package the dependency code with your own function code in the zip that will be uploaded to Lambda. In my case, I needed `cheerio` node module which isn't available in Lambda by default, I had to run `npm install cheerio` on my local and package `node_modules` directory along with my function code in the zip file.

## Test
Of course, you want to test it before sending gibberish to your Twitter wall. I did it by creating another s3 bucket to serve as a static website, hooking up Lambda function to this bucket, creating another Cloudfront distribution for it, deploying my site to this bucket, and checking my Twitter statuses.

## Limitation
In my implementation, I used `<title>` of the post as the source of the tweet. That's the easist the way of telling people what I have just blogged. The limitation of this approach is obviously the tweet is inflexible, e.g I can't mention anyone in the tweet or add hashtags to it. If you want flexibility, you can use a user-metadata to specify the tweet content, but I think that's the same as manually tweeting it after your post is pubished. After all, I wanted to automate this process by not going to my Twitter page.

That's about it. This post is not aimed at showing you how to create a Lambda function, because none of the suppported runtimes (Python, Java, Node) are my type. The takeaway is the architecture I illustrated in the beginning.