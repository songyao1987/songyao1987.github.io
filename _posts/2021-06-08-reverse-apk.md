---
title:  Reverse Android APK and see if obfuscation works
tags:
  - Android
images:
  - https://unsplash.com/photos/AuyF6tZSIpk
---
Reverse Engineering APKS: Guide To See If Obfuscation Works
<!--more-->

**Step 1: Download dex2jar: https://github.com/pxb1988/dex2jar and put in a convenient location"**
{% highlight javascript %}
D:\software\dex2jar> ./gradlew distZip
{% endhighlight %}
**Step 2: Rename app.apk to app.zip. Unpack app.zip, and look for classes.dex file**

**Step 3: In a convenient location, paste your classes.dex (note your classes.dex directory )**

**Step 4: Using command line, go to dex2jar location and run this command:**
{% highlight javascript %}
D:\software\dex2jar\dex-tools\build\distributions\dex-tools-2.2-SNAPSHOT> ./d2j-dex2jar.bat D:\temp\app-debug\classes.dex
{% endhighlight %}

**Step 5: Your .jar should now be available on your dex2jar folder**

**Step 6: Download Java Decompiler, JD-GUI(http://java-decompiler.github.io/)**

**Step 6: Drag and drop your classes-dex2jar.jar to JD-GUI,You can now read source code of each class files**
<img src="/assets/images/dex2jar.jpg" alt="Login" style="width:625px;height:230px;">