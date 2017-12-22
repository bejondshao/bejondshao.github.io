---
layout: post
title: How to check if IP is in IP range
date: 2017-12-22 15:38:20
tags:
- IP

category:
- Java

---

I work on writing services, the client validation contains IP checking. But the customer uses cluster base framework to invoke our services, there are multiple clients with different IPs obviously.
First I change the configuration page and make it able to save multiple IPs with "," seperated, like "192.168.1.12,192.168.1.25". But there are hundreds of IPs. Some of them are in the same network segment, it's not right to write out all the IPs. Right configuration is like "23.25.6.8,192.168.1.20,42.12.56.0/24,124.1.53.0/24".

As the validation needs to be able check multiple IPs and IP range. So I divide them into two parts: one part is real IPs, the other part is IP ranges. Real IPs checking is simple, just spilt it and check if the IP array contains client IP. Let's move to IP range checking.

Way one: I come up with an idea that analyze IP ranges and generate new regex patterns. I search regext about IP checking, it seems complex, even dynamic generating. By the way, regex does not play well with me:joy:. I believe even I make it, it probably gets me more troubles. It'll be hard to maintain for others.

Way two: Converting IP to int numbers and check if the IP is in the range. [Here](https://stackoverflow.com/a/47766603/3908814) is a solution. It might take you a little time to understand the algorithm.
```
 public int intOfIpV4(String ip) {
        int result = 0;
        byte[] bytes = IPAddressUtil.textToNumericFormatV4(ip); // 1
        if (bytes == null) {
            return result;
        }
        for (byte b : bytes) {
            result = result << 8 | (b & 0xFF);
        }
        return result;
    }
```
1. bytes is 4 length array, each byte means a IP part, divided by ".". Eg, IPAddressUtil.textToNumericFormatV4("")