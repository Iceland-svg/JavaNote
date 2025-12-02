手搓版本
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
```

