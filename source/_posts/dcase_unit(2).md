---
title: dcase_unit教程（二）——各单元介绍
date: 2018-03-26 18:16:38
tags: [python]
categories: [DCASE]
---

接着上一篇教程，继续的hi各个UTILITIES的介绍。[网址](https://dcase-repo.github.io/dcase_util/index.html)

## 1. Container

数据容器的类。这些数据的目的是为了包装数据，使用有用的方法来访问和操作数据，以及加载和存储数据。

这些容器是从标准的Python容器（例如对象，列表和字典）继承的，以使它们可以与其他工具和库一起使用。

### 1.1 Basic containers
- ObjectContainer

`dcase_util.containers.ObjectContainer`，从标准对象类继承的对象的容器类。


调用格式 | 功能
---|---
`ObjectContainer(\*args, \*\*kwargs)` | 从标准对象类继承的对象的容器类。
`ObjectContainer.load([filename])` | 加载文件
`ObjectContainer.save([filename])` | 保存文件
`ObjectContainer.show()` | 打印容器内容
`ObjectContainer.log([level])` | 日志容器内容

示例如何从ObjectContainer继承类：

```
class Multiplier(dcase_util.containers.ObjectContainer):
    def __init__(self, multiplier=None, **kwargs):
        super(Multiplier, self).__init__(**kwargs)
        self.multiplier = multiplier

    def __str__(self):
        ui = dcase_util.ui.FancyStringifier()
        output = super(Multiplier, self).__str__()
        output+= ui.data(field='multiplier', value=self.multiplier)
        return output

    def multiply(self, data):
        return self.multiplier * data

m = Multiplier(10)
m.show()
# Multiplier :: Class
#   multiplier                        : 10

print(m.multiply(5))
# 50

# Save object
m.save('test.cpickle')

# Load object
m2 = Multiplier().load('test.cpickle')

print(m2.multiply(5))
# 50

m2.show()
# Multiplier :: Class
#   filename                          : test.cpickle
#   multiplier                        : 10
```


#### 1.1.1 DictContainer

`dcase_util.containers.DictContainer`字典容器类继承自标准的字典类。

调用格式 | 功能
---|---
`DictContainer(\*args, \*\*kwargs)` | 字典从标准dict类继承的容器类。
`DictContainer.load([filename])` | 加载文件
`DictContainer.save([filename])` | 保存文件
`DictContainer.show()` | 打印容器内容
`DictContainer.log([level])` | 日志容器内容
`DictContainer.get_path(path[, default, data])` | 从带嵌入字典的路径中获取值
`DictContainer.set_path(path, new_value[, data])` | 使用点路径（dotted path）在嵌套字典中设置值
`DictContainer.get_leaf_path_list([...])` | 获取路径列表到嵌套字典中的所有叶节点。
`DictContainer.merge(override[, target])` | 递归字典合并
`DictContainer.get_hash_for_path([dotted_path])` | 在给定路径下获取数据的唯一哈希字符串。
`DictContainer.get_hash([data])` | 获取给定参数字典的唯一哈希字符串（md5）。

用法示例：

```
import dcase_util

d = dcase_util.containers.DictContainer(
    {
        'test': {
            'field1': 1,
            'field2': 2,
        },
        'test2': 100
    }
)
d.show()
# DictContainer
#   test
#     field1                          : 1
#     field2                          : 2
#   test2                             : 100

print(d.get_path('test.field1'))
# 1

print(d.get_path(['test', 'field1']))
# 1

print(d.get_path('test2'))
# 100

d.set_path('test.field2', 200)
print(d.get_path('test.field2'))
# 200

print(d.get_leaf_path_list())
# ['test.field1', 'test.field2', 'test2']

print(d.get_leaf_path_list(target_field_startswith='field'))
# ['test.field1', 'test.field2']

d.show()
# DictContainer
#   test
#     field1                          : 1
#     field2                          : 200
#   test2                             : 100
```

#### 1.1.2 ListContainer

`dcase_util.containers.ListContainer`，列表容器从标准列表类继承的容器类。

调用格式 | 功能
---|---
`ListContainer(\*args, \*\*kwargs)` | 字典从标准dict类继承的容器类。
`ListContainer.load([filename, headers])` | 加载文件
`ListContainer.save([filename])` | 保存文件
`ListContainer.show()` | 打印容器内容
`ListContainer.log([level])` | 日志容器内容
`ListContainer.update(data)` | 用给定列表替换内容

#### 1.1.3 ListDictContainer

`dcase_util.containers.ListDictContainer`，从标准列表类继承的字典列表容器类。


调用格式 | 功能
---|---
`ListDictContainer(\*args, \*\*kwargs)` | 字典列表从标准dict类继承的容器类。
`ListDictContainer.load([filename, fields, ...])` | 加载文件
`ListDictContainer.save([filename, fields, ...])` | 保存文件
`ListDictContainer.show()` | 打印容器内容
`ListDictContainer.log([level])` | 日志容器内容
`ListDictContainer.search(key, value)` | 在字典列表中搜索
`ListDictContainer.get_field(field_name[, ...])` | 从字段获取所有数据

用法示例：

```
import dcase_util
ld = dcase_util.containers.ListDictContainer(
    [
        {
            'field1': 1,
            'field2': 2,
        },
        {
            'field1': 3,
            'field2': 4,
        },
    ]
)
ld.show()
# ListDictContainer
# [0] =======================================
#    DictContainer
#      field1                            : 1
#      field2                            : 2
#
# [1] =======================================
#    DictContainer
#      field1                            : 3
#      field2                            : 4

print(ld.search(key='field1', value=3))
# DictContainer
#   field1                            : 3
#   field2                            : 4

print(ld.get_field(field_name='field2'))
# [2, 4]
```

#### 1.1.4 RepositoryContainer

`dcase_util.containers.RepositoryContainer`，存储库的容器类，从`dcase_util.containers.DictContainer`继承。

调用格式 | 功能
---|---
`RepositoryContainer(\*args, \*\*kwargs)` | 从DictContainer继承的存储库的容器类。
`RepositoryContainer.load([filename])` | 加载文件
`RepositoryContainer.save([filename])` | 保存文件
`RepositoryContainer.show()` | 打印容器内容
`RepositoryContainer.log([level])` | 日志容器内容

#### 1.1.5 TextContainer
`dcase_util.containers.TextContainer`文本的容器类，从`dcase_util.containers.ListContainer`继承。

TextContainer（\ * args，\ * \ * kwargs）继承自ListContainer的文本的容器类。

调用格式 | 功能
---|---
`TextContainer(\*args, \*\*kwargs)` | 继承自ListContainer的文本的容器类。
`TextContainer.load([filename, headers])` | 加载文件
`TextContainer.save([filename])` | 保存文件
`TextContainer.show()` | 打印容器内容
`TextContainer.log([level])` | 日志容器内容

### 1.2 Data containers

#### 1.2.1 DataContainer
`dcase_util.containers.DataContainer`，数据的容器类，从`dcase_util.containers.ObjectContainer`继承。


调用格式 | 功能
---|---
`DataContainer([data, stats, metadata, ...])` | 数据的容器类，从ObjectContainer继承
`DataContainer.load([filename])` | Load file
`DataContainer.save([filename])` | Save file
`DataContainer.show()` | 打印容器内容
`DataContainer.log([level])` | 记录容器内容
`DataContainer.data` | 数据矩阵
`DataContainer.shape` | 数据矩阵的形状
`DataContainer.length` | 数据列的数量
`DataContainer.frames` | 数据帧的数量
`DataContainer.push_processing_chain_item(...)` | 加工链项目入栈
`DataContainer.focus_start` | 	聚焦段开始
`DataContainer.focus_stop` |	聚焦段结束
`DataContainer.stats` | 数据矩阵的基本统计
`DataContainer.reset_focus()` | 重置聚焦段（Focus segment）
`DataContainer.get_focused()` | 从数据数组获取聚焦段
`DataContainer.freeze()` | 冻结聚焦段（Freeze focus segment），并复制为容器数据。
`DataContainer.get_frames([frame_ids, frame_hop])` | 从数据数组中获取帧。
`DataContainer.plot()` | 可视化数据数组。


#### 1.2.2 DataArrayContainer

    `dcase_util.containers.DataArrayContainer`

Container class for data, inherited from dcase_util.containers.DataContainer.

调用格式 | 功能
---|---
`DataArrayContainer([data, stats, metadata, ...])` | Array data container `DataArrayContainer.load([filename])` | Load file
`DataArrayContainer.save([filename])` | Save file
`DataArrayContainer.show(` | Print container content
`DataArrayContainer.log([level])` | Log container content
`DataArrayContainer.data` | 	Data matrix
`DataArrayContainer.shape` | Shape of data matrix
`DataArrayContainer.length` | Number of data columns
`DataArrayContainer.frames` | Number of data frames
`DataArrayContainer.push_processing_chain_item(...)` | Push processing chain item
`ataArrayContainer.focus_start` | Focus segment start
`ataArrayContainer.focus_stop` | Focus segment stop
`DataArrayContainer.stats` | Basic statistics of data matrix.
`DataArrayContainer.reset_focus()` | Reset focus segment
`DataArrayContainer.get_focused()` | Get focus segment from data array.
`DataArrayContainer.freeze()` | Freeze focus segment, copy segment to be container’s data.
`DataArrayContainer.get_frames([frame_ids, ...])` | Get frames from data array.
`DataArrayContainer.plot()` | Visualize data array.

#### 1.2.3 DataMatrix2DContainer

    `dcase_util.containers.DataMatrix2DContainer`

`DataMatrix2DContainer`是二维数据矩阵（`numpy.ndarray`）的数据容器。

基本用法：

```
# Initialize container with random matrix 10x100, and set time resolution to 20ms
data_container = dcase_util.containers.DataMatrix2DContainer(
  data=numpy.random.rand(10,100),
  time_resolution=0.02
)

# When storing, e.g., acoustic features, time resolution corresponds to feature extraction frame hop length.

# Access data matrix directly
print(data_container.data.shape)
# (10, 100)

# Show container information
data_container.show()
# DataMatrix2DContainer :: Class
#   Data
#     data                            : matrix (10,100)
#     Dimensions
#       time_axis                     : 1
#       data_axis                     : 0
#     Timing information
#       time_resolution               : 0.02 sec
#   Meta
#     stats                           : Calculated
#     metadata                        : -
#     processing_chain                : -
#   Duration
#       Frames                        : 100
#       Seconds                       : 2.00 sec
```
该容器具有聚焦机制，可灵活定位数据矩阵的一部分。 可以根据时间进行对焦（如果时间分辨率已定义，则以秒为单位），或基于帧ID。

使用焦点机制（ focus mechanism），访问数据和可视化数据的示例：

```
# Using focus to get part data between 0.5 sec and 1.0 sec
print(data_container.set_focus(start_seconds=0.5, stop_seconds=1.0).get_focused().shape)
# (10, 25)

# Using focus to get part data between frame 10 and 50
print(data_container.set_focus(start=10, stop=50).get_focused().shape)
# (10, 40)

# Resetting focus and accessing full data matrix
data_container.reset_focus()
print(data_container.get_focused().shape)
# (10, 100)

# Access frames 1, 2, 10, and 30
data_container.get_frames(frame_ids=[1,2,10,30])

# Access frames 1-5, and only first value per column
data_container.get_frames(frame_ids=[1,2,3,4,5], vector_ids=[0])

# Transpose matrix
transposed_data = data_container.T
print(transposed_data.shape)
# (100, 10)

# Plot data
data_container.plot()
```

调用格式 | 功能
---|---
`DataMatrix2DContainer([data, stats, ...])` | Two-dimensional data matrix container class, inherited from DataContainer.
`DataMatrix2DContainer.load([filename])` | Load file
`DataMatrix2DContainer.save([filename])` | Save file
`DataMatrix2DContainer.show()` | Print container content
`DataMatrix2DContainer.log([level])` | Log container content
`DataMatrix2DContainer.data` | Data matrix
`DataMatrix2DContainer.shape` | Shape of data matrix
`DataMatrix2DContainer.length` | Number of data columns
`DataMatrix2DContainer.frames` | Number of data frames
`DataMatrix2DContainer.vector_length` | Data vector length
`DataMatrix2DContainer.push_processing_chain_item(...)` | Push processing chain item
`DataMatrix2DContainer.focus_start` | Focus segment start
`DataMatrix2DContainer.focus_stop` | Focus segment stop
`DataMatrix2DContainer.T` | Transposed data in a data container
`DataMatrix2DContainer.stats` | Basic statistics of data matrix.
`DataMatrix2DContainer.reset_focus()` | Reset focus segment
`DataMatrix2DContainer.get_focused()` | Get focus segment from data matrix.
`DataMatrix2DContainer.freeze()` | Freeze focus segment, copy segment to be container’s data.
`DataMatrix2DContainer.get_frames([...])` | Get frames from data matrix.
`DataMatrix2DContainer.plot()` | Visualize data matrix.

#### 1.2.4 DataMatrix3DContainer

    `dcase_util.containers.DataMatrix3DContainer`

调用格式 | 功能
---|---
`DataMatrix3DContainer([data, stats, ...])` | 三位数据矩阵容器, 从`DataMatrix2DContainer`继承.
`DataMatrix3DContainer.load([filename])` | Load file
`DataMatrix3DContainer.save([filename])` | Save file
`DataMatrix3DContainer.show()` | Print container content
``DataMatrix3DContainer.log([level])` | Log container content
`DataMatrix3DContainer.data` | Data matrix
`DataMatrix3DContainer.length` | Number of data columns
`DataMatrix3DContainer.frames` | Number of data frames

#### 1.2.5 BinaryMatrix3DContainer

    `dcase_util.containers.BinaryMatrix2DContainer`

调用格式 | 功能
---|---
`BinaryMatrix2DContainer([data, ...])` | Two-dimensional data matrix container class, inherited from DataContainer.
`BinaryMatrix2DContainer.load([filename])` | Load file
`BinaryMatrix2DContainer.save([filename])` | Save file
`BinaryMatrix2DContainer.show()` | Print container content
`BinaryMatrix2DContainer.log([level])` | Log container content
`BinaryMatrix2DContainer.data` | Data matrix
`BinaryMatrix2DContainer.length` | Number of data columns
`BinaryMatrix2DContainer.frames` | Number of data frames
`BinaryMatrix2DContainer.pad(length[, ...])` | Pad binary matrix along time axis
`BinaryMatrix2DContainer.plot([...])` | Visualize binary matrix, and optionally synced data matrix.


#### 1.2.4 DataRepository

    dcase_util.containers.DataRepository

DataRepository是可用于存储多个其他数据容器的容器。存储库存储具有两个级别信息的数据：标签和流。 标签是更高级别的密钥，流是第二级。 例如，可以使用储存库来储存与相同音频信号有关的多个不同声学特征。 流ID可用于存储从不同音频通道提取的特征。 后面的功能可以使用提取器标签和流ID进行访问。

用法示例：

```
# Initialize container with data
data_repository = dcase_util.containers.DataRepository(
    data={
        'label1': {
            'stream0': {
                'data': 100
            },
            'stream1': {
                'data': 200
            }
        },
        'label2': {
            'stream0': {
                'data': 300
            },
            'stream1': {
                'data': 400
            }
        }
    }
)
# Show container information::
data_repository. show()
# DataRepository :: Class
#     Repository info
#       Item class                    : DataMatrix2DContainer
#       Item count                    : 2
#       Labels                        : ['label1', 'label2']
#     Content
#       [label1][stream1]             : {'data': 200}
#       [label1][stream0]             : {'data': 100}
#       [label2][stream1]             : {'data': 400}
#       [label2][stream0]             : {'data': 300}

# Accessing data inside repository
data_repository.get_container(label='label1',stream_id='stream1')
# {'data': 200}

# Setting data
data_repository.set_container(label='label3',stream_id='stream0', container={'data':500})
data_repository. show()
# DataRepository :: Class
#     Repository info
#       Item class                    : DataMatrix2DContainer
#       Item count                    : 3
#       Labels                        : ['label1', 'label2', 'label3']
#     Content
#       [label1][stream1]             : {'data': 200}
#       [label1][stream0]             : {'data': 100}
#       [label2][stream1]             : {'data': 400}
#       [label2][stream0]             : {'data': 300}
#       [label3][stream0]             : {'data': 500}
```

调用格式 | 功能
---|---
`DataRepository([data, filename_dict, ...])` | 数据存储库容器类将多个DataContainer一起存储
`DataRepository.load([filename_dict])` | Load file list
`DataRepository.get_container(label[, stream_id])` | Get container from repository
`DataRepository.set_container(container, label)` | Store container to repository
`DataRepository.push_processing_chain_item(...)` | Push processing chain item
`DataRepository.plot()` | 可视化存储在存储库中的数据。


### 1.3 Audio containers

    dcase_util.containers.AudioContainer
    
AudioContainer是用于多声道音频的数据容器。 它读取多种格式（WAV，FLAC，M4A，WEBM）并写入WAV和FLAC文件。 直接从Youtube下载音频内容也受支持。

基本用法示例：

```
# Generating two-channel audio
audio_container = dcase_util.containers.AudioContainer(fs=44100)
t = numpy.linspace(0, 2, 2 * audio_container.fs, endpoint=False)
x1 = numpy.sin(220 * 2 * numpy.pi * t)
x2 = numpy.sin(440 * 2 * numpy.pi * t)
audio_container.data = numpy.vstack([x1, x2])

audio_container.show()
# AudioContainer :: Class
#  Sampling rate                     : 44100
#     Channels                        : 2
#   Duration
#     Seconds                         : 2.00 sec
#     Milliseconds                    : 2000.00 ms
#     Samples                         : 88200 samples

# Loading audio file
audio_container = dcase_util.containers.AudioContainer().load(
  filename=dcase_util.utils.Example.audio_filename()
)

# Loading audio content from Youtube
audio_container = dcase_util.containers.AudioContainer().load_from_youtube(
  query_id='2ceUOv8A3FE',
  start=1,
  stop=5
)
```

该容器具有聚焦机制，可灵活捕捉部分音频数据，同时保持完整的音频信号不变。 可以根据时间进行聚焦（如果时间分辨率已定义，则以秒为单位），或基于样本ID。 可以对单声道或混音（单声道）频道进行聚焦。 音频容器内容可以通过冻结来替代焦点细分。

使用焦点细分机制的示例：


```
# Using focus to get part data between 0.5 sec and 1.0 sec
print(audio_container.set_focus(start_seconds=0.5, stop_seconds=1.0).get_focused().shape)
# (2, 22050)

# Using focus to get part data starting 5 sec with 2 sec duration
print(audio_container.set_focus(start_seconds=5, duration_seconds=2.0).get_focused().shape)
# (2, 88200)

# Using focus to get part data starting 5 sec with 2 sec duration, mixdown of two stereo channels
print(audio_container.set_focus(start_seconds=5, duration_seconds=2.0, channel='mixdown').get_focused().shape)
# (88200,)

# Using focus to get part data starting 5 sec with 2 sec duration, left of two stereo channels
print(audio_container.set_focus(start_seconds=5, duration_seconds=2.0, channel='left').get_focused().shape)
# (88200,)

# Using focus to get part data starting 5 sec with 2 sec duration, seconds audio channel (indexing starts from 0)
print(audio_container.set_focus(start_seconds=5, duration_seconds=2.0, channel=1).get_focused().shape)
# (88200,)

# Using focus to get part data between samples 44100 and 88200
print(audio_container.set_focus(start=44100, stop=88200).get_focused().shape)
# (2, 44100)

# Resetting focus and accessing full data matrix::
audio_container.reset_focus()
print(audio_container.get_focused().shape)
# (2, 441001)

# Using focus to get part data starting 5 sec with 2 sec duration, and freeze this segment ::
audio_container.set_focus(start_seconds=5, duration_seconds=2.0).freeze()
print(audio_container.shape)
# (2, 88200)
```
Processing examples:


```
# Normalizing audio
audio_container.normalize()

# Resampling audio to target sampling rate
audio_container.resample(target_fs=16000)
```
Visualizations examples:


```
# Plotting waveform
audio_container.plot_wave()

# Plotting spectrogram
audio_container.plot_spec()
```


类 | 功能
---|---
`AudioContainer([data, fs, ...])` | Audio container class.
`AudioContainer.load([filename, fs, mono, ...])` | Load file
`AudioContainer.load_from_youtube(query_id[, ...])` | Load audio data from youtube
AudioContainer.save([filename, bit_depth])` | Save audio
`AudioContainer.show()` | Print container content
`AudioContainer.log([level])` | Log container content
`AudioContainer.data` | Audio data
`AudioContainer.focus_start_samples` | Focus segment start in samples.
`AudioContainer.focus_start_seconds` | Focus segment start in seconds.
`AudioContainer.focus_stop_samples` | Focus segment stop in samples.
`AudioContainer.focus_stop_seconds` | Focus segment stop in seconds.
`AudioContainer.focus_channel` | Focus channel
`AudioContainer.loaded` | Audio load status.
`AudioContainer.shape` | Audio data shape.
`AudioContainer.length` | Length of audio data in samples.
`AudioContainer.duration_samples` | Duration of audio data in samples.
`AudioContainer.duration_ms` | Duration of audio data in milliseconds.
`AudioContainer.duration_sec` | Duration of audio data in seconds.
`AudioContainer.streams` | Rename channels for compatibility.
`AudioContainer.empt` | Check if audio data is empty.
`AudioContainer.reset_focus()` | Reset focus segment.
`AudioContainer.set_focus([start, stop, ...])` | Set focus segment
`AudioContainer.get_focused()` | Get focus segment from audio data.
`AudioContainer.freeze()` | Freeze focus segment, copy segment to be container’s data.
`AudioContainer.frames([frame_length, ...])` | Slice audio into overlapping frames.
`AudioContainer.normalize([headroom])` | Normalize audio data.
`AudioContainer.resample(target_fs[, scale, ...])` | Resample audio data.
`AudioContainer.mixdown()` | Mix all audio channels into single channel.
`AudioContainer.plot([plot_type])` | 	Visualize audio data
`AudioContainer.plot_wave([x_axis, ...])` | Visualize audio data as waveform.
`AudioContainer.plot_spec([spec_type, ...])` | Visualize audio data as spectrogram.

### 1.4 Feature containers

- **FeatureContainer**

    `dcase_util.containers.FeatureContainer`

类 | 功能
---|---
`FeatureContainer([data, stats, metadata, ...])` | 	从DataContainer继承的单个特征矩阵的特征容器类。

- **FeatureRepository**

    `dcase_util.containers.FeatureRepository`

类 | 功能
---|---
`FeatureRepository([filename_dict, ...])` | 	Feature repository container class to store multiple FeatureContainers together.

### 1.5 Mapping containers
**OneToOneMappingContainer**，`dcase_util.containers.OneToOneMappingContainer`

类 | 功能
---|---
`OneToOneMappingContainer(\*args, \*\*kwargs)` | Mapping container class for 1:1 data mapping, inherited from DictContainer class.
`OneToOneMappingContainer.load([filename])` | Load file
`OneToOneMappingContainer.save([filename])` | Save file
`OneToOneMappingContainer.show()` | 	Print container content
`OneToOneMappingContainer.log([level])` | Log container content
`OneToOneMappingContainer.map(key[, default])` | Map with a key.
`OneToOneMappingContainer.flipped` | Exchange map key and value pairs.
### 1.6 Metadata containers

- **MetaDataItem**

    `dcase_util.containers.MetaDataItem`

类 | 功能
---|---
`MetaDataItem(\*args, \*\*kwargs)` | Meta data item class, inherited from standard dict class.
`MetaDataItem.show()` | Print container content
`MetaDataItem.log([level])` | Log container content
`MetaDataItem.id` | Unique item identifier
`MetaDataItem.get_list()` | Return item values in a list with specified order.
`MetaDataItem.filename` | Filename
`MetaDataItem.filename_original` | Filename
`MetaDataItem.scene_label` | Scene label
`MetaDataItem.event_label` | 	Event label
`MetaDataItem.onset` | 	Onset
`MetaDataItem.offset` | Offset
`MetaDataItem.identifier` | Identifier
`MetaDataItem.source_label` | Source label
`MetaDataItem.tags` | Tags
`MetaDataItem.active_within_segment(start, stop)` | Item active withing given segment.

- **MetaDataContainer**

    `dcase_util.containers.MetaDataContainer`


类 | 功能
---|---
`MetaDataContainer(\*args, \*\*kwargs)` | Meta data container class, inherited from ListDictContainer.
`MetaDataContainer.log([level, show_data, ...])` | Log container content
`MetaDataContainer.log_all([level])` | Log container content with all meta data items.
`MetaDataContainer.show([show_data, show_stats])` | Print container content
`MetaDataContainer.show_all()` | Print container content with all meta data items.
`MetaDataContainer.load([filename, fields, ...])	` | Load event list from delimited text file (csv-formatted)
`MetaDataContainer.save([filename, fields, ...])` | Save content to csv file
`MetaDataContainer.append(item)` | Append item to the meta data list
`MetaDataContainer.file_count` | Number of files
`MetaDataContainer.event_count` | Number of events
`MetaDataContainer.scene_label_count` | Number of unique scene labels
`MetaDataContainer.event_label_count` | Number of unique event labels
`MetaDataContainer.identifier_count` | Number of unique identifiers
`MetaDataContainer.tag_count` | Number of unique tags
`MetaDataContainer.unique_files` | Unique files
`MetaDataContainer.unique_event_labels` | Unique event labels
`MetaDataContainer.unique_scene_labels` | Unique scene labels
`MetaDataContainer.unique_tags` | Unique tags
`MetaDataContainer.unique_identifiers` | Unique identifiers
`MetaDataContainer.max_offset` | Find the offset (end-time) of last event
`MetaDataContainer.get_string([show_data, ...])` | Get content in string format
`MetaDataContainer.filter([filename, ...])` | Filter content
`MetaDataContainer.filter_time_segment([...])` | Filter time segment
`MetaDataContainer.process_events([...])` | Process event content
`MetaDataContainer.add_time(time)` | Add time offset to event onset and offset timestamps
`MetaDataContainer.stats([event_label_list, ...])` | Statistics of the container content
`MetaDataContainer.scene_stat_counts()` | Scene count statistics
`MetaDataContainer.event_stat_counts()` | Event count statistics
`MetaDataContainer.tag_stat_counts()` | Tag count statistics
`MetaDataContainer.to_event_roll([...])` | Event roll
`MetaDataContainer.intersection(second_metadata)` | Intersection of two meta containers
`MetaDataContainer.intersection_report(...)` | Intersection report for two meta containers
`MetaDataContainer.difference(second_metadata)` | Difference of two meta containers

### 1.7 Parameter containers
- **ParameterContainer**

    `dcase_util.containers.ParameterContainer`

类名 | 功能
---|---
`ParameterContainer(\*args, \*\*kwargs)` | 	参数容器类，用于继承自`DictContainer`类的参数。

- **AppParameterContainer**

    `dcase_util.containers.AppParameterContainer`

类名 | 功能
---|---
`AppParameterContainer([data, app_base, ...])` | 应用程序参数的参数容器类，继承自`ParameterContainer`。
`AppParameterContainer.reset([field_labels, ...])` | 	
`AppParameterContainer.process([...])` | Process parameters
`AppParameterContainer.override(override)` | 递归地覆盖容器内容。
`AppParameterContainer.get_path_translated(path)` | 用路径获取数据，路径可以包含将被翻译的字符串常量。
`AppParameterContainer.set_path_translated(...)` | 用路径设置数据，路径可以包含将被翻译的字符串常量。

- **DCASEAppParameterContainer**

    `dcase_util.containers.DCASEAppParameterContainer`

类名 | 功能
---|---
`DCASEAppParameterContainer(\*args, \*\*kwargs)` | DCASE应用程序参数文件的参数容器类，从`AppParameterContainer`继承。

- **ParameterListContainer**

    `dcase_util.containers.ParameterListContainer`

类名 | 功能
---|---
`ParameterListContainer(\*args, \*\*kwargs)` | 	参数列表容器，继承自`ListDictContaine`r。

### 1.8 Probability containers

- **ProbabilityItem**

   `dcase_util.containers.ProbabilityItem`

类名 | 功能
---|---
`ProbabilityItem(\*args, \*\*kwargs)` | 概率数据项类，继承自标准字典类。
`ProbabilityItem.show()` | Print container content
`ProbabilityItem.log([level])` | Log container content
`ProbabilityItem.filename` | Filename
`ProbabilityItem.label` | Label
`ProbabilityItem.probability` | Returns:	
`ProbabilityItem.id` | 唯一的项目（item）标识
`ProbabilityItem.get_list()` | 以指定顺序返回列表中的项目（item）值。

- **ProbabilityContainer**

    `dcase_util.containers.ProbabilityContainer`

类名 | 功能
---|---
`ProbabilityContainer(\*args, \*\*kwargs)` | 概率数据容器类，继承自ListDictContainer。
`ProbabilityContainer.show()` | Print container content
`ProbabilityContainer.log([level])` | Log container content
`ProbabilityContainer.load([filename])` | 来自分隔文本文件的加载概率列表（csv格式）
`ProbabilityContainer.save([filename, delimiter])` | Save content to csv file
`ProbabilityContainer.append(item)` | Append item to the meta data list
`ProbabilityContainer.unique_files` | Unique files
`ProbabilityContainer.unique_labels` | Unique labels
`ProbabilityContainer.filter([filename, ...])` | Filter content

### 1.9 MIxins
- ContainerMixin

    `dcase_util.containers.ContainerMixin`

类名 | 功能
---|---
`ContainerMixin(\*args, \*\*kwargs)` | Container mixin to give class basic container methods.
`ContainerMixin.show()` | Print container content
`ContainerMixin.log([level])` | Log container content

- FileMixin

    `dcase_util.containers.FileMixin`

类名 | 功能
---|---
`FileMixin(\*args, \*\*kwargs)` | File mixin to give class methods to load and store content.
`FileMixin.get_file_information()` | Get file information, filename
`FileMixin.detect_file_format([filename])` | Detect file format from extension
`FileMixin.validate_format()` | Validate file format
`FileMixin.exists()` | Checks that file exists
`FileMixin.empty()` | Check if file is empty
`FileMixin.delimiter([exclude_delimiters])` | Use csv.sniffer to guess delimiter for CSV file
`FileMixin.is_package([filename])` | Determine if the file is compressed package.

- PackageMixin

    `dcase_util.containers.PackageMixin`

类名 | 功能
---|---
`PackageMixin(\*args, \*\*kwargs)` | 打包mixin以提供处理压缩文件包的类基本方法。
`PackageMixin.package_password` | Package password
`PackageMixin.extract([overwrite, ...])` | 解压缩包

## 2. Data

数据处理的类

### 2.1 Buffers
`dcase_util.data.DataBuffer`

数据缓冲类，可用于存储与项目关联的数据和元数据。 项目数据通过项目键进行访问。当内部缓冲区被填满时，最旧的项目被替换。


类名 | 功能
---|---
`DataBuffer([size])` | 数据缓冲区（先进先出）
`DataBuffer.set(key[, data, meta])` | 将项目（item）插入缓冲区
`DataBuffer.get(key)` | 根据键获取项目（item）
`DataBuffer.clear()` | 清空缓冲区
`DataBuffer.count` | 缓冲区使用情况
`DataBuffer.full` | 缓冲区已满
`DataBuffer.key_exists(key)` | 检查键（key）是否存在于缓冲区中

### 2.2 Encoders

- BinaryMatrixEncoder

    `dcase_util.data.BinaryMatrixEncoder`

类名 | 功能
---|---
`BinaryMatrixEncoder([label_list，...])` | 二进制矩阵编码器基类
`BinaryMatrixEncoder.pad(length [，binary_matrix])` | 沿时间轴填充二进制矩阵
`BinaryMatrixEncoder.plot([binary_matrix，...])` | 可视化二进制矩阵和可选的同步数据矩阵。

- OneHotEncoder

    `dcase_util.data.OneHotEncoder`

类名 | 功能
---|---
`OneHotEncoder([label_list, time_resolution, ...])` | One hot encoder class
`OneHotEncoder.encode(label[, length_frames, ...])` | Generate one hot binary matrix


- ManyHotEncoder

    `dcase_util.data.ManyHotEncoder`

类名 | 功能
---|---
`ManyHotEncoder([label_list, ...])` | Many hot encoder class
`ManyHotEncoder.encode(label_list[, ...])` | Generate one hot binary matrix

- EventRollEncoder

    `dcase_util.data.EventRollEncoder`

类名 | 功能
---|---
`EventRollEncoder([label_list, ...])` | Event list encoder class
`EventRollEncoder.encode(metadata_container)` | Generate event roll from MetaDataContainer

### 2.3 Data manipulators
- Normalizer
    
    `dcase_util.data.Normalizer`    

类名 | 功能
---|---
`Normalizer([n, s1, s2, mean, std])` | 数据归一化器来累积数据统计
`Normalizer.log([level])` | 记录容器内容
`Normalizer.show()` | 打印容器内容
`Normalizer.load([filename])` | Load file
`Normalizer.save([filename])` | Save file
`Normalizer.mean` | Mean vector
`Normalizer.std` | 标准差矢量
`Normalizer.reset()` | 充值内部变量
`Normalizer.accumulate(data[, time_axis])` | 计算统计量
`Normalizer.finalize()` | 完成统计计算
`Normalizer.normalize(data)	` | 使用该类的内部统计量标准化特征矩阵

- RepositoryNormalizer

    `dcase_util.data.RepositoryNormalizer`

类名 | 功能
---|---
`RepositoryNormalizer([normalizer_dict, ...])` | Data repository normalizer
`RepositoryNormalizer.load(filename_dict)` | Load normalizers from disk.
`RepositoryNormalizer.normalize(data_repository)` | Normalize data repository

- Aggregator（聚合）
`dcase_util.data.Aggregator`

数据聚合器可用于处理窗口中的数据矩阵。 这个处理阶段可以用来通过计算它们的平均值和标准差来在特定的窗口长度内折叠数据，或者将该矩阵平坦化为单个向量。

支持的处理方法：

```
data_aggregator = dcase_util.data.Aggregator(
    recipe=['mean', 'std'],
    win_length_frames=10,
    hop_length_frames=1,
)

data_stacker = dcase_util.data.Stacker(recipe='mfcc')
data_repository = dcase_util.utils.Example.feature_repository()
data_matrix = data_stacker.stack(data_repository)
data_matrix = data_aggregator.aggregate(data_matrix)
```

类名 | 功能
---|---
`Aggregator([win_length_frames, ...])` | Data aggregator
`Aggregator.log([level])` | Log container content
`Aggregator.show()` | Print container content
`Aggregator.load([filename])` | Load file
`Aggregator.save([filename])` | Save file
`Aggregator.aggregate([data])` | Aggregate data

- Sequencer

    `dcase_util.data.Sequencer`

    Sequencer类将数据矩阵处理成序列（图像）。 序列可以重叠，并且可以在调用之间改变排序网格（移位）。

    类名 | 功能
    ---|---
    `Sequencer([frames, hop_length_frames, ...])` | Data sequencer
    `Sequencer.log([level])` | Log container content
    `Sequencer.show()` | Print container content
    `Sequencer.load([filename])` | Load file
    `Sequencer.save([filename])	` | Save file
    `Sequencer.sequence([data])` | Make sequences
    `Sequencer.increase_shifting([shift_step])` | Increase temporal shifting


- Stacker

    `dcase_util.data.Stacker`

数据堆叠类。 Class使用矢量配方和DataRepository，并创建适当的数据矩阵。

矢量方法（Vector recipe）


使用recipe，您可以选择全矩阵，只选择开始和结束索引的一部分，或从中选择单个行。

例子：

```
[
 {
    'method': 'mfcc',
 },
 {
    'method': 'mfcc_delta'
    'vector-index: {
        'channel': 0,
        'start': 1,
        'end': 17,
        'full': False,
        'selection': False,
    }
  },
 {
    'method': 'mfcc_acceleration',
    'vector-index: {
        'channel': 0,
        'full': False,
        'selection': True,
        'vector': [2, 4, 6]
    }
 }
]
```
> 请参阅dcase_util.utils.VectorRecipeParser如何方便地使用配方字符串来生成上述数据结构。


类名 | 功能
---|---
`Stacker([recipe, hop])` | Data stacker
`Stacker.log([level])` | Log container content
`Stacker.show()` | Print container content
`Stacker.load([filename])` | Load file
`Stacker.save([filename])` | Save file
`Stacker.stack(repository)` | Vector creation based on recipe

- Selector
 
   `dcase_util.data.Selector`，数据选择类

类名 | 功能
---|---
`Selector(\*\*kwargs)` | Data selector
`Selector.log([level])` | 	Log container content
`Selector.show()` | Print container content
`Selector.load([filename])` | Load file
`Selector.save([filename])` | Save file
`Selector.select(data[, selection_events])` | Selecting feature repository with given events

- Masker

    `dcase_util.data.Masker`，数据屏蔽（筛选）类。

类名 | 功能
---|---
`Masker(\*\*kwargs)` | Data masker
`Masker.log([level])` | Log container content
`Masker.show()` | Print container content
`Masker.load([filename])` | Load file
`Masker.save([filename])` | Save file
`Masker.mask(data[, mask_events])` | Masking feature repository with given events

### 2.4 Probability

**ProbabilityEncoder**，`dcase_util.data.ProbabilityEncoder`

类名 | 功能
---|---
`ProbabilityEncoder([label_list])` | Constructor
`ProbabilityEncoder.log([level])` | Log container content
`ProbabilityEncoder.show()` | Print container content
`ProbabilityEncoder.load([filename])` | Load file
`ProbabilityEncoder.save([filename])` | Save file
`ProbabilityEncoder.collapse_probabilities(...)` | 	Collapse probabilities
`ProbabilityEncoder.collapse_probabilities_windowed(...)` | Collapse probabilities in windows
`ProbabilityEncoder.binarization(probabilities)` | Binarization

### 2.5 Eecisions
**DecisionEncoder**，`dcase_util.data.DecisionEncoder`

类名 | 功能
---|---
`DecisionEncoder([label_list])` | 构造器
`DecisionEncoder.log([level])` | Log container content
`DecisionEncoder.show()` | Print container content
`DecisionEncoder.load([filename])` | Load file
`DecisionEncoder.save([filename])` | Save file
`DecisionEncoder.majority_vote(frame_decisions)` | 多票制
`DecisionEncoder.find_contiguous_regions(...)` | 从具有布尔值的numpy.array找到连续区域。
`DecisionEncoder.process_activity(...[, operator])` | 处理动态数组（二进制）

## 3. Datasets

### 3.1 Dataset
`dcase_util.datasets.Dataset`这是基类，所有专用数据集都是从它继承而来的。基类本身不能使用。

用法示例：

```
# Create class
dataset = dcase_util.datasets.TUTAcousticScenes_2017_DevelopmentSet(data_path='data')
# Initialize dataset, this will make sure dataset is downloaded, packages are extracted,
# and needed meta files are created
dataset.initialize()
# Show meta data
dataset.meta.show()
# Get all evaluation setup folds
folds = dataset.folds()
# Get all evaluation setup folds
train_data_fold1 = dataset.train(fold=folds[0])
test_data_fold1 = dataset.test(fold=folds[0])
```

语法格式 | 功能
---|---
`Dataset([name, storage_name, data_path, ...])` | 数据集基类
`Dataset.initialize()` | 数据集基类
`Dataset.download_packages()` | 通过互联网将数据集包下载到本地路径
`Dataset.extract_packages()` | 提取数据集包
`Dataset.prepare()` | 为使用准备数据集
`Dataset.process_meta_item(item[, absolute_path])` | 处理单个元数据项目
`Dataset.check_filelist()` | 从文件列表生成哈希，并检查它是否与保存在filelist.hash中的哈希匹配
`Dataset.show()` | 打印数据集信息
`Dataset.log()` | 记录数据集信息
`Dataset.load()` | 将数据集元数据和交叉验证集加载到容器中
`Dataset.load_meta()` | 将元数据添加到容器中
`Dataset.load_crossvalidation_data()` | 将交叉验证加载到容器中
`Dataset.audio_files` | 获取数据集中的所有音频文件
`Dataset.audio_file_count` | 获取数据集中音频文件的数量
`Dataset.meta` | 获取数据集的元数据。
`Dataset.meta_count` | 元数据项的数量
`Dataset.error_meta` | 获取数据集的音频错误元数据
`Dataset.error_meta_count` | 错误元数据项的数量
`Dataset.folds([mode])` | fold ID列表
`Dataset.fold_count` | 评估设置中的fold次数
`Dataset.evaluation_setup_filename([...])` | 评估设置文件名生成
`Dataset.train([fold, absolute_paths])` | 训练列表
`Dataset.test([fold, absolute_paths])` | 测试列表
`Dataset.eval([fold, absolute_paths])` | 评估列表
`Dataset.train_files([fold, absolute_paths])` | 训练文件列表
`Dataset.test_files([fold, absolute_paths])` | 测试文件列表
`Dataset.eval_files([fold, absolute_paths])` | 评估文件列表
`Dataset.validation_split([fold, split_type, ...])` | 验证文件列表
`Dataset.validation_files_dataset([fold, verbose])` | 由数据集提供的验证文件列表
`Dataset.validation_files_random([fold, ...])` | 从训练集中随机选择的验证文件列表。
`Dataset.validation_files_balanced([fold, ...])` | 在保持数据平衡的同时随机选择验证文件列表
`Dataset.scene_labels()` | 元数据中唯一场景标签的列表
`Dataset.scene_label_count()` | 元数据中唯一场景标签的数量
`Dataset.event_labels(\*\*kwargs)` | 元数据中的唯一事件标签列表
`Dataset.event_label_count(\*\*kwargs)` | 元数据中的唯一事件标签列表
`Dataset.tags()` | 元数据中唯一音频标签的列表
`Dataset.tag_count()` | 元数据中唯一音频标签的数量
`Dataset.file_meta(filename)` | 给定文件的元数据
`Dataset.file_error_meta(filename)` | 给定文件的错误元数据
`Dataset.file_features(filename)` | 预先计算给定文件的声学特征
`Dataset.relative_to_absolute_path(path)` | 将相对路径转换为绝对路径
`Dataset.absolute_to_relative_path(path)` | 将绝对路径转换为相对路径
`Dataset.dataset_bytes()` | 数据集的总下载大小（以字节为单位）
`Dataset.dataset_size_string()` | 字符串中数据集的总下载大小
`Dataset.dataset_size_on_disk()` | 当前存储在本地的数据集的总大小


### 3.2 AcousticScenesDataset

`dcase_util.datasets.AcousticSceneDataset`，格式如下：

`AcousticSceneDataset(\*args, \*\*kwargs)`

继承自`AcousticSceneDataset`的几个类：

类名 | 功能
---|---
`TUTAcousticScenes_2017_DevelopmentSet([...])` | TUT Acoustic scenes 2017 开发数据集
`TUTAcousticScenes_2017_EvaluationSet([...])` | TUT Acoustic scenes 2017 评估数据集
`TUTAcousticScenes_2016_DevelopmentSet([...])` | TUT Acoustic scenes 2016 开发数据集
`TUTAcousticScenes_2016_EvaluationSet([...])` | TUT Acoustic scenes 2016 评估数据集

### 3.3 SoundEventDataset

`dcase_util.datasets.SoundEventDataset`

方法 | 功能
---|---
`SoundEventDataset(\*args, \*\*kwargs)` | 
`SoundEventDataset.event_label_count([...])` | Number of unique scene labels in the meta data.
`SoundEventDataset.event_labels([scene_label])` | List of unique event labels in the meta data.
`SoundEventDataset.train([fold, ...])` | List of training items.
`SoundEventDataset.test([fold, ...])` | List of testing items.


继承自`SoundEventDataset`的几个类：

类名 | 功能
---|---
`TUTRareSoundEvents_2017_DevelopmentSet([...])` | TUT Acoustic scenes 2017 development dataset
`TUTRareSoundEvents_2017_EvaluationSet([...])` | TUT Acoustic scenes 2017 evaluation dataset
`TUTSoundEvents_2017_DevelopmentSet([...])` | TUT Sound events 2017 development dataset
`TUTSoundEvents_2017_EvaluationSet([...])` | TUT Sound events 2017 evaluation dataset
`TUTSoundEvents_2016_DevelopmentSet([...])` | TUT Sound events 2016 development dataset
`TUTSoundEvents_2016_EvaluationSet([...])` | TUT Sound events 2016 evaluation dataset
`TUT_SED_Synthetic_2016([storage_name, ...])` | TUT SED Synthetic 2016

### 3.4 AudioTaggingDataset

`dcase_util.datasets.AudioTaggingDataset`

语法格式：`AudioTaggingDataset(\*args, \*\*kwargs)`


类名 | 功能
---|---
`DCASE2017_Task4tagging_DevelopmentSet([...])` | DCASE 2017 Large-scale weakly supervised sound event detection for smart cars
`DCASE2017_Task4tagging_EvaluationSet([...])` | DCASE 2017 Large-scale weakly supervised sound event detection for smart cars
`CHiMEHome_DomesticAudioTag_DevelopmentSet([...])` | Constructor

## 4. Decorators（修饰符）
用于修饰函数的辅助类

### RunOnce
`dcase_util.decorators.RunOnce`

`RunOnce(f)	Decorator` 类只允许执行一次

## 5. Features
提取特征的类
- FeatureExtractor（基本特征提取）

`dcase_util.features.FeatureExtractor`，特征提取基本类，语法格式：`FeatureExtractor([fs, win_length_samples, ...])`

```
class dcase_util.features.FeatureExtractor(fs=44100, win_length_samples=None, hop_length_samples=None, win_length_seconds=0.04, hop_length_seconds=0.02, **kwargs)
```

- SpectralFeatureExtractor
`dcase_util.features.SpectralFeatureExtractor`

类名 | 功能
---|---
`SpectralFeatureExtractor([spectrogram_type, ...])` | 特殊特征提取基础类
`SpectralFeatureExtractor.get_window_function(n)` | 窗函数
`SpectralFeatureExtractor.get_spectrogram(y)` | 谱图

- MelExtractor（Mel）
`dcase_util.features.MelExtractor`

类名 | 功能
---|---
`MelExtractor([fs, win_length_samples, ...])` | 梅尔带能量特征提取类
`MelExtractor.extract(y)` | 提取音频信号的特征

- MfccStaticExtractor（MFCC）
`dcase_util.features.MfccStaticExtractor`

类名 | 功能
---|---
`MfccStaticExtractor([fs, ...])` | 用于提取静态MFCC功能的特征提取器类
`MfccStaticExtractor.extract(y)` | 提取音频信号的特征

- MfccDeltaExtractor（MFCC一阶导）

`dcase_util.features.MfccDeltaExtractor`

类名 | 功能
---|---
`MfccDeltaExtractor([fs, win_length_samples, ...])` | MFCC一阶导
`MfccDeltaExtractor.extract(y)` | 提取音频信号的特征

- MfccAccelerationExtractor（MFCC二阶导）
`dcase_util.features.MfccAccelerationExtractor`

类名 | 功能
---|---
`MfccAccelerationExtractor([fs, ...])` | （MFCC二阶导 MFCC acceleration features
`MfccAccelerationExtractor.extract(y)` | 提取音频信号的特征

- ZeroCrossingRateExtractor（过零率）
`dcase_util.features.ZeroCrossingRateExtractor`

类名 | 功能
---|---
`ZeroCrossingRateExtractor([fs, ...])` | 过零率
`ZeroCrossingRateExtractor.extract(y)` | 提取音频信号的特征

- RMSEnergyExtractor（均方根能量特征）
`dcase_util.features.RMSEnergyExtractor`

类名 | 功能
---|---
`RMSEnergyExtractor([fs, win_length_samples, ...])` | 均方根能量特征
`RMSEnergyExtractor.extract(y)` | 提取音频信号的特征

- SpectralCentroidExtractor（光谱质心）
`dcase_util.features.SpectralCentroidExtractor`

类名 | 功能
---|---
`SpectralCentroidExtractor([fs, ...])` | 光谱质心
`SpectralCentroidExtractor.extract(y)` | 提取音频信号的特征

## 6. Files

### 6.1 File
`dcase_util.files.File`，通用的文件类：

类名 | 功能
---|---
`File(\*args, \*\*kwargs)` | 通用类
`File.load([filename])` | 加载文件
`File.save(data[, filename])` | 保存文件
`File.get_file_information()` | 得到文件信息、文件名
`File.detect_file_format([filename])` | 根据扩展检测文件格式
`File.validate_format()` | 验证文件格式有效性
`File.exists()` | 检查文件是否存在
`File.empty()` | 检查文件是否为空
`File.delimiter([exclude_delimiters])` | 使用csv.sniffer猜测CSV文件的分隔符
`File.is_package([filename])` | 确定文件是否为压缩包

### 6.2 FileLock
`dcase_util.files.FileLock`，简单的基于文件的锁定类。

类名 | 功能
---|---
`FileLock(filename[, timeout, ...])` | 简单的基于文件的锁定类
`FileLock.lock()` | 锁定文件
`FileLock.release()` | 释放文件锁
`FileLock.expired` | 检查比指定超时更早的锁定文件
`FileLock.is_locked` | 检查锁定文件是否存在
`FileLock.touch()` | 使用当前时间戳创建锁定文件

### 6.3 remotefile
`dcase_util.files.RemoteFile`远程文件处理类。

类名 | 功能
---|---
`RemoteFile([filename，content_type，...])` | 远程文件类
`RemoteFile.download()` | 下载远程文件并将其保存为本地文件。
`RemoteFile.is_content_type(content_type)` | 检查该文件是否包含给定类型的内容
`RemoteFile.local_md5` | 本地文件的校验和。
`RemoteFile.local_modified` | 修改本地文件的时间戳。
`RemoteFile.local_bytes` | 本地文件的文件大小(以字节为单位)。
`RemoteFile.local_size_string()` | 以可读形式存在的本地文件的文件大小。
`RemoteFile.local_exists()` | 检查本地文件是否存在。
`RemoteFile.local_changed()` | 检查本地文件是否对应于远程文件(基于校验和或修改时间和文件大小)。
`RemoteFile.remote_file` | 指向远程文件的URL
`RemoteFile.remote_modified` | 远程文件的最后修改时间。
`RemoteFile.remote_bytes` | 远程文件的文件大小。
`RemoteFile.remote_status` | 远程文件的状态。
`RemoteFile.remote_size_string()` | 以人类可读形式的远程文件的文件大小。
`RemoteFile.remote_info()` | 获取有关删除文件(状态，大小，校验和，上次修改时间)的信息。
`RemoteFile.remote_exists()` | 检查远程文件是否存在(基于HTTP状态码)。


### 6.4 RemotePackage
`dcase_util.files.RemotePackage`，远程包处理类。

类名 | 功能
---|---
`RemotePackage([filename，content_type，...])` | 远程软件包类
`RemotePackage.download()` | 下载远程文件并将其保存为本地文件。
`RemotePackage.extract([覆盖，...])` | 解压缩包
`RemotePackage.package_password` | 包密码
`RemotePackage.is_content_type(content_type)` | 检查该文件是否包含给定类型的内容
`RemotePackage.local_md5` | 本地文件的校验和。
`RemotePackage.local_modified` | 修改本地文件的时间戳。
`RemotePackage.local_bytes` | 本地文件的文件大小(以字节为单位)。
`RemotePackage.local_size_string()` | 以可读形式存储本地文件的文件大小。
`RemotePackage.local_exists()` | 检查本地文件是否存在。
`RemotePackage.local_changed()` | 检查本地文件是否对应于远程文件(基于校验和或修改时间和文件大小)。
`RemotePackage.remote_file` | 指向远程文件的URL
`RemotePackage.remote_modified` | 远程文件的最后修改时间。
`RemotePackage.remote_bytes` | 远程文件的文件大小。
`RemotePackage.remote_status` | 远程文件的状态。
`RemotePackage.remote_size_string()` | 以可读形式显示远程文件的文件大小。
`RemotePackage.remote_info()` | 获取有关删除文件(状态，大小，校验和，上次修改时间)的信息。
`RemotePackage.remote_exists()` | 检查远程文件是否存在(基于HTTP状态码)。

### 6.5 Serializer
`dcase_utils.files.Serializer`，数据序列化类

类名 | 功能
---|---
`Serializer` | 数据序列化类
`Serializer.load_yaml(filename)` | 加载YAML文件
`Serializer.load_cpickle(filename)` | 加载CPICKLE文件
`Serializer.load_json(filename)` | 加载JSON文件
`Serializer.load_msgpack(filename)` | Load MSGPACK file
`Serializer.load_marshal(filename)` | Load MARSHAL file
`Serializer.save_yaml(filename, data)` | Save data into YAML file
`Serializer.save_cpickle(filename, data)` | Save data into CPICKLE file
`Serializer.save_json(filename, data)` | Save data into JSON file
`Serializer.save_msgpack(filename, data)` | Save data into MSGPACK file
`Serializer.save_marshal(filename, data)` | Save data into MARSHAL file


## 7. Keras utilities
使用Keras深度学习库的单元。

## 7.1 Model
`dcase_util.keras.model`。*

类名 | 功能
---|---
`create_sequential_model(model_parameter_list)` | 创建顺序Keras模型
`model_summary_string(keras_model)` | 字符串中的模型摘要，类似于Keras模型摘要函数。


## 7.2 Callbacks
用法示例如何将外部度量与dcase_util提供的Callback类一起使用：

```
epochs = 100
batch_size = 256
loss = 'categorical_crossentropy'
metrics =  ['categorical_accuracy']
processing_interval = 1
manual_update = True
external_metric_labels={'ER': 'Error rate'}

callback_list = [
        dcase_util.keras.ProgressLoggerCallback(
            epochs=epochs,
            metric=metrics,
            loss=loss,
            manual_update=manual_update,
            manual_update_interval=processing_interval,
            external_metric_labels=external_metric_labels
        ),
        dcase_util.keras.ProgressPlotterCallback(
            epochs=epochs,
            metric=metrics,
            save=False,
            manual_update=manual_update,
            manual_update_interval=processing_interval,
            external_metric_labels=external_metric_labels
        ),
        dcase_util.keras.StopperCallback(
            epochs=epochs,
            monitor=metric[0],
            manual_update=manual_update,
        ),
        dcase_util.keras.StasherCallback(
            epochs=epochs,
            monitor=metric[0],
            manual_update=manual_update,
        )
    ]

for epoch_start in range(0, epochs, processing_interval):
    epoch_end = epoch_start + processing_interval

    # Make sure we have only specified amount of epochs
    if epoch_end > epochs:
        epoch_end = epochs

    # Train model
    keras_model.fit(
        x=training_X,
        y=training_Y,
        validation_data=(validation_X, validation_Y),
        callbacks=callback_list,
        verbose=0,
        initial_epoch=epoch_start,
        epochs=epoch_end,
        batch_size=batch_size,
        shuffle=True
    )
    # Calculate external metrics
    ER = 0.0

    # Inject external metric values to the callbacks
    for callback in callback_list:
        if hasattr(callback, 'set_external_metric_value'):
            callback.set_external_metric_value(
                metric_label='ER',
                metric_value=ER
            )

    # Manually update callbacks
    for callback in callback_list:
        if hasattr(callback, 'update'):
            callback.update()

    # Check we need to stop training
    stop_training = False
    for callback in callback_list:
        if hasattr(callback, 'stop'):
            if callback.stop():
                stop_training = True

    if stop_training:
        # Stop the training loop
        break
```


- ProgressLoggerCallback
`dcase_util.keras.ProgressLoggerCallback`，Keras回调用于存储tqdm进度条或日志记录界面的指标。 实现Keras回调API。

    此回调与标准的ProgbarLogger Keras回调非常相似，但它增加了对日志接口和外部度量（在Keras训练过程之外计算的度量）的支持。

类名 | 功能
---|---
`ProgressLoggerCallback([manual_update，...])` | Keras回调在日志界面中显示指标。

- ProgressPlotterCallback
`dcase_util.keras.ProgressPlotterCallback`,keras回调在培训过程中计划进度并将最终进展保存到数字中。 实现Keras回调API。

类名 | 功能
---|---
`ProgressPlotterCallback([epochs，...])` | Keras回调在训练过程中绘制进度并将最终进度保存到图中。

- StopperCallback
`dcase_util.keras.StopperCallback`，keras回调停止训练时，改善没有在规定数量的时代看到。 实现Keras回调API。

    此回调与标准的`EarlyStopping` Keras回调非常相似，但它增加了对外部度量标准（在Keras培训过程之外计算的度量）的支持。

类名 | 功能
---|---
`StopperCallback([epochs，manual_update，...])` | Keras回调在特定时间段内没有发现改进时停止训练。

- StasherCallback

`dcase_util.keras.StasherCallback`，keras回调监测训练过程并存储最佳模型。实现Keras回调API。

该回调与标准的ModelCheckpoint Keras回调非常相似，但它增加了对外部度量（在Keras培训过程之外计算的度量）的支持。

类名 | 功能
---|---
`StasherCallback([epochs，manual_update，...])` | Keras回调监视训练过程并存储最佳模型。

- BaseCallback

`dcase_util.keras.BaseCallback`

类名 | 功能
---|---
`BaseCallback([epochs, manual_update, ...])` | 	回调基础类


## 7.3 Utils
`dcase_util.keras.utils`. *

类名 | 功能
---|---
`setup_keras` | 仅执行一次的修饰类

## 8. Processors（数据处理器类）

### 8.1 Processing chain
- ProcessingChainItem
`dcase_util.processors.ProcessingChainItem`

类名 | 功能
---|---
`ProcessingChainItem(\*args, \*\*kwargs)` | 

- ProcessingChain
`dcase_util.processors.ProcessingChain`

类名 | 功能
---|---
`ProcessingChain(\*args, \*\*kwargs)` | 
`ProcessingChain.show_chain()` | 显示链信息
`ProcessingChain.log_chain([level])` | 记录链信息
`ProcessingChain.push_processor(processor_name)	` | 将处理器项目推送到链中。
`ProcessingChain.process([data])` | 用处理链处理数据
`ProcessingChain.call_method(method_name[, ...])` | 调用处理链项目中的类方法
`ProcessingChain.processor_exists(processor_name)` | 检查处理器是否存在于链中
`ProcessingChain.processor_class_reference(...)` | 处理器类的引用
`ProcessingChain.processor_class(...)` | 初始化处理器类

### 8.2 Audio

- AudioReadingProcessor
`dcase_util.processors.AudioReadingProcessor`

类名 | 功能
---|---
`AudioReadingProcessor([data, fs, ...])` | 	构造函数
`AudioReadingProcessor.process([data, ...])` | 	音频读取

- MonoAudioReadingProcessor
`dcase_util.processors.MonoAudioReadingProcessor`

类名 | 功能
---|---
MonoAudioReadingProcessor([data, fs, ...]) | 	构造函数
MonoAudioReadingProcessor.process([data, ...]) | 音频读取

### 8.3 Data
- AggregationProcessor
`dcase_util.processors.AggregationProcessor`

类名 | 功能
---|---
`AggregationProcessor([win_length_frames, ...])` | Data aggregation processor
`AggregationProcessor.process([data])` | Process features

- SequencingProcessor
`dcase_util.processors.SequencingProcessor`

类名 | 功能
---|---
`SequencingProcessor([frames, ...])` | Data sequencing processor
`SequencingProcessor.process([data])` | Process

- NormalizationProcessor
`dcase_util.processors.NormalizationProcessor`

类名 | 功能
---|---
`NormalizationProcessor([n, s1, s2, mean, std])` | Data normalizer to accumulate data statistics
`NormalizationProcessor.process([data])` | Normalize feature matrix with internal statistics of the class

- RepositoryNormalizationProcessor
`dcase_util.processors.RepositoryNormalizationProcessor`

类名 | 功能
---|---
`RepositoryNormalizationProcessor([parameters])` | Data normalizer to accumulate data statistics inside repository
`RepositoryNormalizationProcessor.process([data])` | Normalize data repository with internal statistics

- StackingProcessor
`dcase_util.processors.StackingProcessor`

类名 | 功能
---|---
`StackingProcessor([recipe, hop])` | Data stacking processor
`StackingProcessor.process([data])` | Vector creation based on recipe

- OneHotEncodingProcessor
`dcase_util.processors.OneHotEncodingProcessor`

类名 | 功能
---|---
`OneHotEncodingProcessor([label_list, ...])` | Event roll encoding processor
`OneHotEncodingProcessor.process([data, ...])` | Encode metadata

- ManyHotEncodingProcessor
`dcase_util.processors.ManyHotEncodingProcessor`

类名 | 功能
---|---
`ManyHotEncodingProcessor([label_list, ...])` | Event roll encoding processor
`ManyHotEncodingProcessor.process([data, ...])` | Encode metadata

- EventRollEncodingProcessor
`dcase_util.processors.EventRollEncodingProcessor`

类名 | 功能
---|---
`EventRollEncodingProcessor([label_list, ...])` | Event roll encoding processor
`EventRollEncodingProcessor.process([data])` | Encode metadata

### 8.4 Features

- **RepositoryFeatureExtractorProcessor**
`dcase_util.processors.RepositoryFeatureExtractorProcessor`

类名 | 功能
---|---
`RepositoryFeatureExtractorProcessor([parameters])` | 构造器
`RepositoryFeatureExtractorProcessor.process([data])` | 提取特征

- **FeatureExtractorProcessor**
`dcase_util.processors.FeatureExtractorProcessor`

类名 | 功能
---|---
`FeatureExtractorProcessor(\*args, \*\*kwargs)` | 构造器
`FeatureExtractorProcessor.process([data])` | 提取特征

- **MelExtractorProcessor（Mel能量）**
`dcase_util.processors.MelExtractorProcessor`

类名 | 功能
---|---
`MelExtractorProcessor([fs, ...])` | 构造器
`MelExtractorProcessor.process([data])` | 提取特征

- **MfccStaticExtractorProcessor（MFCC）**
`dcase_util.processors.MfccStaticExtractorProcessor`

类名 | 功能
---|---
`MfccStaticExtractorProcessor([fs, ...])` | 构造器
`MfccStaticExtractorProcessor.process([data])` | 提取特征

- **MfccDeltaExtractorProcessor（MFCC一阶导）**
`dcase_util.processors.MfccDeltaExtractorProcessor`

类名 | 功能
---|---
`MfccDeltaExtractorProcessor([fs, ...])` | 构造器
`MfccDeltaExtractorProcessor.process([data])` | 提取特征

- **MfccAccelerationExtractorProcessor（MFCC二阶导）**
`dcase_util.processors.MfccAccelerationExtractorProcessor`

类名 | 功能
---|---
`MfccAccelerationExtractorProcessor([fs, ...])` | 构造器
`MfccAccelerationExtractorProcessor.process([data])` | 提取特征

- **ZeroCrossingRateExtractorProcessor（过零率）**
`dcase_util.processors.ZeroCrossingRateExtractorProcessor`

类名 | 功能
---|---
`ZeroCrossingRateExtractorProcessor([fs, ...])` | 构造器
`ZeroCrossingRateExtractorProcessor.process([data])` | 提取特征

- **RMSEnergyExtractorProcessor（均方根能量）**
`dcase_util.processors.RMSEnergyExtractorProcessor`

类名 | 功能
---|---
`RMSEnergyExtractorProcessor([fs, ...])` | 构造器
`RMSEnergyExtractorProcessor.process([data])` | 提取特征

- **SpectralCentroidExtractorProcessor（光谱质心）**
`dcase_util.processors.SpectralCentroidExtractorProcessor`

类名 | 功能
---|---
`SpectralCentroidExtractorProcessor([fs, ...])` | 构造器
`SpectralCentroidExtractorProcessor.process([data])` | 提取特征

### 8.5 Metadata
**MetadataReadingProcessor**，`dcase_util.processors.MetadataReadingProcessor`

类名 | 功能
---|---
`MetadataReadingProcessor(\*args, \*\*kwargs)` | 构造器
`MetadataReadingProcessor.process([data, ...])` | 读取元数据

### 8.6 Mixin
**ProcessorMixin**，`dcase_util.processors.ProcessorMixin`

类名 | 功能
---|---
`ProcessorMixin(\*args, \*\*kwargs)` | 数据处理链单元混合
`ProcessorMixin.process([data])` | 处理数据
`ProcessorMixin.get_processing_chain_item()` | 使用当前处理器数据获取处理链项目
`ProcessorMixin.push_processing_chain_item(...)` | 推送加工链项目

## 9. User interfacing
用于轻型用户界面的实用程序类。

FancyLogger和FancyPrinter提供相同的API，唯一不同的是FancyLogger将输出到日志系统，FancyPrinter使用标准打印功能将输出打印到sys.stdout。 当需要以字符串形式输出时，FancyStringifier可用于一般情况。

### 9.1 FancyLogger
`dcase_util.ui.FancyLogger`

使用日志记录时，此类提供额外的格式。 如果在调用`FancyLogger时Python`日志记录尚未初始化，则首先调用`dcase_util.utils.setup_logging`。

用法示例：

```
ui = dcase_util.ui.FancyLogger()
ui.title('title')
ui.section_header('section_header')
ui.sub_header('sub_header')
ui.foot('foot')
ui.line('line', indent=2)
ui.line('line', indent=4)
ui.line('line', indent=6)

# Data row with field and value
ui.data('data field', 'value', 'unit')

# Horizontal separator
ui.sep()

# Table
ui.table(cell_data=[[1, 2, 3], [1, 2, 3]])

# Faster way to create output tables without collecting data into one data structure.
ui.row('Header1', 'Header2', widths=[10,20], types=['float2','str20'])
ui.row('-','-')
ui.row(10.21231, 'String text')
```

输出：

```
[I] title
[I] section_header
[I] ========================================
[I] === sub_header ===
[I]   foot
[I]
[I]   line
[I]     line
[I]       line
[I]   data field                        : value unit
[I] ========================================
[I] Col #0   Col #1
[I] ------   ------
[I]      1        1
[I]      2        2
[I]      3        3
[I]
[I]   Header1 | Header2           |
[I]   ------- | ----------------- |
[I]    10.21  | String text       |
```

类名 | 功能
---|---
`FancyLogger()` | Logger class
`FancyLogger.line([data, indent, level])` | Generic line logger
`FancyLogger.row(\*args, \*\*kwargs)	` | 
`FancyLogger.title(text[, level])` | Title, logged at info level
`FancyLogger.section_header(text[, indent, level])` | Section header, logged at info level
`FancyLogger.sub_header([text, indent, level])` | Sub header
`FancyLogger.foot([text, time, item_count, ...])` | Footer, logged at info level
`FancyLogger.data([field, value, unit, ...])` | Data line logger
`FancyLogger.sep([level, length, indent])` | Horizontal separator, logged at info level
`FancyLogger.table([cell_data, ...])` | Data table
`FancyLogger.info([text, indent])` | Info line logger
`FancyLogger.debug([text, indent])` | Debug line logger
`FancyLogger.error([text, indent])` | Error line logger

### 9.2 FancyPrinter
`dcase_util.processors.FancyPrinter`

该类向控制台提供统一格式的状态打印。

用例：

```
ui = dcase_util.ui.FancyPrinter()
ui.title('title')
ui.section_header('section_header')
ui.sub_header('sub_header')
ui.foot('foot')
ui.line('line', indent=2)
ui.line('line', indent=4)
ui.line('line', indent=6)

# Data row with field and value
ui.data('data field', 'value', 'unit')

# Horizontal separator
ui.sep()

# Table
ui.table(cell_data=[[1, 2, 3], [1, 2, 3]])

# Faster way to create output tables without collecting data into one data structure.
ui.row('Header1', 'Header2', widths=[10,20], types=['float2','str20'])
ui.row('-','-')
ui.row(10.21231, 'String text')
```
输出：

```
title
section_header
========================================
=== sub_header ===
  foot

  line
    line
      line
  data field                        : value unit
========================================
Col #0   Col #1
------   ------
     1        1
     2        2
     3        3

  Header1 | Header2           |
  ------- | ----------------- |
   10.21  | String text       |
```

类名 | 功能
---|---
`FancyPrinter([colors])` | Printer class
`FancyPrinter.line([data, indent, level])` | Generic line logger
`FancyPrinter.row(\*args, \*\*kwargs)` | 	
`FancyPrinter.title(text[, level])` | Title, logged at info level
`FancyPrinter.section_header(text[, indent, ...])` | Section header, logged at info level
`FancyPrinter.sub_header([text, indent, level])` | Sub header
`FancyPrinter.foot([text, time, item_count, ...])` | Footer, logged at info level
`FancyPrinter.data([field, value, unit, ...])` | Data line logger
`FancyPrinter.sep([level, length, indent])` | Horizontal separator, logged at info level
`FancyPrinter.table([cell_data, ...])` | Data table
`FancyPrinter.info([text, indent])` | Info line logger
`FancyPrinter.debug([text, indent])` | Debug line logger
`FancyPrinter.error([text, indent])` | Error line logger


### 9.3 FancyStringifier
`dcase_util.processors.FancyStringifier`

这个类可以用来产生统一格式的输出字符串。

类名 | 功能
---|---
`FancyStringifier()` | Fancy UI
`FancyStringifier.title(text)` | Title
`FancyStringifier.section_header(text[, indent])` | Section header
`FancyStringifier.sub_header([text, indent])` | Sub header
`FancyStringifier.foot([text, time, ...])` | Footer
`FancyStringifier.line([field, indent])` | Line
`FancyStringifier.formatted_value(value[, ...])` | Format value into string.
`FancyStringifier.data([field, value, unit, ...])` | Data line
`FancyStringifier.sep([length, indent])` | Horizontal separator
`FancyStringifier.table([cell_data, ...])` | Data table
`FancyStringifier.row(\*args, \*\*kwargs)` | Table row
`FancyStringifier.class_name(class_name)` | Class name

## 10. Units（单元函数与类）
### 10.1 General function
`dcase_util.utils`. *

类名 | 功能
---|---
`get_class_inheritors(klass)` | 得到从给定的类中继承的所有类
`get_byte_string(num_bytes[, show_bytes])` | 根据语言环境和IEC二进制前缀输出字节数
`check_pkg_resources(package_requirement[, ...])` | 
`is_int(value)`	 | 
`is_float(value)` | 

- SuppressStdoutAndStderr
`dcase_util.utils.SuppressStdoutAndStderr`

类名 | 功能
---|---
`SuppressStdoutAndStderr()` | Context manager to suppress STDOUT and STDERR

- VectorRecipeParser
`dcase_util.utils.VectorRecipeParser`

类名 | 功能
---|---
`VectorRecipeParser([delimiters, default_stream])` | 	

### 10.2 File
`dcase_util.utils`. *

类名 | 功能
---|---
`argument_file_exists(filename)` | 参数文件检查器
`filelist_exists(filelist)` | 检查列表中的所有文件是否存在
`posix_path(path)` | 检查列表中的所有文件是否存在

- Path
`dcase_util.utils.Path`

类名 | 功能
---|---
`Path([path])` | 路径的单元
`Path.posix([path])` | 将路径转换为POSIX格式
`Path.posix_to_nt([path])` | 将posix格式的路径转换为nt
`Path.file_list([path, recursive, ...])` | 获取文件列表
`Path.exists([path])` | 检查路径是否存在
`Path.file_count([path])` | 给定路径下的文件数量包括子目录
`Path.size_bytes([path])` | 给定路径下所有文件的总字节数
`Path.size_string([path, show_bytes])` | 给定路径下的所有文件的总数据大小以可读形式返回
`Path.makedirs([path])` | 创建给定的路径
`Path.create(paths)` | 创建给定的路径


- ApplicationPaths
`dcase_util.utils.ApplicationPaths`

类名 | 功能
---|---
`ApplicationPaths([parameter_container])` | 应用程序路径的实用工具类，根据参数散列自动生成路径
`ApplicationPaths.generate(path_base, structure)` | 生成应用程序路径并将参数散列包含到路径中
`ApplicationPaths.directory_name(prefix, ...)` | 生成目录名称
`ApplicationPaths.save_parameters_to_path(...)` | 将参数保存到每个应用程序子目录
`ApplicationPaths.construct_path(path_parts)` | 基于路径部分生成所有组合

- FileFormat
`dcase_util.utils.FileFormat`

类名 | 功能
---|---
`FileFormat	` | 
`FileFormat.detect(filename[, ...])` | 检测文件格式
`FileFormat.detect_based_on_filename(filename)` | 基于文件名检测文件格式
`FileFormat.detect_based_on_content(filename)` | 使用python-magic检测基于内容的文件格式。
`FileFormat.validate_label(label)` | 根据此类已知的标签验证文件格式标签

### 10.3 Hash

类名 | 功能
---|---
`get_parameter_hash(params)	` | 获取给定参数字典的唯一哈希字符串（md5）
`get_file_hash(filename)	` | 获取给定文件的唯一哈希字符串（md5）

### 10.4 Logging

类名 | 功能
---|---
`setup_logging	` | 修饰器类只允许执行一次

- DisableLogger
`dcase_util.utils.DisableLogger`

类名 | 功能
---|---
`DisableLogger` | 

### 10.5 Math
`dcase_util.utils.SimpleMathStringEvaluator`

- SimpleMathStringEvaluator

类名 | 功能
---|---
`SimpleMathStringEvaluator()` | 简单的数学字符串评估器
`SimpleMathStringEvaluator.eval(string)` | 评估字符串中的数学

### 10.6 Timer
`dcase_util.utils.Timer`

类名 | 功能
---|---
`Timer()` | 定时器类
`Timer.start()` | 启动计时器
`Timer.stop()` | 停止计时器
`Timer.elapsed()` | 返回自启动计时器以来的经过时间，以秒为单位
`Timer.get_string([elapsed])` | 以字符串格式获取已用时间

### 10.7 Validator

`dcase_util.utils.FieldValidator`

类名 | 功能
---|---
`FieldValidator	` | 
`FieldValidator.process(field)` | 测试字段
`FieldValidator.is_empty(field)` | 测试空字段
`FieldValidator.is_number(field)` | 测试数字字段
`FieldValidator.is_audiofile(field)` | 测试音频字段
`FieldValidator.is_list(field)` | 测试列表字段，有效分隔符[：;＃]
`FieldValidator.is_alpha(field[, length])` | 测试长度为1的alpha字段

### 10.8 Example

dcase_util.utils.Example

一些示例数据，便于测试和辅导。

类名 | 功能
---|---
`Example()` | 教程示例文件
`Example.audio_filename()` | 	
`Example.acoustic_scene_audio_filename()` | 
`Example.audio_container()` | 
`Example.event_metadata_container([filename])` | 	
`Example.scene_metadata_container([filename])` | 	
`Example.tag_metadata_container([filename])` | 
`Example.feature_container([filename])` | 
`Example.feature_repository([filename])` | 

