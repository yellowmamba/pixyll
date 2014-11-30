---
layout:     post
title:      Go static with S3 and CloudFront
date:       2014-11-30
summary:    Having my site on S3 means I can spin up an EC2 instance and do whatever I want, install whatever packages available, or even lock myself out of the AMI for good. ;)
categories: [AWS]
---

I now proudly announce that I have moved my blog site onto Amazon S3, and distributed by CloudFront!

#### Why
The main reason is that I want to make the most out of [AWS Free Tier](http://aws.amazon.com/free/){:target="_blank"}. As I used to build my blog on Wordpress (not pointing fingers here), that means the website would need at least a web server and a database. The minimum resources required in that scenario would be one running EC2 instance, which means, I cannot spin up another instance for messing things around without paying more. Of course, you can just mess around in the instance where you host your site, but naturally, you don't want to use a production AMI for that purpose!

Having my site on S3 means I can spin up an EC2 instance and do whatever I want, install whatever packages available, or even lock myself out of the AMI for good. ;)

#### How
To host my blog on S3 means the site has to be static. I came across [Jekyll](http://jekyllrb.com/){:target="_blank"}. I know there are loads of other decent static site generators out there, but this one just works my way, and it has an excellent [deployment method](http://jekyllrb.com/docs/deployment-methods/#amazon-s3){:target="_blank"} that's already seamlessly integrated with S3.

After you get your site generated and pushed to your S3 bucket, it's likely that you will already have a distribution in CloudFront if you follow the deployment method I mentioned above, but if there isn't, it's easy enough to [create a distribution](http://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/distribution-web-creating.html){:target="_blank"} for your S3 bucket.

The next step is to map your own domain (e.g. mysite.com) to the distribution domain name. One way to do that is to add a CNAME record in your DNS provider for the distribution domain name, so it will redirect DNS queries to the distribution. If you use Route 53 as your DNS service, like this site, you can simply [create an alias resource record set](http://docs.aws.amazon.com/Route53/latest/DeveloperGuide/resource-record-sets-values.html#resource-record-sets-values-alias){:target="_blank"}. One thing to note here is that, you **must** have your aliases included in **Alternate Domain Names (CNAMEs)** field in your CloudFront distribution settings. For example, my setting is like this:

~~~
*.wujianfeionline.com
wujianfeionline.com
~~~

The first line uses wildcard to include all subdomains, including `www` record. So when you edit your alias resource records, you can alias your CloudFront distribution domain name to `www.wujianfeionline.com` and `wujianfeionline.com`.

It might take some time for AWS to propagate DNS records, and then you should be able to see your website hosted on S3 via your own domain name.