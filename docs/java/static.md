# String类、static关键字、Arrays类、Math类

## String类

- public boolean equals (Object anObject) ：将此字符串与指定对象进行比较。

- public boolean equalsIgnoreCase (String anotherString) ：将此字符串与指定对象进行比较，忽略大小写。

- public int length () ：返回此字符串的长度。

- public String concat (String str) ：将指定的字符串连接到该字符串的末尾。

- public char charAt (int index) ：返回指定索引处的 char值。

- public int indexOf (String str) ：返回指定子字符串第一次出现在该字符串内的索引。

- public String substring (int beginIndex) ：返回一个子字符串，从beginIndex开始截取字符串到字符串结尾。

- public String substring (int beginIndex, int endIndex) ：返回一个子字符串，从beginIndex到
  endIndex截取字符串。含beginIndex，不含endIndex。

  !>[begin,end)，包含左边，不包含右边。

- public char[] toCharArray () ：将此字符串转换为新的字符数组。

- public byte[] getBytes () ：使用平台的默认字符集将该 String编码转换为新的字节数组。

- public String replace (CharSequence target, CharSequence replacement) ：将与target匹配的字符串使用replacement字符串替换。

- public String[] split(String regex) ：将此字符串按照给定的regex（规则）拆分为字符串数组。

## static关键字

> 静态方法调用的注意事项：
>
> 1. 静态方法可以直接访问类变量和静态方法。
> 2. 静态方法不能直接访问普通成员变量或成员方法。反之，成员方法可以直接访问类变量或静态方法。
> 3. 静态方法中，不能使用this关键字。

## Arrays类

- public static String toString(int[] a) ：返回指定数组内容的字符串表示形式。
- public static void sort(int[] a) ：对指定的 int 型数组按数字升序进行排序。

## Math类

- public static double abs(double a) ：返回 double 值的绝对值。
- public static double ceil(double a) ：返回大于等于参数的最小的整数
- public static double floor(double a) ：返回小于等于参数最大的整数。
- public static long round(double a) ：返回最接近参数的 long。(相当于四舍五入方法)