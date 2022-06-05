---
id: 344
title: 手机端网页的一些开发常见问题
date: '2013-09-10T23:41:03+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=344'
permalink: /2013/09/some-solutions-for-mobile-websites-development
categories:
    - 'HTML&amp;CSS'
    - 教程
---

Two years ago [Jen Simmons](http://jensimmons.com/) ([@jensimmons](https://twitter.com/jensimmons)) and I made a site called [WTF Mobile Web](http://wtfmobileweb.com/), a Tumblr which highlights the frustrations mobile web users regularly experience. While the site is a bit cheeky, the goal isn’t to cut anybody down, but rather demonstrate the challenges web creators are faced with when delivering their experiences to the plethora of [desknots](http://globalmoxie.com/blog/desknots.shtml) now accessing the Web.

So without further ado, here’s two years of the most common mobile web problems and what you can do about them.

- [Denying Access](http://bradfrost.com/blog/post/mobile-web-problems/#access)
    - [Coming soon!](http://bradfrost.com/blog/post/mobile-web-problems/#soon)
    - [Screen size](http://bradfrost.com/blog/post/mobile-web-problems/#size)
    - [Browser support](http://bradfrost.com/blog/post/mobile-web-problems/#browser)
    - [Flash](http://bradfrost.com/blog/post/mobile-web-problems/#flash)
    - [DRM](http://bradfrost.com/blog/post/mobile-web-problems/#drm)
    - [And more!](http://bradfrost.com/blog/post/mobile-web-problems/#access-more)
- [URL Redirects](http://bradfrost.com/blog/post/mobile-web-problems/#url)
    - [m. Links on Desktop](http://bradfrost.com/blog/post/mobile-web-problems/#mdot)
    - [Redirect to Homepage](http://bradfrost.com/blog/post/mobile-web-problems/#homepage)
- [Performance](http://bradfrost.com/blog/post/mobile-web-problems/#performance)
- [Making contextual assumptions](http://bradfrost.com/blog/post/mobile-web-problems/#context)
- [App Doorslams](http://bradfrost.com/blog/post/mobile-web-problems/#app)
- [Confusing Sites and Apps](http://bradfrost.com/blog/post/mobile-web-problems/#sites-apps)
- [Design](http://bradfrost.com/blog/post/mobile-web-problems/#design)
    - [Fixed Positioning](http://bradfrost.com/blog/post/mobile-web-problems/#fixed)
    - [Overlays](http://bradfrost.com/blog/post/mobile-web-problems/#overlays)
    - [And More](http://bradfrost.com/blog/post/mobile-web-problems/#design-more)

## denying access

<div class="wp-block-image"><figure class="aligncenter">![Mobile web users denied an experience](http://bradfrost.com/wp-content/uploads/2013/06/denied.jpg)</figure></div>### the problem

Being denied access to an experience is easily the biggest problem mobile web users face. They come to websites on their mobile devices looking for information, looking to solve problems, looking to complete tasks, looking for answers. And they don’t get it. That’s a huge issue.

There are many flavors of denying access to mobile users:

#### coming soon!

<div class="wp-block-image"><figure class="aligncenter">![Mobile experience coming soon!](http://bradfrost.com/wp-content/uploads/2013/06/coming-soon.jpg)</figure></div>  
“Mobile version coming soon!” “Almost there!” and other nonsense phrases litter these experiences. Offering these empty words doesn’t do much to help a user who is unable to achieve their immediate goals.

#### screen size

<div class="wp-block-image"><figure class="aligncenter">![Users blocked from a web experience because their screen is too small](http://bradfrost.com/wp-content/uploads/2013/06/screen-size.jpg)</figure></div><div class="wp-block-image"><figure class="aligncenter">![PC Mag smartphone version is currently designed for use in portrait mode ](http://bradfrost.com/wp-content/uploads/2013/06/pc-mag.png)</figure></div>Oh you have a small screen? Too bad! Denying users an experience because of the size of their screen or the orientation in which they hold their device is a bizarrely bad idea.

#### browser support

<div class="wp-block-image"><figure class="aligncenter">![Looks like you're using an unsupported browser, even though you're on a perfectly capable Android browser](http://bradfrost.com/wp-content/uploads/2013/06/fastco-unsupported.png)</figure></div>“Looks like you’re using an unsupported browser, even though you’re on a perfectly capable Android browser.” In an age of UA sniffing gone wild, mobile browsers often catch the short end of the stick.

#### flash

<div class="wp-block-image"><figure class="aligncenter">![Flash sites unsupported on mobile browsers](http://bradfrost.com/wp-content/uploads/2013/06/flash.jpg)</figure></div>  
Yes, Flash objects are still causing issues for mobile browsers, especially with regards to video content.

#### digital rights management

<div class="wp-block-image"><figure class="aligncenter">![Youtube blames its users for denying accessing videos containing copyrighted material](http://bradfrost.com/wp-content/uploads/2013/06/youtube-fail.png)</figure></div>DRM is still a thing. I especially love how YouTube *blames their users* for the company’s decision to not make videos containing copyrighted songs available on mobile devices.

#### a whole bunch more

[Cookies](http://wtfmobileweb.com/post/53927659665/i-have-enabled-cookies-but-god-knows-why-you), [unsupported formats](http://wtfmobileweb.com/page/4), [advanced functionality](http://wtfmobileweb.com/post/49783976268/as-soon-as-i-entered-lhr-as-my-departure-airport), and a whole lot more contribute to mobile web users’ collective frustration.

### the solution

The solution is to **achieve [content parity](http://bradfrost.com/blog/mobile/content-parity/)**. **Give people what they want regardless of how they access the Web**. Take [Stephen Hay’s advice](http://www.the-haystack.com/2012/07/09/great-works-of-fiction-presents-the-mobile-context/):

> Just make quality, relevant content with appropriate tasks, and offer all of these to all users, \[…\] then make it easy for the user to decide what it is they want to do.

- **Don’t make content assumptions based on device or context**. Don’t assume that mobile users only want [“on the go”](http://karenmcgrane.com/2012/07/10/mobile-local/) content. Mobile users will do anything and everything a desktop user will do, [provided it’s presented in a usable way](https://twitter.com/brad_frost/status/176697511761805312).
- **Desktop-only content is better than no content at all**—Don’t have all your content optimized for mobile? That’s OK. No need to apologize for it, and certainly no need to deprive users your content to tell them that the content isn’t mobile-optimized. Users can pinch-and-zoom or awkwardly scroll back and forth, and while that’s not as good as having an optimized experience, you’ll end up with less complaints so long as users can get what they came to the site to do.
- **Ditch Flash**—This shouldn’t be too shocking, but you should probably be weening yourself off of Flash.
- **Don’t penalize users for screen size, device capabilities, settings, resolution, etc**—Use sound [progressive enhancement](http://easy-readers.net/books/adaptive-web-design/) techniques to support as many environments as possible while still [optimizing](http://bradfrost.com/blog/mobile/support-vs-optimization/) for the most capable.
- **Plant a flag in the sand.** It’s understandable to not have all of your content mobile optimized right this second. Things take time. But it’s important to have a [long-term strategy](http://www.abookapart.com/products/content-strategy-for-mobile) for making all of your content accessible on the whole slew of present and future web-enabled gizmos.

### further reading

- [Content Parity](http://bradfrost.com/blog/mobile/content-parity/)
- [Great Works of Fiction Presents: The Mobile Context](http://www.the-haystack.com/2012/07/09/great-works-of-fiction-presents-the-mobile-context/)
- [Mobile &gt; Local](http://karenmcgrane.com/2012/07/10/mobile-local/)
- [Content Strategy for Mobile](http://www.abookapart.com/products/content-strategy-for-mobile)

## url redirect fails

<div class="wp-block-image"><figure class="aligncenter">![CBS Mobile site viewed on desktop](http://bradfrost.com/wp-content/uploads/2013/06/cbs-redirect-700x456.png)</figure></div>### the problem

Until recently, the most common mobile web strategy was to create a separate mobile-only website (or m. site). Having two separate sites means having to point the right device classes to the right experience. In theory, that sounds manageable. In practice, however, it’s a nightmare that barely anyone gets right.

URL redirect fails come in a few different awful flavors:

#### m. links on desktop

<div class="wp-block-image"><figure class="aligncenter">![mobile Twitter site on desktop](http://bradfrost.com/wp-content/uploads/2013/06/redirect-twitter-700x384.png)</figure></div>This happens all the time. Step 1: Someone shares a URL from their mobile device. Step 2: Someone sees that link and clicks on it. Step 3: they see a mobile site rendered on their desktop browser. This obviously shouldn’t happen, but even massive sites like Wikipedia and Youtube are guilty of this.

#### redirect to homepage

<div class="wp-block-image"><figure class="aligncenter">[![server_attention_span](http://bradfrost.com/wp-content/uploads/2013/06/server_attention_span.png)](http://xkcd.com/869/)</figure></div><figure><iframe allowfullscreen="" height="480" loading="lazy" src="http://www.youtube.com/embed/7vN49n9RcNM" width="640"></iframe></figure>It’s awful when you’re trying to find some crucial information only to be redirected to the mobile site’s homepage.

### the solution

There are a few solutions to URL redirect issues.

- **Maintain a single codebase.** One of the more invisible advantages of responsive design is that you don’t have to jump through all these URL-redirect nightmare hoops.
- **Canonical URLs.** Sites like Buzzfeed serve a separate mobile template, but the URL stays consistent (so no m. preceding the URL). This ensures that all that link juice stays under the same roof.
- **Initiate a future-friendly device strategy.** Yes, it’s currently possible to keep up with the flood of web-enabled gizmos in a device database. But these solutions are quickly becoming untenable. Start to plan for a more maintainable web strategy that doesn’t involve updating a database every time Samsung craps out a new device (which happens about every 30 seconds).

### further reading

- [Common Mistakes in Smartphone Sites (Faulty Redirects)](https://developers.google.com/webmasters/smartphone-sites/common-mistakes#redirects)
- [W3C Mobile Web Best Practices: Thematic Consistency of Resource Identified by a URI](http://www.w3.org/TR/mobile-bp/#tc)
- [URL Design](http://warpspire.com/posts/url-design/)

## performance

<div class="wp-block-image"><figure class="aligncenter">[![ronin](http://bradfrost.com/wp-content/uploads/2013/06/ronin-700x503.jpg)](http://bradfrost.com/blog/post/mobile-web-problems/)</figure></div>### the problem

Performance matters. A lot. But it’s often neglected. One of the main reasons I feel performance is neglected is because *performance is invisible*. That’s why there aren’t very many performance-related screenshots on WTF Mobile Web. But we’ve all felt the pain of slow performance on our mobile browsers. And huge websites not only test our patience, they’re also extremely [expensive](http://mobiforge.com/designing/blog/performance-money-part-1-end-users-wallet) for users on data plans.

### the solution

I’ve previously written about how to [prioritize performance for responsive designs](http://bradfrost.com/blog/post/prioritizing-performance-in-responsive-design/), so in a nutshell we should treat [performance as design](http://bradfrost.com/blog/post/performance-as-design/), [set performance budgets](http://timkadlec.com/2013/01/setting-a-performance-budget/) (and stick to them), lazy/conditionally load content, [test often](http://mobitest.akamai.com/m/index.cgi) and follow sound [performance best practices](https://developers.google.com/speed/docs/best-practices/rules_intro).

### further reading

- [Performance As Design](http://bradfrost.com/blog/post/performance-as-design/)
- [Prioritizing Performance in Responsive Design](http://bradfrost.com/blog/post/prioritizing-performance-in-responsive-design/)
- [Performance is money, part 1: the end-user’s wallet](http://mobiforge.com/designing/blog/performance-money-part-1-end-users-wallet)
- [Improving UX Through Front-End Performance](http://alistapart.com/article/improving-ux-through-front-end-performance)
- [Setting a Performance Budget](http://timkadlec.com/2013/01/setting-a-performance-budget/)
- [What are Responsive Websites made of?](http://www.guypo.com/mobile/what-are-responsive-websites-made-of/)

## making contextual assumptions

<div class="wp-block-image"><figure class="aligncenter">![Add to Homescreen showing up in the wrong place](http://bradfrost.com/wp-content/uploads/2013/06/context-add-to-home-700x526.jpg)</figure></div>### the problem

Bad things can happen when we make assumptions about users’ browser, screen size, environment, settings, orientation, etc. Web experiences are extremely fluid and are viewed in many different settings and scenarios.

### the solution

Don’t make assumptions. Avoid instructions like “tap the menu button in the upper right corner of your browser” because you simply don’t know what browser or app the user is visiting the website from. Yes, it’s frustrating to accept that we can’t control our users’ environment, but once we come to terms with that fact we’re able to create stronger, more agnostic solutions.

## app doorslams

<div class="wp-block-image"><figure class="aligncenter">![Appblock examples](http://bradfrost.com/wp-content/uploads/2013/06/appblock.jpg)</figure></div>### the problem

This problem is extremely obvious. Instead of taking users to the content they’re after, organizations slap an interstitial ad or popup for their native app, hoping to increase their app downloads and justify the hefty price they paid to make said native app.

There are many issues with this strategy. First of all, it’s annoying as hell. But going the app route is tremendously inefficient. With the Web, there are simply two steps:

1. Select link
2. View content

Whereas going the app route goes something like this:

1. Select link
2. Select interstitial to download app
3. Open app store app
4. Select ‘Install app’
5. Enter password
6. Wait for app to install
7. Open app
8. Learn new interface, look for search bar
9. Search for the original content from link
10. Select link from search results
11. View content

I’ve begun this process before only to eventually forget what I was trying to check out in the first place.

### the solution

Don’t do this. [Google will punish you](https://developers.google.com/webmasters/smartphone-sites/common-mistakes#app). Instead, think of more subtle ways to promote your app. Less intrusive banners across the top or bottom don’t hit users over the head with marketing and can be easily skimmed over. For iOS, consider looking into [Smart Banners](http://functionsource.com/post/smart-app-banners), which provide a more native way of handling app promotion.

### further reading

- [I Don’t Want Your Fucking App](http://idontwantyourfuckingapp.tumblr.com/)
- [Lots of appblock examples on WTF Mobile Web](http://wtfmobileweb.com/tagged/appblock)
- [The User’s Choice?](https://medium.com/tech-talk/25a7fde0f19b)
- [Linking Mobile Web &amp; Native App Experiences](http://www.lukew.com/ff/entry.asp?1654)
- [Common mistakes in smartphone sites (App download interstitials)](https://developers.google.com/webmasters/smartphone-sites/common-mistakes#app)

## confusing sites and apps

<div class="wp-block-image"><figure class="aligncenter">![Mobile web site spinners](http://bradfrost.com/wp-content/uploads/2013/06/spinners-700x526.jpg)</figure></div>### the problem

I have a problem with app lust. App lust is when organizations salivate over apps, so when they finally have a chance to do something for mobile, they immediately think “WE GOTTA HAVE AN APP!” They typically look something like this guy:

<div class="wp-block-image"><figure class="aligncenter">![Guy suffering from app lust](http://bradfrost.com/wp-content/uploads/2013/06/app-guy.jpg)</figure></div>The result of this thinking applied to the Web is often disastrous. I feel like I see more spinners on my phone’s browser than in [this music video](http://www.youtube.com/watch?v=GIiVaACweDc).

The best example of a site trying to be an app is Fast Company’s [staggeringly bizarre mobile web experience](http://wtfmobileweb.com/post/36813293003/its-staggering-to-see-the-amount-of-steps). Enjoy:

All *that* just to put some words on a page.

### the solution

The Web isn’t a native app, and just because a Web experience can be viewed on mobile devices doesn’t mean that it needs to be app-like. Embrace the Web for the unique medium it is instead of trying to create a poor man’s native experience.

### further reading

- [Native Vs Web Is Total Bullshit](http://bradfrost.com/blog/post/native-vs-web-is-total-bullshit/)
- [Of Sites and Apps](http://tripleodeon.com/2011/09/of-sites-and-apps/)
- [On Single Page Apps](http://christianheilmann.com/2011/12/28/on-single-page-apps/)

## design

<div class="wp-block-image"><figure class="aligncenter">![Crazy mobile web overlay](http://bradfrost.com/wp-content/uploads/2013/06/wtf-700x933.png)</figure></div>### the problem

Lots of design decisions can influence the usability of a mobile web experience. Here are just a few examples:

#### fixed positioning

<div class="wp-block-image"><figure class="aligncenter">![Fixed position fails on mobile browsers](http://bradfrost.com/wp-content/uploads/2013/06/fixed-700x552.jpg)</figure></div>Fixed positioning can [wreak havoc on mobile browsers](http://bradfrost.com/blog/mobile/fixed-position/). Support is shaky, even on the latest versions of the most popular mobile platforms. But space is already a premium on small mobile screens, and by fixing elements to the page you’re losing valuable real estate that should be reserved for core content. It’s also worth noting that apps and browsers add their own (often multi-level) chrome to the experience, eating away at your content’s real estate.

#### overlays

<div class="wp-block-image"><figure class="aligncenter">![Overlays causing problems on mobile browsers](http://bradfrost.com/wp-content/uploads/2013/06/overlays-700x595.jpg)</figure></div>Overlays are annoying. They also tend to have positioning issues, which cause for some very undesirable results. Do you really want to risk users not being able to close an overlay and get to your real content?

#### and more

[Text legibility](http://wtfmobileweb.com/post/32329747090/a-comment-nested-within-a-comment-nested-within-a), [indecipherable images](http://wtfmobileweb.com/post/21027710103/the-graphic-is-reduced-in-size-for-mobile-by-being), and just [straight up gross design](http://wtfmobileweb.com/post/18406495668/mobile-web-design-ladies-and-gentlemen)all impact the effectiveness of a design.

### the solution

- **Avoid fixed positioning** for a cleaner, more stable experience.
- **Avoid popups and overlays**. Your users will thank you for it. Not only are they [bullshit](http://bradfrost.com/blog/post/death-to-bullshit-at-creative-mornings/), but they also introduce potential usability issues where users are unable to close the overlays.
- **Simple is usable.** Think twice before adding bells and whistles to your Web experiences. I’m not saying there isn’t room to make things beautiful, rich and complex. But all of these examples demonstrate that the effectiveness of a mobile Web experience has so much to do with basic accessibility and usability than it does about bells, whistles and purely aesthetic decisions.

### further reading

- [Fixed Positioning in Mobile Browsers](http://bradfrost.com/blog/mobile/fixed-position/)
- [Death to Bullshit](http://bradfrost.com/blog/post/death-to-bullshit-at-creative-mornings/)

## less wtf, more ftw

This post has been a long time coming. I started it in spring of 2012. It took [Google publishing their thoughts](https://developers.google.com/webmasters/smartphone-sites/common-mistakes) to kick me into high gear and finish it.

The good news is that we’re seeing progress. Of course there are still problems and there always will be, but combing through 2 years of posts, we’re seeing less hideous mainstream mobile web experiences.

Again, I want to stress again that the point of WTF Mobile Web isn’t to be a jerk and make fun of mobile websites, but rather to highlight the problems we’re all facing as creators for this new medium. We need to know what problems we’re facing in order to get better.

It’s been great watching mobile web design transform from a complete afterthought to the center of everyone’s Web strategy. That’s nothing but a good thing. Here’s to progress!