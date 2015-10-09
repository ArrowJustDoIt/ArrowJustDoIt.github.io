---
layout: post
title: 几个简单的排序算法
category: 技术
tags: 算法
keywords: 
description: 
---
> "内心强大才是真的强大"---子应该没有曰过 -,-

数据结构: 数据存储的方式(存储介质)
算法: 数据运算方式(代码)

常见的排序算法有四种: `冒泡`, `插入`,`选择`和`快速`排序
***
###**冒泡排序**
冒泡: bubble

冒泡排序: 将所有的数据比作一个一个的泡,数据大小代表泡的大小. 泡越大意味着浮力越大,就会最先升上去.

冒泡原理: 对数组进行多次遍历, 每次遍历都能找出最大的泡(剩余部分),将最大的泡放到数组的最后.

{% highlight php linenos %}
<?php

	$arr = [3,5,8,56,3,2];
	//冒泡算法
	//外层循环,控制冒泡次数
	for($i = 0,$count = count($arr);$i < $count;$i++){
		//内层循环:进行交换,冒出当前一轮的最大泡
		for($j = 0;$j < $count - 1 - $i;$j++){
			if($arr[$j] > $arr[$j + 1]){
				$temp = $arr[$j];
				$arr[$j] = $arr[$j + 1];
				$arr[$j + 1] = $temp;
			}
		}
	}
	var_dump($arr);
{% endhighlight %}

分析: 因为后面的是j+1，所以j的比较次数需要总元素个数-1，才能保证j+1不超出下标范围，而已经浮出水面的泡不必再比较，所以需要减掉$i.

###**快速排序**

快速排序: 排序的速度快,利用递归实现

递归是用空间换时间(短时间内使用较多的内存来进行运算),更多递归知识见[php递归遍历文件夹](http://arrowjustdoit.github.io/2014/06/11/Recursive-Traversal.html)

###快速排序原理

1. 从一个数组中取出一个元素

2. 将剩余的其他元素挨个与取出的元素进行比较: 如果比当前元素小,存放到一个数组;如果比它大,存放到另外一个数组. 结束以后, 当前的中间元素已经确定其位置.

3. 但是剩下的两个数组有可能没有排好序: 也需要排序,与父问题一样: 递归点

4. 如果数组只有一个元素,说明一定已经排好序,就不需要再调用函数排序: 递归出口

{% highlight php linenos %}
/*
 * @param1 array $arr 待排序的数组
 * @return array 排序好的数组
*/
function quick_sort($arr){
		//取出数组长度,如果小于等于1则排好序(递归出口)
		$len = count($arr);
		if($len <= 1)return $arr;
		//取出比较数据
		$data = $arr[0];
		//比较,小于$data的数据放左边数组,大于的放右边数组
		$left = $right = array();
		for($i = 1;$i < $len;$i++){
			if($arr[$i] < $data){
				$left[] = $arr[$i];
			}else{
				$right[] = $arr[$i];
			}
		}
		//递归点(可能左边和右边数组未排好序,则递归~)
		$left = quick_sort($left);
		$right = quick_sort($right);
		//合并数组
		return array_merge($left, array($data), $right);
	}
{% endhighlight %}
