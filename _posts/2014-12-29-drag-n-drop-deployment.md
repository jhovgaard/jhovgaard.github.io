--- 
layout: post
title: "Drag n' drop deployment on IIS"
author: "Jonas Hovgaard"
comments: true
description: "Imaging if you could just drag n' drop a folder from your desktop machine in to IIS. Servant.io enables that."
---
Hi guys!

Do you know that special feeling some features can give you? Things that only can be described as neat. I think we just made one of them on <a href="https://www.servant.io" target="_blank">Servant</a>. Check out this GIF:

<a href="/postfiles/dragdropdeploy.gif" target="_blank"><img src="/postfiles/dragdropdeploy.gif" alt="" class="maxwidth" /></a>

Yeah, I just used drag and drop to deploy a website to my IIS. I don't wanna brag but I really think **it's awesome!**

Behind the scene we use the browser to pack and compress your files into one zip archive. Then we upload this zip to a hidden URL. The zip is only stored in memory and it's automatically wiped after 5 minutes. Now we tell your Servant Client (meaning your web server) to download the zip, extract it to the specific site (into a new folder) and change the site's disk path to the new location.

By deploying to a new folder we also remove the seconds of downtime you normally experience when using the same-folder-strategy. The new folder name will include a timestamp.

We will extend this deployment feature with history, auto warm-up and error detection with automatic roll-back (no ETA yet).

**What do you think? Cool and useful or a complete waste of time? [Leave a comment!]({{ page.url }}#comments)**

