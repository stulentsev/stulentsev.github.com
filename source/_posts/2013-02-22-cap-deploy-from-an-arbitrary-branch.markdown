---
layout: post
title: "cap deploy from an arbitrary branch"
date: 2013-02-22 12:34
comments: true
categories: deployment
keywords: deployment capistrano cap branch git
---

As you know, capistrano can deploy your app from a specific branch in the repo. You just have to specify it

``` ruby
set :branch, 'develop'
```

If you develop in a single branch, that should do it for you. All commits go to one branch and all deploys are likely served by the same branch. This is a simple model, good enough for small projects. 

However, if you adopt [git flow](http://nvie.com/posts/a-successful-git-branching-model/), it encourages you (not to say "forces") to have separate branch for every feature, release or hotfix that you're working on. And, naturally, you want to deploy to dev/staging server right from a feature branch, not having to merge it first to develop/master. This is trivial to accomplish, just change the branch name:

``` ruby
set :branch, 'feature/my-test-feature'
```

Now you have a pending file change. What do you do with it? Commit to source control? That increases file churn rate for absolutely no reason. `git checkout` that file? This is an extra action, of which you will be tired quite soon (not to mention chance to accidentally commit the file).

Solution? Externalize the setting. When deploying, look in the environment for a branch to use and fallback to default name.

``` ruby
set :branch, ENV['BRANCH'] || "develop"
```

Then you do this

``` bash
BRANCH=feature/my-test-feature cap deploy
```

Or, if you have multistage enabled

``` bash
BRANCH=feature/my-test-feature cap staging deploy
```

Of course, this requires a bit more typing on each deploy, but at the same time it decreases chances of deploying from a wrong branch.

Happy deploying!