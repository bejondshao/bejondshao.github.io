layout: post
title: "After install liferay sp9(LPE-10066), web contents don't show to guest"
date: 2015-04-23 16:15:04
tags:
- Liferay
category:
- Liferay
---
Recently, some customers met an issue that after installing sp9 or any conponent that contains LPE-10066 ( LPS-40597), web contents won't show to guest or site member.

The reproduction steps:
1. Setting "journal.article.view.permission.check.enabled=true" in portal-ext.properties. Start a clean 6210-sp7 bundle and drag a web content portlet to page, add one web content. Visit site with guest, the web content shows.
2. Setting "journal.article.view.permission.check.enabled=true" in portal-ext.properties. Start another 6210-sp9 bundle, connecting with the same database. Visit site with guest, there's no web content.

The root cause is this commit https://github.com/shinnlok/liferay-portal/commit/896de0dc6a21b19ff1bcc9223677ffc6013a1274. For journal.xml file, they add view permission to guest and site member role. And remove view permission under <guest-unsupported> tag. We can find the differences between the screenshots.

But journal.xml file only works when init the first web content in a site. So this file won't work in customer's old site. That's the reason.

Besides, the permission check mechanism is changed.
Before LPS-40597, portal would check if guest (or site member) Doesn't have view permission, if he doesn't have, he won't see web content; if undefined, he will see web content.
After LPS-40597, portal would check if guest (or site member) Do have view permission, if he do have, he will see web content; if undefined, he won't see web content.
{% asset_img sp7-guest-no-view-permission-setting-under-resource-permissions.png Before install sp9 %}

In sp7, there's no view setting under "Resource Permissions".
{% asset_img sp9-guest-view-permission-setting-under-resource-permissions.png After install sp9 %}

In sp9, there's view permission setting under "Resource Permissions". We can see the view permission is unchecked. So with new permission checking mechanism, guest or site member doesn't have view resource permission, so he can't view web contents, even though we set each web content is viewable by guest, there's no use.

The solution is setting this view permission to guest and site member. The issue we talk about only happens in the site created before sp9, new site create after installing sp9 will work fine, because journal.xml file works later.

I think this can make the issue clear.