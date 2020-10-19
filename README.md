# DataSetParser
# 数据集处理和标注
By Zhang Yingdong

## 主流数据集格式整理

### COCO Dataset

**COCO的全称是Common Objects in context，是微软团队提供的一个可以用来进行图像识别的数据集。**

基本数据结构如下：

```txt
{
    "info" : info, 
    "images" : [image],
    "annotations" : [annotation],
    "licenses" : [license],
}

info {
    "year" : int,
    "version" : str,
    "description" : str,
    "contributor" : str,
    "url" : str,
    "date_created" : datetime,
}

image{
    "id" : int, # 图片id
    "width" : int, # 图片宽
    "height" : int, # 图片高
    "file_name" : str, # 图片名
    "license" : int,
    "flickr_url" : str,
    "coco_url" : str, # 图片链接
    "date_captured" : datetime, # 图片标注时间
}

license{
    "id" : int,
    "name" : str,
    "url" : str,
}
```

##### 3种标注类型

**使用json文件存储，每种类型包含了训练和验证（下面会有详细解释）**

**object instances** （标注目标实例）
**object keypoints** （标注目标上的关键点）
**image captions**（图片描述/说明）Object Instance Annotations

##### object instances Annotations

实例标注形式：

```txt
annotation{
    "id" : int,
    "image_id" : int,
    "category_id" : int,
    "segmentation" : RLE or [polygon],
    "area" : float, 
    "bbox" : [x,y,width,height],
    "iscrowd" : 0 or 1,
}

categories[{
    "id" : int,
    "name" : str,
    "supercategory" : str,
}]123456789101112131415
```

其中，
如果instance表示单个object，则iscrowd=0，segmentation=polygon； 单个object也可能需要多个polygons，比如occluded的情况下；
如果instance表示多个objecs的集合，则iscrowd=1，segmentation=RLE. iscrowd=1用于标注较多的objects，比如人群.

##### Object Keypoint Annotations

关键点标注形式：

```txt
annotation{
    "keypoints" : [x1,y1,v1,...],
    "num_keypoints" : int,
    "[cloned]" : ...,
}

categories[{
    "keypoints" : [str],
    "skeleton" : [edge],
    "[cloned]" : ...,
}]1234567891011
```

关键点标注包括了物体标注的所有数据(比如 id, bbox, 等等)，以及两种额外属性信息.
“keypoints”是长度为 3K 的数组，K是对某类定义的关键点总数，位置为[x,y]，关键点可见性v.
如果关键点没有标注信息，则关键点位置[x=y=0]，可见性v=1；
如果关键点有标注信息，但不可见，则v=2.
如果关键点在物体segment内，则认为可见.
“num_keypoints”是物体所标注的关键点数(v>0). 对于物体较多，比如物体群或者小物体时，num_keypoints=0.
对于每个类别，categories结构体数据有两种属性：”keypoints” 和 “skeleton”.
“keypoints” 是长度为k的关键点名字符串；
“skeleton” 定义了关键点的连通性，主要是通过一组关键点边缘队列表的形式表示，用于可视化.

##### Image Caption Annotations

图片描述/说明标注形式：

```txt
annotation{
    "id" : int,
    "image_id" : int,
    "caption" : str,
}12345
```

图片描述标注包含了图片的主题信息. 每个主题描述了特定的图片，每张图片至少有5个主题.

[参考链接](https://blog.csdn.net/zziahgf/article/details/72819043)

### VOC Dataset

##### 5种格式：

- JPEGImages
- Annotations
- ImageSets
- SegmentationClass
- SegmentationObject

##### JPEGImages

主要提供的是PASCAL VOC所提供的所有的图片信息，包括训练图片，测试图片
这些图像就是用来进行训练和测试验证的图像数据。

##### Annotations

Annotations中是放着所有图片的标记信息，以xml为后缀名，每个xml对应JPEGImage中的一张图片

```xml
<annotation>  
    <folder>VOC2012</folder>                             
    <filename>2007_000392.jpg</filename>                             //文件名  
    <source>                                                         //图像来源（不重要）  
        <database>The VOC2007 Database</database>  
        <annotation>PASCAL VOC2007</annotation>  
        <image>flickr</image>  
    </source>  
    <size>                                            //图像尺寸（长宽以及通道数）                        
        <width>500</width>  
        <height>332</height>  
        <depth>3</depth>  
    </size>  
    <segmented>1</segmented>            //是否用于分割（在图像物体识别中01无所谓）  
    <object>                              //检测到的物体  
        <name>horse</name>                                         //物体类别  
        <pose>Right</pose>                                         //拍摄角度  
        <truncated>0</truncated>                                   //是否被截断（0表示完整）  
        <difficult>0</difficult>                                   //目标是否难以识别（0表示容易识别）  
        <bndbox>                                                   //bounding-box（包含左下角和右上角xy坐标）  
            <xmin>100</xmin>  
            <ymin>96</ymin>  
            <xmax>355</xmax>  
            <ymax>324</ymax>  
        </bndbox>  
    </object>  
    <object>              //检测到多个物体  
        <name>person</name>  
        <pose>Unspecified</pose>  
        <truncated>0</truncated>  
        <difficult>0</difficult>  
        <bndbox>  
            <xmin>198</xmin>  
            <ymin>58</ymin>  
            <xmax>286</xmax>  
            <ymax>197</ymax>  
        </bndbox>  
    </object>  
</annotation> 
```

##### ImageSets

- Action // 人的动作

- Layout // 人体的具体部位

- Main // 图像物体识别的数据，总共20类, 需要保证train val没有交集

  （ImageSets中的Main文件夹中保存的是各类数据出现的统计）

  - train.txt
  - val.txt
  - trainval.txt

- Segmentation // 用于分割的数据

##### SegmentationObject & SegmentationClass

保存的是物体分割后的数据

[参考链接1]([https://blog.csdn.net/See_Star/article/details/103546650?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522160171432519195264764201%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=160171432519195264764201&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_click~default-1-103546650.pc_first_rank_v2_rank_v28&utm_term=VOC%E6%A0%BC%E5%BC%8F&spm=1018.2118.3001.4187](https://blog.csdn.net/See_Star/article/details/103546650?ops_request_misc=%7B%22request%5Fid%22%3A%22160171432519195264764201%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=160171432519195264764201&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_click~default-1-103546650.pc_first_rank_v2_rank_v28&utm_term=VOC格式&spm=1018.2118.3001.4187))

[参考链接2]([https://blog.csdn.net/yogyliu/article/details/51859331?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522160171432519195264764201%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=160171432519195264764201&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~baidu_landing_v2~default-7-51859331.pc_first_rank_v2_rank_v28&utm_term=VOC%E6%A0%BC%E5%BC%8F&spm=1018.2118.3001.4187](https://blog.csdn.net/yogyliu/article/details/51859331?ops_request_misc=%7B%22request%5Fid%22%3A%22160171432519195264764201%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=160171432519195264764201&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~baidu_landing_v2~default-7-51859331.pc_first_rank_v2_rank_v28&utm_term=VOC格式&spm=1018.2118.3001.4187))

### TFRecord Dataset

**TFRecord 是Google官方推荐的一种数据格式，是Google专门为TensorFlow设计的一种数据格式。**

TFRecord文件中的数据都是通过tf.train.Example Protocol Buffer的格式存储的，TFRecord格式是一种二进制文件，它能够更好的利用内存，更方便复制和移动，并且不需要单独的标签文件

```
uint64 length
uint32 masked_crc32_of_length
byte   data[length]
uint32 masked_crc32_of_data
12345
```

上面是 Tensorflow 的官网给出的文档结构。整个文件由文件长度信息、长度校验码、数据、数据校验码组成。

**但对于我们普通开发者而言，我们并不需要关心这些**

Tensorflow 提供了丰富的 API 可以帮助我们轻松读写 TFRecord 文件。

**TFRecord 的核心内容在于内部有一系列的 Example **  

(Example 是 protocolbuf 协议下的消息体)

**一个 Example 消息体包含了一系列的 feature 属性。**

**每一个 feature 是一个 map，也就是 key-value 的键值对。**

key 取值是 String 类型。

而 value 是 Feature 类型的消息体，它的取值有 3 种：

1. BytesList
2. FloatList
3. Int64List

(**他们都是列表的形式**)

protocolbuf 是通用的协议格式，对主流的编程语言都适用。

所以这些 List 对应到 python 语言当中是 列表，而对于 Java 或者 C/C++ 来说他们就是数组。

举例：一个 BytesList 可以存储 Byte 数组，因此像字符串、图片、视频等等都可以容纳进去。

所以 TFRecord 可以存储几乎任何格式的信息。

[参考链接](https://blog.csdn.net/briblue/article/details/80789608)
