---
comments: true
date: 2009-01-08 10:58:18
layout: post
slug: unmount-this-drive-please
title: Unmount this drive... Please?
wordpress_id: 96
categories:
- Windows
tags:
- Windows
---

One thing I dislike in my current Vista installation is how it behaves while trying to unmount USB harddrive. I have a 320 gig external drive, which serves as a storage for my backups, torrents and videos. Also I store and run different virtual machines from it.

This morning I was about to go to the office. I shut down [uTorrent](http://www.utorrent.com/) and clicked "Safely remove" on my drive. "This disk cannot be removed right now. I don't know who uses it, but someone surely is." was the response. I tried again. The same result. Hmm, maybe some program or explorer window is indeed hanging around. I double-checked all icons on a taskbar, then running processes in Task Manager. Nothing.



"What's wrong with you?", I said to the laptop and tried once again. "Now you can safely remove the hardware". Oh, thank you so much! Why couldn't you do it the first time I asked you? (And this happens to me on a regular basis, daily. Sometimes, two or three times a day).

As a tech guy I can guess what's happening. uTorrent opens a lot of files and it probably takes some time to close them and flush all buffers to the drive (which is not very fast, it is external tiny box). Ok, no problem with that. But couldn't it be done synchronously? I think, Windows can easily recognize situations when some program has open file handles on the drive, or just some file buffers need to be flushed. In the latter case it could show a popup "Removing the drive, please wait..." and do its work. That would leave no space for speculations.

In the other case (when a program uses a disk): is it too difficult to point at the culprit? Some installers can do this. And I am pretty sure there is a 3rd party software that does this. This is pure usability issue. I hope they'll take care of it in Windows 7.
