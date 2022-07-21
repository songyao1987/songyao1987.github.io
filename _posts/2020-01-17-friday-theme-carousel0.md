---
title:  How to work with Jenkins,Gitlab and Maven
tags:
  - Jenkins
  - CICD
  - Maven
  - Gitlab
  - Android
  - Groovy
  - Java
images:
  - https://unsplash.com/photos/AuyF6tZSIpk
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


Secondly, we need to complete some configurations and install a few plugins in jenkins page,just like gradle,gitlab,email,pipeline(strong recommended),webhook.etc.In addition,we also need to config environment varibles in System configurations and learning how to write jenkinsfile with Groovy.Anyway the multibranch pipeline mode is strongly recommended.
Below is a jenkinsfile sample for building an android APK:
{% highlight Java %}
def gradleproperties = """
    MAVEN_REPO_SNAPSHOTS_URL=http://127.0.0.1:8081/repository/maven-snapshots/
    MAVEN_REPO_RELEASES_URL=http://127.0.0.1:8081/repository/maven-releases/
    MAVEN_REPO_USERNAME=admin
    MAVEN_REPO_PASSWORD=admin
    SIGNPASS=123456
    SIGNCERT=/home/ubuntu/ApkSignCert/demoapp.jks

pipeline {
    agent any
    stages {
        stage('Build') {
          steps {
              sh """
              #mkdir /root/.gradle
              echo '${gradleproperties}' >> /root/.gradle/gradle.properties
              """
              sh 'chmod +x gradlew'
              sh './gradlew clean build -x check pmd'
            }
          }
        stage('Archive') {
            steps {
                archiveArtifacts 'app/build/outputs/**/*.apk'
            }
        }
        stage('Test') {
              steps {
                  echo 'Testing..'
              }
        }
    }
    options {
      gitLabConnection('Connection2Gitlab')
  	}
    post {
      failure {
        updateGitlabCommitStatus name: 'Connection2Gitlab', state: 'failed'
      }
      success {
        updateGitlabCommitStatus name: 'Connection2Gitlab', state: 'success'
      }
    }
}
{% endhighlight %}

Finally you can see the artifact in build page and can download by manual.
