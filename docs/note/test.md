# JAVA遇到的那些笔试机试

## 1.输入大小写英文，自动转换相反大小写，顺序相反 输入aBcD返回dCbA

```java
/**
 * @description:输入大小写英文，自动转换相反大小写，顺序相反 输入aBcD返回dCbA
 * @author: MayDay
 * @time: 2020/5/27 10:35
 */
public class JavaTest01 {
    public static void main(String[] args) {
        String s = changeS();
        System.out.println(s);
    }

    public static String changeS() {
        Scanner sc = new Scanner(System.in);
        String s = sc.next();
        char[] chars = s.toCharArray();
        StringBuilder builder = new StringBuilder();
        for (char aChar : chars) {
            if (aChar >= 'a' && aChar <= 'z') {
                builder.append(String.valueOf(aChar).toUpperCase());
            } else if (aChar >= 'A' && aChar <= 'Z') {
                builder.append(String.valueOf(aChar).toLowerCase());
            }
        }
        String s1 = builder.reverse().toString();
        return s1;
    }
}
```

## 2.编写函数测试是否回文数

```java
/**
 * @description:判断是否回文数
 * @author: MayDay
 * @time: 2020/5/27 11:16
 */
public class JavaTest02 {
    public static void main(String[] args) {
        change();
    }

    public static void change() {
        Scanner sc = new Scanner(System.in);
        String str = sc.next();
        StringBuilder s = new StringBuilder(str);
        s.reverse();
        int count = 0;
        for (int i = 0; i < str.length(); i++) {
            if (str.charAt(i) == s.charAt(i)) {
                count++;
            }
        }
        if (str.length() == count) {
            System.out.println(str + "是回文数");
        } else {
            System.out.println(str + "不是回文数");
        }
    }
}
```

## 3.列举JSP内置对象，并说明同一应用中页面传值有哪些方式？

>  jsp有九大内置对象，分别为：request，reponse,page,pagecontext,session,application,execption,config,out，param。

同一应用中页面间的传值方式：

- 1)URL传值；
-  2）表单传值；
-  3）Cookie方法；
-  4）Session方法；

## 4.一个发送邮件功能,用户反映没有收到邮件,如何处理bug

- 1.发送成功，然后向用户查证一下邮箱账号，

- 2.当做垃圾邮件放在垃圾箱中，让用户检查一下垃圾箱。
- 1.先看服务器反馈是否成功，如果不成功。在代码里打日志log,特别是try catch里把catch到的错误都打印出来，发送后看错误日志去判断原因。如果成功，检查发送代码。在代码里打日志log,特别是try catch里把catch到的错误都打印出来，发送后看错误日志去判断原因。

## 5.找出数组中只出现一次的数字，其它数字都出现了两次

```java
/**
 * @description:
 * @author: MayDay
 * @time: 2020/5/27 12:10
 */
public class JavaTest03 {
    public static void main(String[] args) {
        int[] arr = {1, 2, 4, 2, 1};
        fun2(arr);
    }

    /**
     * 键值对map找出次数为1的key(通用）
     *
     * @param arr
     */
    public static void fun1(int[] arr) {
        TreeMap<Integer, Integer> treeMap = new TreeMap<>();
        for (int i : arr) {
            if (treeMap.containsKey(i)) {
                treeMap.put(i, treeMap.get(i) + 1);
            } else {
                treeMap.put(i, 1);
            }
        }
        for (Map.Entry<Integer, Integer> entry : treeMap.entrySet()) {
            if (entry.getValue() == 1) {
                System.out.println(entry.getKey());
            }
        }
    }

    /**
     * 有且只有一个数字出现一次用异或（可选
     * ^是异或运算符，异或的规则是转换成二进制比较，相同为0，不同为1.
     *
     * @param arr
     */
    public static void fun2(int[] arr) {
        int a = arr[0];
        for (int i = 1; i < arr.length; i++) {
            a = a ^ arr[i];
        }
        System.out.println(a);
    }
}
```

## 6.方阵顺时针旋转90度(方案2佳)

```java
/**
 * @description:Java 数组转置 方阵顺时针旋转90度
 * <p>
 * 可以看出，将二维数组顺时针旋转90度，就是将其转置后的数组的列进行前后交换（即第一列变为最后一列，第二列变为倒数第二列）
 * @author: MayDay
 * @time: 2020/5/27 12:55
 */
public class JavaTest04 {
    public static void main(String args[]) {
        int data[][] = new int[][]{{1, 2, 3}, {4, 5, 6}, {7, 8, 9}};
        reverse(data);
        exchange(data);
        print1(data);
    }

    //将矩阵转置
    public static void reverse(int temp[][]) {
        for (int i = 0; i < temp.length; i++) {
            for (int j = i; j < temp[i].length; j++) {
                int k = temp[i][j];
                temp[i][j] = temp[j][i];
                temp[j][i] = k;
            }
        }
    }

    //将转置后的矩阵的列交换（第一列跟最后一列交换，第二列跟倒数第二列交换）
    public static void exchange(int temp[][]) {
        int a = 0;
        int b = temp.length - 1;
        for (int i = 0; i < (temp.length) / 2; i++) {
            for (int j = 0; j < temp.length; j++) {
                int k = temp[j][a];
                temp[j][a] = temp[j][b];
                temp[j][b] = k;
            }
            a++;
            b--;
        }
    }

    //将矩阵输出
    public static void print1(int temp[][]) {
        for (int i = 0; i < temp.length; i++) {
            for (int j = 0; j < temp[i].length; j++) {
                System.out.print(temp[i][j] + "\t");
            }
            System.out.println();
        }
    }
}

```

```java
/**
 * @description:Java 数组转置 方阵顺时针旋转90度
 * 列号变为行号
 * （n-行号）变为列号（其中n为方阵维数减1）
 * 若要将矩阵顺时针旋转180，两次调用旋转90度的函数就可以。
 * @author: MayDay
 * @time: 2020/5/27 12:55
 */
public class JavaTest04_2 {
    public static void main(String args[]) {
        int data[][] = new int[][]{{1, 2, 3}, {4, 5, 6}, {7, 8, 9}};
        rotate(data);
        print1(data);
    }

    //旋转90度
    public static void rotate(int temp[][]) {
        int len = temp.length;
        int b[][] = new int[len][len];
        for (int i = 0; i < len; i++) {
            for (int j = 0; j < len; j++) {
                b[j][len - 1 - i] = temp[i][j];
            }
        }
        for (int i = 0; i < len; i++)
            for (int j = 0; j < len; j++)
                temp[i][j] = b[i][j];
    }

    //输出
    public static void print1(int temp[][]) {
        for (int i = 0; i < temp.length; i++) {
            for (int j = 0; j < temp[i].length; j++) {
                System.out.print(temp[i][j] + "\t");
            }
            System.out.println();
        }
    }
}

```

