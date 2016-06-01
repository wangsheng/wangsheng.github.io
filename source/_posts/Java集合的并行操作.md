---
title: Java集合的并行操作
date: 2015-06-26 14:56:00
tags:
- Java
---

用for循环操作一个集合，既读取集合元素，同时又删除集合中的元素，会发生什么事情呢？

上代码『号外，号外，此示例来源于我们项目中的真实代码哦~』

```Java
// Target class
class Target {
	int id;// ID
	String type;// 类型
	int count;// 未读消息数
	
	public Target(int id, String type, int count) {
		this.id = id;
		this.type = type;
		this.count = count;
	}

	@Override
	public String toString() {
		return "Target [id=" + id + ", type=" + type + ", count=" + count + "]";
	}
	
}

// 以下是模拟一组target集合，target包含group、room、user类型。
List<Target> sessions = new ArrayList<Target>();
sessions.add(new Target(1, "group", 1));
sessions.add(new Target(2, "group", 1));
sessions.add(new Target(3, "group", 1));
sessions.add(new Target(4, "room", 1));
sessions.add(new Target(5, "group", 1));
sessions.add(new Target(6, "group", 1));
sessions.add(new Target(7, "group", 1));
sessions.add(new Target(8, "user", 1));
sessions.add(new Target(9, "user", 1));
sessions.add(new Target(10, "group", 1));
System.out.println("before size:"+sessions.size()+", sessions:"+humanPrintList(sessions));
int totalUnread = 0;// 统计集合中类型为group的target未读消息数量
for (int i=0;i<sessions.size();i++) {
	System.out.println("read index:" + i + ", session:"+sessions.get(i));
	if (sessions.get(i).type.equals("group")) {// 如果是group类型，则累加未读消息数
		System.out.println("==> add totalUnread");
		totalUnread += sessions.get(i).count;
	} else {// 否则，将target移除集合
		System.out.println("==> remove");
		sessions.remove(i);
	}
}
System.out.println("after size:"+sessions.size()+", totalUnread:"+totalUnread+",sessions:"+humanPrintList(sessions));
```

至此，示例代码结束。大家可以猜测下最后的输出中sessions的长度，内容以及总共的未读消息数据。

项目中的代码，期望结果是剩余集合只包含group类型的target，而且totalUnread的值是类型为group的target的未读消息数之和。可是，运行的结果却是：

```
before size:10, sessions:
========== List content: ===========
Target [id=1, type=group, count=1]
Target [id=2, type=group, count=1]
Target [id=3, type=group, count=1]
Target [id=4, type=room, count=1]
Target [id=5, type=group, count=1]
Target [id=6, type=group, count=1]
Target [id=7, type=group, count=1]
Target [id=8, type=user, count=1]
Target [id=9, type=user, count=1]
Target [id=10, type=group, count=1]
====================================
read index:0, session:Target [id=1, type=group, count=1]
==> add totalUnread
read index:1, session:Target [id=2, type=group, count=1]
==> add totalUnread
read index:2, session:Target [id=3, type=group, count=1]
==> add totalUnread
read index:3, session:Target [id=4, type=room, count=1]
==> remove
read index:4, session:Target [id=6, type=group, count=1]
==> add totalUnread
read index:5, session:Target [id=7, type=group, count=1]
==> add totalUnread
read index:6, session:Target [id=8, type=user, count=1]
==> remove
read index:7, session:Target [id=10, type=group, count=1]
==> add totalUnread
after size:8, totalUnread:6,sessions:
========== List content: ===========
Target [id=1, type=group, count=1]
Target [id=2, type=group, count=1]
Target [id=3, type=group, count=1]
Target [id=5, type=group, count=1]
Target [id=6, type=group, count=1]
Target [id=7, type=group, count=1]
Target [id=9, type=user, count=1]
Target [id=10, type=group, count=1]
====================================
```

为什么结果完全不是预期的呢？原因是程序走到else时，将元素移除，后面的元素自动往前移动，所以继续取下一个下标时，被移除的后一个元素悄悄溜走了，成了漏网之鱼。

**解决方法** : 最简单的就是在remove后执行 ```i--; ```
