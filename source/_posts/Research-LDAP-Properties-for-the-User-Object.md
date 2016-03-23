layout: post
title: Research LDAP Properties for the User Object
date: 2016-03-23 16:51:50
tags:
- LDAP
category:
- Others
---
Original post: http://www.computerperformance.co.uk/Logon/LDAP_attributes_active_directory.htm
Convert to Markdown tool: http://fuckyeahmarkdown.com/

# LDAP Attributes. Properties Active Directory Users Computers Distinguished name


## Research LDAP Properties for the User Object

This page explains the common LDAP attributes which are used in VBS scripts and PowerShell.&nbsp; Programs like VBScript (WSH), CSVDE and LDIFDE rely on these LDAP attributes to create or modify objects in Active Directory.&nbsp; For example, when you bulk import users you will include the LDAP attributes:&nbsp;dn and sAMAccountName.

* LDAP is the Lightweight Directory Access Protocol.

###  Hall of Fame LDAP Attribute - DN&nbsp; Distinguished Name

As the word 'distinguished' suggests, this is THE LDAP attribute that uniquely defines an object.&nbsp; Each DN must have a different name and location from all other objects in Active Directory.&nbsp; The other side of the coin is that DN provides a way of selecting any object in Active Directory.&nbsp; Once you have selected the object, then you can change its attributes.

Time spent in getting to know the DN attribute will repay many fold.&nbsp; Observe the different components CN=common name, OU = organizational unit.&nbsp; DC often comes with two entries, DC=CP, DC=COM.&nbsp; Note that DC=CP.COM would be wrong.&nbsp; Incidentally in this situation, DC means domain content rather than domain controller.

Another point with the syntax is to check the speech marks; when used with VBScript commands, DN is often enclosed in "speech marks".&nbsp; Even the speech marks have to be of the right type, "double quotes are correct", 'single quotes may be ignored', with unpredictable results.&nbsp; Finally, pay particular attention to commas in distinguished names.

### LDAP Attributes from Active Directory Users and Computers

The diagram below is taken from Active Directory Users and Computers. It shows the commonest LDAP attributes for vbs scripts.

{% asset_img ldap_properties.jpg LDAP Properties %}

When you write your scripts, check how the LDAP attributes map to the Active Directory boxes.

**Research Tip:**  
One of my favourite techniques is to add values in the active directory property boxes, then export using CSVDE.&nbsp; Next, open the .csv file in Excel, search for the value, and read the LDAP field name from row 1.

| LDAP Attribute | Example |
|-------|--------|
|  C |  Country: e.g GB for Great Britain. |   | |
|  CN - Common Name |  CN=Guy Thomas.&nbsp; Actually, this LDAP attribute can be made up from givenName joined to SN. |
|  CN |  Maps to 'Name' in the LDAP provider. Remember CN is a mandatory property.&nbsp; See also sAMAccountName. |
|  description |  What you see in Active Directory Users and Computers.&nbsp; Not to be confused with displayName on the Users property sheet. |
|  displayName |  displayName = Guy Thomas.&nbsp; If you script this property, be sure you understand which field you are configuring.&nbsp; DisplayName can be confused with CN or description. |
|  &nbsp;

{% asset_img Ldap_General_sm.jpg LDAP General SM %}
 Display name -v- Description

**Important LDAP Notes**

Di_s_play name and _D_escription are different

Offi_c_e's LDAP attribute is:

physicalDeliveryOfficeName

E-_m_ail is plain: mail

| LDAP Attribute | Example |
|-------|--------|
|  DN - also distinguishedName |  DN is simply the most important LDAP attribute. CN=Jay Jamieson, OU= Newport,DC=cp,DC=com |
|  givenName |  Firstname also called Christian name |
|  homeDrive |  Home Folder : connect.&nbsp; Tricky to configure |
|  initials |  Useful in some cultures. |
|  name |  name = Guy Thomas.&nbsp; Exactly the same as CN. |
|  objectCategory  |  Defines the Active Directory Schema category. For example, objectCategory = Person |
|  objectClass |  objectClass = User.&nbsp; Also used for Computer, organizationalUnit, even container.&nbsp; Important top level container. |
|  physicalDeliveryOfficeName |  Office! on the user's General property sheet |
|  postOfficeBox |  P.O. box. |
|  profilePath |  Roaming profile path: connect.&nbsp; Trick to set up |
|  sAMAccountName |  This is a mandatory property, sAMAccountName = guyt.&nbsp; The old NT 4.0 logon name, must be unique in the domain.&nbsp;  |
|  sAMAccountName |  If you are using an LDAP provider 'Name' automatically maps to sAMAcountName and CN. The default value is same as CN, but can be given a different value. |
|  SN |  SN = Thomas. This would be referred to as last name or surname. |
|  title |  Job title.&nbsp; For example Manager. |
|  userAccountControl |  Used to disable an account.&nbsp; A value of 514 disables the account, while 512 makes the account ready for logon. |
|  userPrincipalName |  userPrincipalName = guyt@CP.com&nbsp; Often abbreviated to UPN, and looks like an email address.&nbsp; Very useful for logging on especially in a large Forest.&nbsp; Note UPN must be unique in the forest. |
|  wWWHomePage |  User's home page. |


### Examples of Exchange Specific LDAP attributes

| LDAP Attribute | Example |
|-------|--------|
|  homeMDB&nbsp; |  Here is where you set the MailStore |
|  legacyExchangeDN  |  Legacy distinguished name for creating Contacts. In the following example, Guy Thomas is a Contact in the first administrative group of GUYDOMAIN: /o=GUYDOMAIN/ou=first administrative group/cn=Recipients/cn=Guy Thomas  |
|  mail |  An easy, but important attribute.&nbsp; A simple SMTP address is all that is required billyn@ourdom.com |
|  mAPIRecipient - FALSE  |  Indicates that a contact is not a domain user. |
|  mailNickname  |  Normally this is the same value as the sAMAccountName, but could be different if you wished.&nbsp; Needed for mail enabled contacts. |
|  mDBUseDefaults |  Another straightforward field, just the value to:True |
|  msExchHomeServerName |  Exchange needs to know which server to deliver the mail.&nbsp; Example: /o=YourOrg/ou=First Administrative Group/cn=Configuration/cn=Servers/cn=MailSrv |
|  proxyAddresses |  As the name 'proxy' suggests, it is possible for one recipient to have more than one email address.&nbsp; Note the plural spelling of proxyAddresses. |
|  &nbsp;targetAddress  |  SMTP:@ e-mail address.&nbsp; Note that SMTP is case sensitive.&nbsp; All capitals means the default address. |
|  &nbsp;showInAddressBook |  Displays the contact in the Global Address List.  |


### Other Useful LDAP Attributes / Propeties

| LDAP Attribute | Example |
|-------|--------|
| c |  Country or Region |
|  company |  Company or organization name |
|  department |  Useful category to fill in and use for filtering |
|  homephone |  Home Phone number, (Lots more phone LDAPs) |
|  l&nbsp; (Lower case L) |  L = Location.&nbsp; City ( Maybe Office |
|  location |  Important, particularly for printers and computers. |
|  manager |  Boss, manager |
|  mobile |  Mobile Phone number |
|  ObjectClass |  Usually, User, or Computer |
|  OU |  Organizational unit.&nbsp; See also DN |
|  pwdLastSet  |  Force users to change their passwords at next logon  |
|  postalCode |  Zip or post code |
| st |  State, Province or County |
|  streetAddress |  First line of address |
|  telephoneNumber |  Office Phone |
|  userAccountControl |  Enable (512) / disable account (514) |
|  &nbsp;  |   |
|

### Examples of Obscure LDAP Attributes

| LDAP Attribute | Example |
|-------|--------|
|  dNSHostname |   |
|  rID |   |
|  url |   |
|  uSNCreated, uSNChanged |   |
|

 To discover more LDAP attributes, go to the command prompt, type:

```
CSVDE -f Exportfile.csv
```
Then open Exportfile.csv with Excel.exe.&nbsp; &nbsp;Alternatively, use ADSI Edit and right-click the container objects.
