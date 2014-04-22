---
title: Install a base dev environment for Entando
permalink: /entando-base-dev-environment
layout: post
---

# Install a base environment for Entando

## OS X 10.9

* Install [Java JDK 7](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
* set the JAVA_HOME environment variable:  
{% highlight bash %}
$ echo "export JAVA_HOME=\`/usr/libexec/java_home\`" | tee -a ~/.bash_profile
{% endhighlight %}
* Install XCode from the App Store
* Install [Homebrew](http://brew.sh/)
* Run brew doctor and fix any warning you get from it
* Install Maven and Ant:  
{% highlight bash %}
$ brew install maven
$ brew install ant
{% endhighlight %}

## Ubuntu Linux

* Install Maven and Ant:  
{% highlight bash %}
$ sudo apt-get install maven ant
{% endhighlight %}
* set the JAVA_HOME environment variable:  
{% highlight bash %}
$ echo "JAVA_HOME=\"/usr/lib/jvm/default-java\"" | sudo tee -a /etc/environment
{% endhighlight %}
* Reboot

## Windows
* Install [Java JDK 7](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Install [Maven](http://maven.apache.org/download.cgi)
* Install [Ant](http://ant.apache.org/bindownload.cgi)

## Final check for all the OSes

* Generate a test web application typing bogus data when asked:  
{% highlight bash %}
$ mvn archetype:generate -Dfilter=entando-archetype-portal-bootstrap
{% endhighlight %}
* Enter the newly created folder
* Launch Jetty:  
{% highlight bash %}
$ mvn clean jetty:run
{% endhighlight %}
* Open your browser at [http://localhost:8080/](http://localhost:8080/) and ignore the 404 error
* Click on the only link you'll find on that page
