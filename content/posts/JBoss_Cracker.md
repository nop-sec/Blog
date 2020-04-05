---
title: "Cracking JBoss Passwords"
date: 2020-04-04T10:28:52+01:00
draft: false
tags: ["Cracking", "Tools", "Passwords"]
categories: ["Tools"]
---

# Introduction to Jboss EAP 6.7 Passwords

Not too long ago I came across a Jboss Enterprise Application Platform (EAP) based application server. This is a Java based server used for building, deploying and hosting Java applications and Services. Having gained access to the server using another techniques I was performing some enumeration when I noted the presense of the application.


A bit of research showed me that access to the web portal and command line is controlled by one of two files depending on whether the server is running in a standalone mode or domain.

``` 
EAP_HOME/standalone/configuration/mgmt-users.properties
EAP_HOME/domain/configuration/mgmt-users.properties 
```

New users are created using an included script _add-user.sh_ which takes three inputs. Username, Password and Realm (The Realm can be either ManagementRealm or ApplicationRealm depending on the level of access required). The script then generates a password hash that is added to the relevant properties file in the format of _user:hash_ . Analysis of the script showed that it was taking the three variables and creating an MD5 hash which is definitely not a secure method.

**Example:**

+ Username: test
+ Password: password
+ Realm: ManagementRealm

This is formatted as ` test:ManagementRealm:password ` and a md5 sum of the resulting string is created as a password hash ` 87c1b764741f1b6d3cac6f95f3a2b226 `

This can be performed in bash using OpenSSL to generate the hash.

``` 
echo -n test:ManagementRealm:password | openssl dgst -md5 -hex
(stdin)= 87c1b764741f1b6d3cac6f95f3a2b226
```

# How can this be abused?

Depending on the permissions of the mgmnt-users.properties file there are different attacks you can perform. If badly configured with world writeable privilileges you can just create your own user hash and add it to the properties file. Instant management access to the application. 

More likely though is that you will find the file with world readable privileges but not writeable. The percludes adding your own user so you'll need to crack the hashes. As we found out earlier the hash is made up of three components, this unfortunately counts out the use of Hashcat & John.

You can do this in bash using a couple of loops for the user and password dictionarys to create the hash and then compare against the hashes in the properties file. I wanted to create something a bit more long term so put together a small python that takes three inputs the mgmnt-users.properties file, a password dictionary and the realm. It follows the same method of checking for hash collisions to determine valid passwords and will run through the RockYou password list in about 5 minutes per user.

![Jboss Cracking](/img/jboss.png)

A copy of the tool can be found here. [Jboss Password Cracker](https://github.com/nop-sec/Jboss-EAP-Hash-Cracker)








