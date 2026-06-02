

文件操作

File类
文件内容操作
创建文件
创建目录

renameTo

文件名
目录名

移动：
移动前文件路径
移动后文件路径

代码演示

读文件
写文件
通过流对象来进行文件操作（围绕文件的操作称为流）
字节流（读二进制文件）
InputStream
OutputStream
字符流（读文本文件）
Reader
Writer
输入输出都是以cpu为基准的
在读一个文件的时候数据流从硬盘进入cpu到我们眼前（input），在写一个文件的时候，数据流从内存进入cpu再从cpu出来到硬盘（output）


文件读取操作（二进制）
代码
三个版本

关闭文件操作
类似的像文件描述符表
表的长度是固定的
如果打开文件而不关闭，文件的资源会被慢慢消耗殆尽
加上close后的版本
代码
利用try with resource优化后的版本


写文件操作（二进制）
追加写

字符流读写
代码
根据你的笔记大纲，我为你整理并完善了一份结构清晰、内容详尽的 Java 文件操作笔记。补充了必要的概念解释、代码示例以及常见注意事项，方便你复习和查阅。

---

### 一、File 类：文件与目录的抽象表示

`File` 类位于 `java.io` 包中，可以代表一个文件或目录的路径，但**不涉及文件内容**的读写。

#### 1. 创建文件/目录

| 方法 | 说明 | 返回值 |
|------|------|--------|
| `createNewFile()` | 创建新文件（若文件已存在则返回 false） | `boolean` |
| `mkdir()` | 创建单级目录 | `boolean` |
| `mkdirs()` | 创建多级目录（包括父目录） | `boolean` |

```java
File file = new File("D:/test/hello.txt");
if (!file.exists()) {
    boolean created = file.createNewFile();  // 创建文件
    System.out.println(created ? "创建成功" : "创建失败");
}

File dir = new File("D:/test/a/b/c");
dir.mkdirs();  // 一次性创建多层目录
```

#### 2. 重命名或移动文件：`renameTo()`

- **重命名**：同一目录下改名（改文件名或者目录名）
- **移动**：改变文件所在的目录路径

```java
File src = new File("D:/test/old.txt");
File dest = new File("D:/test/new.txt");
src.renameTo(dest);   // 重命名

// 移动 + 重命名
File target = new File("E:/backup/new.txt");
src.renameTo(target);
```

> 注意：`renameTo()` 在不同文件系统之间移动时可能失败，推荐使用 `Files.move()`（NIO）。

#### 3. 常用辅助方法

| 方法                                | 作用          |
| --------------------------------- | ----------- |
| `getName()`                       | 获取文件名（含扩展名） |
| `getPath()` / `getAbsolutePath()` | 获取路径        |
| `isFile()` / `isDirectory()`      | 判断是文件还是目录   |
| `delete()`                        | 删除文件或空目录    |
| `exists()`                        | 判断是否存在      |

---

### 二、文件内容操作（核心：流）

对文件内容的读写必须通过 **I/O 流** 完成。数据在程序和文件之间传输，就像水流一样，因此称为“流”。

#### 流的分类（以 CPU 为基准）

- **输入流（Input）**：数据从 **硬盘 → 内存 → CPU → 程序**（读取文件内容）
- **输出流（Output）**：数据从 **程序 → CPU → 内存 → 硬盘**（写入文件内容）

| 类型 | 抽象基类 | 适用场景 |
|------|----------|----------|
| 字节流 | `InputStream` / `OutputStream` | 二进制文件（图片、视频、压缩包等） |
| 字符流 | `Reader` / `Writer` | 纯文本文件（.txt, .java, .csv 等） |

文件就是硬盘，读就是数据从硬盘中出来，写就是数据从外面进入硬盘，输入输出是以内存为基准的

---

### 三、字节流读写二进制文件

#### 1. 读文件

```java


InputStream inputStream = new FileInputStream("./test.txt"); 
//InputStream有多个子类，向上转型，可以换成其他子类 
while (true){  
    int c = inputStream.read();  
    if(c == -1){  
        break;  
    }  
    System.out.printf("0x%x\n",c);  
}

InputStream inputStream = new FileInputStream("./test.txt");  
while (true){  
    byte[] bytes = new byte[1024];  
    int c = inputStream.read(bytes);//输出型参数  
    if(c == -1){  
        break;  
    }  
    for (int i = 0; i < c; i++){  
        System.out.printf("0x%x\n",c);  
    }  
}

//其他写法
try (FileInputStream fis = new FileInputStream("a.jpg")) {
    byte[] buffer = new byte[1024];
    int len;
    while ((len = fis.read(buffer)) != -1) {
        // len 表示实际读到的字节数
    }
}

// 一次读取全部（适合小文件）
byte[] allBytes = Files.readAllBytes(Paths.get("a.jpg"));
```

#### 2. 写文件

```java
public static void writeFile(){  
    try (OutputStream outputStream = new FileOutputStream("./tset.txt")){  
        outputStream.write(97);  
        outputStream.write(98);  
        outputStream.write(99);  
    }catch (IOException e){  
        e.printStackTrace();  
    }  
}
```


```java
public static void writeFile(){  
//每次打开文件之前都会清空文件，所以不会产生覆盖
        try (OutputStream outputStream = new FileOutputStream("./tset.txt")){  
//            outputStream.write(97);  
//            outputStream.write(98);  
//            outputStream.write(99);  
            byte[] bytes = {  
                (byte) 0xe4, (byte) 0xbd, (byte) 0xa0, (byte) 0xe5,  
            };  
        }catch (IOException e){  
            e.printStackTrace();  
        }  
    }
```

**追加写入**

```java
public static void writeFile(){  
        try (OutputStream outputStream = new FileOutputStream("./tset.txt",true)){  
//            outputStream.write(97);  
//            outputStream.write(98);  
//            outputStream.write(99);  
            byte[] bytes = {  
                (byte) 0xe4, (byte) 0xbd, (byte) 0xa0, (byte) 0xe5,  
            };  
        }catch (IOException e){  
            e.printStackTrace();  
        }  
    }
```


> 💡 **追加模式**：构造 `FileOutputStream` 时传入 `true`，新内容会写在文件末尾。

---

### 四、关闭文件与资源管理

#### 1. 为什么必须关闭流？

- 操作系统会为每个进程维护一个 **文件描述符表**（表长度有限）
- 若不关闭流，文件描述符会被耗尽，导致“打开太多文件”的错误
- 未关闭的输出流可能导致数据滞留缓冲区，内容未真正写入硬盘


#### 2. 正确关闭的方式

**错误关闭**  

```java
public static void readFile(){  
    try {  
        InputStream inputStream = new FileInputStream("./test.txt");  
        while (true){  
            byte[] bytes = new byte[1024];  
            int c = inputStream.read(bytes);//输出型参数  
            if(c == -1){  
                break;  
            }  
            for (int i = 0; i < c; i++){  
                System.out.printf("0x%x\n",c);  
            }  
        }  
        inputStream.close();  
    } catch (IOException e) {  
        e.printStackTrace();  
    }  
}
```

 **科学写法**

```java
public static void readFile(){  
    InputStream inputStream = null;  
    try {  
        inputStream = new FileInputStream("./test.txt");  
  
        while (true){  
            byte[] bytes = new byte[1024];  
            int c = inputStream.read(bytes);//输出型参数  
            if(c == -1){  
                break;  
            }  
            for (int i = 0; i < c; i++){  
                System.out.printf("0x%x\n",c);  
            }  
        }  
    } catch (IOException e) {  
        e.printStackTrace();  
    }finally {  
        try {  
            //保证在read之后再关闭文件  
            if(inputStream != null){  
                inputStream.close();  
            }  
        }catch (IOException e){  
            e.printStackTrace();  
        }  
    }  
}
```

**try with resourse 简化代码 **

```java
 public static void readFile(){  
          
        //try执行结束后自动调用close  
        try (InputStream inputStream = new FileInputStream("./test.txt")){  
  
            while (true){  
                byte[] bytes = new byte[1024];  
                int c = inputStream.read(bytes);//输出型参数  
                if(c == -1){  
                    break;  
                }  
                for (int i = 0; i < c; i++){  
                    System.out.printf("0x%x\n",c);  
                }  
            }  
              
        } catch (IOException e) {  
            e.printStackTrace();  
        }  
    }  
}
```

---

### 五、字符流读写文本文件

字符流用于处理 **文本文件**，自动处理字符编码（如 UTF-8, GBK）。

| 字符流类 | 用途 |
|----------|------|
| `FileReader` / `FileWriter` | 读写文本文件（使用系统默认编码，不推荐跨平台） |
| `InputStreamReader` / `OutputStreamWriter` | 可指定编码的字符流（推荐） |
| `BufferedReader` / `BufferedWriter` | 带缓冲，提供 `readLine()` 方法 |

#### 代码示例（推荐写法）

```java
public static void readFileTest(){  
    try(Reader reader = new FileReader("./test.txet")) {  
        while (true){  
            int c = reader.read();  
            if(c == -1){  
                break;  
            }  
        }  
    }catch (IOException e){  
        e.printStackTrace();  
    }  
}
```



```java
// 读文本文件（指定 UTF-8 编码）
try (BufferedReader reader = new BufferedReader(
        new InputStreamReader(new FileInputStream("note.txt"), StandardCharsets.UTF_8))) {
    String line;
    while ((line = reader.readLine()) != null) {
        System.out.println(line);
    }
}

// 写文本文件（指定编码，可追加）
try (BufferedWriter writer = new BufferedWriter(
        new OutputStreamWriter(new FileOutputStream("note.txt", true), StandardCharsets.UTF_8))) {
    writer.write("新增一行文字");
    writer.newLine();   // 写入换行符
}
```

> ⚠️ 注意：  
> - 直接用 `FileReader` 会使用系统默认编码，在 Windows（GBK）和 Linux（UTF-8）下可能乱码  
> - 推荐使用 `InputStreamReader` / `OutputStreamWriter` 显式指定编码

---

### 六、补充：文件移动的最佳实践（Java NIO）

虽然 `renameTo()` 可以移动文件，但更强大、更可靠的是 `Files.move()`（JDK 7+）：

```java
Path source = Paths.get("D:/test/src.txt");
Path target = Paths.get("E:/backup/dest.txt");
Files.move(source, target, StandardCopyOption.REPLACE_EXISTING);
```

优势：
- 支持跨文件系统移动
- 可指定覆盖、原子移动等选项

---

### 附：脑图式的总结

```text
文件操作
├── File 类
│   ├── createNewFile / mkdir / mkdirs
│   ├── renameTo（重命名/移动）
│   └── exists, delete, getName...
├── 流（Stream）
│   ├── 字节流（二进制）
│   │   ├── InputStream → FileInputStream
│   │   └── OutputStream → FileOutputStream
│   └── 字符流（文本）
│       ├── Reader → InputStreamReader → FileReader
│       └── Writer → OutputStreamWriter → FileWriter
├── 读文件
│   ├── 单字节（慢）
│   ├── 字节数组缓冲（快）
│   └── Files.readAllBytes
├── 写文件
│   └── 覆盖 / 追加（构造时传 true）
├── 关闭资源
│   ├── 手动 close（finally）
│   └── try-with-resources（自动关闭）
└── 文件移动（NIO）: Files.move
```

---

这份笔记已覆盖你大纲中的所有要点，并补充了编码选择、NIO 替代方案以及资源管理的原理。你可以把它当作复习材料或开发参考。如果有特定部分需要更详细的代码或例子，欢迎告诉我！