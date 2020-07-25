# 一、数据类型

## 基本数据类型

* 整数型
  * byte，内存占用一个字节，取值范围-128~127
  * short，内存占用两个字节，取值范围-32768~32767
  * int（默认），内存占用四个字节，取值范围$-2^{31}$ ~ $2^{31}-1$
  * long，内存占用八字节，取值范围$-2^{63}$ ~ $2^{63}-1$
* 浮点型
  * float（单精度浮点数），内存占用四字节
  * double默认（双精度浮点数），内存占用八字节
* 字符型
  * char，内存占用两个字节
* 布尔型
  * boolean，内存占用一个字节

## 引用数据类型

* 字符串、数组、类、接口、Lambda
* 所有引用类型变量，都可以赋值为一个null值。但是代表其中什么都没有。

## 注意事项

* 字符串不是基本类型，而是引用类型。

* 浮点型可能只是一个近似值，并非精确的值

* 数据范围与字节数不一定相关，例如float数据范围比long更加广泛，但是float是四字节，long是八字节

* 浮点数当中默认类型是double。如果一定要使用float类型，需要加上一个后缀F。

  如果是整数，默认为int类型，如果一定要使用long类型，需要加上一个后缀L。推荐使用大写字母后缀

## 数据类型转换

当数据类型不一样时，会发生数据类型转换

### 自动类型转换（隐式）

* 特点：代码不需要进行特殊处理，自动完成
* 规则：数据范围从小到大

* 例子

```
// 左边是long类型，右边是默认的int类型，左右不一样
// 一个等号代表赋值，将右侧的int常量，交给左侧的long变量进行存储
// 此处发生了自动类型转换
long num = 100;
System.out.println(num); // 100

double num1 = 2.5F;
System.out.println(num2); // 2.5

float num2 = 30L;
System.out.println(num2) // 30.0

float num3 = 30.0D; // 报错，此处需要使用强制类型转换
```

### 强制类型转换（显式）

* 特点：代码需要进行特殊的格式处理，不能自动完成

* 格式：范围小的类型 范围小的变量名 = （范围小的类型）原本范围大的数据

  ```java
  int num = (int) 100L;
  ```

* 注意事项

  * 强制类型转换一般不推荐使用，因为有可能发生精度损失、数据溢出

    ```java
    int num2 = (int) 6000000000L;
    System.out.println(num2); // 1705032704
    
    int num3 = (int) 3.99;
    System.out.println(num3); // 3
    ```

  * byte/short/char这三种类型都可以发生数学运算，例如加法“+”

    ```java
    char zifu = 'A';
    System.out.println(zifu + 1); // 66
    // 大写字母A被当作65进行处理，计算机的底层会用一个数字（二进制）来代表字符A，就是65
    // 一旦char类型进行了数学运算，那么字符就会按照一定的规则翻译成一个数字
    ```

  * byte/short/char这三种类型在运算的时候，都会被首先提升为int类型，然后再计算

    ```java
    byte num1 = 40;
    byte num2 = 50;
    // byte + byte => int + int => int
    int result1 = num1 + num2;
    
    byte num1 = 40;
    short num2 = 50;
    // byte + short => int + int => int
    // int强制转换为short：注意必须保证逻辑上真实大小本来就没有超过short范围，否则会发生数据溢出
    short result1 = (short) (num1 + num2);
    ```

  * boolean类型不能发生数据类型转换

### 注意事项

* 对于byte/short/chat三种类型来说，如果右侧赋值的数值没有超过范围，那么`javac`编译器将会自动隐含地为我们补上一个（byte）（short）（char）。如果右侧超过了左侧范围，那么直接编译器报错。

* 在给变量进行赋值的时候，如果右侧的表达式当中全都是常量，没有任何变量，那么编译器javac将会直接将若干个常量表达式计算得到结果

  ```java
  short result = 5 + 8; // 等号右边全都是常量，没有任何变量参与运算
  ```

  编译之后，得到的.class字节码文件当中相当于【直接就是】：

  ```java
  short result = 13;
  ```

  这称为“编译器的常量优化”

  但是注意：一旦表达式当中有变量参与，那么就不能进行这种优化了。

# 二、变量定义的注意事项

* 如果创建多个变量，那么变量之间的名称不可以重复

* 对于float和long类型来说，字母后缀F和L不要丢掉

* 如果使用byte或者short类型的变量，那么右侧的数据值不能超过左侧类型的范围。

* 没有进行赋值的变量，不能直接使用；一定要赋值之后，才能使用。

* 可以通过一个语句来创建多个变量，但是一般情况下不推荐这么写。

  ```java
  int a, b, c
  ```

# 三、方法

## 注意事项

* 方法定义的先后顺序无所谓
* 方法的定义不能产生嵌套包含关系
* 如果方法有返回值，那么必须写上“ return 返回值；”，不能没有
* return后面的返回值数据，必须和方法的返回值类型对应起来。

## 方法重载

多个方法的名称一样，但是参数列表不一样

```java
System.out.println(sum(1, 2));
System.out.println(sum(1, 2, 3));

public static int sum (int a, int b) {
  System.out.println("有两个参数的方法执行");
  return a + b;
}

public static int sum (int a, int b, int c) {
  System.out.println("有三个参数的方法执行");
  return a + b + c;
}
```

方法重载和下列因素相关：

* 参数个数不同
* 参数类型不同
* 参数的多类型顺序不同

与下列因素无关：

* 参数名称
* 与方法的返回值类型无关

# 四、数组

是一种容器，可以同时存放多个数据值

## 特点

* 数组是一种引用数据类型
* 数组当中的多个数据，类型必须统一
* 数组的长度在程序运行期间不可改变

## 动态初始化数组

格式：数据类型 [] 数组名称 = new 数据类型[数组长度]

```java
int[] arrayA = new int[300]; // 创建一个数组，里面可以存放300个int数据
double[] arrayB = new double[10]; // 创建一个数组，里面可以存放10个double类型的数据
String[] arrayC = new String[5]; // 创建一个数组，能存放5个字符串

int [] arrayD;
arrayD = new int[10];
```

## 静态初始化数组

```java
// 标准格式shujul
数据类型[] 数组名称 = new 数据类型[] { 元素1, 元素2, ...}
int[] arrayA = new int[] { 5, 15, 25, 40};
String[] arrayB = new String[] { "Hello", "World", "java" };

int [] arrayC;
arrayC = new int[] { 11, 21, 31};

// 省略格式
数据类型[] 数组名称 = {元素1, 元素2, ...}
```

## 访问数组元素

* 直接打印数组名称，得到的是数组对应的**内存地址哈希值**。
* 访问数组元素的格式：数组名称[索引值]。【注意】：索引从0开始，一直到“数组长度-1”为止。
* 使用动态初始化数组的时候，其中的元素将会自动拥有一个默认值。（当然，静态初始化数组也会有默认值，只不过被覆盖了而已）
  * 整数类型，默认值为0
  * 浮点类型，默认为0.0
  * 字符类型，默认为`\u0000`
  * 布尔类型，默认为false
  * 引用类型，默认为null

# 五、内存划分

* 栈（Stack）：存放的都是方法中的局部变量。方法的运行一定要在栈当中运行

  * 局部变量：方法的参数，或者是方法{}内部的变量
  * 作用域：一旦超出作用域，立刻从栈内存当中消失

* 堆（Heap）：凡是new出来的东西，都在堆当中

  堆内存里面的东西都有一个地址值：16进制

  堆内存里面的数据都有默认值。规则：

  * 整数类型，默认值为0
  * 浮点类型，默认为0.0
  * 字符类型，默认为`\u0000`
  * 布尔类型，默认为false
  * 引用类型，默认为null

* 方法区（Method Area）：存储.class相关信息，包含方法的信息
* 本地方法栈（Native Methods Stack）：与操作系统相关
* 寄存器（`pc Register`）：与CPU相关

# 六、对象

## 创建

通常一个类并不能直接使用，需要根据类创建一个对象才能使用

* 导包，也就是指出需要使用的类在什么位置

  格式：import 包名称.类名称

  ```java
  // cn/itcast/day/demo1/Student.java
  public class Student {
    String name;
    int age;
    public static void say () {
      Systen.out.println("hello, world");
    }
  }
  ```

  ```java
  import cn.itcast.day.demo1.Student
  ```

  对于和当前类属于同一包的情况，可以省略导包语句不写

* 创建格式：

  类名称 对象名 = new 类名称();

  ```java
  Student stu = new Student();
  ```

* 使用

  使用成员变量：对象名.成员变量名（如果成员变量没有进行赋值，那么将会有一个默认值，规则和数组一样）

  使用成员方法：对象名.成员方法名（参数）

  ```java
  stu.say();
  System.out.println(stu.name) // null
   System.out.println(stu.name) // 0
  ```

  