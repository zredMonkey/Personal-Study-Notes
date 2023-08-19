# BigDecimal注意事项
## 1.BigDecimal(double)和BigDecimal(String)的区别
对于BigDecimal(double)，因为double是不精确的，所以用double来创建的BigDecimal也是不精确的。如0.1这个数字，double也只能表示它的近似值。

所以，new BigDecimal(0.1)创建出来的值并不是正好等于0.1的。

而对于BigDecimal(String)，我们使用BigDecimal(“0.1”)创建一个Decimal的时候，创建出来的值正好等于0.1。



## 2.不要用BigDecimal的equals方法做等值比较
因为BigDecimal的equals方法会比较两部分：值(value)和标度(scale)，对于0.1和0.10这两个数字，虽然值一样，但是标度不一样，用equals比较会返回false。BigDecimal比较需要使用compare。



