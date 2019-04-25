---
title: Java Random seed伪随机
categories: Java
tags: java,random
time: 2017-11-19 13:12:01
---

> 在指定区间内获得随机数，随机的几种方式。


方法：

```
	/**
	 * 在指定区间[min,max)通过种子seed设置伪随机数
	 * @param min 最小值
	 * @param max 最大值
	 * @param seed 种子
	 */
	public static int random(int min, int max, long seed) {
		Random random = new Random(seed);
		final int size = 100;
		for (int i = 0; i < size ; i++) {
			int randomNum = random.nextInt(max);
			if (randomNum > min && randomNum < max) {
				return randomNum;
			}
		}
		// 确保生成随机数。
		return random(min, max, send + size);
	}
```
 测试：
 

```
	public static void main(String[] args) {
		for (int j = 0; j < 3; j++) {
			System.out.println(String.format("第%d遍", j));
			for (int i = 0; i < 10; i++) {
				System.out.print(random(0,100, i) + "		");
			}
			System.out.println();
		}
	}
```
结果：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181219095438137.png)



Apache.commons.lang3包中也有RandomUtils生成随机数， 一般的需求足够了。但是没法加seed，无法生成可控的随机数！
```
	<-- org.apache.commons.lang3 -->
      <dependency>
            <groupId>org.apache.commons</groupId>
            <artifactId>commons-lang3</artifactId>
            <version>3.8.1</version>
        </dependency>
```

方法：
```
    public static int random(int min, int max)  {
		return RandomUtils.nextInt(min, max);
	}
```
测试：

```
	public static void main(String[] args) {
		for (int j = 0; j < 3; j++) {
			System.out.println(String.format("第%d遍", j));
			for (int i = 0; i < 10; i++) {
				System.out.print(RandomUtils.nextInt(0,100) + "		");
			}
			System.out.println();
		}
	}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20181219100330868.png)
