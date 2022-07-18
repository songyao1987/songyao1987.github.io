---
title:  How to implement CICD
tags:
  - Jenkins
  - CICD
  - Maven
  - Gitlab
  - Android
  - Groovy
  - Java
images:
  - https://images.unsplash.com/photo-1421789665209-c9b2a435e3dc?ixlib=rb-0.3.5&ixid=eyJhcHBfaWQiOjEyMDd9&s=5b1016b885e7438c4633109d77368d4d&auto=format&fit=crop&w=1651&q=80
  - https://images.unsplash.com/photo-1504626835342-6b01071d182e?ixlib=rb-0.3.5&ixid=eyJhcHBfaWQiOjEyMDd9&s=975855d515c9d56352ee3bfe74287f2b&auto=format&fit=crop&w=1651&q=80
  - https://images.unsplash.com/photo-1476514525535-07fb3b4ae5f1?ixlib=rb-0.3.5&ixid=eyJhcHBfaWQiOjEyMDd9&s=468a8c18f5d811cf03c654b653b5089e&auto=format&fit=crop&w=1650&q=80
  - https://images.unsplash.com/photo-1506291318501-948562d765d7?ixlib=rb-0.3.5&ixid=eyJhcHBfaWQiOjEyMDd9&s=71ad8e3b7b4bd210182ed5e5c024903b&auto=format&fit=crop&w=1650&q=80
  - https://images.unsplash.com/photo-1500402448245-d49c5229c564?ixlib=rb-0.3.5&ixid=eyJhcHBfaWQiOjEyMDd9&s=f19c590b253f803a7f9b643c59017160&auto=format&fit=crop&w=1650&q=80
---
First of all, we need to install jenkins as the following steps.
<!--more-->
Step 1: install JDK, and we can check it with command "java --version"

Step 2: install android SDK and android NDK if you are working in Android projects

Step 3: install Jenkins
Add the key from official software repository to the apt key in locall
{% highlight javascript %}
wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
{% endhighlight %}

Add the address to your locall list
{% highlight javascript %}
sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
{% endhighlight %}

Then you can complete the installation of jenkins by the following commands,
{% highlight javascript %}
sudo apt-get update
sudo apt-get install jenkins
sudo /etc/init.d/jenkins [start|restart|stop]
systemctl status jenkins
{% endhighlight %}

Now we can try to access the home page of jenkins and you can see the login required as below.
<!-- ![](/assets/images/jenkins_login.png) -->


<img src="/assets/images/jenkins_login.png" alt="Login" style="width:525px;height:480px;">

pending...
