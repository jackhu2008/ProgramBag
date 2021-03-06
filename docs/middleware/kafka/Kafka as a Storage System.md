参考：
- [kafka design](https://kafka.apache.org/documentation/#design)

## 存储
### 文件系统
kafka深度依赖文件系统来存储、缓存消息。
- 硬盘顺序读写的速度远大于随机读写，而且，现代os对顺序读写作了大量优化，提供预读和后写技术。
- 以pagecache为中心的设计：文件系统和pagecache要优于内存缓存、其它结构，因为可以访问所有内存；存储压缩字节结构而不是单个对象，存储更多数据，而且不受gc影响；即使服务重启，pagecache仍可用，而进程内cache则要重新加载；代码逻辑简单，因为文件系统和pagecache的一致性os已经提供。

### 常量时间
- BTrees是可用的最通用的数据结构，可以在消息传递系统中支持各种各样的事务性和非事务性语义。时间复杂度是O（log N），可以等同与常量时间，但对于磁盘来说不是真的，一次寻道10ms，不能并行。存储系统将缓存操作和磁盘操作混合在一起，使得树形结构的操作是超线性的。
- 持久队列可以建立在简单的读取上，并附加到文件中，所有的操作都是O（1）。性能和数据大小分离，机器可以使用廉价、低转速的磁盘。
- 基于上一点，kafka可以较长时间保留消息，而不是消费完就删除。
