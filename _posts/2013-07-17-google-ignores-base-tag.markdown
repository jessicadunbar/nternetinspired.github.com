---
layout: post
title: "Google Ignores base Meta Tag"
date: 2013-07-17 14:48
comments: true
categories: html, google, "base tag"
---

Last week while doing routine maintenance within Google Webmaster Tools, I noticed a spike in 404 errors on one of my client websites. This was really weird to me, because not much had changed on the site. The only major change was upgrading from Joomla 1.5 to 2.5 and creating a custom component for them, but that happend late last year. Having 404's show up do to that change didn't seem right to me.

I'm not sure when Google added this feature, but it's a nice addition to their error reporting. When you have 404 errors on your website, they now notify you of when they first encountered it as well as what page contained the link that sent them to the 404 (the referring page).

<img src="/img/google-linked-from.png" />

So, I inspected those pages that Google said referred them to the 404, but I could not find the broken link anywhere; everything was working as expected. After looking more closely, I saw that the link wasn't actually pointing to the root of the domain, as it was supposed to, it was acting as a relative link on that page. A relative link is one that links to another page using the current page as the starting point. This is accomplished by not including a leading / within the href on your links.

But as I said earlier, the browser was interpreting the links just fine. I couldn't produce the 404 by clicking around. This is because of the presence of a &lt;base href="/" /&gt; tag in the head of the  document. If you have a base tag, all relative links on that page use it as the base to link from, rather than the current URL.

The only conclusion I could come to after reviewing all this is that Google no longer respects the base tag when crawling and parsing your website. I'm not sure why they would do this, because it's a valid tag; it even made it into the [HTML 5 spec](http://dev.w3.org/html5/html-author/#the-base-element).

So I should ask, have any of you experienced this? If so (or if you think I'm just crazy) sound off in the comments below.
