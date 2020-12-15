# 解析地图是规划的第一步
[]()[Dig into Apollo - Map](https://github.com/daohu527/Dig-into-Apollo/tree/master/modules/map)

## map框架以及map之间传输地图都是用的opendrive标准格式
map框架：

![]()![](images_map/map框架.png)

apollo的高精度地图采用了opendrive格式，opendrive是一个统一的地图标准，这样保证了地图的通用性。其中map模块主要提供的功能是**读取高精度地图**，并且转换成apollo程序中的Map对象。直白一点就是说把xml格式的opendrive高精度地图，读取为程序能够识别的格式。[注意map是读取高精地图，而不是高精度地图的制作]

## /proto: 地图各元素的消息格式（人行横道，车道线等）
包括lane、junction、crosswalk等等消息格式类型，通过写.proto文件，在package命名空间下定义message数据结构，其中有optional可选的、required要求必须有的，repeated可以重复的各类具体类型，然后再通过编译.proto文件，根据需要的语言类型，得到对应的比如c++文件等，包括头文件和source文件，一起构成了新类的定义（而定义出的就和.proto文件里的一样）。

## /hdmap/adapter/xml_parser/: xml文件解析（遵循opendrive标准）
这里的地图来自于openstreet高精地图，导出成遵循opendrive标准的xml文件。

![]()![](images_map/xml_paser.png)






