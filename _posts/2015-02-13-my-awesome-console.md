--- 
layout: post
title: "My Awesome Console"
author: "Jonas Hovgaard"
comments: true
description: "This is how i optimized my cmd.exe prompt with Console2, Powershell, Git integration, auto find SLN file and more."
---
I've been using cmd.exe with Console2 for a few years now. I haven't spent much time trying to optimize the experience. Actually the only reason I'm using Console2 is for my copy/paste needs.

Until <a href="http://www.bsthomsen.com" target="_blank">my friend</a> showed me a screenshot of his new Powershell. It had **nice Git integration with colors and auto-complete**. My own console was stupid and colorless. Time to geek out.

<img src="/postfiles/awesomeconsole.gif" alt="" class="banner">

> The steps below requires that you already have Git installed and configured.

##Install Console2
If you haven't already <a href="https://chocolatey.org/" target="_blank">go install Chocolatey</a>. 

Next, install Console2:

    choco install console2
    
You can now run Console2 by hitting `Win Key+R` and enter `console`.

##Install PowerShell Git integration
Posh-git is Git integration/highlight-coloring-stuff for Powershell. Again we use Chocolatey:

    choco install poshgit

It's plug and play. Nothing more to do.

##Configuring Console2
If Console2 is already running please **restart it**. We need the post-git modules imported. Next, go to `Edit -> Settings...`. Select `Tabs`. Select `Powershell` and press the `Move up`button until it's in the top.

Mark the `Use default` checkbox. If you like you can set your `Startup dir` (mine is C:\code).

In the same Settings window now select `Hotkeys`. Find `Paste` and change the hotkey to `CTRL+V`. You can now paste text into the commandline using `CTRL+V`!

##Set up Powershell scripts

I've got two going on. The first is the best.
Powershell scripts is "installed" by referring to psm1 files in your so called $profile. Jump to Powershell and enter `$profile`. It will return something like `C:\Users\jhovgaard\Documents\WindowsPowerShell\Microsoft.PowerShell_profile.ps1`. To include a script simply append a new line to the file like this:

    Import-Module "C:\PATH\TO\YOUR\SCRIPT.PSM1"

### Search and open .sln file in current folder

My script is available here:
https://gist.github.com/jhovgaard/8a91441e621094544a17

Save the file to your machine and add a reference in your $profile.

#### Open new Google Search from Powershell

My script is available here:
https://gist.github.com/jhovgaard/9dad2a49a441a83a5874

Save the file to your machine and add a reference in your $profile.

**Please notice all changes made to $profile requires restarting your console.**

Last, here's a nice wallpaper for your screens!

<a href="http://www.hdwallpapers.in/walls/blurry_abstract-wide.jpg" target="_blank">http://www.hdwallpapers.in/walls/blurry_abstract-wide.jpg</a>
