---
title: redis命令简介 列表
date: 2020-07-08 13:25:45
categories: redis
index_img: /img/articles/redis.jpg
tags:
	- redis
	- 列表
	- list
---

# 列表

`redis`的列表（`list`）是一种线性的有序结构，可以按照元素被推入列表中的顺序来存储元素。这些元素可以是文字数据，也可以是二进制数据。

列表中的元素可以重复出现。



## lpush

**作用**：将一个或者多个元素推入到列表左端

**格式**：`lpush list item [item ...]`

**扩展**：

在完成操作后，命令回返回列表中当前包含的元素的数量

如果在执行命令时给定了多个元素，则命令将按照元素给定的顺序，从左到右依次将所有给定的元素推入列表的左端

如果给定的列表并不存在，则命令将自动创建一个空列表，然后将元素推入到刚刚创建的列表中



## rpush

**作用**：将一个或者多个元素推入到列表右端

**格式**：`rpush list item [item ...]`

**扩展**：

在完成操作后，命令回返回列表中当前包含的元素的数量

如果在执行命令时给定了多个元素，则命令将按照元素给定的顺序，从左到右依次将所有给定的元素推入列表的右端

如果给定的列表并不存在，则命令将自动创建一个空列表，然后将元素推入到刚刚创建的列表中



## lpushx & rpushx

**作用**：只对已经存在的列表执行推入操作

**格式**：

`lpushx list item`

`rpushx list item`

**扩展**：
如果成功执行推入操作，两个命令将返回列表当前的长度

如果推入操作未能成功执行，两个命令将返回`0`

两个命令每次都只能推入一个元素，尝试向命令指定多个元素将引发错误



## lpop

**作用**：弹出列表最左端的元素，并将被弹出的元素返回给用户

**格式**：`lpop list`



## rpop

**作用**：弹出列表最右端的元素，并将被弹出的元素返回给用户

**格式**：`rpop list`



## rpoplpush

**作用**：将源列表最右端的元素弹出，然后将被弹出的元素推入到目标列表的左端

**格式**：`rpoplpush source target`

**扩展**：

命令会返回被弹出的元素作为结果

命令允许源列表与目标列表设置为同一个列表，这样，命令的效果相当于将列表最右端的元素变为最左端的元素

如果源列表并不存在，则命令将返回一个空值，表示命令执行失败

如果目标列表为空，源列表非空，则命令将正常执行操作



## llen

**作用**：获取列表的长度，即列表中包含的元素的数量

**格式**：`llen list`

**扩展**：

对于不存在的列表，命令将返回`0`作为结果



## lindex

**作用**：获取指定索引上的元素

**格式**：`lindex list index`

**扩展**：

`redis`列表中包含的每个元素，都有与之相对应的正数索引与负数索引

1. 正数索引从列表的左端开始计算，最左端元素索引为`0`，左端第二个元素索引为`1`，依此类推
2. 负数索引从列表的右端开始计算，最右端元素的索引为`-1`，右端第二个元素索引为`-2`，依此类推

命令接受一个列表和一个索引作为参数，返回列表在指定的索引上的元素，索引可以是正数，也可以是负数

对于一个长度为`N`的非空列表来说：

1. 正数索引必然是大于等于`0`，小于等于`N-1`
2. 负数索引必然是小于等于`-1`，大于等于`-N`

如果命令给定的索引超出这个范围，则命令将返回空值，表示给定索引上并不存在任何的元素



## lrange

**作用**：获取指定索引范围上的元素

**格式**：`lrange list start end`

**扩展**：

命令返回列表从开始索引（`start`）到结束索引（`end`）范围内的所有元素，其中开始索引与结束索引对应的元素也会包含在命令返回的元素中。

一个快捷的获取列表中包含的所有元素的方法，就是使用`0`作为开始索引，`-1`作为结束索引，调用`lrange`命令。

如果用户给定的开始索引与结束索引都超出范围，则命令将返回空列表作为结果

如果用户给定的开始索引或者结束索引其中的一个超出范围，则命令将对超出范围的索引进行修正（修正为`0`与`-1`），然后再执行实际的范围获取操作



## lset

**作用**：为指定索引设置新的元素

**格式**：`lset list index new_element`

**扩展**：

命令只能对列表中已经存在的索引进行设置，如果用户给定的索引超出了列表的有效索引范围，命令将返回一个错误



## linsert

**作用**：将一个新元素插入到列表的某个指定元素的前面或者后面

**格式**：`linsert list BEFORE|AFTER target_element new_element`

**扩展**：

命令的参数`BEFORE|AFTER`分别表示命令将新元素插入到目标元素的前面或者后面

命令在完成插入操作后，将会返回列表当前的长度

**注意：命令中参数`target_element`与`new_element`均为元素，而非元素的索引**

为了执行插入操作，命令要求用户给定的目标元素必须已经存在于列表中。如果用户给定的元素并不存在，则命令将返回`-1`表示插入失败



## ltrim

**作用**：

修剪列表，命令接受一个列表和一个索引范围作为参数，移除列表中位于给定索引范围之外的所有元素，只保留给定范围之内的元素

**格式**：`ltrim list start end`

**扩展**：

命令在执行完成移除操作后，将返回`OK`作为结果

命令不仅可以处理正数索引，还可以处理负数索引



## lrem

**作用**：从列表中移除指定的元素

**格式**：`lrem list count element`

**扩展**：

命令中的参数`count`将决定了命令移除元素的方式：

1. 如果参数`count`等于`0`，则命令将移除列表中包含的所有的指定元素
2. 如果参数`count`大于`0`，则命令将从列表的左端开始向右端进行检查，并移除最先发现的`count`个指定的元素
3. 如果参数`count`小于`0`，则命令将从列表的右端开始向左端进行检查，并移除最先发现的`abs(count)`个指定的元素（`abs(count)`表示`count`的绝对值）

命令在执行完成后，将返回被移除的元素数量



## blpop

**作用**：阻塞式左端弹出操作

**格式**：`blpop list [list ...] timeout`

**扩展**：

命令会按照从左到右的顺序依次检查给定的列表，并对最先遇到的非空列表执行左端元素弹出操作

如果命令在检查了用户给定的所有列表后，发现没有可以执行弹出操作的非空列表，那么命令将阻塞执行该命令的客户端，并开始等待，直到某个列表变为非空，或者等待超时

当命令成功对某个非空列表执行弹出操作后，命令将返回一个包含两个元素的数组。数组的第一个元素为执行弹出操作的列表，数组的第二个元素则是被弹出元素本身

如果在同一时间，有多个客户端因为同一个列表而被阻塞，那么当导致阻塞的列表变为非空时，服务器按照“先阻塞先服务”的原则，依次唤醒被阻塞的客户端，并弹出列表中的元素。

如果被推入列表的元素数量少于被阻塞的客户端数量，则先被阻塞的客户端将会先解除阻塞，而未能解除阻塞的客户端则需要继续等待列表的非空状态。



## brpop

**作用**：阻塞式右端弹出操作

**格式**：`brpop list [list ...] timeout`



## brpoplpush

**作用**：阻塞式弹出并推入操作

**格式**：`brpoplpush source target timeout`

**扩展**：

该命令是`rpoplpush`的阻塞版本。

如果源列表非空，则命令行为与`rpoplpush`的行为一致

如果源列表为空，则命令将阻塞执行该命令的客户端，然后在给定的时间内等待可弹出的元素出现，或者等待时间超过给定的时限