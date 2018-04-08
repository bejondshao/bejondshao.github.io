layout: post
title: 'Simple String Null, Empty and Blank Issues'
tags:
  - Java
categories:
  - Java
date: 2018-04-08 20:40:00
---
@ Today I meet an interesting issue about String. First, let's guess what's the result of the code below:
```
@Test
public void testConcat() {
  String a = null;
  String b = null;
  String c = a + b;
  System.out.println("c = " + c);
}
```

The answer is:

```
  c = nullnull
```

Yes, String c becomes "nullnull", which is not our desired result. And how to avoid this?

```
  a = a == null ? "" : a;
  b = b == null ? "" : b;
  String d = a + b;
  System.out.println("d = " + d);
```

This time, String d is "". It also happends to `StringBuffer.append(String s)` if s is null.

We should be patient to String concat behavior.

@ If we create a Restful API that accepts two parameters, String firstName and String lastName. But we need to make sure that they are not all empty. We can set default value to attribute when defining to the parameter to avoid "null" issue. Like:

```
@XmlAttribute(name = "LastName")
@JsonProperty("lastName")
private String lastName = "";

@XmlAttribute(name = "FirstName")
@JsonProperty("firstName")
private String firstName = "";

```

@ Another use case is that we usually want to check if a String is not null or "". We usually use
`org.apache.commons.lang3.StringUtils.isNotEmpty(final CharSequence cs)`. But it just checks if cs is null or "". It treats a whitespace string as not empty. We can trim cs before checking, but it's not a good way because:
1. The method points up "final".
2. We have to trim cs all the time if we don't change cs at the first place.

Good news is there's a better method `org.apache.commons.lang3.StringUtils.isNotBlank(final CharSequence cs)`. It treats whitespace string as empty too.:slightly_smiling_face: