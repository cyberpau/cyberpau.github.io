---
layout: post
title: SRE Troubleshooting Cheatsheat
subtitle: Basic to Advanced Troubleshooting Commands, Tools and Tricks
tags: [operations, troubleshooting, linux, centos, administrator]
comments: true
---

## Contents
1. [Cheatsheets](#compilation-of-cheatsheet)
2. [Tools for Windows](#tools-for-windows)
3. [Book of Gist](#book-of-gist)
    - [How to Install Java 8 JDK on CentOS 7](#install-java-8-jdk-on-centos-7)
    - [How to Install MySQL on CentOS 7](#install-mysql-on-centos-7)

## Compilation of Cheatsheet

 - [Official Kubernetes Cheatsheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)
 - [Linux Administrator Cheatsheet](https://www.linuxtrainingacademy.com/linux-commands-cheat-sheet/)

## Tools for Windows

 - [WinSCP](https://winscp.net/eng/download.php): used for transfering files from Windows to Linux via SSH
 - [FileZilla](): used for SFTP
 - [EverythingSearch](): powerful search for Windows 10.


## Book of Gist

Github Source: `https://github.com/cyberpau/book-of-gists`

### Install Java 8 JDK on CentOS 7

Verify if Java 8 is not yet installed

    java -version

Pre-req: Download and copy the installer to /usr/local/java

    cd /usr/local/java

Extract the installer

    tar -zxvf jdk-8u261-linux-x64.tar.gz

(Optional) Remove the package

    rm -y jdk-8u261-linux-x64.tar.gz

Add to environment variable (assuming same install directory). First, update /etc/profile:

    cat <<EOF >> /etc/profile

    # JDK Environment Variable Settings
    JAVA_HOME=/usr/local/java/jdk1.8.0_261
    PATH=$PATH:$JAVA_HOME/bin
    CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar

    export JAVA_HOME PATH CLASSPATH
    EOF

Then update .bash_profile:

    cat <<EOF >> .bash_profile

    # JDK Environment Variable Settings
    JAVA_HOME=/usr/local/java/jdk1.8.0_261
    PATH=$PATH:$JAVA_HOME/bin
    CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar

    export JAVA_HOME PATH CLASSPATH
    EOF



### Install MySQL on CentOS 7

Setup yum repository:

    rpm -Uvh https://repo.mysql.com/mysql80-community-release-el7-3.noarch.rpm

Install MySQL 8 Community Server

    sed -i 's/enabled=1/enabled=0/' /etc/yum.repos.d/mysql-community.repo
    yum --enablerepo=mysql80-community install -y mysql-community-server

Update configuration by running the command below:

    cat <<EOF >> /etc/my.cnf
    lower_case_table_names = 1
    EOF

or manually editting the file:

    vi /etc/my.cnf

Start MySQL Service:

    systemctl start mysqld

Now, let's secure our mysql install. First, show the temp password:

    grep "A temporary password" /var/log/mysqld.log

Copy the output, then do a MySQL secure Installation:

    mysql_secure_installation

Paste the copied password from the previous steps. Change your password. Press enter.

For suceeding queries, just enter "y":

    Remove anonymous users? (Press y|Y for Yes, any other key for No) : y

    Disallow root login remotely? (Press y|Y for Yes, any other key for No) : y

    Remove test database and access to it? (Press y|Y for Yes, any other key for No) : y

    Reload privilege tables now? (Press y|Y for Yes, any other key for No) : y

Login using the new credentials:

    mysql - u root -p

Verify if `lower_case_table_names = 1` was configured
    
    mysql> show variables like '%name%'

