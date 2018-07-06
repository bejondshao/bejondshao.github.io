layout: post
title: SCIM(System for Cross-domain Identity Management)
tags:
  - SCIM
  - Identity Management
categories:
  - Others
author: ''
date: 2018-07-06 15:51:00
---
@ SCIM(System for Cross-domain Identity Management) 2是一套开放式API，用于管理身份信息。

@ SCIM意在更方便的管理用户身份和web服务，尤其是云服务。其意在减少构建用户管理模块的开销，减少工作量。

@ Model

SCIM 2.0是以Resource为基础设计的一套模型。Resource有id, externalId和meta属性（meta属性存储记录的基本信息，比如resourceType, created, lastModified, location, version等）。根据RFC7643定义的User, Group继承Resource，具有额外的属性，EnterpriseUser继承User。

{% asset_img model.png Model %}

@ User举例

```

{
  "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
  "id":"2819c223-7f76-453a-919d-413861904646",
  "externalId":"bjensen",
  "meta":{
    "resourceType": "User",
    "created":"2011-08-01T18:29:49.793Z",
    "lastModified":"2011-08-01T18:29:49.793Z",
    "location":"https://example.com/v2/Users/2819c223...",
    "version":"W\/\"f250dd84f0671c3\""
  },
  "name":{
    "formatted": "Ms. Barbara J Jensen, III",
    "familyName": "Jensen",
    "givenName": "Barbara",
    "middleName": "Jane",
    "honorificPrefix": "Ms.",
    "honorificSuffix": "III"
  },
  "userName":"bjensen",
  "phoneNumbers":[
    {
      "value":"555-555-8377",
      "type":"work"
    }
  ],
  "emails":[
    {
      "value":"bjensen@example.com",
      "type":"work",
      "primary": true
    }
  ]
}
```

@ Group表示user的集合，group也可以包含其他group。

```

{
  "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
  "id":"e9e30dba-f08f-4109-8486-d5c6a331660a",
  "displayName": "Tour Guides",
  "members":[
    {
      "value": "2819c223-7f76-453a-919d-413861904646",
      "$ref": "https://example.com/v2/Users/2819c223-7f76-453a-919d-413861904646",
      "display": "Babs Jensen"
    },
    {
      "value": "902c246b-6245-4190-8e05-00816be7344a",
      "$ref": "https://example.com/v2/Users/902c246b-6245-4190-8e05-00816be7344a",
      "display": "Mandy Pepperidge"
    }
  ],
  "meta": {
    "resourceType": "Group",
    "created": "2010-01-23T04:56:22Z",
    "lastModified": "2011-05-13T04:42:34Z",
    "version": "W\/\"3694e05e9dff592\"",
    "location": "https://example.com/v2/Groups/e9e30dba-f08f-4109-8486-d5c6a331660a"
  }
}
```

@ **Operations**

SCIM提供几个简单的REST API，用于管理资源           

* Create: POST https://example.com/{v}/{resource}

* Read: GET https://example.com/{v}/{resource}/{id}           

* Replace: PUT https://example.com/{v}/{resource}/{id}

* Delete: DELETE https://example.com/{v}/{resource}/{id}           

* Update: PATCH https://example.com/{v}/{resource}/{id}

* Search: GET https://example.com/{v}/{resource}?ﬁlter={attribute}{op}{value}&sortBy={attributeName}&sortOrder={ascending|descending}    

* Bulk（批量）: POST https://example.com/{v}/Bulk

@ 为了简化沟通流程，SCIM提供三个端点接口，支持特定的属性和特性。      

* GET /ServiceProviderConfig 验证数据合法性，指定验证信息，数据类型。

* GET /ResourceTypes 发现可用的资源。

* GET /Schemas 读取资源和扩展的属性。

﻿@ Create Request

创建一个User。下面例子请求URL包含version，可以看出SCIM API支持多个版本的服务，可以通过ServiceProviderConfig查找可用的服务。

```

POST /v2/Users  HTTP/1.1
Accept: application/json
Authorization: Bearer h480djs93hd8
Host: example.com
Content-Length: ...
Content-Type: application/json

{
  "schemas":["urn:ietf:params:scim:schemas:core:2.0:User"],
  "externalId":"bjensen",
  "userName":"bjensen",
  "name":{
    "familyName":"Jensen",
    "givenName":"Barbara"
  }
}
```

@ Create Response

预定的响应内容和HTTP 201表明Resource创建成功。id和meta数据也同时返回，location表示可以在那里获取到用户。

```

HTTP/1.1 201 Created
Content-Type: application/scim+json
Location: https://example.com/v2/Users/2819c223-7f76-453a-919d-413861904646
ETag: W/"e180ee84f0671b1"

{
  "schemas":["urn:ietf:params:scim:schemas:core:2.0:User"],
  "id":"2819c223-7f76-453a-919d-413861904646",
  "externalId":"bjensen",
  "meta":{
    "resourceType":"User",
    "created":"2011-08-01T21:32:44.882Z",
    "lastModified":"2011-08-01T21:32:44.882Z",
    "location": "https://example.com/v2/Users/2819c223-7f76-453a-919d-413861904646",
    "version":"W\/\"e180ee84f0671b1\""
  },
  "name":{
    "familyName":"Jensen",
    "givenName":"Barbara"
  },
  "userName":"bjensen"
}
```

@ Get Request

使用HTTP GET发送到端点，查询资源

```

GET /v2/Users/2819c223-7f76-453a-919d-413861904646 HTTP/1.1
Host: example.com
Accept: application/scim+json
Authorization: Bearer h480djs93hd8
```

@ Create Response

返回GET获取的资源。Etag头用于阻止并发修改资源。

```

HTTP/1.1 201 Created HTTP/1.1
Content-Type: application/scim+json
Location: https://example.com/v2/Users/2819c223-7f76-453a-919d-413861904646
ETag: W/"e180ee84f0671b1"

{
  "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
  "id":"2819c223-7f76-453a-919d-413861904646",
  "externalId":"bjensen",
  "meta":{
    "resourceType": "User",
    "created":"2011-08-01T18:29:49.793Z",
    "lastModified":"2011-08-01T18:29:49.793Z",
    "location":"https://example.com/v2/Users/2819c223...",
    "version":"W\/\"f250dd84f0671c3\""
  },
  "name":{
    "formatted": "Ms. Barbara J Jensen, III",
    "familyName": "Jensen",
    "givenName": "Barbara",
    "middleName": "Jane",
    "honorificPrefix": "Ms.",
    "honorificSuffix": "III"
  },
  "userName":"bjensen",
  "phoneNumbers":[
    {
      "value":"555-555-8377",
      "type":"work"
    }
  ],
  "emails":[
    {
      "value":"bjensen@example.com",
      "type":"work",
      "primary": true
    }
  ]
}
```

@ Filter Request

通过加查询条件，查询资源列表。SCIM支持equals, contains, starts with等等。对查询结果还可以排序，可以返回特定的属性以及返回指定数量的资源。

* https://example.com/{resource}?ﬁlter={attribute} {op} {value} & sortBy={attributeName}&sortOrder={ascending|descending}&attributes={attributes}

* https://example.com/Users?ﬁlter=title pr and userType eq “Employee”&sortBy=title&sortOrder=ascending&attributes=title,username

@ Filter Response

```

{
  "schemas":["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
  "totalResults":2,
  "Resources":[
    {
      "id":"c3a26dd3-27a0-4dec-a2ac-ce211e105f97",
      "title":"Assistant VP",
      "userName":"bjensen"
    },
    {
      "id":"a4a25dd3-17a0-4dac-a2ac-ce211e125f57",
      "title":"VP",
      "userName":"jsmith"
    }
  ]
}
```
@ SCIM不涉及验证（Authentication）和授权（Authorization），想要实现验证和授权功能，参考[RFC 7644 2. Authentication and Authorization](https://tools.ietf.org/html/rfc7644#section-2).

[[1] simplecloud SCIM](http://www.simplecloud.info/)
[[2] RFC 7644](https://tools.ietf.org/html/rfc7644)