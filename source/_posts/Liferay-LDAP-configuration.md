title: Liferay - LDAP configuration
date: 2015-12-18 18:05:44
comments: true
tags:
- Liferay
- DLAP
categories:
- Liferay

---
1) Concept http://www.jianshu.com/p/a41b41977f95
2) Install LDAP. 

Download http://directory.apache.org/apacheds/downloads.html

Install. My test is http://directory.apache.org/apacheds/download/download-linux-deb.html 64bit

After install, start apacheds with sudo or root 

    # /etc/init.d/apacheds-2.0.0-M20-default start

    # /etc/init.d/apacheds-2.0.0-M20-default status 

then check if it starts. My first starting failed. It's because /var/lib/apacheds-2.0.0-M20/default/conf/wrapper-instance.conf

    # Path to java executable 

    # Override the JRE used

    # wrapper.java.command=

uncomment wrapper.java.command, set it to your java executable file

    wrapper.java.command=/home/bejond/tools/java-tools/jdk1.7.0_40/jre/bin/java

then try another start, status.

My second starting failed again. I look the log

    INFO  | jvm 1    | 2015/11/18 17:43:30 | [17:43:30] WARN [org.apache.directory.server.config.ConfigPartitionInitializer] - Conflict in selecting configuration source, both config.ldif and ou=config exist delete either one of them and restart the server

so just delete all folders and *.ldif files inside /var/lib/apacheds-2.0.0-M20/default/conf/ but log4j.properties and wrapper-instance.conf.

Then start apacheds successfully.
3) Add test data in Apacheds.

I use ApacheDirectoryStudio to do this.

Download https://directory.apache.org/studio/downloads.html

After unzip it, it needs jre to run it. I copy my jre into ApacheDirectoryStudio, just the same folder of ApacheDirectoryStudio.ini.
a) After ApacheDirectoryStudio starts, new connection, leave authentication blank.

Then new Entry behind dc=example,dc=com.

{% asset_img LDAP-New-Entry.png LDAP-New Entry %}
{% asset_img LDAP-new-Entry-ou.png LDAP-new Entry ou %}
{% asset_img LDAP-new-Entry-ou-new.png LDAP-new　Entry ou 2 %}

b) After creating ou, let's import users.

i) Right click "Root DSE" -> Import -> LDIF import...

Check the things like this
{% asset_img LDAP-import.png LDAP-import %}

ii) Import the data that saved in a.ldif file

	
```
dn: cn=test1,ou=Users,dc=example,dc=com
 
cn: test1
 
givenName: test1givenName
 
objectClass: person
 
objectClass: inetOrgPerson
 
objectClass: organizationalPerson
 
objectClass: top
 
sn: test1lastName
 
userPassword:: e1NTSEF9dVplNG13WkV2ZVNVT3RrZlh0aW5iK0cyM0g0RjZib0EzNDNOVWc9PQ==
```

 You can see I didn't add emain for users. If you don't want to add email for users, please add the properties in portal-ext.properties
	
```
users.email.address.required=false
 
users.email.address.auto.suffix=@no-emailaddress.com
```

3) Add LDAP server in Liferay.

Start Liferay 6.2.10 sp13

Go to control panel -> Portal Settings -> Authentication -> LDAP, check "Enabled", "Import Enabled", then click "Add" to add LDAP Servers.
{% asset_img LDAP-server.png LDAP server %}

In the Edit LDAP Server, just click "Reset Values", liferay would set all values in defaut values. You need other changing to adapt to LDAP server.

After that, click "Test LDAP Connection", "Liferay has successfully connected to the LDAP server." would show.

If it saids failed, please check if LDAP server starts, if liferay server can connect to LDAP server, if the settings are correct.

After settings, we can try to search users in LDAP. Click "Test LDAP Users".
{% asset_img LDAP-search-for-users.png LDAP-search-for-users.png %}
The blue info says missing the required attribtes, just ignore it, because we've set properties in portal-ext.properties file.

Save the LDAP server, Save portal settings.

4) Import LDAP users

Go to control panel -> Configuration -> Server Administration -> Script, select language "Javascript". Type the code in Script and Execute.

    Packages.com.liferay.portal.security.ldap.PortalLDAPImporterUtil.importFromLDAP(); // This is for ee6.2

In trunk, the PortalLDAPImporterUtil has been removed, so we need to import user with other API.

    In trunk, there's a bug that script only supports Groovy now (2015-12-04). Please change the code to

    com.liferay.portal.security.exportimport.UserImporterUtil.importUsers()

 

Then we can see that users are imported into Liferay!

{% asset_img LDAP-Users-in-database.png LDAP-Users-in-database.png %}
