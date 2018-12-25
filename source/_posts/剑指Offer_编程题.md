---
title: 剑指Offer_编程题
date: 2018-12-15 14:49:40
tags:
---

# 数组中重复的数字

## 题目描述

在一个长度为n的数组里的所有数字都在0到n-1的范围内。 数组中某些数字是重复的，但不知道有几个数字是重复的。也不知道每个数字重复几次。请找出数组中任意一个重复的数字。 例如，如果输入长度为7的数组{2,3,1,0,2,5,3}，那么对应的输出是第一个重复的数字2。 

## 解法一

> 来源：[牛客网回答区](https://www.nowcoder.com/questionTerminal/623a5ac0ea5b4e5f95552655361ae0a8)

### 思路

循环遍历数组，给数组下标为当前元素的数组元素的值减去n，当有重复元素时，访问后一个重复值时发现此时对应位序上的值已经小于n了，放回这个位序即可。

注：

1. 使用“减n”而不用“加n”是为了避免加法溢出
2. 当当前元素值p小于0时，要做减n操作的元素是位于（p+n）上的元素

```java
eg：array = [2,3,1,0,2,5] , n = 6
position-0 : [2,3,1,0,2,5] // array[2] - 6
             [2,3,-5,0,2,5] 
position-1 : [2,3,-5,0,2,5] // array[3] - 6
             [2,3,-5,-6,2,5]            
position-2 : [2,3,-5,-6,2,5] // array[array[2] + 6] - 6
             [2,-3,-5,-6,2,5] 
position-3 : [2,-3,-5,-6,2,5] // array[array[3] + 6] - 6             
             [-4,-3,-5,-6,2,5]
position-4 : [-4,-3,-5,-6,2,5] // array[2] < 0，结束，重复数字为2
```

### Java代码实现

```java
public boolean duplicate(int numbers[],int length,int [] duplication) {
    if(numbers == null || length <= 0){
        return false;
    }
    for (int i = 0; i < length; i++){
        int index = numbers[i];
        if (index < 0){
            index += length;
        }
        if (numbers[index] < 0){
            duplication[0] = index;
            return true;
        }
        numbers[index] -= length;
    }
    return false;
}
```

## 解法二

> 来源：[[CyC2018](https://github.com/CyC2018)/**Interview-Notebook**](https://github.com/CyC2018/Interview-Notebook/blob/master/notes/%E5%89%91%E6%8C%87%20offer%20%E9%A2%98%E8%A7%A3.md#3-%E6%95%B0%E7%BB%84%E4%B8%AD%E9%87%8D%E5%A4%8D%E7%9A%84%E6%95%B0%E5%AD%97)

### 思路

将值为i的元素交换到位序为i的位置上，如果对应位置上已经有正确的值（元素值等于位序即为正确）了则可判定该值即为重复值。

```java
eg：array = [2,3,1,0,2,5] 
position-0 : [2,3,1,0,2,5] // 2 <-> 1
             [1,3,2,0,2,5] // 1 <-> 3
             [3,1,2,0,2,5] // 3 <-> 0
             [0,1,2,3,2,5] // already in position
position-1 : [0,1,2,3,2,5] // already in position
position-2 : [0,1,2,3,2,5] // already in position
position-3 : [0,1,2,3,2,5] // already in position
position-4 : [0,1,2,3,2,5] // array[i] == array[array[i]], exit
```

### Java代码实现

```java
public boolean duplicate(int numbers[],int length,int [] duplication) {
    if(numbers == null || length <= 0){
        return false;
    }
    for(int i = 0; i < length; i++){
        while(numbers[i] != i){
            if(numbers[i] == numbers[numbers[i]]){
                duplication[0] = numbers[i];
                return true;
            }
            swap(numbers, i, numbers[i]);
        }
    }
    return false;
}

private void swap(int arrays[], int i, int j){
    int temp = arrays[i];
    arrays[i] = arrays[j];
    arrays[j] = temp;
}
```