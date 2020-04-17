# 缓冲流、转换流、序列化流、打印流

## 1.1 概述

缓冲流,也叫高效流，是对4个基本的`FileXxx` 流的增强，所以也是4个流，按照数据类型分类：

* **字节缓冲流**：`BufferedInputStream`，`BufferedOutputStream` 
* **字符缓冲流**：`BufferedReader`，`BufferedWriter`

缓冲流的基本原理，是在创建流对象时，会创建一个内置的默认大小的缓冲区数组，通过缓冲区读写，减少系统IO次数，从而提高读写的效率。

```java
 //    java.io.BufferedOutputStream extends OutputStream
//    BufferedOutputStream:字节缓冲输出流
//
//    继承自父类的共性成员方法:
//        - public void close() ：关闭此输出流并释放与此流相关联的任何系统资源。
//        - public void flush() ：刷新此输出流并强制任何缓冲的输出字节被写出。
//        - public void write(byte[] b)：将 b.length字节从指定的字节数组写入此输出流。
//        - public void write(byte[] b, int off, int len) ：从指定的字节数组写入 len字节，从偏移量 off开始输出到此输出流。
//        - public abstract void write(int b) ：将指定的字节输出流。
//
//     构造方法:
//        BufferedOutputStream(OutputStream out)  创建一个新的缓冲输出流，以将数据写入指定的底层输出流。
//        BufferedOutputStream(OutputStream out, int size)  创建一个新的缓冲输出流，以将具有指定缓冲区大小的数据写入指定的底层输出流。
//        参数:
//           OutputStream out:字节输出流
//                我们可以传递FileOutputStream,缓冲流会给FileOutputStream增加一个缓冲区,提高FileOutputStream的写入效率
//           int size:指定缓冲流内部缓冲区的大小,不指定默认
//     使用步骤(重点)
//        1.创建FileOutputStream对象,构造方法中绑定要输出的目的地
//        2.创建BufferedOutputStream对象,构造方法中传递FileOutputStream对象对象,提高FileOutputStream对象效率
//        3.使用BufferedOutputStream对象中的方法write,把数据写入到内部缓冲区中
//        4.使用BufferedOutputStream对象中的方法flush,把内部缓冲区中的数据,刷新到文件中
//        5.释放资源(会先调用flush方法刷新数据,第4部可以省略)
    public static void Test01() throws IOException {
        FileOutputStream fos = new FileOutputStream("test\\txt\\a.txt");
        BufferedOutputStream bos = new BufferedOutputStream(fos);
        bos.write("写入数据的字节类型".getBytes());
        bos.close();
    }

 // 继承自父类的成员方法:
    //     int read()从输入流中读取数据的下一个字节。
    //     int read(byte[] b) 从输入流中读取一定数量的字节，并将其存储在缓冲区数组 b 中。
    //     void close() 关闭此输入流并释放与该流关联的所有系统资源。

    // 构造方法:
    //     BufferedInputStream(InputStream in) 创建一个 BufferedInputStream 并保存其参数，即输入流 in，以便将来使用。
    //     BufferedInputStream(InputStream in, int size) 创建具有指定缓冲区大小的 BufferedInputStream 并保存其参数，即输入流 in，以便将来使用。
    //     参数:
    //         InputStream in:字节输入流
    //             我们可以传递FileInputStream,缓冲流会给FileInputStream增加一个缓冲区,提高FileInputStream的读取效率
    //         int size:指定缓冲流内部缓冲区的大小,不指定默认

    // 使用步骤(重点):
    //     1.创建FileInputStream对象,构造方法中绑定要读取的数据源
    //     2.创建BufferedInputStream对象,构造方法中传递FileInputStream对象,提高FileInputStream对象的读取效率
    //     3.使用BufferedInputStream对象中的方法read,读取文件
    //     4.释放资源

public static void Test02() throws IOException {
        BufferedInputStream bis = new BufferedInputStream(new FileInputStream("test\\txt\\a.txt"));
        byte[] bytes = new byte[10240];
        int len = 0;
        while ((len = bis.read(bytes)) != -1) {
            System.out.println(new String(bytes, 0, len));
        }
        bis.close();
    }
```

```java
    // java.io.BufferedWriter extends Writer
    // BufferedWriter:字符缓冲输出流

    // 继承自父类的共性成员方法:
    //     - void write(int c) 写入单个字符。
    //     - void write(char[] cbuf)写入字符数组。
    //     - abstract  void write(char[] cbuf, int off, int len)写入字符数组的某一部分,off数组的开始索引,len写的字符个数。
    //     - void write(String str)写入字符串。
    //     - void write(String str, int off, int len) 写入字符串的某一部分,off字符串的开始索引,len写的字符个数。
    //     - void flush()刷新该流的缓冲。
    //     - void close() 关闭此流，但要先刷新它。

    // 构造方法:
    //     BufferedWriter(Writer out) 创建一个使用默认大小输出缓冲区的缓冲字符输出流。
    //     BufferedWriter(Writer out, int sz) 创建一个使用给定大小输出缓冲区的新缓冲字符输出流。
    //     参数:
    //         Writer out:字符输出流
    //             我们可以传递FileWriter,缓冲流会给FileWriter增加一个缓冲区,提高FileWriter的写入效率
    //         int sz:指定缓冲区的大小,不写默认大小

    // 特有的成员方法:
    //     void newLine() 写入一个行分隔符。会根据不同的操作系统,获取不同的行分隔符
    //     换行:换行符号
    //     windows:\r\n
    //     linux:/n
    //     mac:/r
    //  使用步骤:
    //     1.创建字符缓冲输出流对象,构造方法中传递字符输出流
    //     2.调用字符缓冲输出流中的方法write,把数据写入到内存缓冲区中
    //     3.调用字符缓冲输出流中的方法flush,把内存缓冲区中的数据,刷新到文件中
    //     4.释放资源
public static void Test03() throws IOException {
        BufferedWriter bw = new BufferedWriter(new FileWriter("test\\txt\\a.txt"));
        for (int i = 0; i < 10; i++) {
            bw.write("程序员的世界");
            bw.newLine();
        }
        bw.close();
    }

 // java.io.BufferedReader extends Reader
    // BufferedReader:字符缓冲输入流

    // 继承自父类的共性成员方法:
    //     int read() 读取单个字符并返回。
    //     int read(char[] cbuf)一次读取多个字符,将字符读入数组。
    //     void close() 关闭该流并释放与之关联的所有资源。

    //  构造方法:
    //     BufferedReader(Reader in)  创建一个使用默认大小输入缓冲区的缓冲字符输入流。
    //     BufferedReader(Reader in, int sz)     创建一个使用指定大小输入缓冲区的缓冲字符输入流。
    //     参数:
    //         Reader in:字符输入流
    //             我们可以传递FileReader,缓冲流会给FileReader增加一个缓冲区,提高FileReader的读取效率
    //  特有的成员方法:
    //     String readLine() 读取一个文本行。读取一行数据
    //         行的终止符号:通过下列字符之一即可认为某行已终止：换行 ('\n')、回车 ('\r') 或回车后直接跟着换行(\r\n)。
    //     返回值:
    //         包含该行内容的字符串，不包含任何行终止符，如果已到达流末尾，则返回 null

    //  使用步骤:
    //     1.创建字符缓冲输入流对象,构造方法中传递字符输入流
    //     2.使用字符缓冲输入流对象中的方法read/readLine读取文本
    //     3.释放资源
public static void Test04() throws IOException {
        BufferedReader br = new BufferedReader(new FileReader("test\\txt\\a.txt"));
        String line;
        while ((line = br.readLine()) != null) {
            System.out.println(line);
        }
        br.close();
    }
```

# 第二章 转换流

## 2.1 字符编码和字符集

### 字符编码

计算机中储存的信息都是用二进制数表示的，而我们在屏幕上看到的数字、英文、标点符号、汉字等字符是二进制数转换之后的结果。按照某种规则，将字符存储到计算机中，称为**编码** 。反之，将存储在计算机中的二进制数按照某种规则解析显示出来，称为**解码** 。比如说，按照A规则存储，同样按照A规则解析，那么就能显示正确的文本符号。反之，按照A规则存储，再按照B规则解析，就会导致乱码现象。

编码:字符(能看懂的)--字节(看不懂的)

解码:字节(看不懂的)-->字符(能看懂的)

* **字符编码`Character Encoding`** : 就是一套自然语言的字符与二进制数之间的对应规则。

  编码表:生活中文字和计算机中二进制的对应规则

### 字符集

* **字符集 `Charset`**：也叫编码表。是一个系统支持的所有字符的集合，包括各国家文字、标点符号、图形符号、数字等。

计算机要准确的存储和识别各种字符集符号，需要进行字符编码，一套字符集必然至少有一套字符编码。常见字符集有ASCII字符集、GBK字符集、Unicode字符集等。

可见，当指定了**编码**，它所对应的**字符集**自然就指定了，所以**编码**才是我们最终要关心的。

* **ASCII字符集** ：
  * ASCII（American Standard Code for Information Interchange，美国信息交换标准代码）是基于拉丁字母的一套电脑编码系统，用于显示现代英语，主要包括控制字符（回车键、退格、换行键等）和可显示字符（英文大小写字符、阿拉伯数字和西文符号）。
  * 基本的ASCII字符集，使用7位（bits）表示一个字符，共128字符。ASCII的扩展字符集使用8位（bits）表示一个字符，共256字符，方便支持欧洲常用字符。
* **ISO-8859-1字符集**：
  * 拉丁码表，别名Latin-1，用于显示欧洲使用的语言，包括荷兰、丹麦、德语、意大利语、西班牙语等。
  * ISO-8859-1使用单字节编码，兼容ASCII编码。
* **GBxxx字符集**：
  * GB就是国标的意思，是为了显示中文而设计的一套字符集。
  * **GB2312**：简体中文码表。一个小于127的字符的意义与原来相同。但两个大于127的字符连在一起时，就表示一个汉字，这样大约可以组合了包含7000多个简体汉字，此外数学符号、罗马希腊的字母、日文的假名们都编进去了，连在ASCII里本来就有的数字、标点、字母都统统重新编了两个字节长的编码，这就是常说的"全角"字符，而原来在127号以下的那些就叫"半角"字符了。
  * **GBK**：最常用的中文码表。是在GB2312标准基础上的扩展规范，使用了双字节编码方案，共收录了21003个汉字，完全兼容GB2312标准，同时支持繁体汉字以及日韩汉字等。
  * **GB18030**：最新的中文码表。收录汉字70244个，采用多字节编码，每个字可以由1个、2个或4个字节组成。支持中国国内少数民族的文字，同时支持繁体汉字以及日韩汉字等。
* **Unicode字符集** ：
  * Unicode编码系统为表达任意语言的任意字符而设计，是业界的一种标准，也称为统一码、标准万国码。
  * 它最多使用4个字节的数字来表达每个字母、符号，或者文字。有三种编码方案，UTF-8、UTF-16和UTF-32。最为常用的UTF-8编码。
  * UTF-8编码，可以用来表示Unicode标准中任何字符，它是电子邮件、网页及其他存储或传送文字的应用中，优先采用的编码。互联网工程工作小组（IETF）要求所有互联网协议都必须支持UTF-8编码。所以，我们开发Web应用，也要使用UTF-8编码。它使用一至四个字节为每个字符编码，编码规则：
    1. 128个US-ASCII字符，只需一个字节编码。
    2. 拉丁文等字符，需要二个字节编码。 
    3. 大部分常用字（含中文），使用三个字节编码。
    4. 其他极少使用的Unicode辅助字符，使用四字节编码。