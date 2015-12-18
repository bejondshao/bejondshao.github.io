title: Last Name became required in Liferay 7.0
date: 2015-12-18 18:39:15
tags:
- liferay
categories:
- Liferay

---

Last name is not required in Liferay 6.2.10, while becomes required in Liferay 7.0. How to make this field required?

I search for /liferay-portal/modules/apps/directory/directory-web/src/main/resources/META-INF/resources/user/details.jsp

There are email-address, birthday, job-title, etc. But there's no prefix, first-name, middle-name, last-name, suffix. I can't find last-name in any jsp file. So I guess it's setting in Java code, then generate it as jsp.

Then I do a search for "last-name". I find it is in Language.properties file in property "lang.user.name.field.names=prefix,first-name,middle-name,last-name,suffix". 

Do another search for "lang.user.name.field.names". It locates in FullNameDefinitionFactory
1
	
String[] fieldNames = StringUtil.split(LanguageUtil.get(locale, "lang.user.name.field.names"));

 so these fields are splitted into a String array.Then I find
1
	
fullNameField.setRequired(fullNameDefinition.isFieldRequired(userNameField));

 

What's fullNameField? It's an object of FullNameDefinition, the FullNameDefinition has _requiredFields. Then I set a breakpoint, _requeiredFields contains two values "first-name" and "last-name".

But where did portal set this attribute?Let's do a full search for "FullNameDefinitionFactory", /liferay-portal/portal-web/docroot/html/taglib/ui/user_name_fields/page.jsp

We can see that user name values are generated here, each field would be added "required" if it's required.But when and why last name became required? We see there's an exception in the page.jsp

The message says "and-last-name".

Let's blame this line and find the log.This line and the whole block of code were added in      LPS-55591 Combine jsp fragments in page.jsp. The whole block of code is added in this commit. Besides, details_user_name.jspf contains this line before, but it's removed in this commit. So we should blame this file. (We need smartgit or gitk to do this.) This line of code was changed in LPS-55364. Now we can go to trunk and search for the LPS.After search for the log, the diff of the line is

The message says "and-last-name".

Let's blame this line and find the log.This line and the whole block of code were added in      LPS-55591 Combine jsp fragments in page.jsp. The whole block of code is added in this commit. Besides, details_user_name.jspf contains this line before, but it's removed in this commit. So we should blame this file. (We need smartgit or gitk to do this.) This line of code was changed in LPS-55364. Now we can go to trunk and search for the LPS.After search for the log, the diff of the line is

It means last name is added before LPS-55364. Well, let go deep, go deep. Finally, we find this commit LPS-16453 Make last name optionalgit-svn-id: svn://svn.liferay.com/repos/public/portal/trunk@81911 05bdf26c-840f-0410-9ced-eb539d925f36  

So we go to source code, search for users.last.name.required in portal.properties, see if we can find it.

Of course, it's gone. Don't worry. We just need to log for portal.properties to see when this property is deleted. It's LPS-54956 (Implement the correct handling of defining required fields). But during research, required is set in LPS-55364 (Create ContactNameException and add appropriate exceptions as inner classes).Finally, we can find in Language.properties

	
lang.user.name.required.field.names=last-name

 If you want to set last name not required, you can remove this property and to go portal-impl/ run "ant build-lang". Then do "ant deploy" to apply the change.

 

so, last-name is required, in LPS-51819 (Add multi-cultural support to user admin portlet)

But all in all, if we find this line at the first, we would not need to search the whole things. Org.