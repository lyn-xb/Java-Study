# [CS 61B]12、Hashing

## 一些问题

目前已经学习了一些数据结构，用于有效地搜索数据结构中项目的存在。例如二叉搜索树，2-3树等平衡二叉搜索树。但是，这些结构存在一定限制：

- 它们要求项目具有可比性，对于某些对象，这个问题可能没有意义
- 它们给出的时间复杂度为 Θ(*logn*)，但是可以做到更好的性能

## 思考1

创建一个类型和大小为 2 亿的 ArrayList。默认情况下，让一切都为`false`

+ `add(int x)`只是将 ArrayList 中`x`的位置设置为 `true`，只需 Θ(*1*) 时间
+ `contains(int x)`仅返回 ArrayList 中`x`的位置是 `true`还是 `false`，也只需要 Θ(*1*) 时间

```java
public class DataIndexedIntegerSet {
    private boolean[] present;

    public DataIndexedIntegerSet() {
        present = new boolean[2000000000];
    }

    public void add(int x) {
        present[i] = true;
    }

    public boolean contains(int x) {
        return present[i];
    }
```

这样的方式存在一定的问题：

- 浪费未使用的长度

- 不支持其他类型，如`String`

  

## 思考2 ：使用第一个字母支持`String`（只包含字母）

这种方式导致第一个字母相同的字符串出现冲突的，显然不合理

## 思考3：改进

一个四位数字，比如 5149，可以写成

5⋅10^3+1⋅10^2+4⋅10^1+9⋅10^0

实际上，**任何** 4 位数字都可以以这种形式**唯一地**写入，10在这里很重要。如果我们选择一个错误的数字，比如 2不可以作为基数，会发生冲突：

*a*,*b*,*c*,*d* = 1,1,1,1 => 1⋅2^3+1⋅2^2+1⋅2^1+1⋅2^0 = 15
*a*,*b*,*c*,*d* = 0,3,1,1 => 0⋅2^3+3⋅2^2+1⋅2^1+1⋅2^0 = 15

而10不会发生冲突，因为十进制系统中有 10 个唯一数字：0，1，2，3，4，5，6，7，8，9

同样英语小写字母中有26个唯一字符。让*a*=1，*b*=2，...，*z*=26。可以以 **26 为基数**编写任何唯一的小写字符串

- “cat” = “c” 26^2+ “a”* 26^1+ “t” 26^0=2074

这种表示形式为每个包含小写字母的英语单词提供一个唯一的整数，就像使用基数 10 为每个数字提供唯一的表示形式一样，并且不会发生碰撞

```java
public class DataIndexedEnglishWordSet {
    private boolean[] present;

    public DataIndexedEnglishWordSet() {
        present = new boolean[2000000000];
    }

    public void add(String s) {
        present[englishToInt(s)] = true;
    }

    public boolean contains(int i) {
        resent present[englishToInt(s)];
    }
}
public static int letterNum(String s, int i) {
    /** Converts ith character of String to a letter number.
    * e.g. 'a' -> 1, 'b' -> 2, 'z' -> 26 */
    int ithChar = s.charAt(i)
    if ((ithChar < 'a') || (ithChar > 'z')) {
        throw new IllegalArgumentException();
    }

    return ithChar - 'a' + 1;
}

public static int englishToInt(String s) {
    int intRep = 0;
    for (int i = 0l i < s.length(); i += 1) {
        intRep = intRep * 26;
        intRep += letterNum(s, i);
    }

    return intRep;
}
```

要使得这种方式支持所有的字符，可以使用 `ASCII`(支持有限) 或者 `UTF`(支持所有字符)的字符格式，每个字符对应一个整数

```java
public static int asciiToInt(String s) {
    int intRep = 0;
    for (int i = 0; i < s.length(); i += 1) {           
        intRep = intRep * 126;
        intRep = intRep + s.charAt(i);
    }
    return intRep;
}
```

存在的问题：

+ 即使采用正确的基，也可能会出现碰撞，因为转换后的数字可让会溢出`int`=>导致碰撞
+ 空间占用多
+ 如何将任意数据转换为索引

## 思考4：碰撞处理(链地址法)

改变数组，使其不包含项目，而是包含项目的链接列表（或任何其他列表）
如果我们得到一个新项目，并且它的哈希码是 `h`：

- 如果目前索引 `h`处为空，则创建一个新`LinkedList`到`h`处，新项添加到链表中 
- 如果不为空，则将新项目添加到该项中。**注：不允许有任何重复的项目/键。因此必须首先检查尝试插入的项目是否已经在此链接列表中。这也意味着我们将插入到链表的 END，因为我们无论如何都需要检查所有元素**

运行时分析：

+ `add`

  - 获取项目的哈希码（即索引）

  - 如果索引没有项目，则创建新的链表

  - 如果索引已有链表，检查列表以查看项目是否已在其中。如没有将项目添加到列表
  - Θ(*Q*)(Q：链表最长长度)


+ `contains`

  - 获取项目的哈希码（即索引）

  - 如果索引为空，则返回 `false`

  - 否则，检查列表中该索引处的所有项目，如果该项目存在，则返回`true`
  - Θ(*Q*)

空间节省：碰撞处理完成，就不需要创建非常大的数组，将哈希码取模后存入即可

存在问题：

数组长度为`M`，随着数组中的项目数`N`越爱越多， LinkedList会越来越长，负载系数`L = N/M`越大，平均运行时 Θ(*L*) 越大，因此需要对数组长度动态调整

## 最终的数据结构：Hash Table

最终的数据结构被称为哈希表：

+ 每个输入由哈希函数转换为`hashCode`，然后使用模运算符将它们转换为有效索引，然后添加到该索引中，两种常见的解决碰撞(**哈希冲突**)的方式：

  + 开放地址法(Open addressing):
    开放地址法通过探测序列中的下一个空槽来解决冲突。常见的探测方法有:
    + 线性探测(Linear probing):以步长为1探测下一个槽
    + 平方探测(Quadratic probing):以步长为原来步长的平方探测下一个槽
    + 双哈希法(Double hashing):使用另一个哈希函数计算下一步的探测
  + 链地址法(Separate chaining):
    链地址法为每个哈希槽建立一个单向链表。发生冲突时,将元素添加到链表的尾部。

  + 优缺点：
    + 开放地址法的优点是不需要额外空间，空间利用率高，但是代价是查找、删除元素时需要进行重新哈希，并且容易出现聚集现象
    + 链地址法的优点是查找、删除元素简单，不会出现聚集现象。但是需要额外空间维护链表,并且查找元素的时间复杂度较高

  > Java中的`Hash Map`和`Hash Table`使用链地址法来解决冲突,每个哈希槽中的元素以链表的形式保存

+ 如果负载系数`L = N/M`大于**负载系数阈值**，则增加`M`

运行时分析：

如果有 100 个项目，并且 List 大小为 5，则

- 最好的情况：所有项目都会均匀地分配到不同的索引，有 5 个链接列表，每个链接列表包含 20 个项目
- 最坏的情况：所有项目都会发送到同一个索引，只有 1 个 LinkedList，它包含所有 100 个项目

有两种方法可以尝试解决此问题：

- 动态增长哈希表，**也有助于打乱哈希表中的项目**

  - 假设均匀分布，则所有列表都将大致为`L`，运行时θ(*L*）。`L`只允许低于恒定的**负载系数阈值**，因此Θ(L)=Θ(1)
  - 假设项目分布不均匀，运行时Θ(*N*)，因为可能只有一个链表
  - 调整大小需要Θ(N)时间,在调整大小时，实际上不需要检查项目是否已经存在于` List `中（因为没有重复项）

- 改进我们的哈希码

  - 如果有良好的哈希函数（即为不同项目提供相当随机值的哈希码），项目将均匀分布

  + 一些一般的经验法则：
    + 上述思考中的经验
    + 使用一个小素数的“基数”：使用126作为基数意味着任何以相同的最后 32 个字符（`int`范围`2^32`,而从第`33`位开始就变成`n*63^33*2^33`，无论`n`是多少都对`hashcode`没有影响）结尾的字符串都具有相同的哈希码，发生这种情况是因为溢出，使用质数有助于避免溢出而导致的哈希冲突，为什么是小素数？因为它更容易计算

总的来说，有了好的哈希函数和调整大小，运算运行时被摊销为θ(*1*）

![image-20230427101845667](assets\image-20230427101845667.png)

> 拓展读物：  
> + 一篇很好的文章：[HashMap实现原理及源码分析 - dreamcatcher-cx - 博客园 (cnblogs.com)](https://www.cnblogs.com/chengxiao/p/6059914.html)  
> + 将可变对象存储在HashSet或HashMap中带来的问题：https://blog.csdn.net/hubianxiaohang/article/details/17885631?ydreferer=aHR0cHM6Ly9jbi5iaW5nLmNvbS8%3D  
> + Java Object中的hashCode()实现：https://blog.csdn.net/weixin_45244678/article/details/107256002  
> + Java中在不覆盖hashCode的情况下永远不要覆盖equals：Java 中 HashMap、Hashset 等集合类型的实现都是基于哈希表来进行的。在使用哈希表时，我们需要确保对象的哈希值是稳定的，即相同的对象应该产生相同的哈希值。 在 Java 中，如果两个对象相等（equals() 方法返回 true），它们的哈希值也必须相等。如果我们没有重写一个类的 hashCode() 方法，那么这个类默认会使用 Object 类中的 hashCode() 方法，该方法对于不同的对象会产生不同的哈希值，这就可能导致出现哈希冲突，进而影响到哈希表的性能