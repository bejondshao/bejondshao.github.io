layout: post
title: Liferay - ldap password policy - need specific error messages from AD on failed binds
date: 2015-12-18 19:34:23
category: 
- Liferay
tags:
- Liferay
- LDAP
---
Customer has configured Liferay with Microsoft Active directory through LDAP and have below queries regarding LDAP password policies:

1) How to display MS Active Directory password policy error message into liferay?(ex. if password expires in AD then password message expiry message should show in Liferay while login)

2) If we have multiple directory servers (ADS,OpenDs..etc) in our environment, each directory servers having unique users and different password policies, how can we integrated this password polices in liferay & show corresponding Directory Server's ( For Example ADS1, ADS2, OpenDs) error messages.

3) How "Ldap password policies" works, specially in below Use Case(Ldap Required and Enable option checked):

If we uncheck the LDAP password policies, now if user password got expired in AD and we try to login in Liferay with AD password it will show "Authentication failed", which means it is still using LDAP password policy while we have unchecked it.

CSE Research:

I did some research and found, to display the LDAP (AD) error message into liferay as per the AD password policy, there are some property into Liferay portal.properties which need to map with AD. Below are the properties from portal.properties:

    Set these values to be a portion of the error message returned by the
    appropriate directory server to allow the portal to recognize messages
    from the LDAP server. The default values will work for Fedora DS.
    #
    ldap.error.password.age=age
    ldap.error.password.expired=expired
    ldap.error.password.history=history
    ldap.error.password.not.changeable=not allowed to change
    ldap.error.password.syntax=syntax
    ldap.error.password.trivial=trivial
    ldap.error.user.lockout=retry limit

Request/Task:

1) Please analyze the above property and let us know, what values need to be set in these properties to display the AD message at Liferay end.
2) How "Ldap password policies" will work in the above point 3 use case?
3) How to integrated the password polices in liferay & show corresponding Directory Server's error messages( For Example ADS1, ADS2, OpenDs).

=================================================================================================

    3) How "Ldap password policies" works, specially in below Use Case(Ldap Required and Enable option checked):
    If we uncheck the LDAP password policies, now if user password got expired in AD and we try to login in Liferay with AD password it will show "Authentication failed", which means it is still using LDAP password policy while we have unchecked it.

If you check "Enabled" and "Required", "Required" means check this box if LDAP authentication is required. Liferay will then not allow a user to log in unless he or she can successfully bind to the LDAP directory first. Uncheck this box if you want to allow users with Liferay accounts but no LDAP accounts to log in to the portal.

There's another setting "User LDAP Password Policy", it is called in UserLocalServiceImpl.checkPasswordExpired(User),

	public void checkPasswordExpired(User user)
		throws PortalException, SystemException {
		if (LDAPSettingsUtil.isPasswordPolicyEnabled(user.getCompanyId())) {
			return;
		}
		PasswordPolicy passwordPolicy = user.getPasswordPolicy();
		// Check if password has expired
		if (isPasswordExpired(user)) {
			int graceLoginCount = user.getGraceLoginCount();
			if (graceLoginCount < passwordPolicy.getGraceLimit()) {
				user.setGraceLoginCount(++graceLoginCount);
				userPersistence.update(user);
			}
			else {
				user.setDigest(StringPool.BLANK);
				userPersistence.update(user);
				throw new PasswordExpiredException();
			}
		}
		// Check if user should be forced to change password on first login
		if (passwordPolicy.isChangeable() &&
			passwordPolicy.isChangeRequired()) {
			if (user.getLastLoginDate() == null) {
				user.setPasswordReset(true);
				userPersistence.update(user);
			}
		}
	}

You can see if you check "User LDAP Password Policy", portal would not check passwordPolicy. If you uncheck "User LDAP Password Policy", portal would check the password policy of this user and check for him. That's why customer uncheck "User LDAP Password Policy", failed to login. It's because the user doesn't pass his password policy in Liferay. We can ask customer to check if there's any other password policy in liferay.

Why do this happen? Please see

        Available authenticators are:
        com.liferay.portal.security.auth.LDAPAuth
        #
        See the LDAP properties to configure the behavior of the LDAPAuth class.
        #
        auth.pipeline.pre=com.liferay.portal.security.auth.LDAPAuth
        #auth.pipeline.post=

    #

        Set this to true to enable password checking by the internal portal
        authentication. If set to false, you're essentially delegating password
        checking to the authenticators configured in "auth.pipeline.pre" and
        "auth.pipeline.post" settings.
        #
        auth.pipeline.enable.liferay.check=true

Where does portal use LDAP to authenticate user?
It locates in LDAPAuth.authenticateAgainstPreferredLDAPServer(long, String, String, long, String).

result = authenticate(
   ldapServerId, companyId, emailAddress, screenName, userId,
   password);

so in

authenticate(
   ldapServerId, companyId, emailAddress, screenName, userId,
   password)

, LDAP server will return validation result, or validation error message. Then portal would check error message with the key words

        Set these values to be a portion of the error message returned by the
        appropriate directory server to allow the portal to recognize messages
        from the LDAP server. The default values will work for Fedora DS.
        #
        ldap.error.password.age=age
        ldap.error.password.expired=expired
        ldap.error.password.history=history
        ldap.error.password.not.changeable=not allowed to change
        ldap.error.password.syntax=syntax
        ldap.error.password.trivial=trivial
        ldap.error.user.lockout=retry limit

For the specific key words for the error message, I'm sorry I don't know really. Customer can catch the error message that throwed from LDAP server, try to get the key words in different situagions and set the key words to the properties above.

How to enable LDAP authentication?
So in order to disable internal portal authentication, please set

auth.pipeline.enable.liferay.check=false

    2) If we have multiple directory servers (ADS,OpenDs..etc) in our environment, each directory servers having unique users and different password policies, how can we integrated this password polices in liferay & show corresponding Directory Server's ( For Example ADS1, ADS2, OpenDs) error messages.

Portal allows adding multiple directory servers in control panel. If customer disables internal portal authentication, different users will do different authentication according to his LDAP server.

So please try to help customer to enable LDAP server authentication first, then try to help customer to get error messages and set right key words.