

# IDEA使用

## 快捷键

Alt+Enter 导入包，自动修正代码
Ctrl+Y 删除光标所在行
Ctrl+D 复制光标所在行的内容，插入光标位置下面
Ctrl+Alt+L 格式化代码
Ctrl+/ 单行注释
Ctrl+Shift+/ 选中代码注释，多行注释，再按取消注释
Alt+Ins 自动生成代码，toString，get，set等方法
Alt+Shift+上下箭头 移动当前代码行

## 快捷输入代码

psvm  快速生成main主程序类
sout 快速生成打印方法
@包名   自动引用

listfori 快速生成for循环list

## 注释模板

### 类注释

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200221230750.png)

```java
/**
 *
 *@description: 
 *@author: MayDay
 *@time: ${DATE} ${TIME}
 * 
 */
```

!>类注释：在新建一个类的时候自动添加。

### 方法注释

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200221231418.png)

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200221231526.png)

```java
/** 
 * @description 
 * @author $author$
 * @date $date$ $time$
$params$
 * @return $return$
 */
```

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200221232023.png)

!>其中params需要写脚本，内容如下:groovyScript("def result=''; def params=\"${_1}\".replaceAll('[\\\\[|\\\\]|\\\\s]', '').split(',').toList(); for(i = 0; i < params.size(); i++) {result+=' * @param ' + params[i] + ((i < params.size() - 1) ? '\\n' : '')}; return result", methodParameters())

!>如果注释有红框可以勾选skip if default；