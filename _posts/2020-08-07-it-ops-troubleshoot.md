---
layout: post
title: SRE Troubleshooting Cheatsheat
subtitle: Basic to Advanced Troubleshooting Commands, Tools and Tricks
tags: [operations, troubleshooting, linux, centos, administrator]
comments: true
---

## Compilation of Cheatsheat

 - [Official Kubernetes Cheatsheat](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)
 - [Linux Administrator Cheatsheat](https://www.linuxtrainingacademy.com/linux-commands-cheat-sheet/)

## Downloadable Tools (Windows)

 - [WinSCP](https://winscp.net/eng/download.php): used for transfering files from Windows to Linux via SSH

## "Book of Gist" by cyberpau

Github Source: `https://github.com/cyberpau/book-of-gists`

### Install Oracle JDK 8 on CentOS

#### Verify if Java 8 is not yet installed

    java -version

#### Pre-req: Download and copy the installer to /usr/local/java

    cd /usr/local/java

#### Extract the installer

    tar -zxvf jdk-8u261-linux-x64.tar.gz

#### (Optional) Remove the package

    rm -y jdk-8u261-linux-x64.tar.gz

#### Add to environment variable (assuming same install directory)

Update /etc/profile:

    cat <<EOF >> /etc/profile

    # JDK Environment Variable Settings
    JAVA_HOME=/usr/local/java/jdk1.8.0_261
    PATH=$PATH:$JAVA_HOME/bin
    CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar

    export JAVA_HOME PATH CLASSPATH
    EOF

Update .bash_profile:

    cat <<EOF >> .bash_profile

    # JDK Environment Variable Settings
    JAVA_HOME=/usr/local/java/jdk1.8.0_261
    PATH=$PATH:$JAVA_HOME/bin
    CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar

    export JAVA_HOME PATH CLASSPATH
    EOF

