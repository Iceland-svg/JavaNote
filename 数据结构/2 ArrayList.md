# （1）手搓版本
## 1 构造和成员变量
```java
public class MyArrayList implements IList{  
   public int[] array;  
   public int usedSize;  
  
   public static final int DEFAULT_CAPACITY = 10;  
  
   public MyArrayList() {  
        array = new int[DEFAULT_CAPACITY];  
    }  
  
   public MyArrayList(int initCapacity){  
        array = new int[initCapacity];  
    }
}
```


首先需要一个数组和一个表示有效空间大小的变量，为了优化代码，同时基于源码的设计思路，这里给DEFAULT_CAPACITY作为初始的空间大小，在构造里就把数组先new出来

---


## 2 准备工作，几个常见方法
### 2.1 满则扩容 -grow,isFull方法
#### isfull方法
```java
public boolean isFull() {  
return usedSize == array.length;  
}
```
#### grow方法
```java
private void grow() {
     if (isFull()){
      array = Arrays.copyOf(array,2*array.length);
     }
}
    //重载一个指定长度的扩容
private void grow(int growSize){
     if (isFull()){
      array = Arrays.copyOf(array,growSize);
     }
}
```
这里利用 Arrays.copyOf（）来扩容传目标数组和长度

---


## 3 Add方法
### 3.1 在末尾添加数据
```java
public void Add(int date) {  
 grow();  
 array[usedSize++] = date;  
}
```

### 3.2 在指定位置插入
``` java
public void Add(int pos, int date) {  
 checkPos(pos);  
 grow();  
 for(int i = usedSize-1; i >= pos; i--){  
  array[i+1]=array[i];  
 }  
 array[pos] = date;  
 usedSize++;  
}
```
在插入数据之前都要检查是否需要扩容，插入之后==更新usedSize的大小==
作为在指定位置插入数据的方法，则是用挪动数据的方法实现

**两个需要注意的点**
1 只能从后往前开始覆盖，从前往后会导致覆盖掉后面的值，找不了
2 注意越界访问，无论是从前还从后，最重要的是只要有i+1，就需要让临界条件 -1

---


## 4 两个 poscheck方法
为了保证pos位置的合法性，使用pos索引是需要优先检查

```java
public void checkPosByGetOrSet(int pos){  
  if(pos < 0 || pos >= usedSize){  
  throw new PosOutOfBoundsException("添加元素是pos位置不合法："+pos);  
  }  
}
```
为了增加可维护性，这里我们自己写定义异常 PosOutOfBoundsException 

实现
```java
public class PosOutOfBoundsException extends RuntimeException{  
  
    public PosOutOfBoundsException() {  
    }  
  
    public PosOutOfBoundsException(String message) {  
        super(message);  
    }  
}
```

---


## 5 查找是否包含指定元素

contain方法
```java
public boolean contain(int toFind) {  
   for(int i = 0; i < usedSize; i++){  
    if (array[i]==toFind) {return true;}  
    }  
   return false;  
   }
```
直接遍历查照即可

---


## 6 查找指定元素索引
indexOf方法
```java
public int indexOf(int toFind) {  
 for(int i = 0; i < usedSize; i++){  
  if(array[i] == toFind){  
   return i;  
  }  
}  
 return -1;  
}
```
遍历查找，找到了就返回对应下标

---


## 7 获取指定位置元素
get方法
```java
public int get(int pos) {  
  
 if(isEmpty()) {  
  throw new ListEmptyException("获取元素时，顺序表为空");  
 }  
  
 checkPosByGetOrSet(pos);  
    return array[pos];  
}
```

为了可维护性，避免顺序表为空的情况自定义 ListEmptyException 异常

```java
public class ListEmptyException extends RuntimeException{  
    public ListEmptyException() {  
    }  
  
    public ListEmptyException(String message) {  
        super(message);  
    }  
}
```

---


## 8 在指定位置放置指定数据

```java
public void set(int pos, int value) {  
 checkPosByGetOrSet(pos);  
 array[pos] = value;  
}
```

---


## 9 移除指定元素

remmove方法
```java
public void remmove(int toRemmove) {  
 int index = indexOf(toRemmove);  
 if(index == -1){  
  System.out.println("不存在该数据");  
 }  
 //覆盖删除  
 for(int i = index; i < usedSize-1; i++){  
  array[i] = array[i+1];  
 }  
 usedSize--;  
}
```
**思路：**
1 利用indexOf方法找到指定元素的下标
2 从index位置开始，挪动数据覆盖index位置的元素
3 更新usedSize的大小

==注意越界问题==

---


## 10 清除所有元素

非引用情况下 clear方法
```java
public void clear() {  
 usedSize = 0;  
}
```

引用情况下 clear方法
```java
public void clear() {  

for(int i = 0; i < usedSize; i++){
array[i] = null;
}

 usedSize = 0;  
}
```

---




# （2） ArrayList源码
## 1 构造

| ArrayList()                        | 无参构造                       |
| ---------------------------------- | -------------------------- |
| ArrayList(Collection<?extends E>c) | 利用其他的Collectin来创建ArrayList |














