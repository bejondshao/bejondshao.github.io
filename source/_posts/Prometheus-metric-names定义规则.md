layout: post
title: Prometheus metric names定义规则
tags:
  - Prometheus
  - Metric names
categories:
  - DevOps
date: 2018-05-16 18:13:00
---
**Metrics定义规则**

* Metrics名字以应用名为命名空间，即该metric是从哪个应用来的，比如：

  * pms_reservations_total

  * crs_failed_requests_total

* Metrics必须有确定的单位，并且是基本单位，比如seconds, bytes, meters。而不是milliseconds, megabytes, kilometers。

* 每个单位都应该用复数，并且可以加描述性的后缀，比如total：

  * http_request_duration_seconds

  * node_memory_usage_bytes

  * http_requests_total（针对累加性质的计数集体）

* Metrics应该代表一个整体性的描述，比如：

  * 请求时长

  * 数据bytes大小

  * 瞬时资源使用百分比

sum()或者avg()之类的指标用于创建metric是很好的方式。Metric应该是有特定意义的词汇。如果一个metric无法描述清数据类型，那就分成几个metrics。

**Labels**

使用labels区分metric不同的特点，比如：

* apt_http_requests_total - 区别不同的请求类型：`type="create|update|delete"`

* api_request_duration_seconds - 区别请求的阶段：`stage="extract|transform|load"`

不要把label放到metric  name中，这会造成冗余和混乱。

注意：每一个独立的lable键值对组合都代表一个新的time series，这很有可能增加数据量。不要用labels存储数据量大的指标，比如userId, email等。也就是说labels一般用作内容可能性比较少的指标。

**基本单位**

Prometheus使用部分基本单位列表：

Time: seconds

Temperature: celsius

Length: meters

Bytes: bytes

Bits: bytes

Percent: ratio() (值为0~1, ratio通常不用做后缀，但是A_per_B类型除外，比如disk_usage_ratio会这么使用)

Voltage: volts

Electric current: amperes

Energy: joules

Weight: grams