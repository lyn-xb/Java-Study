# [CS 61B]12、Hashing

## 一些问题

目前已经学习了一些数据结构，用于有效地搜索数据结构中项目的存在。例如二叉搜索树，2-3树等平衡二叉搜索树。但是，这些结构存在一定限制：

- 它们要求项目具有可比性，对于某些对象，这个问题可能没有意义
- 它们给出的时间复杂度为 Θ(logn)，但是可以做到更好的性能

## 思考1

创建一个类型和大小为 2 亿的 ArrayList。默认情况下，让一切都为`false`

+ `add(int x)`只是将 ArrayList 中`x`的位置设置为 `true`，只需 Θ(1) 时间
+ `contains(int x)`仅返回 ArrayList 中`x`的位置是 `true`还是 `false`，也只需要 Θ(1) 时间

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
  - Θ(*Q*)(Q链表最长长度)


+ `contains`

  - 获取项目的哈希码（即索引）

  - 如果索引为空，则返回 `false`

  - 否则，检查列表中该索引处的所有项目，如果该项目存在，则返回`true`
  - Θ(*Q*)

空间节省：碰撞处理完成，就不需要创建非常大的数组，将哈希码取模后存入即可

存在问题：

数组长度为`M`，随着数组中的项目数`N`越爱越多， LinkedList会越来越长，负载系数`L = N/M`越大，平均运行时 Θ(*L*) 越大，因此需要对数组长度动态调整

## 最终的数据结构：Hash Table