# OneDNN

[oneDNN v2.5.0 documentation](https://oneapi-src.github.io/oneDNN/index.html)

# Concepts 基础概念
## Primitives 原语

oneDNN是围绕primitive (dnnl::primitive)这个概念来构建的，一个primitive是一个对象，一个封装了特殊运算的对象，这些运算包括了例如：forward，convolution，backward LSTM运算，或者说是一些data transformation的操作。除此之外，基于primitive属性 (dnnl::primitive_attr)构建出来的具体primitive，可以表示为更复杂的混合运算操作，例如常见的前向卷积(forward convolution)后面接一个ReLU。

primitive和纯函数的区别主要在于：**primitive可以保存state信息**

1. primitive的一部分state是不可变的。例如，convolution primitive保存了一些参数（例如tensor shapes），并且可以据此提前计算出来其他的受依赖的参数（例如cache blocking）。因此，oneDNN可以按照这种方式为即将执行的操作来量身定制，并且预生成代码。oneDNN编程模型假设预计算所花费的时间，将被分摊到多次复用该primitive的头上，性价比更高。
2. primitive的state的可变部分称为暂存器（scratchpad）。暂存器是一个内存缓存（Memory buffer），用作primitive计算过程中的临时存储。暂存器可以归属于一个primitive对象（这会使得该对象非线程安全），也可以算作一个执行时的参数。

## Engines
Engines (dnnl::engine) 是计算设备的一个抽象表示。计算设备可以是CPU, GPU等等。大多数的primitive都是为了执行在一个特定engine上而创建的。唯一不同的是reorder primitive，用于在两个不同的engine间转换数据。

## Streams
Streams (dnnl::stream) 封装了与特定引擎相关的执行上下文(context)。例如，他们可以对应于OpenCL的命令队列。

## emory Objects