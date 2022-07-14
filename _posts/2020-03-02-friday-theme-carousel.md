---
title:  How to config SSL in Jenkins
tags:
  - Jenkins
  - CICD
  - Maven
  - Markdown
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
Here is the guidelines to activate SSL in jenkins.
<!--more-->
Step 1: Generate CSR certificate
Execute the following command to generate csr certificate and give name “demo.ssl.csr“.
{% highlight javascript %}
openssl req -new > demo.ssl.csr
{% endhighlight %}

Step 2: Create a key file
Use your PEM file and execute the following command to create a file for the generating certificate and give the name "demo.cert.key".
{% highlight javascript %}
openssl rsa -in privkey.pem -out demo.cert.key
{% endhighlight %}

Step 3: Create CSR certificate using Key file
Now we have a Key file and CSR file, run following command to create CSR file and define retention periods.
{% highlight javascript %}
openssl x509 -in demo.ssl.csr -out demo.cert.cert -req -signkey demo.cert.key -days 365
{% endhighlight %}

Step 4: Create pkcs12 file
Now, use following command to create an intermediate pkcs12 file and define following parameters.
Give name of pkcs12 file name, example- jenkins_demo.p12
Set strong password for pkcs12 file
Give FQDN or alias name, example- ericsong
{% highlight javascript %}
openssl pkcs12 -export -out jenkins_demo.p12 -passout 'pass:songyao1234' -inkey demo.cert.key -in demo.cert.cert -name ericsong
{% endhighlight %}

Step 5: Create Java Keystore file (JKS)
Now we will use Ketstore command-line tool to generate a new key “jenkins_demo_jks” and will set a password in deststorepass field.
{% highlight javascript %}
keytool -importkeystore -srckeystore jenkins_demo.p12 -srcstorepass 'songyao1234' -srcstoretype PKCS12 -srcalias ericsong -deststoretype JKS -destkeystore jenkins_demo.jks -deststorepass 'songyao1234' -destalias ericsong
{% endhighlight %}

Step 6: Copy keystore file to Jenkins
Execute following commands to create keystore directory and add keystore file to Jenkins at default location as well as change directory permissions.
{% highlight javascript %}
cd /var/lib/jenkins
mkdir .keystore
cp /home/sy/jenkins_demo.jks /var/lib/jenkins/.keystore/
chmod 700 .keystore/
{% endhighlight %}

Step 7: Change in Jenkins file
Make few changes in the Jenkins’s file property. Open the file /etc/default/jenkins file.
{% highlight javascript %}
HTTPS_PORT=8088
KEYSTORE=/var/lib/jenkins/.keystore/jenkins_demo.jks
PASSWORD=songyao1234
JENKINS_ARGS="--webroot=/var/cache/$NAME/war --httpsPort=$HTTPS_PORT --httpsKeyStore=$KEYSTORE --httpsKeyStorePassword=$PASSWORD --httpPort=-1"
{% endhighlight %}

Step 8: Validate the configuration
{% highlight javascript %}
sudo systemctl restart jenkins
sudo systemctl status jenkins
https://<dns_name/ip>:8088
{% endhighlight %}

<div id="carouselExampleControls" class="carousel slide mb-4" data-ride="carousel">
    <div class="carousel-inner">
        {% for img in page.images %}
            <div class="carousel-item {% if forloop.first %}active{% endif %}">
                <img src="{{ img }}" class="d-block w-100" alt="">
            </div>
        {% endfor %}
    </div>
    <a class="carousel-control-prev" href="#carouselExampleControls" role="button" data-slide="prev">
        <span class="carousel-control-prev-icon" aria-hidden="true"></span>
        <span class="sr-only">Previous</span>
    </a>
    <a class="carousel-control-next" href="#carouselExampleControls" role="button" data-slide="next">
        <span class="carousel-control-next-icon" aria-hidden="true"></span>
        <span class="sr-only">Next</span>
    </a>
</div>


Hope it can be helpful to you.
