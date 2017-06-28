---
title: 数据结构----线性表
---

## 概述

线性表是最基本、最简单、也是最常用的一种数据结构。

在线性表中数据元素之间的关系是线性，数据元素可以看成是排列在一条线上或一个环上。

线性表分为静态线性表和动态线性表，常见的有顺序表(静态的)、单向链表(动态的)和双向链表(动态的)。

线性表的操作主要包括：
1. 计算表的长度n。
2. 线性表是否为空
3. 将元素添加到线性表的末尾
4. 获取第i个元素
5. 清除线性表
6. 返回列表中首次出现指定元素的索引，如果列表不包含此元素，则返回 -1。
7. 返回列表中最后一次出现指定元素的索引，如果列表不包含此元素，则返回 -1。
8. 将新元素插入第i个位置。
9. 更改第i个元素
10. 删除第i个元素

由此，对线性表抽象数据类型定义List接口如下：
```java 
public interface List<E> {
    //线性表长度
    public int size();

    //是否为空
    public boolean isEmptry();

    //向某位置插入一个元素
    public boolean add(int i, E e);

    //向最后插入一个元素
    public boolean add(E e);

    //删除第i个元素
    public E remove(int i);

    //删除元素e
    public boolean remove(E e);

    //修改索引为i的元素
    public boolean set(int i, E e);

    //得到索引为i的元素
    public E get(int i);

    //得到元素e的索引
    public int indexOf(E e);

    //得到最后一个元素e的索引
    public int lastIndexOf(E e);

    //是否存在元素e
    public boolean contains(E e);

    //清空所有元素
    public boolean clear();
}  
```

## 顺序表

顺序表内部以数组方式来实现。

### 代码展示
```java
public class ArrayList<E> implements List<E> {
    //初始化大小
    public static final int DEFAULT_CAPACITY = 10;
    private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};

    //元素数组
    private Object[] elementData;
    //元素个数
    private int size = 0;

    /**
     * 线性表大小
     *
     * @return
     */
    public int size() {
        return size;
    }

    /**
     * 判断线性表是否为空
     *
     * @return
     */
    public boolean isEmptry() {
        return size == 0;
    }

    /**
     * 初始化线性表，数组默认为空
     */
    public ArrayList() {
        elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
    }

    /**
     * 根据传入参数实例化线性表，确定数组大小
     *
     * @param initialCapacity
     */
    public ArrayList(int initialCapacity) {
        if (initialCapacity < 0)
            throw new IllegalArgumentException("Illegal Capacity: " + initialCapacity);
        if (initialCapacity == 0) {
            elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
        } else {
            elementData = new Object[initialCapacity];
        }
    }

    /**
     * 判断数组容量
     *
     * @param minCapacity 最小容量
     */
    private void ensureCapacityInternal(int minCapacity) {
        if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
            minCapacity = Math.max(minCapacity, DEFAULT_CAPACITY);
        }
        //如果大于数组容量，扩容
        if (minCapacity > elementData.length) {
            grow(minCapacity);
        }
    }

    /**
     * 扩容数组容量
     *
     * @param minCapacity
     */
    private void grow(int minCapacity) {
        //元素个数
        int oldDataLength = elementData.length;
        //容量扩容
        int newDataLength = oldDataLength + (oldDataLength >> 1);
        if (newDataLength < minCapacity) {
            newDataLength = minCapacity;
        }
        //将数组copy到新数组上
        elementData = Arrays.copyOf(elementData, newDataLength);
    }

    /**
     * 添加元素
     *
     * @param index 索引
     * @param o     添加元素
     * @return
     */
    public boolean add(int index, Object o) {
        //判断i是否大于数组容量
        rangeCheckForAdd(index);
        //数组容量加1
        ensureCapacityInternal(size + 1);

        //数组从索引为index后面的数，依次向后移动一位
        System.arraycopy(elementData, index, elementData, index + 1, size - index);
        //索引为index的元素赋值为o
        elementData[index] = o;
        //线性表大小+1
        size++;
        return true;
    }

    /**
     * 添加元素
     *
     * @param object 元素
     * @return
     */
    public boolean add(Object object) {
        //判断数组大小
        ensureCapacityInternal(size + 1);
        //赋值
        elementData[size++] = object;

        return true;
    }

    /**
     * 删除索引为index的元素
     *
     * @param index 索引
     * @return
     */
    public E remove(int index) {
        //判断是否越界
        rangeCheck(index);
        //得到索引为index的元素
        E oldValue = elementData(index);
        int numMoved = size - index - 1;
        if (numMoved > 0)
            System.arraycopy(elementData, index + 1, elementData, index, numMoved);
        //GC回收
        elementData[--size] = null;

        return oldValue;
    }

    /**
     * 删除元素
     *
     * @param object
     * @return
     */
    public boolean remove(Object object) {
        if (object == null) {
            for (int i = 0; i < size; i++) {
                if (null == elementData[i]) {
                    remove(i);
                }
            }
        } else {
            for (int i = 0; i < size; i++) {
                if (object.equals(elementData[i])) {
                    remove(i);
                }
            }
        }
        return true;
    }

    /**
     * 设置索引为index的元素为object
     *
     * @param index  索引
     * @param object 存入元素
     * @return
     */
    public boolean set(int index, Object object) {
        //判断是否越界
        rangeCheck(index);
        //设值
        elementData[index] = object;

        return true;
    }

    /**
     * 得到索引为i的元素
     *
     * @param i
     * @return
     */
    public E get(int i) {
        //判断索引i是否越界
        rangeCheck(i);
        //取值
        return elementData(i);
    }

    /**
     * 首次出现的索引
     *
     * @param object
     * @return
     */
    public int indexOf(Object object) {
        if (object == null) {
            for (int i = 0; i < size; i++) {
                if (null == elementData[i]) {
                    return i;
                }
            }
        } else {
            for (int i = 0; i < size; i++) {
                if (elementData[i].equals(object)) {
                    return i;
                }
            }
        }
        return -1;
    }

    /**
     * 元素最后出现的索引
     *
     * @param object
     * @return
     */
    public int lastIndexOf(Object object) {
        if (object == null) {
            for (int i = size - 1; i < 0; i--) {
                if (null == elementData[i]) {
                    return i;
                }
            }
        } else {
            for (int i = size - 1; i < 0; i--) {
                if (elementData[i].equals(object)) {
                    return i;
                }
            }
        }
        return -1;
    }

    /**
     * 是否包含元素元素o
     *
     * @param object
     * @return
     */
    public boolean contains(Object object) {
        //判断是否存在索引
        return indexOf(object) >= 0;
    }

    /**
     * 清除线性表
     *
     * @return
     */
    public boolean clear() {
        //数组元素设为null，GC回收
        for (int i = 0; i < size; i++) {
            elementData[i] = null;
        }
        //线性表大小设为0
        this.size = 0;

        return true;
    }

    //判断数组边界问题
    private void rangeCheck(int index) {
        if (index >= size || index < 0)
            throw new IndexOutOfBoundsException("Index: " + index + ", Size: " + size);
    }

    //判断添加数组边界问题
    private void rangeCheckForAdd(int index) {
        if (index > size || index < 0)
            throw new IndexOutOfBoundsException("Index: " + index + ", Size: " + size);
    }

    private E elementData(int i) {
        E e = (E) elementData[i];
        return e;
    }
}
```