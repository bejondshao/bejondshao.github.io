layout: post
title: Liferay - Microsofe Active Directory Configuration
tags:
  - Liferay
  - LDAP
  - Active Directory
category:
  - Liferay
categories:
  - Java
  - Liferay
date: 2016-03-24 14:18:00
---
Before config this, please view properties in AD. http://bejondshao.github.io/2016/03/23/Research-LDAP-Properties-for-the-User-Object/

There are so many traps on config Liferay with Active Directory. I'm gonna show you the right configuration.

1. go to Control Panel -> Configuration -> Portal Settings -> Authentication, choose "LDAP" tab.
2. Check Enabled. Import Enabled, Export Enabled can also be checked if you want.
3. Click "Add" to add a LDAP server.
4. On the new form, name it, choose "Apache Directory Server(Yes, click "Apache Directory Server", not "Microsoft Active Directory Server")" and click "Reset Values", it will create default values of config.
5. optmize values.
###### Connection section
@ Base Provider URL: change to `ldap://ip_of_AD_server:389`, the port is 389.
@ Base DN: change to your own, eg: `ou=org1,dc=bejond,dc=com`.
@ Principal: authentication for AD server.
eg: `cn=administrator,cn=users,dc=bejond,dc=com` (it's not case sensitive)
or you can use user logon name eg:
`MYDOMAIN\administrator`
@ Credentials: password of the user.
Click "Test LDAP Connection" to test if you can logon AD via this user.
###### Users section
@ Authentication Search Filter: change it to `(userprincipalname=@email_address@)`.
@ Import Search Filter: leave it as default `(objectClass=person)`
###### User Mapping section
@ UUID: leave it blank.
@ *Screen Name*: If you just import user to Liferay, just map any field in AD, eg "sAMAccountName"("sAMAccountName" is User login name(pre-Windows 2000)), "cn", please make sure the filed is unique in AD. But if you also want to export user to AD, just map screen name with `cn`, there's no second choice. Because liferay would put screen name to add user, while cn is mandatory property. So unique screen name maps to cn suits for export user to AD. If you try other fields like "sAMAccountName", you will get
```
06:47:26,023 ERROR [http-bio-8690-exec-38][render_portlet_jsp:132] null
javax.naming.InvalidNameException: sAMAccountName=ls15,ou=export,dc=mydomain,dc=com: [LDAP: error code 64 - 00002073: NameErr: DSID-03050AAB, problem 2005 (NAMING_VIOLATION), data 0, best match of:_	'sAMAccountName=ls15,ou=export,dc=mydomain,dc=com'__]; remaining name 'sAMAccountName=ls15,ou=export,dc=mydomain,dc=com' [Sanitized]
```
But take care that, cn in AD is for "First Name + Last Name" by default, it contains whitespace, so it can't be imported to liferay as screen name. So you need to change it in AD.

@ Email Address: change it to `userprincipalname`. It can be mapped with "mail" or "userprincipalname". "mail" is email in AD, "userprincipalname" is User logon name. But if "Authentication Search Filter" is `(userprincipalname=@email_address@)`, then Email Address should be `userprincipalname`. Portal login with email, so this should be unified.
@ Password: leave it as default `userPassword`.
@ First Name: leave it as default `givenName`.
@ Middle Name: leave it blank.
@ Last Name: leave it as default or blank `sn`.
@ Full Name: leave it as blank, you can also set to other attributes.
@ Job Title: leave it as default `title`.
@ Status: You can map "status" with "useraccountcontrol" and try to export user to AD. You will get
```
javax.naming.OperationNotSupportedException: [LDAP: error code 53 - 0000052D: SvcErr: DSID-031A0FC0, problem 5003 (WILL_NOT_PERFORM)
```
Because a certificate must be installed on the domain controller and tied to AD, and then the client must support an SSL connection to AD for LDAP. The other attributes in the schema do not have this restriction and can be updated without a secure connection.
So as far, let's leave status blank.
@ Group, Portrait, Custom User Mapping, Custom Contact Mapping: leave them blank
Now click "Test LDAP Users", you'll see users from AD
###### Groups section
@ Import Search Filter: change it to `(objectClass=group)`
###### Group Mapping section
@ Group Name: leave it as default `cn`
@ Descrption: leave it as default `description`
@ User: leave it as default `uniqueMember`
Now click "Test LDAP Groups", you'll see groups from AD
###### Export section
@ Users DN: change to the group or organization unit you want to export `ou=export,dc=bejond,dc=com`
@ User Default Object Classes: leave it as default `top,person,inetOrgPerson,organizationalPerson`
@ Groups DN: change it according to your domain, eg `dc=bejond,dc=com`
@ Group Default Object Classes: leave it as default `top,groupOfUniqueNames`
Then click "Save", all settings are done.

6. Let's test export. Go to Users and Groups, create a user and save. If your config is right, you'll see the user you created in AD.

Liferay would export user when the user is added or updated. If you want to export existing users to LDAP, you can run a script on updating existing users with changing a non-important field.