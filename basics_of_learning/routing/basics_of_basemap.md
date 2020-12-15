# base_map 高精地图
在 routing 模块中需要将高精地图base_map转换为拓扑地图routing_map，从而用有向图 graph 进行全局路线规划。

这个建图的步骤主要是在"routing/topo_creator”中，因此对这个包进行一定的解释和说明。

**（1）输入的base_map** </br>
先查看“#include "modules/map/hdmap/hdmap_util.h"“

该头文件的主要几个功能：</br>
(1) 在config(也就是gflags)中根据设定的路径或目录directory来找到对应地图格式的文件，并返回该文件路径：</br>

    std::string BaseMapFile();
    std::string SimMapFile();
    std::string RoutingMapFile();

(2) 创建高精地图的指针：</br>

    std::unique_ptr<HDMap> CreateMap(const std::string& map_file_path);

具体而言，是

    hdmap->LoadMapFromFile(map_file_path)
有关`HDMap`类：</br>
**成员变量**：
    
    HDMapImpl impl_;
**成员函数**：
各类返回对应已定义好的道路类型，如road，lane等的函数；</br>
加载文件，完善地图信息</br>

    int LoadMapFromFile(const std::string&  map_filename);
具体而言，是成员变量impl加载文件：

    impl_.LoadMapFromFile(map_filename);
而`HDMapImpl`类：
**成员变量**：

    Map map_;

    LaneTable lane_table_;
    JunctionTable junction_table_;
    CrosswalkTable crosswalk_table_;
    ...等各类已定义好的道路结构类型

**成员函数**：
各类返回对应已定义好的道路类型，如road，lane等的函数；</br>
加载文件，完善地图信息</br>

    int HDMapImpl::LoadMapFromFile(const std::string& map_filename) {
        Clear();
        // TODO(All) seems map_ can be changed to a local variable of this
        // function, but test will fail if I do so. if so.
        if (absl::EndsWith(map_filename, ".xml")) {
            if (!adapter::OpendriveAdapter::LoadData(map_filename, &map_)) {
            return -1;
            }
        } else if (!cyber::common::GetProtoFromFile(map_filename, &map_)) {
            return -1;
        }

        return LoadMapFromProto(map_);
    }

而加载的主要是两种格式，基于opendrive的标准格式，以及Apollo自己的Cyber里的格式。而此次需要用的是基于opendrive的标准格式，所以参考：

    adapter::OpendriveAdapter::LoadData(map_filename, &map_)

具体而言，是`OpendriveAdapter`类：</br>
包含标准库中用于xml解析的头文件：

    #include <tinyxml2.h>
只有一个**成员函数**：</br>

    static bool LoadData(const std::string& filename, apollo::hdmap::Map* pb_map);

简单说，作用是加载数据，放入到`apollo::hdmap::Map* pb_map`类中。</br>
具体而言，先基于`tinyxml2`标准库进行xml文件的读取，

    tinyxml2::XMLDocument document;
    ...document.LoadFile(filename.c_str())...
然后，读取和解析xml文件，由于该xml文件是基于opendrive标准的，所以也包含各种 // root node， // header，// road， // junction， // objects，并输出到`apollo::hdmap::Map* pb_map`中。

而`apollo::hdmap::Map* pb_map`类：</br>
是由/map/proto/map.proto编译生成的：</br>
**成员变量**：</br>

**成员函数**：</br>


(3) 定义HDMapUtil类：</br>
**成员变量**：</br>
base_map_高精地图的指针，其中mutex是互斥锁，是在进程中考虑的。
    
    static std::unique_ptr<HDMap> base_map_;
    static uint64_t base_map_seq_;
    static std::mutex base_map_mutex_;

sim_map_高精地图的指针，其中mutex是互斥锁，是在进程中考虑的。

    static std::unique_ptr<HDMap> sim_map_;
    static std::mutex sim_map_mutex_;

**成员函数**：
读取地图（可以是base_map_或是sim_map_）
base_map_：

    static const HDMap* BaseMapPtr();
    static const HDMap* BaseMapPtr(const relative_map::MapMsg& map_msg);
    static const HDMap& BaseMap();
sim_map_：

    static const HDMap* SimMapPtr();
    static const HDMap& SimMap();

具体而言，已经打开了地图就直接返回，否则`CreateMap`，而`CreateMap`就是`(hdmap->LoadMapFromFile(map_file_path)`，具体可以见上面描述的`hdmap`类。
