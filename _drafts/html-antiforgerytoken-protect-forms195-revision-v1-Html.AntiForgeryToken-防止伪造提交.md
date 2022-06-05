---
id: 196
title: 'Html.AntiForgeryToken 防止伪造提交'
date: '2019-08-16T12:39:29+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/195-revision-v1/'
permalink: '/?p=196'
---

In this tutorial, I am not going to discuss the concept in-depth since they have done such a fantastic job. Instead, I want to show how you can easily incorporate the **Html.AntiForgeryToken** HtmlHelper Method and **\[ValidateAntiForgeryToken\]** Attribute in the sample code from our first meeting:[Introduction to ASP.NET MVC Screencast and Sample Code](http://www.tampadev.org/News/Details/IntroductionASPNETMVCScreencastSourceCode).

This is really a two-step process:

Add the **\[ValidateAntiForgeryToken\]** Attribute to any Post Action Methods.Add the **Html.AntiForgeryToken()** HtmlHelper Method to any forms posting back to the website.

Let’s just do this to the Edit Action Method in the ContactsController in this tutorial.

\[ValidateAntiForgeryToken\] Attribute

The ValidateAntiForgeryToken Attribute in the ASP.NET MVC Framework is an **IAuthorizationFilter** which is guaranteed to run before any other filters. You add it to your post Action Methods as such:

\[AcceptVerbs(HttpVerbs.Post)\]

\[ValidateAntiForgeryToken\]

publicActionResultEdit(Contactcontact)

{

 try

 {

 if (!ModelState.IsValid)

 returnView(contact);

 \_db.Contacts.Attach(contact, true);

 \_db.SubmitChanges();

 returnRedirectToAction(“List”);

 }

 catch

 {

 returnView(contact);

 }

}

The ValidateAntiForgeryToken Attribute does some voodoo magic in the background. It checks to see that the **cookie** and **hidden form field** left by the **Html.AntiForgeryToken()** HtmlHelper essentially exists and match. If they do not exist or match, it throws an **HttpAntiForgeryException**:

<figure class="wp-block-image">![](http://davidhayden.com/photos/HttpAntiForgeryException.jpg)</figure>You can obviously clean that exception up, but the important point is that if the cookie and form field do not exist or match the action method is not executed for security reasons. This is a good thing!

Html.AntiForgeryToken HtmlHelper Method

As mentioned before, we need to add the **Html.AntiForgeryToken** Method to the forms so that a cookie is added on the client and a hidden form field is added to the form itself. This is as simple as:

<figure class="wp-block-image">![](http://davidhayden.com/photos/HtmlAntiForgeryToken.jpg)</figure>You will notice the hidden form field in the HTML source:

<figure class="wp-block-image">![](http://davidhayden.com/photos/CrossSiteRequestForgeryAttack.jpg)</figure>The Html.AntiForgeryToken Method will also add a cookie on your machine matching the same value in the hidden form field. It is the cookie and the hidden form field value that the ValidateAntiForgeryToken Attribute is checking.

Conclusion

Pretty simple way to protect your website against Cross-Site Request Forgery Attacks in the ASP.NET MVC Framework.

You can add this code yourself by downloading the [Tampa MVC Developer Group Screencast and sample code](http://www.tampadev.org/News/Details/IntroductionASPNETMVCScreencastSourceCode) based on our first meeting.