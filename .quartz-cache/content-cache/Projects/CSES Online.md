---
title: CSES Online
description: 
permalink: 
aliases: 
tags: 
draft: false
---
# Scope + Design

A while ago I made a not-very-usable version clone of leetcode with the CSES problemset. The core issue I had with this was that the editor used CodeMirror, which was quite difficult to configure. It was written in TypeScript, and I'm just not that interested in TypeScript so it wasn't very fun at all.

I spent a while trying to find similar projects to what I want online, but it seemed like all the open source ones were actually youtube tutorials trying to get clicks. The ["best"](https://github.com/burakorkmez/leetcode-clone-youtube) one I saw only had a problem list, a login screen and that's about it. No text editor, no problem judging or anything. Just a wrapper around firebase's authentication, then they manage to get 200 stars on Github somehow.

## Goals of the project
I think it's important to define what I actually need before I start anything. On my last attempt with this, I got sidetracked. If I have a fairly strict TODO list then I'll probably wrestle through it happier.

1. I want a project to learn functional programming that would be useful to me.
2. Basic functions/pages
	- [ ] Home page is the list of problems
	- [ ] Problem statement page
		- [ ] Left side - problem list, previous submissions, and ability to view a specific submission
		- [ ] Right side - Code editor with language picker at the top, and a separate pane below it with run + submit at the top, and you can see input / output / run results here too.
		- [ ] If a user runs or submits and they aren't logged in, it prompts them to log in
		- [ ] If a user runs, the bottom panel should show their run result with their testcases. I don't think we should have accept/fails here though.
		- [ ] If a user submits, the left side should open up the submission for the initial request. We might want to make this submission component flexible enough for it to open as it's own page and you can share it.
	- [ ] Support C++ and Python in submissions and in the code editor.
	- [ ] User can log in. Just support this with firebase or something since it seems quite easy
	- [ ] User profiles. Displays the list of recent submissions you've made and an activity heatmap. You can publicly find these
3. Extras
	1. Export everything you've worked on. Since CSES is nicely defined by sections, the output would look quite nice
	2. Support more languages
		- [ ] Haskell
		- [ ] Ocaml
		- [ ] C#
		- [ ] Java
	3. Support vim

If I start listing more things than this it starts getting way too much. My other ideas are leaderboards, comments + solutions, and code template support. Maybe templates are quite doable though?

## Architecture
![[Pasted image 20241010151256.png]]
![[architecture.drawio]]
1. Backend server - PureScript
2. Frontend server - PureScript
3. Monaco editor https://github.com/maxhillaert/purescript-monaco
	1. One alternative is [judge0](https://github.com/judge0/ide), but I don't think there are PureScript bindings for this yet
4. Firebase for authentication
5. Judge0
	1. This seems like it might be quite a side quest to do. Judge0 doesn't support PureScript. 
	2. My options are I either add PureScript bindings, or I use a docker container running Judge0 and send requests to it. *In theory*, Judge0 is the heaviest component of the entire system
6. PureScript react bindings https://github.com/purescript-contrib/purescript-react
7. Postgresql for any storage things. For now, we can probably rely on the database's caching. 

I'm quite tempted to containerize everything and add a flag that makes firebase authentication optional. Conveniently, Judge0 will already be in a docker container, but I would want to put the web server, backend, and postgresql into their own containers.

### Judge0
Best approach here is going to be a Judge0 Docker container. I don't really need the premium version, Judge0 CE is fine. The way you input files is a bit weird though... I'm not sure how to do multiple inputs...

https://hub.docker.com/r/judge0/judge0
https://ce.judge0.com/

Hmm, I thought DOMJudge was an alternative, but looking into it, I don't think it is... Seems its engine is very baked into it and you can't really separate it.

### Colourschemes
I (personally) would really like it if this supported multiple colourschemes. This might be quite a big pain to configure, so I'd have to see if I can put VS code theme plugins into config for Monaco. The more important thing is that I define the available colours, from the start, in terms of colourschemes. If I support it early, it's way easier than trying to wrangle themes into every single page.

### Endpoints
I tried out HATEOS to make them actually RESTful. I really don't know if I can be bothered this time or if I should simply accept pure json instead. It would be a lot easier, and I'm not really intending it to be publicly useable or anything. So I'd rather just go for plain json requests. It also improves performance for encoding this significantly better.

The api can be prefixed with `/api/v1/` on everything.

1. Authentication
	1. POST Sign up
	2. POST Log in
2. Problems
	1. POST - submit the problem 
	2. GET - Problem statement
	3. GET - Previous runs 
	4. GET - specific submission. This one is special because I except the UI is going to poll it
3. Users
	1. GET user submissions - returns the list of submission IDs
	2. GET user activity - returns the activity they have

### Tables
We want to contain this as a base on every table

1. UUID
2. Version
3. Created timestamp
4. Last updated timestamp

I haven't used UUIDs before, and I think it'd be a nice experience to have a shot at those.

I won't include these in this list here. I also think for user settings, like theme, default editor mode, default language, we should store this information in cookies. We will maybe need to add a user settings table in the future, but I'm not sure.


1. users
	1. username - indexed for lookups
	2. Whatever firebase demands we need to store for authentication
2. problems
	1. description - Problem description. Probably HTML encoded. I don't think I'll bother supporting anything else
	2. source - Problem source - enum \[cses\]
	3. link - Problem link - hyper link to the problem
	4. remote_id - Problem ID on their website - probably useful
	5. directory - Problem testcases location in our files
3. submissions
	1. linked_user_id - indexed for lookups
	2. everything judge0 spits out. I'm not sure what the list is at this point, but {language, runtime, }
