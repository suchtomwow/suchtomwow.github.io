---
layout: post
title: This Is A Static Blog
published: true
date: 2015-02-13 20:52:16 -0600
author: Thomas Carey
---

**Disclaimer:** <span style="font-size:9">Wordpress is a wonderful content management system. Wordpress sites account for a [metric](http://w3techs.com/technologies/overview/content_management/all) [butt-ton](http://trends.builtwith.com/cms) of traffic on the Internet and are responsible for giving millions of people a voice in a very loud world. It is a terrific solution for people who just want to get a blog or website up and running very quickly and easily and don't have time or expertise to configure a static site. Automattic, the company behind Wordpress, is a terrific company and one I've looked at over the years as a shining example of how a company should be run.</span>

### Static Blogs

This site is built using Jekyll, a static blogging engine. Unlike Wordpress or other blogging engines of that ilk, which generate an entire website from scratch based on a database each time the page is visited, a static blogging engine only generates the site anew whenever the site itself changes. 

### For Example
Lets say John blogs at johnawesomebloggreatjob.com. Since the dawn of the blog, he has written quite a few posts and amassed an audience of [literally dozens](../images/dozens_of_us_arrested_development.gif). John's blogging platform of choice is Wordpress, which means that whenever someone visits his site, the Wordpress engine living at johnsawesomebloggreatjob.com must do a database lookup to get all of his posts, build the HTML by looping through the list of posts, and then serve the HTML to the visitor. 

While not a problem most days, this became a problem when one day John wrote a particularly interesting blog post on the perils of basket weaving. Basket Weavers United picked up the article, and suddenly John's normal audience of dozens transformed into hundreds of angry basket weavers. As John's readership grew, so did the time it took for his website to serve out pages to visitors, until finally the website went down altogether. It turns out that while johnawesomebloggreatjob.com handled dozens of visitors everyday just fine, it choked as soon as that number grew to hundreds due to all the database lookups and processing it was having to do each time someone visited.

This is precisely the problem that static blogs solve. Instead of generating the site from scratch each time someone visits the site, the site generates only when the site itself is changed - mainly when a new blog post is published. So when a website grows from dozens to hundreds of visitors, the impact is the same - just serve out the HTML that's already been generated. Obviously scalability and other concerns still exist with static blogs, but at least the issue of database lookups and processing time have been optimized out. 

Even though most websites (including this one) will never reach the level of traffic that Daring Fireball or Hacker News see in one minute, the programmer in me can sleep well tonight knowing that my blog is not wasting precious CPU cycles.

### Other Benefits
Another great benefit to using Jekyll is its support for writing posts in [Markdown](http://daringfireball.net/projects/markdown). Of course sites like Wordpress offer this capability, but Jekyll offers it out of the box, by default, dead simple. The benefit to writing in Markdown is of course A) its expressive syntax, and B) its portability. Since I'm writing posts in plain text files with non-complicated syntax in a markup language that is very widely supported, if I ever decide to change blogging engines, it's as easy as moving text files around. I have complete control over what happens with the content I've created. With engines like Wordpress, posts are stored in a database which could become corrupted, or you may not know how to query it to get the posts out, or they could be written in some weird proprietary format. The point is, who knows?

<!--[^1]: Obviously, any website worth its salt would be able to withstand hundreds of visitors.-->