---
title: Hello world!
description: 
permalink: 
aliases: 
tags: 
draft: true
---
# Scope + Design

A while ago I made a not-very-usable version clone of leetcode with the CSES problemset. The core issue I had with this was that the editor used CodeMirror, which was quite difficult to configure. It was written in TypeScript, and I'm just not that interested in TypeScript so it wasn't very fun at all.

I spent a while trying to find similar projects to what I want online, but it seemed like all the open source ones were actually youtube tutorials trying to get clicks. The ["best"](https://github.com/burakorkmez/leetcode-clone-youtube) one I saw only had a problem list, a login screen and that's about it. No text editor, no problem judging or anything. Just a wrapper around firebase's authentication, then they manage to get 200 stars on Github somehow.

### Goals of the project
I think it's important to define what I actually need before I start anything. On my last attempt with this, I got sidetracked. If I have a fairly strict TODO list then I'll probably wrestle through it happier.

1. I want a project to learn functional programming that would be useful to me.
2. Basic functions
	- [ ] List all the problems
	- [ ] Read the problem statement
	- [ ] If you try to run or submit without logged in, bring out a popup that the user isn't logged in
	- [ ] Submit the problem - show runtime and memory usage. If it failed say runtime error / compile error / etc. Probably the judge library I use will be able to handle this
	- [ ] Support C++ and Python in submissions and in the code editor.
	- [ ] User can log in. Just support this with firebase or something since it seems quite easy
3. Extras
	1. Export everything you've worked on. Since CSES is nicely defined by sections, the output would look quite nice
	2. Support more languages
		- [ ] Haskell
		- [ ] Ocaml
		- [ ] C#
		- [ ] k

If I start listing more things than this it starts getting way too much.