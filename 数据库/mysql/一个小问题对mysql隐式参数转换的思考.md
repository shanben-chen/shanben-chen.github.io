## 遇到的问题

最近一位以前的老同学找到我，跟我说他们线上有出现了两个由sql语句引起的事故，并且都没找到原因。其中一个事故简单描述如下。

一个简单的mysql blog表

```
CREATE TABLE `blog` (
  `id` int(11) DEFAULT NULL,
  `title` varchar(255) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci
```

其中一行数据为

```
INSERT INTO `blog` VALUES ('1', '32019051669436435');
```

查询语句：

```
select * from blog where title = 32019051669436436 
```

结果查出了title为32019051669436435的数据：

![image-20190609173426706](/Users/cey/Library/Application Support/typora-user-images/image-20190609173426706.png)

## 原因

这个原理其实很简单，其实就是发生了隐式参数转换，title为varchar类型，而查询语句中使用的是数值类型，因为mysql innodb varchar与数值类型的比较会转换成double类型去比较。而double类型是有精度限制的。导致这个问题的发生。

## 思考

### 这类问题该怎么防止呢？

其实很难让每个人程序员都知道mysql底层的隐式参数转换的每个细节。

我们应该谨记会发生隐式参数转换的情况，并且写sql时，要注意字段比较不能发生隐式参数转换，就不会发生这类问题。

隐式参数转换的情况：

1. 字符集不一样：比如utf-8余utf-mb4不一样，比较时会发生转换，可能会使索引失效
2. 数据类型不一样