### 1. GO语言特点
1. 从C语言继承了很多理念，包括表达式语法，控制结构，基础数据类型等等

2. 引入包的概念，用于组织程序结构，Go语言的一个文件都要归属于一个包，而不能单独存在

3. 垃圾回收机制，内存自动回收，不需要开发人员管理

4. 天然并发

   1. 从语言层面支持并发，实现简单

   2. goroutine，轻量级线程，**可实现大并发处理，高效利用内核**

   3. 基于CPS并发模型(Communicating Sequential Process) 实现

5. 管道通信机制，形成Go语言特有的管道channel，通过管道channel可以实现不同的goroute之间的通信

6. 函数返回多个值

7. 新的创新：切片slice、延时执行defer等

#### 1.1 Golang环境变量
1. GOROOT:指定go sdk安装目录
2. Path: 指定sdk/bin 目录
3. GOPATH: Golang工作目录，我们所有项目的源码放到这个目录下

### 2. Go快速入门

```
package main
import "fmt"

func main(){
	fmt.Println("hello Golang")
}
```

#### 2.1 Golang执行流程分析

- 两种运行方式
  1. 源文件 .go—— go build ——> 可执行文件(.exe或可执行文件) —— 运行 ——> 结果
  2. 源文件 .go—— go run ——> 结果
- 两种执行方式的区别：
  1. 如果先编译成可执行文件，该可执行文件可以在没有go开发环境的机器上运行
  2. 如果直接用 go run 运行，如果在另外机器上这么运行，需要go开发环境
  3. 在编译时，编译器会将运行依赖的库文件包含在可执行文件中，可执行文件会变大很多

#### 2.2 Go程序开发注意事项

1.  Go源文件以“go”为扩展名
2. Go应用程序的执行入口是main()函数
3. Go语言严格区分大小写
4. Go方法一条条语句构成，每个语句后不需要分号(Go语言会在每行后自动加分号)
5. Go编译器是一行一行进行编译的，因此我们一行就写一条语句，不能把多条语句写在同一，否则报错
6. Go语言定义的变量或者import的包如果没有使用到，代码不能通过编译
7. 大括号要成对出现，缺一不可

#### 2.3 Go常用转义字符(escape char)

1. \t : 一个制表位，实现对齐的功能
2. \n: 换行符
3. \\\ : 一个\
4. \\" : 一个"
5. \\r: 一个回车

#### 2.4 注释
- 行注释 \\\
- 块注释 \ \* *\

#### 2.5 规范的代码风格
- 正确的使用行注释
    - 推荐使用行注释
    - 看Go源码
- 正确使用缩进
    - gofmt命令格式化
- 行长约定：一行最长不超过80个字符，超过的请使用换行展示。

### 3 Golang-变量
#### 3.1 变量：

- 变量相当于内存中一个数据存储空间的表示
- 该空间有自己的变量名和数据类型
- Golang变量使用的三种方式：
    - 指定变量类型，声明后若不赋值，使用默认值
    - 根据值自行判定变量类型(类型推导)
    - 省略var，注意 := 左侧的变量不应该是已经声明过的，否则导致编译错误
- 多变量声明：golang支持一次性声明多个变量
- 该区域的数值可以在同一类型范围内不断变化
- 变量在同一作用域内不能重名
- 变量=变量名+值+数据类型
- Golang的变量如果没有赋值，编译器会使用默认值

示例：
```
// 定义全局变量
var m1 = 100
var m2 = "test"
var m3 = 1.1
// 一次性声明
var(
	m5 = "aaa"
	m6 = 1212
)

func main() {
	//变量
	// 1. var 声明变量
	var i int 
	fmt.Println("i =",i)
	// 2. 类型推导
	var j = 10
	fmt.Println("j =",j)
	// 3. 使用 :=
	name := "roger"
	fmt.Println("name =",name)
	// 4. 声明多个变量
	//4.1
	var n1, n2, n3 int 
	fmt.Println("n1 =", n1, "n2 =", n2, "n3 =", n3)
    //4.2
	var a1, a2, a3 = "aaa3", 111, "aaa2"
	fmt.Println("a1 =", a1, "a2 =", a2, "a3 =", a3)
	//4.3
	b1, b2, b3 := "bbb3", 111, "bbb2"
	fmt.Println("b1 =", b1, "b2 =", b2, "b3 =", b3)

	fmt.Println("m1 =",m1,"m2 =",m2,"m3 =",m3,"m5 =",m5,"m6 =",m6)

}
```
声明变量：
- var 变量名 数据类型

变量初始化：
- var a int = "test"

程序中➕号的使用
1. 当左右两边都是数值型时，则做加法运算
2. 当左右两边都是字符串，则做字符串拼接

#### 3.2 数据类型
- 基本数据类型
  - 数值型
    - 整数型
    - 浮点类型
      - 单精度 float32 占 4个字节
      - 双精度 float64 占 8个字节
  - 字符型
    - golang没有专用的字符
  - 布尔型
  - 字符串
- 派生/复杂数据类型
  - 指针
  - 数组
  - 结构体
  - 管道
  - 函数
  - 切片
  - 接口
  - map

- 整型的使用细节：
  - Golang各整数类型分：有符号和无符号，int uint的大小跟操作系统有关
  - Golang默认声明的是int型
  - 查看程序中某个变量占用字节大小和数据类型
  - Golang程序中整型变量在使用时，最受保小不保大的原则
- 小数类型/浮点数使用细节
  - Golang浮点类型有固定的范围和字段长度，不受操作系统影响
  - Golang浮点类型默认声明为float64类型
  - 浮点型常量有两种表示形式
  - 通常情况下，应该使用float64，因为它比float32更精确

- 字符类型
  - 字符常量是用单引号括起来的单个字符，比如 var a byte = 'a'
  - Go中允许使用转义字符 \ 来将其后的字符转义变为特殊字符型常量 
  - Go语言的字符使用UTF-8编码
  - 在Go中，字符的本质是一个整数，直接输出时，是字符对应的UTF-8编码的码值
  - 直接给某个变量赋值一个数字，然后按格式化输出时%c，会输出该数字对应的unicode字符
  - 字符类型是可以进行运算的，相当于一个整数，因为它都对应有Unicode码
  - 如果保存的字符在ASCII表，可以直接保存为byte
  - 如果我们保存的字符对应码值大于255，这时我们可以考虑使用int类型保存 
  - 如果我们需要按照字符的方式输出，这时我们需要格式化输出，即 fmt.Printf("d=%c"，c) 

- 布尔类型 bool
  - 布尔类型也叫bool类型，bool类型数据只允许取值true和false 
  - bool类型占1个字节
  - boolean类型适于逻辑运算，一般用于程序流程控制 

- 字符串类型: 
  - Go的字符串是由单个字节连接起来的，Go语言的字符串的字节使用UTF-8编码标识Unicode文本
  - 字符串一旦赋值了，字符串就不能修改了：** 在Go中字符串是不可变的  **
  - 字符串的两种表示形式
    1. 双引号，会识别转义符
    2. 反引号，以字符串的原生形式输出，包括换行和特殊字符，可以实现防止攻击，输出源代码效果 
    3. 字符串的拼接方式 +
    4. 当一个字符串拼接操作特别长，怎么办？可以分行写，但注意，需要将+保留在上一行

- 基本数据类型的默认值
  - 整型：0
  - 浮点数：0
  - 字符串：""
  - 布尔类型：false 

- 基本数据类型的转换
  - Golang和java/c不同，Go在不同类型的变量之间赋值时，需要**显式的转换**。也就是说Golang中数据类型不能自动转换
  - 基本用法：
    - 表达式Type(value)，将值 value 转换为类型 T

- 基本数据类型和string的转换
  - 实现基本数据类型和string类型的相互装换
    - 基本类型转string类型
      1. fmt.Sprintf("%参数"，表达式)
        - 参数需要和表达式的数据类型相匹配
        - fmt.Sprintf()返回转换后的字符串
      2. 使用strconv包的函数
        - strconv.FormatBool
        - strcovn.FormatInt
    - string转基本数据类型
      - 使用strconv包的函数
        - strconv.ParseBool
        - strconv.ParseFloat
        - strconv.ParseInt
        - strconv.ParseUint
  - 注意：string转基本数据类型，要确保string可以转为一个有效数据，如果将“hello”转为数据，Golang将会把值处理为0

- 指针
  1. 基本数据类型，变量存的就是值，也叫值类型
  2. 获取变量的地址，用&，比如： var num int，获取num的地址：&num
  3. 指针类型，变量存的是一个地址，这个地址指向的空间存的才是值，比如：var ptr \*int = &num
  4. 获取指针类型所指向的值，使用：\*, 比如：var ptr \*int，使用\*ptr 获取ptr指向的值
  - 使用细节：
    - 值类型，都有对应的指针类型，形式为 \*数据类型，比如int的对应的指针就是 \*int，float32对应的指针类型就是 \*float32 依次类推
    - 值类型包括: 基本数据类型int系列，float系列，bool，string，数组，结构体struct

#### 3.3 值类型和引用类型
1. 值类型：
  - 基本数据类型int系列，float系列，bool，string，数组，结构体struct
2. 引用类型：
  - 指针，slice切片，map，管道chan，interface等都是引用类型
- 特点：
  1. 值类型：变量直接存储值，内存通常在栈中分配
  2. 引用累心: 变量存储的是一个地址，这个地址对应的空间才是真正存储的数据值，内存通常在堆上分配，当没有任何变量引用这个地址时，该地址对应的数据空间成为一个垃圾，由GC来回收

#### 3.4 标识符的命名规范
- 标识符概念：
  1. Golang 对各种变量、方法、函数等命名时，使用的字符序列称为标识符
- 标识符命名规则：
  1. 由26个英文字母大小写，0-9，\_组成
  2. 不可以以数字开头
  3. Golang中严格区分大小写
  4. 标识符不能包含空格
  5. 下划线 \_ 本身在Go中是一个特殊的标识符，称为**空标识符**。可以代表任何其它的标识符，但是它对应的值会被忽略(比如:忽略某个返回值)，所以仅能被作为占位符使用，不能作为标识符使用，
  6. 不能使用系统**保留关键字**作为标识符

- 注意事项：
  1. 包名：保持package的名字和目录保持一致，尽量采取有意义的包名，简短，有意义，不要和标准库冲突
  2. 变量名、函数名、常量名: 驼峰写法
  3. 如果变量名、函数名、常量名首字母大写，则可以被其他的包访问；如果首字母小写，则只能在本包内使用(注：可以简单的理解为：首字母大写是公开的，首字母小写是私有的 )

- 关键字：

###  4 运算符
#### 4.1 运算符介绍
- 运算符是一种特殊的符号，用以表示数据的运算、赋值和比较等
  1. 算术运算符
  2. 赋值运算符
  3. 比较运算符/关系运算符
  4. 逻辑运算符
  5. 位运算符
  6. 其他运算符

#### 4.2 算术运算符
- 加减乘除、取模，自增、自减、连接字符串

#### 4.3 关系运算符
- 关系运算符的结果都是bool型，要么是true，要么是false
- 关系表达式经常在**if结构**的条件中或循环结构的条件中
  ![image](../images/RelationalOperators.png)
#### 4.4 逻辑运算符
- 用于连接多个条件(一般来讲就是关系表达式)，最终的结果bool值
  ![image](../images/logicalOperators.png)
#### 4.5 赋值运算符
- 赋值运算符就是将某个运算后的值，赋给指定的变量
#### 4.6 位运算符
#### 4.7 其他运算符
![image](../images/otherOperators.png)

### 5 流程控制
1. 顺序控制
2. 分支控制
3. 循环控制
#### 5.1 顺序控制
- 自上而下顺序执行
#### 5.2 分支控制
- 单分支控制
```
if 条件 {
    操作
}
```
- 双分支控制
```
if 条件 {
    操作
} else {
    操作
}
```
- 多分支控制
```
if 条件 {
    操作
} else if {
    操作
} else {
    操作
}
```
- 嵌套分支
```
if 条件 {
    if 条件 {
    	操作
	}
}
```

#### 5.3 switch 分支
- 定义：
  switch语句用于基于不同条件执行不同动作，每一个case分支都是唯一的，从上到下逐一测试，直到匹配到为止，匹配项后面不需要加break

- 基本语法：
```
switch 表达式 {
    case 表达式1，表达式2，...:
        语句块1
    case 表达式3，表达式4，...:
        语句块2
    
    default:
        语句块
}
```

- 注意细节：
  1. case 后是一个表达式(即：常量值，变量，一个有返回值的函数等都可以)

  2. case 后的各个表达式的值的数据类型，必须和switch的表达式数据类型一致

  3. case 后面可以带多个表达式，使用逗号间隔，比如：case 表达式1，表达式2

  4. case 后面的表达式如果是常量值(字面量)，则要求不能重复

  5. case 后面不需要带break，程序匹配到一个case后，执行对应的代码块，然后退出switch，如果没有匹配到case，则执行default

  6. default 语句不是必须的

  7. switch 后可以不带表达式，类似 if---else 分支来使用

  8. switch 后也可以直接声明/定义一个变量，分号结束，不推荐

  9. switch穿透 fallthrough， 如果在case语句块后增加fallthrough，则会继续执行下一个case

  10. Type Switch：switch语句还可以被用于 tpye-switch 来判断某个 interface 变量中实际指向的变量类型

#### 5.4 for循环控制
- 示例
```
	for i:=0; i<10; i++ {
		fmt.Println("i=",i)
	}
```
- 基本语法
  for 循环变量初始化;循环条件;循环变量迭代{
    循环操作
  }

```
用法：
// 第一种写法
// 
	i := 1
	for ; i<3; i++ {
		fmt.Println("aaa",i)

	}
	fmt.Println("i=",i)

	for j:=1; j<3; j++ {
		fmt.Println("jjj",j)

	}


// 第二种写法
	n := 1
	for n <= 3 {
		fmt.Println("n=",n)
		n++
	}


// 第三种写法。死循环，一般配合break使用（go没有while（do..while）的实现，可以实现类似while或 do...while用法），等价于 for ; ; {...}
	 for {
	 	fmt.Println("aaa")
	 }

	k := 1
	for ; ; {
		if k < 3 {
			fmt.Println("hahaha")
		} else {
			break
		}
		k ++
	}
	
// 第四种： Go提供for-range的方式，可以方便遍历字符串和数组
// 字符串遍历 传统方式
	var sss string = "hello world"
	for i := 0; i < len(sss); i++ {
		fmt.Printf("%c \n",sss[i])
	}
	//字符串遍历 for-range
	var ss string = "123 456"
	for index, val := range ss {
		fmt.Printf("index=%d, val=%c \n", index, val)
	}
注意： 如果我们的字符串含有中文，那么传统的遍历字符串方式是错误的，会出现乱码。原因是传统的对字符的遍历是按照字节来遍历，而一个汉字在utf8编码对应的是3个字节
如何解决 需要将 str 转换成 []rune 切片
str2 := []rune(str)
```

**注：go没有while（do..while）的实现**

#### 5.5 跳转控制 break

- 基本介绍

  break语句用于终止某语句块的执行，用于终端当前的for循环或者跳出switch语句

- 基本语法

  ```
  {
   ...
   break
   ...
  }
  
  	i := 1
      for ;; {
  		// 种子
  		rand.Seed(time.Now().UnixNano())
  		//生成随机数
  		n := rand.Intn(100)+1
  		i ++
  		if n == 99 {
  			break
  		}
  	}
  	fmt.Println(i)
  ```

- 注意细节

  1. break出现在多层嵌套的语句块时，可以通过标签指明要终止的是哪一层的语句块
  2. 标签的基本使用

  ```
  lable1:
  {
      lable2:
      {
          break lable1
          //break lable2
      }
  }
  
  lable1:
  	for i:=0; i<100; i++ {
  		lable2:
  		for j:=0; j<10; j++ {
  			for n:=0; n<5; n++ {
  				if i == 80 {
  					break lable1
  				} 
  				if j == 8 {
  					break lable2
  				} else {
  					fmt.Printf("i= %d, j= %d n= %d\n", i, j, n)
  				}
  			}
  		}
  	}
  ```


- 例子

  ```
  	// 用户登入3次
  	var username string 
  	var pwd string
  	
  	for i:=2; i>=0; i-- {
  		fmt.Println("请输入username")
  		fmt.Scanln(&username)
  		fmt.Println("请输入pws")
  		fmt.Scanf("%s",&pwd)
  
  		if username == "roger" && pwd == "123" {
  			fmt.Println("pass")
  			break
  		}
  
  		fmt.Println(username, pwd)
  		fmt.Printf("剩余输入次数: %d \n", i)
  	}
  ```



#### 5.6 跳转控制语句 continue

- 基本介绍

  continue 语句用于结束本次循环，继续执行下一循环

  continue 语句出现在多层嵌套的循环语句体中时，可以通过标签指明要跳过的是哪一层循环，跟前面的break标签的使用规则一样

- 基本语法

  ```
  {
  	...
      continue
      ...
  }
  当执行到continue时，就不再执行下面的代码，而是直接到循环迭代，进行下一次循环。
  ```

- 例子：

  ```
  for i:=0; i<=10; i++ {
  		if i == 3 {
  			continue
  		}
  		fmt.Printf("i = %d \n", i)
  	}
  结果不会输出 i = 3
  
  ## 输出基数
  for j:=1; j<=100; j++ {
  	if j%2 == 0{
  		continue
  	}
  	fmt.Println(j)
  }
  
  # 统计正数 负数
  var positive_count int
  	var negative_count int
  	var num int
  	for {
  		fmt.Println("请输入一个整数：")
  		fmt.Scanln(&num)
  
  		if num == 0 {
  			break
  		}
  
  		if num > 0 {
  			positive_count++
  			continue
  		}
  
  		negative_count++
  	}
  
  	fmt.Printf("positive_count: %d \t negative_count: %d \n",positive_count, negative_count)
  ```



#### 5.7 跳转控制语句 goto

- 基本介绍

  1. Go 语言的 goto 语句可以无条件的转移到程序中指定的行
  2. goto 语句通常与条件语句配合使用。可用来实现条件转移，跳出循环体等功能
  3. 在 go 程序设计中**一般不主张使用 goto 语句**，以免造成程序流程等混乱，使理解和调试程序产生困难

- 基本用法：

  ```
  goto label
  ...
  label: statement
  ```

- 例子

  ```
  var num int
  fmt.Println("pls input a number：")
  fmt.Scanln(&num)
  
  if num > 10 {
  	goto label_big
  }
  fmt.Println("小")
  fmt.Println("中")
  label_big:
  fmt.Println("大")
  ```

#### 5.8 跳转控制语句 return

- 基本介绍：

  return使用在方法或者函数中，表示跳出所在的方法或函数

- 说明：

  1. 如果 return 是在普通的函数，则表示跳出该函数，即不再执行函数中return后面代码，终止函数
  2. 如果 return 是在 main 函数，表示终止 main 函数，也就是说终止程序



### 6 函数

#### 6.1 函数定义

- 为完成某一功能的程序指令(语句)的集合，称为函数。
- 在go中，函数分为： 自定义函数、系统函数

#### 6.2 函数语法

```
fun 函数名 (形参列表) (返回值列表) {
	执行语句
	return 返回值列表
}
形参列表： 表示函数的输入
执行语句： 表示实现某一功能的代码块
函数可以有返回值，也可以无返回值
```

- 例子

  ```
  func add (a int, b int) int {
  	c := a + b 
  	return c 
  }
  
  func main() {
  	i := add(1,3)
  	fmt.Println(i)
  }
  ```

#### 6.3 包的介绍

- 包可以实现 引用其他文件中的函数
- 包可以实现 不同的包中定义相同名称的函数
- 包的本质就是创建不同的文件夹来保存程序文件



- 包的基本概念

  - go的每个文件都属于一个包，也就是说go以包的形式来管理文件和项目目录结构的

- 包的作用

  - 区分相同名字的函数、变量标识符
  - 当程序文件很多时，可以很好的管理项目目录
  - 控制函数、变量等访问范围，即作用域

- 包的相关说明

  - 打包的基本语法

    ```
    package “包名”
    ```

  - 引入包的基本语法

    ```
    import "包的路径"
    ```

#### 6.4 包的使用

- 目录 GOPATH/src 为根目录下的项目目录

![image-20181228192336315](./images/bao_1.png)

- 源码 注意包的引用

  ```
  ### 文件 main.go
  package main
  import (
  	"fmt"
  	//包的引用
  	"go_code/6_function/utils" 
  )
  
  func main() {
  	n1 := 1.2
  	n2 := 3.1
  	i := utils.Add(n1,n2)
  	fmt.Printf("result= %.2f", i)
  }
  
  
  ### 文件 utils.go
  package utils
  
  //如果允许该函数在其他包中引用，函数名首字母要大写，类似java定义的public
  func Add (a float64, b float64) float64 {
  	c := a + b 
  	return c 
  }
  
  ```



#### 6.5 包的使用细节

- 在给一个文件打包时，该包对应一个文件夹，比如这里的 utils 文件夹对应的包名就是utils，文件包名**通常**和文件所在的文件夹名一致，一般为小写字母

- 当一个文件要使用其他包函数和变量时，需要先引入对应的包

  - 引入方式 1：

    ```
    import "包名"
    ```

  - 引入方式 2：

    ```
    import (
       "包名"
       "包名"
    )
    ```

- package 指令在文件第一行，然后是import命令

- 在import包时，路径从$GOPATH的src下开始，不用带src，编译器会自动从src下开始引入

- 为了让其他包的文件，可以访问到本包的函数，则该函数名的首字母需要大写，类似于其他语言的public，这样才能跨包访问

  ```
  ### 文件 utils.go
  package utils
  
  //如果允许该函数在其他包中引用，函数名首字母要大写，类似java定义的public
  func Add (a float64, b float64) float64 {
  	c := a + b 
  	return c 
  }
  ```

- 在访问其他包的函数时，其语法是：**包名.函数名**

- 如果包名较长，Go 支持给包取别名，**注意：**取别名后，原来的别名就不能用了

- 在同一个包下，不能有相同的函数名、全局变量，否则报重复定义

- 如果你要编译一个可执行程序文件，就需要将这个包声明为 main，即 package main 这是一个语法规范，如果写的是一个库，包名可以自定义



#### 6.6 函数-调用机制

- 示例

  ```
  package main
  import (
  	"fmt"
  )
  
  
  func AddInt (n int) {
  	n = n + 1
  	fmt.Printf("AddInt() n = %d \n", n)
  }
  
  func main() {
  	a := 3
  	AddInt(a)
  	fmt.Printf("main() a = %d \n", a )
  }
  ```

- 下面展示的都是 逻辑分区

  - 在调用一个函数时，会给该函数分配一个新空间，编译器会通过自身处理，让这个新空间和其他栈空间区分开
  - 在每个函数对应的栈中，数据空间时隔离的，不会混淆
  - 当一个函数调用执行完后，程序会销毁这个函数对应的栈空间

![function](../images/function_go.png)

- return 语句

  - 基本用法：

    - go 语言支持返回多个值，这一点其他语言没有的

      ```
      func 函数名 (形式参数列表) (返回值类型列表) {
      	语句...
      	return 返回值列表
      }
      
      // return 返回多个值
      func getSumSub (n1 int, n2 int) (int, int) {
      	sum := n1 + n2
      	sub := n1 - n2
      	return sum, sub
      }
      
      func main() {
          //返回两个值
      	sum, sub := getSumSub(10,12)
      	//忽略一个值
      	_, num := getSumSub(20,12)
      	fmt.Printf("sum=%d \nsub=%d \n", sum, sub)
      }
      
      ```

    - 如果返回多个值时，在接收时，希望忽略某个返回值，则使用 _ 符号表示占位忽略

    - 如果返回值，只有一个，（返回值类型列表）可以不写括号()

#### 6.7 函数-递归调用

- 基本介绍：

  - 一个函数在函数体内又调用了它本身

    ```
    // 递归调用
    func recursion (n int) int {
    	if n == 1 {
    		return 1
    	}
    	return n * recursion(n-1)
    }
    func main() {
    	fmt.Println(recursion(4))
    }
    ```

- 函数递归需要遵守的重要准则

  - 执行一个函数时，就要创建一个新的受保护的独立空间（新函数栈）
  - 函数局部变量是独立的，不会相互影响
  - 递归必须向退出递归的条件逼近，否则就是无限递归，死循环
  - 当一个函数执行完毕，或者return，就会返回，遵守谁调用，就将结果返回给谁

- 示例：

  ```
  //斐波那契数
  func feibo (n int) int {
  	if n == 1 || n == 2 {
  		return 1
  	} else {
  		return feibo(n-1)+feibo(n-2)
  	}
  }
  
  //test
  func dg (n int) int {
  	if n == 1 {
  		return 3
  	} else {
  		return 2*dg(n-1)+1
  	}
  }
  
  /猴子吃桃
  // 有一堆桃子，猴子第一天吃掉一半，再多吃一个，第二天吃掉剩下的一半，再多吃一个，依次类推，
  // 第十天的时候，还没有吃，发现还有1个桃子。求起初有多少桃子？
  // 分析：
  // 1. 第十天 有 1个
  // 2. 第九天 有 (f(10)+1)*2
  // 逻辑公式： f(n) = (f(n+1)+1)*2
  
  func peach (n int) int {
  	if n == 10 {
  		return 1
  	} else {
  		return 2*(peach(n+1)+1)
  	}
  }
  ```

#### 6.8 函数注意事项和细节讨论

1. 函数的形参列表可以是多个，返回值列表也可以是多个

2. 形参列表和返回值列表的数据类型可以是值类型和引用类型

3. 函数命名规范遵循标识符命名规范，首字母不能是数字，**首字母大写该函数可以被本包文件和其他包文件使用，类似public，首字母小写只能被本包文件使用，其他包不能使用，类似于private**

4. 函数中的变量是局部的，函数外不生效

5. 基本数据类型和数组默认都是值传递，即进行值拷贝。在函数内修改不影响到原来的值

   ```
   func test (n1 float64) {
   	n1 = n1 + 1
   	fmt.Println("test() n1 = ", n1)
   }
   func main() {
   	n1 := 1.2
   	test(n1)
   	fmt.Println("main() n1 = ", n1)
   }
   ----
   test() n1 =  2.2
   main() n1 =  1.2
   ```

6. 如果函数内到变量需要修改函数外的变量，可以传入变量的地址&，函数内以指针的方式操作变量，从效果上看类似引用，

   ```
   func test (n1 *float64) {
   	*n1 = *n1 + 1
   	fmt.Println("test() n1 = ", *n1)
   }
   
   func main() {
   	n1 := 1.2
   	test(&n1)
   	fmt.Println("main() n1 = ", n1)
   }
   ----
   test() n1 =  2.2
   main() n1 =  2.2
   ```

7. golang不支持函数重载

8. 在go中，函数也是一种数据类型，可以赋值给一个变量，则该变量就是一个函数类型的变量了。通过该变量可以对函数进行调用。

   ```
   // 函数可以是一种数据类型，赋值给变量使用
   func getSum(n1 int, n2 int) int {
   	return n1+n2
   }
   
   func main() {
   	// 函数赋值
   	aa := getSum
   	fmt.Printf("a 的类型 %T; getSum的类型 %T\n", aa, getSum)
   	res := aa(10, 20) //等价于 res := getSum(10, 20)
   	fmt.Println("getSum:",res)
   }
   -----
   a 的类型 func(int, int) int; getSum的类型 func(int, int) int
   getSum: 30
   ```

9. 函数既然是一种数据类型，因此在go中函数可以作为形参传入，并调用

   ```
   // 函数可以是一种数据类型，赋值给变量使用
   func getSum(n1 int, n2 int) int {
   	return n1+n2
   }
   
   // 函数作为形式参数，并调用
   func argsFun( funvar func(int, int) int, n1 int, n2 int) int {
   	return funvar(n1, n2)
   }
   func main() {
   	// 函数作为形参传入，并掉调用
   	res2 :=argsFun(getSum, 1, 2)
   	fmt.Println("函数作为形参传入，argsFun 结果 res2 = ", res2 )
   }
   -----
   函数作为形参传入，argsFun 结果 res2 =  3
   ```

10. 为了简化数据类型定义，go 支持自定义数据类型

    ```
    基本语法：
    type 自定义数据类型名 数据类型
    // 可以理解为： 相当于一个别名
    示例：
    type myInt int // 这里的myInt就等价于 int 来使用
    type funvar func(int, int) int // 这里的 funvar 类型就等价于 func(int, int) int 函数类型
    
    1.
    func main () {
    	// 自定义数据类型
    	// 给int取别名，在go中 myInt 和 int 虽然都是int类型，但是go认为 myInt 和 int 是两个类型
    	type myInt int
    
    	var inum1 myInt
    	var inum2 int 
    	inum1 = 10
    	//不能直接 inum2 = inum1, 需要显示转换
    	inum2 = int(inum2)
    	fmt.Println("inum1=", inum1, "inum2=", inum2)
    }
    -----
    inum1= 10 inum2= 0
    
    2.
    type funcvars func(int, int) int
    func argsFun2(funcvar funcvars, n1 int, n2 int) int {
    	return funcvar(n1, n2)
    }
    func main () {
    	res3 :=argsFun2(getSum, 1, 20)
    	fmt.Println("函数作为形参传入，argsFun2 结果 res3 = ", res3 )	
    }
    ----
    函数作为形参传入，argsFun2 结果 res3 =  21
    ```

11. 支持对函数返回值命名，按顺序返回

    ```
    // 对函数返回值命名
    func nameFuncSumAndSub (n1 int, n2 int) (sum int, sub int) {
    	sum = n1 + n2
    	sub = n1 - n2
    	return
    }
    func main () {
    	// 命名函数返回值
    	na, nb:= nameFuncSumAndSub(5, 3)
    	fmt.Printf("na = %v nb = %v", na, nb)
    }
    ------
    na = 8 nb = 2
    ```

12. 使用 _标识符，忽略返回值

    ```
    func nameFuncSumAndSub (n1 int, n2 int) (sum int, sub int) {
    	sum = n1 + n2
    	sub = n1 - n2
    	return
    }
    func main () {
    	na, _:= nameFuncSumAndSub(5, 3)
    	fmt.Printf("na = %v\n", na)
    }
    ------
    na = 8
    ```

13. Go 支持可变参数

    - 语法

      ```
      // 支持0到多个参数
      func sum(args... int) sum int {
      }
      // 支持1到多个参数
      func sum(n1 int, args... int) sum int {
      }
      ```

    - 说明：

      - args是slice切片，通过args[index] 可以访问到各个值
      - 如果一个函数的形参列表中有可变参数，则可变参数需要放到形参列表最后

    - 示例

      ```
      // 可变参数的使用
       func sumArgs (n1 int, args... int) int {
      	 sum := n1 
      	 //遍历 args
      	 for i:=0; i<len(args); i++ {
      		 sum += args[i]
      	 }
      	 return sum
       }
       func main () {
       	//可变参数
      	saa := sumArgs(1,2,3)
      	fmt.Println("args sum = ", saa)
      }
      
      
      ```

    ```
    注意：Go语言中所有的传参都是值传递（传值），都是一个副本，一个拷贝。因为拷贝的内容有时候是非引用类型（int、string、struct等这些），这样就在函数中就无法修改原内容数据；有的是引用类型（指针、map、slice、chan等这些），这样就可以修改原内容数据。
    
    是否可以修改原内容数据，和传值、传引用没有必然的关系。在C++中，传引用肯定是可以修改原内容数据的，在Go语言里，虽然只有传值，但是我们也可以修改原内容数据，因为参数是引用类型。
    
    这里也要记住，引用类型和传引用是两个概念。
    再记住，Go里只有传值（值传递，看传递的是 基本数据类型，还是引用数据类型
    
    函数练习，交换两个值
    func swap(n1, n2 *int) {
    	tmp := *n1
    	*n1 = *n2
    	*n2 = tmp
    }
    
    func main() {
    	aa := 10
    	bb := 15
    	fmt.Printf("aa=%v, bb=%v\n", aa, bb)
    	swap(&aa,&bb)
    	fmt.Printf("aa=%v, bb=%v\n", aa, bb)
    }
    ```
#### 6.9 init函数

- 每一个源文件都可以包含一个init函数，该函数会在main函数执行之前，被go运行框架调用，也就是说init函数会在main函数之前被调用。

  ```
  // init 函数，通常在init函数中完成初始化工作
  func init () {
  	fmt.Println("init() ...")
  }
  func main() {
  	fmt.Println("main() ...")
  }
  ```

- init 函数的注意事项和细节

  - 如果一个文件同时包含全局变量定义、init函数、main函数，则执行的流程是：变量定义--> init函数 --> main 函数

    ```
    // 验证执行流程
    var global = testGloVars()
    
    func testGloVars() string {
    	fmt.Println("testGloVars()...")
    	return "global vars"
    }
    
    // init 函数，通常在init函数中完成初始化工作
    func init () {
    	fmt.Println("init() ...")
    }
    
    func main() {
    	fmt.Println("main() ...", global)
    }
    ----
    testGloVars()...
    init() ...
    main() ... global vars
    ```

    

  - init函数最主要的作用，就是完成一些初始化工作

  - 如果main.go和utils.go都含有 变量定义、init函数时，执行流程：

    ![image-20190215181937782](../images/go_6_9_init.png)

​    



 




# note

```
linux
GOOS=linux GOARCH=amd64 go build -ldflags "-w -s" -o web
windows
GOOS=windows GOARCH=amd64 go build -ldflags "-w -s" -o web
mac
GOOS=darwin GOARCH=amd64 go build -ldflags "-w -s" -o web
```






