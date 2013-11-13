---
title: Posting things
layout: post
description: How do I post stuff using Jekyll and Github
---

Again, super-simple. Just create a new file, in markdown format with the date and title in the filename. Drop it in the _posts folder and Jekyll and Github take care of everything else.

##Read, copy, learn
Seriously, just look at the posts already in that folder, they create the output you see. This one is called 2013-03-18-writing-posts.md

Note the stuff at the top. YAML Front Matter (It's just a name, don't sweat it), it's where you can give your posts some smarts. You can define the page title, set a description (this is used for the article meta decsription, but you can also use it in article lists.), enable comments on the page, etc.

layout: post is the default used to render your blog posts. These are different from 'pages' which, on this site, are used for things like the license page and the archive page. Pages don't need article navigation, or comments, posts do.

###I don't want comments.
That's not a problem. If that's only for selected posts, just remove the 'comments: enabled' string from the post in question.

If you never want comments, it's better to remove the comment code block from _layouts/post.html instead and save some code. You can stop bothering with the 'comments: enabled' string at that point also.

##I don't like your layouts
As is your prerogative. Simply edit the layout files in the _layouts folder, pages or posts for the specific section, default.html for everything else.
