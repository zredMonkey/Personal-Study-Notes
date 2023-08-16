# 一、MySql

## 1.InnoDB索引下的最左前缀法则
MySQL 索引中的最左前缀原则是指，当创建复合索引（Composite Index）时，MySQL 只会使用索引中最左边的连续列来进行索引搜索和匹配。这意味着如果你在查询中只使用了复合索引的前几个列，那么数据库会利用这些列的索引来加速查询；而如果查询使用了复合索引的后面列，或者中间有间隔的列，数据库则无法有效地使用索引进行优化。

最左前缀原则的作用是提高索引的效率和查询的性能。通过将最常用的列放在索引的最左侧，可以使查询更快速地定位到匹配的数据，减少磁盘 I/O 和数据扫描的开销。

举个例子，假设有一个复合索引 `(col1, col2, col3)`。那么，以下查询可以充分利用索引：

1. 查询使用了索引的最左侧列：`WHERE col1 = 'value'`
2. 查询使用了索引的最左侧两列：`WHERE col1 = 'value' AND col2 = 'value'`

而以下查询无法充分利用索引：

1. 查询使用了索引的后两列：`WHERE col2 = 'value' AND col3 = 'value'`
2. 查询中间有间隔的列：`WHERE col1 = 'value' AND col3 = 'value'`

需要注意的是，最左前缀原则只适用于B+树索引，而不适用于哈希索引。因此，在创建复合索引时，需要根据查询的模式和频率来选择适当的列顺序，以使得索引能够最大程度地提高查询性能。

