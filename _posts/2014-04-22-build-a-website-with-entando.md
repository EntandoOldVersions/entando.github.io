---
title: Build a website with Entando
permalink: /build-a-website-with-entando
layout: post
---

# Build a website with Entando

## Preparations

### Intro
Hello, William Ghelfi here. 

I serve as R&D Director at Entando, and you can find me on [GitHub](http://github.com/trumbitta) or [Twitter](http://twitter.com/trumbitta).

So you heard about Entando and thought:

> Finally an enterprise portal platform which is at the same time powerful, extensible, and not \[Liferay|Oracle Portal|WebSphere\]-difficult to use!

But then you looked left, and then you looked right, and then you looked around and thought:

> Right. How am I supposed to use Entando to get my new shiny portal up and running, **exactly**?

Hey! That's **exactly** the reason why I'm writing this guide for you to enjoy!

### Get this party started

Also known as *prerequisites*.

This journey through Entando is especially tailored for a Mac OS X 10.9 development environment but if you know the moves, you could also follow me from a Linux or Windows machine easily enough.

We will be using Entando 4, which comes with an awesome lot of improvements we'll take advantage of.

Also, let me suggest you not to skim through this guide like a squirrel looking for acorns. Take your time, read carefully, relax, and enjoy the ride.  
There's nothing really difficult on the road ahead.

If you didn't stumble across the mini-guide about setting up a development environment for Entando, this is the perfect time for going through it and [prepare your environment]({% post_url 2014-04-22-entando-base-dev-environment %}).

Aside from that, you will need your favorite text editor and / or IDE.

I use [Sublime Text](http://www.sublimetext.com/) and [Eclipse](http://eclipse.org/), while the lead developer of Entando - [Eugenio Santoboni](http://github.com/eugeniosant) - prefers [Netbeans](http://netbeans.org/).

## From the archetype comes life

Here we go. Find a comfortable spot in the filesystem, like:

{% highlight bash %}
mkdir ~/entando/ && cd ~/entando
{% endhighlight %}

And create your new, barebones, portal bundled with the awesome [Bootstrap](http://getbootstrap.com), version 2:

{% highlight bash %}
mvn archetype:generate -Dfilter=entando
{% endhighlight %}

If this is the first time you use Maven, *it's important that you stop reading right now and go read an [intro to Maven](http://maven.apache.org/guides/getting-started/index.html)*.

Maven will ask you the good 'ole questions, to which you'll answer like so:

{% highlight bash %}
Choose archetype:
1: remote -> org.entando.entando:entando-archetype-portal-bootstrap (Twitter Bootstrap Portal Archetype for Entando: an agile, modern and user-centric open source portal-like platform.)
2: remote -> org.entando.entando:entando-archetype-plugin-generic (Generic Plugin Archetype for Entando: an agile, modern and user-centric open source Portal platform.)
3: remote -> org.entando.entando:entando-archetype-portal-generic (Generic Portal Archetype for Entando: an agile, modern and user-centric open source portal-like platform.)
Choose a number or apply filter (format: [groupId:]artifactId, case sensitive contains): :
{% endhighlight %}

Choose 1 or **whatever number points to org.entando.entando:entando-archetype-portal-bootstrap**.  
Then choose version 4.0.0, and go on like so:

{% highlight bash %}
Define value for property 'groupId': : com.mydomain
Define value for property 'artifactId': : myportal
Define value for property 'version': 1.0-SNAPSHOT:
Define value for property 'package': com.mydomain:
Confirm properties configuration:
groupId: com.mydomain
artifactId: myportal
version: 1.0-SNAPSHOT
package: com.mydomain
Y:
{% endhighlight %}

Now enter the root directory of `myportal`:

{% highlight bash %}
cd myportal
{% endhighlight %}

Cool. Let's see if it worked.

1. Start Entando:  
{% highlight bash %}
mvn clean jetty:run
{% endhighlight %}
2. Lean back and relax until you see these ending log lines by Jetty:  
{% highlight bash %}
[INFO] Started Jetty Server
[INFO] Starting scanner at interval of 5 seconds.
{% endhighlight %}

Those two lines mean that Entando is up and running and Jetty is now ready to accept connections.  
That is, point your web browser to [http://localhost:8080/myportal](http://localhost:8080/myportal) and behold the mostly empty placeholder page, stating that **It Worked!**.

So we are now ready to move on to the next checkpoint; but first be sure to stop Jetty:

{% highlight bash %}
CTRL C
{% endhighlight %}

## Decide and compose

`entando-archetype-portal-bootstrap` comes with a handy Page Bundle, some Widget Bundles, and a Content Bundle.

We'll see how to activate and use them in a minute, but before that it's now time to stop and think of what you need for `myportal`.

As you may already know, Entando sports a bunch of key concepts: Content Types, Page Models, Widgets, and so on.

If you're familiar with Entando, you already know how to configure Content Types, manage Pages, put Widgets in place. Since version 3.2, using Bundles you can configure and customize your Entando based application even faster.

Adding a Bundle to `myportal` is as simple as inserting a XML snippet at the very top of the `dependencies` section of the `pom.xml`.

Let me repeat this: you **have to** insert the XML snippets for Bundles **at the very top of the `dependencies` section**, and especially **before any other `dependency` related to Entando**.

Really.

It's **mandatory**.

### Contents

Ok, so you thought about it and are almost sure that Generic Content won't be enough for `myportal` because - hey - everybody likes News!

Open `pom.xml`, locate the very top of the `dependencies` section and insert this XML snippet:

{% highlight xml %}
<dependency>
  <groupId>org.entando.entando.bundles.contents</groupId>
  <artifactId>entando-content-news</artifactId>
  <version>${entando.version}</version>
  <type>war</type>
</dependency>
{% endhighlight %}

### Pages

During its first run, the freshly generated `myportal` has already activated the awesome and flexible `entando-page-bootstrap-hero` Page Model which is loosely based on the famous [Hero Unit example from Twitter Bootstrap](http://getbootstrap.com/2.3.2/examples/hero.html).

Sure enough, you could argue that adding a slightly different Page Model with its default Widgets would be useful.

So add the proper XML snippet to the `pom.xml`, maybe just near `entando-page-bootstrap-hero` for increased order, like this:

{% highlight xml %}
<!--
    This one is already in the pom.xml and it's here in this XML snippet
    just to show you a good place where to put the next one
-->
<dependency>
  <groupId>org.entando.entando.bundles.pages</groupId>
  <artifactId>entando-page-bootstrap-hero</artifactId>
  <version>${entando.version}</version>
  <type>war</type>
</dependency>

<!--
    This is the NEW Page Bundle, and the ONLY one you actually are
    supposed to add to myportal right now
-->
<dependency>
  <groupId>org.entando.entando.bundles.pages</groupId>
  <artifactId>entando-page-2columns-left</artifactId>
  <version>${entando.version}</version>
  <type>war</type>
</dependency>
{% endhighlight %}

### Widgets and more

Still with me?

Fine, Widget Bundles come as dependencies of Page Bundles so let's say we don't need any added Widget nor Misc Bundle.

### Connecting the dots

Time to finalize the work done so far, and activate the newly added Bundles.

... that is: just start Entando with Jetty!

Remember?

{% highlight bash %}
mvn clean jetty:run
{% endhighlight %}

Well done! From now on, you will proceed using and customizing `myportal` until it's exactly how you want it.

Ok, actually it will be exactly how **I** want it. But if everything goes as expected, at the end of this journey you will be able to get your own `myportal` up and running and to get it how **you** want it **for real**.

> **Pro Tip**
>
> I wrote a bunch of useful Bash aliases, which you can find here:
> [https://gist.github.com/trumbitta/2312873](https://gist.github.com/trumbitta/2312873)
>
> I, for one, use `entando-start` all the time, and with tab-completion it feels like heaven.

## Structure

### Home sweet home (Alabama)

First of all, sign in to enter the Administration Area.

There's a fairly big green button just front and center in the placeholder page your browser is currently showing.  
It's there to ease the very first access to the Administration Area, and soon it will be gone, so use it while there is time!

> **Pro Tip**
>
> The Administration Area can always be reached from  
> [http://localhost:8080/myportal/do/login](http://localhost:8080/myportal/do/login)  
> and since version 4 it has been renewed, or should I say *rebuilt almost from scratch* and made responsive and – wherever applicable – *mobile-first*.
>
> The following are the default credentials of the hard-coded admin user:
{% highlight properties %}
    username: admin
    password: adminadmin
{% endhighlight %}
> **PLEASE PLEASE PLEASE change the password before going out in the big scary wild web**!  
> At any time, you can change the password going in the Administration Area as `admin`, opening the top right dropdown menu, and choosing `My Profile`.

But wait, there's one more:

> **Pro Tip**
>
> While you are exploring that `My Profile` thingy, insert your full name (yes, *The Boss* is a perfectly fine full name, but your real name might be a better choice) and an email address, then save.
> If that email address is a real one, and you have a [Gravatar](http://gravatar.com) linked to it, it will show up in various places making your experience with Entando a little more personal.
>

Proceed to the `Pages` section, select `Home` from the Page Tree and then `Edit`.  
Change the Page Model to one of the two coming from the installed Bundles: `Bootstrap - Hero Unit`, check `Apply default widgets`, then `Save`.

In another browser tab, go back to `http://localhost:8080/myportal` to see what has changed.

But don't linger there too much because we are not yet done with the Home.

#### How to publish a Content

Go back to the Administration Area, `Pages`, select `Home` again but this time choose `Configure`.

See that fairly long panel? It shows all the positions available in the current Page after you applied the new Page Model, and some of them are already filled with the Widgets you placed when you checked `Apply default widgets`.

One of those is described as `Hero Unit`. Follow its link.  
Still with me? It gets easier, just follow these steps:

* Choose the Widget `Contents - Publish a Content`
* `Confirm`
* `Choose` the Content to publish:  
You have two sample Contents of type Generic Content, thanks to `entando-content-generic` being a dependency of `entando-page-bootstrap-hero` which comes with the archetype you used to create `myportal`.  
Long story short, choose `Sample - Hero Unit` and `Confirm`
* Under `Publishing settings`, as the Content model, choose `Hero Unit + Picture` and `Save`

See? You always deal with Widgets on a Page repeating just three steps:

1. Choose a Position on the Page
2. Choose a Widget
3. Configure the Widget

Some Widgets don't even need to be configured!

Anyway. Time to go back to the public side of `myportal` and bask in the light of the freshly published Content.

Do it now, then come back for the next step.

> **Pro Tip**
>
> From the Administration Area, you can reach the public side in three major ways:
>
> 1. The general utility dropdown menu in the header of every section (which at this time should read "**The Boss** \[your avatar\]") has a handy `Go to the portal` link
>
> 2. When managing Pages: the `Configure`, `Details`, and `Edit` interfaces have a breadcrumbs recap of where the current Page is in the Page Tree; in that recap, next to every public Page in the path from Home to the current one, there's a planet-shaped icon which links to the public side of that Page
>
> 3. Of course, you can always use the address bar of your browser and point it to `http://localhost:8080/myportal`

#### How to publish a list of Contents

Now that you know how to publish a Content, and you also just learned how to put a Widget on a Page, it's time to go for the extra mile and use that knowledge to get a fancy `Latest News` on the homepage of `myportal`.

I'll keep it straight and short this time, to help you feel like you really just learned something valuable and are able to accomplish the new mission.

1. Administration Area --> `Pages`
2. Home --> `Configure`
3. Put the Widget `News - Latest News` in `Side 1`
4. Beer

Back to the public side of `myportal` you'll find a list of News ordered by descending Date, each with its own `View Details` button, the whole thing ending with an `Archive` button.  
Take mental note of the actual presence of the two buttons, because you are going to use them pretty soon.

Supplying proper target Pages for those buttons, it's the goal of the next section.

> **Pro Tip**
>
> `News - Latest News` comes from `entando-content-news` and it is a **User Widget**: a pre-configured logical Widget created as a saved configuration from a `Custom`, `Plugin`, or `Stock Widget`.
>
> In fact, `News - Latest News` is created from `Contents - Publish a List of Contents`.
>
>Thus, if you're feeling confident enough, you could go on experimenting by putting a `Contents - Publish a List of Contents` in `Side 2` and tinkering with its configuration by yourself, this time without going the `User Widget` route.

### The other pages
In this section you'll create two new Pages, and configure them to properly act as target pages for the `View Details` and `Archive` buttons from the previous section.

#### Page for on-the-fly publishing
This mythological animal is one of the smartest guys in Entando as a platform.  
Every Content Type has one of them, and thanks to it – no matter **where** or **if** a Content is published – there will always be a Page able to show that Content to a visitor of your portal (once the authorization policies are applied).

1. Administration Area --> `Pages`
2. Home --> `New`
3. Code: `read_the_news`  
Title: `Read the News` (repeat at will for each configured language)  
Model: `2 Columns - Left`  
Check:  
`Apply default Widgets`  
`Configure for on-the-fly publishing`  
`Show this page in menus`  
`SEO: When available, use extra titles`  
4. `Save`

If you want, take a look at `Configure` on the new Page and you'll see that by checking `Configure for on-the-fly publishing` you told Entando to put a specially pre-configured `Contents - Publish a Content` in the Main Position marked by a different "gears" icon and colored in green.  
And this is exactly how you prepare a Page for the ever honorable job of being a Page for on-the-fly publishing.

Now, instruct the `News` Content Type on how to use it.

But first, a detour.

1. Administration Area --> `Settings` --> `General`
2. Under `Additional features`, check `Content types`
3. Save 

Going back on the main road:

1. Administration Area --> `Contents` --> `Content types` --> `News` --> `Edit`
2. Make sure that `Page for on-the-fly publishing` is pointing to `Read the News` (given it's the only page properly configured for the job so far, you should find it selected as expected when you arrive there)
3. `Save`

Hey, done!  
Go back to the public side of `myportal`, reload the Home to let Entando update the links for the `View Details` buttons, and try to use one.

You'll end up in a Page with the following interesting characteristics:
* Page Model `2 Columns - Left` with proper default Widgets such as `Navigation - Breadcrumbs` and `Navigation - Vertical Menu`
* The Content you wanted to publish on-the-fly, rendered with the `Default content model`
* Document `<title>` (it was `Read the News`, remember) overridden on-the-fly by the title of the Content, thanks to `SEO: When available, use extra titles`

#### News archive
One button down, one left to go.

Go take a look at the configuration of `News - Latest News`, in order to get the expected code for the Page you are going to create:

1. Administration Area --> `Widgets`
2. Follow the link on `News - Latest News`
3. Search until you spot `pageLink` and copy the value (it should be `news`)

That's the expected code for the Page to which the `Archive` button from `News - Latest News` is supposed to point.

So, now create the Page:

1. Administration Area --> `Pages`
2. Home --> `New`
3. Code: `news`  
Title: `News Archive` (repeat at will for each configured language)  
Model: `2 Columns - Left`  
Check:  
`Apply default Widgets`  
`Show this page in menus`  
4. `Save`

That's it. 
Just go back to the public **Home**, reload, and use the `Archive` button... 

*Bummer!* The Page is empty!

Fret not, my dear, for you have to go and configure your own luck!

1. Administration Area --> `Pages`
2. Home --> News Archive --> `Configure`
3. Put the Widget `News - Archive` in `Content 1` (because it is often a very good idea to save `Top Story` for a possible future introductory Content)
4. `Confirm`

Go back to that ugly empty page, reload & rejoice for its new fulfillment.

> **Pro Tip**
>
> Looking lazily at the Breadcrumbs, you may have noticed that `Read the News` and `News Archive` are sister Pages living in the same level of the Page Tree.  
>You may also have started wondering if having `Home` / `News Archive` / `Read the News` wouldn't have been a better choice.
>
> Well, as it happens, you are right.
>
>It **is** better and much more clean and elegant. Also, it is even better to change the title `News Archive` to just `News`, thus ending up with:  
> `Home` / `News` / `Read the News`
>
> So - if you're feeling brave - it's now up to you as an exercise to delete those two sister Pages (check the Page Tree for the `Delete` option on a Page) and re-do the steps described in this section applying the proper tiny modifications in order to get that fancier Page Tree layout.

### Taking care of the Contents

In the previous section you learned how to take care of a Content Type (News): you created, configured, and set a Page for on-the-fly publishing.

Cool. Now go and do it all also for the Generic Content.  
No need to hurry, take your time.  
You'll find me here when you come back.

Seriously :) this is not optional. Laters.

#### WYSIWYG,IYAI (What You See Is What You Get, If You Activate It)
Entando has always cared about Web Accessibility.  
This is why we chose not to activate the great [CKEditor](http://ckeditor.com/) by default.

But it is just a few clicks away. Go ahead and activated it now:  
* Administration Area --> `Settings` --> `General`
* `Miscellaneous` --> `Choose the Hypertext Editor:` --> `CKEditor`
* `Save`

#### Strippers gonna strip
As you may have noticed wandering through the public side of `myportal`, when you are logged in and a Content is directly or on-the-fly published via `Contents - Publish a Content`, a nice purple `Edit` button appears on the top left of the Content.  
Using it, will gracefully land you in Administration Area --> `Contents` --> `Edit`, where you can – wait for it – edit that Content.

> **Pro Tip**
>
> That happens because you are in as `admin`, also known as **The Almighty Superuser**.
> In real world scenarios, it's not unusual to have some `Editor` role assigned to users who can only access the Administration Area and edit Contents.
>
> Check Administration Area --> `Settings` --> `Users` and `User Roles` and experiment for a while, if you want.

Now to the real job.  
We all know those News have way too long texts when they're grouped in lists, right?

That's because their `Abstract` is too long.  
Go on, edit some of the sample News and strip down their `Abstract` until you are fine with their length.  
You'll find the button for saving your modifications on the bottom of the edit interface; it's the one labeled as `Save and approve`.  
While you are there, it's not a bad idea to also try what the other buttons do.

When you are done, you will have a much nicer `News - Latest News` and `News Archive`.

### Searching
One my favorite features in Entando is the embedded Search Engine.  
Now that you got the basics, putting it up and running is a matter of three simple steps:

1. Place a `Search Form` in a suitable position (and this is already done by the Page Bundles in use, so nothing to do for you)
2. Create a new Page with code `search_result` and put a `Search - Search Result` in the Main Position (remember? different "gears" icon and colored in green...)  
Also remember to always choose one of the Page Models coming from the Page Bundles: `Bootstrap - Hero Unit` or `2 Columns - Left`
3. Initialize the search index:  
Administration Area --> `Settings` --> `Reload the content indexes` --> `Reload contents` & `Reload indexes`

Go back to the Homepage and try a search.  
If you didn't edit the sample Contents that much, `news` could be a good test word.

> **Pro Tip**
>
> In a development environment, the search index is stored in `/target/temp/indexdir`.
>
> Thus, whenever your run `mvn clean`, the `indexdir` is gone and you'll have to rebuild the index.
>
> Keeping that in mind, and acting accordingly, will contribute in keeping you away from mental hospitals.

## Dressing up for the party
In this section you'll learn the basics of overriding some default interfaces and you'll also leverage the presence of Twitter Bootstrap as a Bundle in `myportal`, to quickly change the look & feel.

### Override at will
To override one file of Entando, [one does simply](http://knowyourmeme.com/photos/157116) copy it from the sources into `myportal`.

Say you'd like to customize the Page Model `Bootstrap - Hero Unit`, currently used for the Homepage, which comes from the Bundle `entando-page-bootstrap-hero`.  
There are a bunch of ways to accomplish this. Here's the easiest one:

1. Launch `myportal` with Jetty
2. Copy  
`target/tmp/entando-page-bootstrap-hero-4_0_0_war/WEB-INF/aps/jsp/models/entando-page-bootstrap-hero.jsp`  
into  
`src/main/webapp/WEB-INF/aps/jsp/models/entando-page-bootstrap-hero.jsp`
3. Stop and relaunch Jetty to make it aware of the new file
4. Edit the new file at will and you will see the changes as expected

Do it now, because in the next section you'll be customizing that very file.

### Pimp my portal
[Yo Dawg, I herd you like Bootstrap in your portal, so I put a portal in your Bootstrap so you can swatch it while you swatch it.](http://knowyourmeme.com/photos/176526-xzibit-yo-dawg)

Enter the amazing [Bootswatch](http://bootswatch.com/).  
Enter the even more amazing [Bootswatch CDN](http://www.bootstrapcdn.com/#bootswatch_tab).

Choose one swatch from the Bootswatch CDN, say `Cerulean`, and copy its **URL for Bootstrap 2**:  
`//netdna.bootstrapcdn.com/bootswatch/2.3.0/cerulean/bootstrap.min.css`

Now open `src/main/webapp/WEB-INF/aps/jsp/models/entando-page-bootstrap-hero.jsp` and place a new link to that stylesheet right before the closing `</head>`, just like this:

{% highlight html %}
  <jsp:include page="inc/lesscss-active/lesscss.jsp" />
  <jsp:include page="inc/models-common-utils.jsp" />

  <link rel="stylesheet" type="text/css" href="//netdna.bootstrapcdn.com/bootswatch/2.3.0/cerulean/bootstrap.min.css" />

</head>
{% endhighlight %}

Reload the Homepage and behold the switch from the default Entando Bootstrap theme to Cerulean.

> **Pro Tip**
>
> That was the quickest path, and a path also useful for showing you the fundamentals about overriding and customizing.  
>But if you try the responsive features of Bootstrap now, you'll see that something broke when we added Cerulean.  
>That's because the best place where to put the new stylesheet is not the file we put it in.  
>Try this instead:
>
> 1. Delete the new `<link>` element from `entando-page-bootstrap-hero.jsp`
> 2. Open all the `lesscss.jsp` files you find in `src/main/webapp/WEB-INF/aps/jsp/models/inc/lesscss-active` and `src/main/webapp/WEB-INF/aps/jsp/models/inc/lesscss-available` and subdirectories.  
>There must be a total of 5 files to edit.
> 3. Put the new `<link>` element inside each of those files, just between the `<link>` for `bootstrap` and the one for `bootstrap-responsive` (or just `responsive`).  
>That will override the look and feel of Bootstrap, while preserving the responsive part.

### Embrace Eternity
Here we are, at the end of the journey. You learned how to:  
* Create a new portal
* Launch it
* Add some Bundles
* Publish a Content and a list of Contents
* Put a Widget on a Page
* Create and configure a Page
* Prepare a Page for on-the-fly publishing
* Activate the embedded search engine
* Quickly go to edit a Content when on the public side of a portal
* Override and customize your portal

That's quite a lot of basic concepts, for such a quick journey.  
You're just beginning to grasp the amazing power that comes with Entando, and the best is yet to come.

I release thee, go!

And boast a little.

![Bart like a boss](/img/bart.gif)

You deserve it.