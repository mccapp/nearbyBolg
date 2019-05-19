---
title: java List 删除数据问题
date: 2017-11-05 16:30:12
categories: java
tags: [java]
---



```
     void test() {
        List<String> list = 
        new ArrayList<>(Arrays.asList("a", "b", "c", "d", "e");
        );
        //第一种方式
        for (String str : list) {
            if (str.contains("b" )) {
                list.remove(str);
            }
        }

        //第二种方式
        int size = list.size();
        for (int i = 0; i < size; i++) {
            String str = list.get(i);
            if (str.contains("b" )) {
                list.remove(i);
            }
        }


        //第三种方式
        for (int i = 0; i < list.size(); i++) {
            String str = list.get(i);
            if (str.contains("b" )) {
                list.remove(i);
            }
        }


        //第四种方式
        for (Iterator<String> ite = list.iterator(); ite.hasNext(); ) {
            String str = ite.next();
            if (str.contains("b" )) {
                ite.remove();
            }
        }

        //第五种方式
        for (Iterator<String> ite = list.iterator(); ite.hasNext(); ) {
            String str = ite.next();
            if (str.contains("b" )) {
                list.remove(str);
            }
        }
    }

```

方式一：报错java.util.ConcurrentModificationException
方式二：报错：下标越界 java.lang.IndexOutOfBoundsException  
	list大小会发生变化，索引也会变化。导致你在遍历的时候漏掉某些元素，当删除第1个元素后，继续根据索引访问第2个元素时，因为删除的关系后面的元素都会往前移动一位，所以实际访问的是第3个元素，因此，这种方式可以用在删除特定的一个元素时使用，但不适合循环删除多个元素时使用。
方式三：正常删除，不推荐，每次循环都需要计算list的大小，效率低。
方式四：正常删除，推荐使用
方式五：报错：java.util.ConcurrentModificationException
