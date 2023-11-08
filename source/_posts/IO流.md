---
title: IO流
date: 2023-05-24 23:44:04
tags: IO流
---

# IO流

io是相对于内存而言的

In：将外部的数据读入内存中

Out：将内存中的数据保存到外部

## 字节流（每次以一个字节或一组字节作为单位）

### InputStream（读）

#### FileInputStream（文件读）

`````java
File file = new File("io.txt");
byte[] data = new byte[1024];
int len = 0;
InputStream inputStream = null;//创建流
try {
    inputStream = new FileInputStream(file);
    len = inputStream.read(data);
} catch (FileNotFoundException e) {
    throw new RuntimeException(e);
} catch (IOException e) {
    throw new RuntimeException(e);
}finally {
    if(inputStream != null){//判断流是否为空
        try {
            inputStream.close();//关闭流
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }
}
System.out.println(new String(data, 0, len));
//123
`````

### OutputStream（写）

#### FileOutputStream（文件的写）

`````java
File file = new File("io.txt");
byte[] data = "hello world".getBytes();
int len = 0;
OutputStream outputStream = null;//创建流
try {
    outputStream = new FileOutputStream(file);
    outputStream.write(data);
} catch (FileNotFoundException e) {
    throw new RuntimeException(e);
} catch (IOException e) {
    throw new RuntimeException(e);
}finally {
    if(outputStream != null){
        try {
            outputStream.close();
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }
}
//io.txt中：hello world
`````

## 带缓冲的字节流（装饰模式）

### BufferedInputStream（带缓冲的输入流）

```java
File file = new File("io.txt");
byte[] data = new byte[1024];
int len = 0;
BufferedInputStream bufferedInputStream = null;//创建流
try {
    bufferedInputStream = new BufferedInputStream(new FileInputStream(file));
    len = bufferedInputStream.read(data);
} catch (FileNotFoundException e) {
    throw new RuntimeException(e);
} catch (IOException e) {
    throw new RuntimeException(e);
}finally {
    if(bufferedInputStream != null){
        try {
            bufferedInputStream.close();
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }
}
System.out.println(new String(data, 0, len));
//hello world
```

### BufferedOutputStream（带缓冲的输出流）

```java
File file = new File("io.txt");
byte[] data = "使用BufferedOutputStream：hello world".getBytes();
BufferedOutputStream bufferedOutputStream = null;//创建流
try {
    bufferedOutputStream = new BufferedOutputStream(new FileOutputStream(file));
    bufferedOutputStream.write(data);//写入文件
} catch (FileNotFoundException e) {
    throw new RuntimeException(e);
} catch (IOException e) {
    throw new RuntimeException(e);
}finally {
    if(bufferedOutputStream != null){
        try {
            bufferedOutputStream.close();
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }
}
//io.txt中：使用BufferedOutputStream：hello world
```

## 字符流（每次以一个字符或一组字符作为单位）

### Reader（读）

```java
File file = new File("字符流io.txt");
Reader reader = null;
int len = 10;
char[] data = new char[1024];
try {
    reader = new FileReader(file);
    int result = reader.read(data);//-1为已读完
    while(result != -1){
        System.out.print(new String(data,0,result));
        result = reader.read(data,0,len);
    }
} catch (FileNotFoundException e) {
    throw new RuntimeException(e);
} catch (IOException e) {
    throw new RuntimeException(e);
}finally {
    if (reader != null){
        try {
            reader.close();
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }
}
```

### Writer（写）

```java
File file = new File("字符流io.txt");
String str = "使用字符流（Writer）输出的字符串";
Writer writer = null;
try {
    writer = new FileWriter(file);
    writer.write(str);
} catch (IOException e) {
    throw new RuntimeException(e);
}finally {
    if(writer != null){
        try {
            writer.close();
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }
}
//字符流io.txt中：使用字符流（Writer）输出的字符串
```

## 带缓冲的字符流（装饰模式）

### BufferedReader

```java
File file = new File("字符流io.txt");
BufferedReader bufferedReader = null;
char[] data = new char[10];
try {
    bufferedReader = new BufferedReader(new FileReader(file));
    int result = bufferedReader.read(data);//-1为已读完
    while(result != -1){
        System.out.print(new String(data,0,result));
        result = bufferedReader.read(data);
    }
} catch (FileNotFoundException e) {
    throw new RuntimeException(e);
} catch (IOException e) {
    throw new RuntimeException(e);
}finally {
    if (bufferedReader != null){
        try {
            bufferedReader.close();
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }
}
```

### BufferedWriter

```java
File file = new File("字符流io.txt");
String str = "使用带缓冲的字符流（BufferedWriter）输出的字符串";
BufferedWriter bufferedWriter = null;
try {
    bufferedWriter = new BufferedWriter(new FileWriter(file));
    bufferedWriter.write(str);
} catch (IOException e) {
    throw new RuntimeException(e);
}finally {
    if(bufferedWriter != null){
        try {
            bufferedWriter.close();
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }
}
```

## 字节流与字符流的对比

### 字节流

```java
//字节输入流
byte[] data = new byte[1024];//字节数组
inputStream.read();
inputStream.read(byte[] b);
inputStream.read(byte[] b, int off, int len);

//字节输出流
byte[] data = "hello world".getBytes();//需要将输入内容转换为字节
outputStream.write(int b);
outputStream.write(byte[] b);
outputStream.write(byte[] b, int off, int len);
```

### 字符流

```java
//字符输入流
char[] data = new char[1024];//字符数组
reader.read();
reader.read(char[] cbuf);
reader.read(CharBuffer target);
reader.read(char[] cbuf,int off,int len);

//字符输出流
String str = "使用字符流（Writer）输出的字符串";//直接是字符串对象
writer.write(char c);
writer.write(String str);
writer.write(char[] cbuf);
writer.write(String str,int off,int len);
writer.write(char[] cbuf,int off, int len);
```

## 装饰模式

装饰模式又名包装(Wrapper)模式。装饰模式以对客户端透明的方式扩展对象的功能，是继承关系的一个替代方案。





## IO流使用步骤 （每个流都有异常需要捕获）

1.   #### 确定要读写的文件

     *   读文件：文件必须存在，否则抛出异常
     *   写文件：
         *   文件不存在：创建
         *   文件已存在：
             *   覆盖：默认
             *   追加：构造方法第二个参数传入true
         *   路径不存在：抛出异常

2.   #### 创建指向文件的IO流

3.   #### 读写操作

4.   #### 关闭流

## 对象的持久化存储

Java中的对象如果想要进行持久话存储需要实现`Serializable`（序列化接口）

`Serializable`：这是一个标志性接口 接口内部为空 不需要重写任何方法。

其他标志性接口还有`Cloneable`（可克隆的）`clone`方法是属于`Object`对象的而不是`Cloneable`接口

