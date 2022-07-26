---
title:  How to publish your android library to MavenCenter
tags:
  - Jira
  - MavenCenter
  - Github
  - Android
  - Groovy
  - Java
---
I will take my first published library as an example and make this a single source of guidance for all of you who are interested in publishing Android libraries.
<!--more-->

Step 1: Registering a Jira account with Sonatype, and verifying your ownership of the group ID you want to publish your artifact with.
While login in with your Jira account registered above,you need to create a Jira ticket and it will be assigned automatically.Notes the 
Issue type must be "New Project" and select Project as "Community Support - Open Source Project Repository Hosting (OSSRH)".

On the next page, fill out the following fields:

Summary: Create repository for your.group.id.here

Description: An optional, quick summary of what your project is.

Group Id: Your group ID, as described a few sections earlier.

Project URL: If your project has a webpage, the URL of that page. This can also be just the GitHub repository.

SCM url: Your source control URL, i.e. the GitHub repository link.

Username(s): If you want additional users (on top of the one you’re using for this process) to have deploy access for your group ID,you can list them here.

Already Synced to Central: If you’re just getting started, this should be No.

Soon after opening it, your issue will get a comment telling you to verify that you own the domain corresponding to your group ID:

<img src="/assets/images/jira_verify.png" style="width:625px;height:240px;">

When done, don’t forget to leave a comment on the issue so that Sonatype knows to check the record. You’ll eventually get a response telling you that you now have deploy rights - congrats!
Below is Done for my ticket.

<img src="/assets/images/jira_sonatype.png" style="width:625px;height:240px;">

Step 2: Generating a GPG key pair for signing your artifacts, publishing your public key, and exporting your private key.
GPG Suite is an easy-to-use distribution for macOS, and you can use Gpg4win if you’re on Windows. Both of these come with GUI tools 
that make managing keys easier.
To generate a new key, run the following command:
{% highlight javascript %}
gpg --full-gen-key
{% endhighlight %}

You’ll be prompted to enter a few details:
Kind of key: Accept the default value, which is (1) RSA and RSA.
Key size: 4096.
Expiration: You can input 0 to generate a key that never expires. You can also create a key that has an expiry date and then renew it 
periodically, if you prefer to do so.
Real name, email: Should be obvious.
Comment: Freeform text, can be left empty.
Here’s the full flow you'll go through, with a bit of truncation:
{% highlight javascript %}
Please select what kind of key you want:
   (1) RSA and RSA (default)
   ...
Your selection? 1

RSA keys may be between 1024 and 4096 bits long.
What keysize do you want? (3072) 4096
Requested keysize is 4096 bits

Please specify how long the key should be valid.
         0 = key does not expire
         ...
Key is valid for? (0) 0
Key does not expire at all
Is this correct? (y/N) y

GnuPG needs to construct a user ID to identify your key.

Real name: Marton Braun
Email address: marton@getstream.io
Comment: Example key for tutorial
You selected this USER-ID:
    "Marton Braun (Example key for tutorial) <marton@getstream.io>"

Change (N)ame, (C)omment, (E)mail or (O)kay/(Q)uit? O

gpg: key 36271B955BEF072A marked as ultimately trusted
gpg: revocation certificate stored as '.../gnupg/openpgp-revocs.d\7A5D73CFEDDDBC915986998A36271B955BEF072A.rev'
public and secret key created and signed.

pub   rsa4096 2021-02-03 [SC]
      7A5D73CFEDDDBC915986998A36271B955BEF072A
uid                      Marton Braun (Example key for tutorial) <marton@getstream.io>
sub   rsa4096 2021-02-03 [E]
{% endhighlight %}
Your key’s ID is the last eight digits of its fingerprint (the long hexadecimal string above). In this case, this is 5BEF072A - take note of this, as you’ll use this later.
{% highlight javascript %}
gpg --keyserver keyserver.ubuntu.com --send-keys 5BEF072A
{% endhighlight %}

Your private key will need to be referenced by your project when it signs the artifacts. You can get a base 64 export of it with the following:
{% highlight javascript %}
gpg --export-secret-keys 5BEF072A | base64
{% endhighlight %}
Enter your passphrase that you’ve set earlier when prompted. You can either copy-paste this key from the terminal output later on, or save it to a file temporarily.

Step 3: Setting up Gradle tasks that can sign upload your artifacts to a staging repository.
Below is a sample for publishing artifact in my project.
{% highlight javascript %}
apply plugin: 'maven'
apply plugin: 'signing'
def ossrhUsername = "ericsong"
def ossrhPassword = "xxxxxxxx"
def PUBLISH_GROUP_ID = 'io.github.songyao1987'
def PUBLISH_ARTIFACT_ID = "xxxxToolKit"
def PUBLISH_VERSION = rootProject.ext.VERSION_NAME
signing {
    required { gradle.taskGraph.hasTask("uploadArchives") }
    sign configurations.archives
}

uploadArchives {
    repositories {
        mavenDeployer {
            beforeDeployment { MavenDeployment deployment -> signing.signPom(deployment) }
            repository(url: "https://s01.oss.sonatype.org/service/local/staging/deploy/maven2/") {
                authentication(userName: ossrhUsername, password: ossrhPassword)
            }
            snapshotRepository(url: "https://s01.oss.sonatype.org/content/repositories/snapshots/") {
                authentication(userName: ossrhUsername, password: ossrhPassword)
            }
            pom.groupId = PUBLISH_GROUP_ID
            pom.artifactId = PUBLISH_ARTIFACT_ID
            pom.version = PUBLISH_VERSION
            pom.project {
                name 'xxxxToolKit'
                packaging 'aar'
                // optionally artifactId can be defined here
                description 'For Minesec development use only'
                url 'https://github.com/songyao1987/xxxxToolKit'
                scm {
                    connection 'scm:https://github.com/songyao1987/xxxxToolKit.git'
                    developerConnection 'scm:git@github.com:songyao1987/xxxxToolKit.git'
                    url 'https://github.com/songyao1987/xxxxToolKit'
                }
                licenses {
                    license {
                        name 'The Apache License, Version 2.0'
                        url 'http://www.apache.org/licenses/LICENSE-2.0.txt'
                    }
                }
                developers {
                    developer {
                        id 'ericsong'
                        name 'ericsong'
                        email '413142768@qq.com'
                    }
                }
            }
        }
    }
}
{% endhighlight %}
Finally,do not forgot to add the reference in build.gradle of Module directoy for the above script
{% highlight javascript %}
apply from: '../maven-publish-aar.gradle'
{% endhighlight %}
Step 4: Manually going through the process of checking your artifacts in the staging repo and releasing them via the Sonatype web UI.

Step 5: Automating the close & release flow with a Gradle plugin

Step 6: Configuring CI workflows with GitHub Actions to automate all of the above

<img src="https://images.unsplash.com/photo-1657659448918-f54b8cdc3c67?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHx0b3BpYy1mZWVkfDQ2fEpwZzZLaWRsLUhrfHxlbnwwfHx8fA%3D%3D&auto=format&fit=crop&w=500&q=60">
