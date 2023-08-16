Go的数据类型分为4大类：
基础类型(数字、字符串、布尔型)、
聚合类型(数组、结构体-通过组合简单类型得到的更复杂的数据类型)、
引用类型(指针(pointer)、slice、map、函数(function)、通道(channel))、
接口类型。


# 一、基础类型
## 1.数字
### 1.1 整数
#### 1.1.1 有符号整数
分四种大小：8位(int8)、16位(int16)、32位(int32)、64位(int64)。

#### 1.1.2 无符号整数
分四种大小：8位(uint8)、16位(uint16)、32位(uint32)、64位(uint64)。

### 1.2 浮点数
分为：float32、float64。

### 1.3 复数
分为complex64、complex128，二者分别由float32和float64构成。

## 2.字符串
是不可变的字节序列。

## 3.布尔型
分为true和false。

### 4.常量
**常量生成器iota**:它创建一系列相关值，而不是逐个值显式写出。
例如：


# 二、聚合类型
## 1.数组
是具有固定长度且拥有零个或者多个相同数据类型元素的序列。

```
var a [3]int // 3个整数的数组
fmp.Println(a[0]) // 输出数组的第一个元素


// 根据一组值初始化数组
var q [3]int = [3]int{1, 2, 3}

// 在数组字面量中，如果省略号“...”出现在数组长度的位置，那么数组的长度由初始化的数组元素个数决定

q := [...]{1, 2, 3}
```

注：
1.数组的长度是数组的一部分，所以[3]int和[4]int是两种不同的数组类型，数组的长度必须是常量表达式，也就是，这个表达式的值在程序编译时就可以确定。

```
q := [3]{1, 2, 3}
q = [4]int{1, 2, 3, 4}// 编译错误，不可将[4]int赋值给[3]int
```


## 2.结构体
将零个或者多个类型的命名变量组合到一起的聚合数据类型。

```
// 定义一个Employee的结构体
type Employee struct {
    ID        int
    Name      string 
    Address   string
    Position  string
}

var dilbert Employee

// 访问成员变量
dilbert.Name

// 获取成员变量的地址，然后通过指针来访问它
position := &dilbert.Position
*position = "Senior " + *position
```


# 三、引用类型
## 1.指针(pointer)
## 2.slice
- 动态数据结构。
- 可以用来访问数组的部分或者全部元素，而这个数组称为slie的底层数组。
- 表示一个拥有相同类型元素的可变长度的序列。slice通常写成[]T，其中元素的类型都是T，它看上去像是没有长度的数组类型。
- 有三个属性：指针、长度、容量：

```
- 指针：指向数组的第一个可以从slice中访问的元素，这个元素不一定是数组的第一个元素。
- 长度：slice中的元素个数，它不能超过slice的容量
- 容量：从slice的起始元素到底层数组的最后一个元素间元素的个数。
```

```
months := [...]{1: "January", 2:"February", 3:"March", 4:"April", 5:"May",6: "June", 7: "July", 8: "August", 9: "September",, 10: "October",11："November", 12: "Decemeber"}
// months[1]是January， months[12]是Decemeber, months[0]是“”
```

slice[i:j] 创建了一个新的slice。

```
Q2 = months[4:7]
summer := month[6:9]
fmp.PrintLn(Q2)
// ["April", "May", "June"]

endlessSummer := summer[:5] // 在slice容量范围内扩展了slice
fmt.PrintLn(endlessSummer)
// ["June July August Septemer October"]


s := []int{1, 2, 3, 4, 5}
// 注意初始化slice s时没有指定数组长度，这是与初始化数组的区别

// 内置哈数make可以创建一个具有指定元素类型、长度和容量的slice。其中容量参数可省略，在此情况下，slice的长度和容量相同
make([]T, len)
make([]T, len, cap)
```

注意：
（1）和数组不同，slice无法作比较，因此不能用==来测试两个slice是否具有相同的元素。
（2）想检查一个slice是否为空，那么使用len(s) == 0,而不是s == nil，因为s != nil的情况下，slice也有可能是空。


## 3.function
## 4.通道(channel)

## 5.map
- 动态数据结构。
- 在Go中，map是散列表的引用，map的类型是map[K]V，其中K和V是字典的键和值对应的数据类型。


```
// 用make创建map
ages := make(map[string]int)

ages := map[string]int{
    "alice": 31,
    "charlie": 34
}

// 新的空map map[string]int{}

-- map的访问  通过下标
ages["alice"] // 32

// 移除元素
delete(ages, "alice")

// map元素不是一个变量，无法获取地址
_ = &ages["bob"]  // 编译错误

```

# 四、接口类型