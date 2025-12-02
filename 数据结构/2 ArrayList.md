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


## 4 两个 posCheck方法
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


**非引用情况下 clear方法**
```java
public void clear() {  
 usedSize = 0;  
}
```

**引用情况下 clear方法**
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

| ArrayList()                         | 无参构造                      |
| ----------------------------------- | ------------------------- |
| ArrayList(Collection< ?extents E>c) | 利用其他Collection构建ArrayList |
| ArrayList(int initialCapacity)      | 指定顺序表初始容量                 |


---
## 2 常见操作

## 一、创建和初始化 ArrayList

### 1. **基本创建方式**
```java
// 1. 默认构造（初始容量10）
ArrayList<String> list1 = new ArrayList<>();

// 2. 指定初始容量（避免频繁扩容）
ArrayList<String> list2 = new ArrayList<>(100);

// 3. 从其他集合创建
List<String> existingList = Arrays.asList("A", "B", "C");
ArrayList<String> list3 = new ArrayList<>(existingList);

// 4. Java 10+ 局部变量类型推断
var list4 = new ArrayList<String>();
```

### 2. **初始化时添加元素**
```java
// 传统方式
ArrayList<String> list = new ArrayList<>();
list.add("Apple");
list.add("Banana");

// 使用 Arrays.asList（返回的是固定大小的列表）
ArrayList<String> list = new ArrayList<>(Arrays.asList("A", "B", "C"));

// Java 9+ 使用 List.of（最推荐）
ArrayList<String> list = new ArrayList<>(List.of("A", "B", "C"));
```

---

## 二、CRUD 操作（增删改查）

### 1. **添加元素**
```java
ArrayList<String> fruits = new ArrayList<>();

// 1. 尾部添加
fruits.add("Apple");      // ["Apple"]
fruits.add("Banana");     // ["Apple", "Banana"]
fruits.add("Orange");     // ["Apple", "Banana", "Orange"]

// 2. 指定位置插入
fruits.add(1, "Mango");   // ["Apple", "Mango", "Banana", "Orange"]

// 3. 批量添加
List<String> moreFruits = Arrays.asList("Grape", "Peach");
fruits.addAll(moreFruits); // ["Apple", "Mango", "Banana", "Orange", "Grape", "Peach"]

// 4. 批量插入到指定位置
fruits.addAll(2, Arrays.asList("Kiwi", "Pear"));
// 结果: ["Apple", "Mango", "Kiwi", "Pear", "Banana", "Orange", "Grape", "Peach"]
```

### 2. **访问/查询元素**
```java
// 1. 根据索引获取元素
String first = fruits.get(0);  // "Apple"
String last = fruits.get(fruits.size() - 1);  // "Peach"

// 2. 检查是否包含元素
boolean hasApple = fruits.contains("Apple");  // true
boolean hasWatermelon = fruits.contains("Watermelon");  // false

// 3. 查找元素位置
int index = fruits.indexOf("Banana");  // 4（如果存在）
int lastIndex = fruits.lastIndexOf("Apple");  // 查找最后出现的位置

// 4. 检查空集合
boolean isEmpty = fruits.isEmpty();  // false
int size = fruits.size();  // 8

// 5. 遍历所有元素（多种方式）
for (int i = 0; i < fruits.size(); i++) {
    System.out.println(fruits.get(i));
}

for (String fruit : fruits) {
    System.out.println(fruit);
}

fruits.forEach(System.out::println);

fruits.forEach(fruit -> {
    System.out.println("Fruit: " + fruit);
});
```

### 3. **修改元素**
```java
// 1. 根据索引修改
String oldValue = fruits.set(0, "Red Apple");  // oldValue = "Apple"
// 现在列表: ["Red Apple", "Mango", "Kiwi", ...]

// 2. 批量修改（使用 replaceAll）
List<Integer> numbers = new ArrayList<>(Arrays.asList(1, 2, 3, 4, 5));
numbers.replaceAll(n -> n * 2);  // [2, 4, 6, 8, 10]
```

### 4. **删除元素**
```java
// 1. 根据索引删除
String removed = fruits.remove(0);  // 删除索引0的元素，返回被删除的值

// 2. 根据对象删除（删除第一个匹配项）
boolean isRemoved = fruits.remove("Banana");  // true，删除成功

// 3. 批量删除
List<String> toRemove = Arrays.asList("Mango", "Kiwi");
fruits.removeAll(toRemove);  // 删除所有匹配的元素

// 4. 条件删除（Java 8+）
fruits.removeIf(fruit -> fruit.startsWith("P"));  // 删除所有以P开头的元素

// 5. 清空列表
fruits.clear();  // []
```

### 5. **删除时的重要注意事项**
```java
ArrayList<Integer> numbers = new ArrayList<>(Arrays.asList(1, 2, 3, 4, 5));

// ❌ 错误方式：在遍历时删除（会抛出 ConcurrentModificationException）
for (Integer num : numbers) {
    if (num % 2 == 0) {
        numbers.remove(num);  // 运行时异常！
    }
}

// ✅ 正确方式1：使用迭代器
Iterator<Integer> iterator = numbers.iterator();
while (iterator.hasNext()) {
    Integer num = iterator.next();
    if (num % 2 == 0) {
        iterator.remove();  // 安全删除
    }
}

// ✅ 正确方式2：使用 removeIf（Java 8+，最推荐）
numbers.removeIf(num -> num % 2 == 0);

// ✅ 正确方式3：从后往前遍历（适用于按索引删除）
for (int i = numbers.size() - 1; i >= 0; i--) {
    if (numbers.get(i) % 2 == 0) {
        numbers.remove(i);
    }
}
```

---

## 三、转换和复制操作

### 1. **转换为数组**
```java
ArrayList<String> list = new ArrayList<>(Arrays.asList("A", "B", "C"));

// 1. 转换为 Object[] 数组
Object[] array1 = list.toArray();

// 2. 转换为指定类型的数组（推荐）
String[] array2 = list.toArray(new String[0]);  // 参数是目标数组，0表示自动分配

// 3. 转换为指定大小的数组
String[] array3 = list.toArray(new String[list.size()]);

// 4. 转换为特定类型数组（Java 11+）
String[] array4 = list.toArray(String[]::new);
```

### 2. **复制 ArrayList**
```java
ArrayList<String> original = new ArrayList<>(Arrays.asList("A", "B", "C"));

// 1. 构造函数复制（浅拷贝）
ArrayList<String> copy1 = new ArrayList<>(original);

// 2. addAll 方法复制
ArrayList<String> copy2 = new ArrayList<>();
copy2.addAll(original);

// 3. 使用 clone 方法（不推荐，需要类型转换）
ArrayList<String> copy3 = (ArrayList<String>) original.clone();

// 4. Java 8+ Stream 复制
List<String> copy4 = original.stream().collect(Collectors.toList());

// 5. 使用 List.copyOf（Java 10+，返回不可变列表）
List<String> copy5 = List.copyOf(original);
```

### 3. **浅拷贝 vs 深拷贝**
```java
// 浅拷贝示例
class Person {
    String name;
    Person(String name) { this.name = name; }
}

ArrayList<Person> people = new ArrayList<>();
people.add(new Person("Alice"));
people.add(new Person("Bob"));

// 浅拷贝 - 拷贝引用，不拷贝对象
ArrayList<Person> shallowCopy = new ArrayList<>(people);
shallowCopy.get(0).name = "Changed";  // 会影响原始列表中的对象
System.out.println(people.get(0).name);  // 输出 "Changed"

// 深拷贝 - 需要手动实现
ArrayList<Person> deepCopy = new ArrayList<>();
for (Person p : people) {
    deepCopy.add(new Person(p.name));  // 创建新对象
}
```

---

## 四、排序和查找操作

### 1. **排序**
```java
ArrayList<Integer> numbers = new ArrayList<>(Arrays.asList(5, 2, 8, 1, 9));

// 1. 自然排序（升序）
Collections.sort(numbers);  // [1, 2, 5, 8, 9]

// 2. 降序排序
Collections.sort(numbers, Collections.reverseOrder());  // [9, 8, 5, 2, 1]

// 3. 自定义排序（使用 Comparator）
ArrayList<String> words = new ArrayList<>(Arrays.asList("banana", "apple", "cherry"));

// 按长度排序
Collections.sort(words, (s1, s2) -> s1.length() - s2.length());

// 或使用 Comparator 方法
Collections.sort(words, Comparator.comparingInt(String::length));

// 4. 多条件排序
class Person {
    String name;
    int age;
    // 构造器、getter、setter...
}

ArrayList<Person> people = new ArrayList<>();
// 先按年龄升序，再按姓名降序
people.sort(Comparator
    .comparingInt(Person::getAge)
    .thenComparing(Comparator.comparing(Person::getName).reversed()));

// 5. 原地排序（Java 8+）
numbers.sort(Comparator.naturalOrder());  // 升序
numbers.sort(Comparator.reverseOrder());  // 降序
```

### 2. **查找**
```java
ArrayList<Integer> numbers = new ArrayList<>(Arrays.asList(1, 3, 5, 7, 9, 11));

// 1. 二分查找（必须先排序）
Collections.sort(numbers);
int index = Collections.binarySearch(numbers, 7);  // 返回索引 3

// 2. 查找最大最小值
Integer max = Collections.max(numbers);  // 11
Integer min = Collections.min(numbers);  // 1

// 3. 条件查找（Java 8+ Stream）
Optional<Integer> firstEven = numbers.stream()
    .filter(n -> n % 2 == 0)
    .findFirst();

Optional<Integer> anyMatch = numbers.stream()
    .filter(n -> n > 10)
    .findAny();
```

---

## 五、批量操作和集合运算

### 1. **集合运算**
```java
ArrayList<Integer> list1 = new ArrayList<>(Arrays.asList(1, 2, 3, 4, 5));
ArrayList<Integer> list2 = new ArrayList<>(Arrays.asList(4, 5, 6, 7, 8));

// 1. 求交集
list1.retainAll(list2);  // list1 变为 [4, 5]

// 2. 求差集（list1 中有，list2 中没有的）
list1 = new ArrayList<>(Arrays.asList(1, 2, 3, 4, 5));
list1.removeAll(list2);  // list1 变为 [1, 2, 3]

// 3. 求并集
list1 = new ArrayList<>(Arrays.asList(1, 2, 3));
list1.addAll(list2);  // [1, 2, 3, 4, 5, 6, 7, 8]

// 4. 去重并集（使用 Set）
Set<Integer> union = new HashSet<>(list1);
union.addAll(list2);  // 自动去重
```

### 2. **子列表操作**
```java
ArrayList<Integer> numbers = new ArrayList<>(Arrays.asList(0, 1, 2, 3, 4, 5, 6, 7, 8, 9));

// 1. 获取子列表（视图，非副本）
List<Integer> subList = numbers.subList(2, 6);  // [2, 3, 4, 5]

// 修改子列表会影响原列表
subList.set(0, 99);
System.out.println(numbers.get(2));  // 输出 99

// 2. 清空子列表范围
numbers.subList(3, 7).clear();  // 删除索引3到6的元素

// 3. 注意事项：获取子列表后，不要直接修改原列表
List<Integer> sub = numbers.subList(1, 4);
// numbers.add(10);  // ❌ 这会导致后续对sub的操作抛出异常
```

---

## 六、性能优化技巧

### 1. **初始容量设置**
```java
// 已知元素数量时，预分配容量避免扩容
int expectedSize = 10000;
ArrayList<String> list = new ArrayList<>(expectedSize);

// 估算容量的经验法则：已知元素数量 × 1.5
list = new ArrayList<>((int)(expectedSize * 1.5));
```

### 2. **扩容机制了解**
```java
// ArrayList 的扩容机制
ArrayList<Integer> list = new ArrayList<>();  // 默认容量10
for (int i = 0; i < 100; i++) {
    list.add(i);
    // 扩容发生时间：第11、16、24、36、54、81、122...个元素时
    // 每次扩容为原来的1.5倍（int newCapacity = oldCapacity + (oldCapacity >> 1)）
}
```

### 3. **内存优化**
```java
// 1. 列表不再使用时，及时清空
ArrayList<byte[]> largeData = new ArrayList<>();
// ... 使用数据
largeData.clear();  // 清空引用
largeData = null;   // 帮助GC

// 2. 使用 trimToSize 释放多余空间
ArrayList<String> list = new ArrayList<>(1000);
list.add("A");
list.add("B");
list.trimToSize();  // 将容量调整为当前元素数量（2）
```

---

## 七、实战示例

### 示例1：学生管理系统
```java
class Student {
    private int id;
    private String name;
    private double score;
    
    // 构造器、getter、setter...
}

class StudentManager {
    private ArrayList<Student> students = new ArrayList<>();
    
    // 添加学生
    public void addStudent(Student student) {
        students.add(student);
    }
    
    // 根据ID查找
    public Student findById(int id) {
        return students.stream()
            .filter(s -> s.getId() == id)
            .findFirst()
            .orElse(null);
    }
    
    // 按分数排序
    public void sortByScore() {
        students.sort(Comparator.comparingDouble(Student::getScore).reversed());
    }
    
    // 获取平均分
    public double getAverageScore() {
        return students.stream()
            .mapToDouble(Student::getScore)
            .average()
            .orElse(0.0);
    }
    
    // 删除不及格学生
    public void removeFailedStudents() {
        students.removeIf(s -> s.getScore() < 60);
    }
}
```

### 示例2：分页查询
```java
class Pagination<T> {
    private ArrayList<T> data;
    private int pageSize;
    
    public Pagination(ArrayList<T> data, int pageSize) {
        this.data = data;
        this.pageSize = pageSize;
    }
    
    // 获取第page页的数据
    public List<T> getPage(int page) {
        int fromIndex = (page - 1) * pageSize;
        int toIndex = Math.min(fromIndex + pageSize, data.size());
        
        if (fromIndex >= data.size()) {
            return Collections.emptyList();
        }
        
        return new ArrayList<>(data.subList(fromIndex, toIndex));
    }
    
    // 获取总页数
    public int getTotalPages() {
        return (int) Math.ceil((double) data.size() / pageSize);
    }
}
```

### 示例3：数据去重和统计
```java
public class DataProcessor {
    // 统计单词频率
    public static Map<String, Integer> wordFrequency(ArrayList<String> words) {
        Map<String, Integer> frequency = new HashMap<>();
        for (String word : words) {
            frequency.put(word, frequency.getOrDefault(word, 0) + 1);
        }
        return frequency;
    }
    
    // 去重并保持顺序
    public static ArrayList<String> removeDuplicates(ArrayList<String> list) {
        // 使用 LinkedHashSet 保持顺序
        Set<String> set = new LinkedHashSet<>(list);
        return new ArrayList<>(set);
    }
    
    // 分组操作
    public static Map<String, List<Person>> groupByCity(ArrayList<Person> people) {
        Map<String, List<Person>> groups = new HashMap<>();
        for (Person person : people) {
            groups.computeIfAbsent(person.getCity(), k -> new ArrayList<>())
                  .add(person);
        }
        return groups;
    }
}
```

---

## 八、常见陷阱

### 陷阱1：并发修改异常
```java
// ❌ 错误示例
ArrayList<Integer> list = new ArrayList<>(Arrays.asList(1, 2, 3));
for (Integer num : list) {
    if (num == 2) {
        list.remove(num);  // ConcurrentModificationException!
    }
}

// ✅ 正确做法
Iterator<Integer> it = list.iterator();
while (it.hasNext()) {
    if (it.next() == 2) {
        it.remove();  // 使用迭代器的remove方法
    }
}
```

### 陷阱2：sublist 的并发修改
```java
ArrayList<Integer> list = new ArrayList<>(Arrays.asList(1, 2, 3, 4, 5));
List<Integer> sub = list.subList(1, 4);

// ❌ 获取子列表后修改原列表
list.add(6);  // 这里没问题，但是...
// sub.get(0);  // ❌ 这里可能抛出 ConcurrentModificationException

// ✅ 正确做法：先完成子列表操作，再修改原列表
List<Integer> subCopy = new ArrayList<>(list.subList(1, 4));
list.add(6);  // 安全
```

### 总结

1. **选择合适的初始容量**，减少扩容次数
2. **使用泛型**保证类型安全
3. **优先使用 for-each 循环**，简洁安全
4. **修改时使用迭代器或 removeIf**
5. **大量数据时考虑使用 trimToSize** 节省内存
6. **复杂操作优先使用 Stream API**（Java 8+）
7. **多线程环境使用 CopyOnWriteArrayList** 或外部同步
8. **注意 subList 的视图特性**，避免并发修改

---
