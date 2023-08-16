# 一、名称

规则：开头是一个字母或者下划线，后面可以跟任意数量的字符、数字、下划线，并且区分大小写。


# 二、声明
有四个主要声明：变量(var)、常量(const)、类型(type)、函数(func)。


# 三、变量

var name type = expression

```
var b,f,s = true, 2.3, "four" // bool float64,string

var f, err = os.Open(name) // os.Open() 返回的一个文件和一个错误
```

## 1.短变量声明
name := expresion, name的类型由expression决定

```
i := 100 // := 表示声明，而不是赋值，=表示赋值
```

注：
（1）如果一些变量在同一个词法块中声明，那么对于那些变量，短声明的行为等同于赋值。
（2）短变量声明最少声明一个新变量，否则，代码编译无法通过。

## 2.指针
指针的值是一个变量的地址。一个指针指示值所保存的位置，但不是所有的值都有地址。

```
x := 1
p := &x // p是整型指针，指向x
fmt.PrintLn(*p) // "1"
*p = 2
fmt.PrintLn(x) // "2"

```
注：
（1）两个指针当且仅当指向同一个变量或者两者都是nil的情况下才相等。

## 3. new函数

new(T)创建一个未命名的T类型变量，初始化为T类型的零值，并返回其地址(地址类型为*T)

```
p := new(int) // *int 类型的p,指向未命名的int变量
fmt.PrintLn(*p) // 输出“0”
*p = 2 // 把未命名的int设置成2
fmt.Println(*p) // 输出“2”
```

# 四、赋值

# 五、类型声明

# 六、包和文件

# 七、作用域

# 八、语法
## 1. for循环遍历

用法一：for 赋值表达式; 判断条件; 赋值同时控制变量增减 { }

```
for i:=0; i<10; i++ { 
    // 循环10次
}
```

用法二：for 条件 { }

```
i := 0
for i<10 {
    // 只要条件满足就循环，尝尝在这里修改循环条件来控制循环
}
```
用法三：for {}

```
for {
    // 死循环。需要配合if 判断，当达到条件 break 退出循环
}
```
用法四：For-each range 循环
这种循环可以对字符串、数组、切片、字典等进行迭代，获取元素。有不同应用形式：
- 只获取索引（字典就是键）
```
var a [3]int  
for i := range a {
    fmt.Printf("%d %d\n", i, a[i])    
}

ages := map[string]int{
    "alice":   31,
    "charlie": 34,
}
for name := range ages {
    fmt.Printf("%s\t%d\n", name, ages[name])
}
```
- 获取索引及对应元素（字典就是键和值）

```
var a [3]int  

for i, v := range a {
    fmt.Printf("%d %d\n", i, v)
}


ages := map[string]int{
    "alice":   31,
    "charlie": 34,
}
for name, age := range ages {
    fmt.Printf("%s\t%d\n", name, age)
}
// 如果只想取值，不需要索引，可以用"_"下划线占位索引
for _, age := range ages {
    fmt.Printf("%d\n", age)
}
```

## 2. 循环控制语句
1.首先，当然要第一个说一下break，和其他语言一样，直接结束循环。

```
func main() {
	for i:=0; i<10;i++{
		if i == 5{
			break
		}
		fmt.Println(i)
	}
}
```
如何是循环嵌套，结束距离break最近的循环。当然如果想结束外层循环，可以通过指定标签来实现：

```
func main() {
	label1:
	for i:=0; i<10;i++{
		//label2:
		for j:=0; j<10;j++{
			if j == 5{
				//默认跳出最近的for循环,等同于 break label2
				//break
				//跳出外层循环也就是label1下的for循环
				break label1
			}
			fmt.Println(j)
		}
		fmt.Println(i)
	}
}
```

2. continue 语句，结束本次循环，继续执行当前循环下的下一次循环。 也就是执行到continue时，不在执行下面语句，直接返回循环条件处继续执行。
多层嵌套和break用法相同，可以通过标签指明要跳过的是哪一层循环。

```
func main() {
	for i:=0; i<10;i++{
		for j:=0; j<10;j++{
			if j == 5{
				//不在执行输出j=5，直接跳到执行j++,然后判断j是否小于10，然后继续执行循环体
				continue
			}
			fmt.Println("j= ", j)
		}
		fmt.Println("i=", i)
	}
}
```

3. return 语句，用在方法或者函数中，表示跳出所在的方法或函数。如果是普通方法，结束当前方法返回到调用处；如果是main 方法，则程序执行结束。


```
func main() {
	for i:=0; i<10;i++{
		for j:=0; j<10;j++{
			if j == 5{
				//不在输出j=5同时后面的循环结束，程序运行结束。
				return
			}
			fmt.Println("j= ", j)
		}
		fmt.Println("i=", i)
	}
}
```

4. Go语言中还有一个特殊用法，goto语句，无条件地转移到程序中指定的行。
goto 语句可以和条件语句配合使用，可用来实现条件转移，跳出循环体等功能。


```
func main() {
	for i:=0; i<10;i++{
		if i == 5{
			goto label1
		}
		fmt.Println("i=", i)
	}
	//因为用了goto，此输出会被直接跳过
	fmt.Println("我被跳过了！")
	label1:
	fmt.Println("我是label1")
}
```
需要注意的是，在Go程序设计中一般不主张使用goto语句，以免造成程序流程的混乱，使理解和调试程序都产生困难。


