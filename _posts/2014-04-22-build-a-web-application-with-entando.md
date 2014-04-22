---
title: Build a web application with Entando
permalink: /build-a-web-application-with-entando
layout: post
---

# How to build a web application with Entando (and Edo)

Hello, I'm Stefano Puddu – [GitHub/stefanopuddu](https://github.com/stefanopuddu) – and I'm a backend engineer at Entando.

In this guide, I'm going to show you how to:

1. Create an Entando Plugin for a todo list.
2. Install the plugin into the local maven repository
3. Create a website with Entando and add the plugin as a maven dependency
4. Add some customizations

### Prerequisites

* *(Optional)* Read [*How to build a website with Entando*]({% post_url 2014-04-22-build-a-website-with-entando %})
* Install [Edo](http://entando.github.io/edo/) v4.0.0 [*(Direct download)*](https://github.com/entando/edo/archive/v4.0.0.zip)
* Get the sources of [`entando-plugins-parent`](https://github.com/entando/entando-plugins-parent) v4.0.0 [*(Direct download)*](https://github.com/entando/entando-plugins-parent/archive/v4.0.0.zip)

## 1. Create an Entando Plugin

Once you have `entando-plugins-parent` in a comfortable place – let's say `~/entando/entando-plugins-parent` – go there:

{% highlight bash %}
cd ~/entando/entando-plugins-parent
{% endhighlight %}

And launch:

{% highlight bash %}
mvn archetype:generate -Dfilter=entando -DpluginCode=jptodo -DpluginName="ToDo List"
{% endhighlight %}

Choose whatever the option number for the plugin archetype is:

{% highlight bash %}
remote -> org.entando.entando:entando-archetype-plugin-generic (Generic Plugin Archetype for Entando:  an agile, modern and user-centric open source portal-like platform.)
{% endhighlight %}

We are working on Entando version 4.0.0 so that's also the version to choose for the plugin archetype.

Configure the remaining options like so:

{% highlight bash %}
Define value for property 'groupId': : org.entando.entando.plugins
Define value for property 'artifactId': : entando-plugin-jptodo
Define value for property 'version':  1.0-SNAPSHOT: :
Define value for property 'package':  org.entando.entando.plugins: :
[INFO] Using property: pluginCode = entando-plugin-jptodo
[INFO] Using property: pluginName = ToDo List
Confirm properties configuration:
groupId: org.entando.entando.plugins
artifactId: entando-plugin-jptodo
version: 1.0-SNAPSHOT
package: org.entando.entando.plugins
pluginCode: entando-plugin-jptodo
pluginName: ToDo List
{% endhighlight %}

This leaves you with an empty, barebones plugin.

The archetype creates a set of files, mainly based on the `pluginCode`.  
Some of these files must be present once per plugin and if Edo finds them, it will preserve the existing ones and it will create his own with a special prefix.

If this happens, don't worry. These files are well documented in the report Edo creates when it's done with its job, and you can then safely merge them according to your needs.

Since we are starting a plugin from scratch, let's make things a bit easier to begin with.

Delete some of those files so that Edo can create and configure them with no hassle.

{% highlight bash %}
cd entando-plugin-jptodo/
rm src/main/resources/entando-struts-plugin.xml
rm src/main/resources/component/plugins/jptodo/component.xml
rm src/main/resources/sql/plugins/jptodo/port_data_production.sql
rm src/main/resources/sql/plugins/jptodo/serv_data_production.sql
rm src/test/resources/sql/plugins/jptodo/port_data_test.sql
rm src/test/resources/sql/plugins/jptodo/serv_data_test.sql
{% endhighlight %}

You should already be in the plugin's main directory, otherwise:

{% highlight bash %}
cd ~/entando/entando-plugins-parent/entando-plugin-jptodo
{% endhighlight %}

Now we are ready to create a plugin that handles our simple todo list.

With Edo is as simple as:

{% highlight bash %}
edo Todo descr:string-r status:int
{% endhighlight %}

## 2. Install the plugin into the local maven repository

{% highlight bash %}
cd ~/entando/entando-plugins-parent/entando-plugin-jptodo
mvn clean install
{% endhighlight %}

## 3. Create a website with Entando and add the plugin as a maven dependency

### Create a website

If you don't know how to do it, this is better explained in [*How to build a website with Entando*]({% post_url 2014-04-22-build-a-website-with-entando %}).

*Just remember to keep on using version 4.0.0 also for the website.*

I'll name the website `myportal`.

### Add the plugin to `myportal`

{% highlight bash %}
cd ~/entando/myportal
{% endhighlight %}

Then edit the `pom.xml` file, and add this dependency snippet:

{% highlight xml %}
<dependency>
<groupId>org.entando.entando.plugins</groupId>
<artifactId>entando-plugin-jptodo</artifactId>
<version>${entando.version}</version>
<type>war</type>
</dependency>
{% endhighlight %}

The `<version>${entando.version}</version>` part works just fine because we are using the same version number for everything (4.0.0).

This is actually working as intended because we keep the versions of the various Entando artifacts aligned on purpose.

### Start the website and check out the plugin

Fire it up and enter the administration area.

Again, if you don't know how to do it, this is better explained in [*How to build a website with Entando*]({% post_url 2014-04-22-build-a-website-with-entando %}).

Once you are in, under `Plugins` you'll notice that we have a menu item called `Todos`.  
This is the entry point for the backoffice operations, a full ready-to-go CRUD for our todos.

Go on, try it. Create some new todos, since we are going to need them for the next steps.

### REST API

Go to `Settings` --> `General` and activate the `API` feature. Now you can see a new item in the main menu: `API`.

Go to `API` --> `Resources`.  
In the bottom, you can find the entry point for the REST API, ready to use for our todos.

For example, click to `todos`, find the `Resource URI` element and try the link.

### Widgets

Go to `Widgets`. Notice the two ready-to-use widgets Edo created for you.

A widget is meant to to published in a page, so:

1. Go to `Pages` and create a new page. Choose a suitable page model, like `Bootstrap - Hero Unit`.
2. Configure the page, and add the `Todo List and Form` widget to a good position, like `Top Story`.

Then go check out the public side of the website.

As you can see, this widget provides a full todo mini-app you can use in the public side of the website.

Also – and I promise this is the last time I'll bug you with this reminder – if you don't know how to do it, this is better explained in [*How to build a website with Entando*]({% post_url 2014-04-22-build-a-website-with-entando %}).

## 4. Add some customizations

Now that we have our plugin, is time to make it better.

For example would it be nice if only authorized users – e.g. only the ones with the `superuser` permission – were able to add and edit entries.

From this point on I suggest you to use your favorite IDE.

First, we will modify the source of the plugin. Then we will play with our website and make some JSP *overlays* we'll finally backport into the sources.

### Authorizations

Every user which is not currently signed in, is called `guest` in Entando.

We have to protect our widget actions in two ways:

1. Protecting our action from being executed by `guest` users
2. Hiding links and forms when the user is `guest`

#### 1. Protecting our action from being executed by `guest` users

Go back to the sources of `entando-plugin-jptodo` and open `src/main/java/org/entando/entando/plugins/jptodo/aps/internalservlet/todo/todoFront.xml`.

This file is the configuration behind the `Todo List and Form` widget.

As you can see, all the actions have two interceptors: `entandoFrontEndFreeStack` and `entandoFrontEndFreeValidationStack`.

All we have to do is change them all, **except for the `list` and `search` actions, which we want to remain `free`**.

Change this:

{% highlight xml %}
<interceptor-ref name="entandoFrontEndFreeStack"></interceptor-ref>
{% endhighlight %}

Into this:

{% highlight xml %}
<interceptor-ref name="entandoFrontEndStack">
     <param name="requestAuth.requiredPermission">superuser</param>
</interceptor-ref>
{% endhighlight %}

And this:

{% highlight xml %}
<interceptor-ref name="entandoFrontEndFreeValidationStack"></interceptor-ref>
{% endhighlight %}

Into this:

{% highlight xml %}
<interceptor-ref name="entandoFrontEndValidationStack">
    <param name="requestAuth.requiredPermission">superuser</param>
</interceptor-ref>
{% endhighlight %}

##### Customizing the `userNotAllowed` JSP

If any unauthorized user tries to perform an action, Entando will perform a redirect to a result called `userNotAllowed`.  
There's a default JSP for this, but we can still customize it for our namespace.

Edit the `<global-results>` item at the top of the `todoFront.xml` file. Add a new `<result>` with name `userNotAllowed`.

{% highlight xml %}
<global-results>
    <result name="failure">/WEB-INF/plugins/jptodo/aps/jsp/internalservlet/todo/frontend-todo-error.jsp</result>
    <result name="userNotAllowed">/WEB-INF/plugins/jptodo/aps/jsp/internalservlet/todo/frontend-todo-notallowed.jsp</result>
</global-results>
{% endhighlight %}

*Pretend you forgot to create the JSP* and compile the plugin:

{% highlight bash %}
cd ~/entando/entando-plugins-parent/entando-plugin-jptodo
mvn clean install
{% endhighlight %}

Let's go to our project and start jetty:

{% highlight bash %}
cd ~/entando-projects/myportal
mvn clean jetty:run
{% endhighlight %}

On the public side of the website, find the page where you previuosly published the `Todo List and Form` widget and – as `guest` (i.e. without first signing in) – try to add a new todo with the `New` button.

Notice the console messages from Jetty in your terminal or IDE console.

{% highlight java %}
java.io.FileNotFoundException: /WEB-INF/plugins/jptodo/aps/jsp/internalservlet/todo/frontend-todo-notallowed.jsp
{% endhighlight %}

Jetty is right, we *forgot* to create the JSP!

Stop Jetty, and actually create the file `~/entando-projects/myportal/src/main/webapp/WEB-INF/plugins/jptodo/aps/jsp/internalservlet/todo/frontend-todo-notallowed.jsp` with the following sample content:

{% highlight jsp %}
<%@ taglib prefix="wp" uri="/aps-core" %>
<div class="alert alert-error">
    <p>
        <wp:i18n key="jptodo_NOT_ALLOWED" />
    </p>
</div>
{% endhighlight %}

Restart Jetty and try again to add a todo: here's your `jptodo_NOT_ALLOWED`.

* Sign in as `admin` (the default password is `adminadmin`)
* Administration area --> `Settings` --> `Labels`
* Create a new label using `jptodo_NOT_ALLOWED` as the `Key` and whatever you like best as the values – e.g. *"Sorry mate, you can't add a new todo."*
* Sign out and try again to add a new todo action as `guest`

And here's the expected message instead of the label key.

#### 2. Hiding links and forms when the user is `guest`

Now we have to show links and buttons only for authorized users.
To discover which JSP is involved, open the `src/main/java/org/entando/entando/plugins/jptodo/aps/internalservlet/todo/todoFront.xml` file from the plugin sources.

Find the `list` action and the jsp declared as `result`.

The JSP is `/WEB-INF/plugins/jptodo/aps/jsp/internalservlet/todo/frontend-todo-list.jsp` and it'll be under `src/main/webapp`.  
Copy it from the plugin sources into the same path inside your project.

This time, the folder will happen to be the same as `frontend-todo-notallowed.jsp`.

I.e.: `~/entando/myportal/src/main/webapp/WEB-INF/plugins/jptodo/aps/jsp/internalservlet/todo/frontend-todo-list.jsp`

Hiding the `New` button away according to our needs, is very simple.

Just surround the proper html portion with the `wp:ifauthorized` custom tag:

{% highlight jsp %}
<wp:ifauthorized permission="superuser">
<p>
  <a href="<wp:action path="/ExtStr2/do/FrontEnd/jptodo/Todo/new.action"></wp:action>" title="<wp:i18n key="NEW" />" class="btn btn-info">
    <span class="icon-plus-sign icon-white"></span>&#32;
    <wp:i18n key="NEW" />
  </a>
</p>
</wp:ifauthorized>
{% endhighlight %}

If you were using Jetty, you'll now have to stop-and-restart-it because while it's perfectly fine with hot-deployed *modified* files, it doesn't know how to deal with hot-deployed *new* files.

If you check the page, you'll now see that the `New` button is gone. It will be back in place once you sign in as a user with the `superuser` permission (i.e. `admin`).

### Porting the customizations from the website back to the plugin sources

Once you are happy with the result, you can (well, you *should*) port it back into the sources of the plugin.

So far, you'll have to:

* Update the `frontend-todo-list.jsp` file
* Add the `frontend-todo-notallowed.jsp` file
* Provide proper installation instructions for the `jptodo_NOT_ALLOWED` label.

The first two points are quite straightforward: just update the first file, and add the second one, in the proper path (see above if you forgot).

About the third point, here's what to do.

In the plugin sources, open the `src/main/resources/sql/plugins/jptodo/port_data_production.sql` file.

Add a handful of `INSERT` statements for the `jptodo_NOT_ALLOWED` label:

{% highlight sql %}
INSERT INTO localstrings ( keycode, langcode, stringvalue ) VALUES ('jptodo_NOT_ALLOWED', 'it', 'Sorry mate, you can''t add a new todo');
INSERT INTO localstrings ( keycode, langcode, stringvalue ) VALUES ('jptodo_NOT_ALLOWED', 'en', 'Sorry mate, you can''t add a new todo');{% endhighlight %}

And you're done!

### Final steps

Now that the plugin sources are up-to-date, clean `myportal` up by deleting the *JSP overlays* you put in place while customizing the plugin.

{% highlight bash %}
cd ~/entando/myportal
rm src/main/webapp/WEB-INF/plugins/jptodo/aps/jsp/internalservlet/todo/frontend-todo-list.jsp
rm src/main/webapp/WEB-INF/plugins/jptodo/aps/jsp/internalservlet/todo/frontend-todo-notallowed.jsp
{% endhighlight %}

Also update the plugin in your local maven repository, just in case:

{% highlight bash %}
cd ~/entando/entando-plugins-parent/entando-plugin-jptodo
mvn clean install
{% endhighlight %}

#### Some notes

Sometimes you could be in the position of choosing to start a plugin directly inside an Entando website / portal / web application.

In such a scenario, just run Edo from the root directory of the website without going the *new-plugin-from-archetype* route:

{% highlight bash %}
cd ~/entando/myportal
edo Todo descr:string-r status:int
mvn clean jetty:run
{% endhighlight %}

That's all!

Remember: using Edo is a great way of starting a project with Entando. But to discover how simple, powerful and scalable Entando is, you'll still need to fire up your favorite IDE and write your own code.
