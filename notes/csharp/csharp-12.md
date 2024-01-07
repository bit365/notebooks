# C# 12 中的新增功能

## 主构造函数

C# 12 中的一个新功能是主构造函数。主构造函数是一个类的构造函数，它在类的声明中声明，而不是在类的主体中声明。主构造函数的参数可以用于初始化类的属性。

### 记录类型

```csharp
public record Address(string FirstName, string LastName);
```

### 类类型


```csharp
public class Person(string firstName, string lastName)
{
    public override string ToString()
    {
        return $"{firstName},{lastName}";
    }
}
```

```csharp
public Person(string firstName) : this(firstName, "hello")
{

}
```
## 集合表达式

C# 12 中的另一个新功能是集合表达式。集合表达式是一种新的语法，用于初始化集合。集合表达式使用大括号，其中包含一个或多个元素初始化器。每个元素初始化器都是一个表达式，它可以是一个值，也可以是一个键值对。

```csharp
// Create an array:
int[] a = [1, 2, 3, 4, 5, 6, 7, 8];

// Create a list:
List<string> b = ["one", "two", "three"];

// Create a span
Span<char> c  = ['a', 'b', 'c', 'd', 'e', 'f', 'h', 'i'];

// Create a jagged 2D array:
int[][] twoD = [[1, 2, 3], [4, 5, 6], [7, 8, 9]];

// Create a jagged 2D array from variables:
int[] row0 = [1, 2, 3];
int[] row1 = [4, 5, 6];
int[] row2 = [7, 8, 9];
int[][] twoDFromVariables = [row0, row1, row2];
```

## 数组索引范围

数组的 0 索引与 sequence[0] 相同。 ^0 索引与 sequence[sequence.Length] 相同。 表达式 sequence[^0] 会引发异常，就像 sequence[sequence.Length] 一样。 对于任何数字 n，索引 ^n 与 sequence.Length - n 相同。

```csharp
string[] strs = ["a", "b", "c", "d", "e","f"];

string[] strs1= strs[0..3];

string[] strs2 = strs[2..^2];

string[] strs3 = strs[^3..^1];
```


## 默认 Lambda 参数

```csharp
var IncrementBy = (int source, int increment = 1) => source + increment;
```

## 展开运算符

```csharp
int[] row0 = [1, 2, 3];
int[] row1 = [4, 5, 6];
int[] row2 = [7, 8, 9];
int[] single = [.. row0, .. row1, .. row2];
```

## 类型别名

### 类型别名

```csharp
using MyPerson = ConsoleApp1.Person;
```
    
```csharp
using MyType = (string FirstName,int LastName);
```