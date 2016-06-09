---
layout: post
title: Implementing CI/CD on Gitlab
date: 2016-06-09
summary: This article will illustrate how to take advantage of Gitlab's built-in continuous integration server to implement continuous delivery for your projects.
categories: [Workflow]
---

A wee while ago, I wrote an article [introducing Gitlab](/posts/install-gitlab-ce-on-ubuntu-14-04/), a rather new kid on the block joining Github and Bitbucket. The work of migrating our projects has been on and off due to overwhelming amount of other work, but fortunately, after two months, I have been able to finalise the migration and introduce my dev team to a whole new (and of course, better) workflow. In this article, I will focus on how to implement Continuous Delivery on Gitlab.

## Gitlab CI
Gitlab offers its own Continuous Integration system, dubbed as Gitlab-ci. Since it's built-in, it doesn't require much configuration, all you need is configure a runner for your project, and it will run a build on each commit. The benefit of having a built-in CI server is obviously that you don't need to configure a third-party CI tool, such as Jenkins, and then hook it into your VCS platform. You can see build results or history straight from your project repository.

<img src="/images/build-history.png">

## CI Runner
A runner is basically a process that runs a build. For each project, you will need to specify a runner in order to run builds for that project. In project setting, you can see the following:

<img src="/images/runner.png">

You can use a shared runner for all your projects, or configure a specific runner for each project as shown above. The configuration file for runners is located at `/etc/gitlab-runner/config.toml`, or it can also be under user's directory if you don't want gitlab-runner run as a privileged user. The configuration looks something like this:

~~~
concurrent = 1

[[runners]]
  name = "Project A"
  url = "<CI URL>"
  token = "token-string"
  executor = "shell"
  [runners.ssh]
  [runners.docker]
    tls_verify = false
    image = ""
    privileged = false
    disable_cache = false
  [runners.parallels]
    base_name = ""
    disable_snapshots = false
  [runners.virtualbox]
    base_name = ""
    disable_snapshots = false
  [runners.cache]
    Insecure = false

[[runners]]
  name = "Project B"
  url = "<CI URL>"
  token = "token-string"
  executor = "shell"
  [runners.ssh]
  [runners.docker]
    tls_verify = false
    image = ""
    privileged = false
    disable_cache = false
  [runners.parallels]
    base_name = ""
    disable_snapshots = false
  [runners.virtualbox]
    base_name = ""
    disable_snapshots = false
  [runners.cache]
    Insecure = false
~~~

As you can see, you have two runners configured, respectively for two projects, and they are both using shell as the executor. You might already noticed that under each runner, there's configuration for various executor options. And yes, docker is one of them! For more information of runner configuration, please have a look [over here](https://gitlab.com/gitlab-org/gitlab-ci-multi-runner/tree/master/docs/executors).

## Continuous Delivery
At the end of the day, you want your code automatically shipped out to production. After all, that's the end goal of employing a CI system, i.e. to make sure new code is well-tested and then delivered without human intervention. You can achieve that by configuring your project's CI configuration.

The configuration of Gitlab-ci is a bit similar to Travis. It's using yaml syntax. You will need to create a file named **.gitlab-ci.yml** under your project root. Take the blow snippet for example:

~~~
production:
  script:
    - cap production deploy --trace
  only:
    - master
  allow_failure: true

staging:
  script:
    - cap staging deploy --trace
  only:
    - staging
  allow_failure: true
~~~

You can define as many **jobs** as you want, using a top-level key, as long as the word is not reserved. From above example, you can see we have two jobs, **production** and **staging**. Under each job, we run a command **only** on the branch as we specify. In this example, we use Capistrano to deploy code to the server. So what happens is, if you push a commit to your remote master branch, the **production** job will be run, which runs `cap production deploy --trace` command on the CI server. (Of course, this would require Capistrano to be installed on the CI server and proper configuration for the deploy key, which is out of scope of this article.)

There are many more configuration options available to this file, e.g. **stages**, **services**, which I am not going to elaborate on here. Feel free to head over to their [documentation](http://docs.gitlab.com/ce/ci/yaml/README.html).

## What's Next
We have only been using Gitlab for a few days, and over the next few weeks, I will write a review on using this platform.