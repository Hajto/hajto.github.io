---
layout: post
title: "Git it?"
status: draft
type: post
published: released
draft: true
comments: true
open_image: "https://cdn.meme.am/cache/instances/folder413/500x/75717413.jpg"
---

Nowadays git is like air for programmers. You have to know it, it will come in handy sooner or later. There is no point fighting against it. You should just embrace it's possibilities.

<!--more-->

If you don't know or (what's even worse) feer git, you are gonna have baaaad time... In such situation Git is like that aunt in family that no one really likes, but have to take of her. But when you are gonna make a giant mess you know that she will come and help you out.

Git is very simple system. I suggest you start with whatever project you like, even a folder with couple of text files will suffice. If you're ready please start terminal of choice and make sure have installed git package from whichever packagemanager you're using.
The first thing you want to is to type `git init` it will create `.git` directory with bunch of files and folders you don't have to worry about right now. Hurray!! You have successfully started a Git repository! But right now Git have no idea on which files to watch. Imagine that you had a new watch dog. You would make him comfortable and hope that he will bark at everything that touches your things. Next day you wonder where is your beloved car... Dog would look at you in despair, because he didn't bark at thief, because he had no idea that car is yours! Same applies for git. It will protect all your precious files, but have to tell it which files you care about.

So let's do it right now. We use command `git add` for that purpose. As a parameter we will pass a path to file that you want to add to tracked files. But what does `tracked` actually mean? In this case tracked files are files which are gonna be added to commit. But w8.. What if we want to track multiple files? Do we really have to add each file manually? No... Git will be also happy enough with directory. So.. if you really want to, you can just pass `.` as a parameter for `git add` and every single file in your root of the project should be added to tracking, because as you probably know by now `.` means current catalog in Linux.

So what is that scarry `commit` thing that I mentioned? You can think of it as save state in Game. When you play your favorite game and you save it, you usually name it appropriately like `Boss XYZ killed, before talk to 123`. Actually, the same thing applies for Git. Commit is like save for your project. What will `git commit` do is take list of your `tracked` files, it will calculate diffences between your previous state and this, then save the diffences. If you didn't specify the `-m` param in the `git commit` it will open the default text editor so you could name the commit. The scary part is that most often the default text editor is VIM. But don't worry it won't bite you! If you are really scare of that beast please use `-m "name of the commit"`.

Knowing that you can actually *load* any of those *saves* and continue from any of previous commits in the past, but that's material for whole another post..

When it comes to names there is a lot of conventions. So choose one that fits your style the most. But for GOD SAKE don't do such terrible things like this:

![]({{ site.baseurl }}/images/git-basics/bad.png){:class="center limit-height"}
*Very bad, ugly and unmeaningfull names*

If there is fire in the building just name the commit fire not `ouehnglndsgbergour`. Here are few good examplea of  good commit names examples taken from Phoenix repository on github.

![]({{ site.baseurl }}/images/git-basics/good.png){:class="center limit-height"}
*Proper commit naming.*

Now that you finally know how to handle git locally it is FINALLY time to take advantage of best git feature. `git remote`! It is collection of commands that will enable you to store a backup copy of your data on *remote* server! If you want to add server connection to your repository use this:

```bash
git remote add name linkToRemote
```

Where:
- name is any word that describes your remote server, origin is used usually as default to main remote repository
- linkToRemote is connection string like this: *https://github.com/Hajto/hajto.github.io.git*

If you added a remote you can push your `commits` to it, like that:

```bash
git push name branch
```

Where:
- name is any name you specified in above command when adding your remote
- branch is different version of project, we will take about it in another post, but default is master

So for example:
```bash
git push origin master
```

Will push all commits from default branch to default remote repository.
