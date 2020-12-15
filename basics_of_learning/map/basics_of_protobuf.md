# protobuf的.proto文件

[]()[扩展名为的proto的文件](https://www.cnblogs.com/hnxxcxg/p/9405247.html)

[]()[Google Protocol Buffer 的使用和原理](https://www.ibm.com/developerworks/cn/linux/l-cn-gpb/index.html)

[]()[[索引]文章索引 包含protobuf](https://www.jianshu.com/p/b33ca81b19b5)

## 概念

简单来讲， ProtoBuf 是结构数据序列化[1] 方法，可简单类比于 XML[2]，其具有以下特点：

语言无关、平台无关。即 ProtoBuf 支持 Java、C++、Python 等多种语言，支持多个平台</br>
高效。即比 XML 更小（3 ~ 10倍）、更快（20 ~ 100倍）、更为简单</br>
扩展性、兼容性好。你可以更新数据结构，而不影响和破坏原有的旧程序</br>

序列化[1]：将结构数据或对象转换成能够被存储和传输（例如网络传输）的格式，同时应当要保证这个序列化结果在之后（可能在另一个计算环境中）能够被重建回原来的结构数据或对象。</br>
类比于 XML[2]：这里主要指在数据通信和数据存储应用场景中序列化方面的类比，但个人认为 XML 作为一种扩展标记语言和 ProtoBuf 还是有着本质区别的。</br>



## 开始入门
### 1) 书写 .proto 文件
在 protobuf 的术语中，结构化数据被称为 Message。

    package lm; 
    message helloworld 
    { 
    required int32     id = 1;  // ID 
    required string    str = 2;  // str 
    optional int32     opt = 3;  //optional field 
    }
在上例中，package 名字叫做 lm，定义了一个消息 helloworld，该消息有三个成员，类型为 int32 的 id，另一个为类型为 string 的成员 str。opt 是一个可选的成员，即消息中可以不包含该成员。

命名规则：

    packageName.MessageName.proto

### 2) 编译 .proto 文件

写好 proto 文件之后就可以用 Protobuf 编译器将该文件编译成目标语言了。

    protoc -I=$SRC_DIR --cpp_out=$DST_DIR $SRC_DIR/addressbook.proto

命令将生成两个文件：

`lm.helloworld.pb.h` ， 定义了 C++ 类的头文件

`lm.helloworld.pb.cc` ， C++ 类的实现文件

在生成的头文件中，定义了一个 C++ 类 helloworld，后面的 Writer 和 Reader 将使用这个类来对消息进行操作。