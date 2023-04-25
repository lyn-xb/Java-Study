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

*a*,*b*,*c*,*d*=1,1,1,1 => 1⋅2^3+1⋅2^2+1⋅2^1+1⋅2^0=15
*a*,*b*,*c*,*d*=0,3,1,1 => 0⋅2^3+3⋅2^2+1⋅2^1+1⋅2^0=15

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

要使得这种方式支持所有的字符，可以使用 `ASCII`(支持有限) 或者 `UTF`(支持所有字符)的字符格式，每个字符对于一个整数

## 思考4：

+ 