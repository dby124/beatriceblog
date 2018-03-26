---
title: dcase_unit教程
date: 2018-03-26 11:31:53
tags: [python]
categories: [DCASE]
---


关于音频场景识别的一个基于python的程序合集dcase_unit，是英文的，所以在此将其翻译成了中文，以便查阅。

[dcase_unit](https://dcase-repo.github.io/dcase_util)文档描述了为声场景和事件检测和分类（DCASE）创建的实用程序集合。这些实用程序最初是为DCASE挑战基线系统（2016和2017）创建的，并捆绑到独立库中以允许其在其他研究项目中重复使用。

这些实用程序的主要目标是简化研究代码，使其更易读，更易于维护。大多数实施的实用程序都与音频数据集相关：处理元数据和各种形式的其他结构化数据，并为各种来源的音频数据集提供标准化的使用API。

dcase_unit中包含的概念有：

- Dataset：一组音频信号和与之相关的参考标注。
- Container：类存储数据并基于要存储的数据类型，提供有意义且清晰的数据访问。
- Metadata：数据注释。
- Repository：容器来存储多个数据容器。
- Encoder：用于将数据从不同类型转换为另一类型。
- Processor：为处理数据提供统一API的类。
- ProcessingChain：在一个链中连接多个数据处理器，允许构建数据的复杂处理。

在搭建好了python环境之后，使用下列pip命令安装dcase_unit。
```
pip install dcase_util
```

下面是各个单元的使用教程

## 1.Container
该库提供数据container以简化工作流程。 这些container是从标准的Python container（例如对象，列表和字典）继承的，以使它们可以与其他工具和库一起使用。 这些数据的目的是为了包装数据，使用有用的方法来访问和操作数据，以及加载和存储数据。

### 1.1基本用法
- 四种从文件中加载内容的方式：

```
# 1
dict_container = dcase_util.containers.DictContainer(filename='test.yaml')
dict_container.load()

# 2
dict_container = dcase_util.containers.DictContainer()
dict_container.load(filename='test.yaml')

# 3
dict_container = dcase_util.containers.DictContainer(filename='test.yaml').load()

# 4
dict_container = dcase_util.containers.DictContainer().load(filename='test.yaml')
```
- 将内容保存到文件

```
dict_container.save(filename='test.yaml')
```
- 查看并在控制器中打印container内容

```
dict_container.show()
```
- 查看并在标准日志记录系统中打印

```
dict_container.log()
```
> 如果日志记录系统在调用之前未被初始化，那么将使用带默认参数的dcase_util.utils.setup_logging来初始化它。

### 1.2 字典
`dcase_util.containers.DictContainer`设计用于嵌套字典比标准字典数据container更容易一些。它允许通过所谓的虚线路径或路径部分列表访问嵌套字典中的字段。

- 用字典初始化container

```
dict_container = dcase_util.containers.DictContainer(
    {
        'test': {
            'field1': 1,
            'field2': 2,
        },
        'test2': 100,
        'test3': {
            'field1': {
                'fieldA': 1
            },
            'field2': {
                'fieldA': 1
            },
            'field3': {
                'fieldA': 1
            },
        }
    }
)
```
- 初始化container，从文件中加载内容

```
dict_container = dcase_util.containers.DictContainer().load(filename='test.yaml')
```
- 通过点路径(dotted path)访问字段：

```
# Field exists
value = dict_container.get_path('test.field1')

# Using wild card
values = dict_container.get_path('test3.*')

# Non existing field with default value
value = dict_container.get_path('test.fieldA', 'default_value')
```
- 通过路径部分列表访问字段

```
# Field exists
value = dict_container.get_path(['test', 'field1'])

# Non existing field with default value
value = dict_container.get_path(['test', 'fieldA'], 'default_value)
```
- 通过点路劲设置字段

```
dict_container.set_path('test.field2', 200)
```
- 获取嵌套字典中所有叶节点的点路径

```
dict_container.get_leaf_path_list()
```
- 获取嵌套字典中以'field'开头的所有叶节点的虚线路径

```
dict_container.get_leaf_path_list(target_field_startswith='field')
```
- 把container存成.yaml文件

```
dict_container.save(filename='test.yaml')
```
- 从.yaml文件中加载container内容

```
dict_container.load(filename='test.yaml')
```

### 1.3 字典列表
`dcase_util.containers.ListDictContainer`是用于存储`dcase_util.containers.DictContainer`的列表。

- 用字典列表初始化container

```
listdict_container = dcase_util.containers.ListDictContainer(
    [
        {'field1': 1, 'field2': 2},
        {'field1': 10, 'field2': 20},
        {'field1': 100, 'field2': 200},
    ]

)
```
- 根据键和值访问列表中的项目

```
print(listdict_container.search(key='field1', value=10))
# DictContainer
#   field1                            : 10
#   field2                            : 20

```

- 得到字典中特定字段的值

```
print(ld.get_field(field_name='field2'))
# [2, 20, 200]
```

### 1.4 Data Containers
- 其中三种是可用的几种数据容器类型：
    - `dcase_util.containers.DataArrayContainer`，数组数据的数据容器，内部数据存储在numpy.array中。（可用）
    - `dcase_util.containers.DataMatrix2DContainer`，二维数据矩阵的数据容器，内部数据存储在二维numpy.ndarray中。（可用）
    - case_util.containers.DataMatrix3DContainer`，用于三维数据矩阵的数据容器，内部数据存储在3-D numpy.ndarray中。（可用）
    - `dcase_util.containers.BinaryMatrixContainer`，用于二维二进制数据矩阵的数据容器，内部数据存储在二维numpy.ndarray中。（不可用）
- 用随机矩阵10x100初始化container，并将时间分辨率设置为20ms：

```
data_container = dcase_util.containers.DataMatrix2DContainer(
  data=numpy.random.rand(10,100),
  time_resolution=0.02
)
```
> 当存储例如声学特征时，时间分辨率对应于特征提取帧跳长。

- 直接访问数据矩阵：

```
print(data_container.data.shape)
# (10, 100)
```
- 显示container信息

```
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
> 该容器具有聚焦机制，可灵活捕捉数据矩阵的一部分。 可以根据时间进行对焦（如果时间分辨率已定义，则以秒为单位），或基于帧ID。

- 使用焦点在0.5秒和1.0秒之间获取部分数据：

```
print(data_container.set_focus(start_seconds=0.5, stop_seconds=1.0).get_focused().shape)
# (10, 25)
```
- 使用焦点获取第10帧和第50帧之间的零件数据：

```
print(data_container.set_focus(start=10, stop=50).get_focused().shape)
# (10, 40)
```

- 重置焦点并访问完整的数据矩阵：

```
data_container.reset_focus()
print(data_container.get_focused().shape)
# (10, 100)
```
- 访问帧1，2，10和30

```
data_container.get_frames(frame_ids=[1,2,10,30])
```
- 访问帧1-5，每列只有第一个值：

```
data_container.get_frames(frame_ids=[1,2,3,4,5], vector_ids=[0])
```
- 转置矩阵：

```
transposed_data = data_container.T
print(transposed_data.shape)
# (100, 10)
```
- 绘制数据：

```
data_container.plot()
```
> `dcase_util.containers.BinaryMatrixContainer`提供与`DataMatrix2DContainer`相同的用途，但是用于二进制内容。

### 1.5 存储库（Repositories）
`dcase_util.containers.DataRepository`和`dcase_util.containers.FeatureRepository`是可用于存储多个其他数据容器的容器。 存储库存储具有两个级别信息的数据：标签和流。 标签是更高级别的密钥，流是第二级。

例如，可以使用储存库来储存与相同音频信号有关的多个不同声学特征。 流ID可用于存储从不同音频通道提取的特征。 后面的功能可以使用提取器标签和流ID进行访问。

- 用数据初始化容器：

```
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
```
- 显示container信息:

```
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
```
- 访问存储库中的数据：

```
data_repository.get_container(label='label1',stream_id='stream1')
# {'data': 200}
```

- 设置数据

```
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

## 2. 音频
`dcase_util.containers.AudioContainer`是多声道音频的数据容器。它读取多种格式（WAV，FLAC，M4A，WEBM）并写入WAV和FLAC文件。 直接从Youtube下载音频内容也受支持。

### 2.1 创建container
- 创建双通道的音频container

```
audio_container = dcase_util.containers.AudioContainer(fs=44100)
t = numpy.linspace(0, 2, 2 * audio_container.fs, endpoint=False)
x1 = numpy.sin(220 * 2 * numpy.pi * t)
x2 = numpy.sin(440 * 2 * numpy.pi * t)
audio_container.data = numpy.vstack([x1, x2])
```
- 显示的container信息

```
# AudioContainer :: Class
#  Sampling rate                     : 44100
#     Channels                        : 2
#   Duration
#     Seconds                         : 2.00 sec
#     Milliseconds                    : 2000.00 ms
#     Samples                         : 88200 samples
```
### 2.2 加载和保存
- 加载
```
audio_container = dcase_util.containers.AudioContainer().load(
  filename=dcase_util.utils.Example.audio_filename()
)
```
- 显示的container信息

```
# AudioContainer :: Class
#  Filename                          : acoustic_scene.flac
#  Synced                            : Yes
#  Sampling rate                     : 44100
#    Channels                        : 2
#  Duration
#    Seconds                         : 10.00 sec
#    Milliseconds                    : 10000.02 ms
#    Samples                         : 441001 samples
```
- 从Youtube加载content 

```
audio_container = dcase_util.containers.AudioContainer().load_from_youtube(
  query_id='2ceUOv8A3FE',
  start=1,
  stop=5
)
```

### 2.3 焦点段（Focus segment）
container具有聚焦机制，可灵活捕捉部分音频数据，同时保持完整的音频信号不变。 可以根据时间进行聚焦（如果时间分辨率已定义，则以秒为单位），或基于样本ID。 可以对单声道或混音（单声道）频道进行聚焦。 音频容器内容可以通过冻结来替代焦点细分。

- 使用焦点在0.5秒和1.0秒之间获取部分数据：

```
print(audio_container.set_focus(start_seconds=0.5, stop_seconds=1.0).get_focused().shape)
# (2, 22050)
```
- 使用焦点从5秒开始持续2秒获取部分数据：

```
print(audio_container.set_focus(start_seconds=5, duration_seconds=2.0).get_focused().shape)
# (2, 88200)
```
- 使用焦点从5秒开始持续2秒获取部分数据，混合两个立体声声道：

```
print(audio_container.set_focus(start_seconds=5, duration_seconds=2.0, channel='mixdown').get_focused().shape)
# (88200,)
```
- 使用焦点从5秒开始2秒开始数取部分数据，在两个立体声通道左侧：

```
print(audio_container.set_focus(start_seconds=5, duration_seconds=2.0, channel='left').get_focused().shape)
# (88200,)
```
- 使用焦点从5秒开始2秒开始数取部分数据，秒音频通道（索引从0开始）：

```
print(audio_container.set_focus(start_seconds=5, duration_seconds=2.0, channel=1).get_focused().shape)
# (88200,)
```
- 使用焦点获取样本44100和88200之间的零件数据：

```
print(audio_container.set_focus(start=44100, stop=88200).get_focused().shape)
# (2, 44100)
```

- 重置焦点并访问完整的数据矩阵：

```
audio_container.reset_focus()
print(audio_container.get_focused().shape)
# (2, 441001)
```

- 使用焦点从5秒开始2秒开始数取部分数据，，并冻结该部分

```
audio_container.set_focus(start_seconds=5, duration_seconds=2.0).freeze()
print(audio_container.shape)
# (2, 88200)
```

### 2.4 处理
- 正则化音频

```
audio_container.normalize()
```

- 对音频重采样到目标采样率：

```
audio_container.resample(target_fs=16000)
```

### 2.5 可视化
- 绘图波形：

```
audio_container.plot_wave()
```

- 绘制频谱

```
audio_container.plot_spec()
```

## 3. 声学特征

库提供基本的声学特征提取器：`dcase_util.features.MelExtractor`，`dcase_util.features.MfccStaticExtractor`，`dcase_util.features.MfccDeltaExtractor`，`dcase_util.features.MfccAccelerationExtractor`，`dcase_util.features.ZeroCrossingRateExtractor`，`dcase_util.features.RMSEnergyExtractor`和`dcase_util.features.SpectralCentroidExtractor`。

### 3.1 特征提取
- 为音频信号提取梅尔带能量（使用默认参数）：

```
# Get audio in a container, mixdown of a stereo signal
audio_container = dcase_util.containers.AudioContainer().load(
  filename=dcase_util.utils.Example.audio_filename()
).mixdown()

# Create extractor
mel_extractor = dcase_util.features.MelExtractor()

# Extract features
mels = mel_extractor.extract(audio_container)
```
- 为特定音频段提取梅尔带能量：

```
# Get audio in a container, mixdown of a stereo signal
audio_container = dcase_util.containers.AudioContainer().load(
  filename=dcase_util.utils.Example.audio_filename()
).mixdown()

# Set focus
audio_container.set_focus(start_seconds=1.0, stop_seconds=4.0)

# Create extractor
mel_extractor = dcase_util.features.MelExtractor()

# Extract features
mels = mel_extractor.extract(audio_container.get_focused())

# Plot
dcase_util.containers.DataMatrix2DContainer(
    data=mels,
    time_resolution=mel_extractor.hop_length_seconds
).plot()
```

- 直接从numpy矩阵中提取特征：

```
# Create an audio signal
t = numpy.linspace(0, 2, 2 * 44100, endpoint=False)
x1 = numpy.sin(220 * 2 * numpy.pi * t)

# Create extractor
mel_extractor = dcase_util.features.MelExtractor()

# Extract features
mels = mel_extractor.extract(x1)

# Plot
dcase_util.containers.DataMatrix2DContainer(
    data=mels,
    time_resolution=mel_extractor.hop_length_seconds
).plot()
```

> 库中提供的所有音频提取器适用于单声道音频。

### 3.2 可视化
- 绘制提取的特征

```
# Get audio in a container, mixdown of a stereo signal
audio_container = dcase_util.containers.AudioContainer().load(
  filename=dcase_util.utils.Example.audio_filename()
).mixdown()

# Create extractor
mel_extractor = dcase_util.features.MelExtractor()

# Extract features
mels = mel_extractor.extract(audio_container)

# Plotting
dcase_util.containers.DataMatrix2DContainer(
    data=mels,
    time_resolution=mel_extractor.hop_length_seconds
).plot()
```

## 4. 数据处理
### 4.1 数据操作
有几个不同的实用程序来操作数据：

- `dcase_util.data.Normalizer`，计算归一化因子和归一化数据。
- `dcase_util.data.RepositoryNormalizer`，一次性标准化数据存储库。
- `dcase_util.data.Aggregator`，聚合滑动处理窗口中的数据。
- `dcase_util.data.Sequence`r，对数据矩阵进行排序。
- `dcase_util.data.Stacker`，基于给定的矢量配方堆叠数据矩阵。
- `dcase_util.data.Selector`，根据具有开始和偏移量的事件选择数据的数据段。
- `dcase_util.data.Masker`，基于具有开始和偏移的事件来掩盖数据的数据段。

#### 4.1.1 归一化
`dcase_util.data.Normalizer`类可用于计算数据的归一化因子（平均值和标准偏差），而不一次读取所有数据。在小部分读取数据时累计中间统计数据。

- 逐个文件计算归一化因子：

```
data = dcase_util.utils.Example.feature_container()

# Initialize normalizer
normalizer = dcase_util.data.Normalizer()

# Accumulate -- feed data per file in
normalizer.accumulate(data=data)

# After accumulation calculate normalization factors (mean + std)
normalizer.finalize()

# Save
normalizer.save(filename='norm_factors.cpickle')

# Load
normalizer = dcase_util.data.Normalizer().load(filename='norm_factors.cpickle')
```
- 使用声明：

```
data = dcase_util.utils.Example.feature_container()

# Accumulate
with dcase_util.data.Normalizer() as normalizer:
    normalizer.accumulate(data=data)

# Save
normalizer.save(filename='norm_factors.cpickle')
```

- 用预先计算的值初始化标准化器（normalizer）：

```
data = dcase_util.utils.Example.feature_container()
normalizer = dcase_util.data.Normalizer(
    **data.stats
)
```
- 归一化数据

```
data = dcase_util.utils.Example.feature_container()
normalizer = dcase_util.data.Normalizer().load(filename='norm_factors.cpickle')
normalizer.normalize(data)
```

#### 4.1.2 聚合（Aggregator）
数据聚合器类（dcase_util.data.Aggregator）可用于在滑动处理窗口中处理数据矩阵。 这个处理阶段可以用来通过计算它们的平均值和标准偏差来折叠具有特定窗口长度的数据，或者将该矩阵平坦化为单个向量。

支持的处理方法：flatten、mean、std、cov、kurtosis、skew。所有这些处理方法都可以结合使用。

- 计算分为10帧窗口的均值和标准差，以1帧跳跃（Calculating mean and standard deviation in 10 frame window, with 1 frame hop）：

```
data = dcase_util.utils.Example.feature_container()
print(data.shape)
# (40, 501)

data_aggregator = dcase_util.data.Aggregator(
    recipe=['mean', 'std'],
    win_length_frames=10,
    hop_length_frames=1,
)
data_aggregator.aggregate(data)
print(data.shape)
# (80, 501)
```
- 将具有10帧的数据矩阵压缩成一个单独的向量，具有1帧跳跃（with 1 frame hop）：

```
data = dcase_util.utils.Example.feature_container()
print(data.shape)
# (40, 501)

data_aggregator = dcase_util.data.Aggregator(
    recipe=['flatten'],
    win_length_frames=10,
    hop_length_frames=1,
)
data_aggregator.aggregate(data)
print(data.shape)
# (400, 501)
```

#### 4.1.3 测序（Sequencing）

Sequencer类（dcase_util.data.Sequencer）将数据矩阵处理成序列（图像）。序列可以重叠，并且可以在调用之间改变排序网格（移位）。

- 创建序列：

```
data = dcase_util.utils.Example.feature_container()
print(data.shape)
# (40, 501)
data_sequencer = dcase_util.data.Sequencer(
    frames=10,
    hop_length_frames=10
)
sequenced_data = data_sequencer.sequence(data)
print(sequenced_data.shape)
# (40, 10, 50)

sequenced_data.show()
# DataMatrix3DContainer :: Class
#   Data
#     data                            : matrix (40,10,50)
#     Dimensions
#       time_axis                     : 1
#     Timing information
#       time_resolution               : None
#   Meta
#     stats                           : Calculated
#     metadata                        : -
#     processing_chain                : -
#   Duration
#     Frames                          : 10
#   Data
#     Dimensions
#       time_axis                     : 1
#       data_axis                     : 0
#       sequence_axis                 : 2
```

#### 4.1.4 堆叠（Stacker）
Stacker类（`dcase_util.data.Stacker`）根据配方堆叠存储在数据存储库中的数据。例如，可以使用此类创建包含使用多个特征提取器提取的数据的新特征矩阵。使用配方，您可以选择全矩阵，只有部分具有开始和结束索引的数据向量，或选择单个数据行。

例：

```
# Load data repository
repo = dcase_util.utils.Example.feature_repository()

# Show labels in the repository
print(repo.labels)

# Select full matrix from 'mel' and with default stream (0) (40 mel bands).
data = dcase_util.data.Stacker(recipe='mel').stack(repo)
print(data.shape)
# (40, 501)

# Select full matrix from 'mel' and define stream 0 (40 mel bands).
data = dcase_util.data.Stacker(recipe='mel=0').stack(repo)
print(data.shape)
# (40, 501)

# Select full matrix from 'mel' and 'mfcc' with default stream (0) (40 mel bands + 20 mfccs).
data = dcase_util.data.Stacker(recipe='mel;mfcc').stack(repo)
print(data.shape)
# (60, 501)

# Select data from 'mfcc' matrix with default stream (0), and omit first coefficient (19 mfccs).
data = dcase_util.data.Stacker(recipe='mfcc=1-19').stack(repo)
print(data.shape)
# (19, 501)

# Select data from 'mfcc' matrix with default stream (0), select coefficients 1,5,7 (3 mfccs).
data = dcase_util.data.Stacker(recipe='mfcc=1,5,7').stack(repo)
print(data.shape)
# (3, 501)
```

### 4.2 数据编码
数据编码器可用于将参考元数据转换为二进制矩阵。
- one hot
OneHotEncoder类（`dcase_util.data.OneHotEncoder`）可用于创建二进制矩阵，其中单个类在整个信号中处于活动状态。该编码器适用于多类单标签分类应用。

例：

```
# Initilize encoder
onehot_encoder = dcase_util.data.OneHotEncoder(
    label_list=['class A','class B','class C'],
    time_resolution=0.02
)

# Encode
binary_matrix = onehot_encoder.encode(
    label='class B',
    length_seconds=10.0
)

# Visualize
binary_matrix.plot()
```

- Many-hot
ManyHotEncoder类（`dcase_util.data.ManyHotEncoder`）可用于创建二进制矩阵，其中多个类在整个信号中处于活动状态。 该编码器适用于多类多标签分类应用，如音频标签。

例：

```
# Initilize encoder
manyhot_encoder = dcase_util.data.ManyHotEncoder(
    label_list=['class A','class B','class C'],
    time_resolution=0.02
)

# Encode
binary_matrix = manyhot_encoder.encode(
    label_list=['class A', 'class B'],
    length_seconds=10.0
)

# Visualize
binary_matrix.plot()
```

- 事件滚动（Event roll）

EventRollEncoder类（dcase_util.data.EventRollEncoder）可用于创建二进制矩阵，其中多个事件在指定的时间段内处于活动状态。 该编码器适用于事件检测应用。

例子：

```
# Metadata
meta = dcase_util.containers.MetaDataContainer([
    {
        'filename': 'test1.wav',
        'event_label': 'cat',
        'onset': 1.0,
        'offset': 3.0
    },
    {
        'filename': 'test1.wav',
        'event_label': 'dog',
        'onset': 2.0,
        'offset': 6.0
    },
    {
        'filename': 'test1.wav',
        'event_label': 'speech',
        'onset': 5.0,
        'offset': 8.0
    },
])

# Initilize encoder
event_roll_encoder = dcase_util.data.EventRollEncoder(
    label_list=meta.unique_event_labels,
    time_resolution=0.02
)

# Encode
event_roll = event_roll_encoder.encode(
    metadata_container=meta,
    length_seconds=10.0
)

# Visualize
event_roll.plot()
```

## 5. Metadata

Library提供容器dcase_util.containers.MetaDataContainer，用于处理来自大多数DCASE相关应用程序区域的元数据：声场景分类，事件检测和音频标记。

原则上，元数据是一个包含元项目字典的列表，它可以像普通列表一样使用。

### 5.1 创建容器(container)

- 用声场列表初始化元数据容器：
```
meta_container_scenes = dcase_util.containers.MetaDataContainer(
    [
        {
            'filename': 'file1.wav',
            'scene_label': 'office'
        },
        {
            'filename': 'file2.wav',
            'scene_label': 'street'
        },
        {
            'filename': 'file3.wav',
            'scene_label': 'home'
        }
    ]
)
```
- 用声音事件列表初始化元数据容器：

```
meta_container_events = dcase_util.containers.MetaDataContainer(
    [
        {
            'filename': 'file1.wav',
            'event_label': 'speech',
            'onset': 10.0,
            'offset': 15.0,
        },
        {
            'filename': 'file1.wav',
            'event_label': 'footsteps',
            'onset': 23.0,
            'offset': 26.0,
        },
        {
            'filename': 'file2.wav',
            'event_label': 'speech',
            'onset': 2.0,
            'offset': 5.0,
        }
    ]
)
```
- 用标签初始化元数据容器：

```
meta_container_tags = dcase_util.containers.MetaDataContainer(
    [
        {
            'filename': 'file1.wav',
            'tags': ['cat','dog']
        },
        {
            'filename': 'file2.wav',
         'tags': ['dog']
        },
        {
            'filename': 'file3.wav',
            'tags': ['dog','horse']
        }
    ]
)
```
- 显示内容

```
meta_container_scenes.show()
# MetaDataContainer :: Class
#   Items                             : 3
#   Unique
#     Files                           : 3
#     Scene labels                    : 3
#     Event labels                    : 0
#     Tags                            : 0
#
#   Scene statistics
#         Scene label             Count
#         --------------------   ------
#         home                        1
#         office                      1
#         street                      1

meta_container_events.show()
# MetaDataContainer :: Class
#   Items                             : 3
#   Unique
#     Files                           : 2
#     Scene labels                    : 0
#     Event labels                    : 2
#     Tags                            : 0
#
#   Event statistics
#         Event label             Count   Tot. Length   Avg. Length
#         --------------------   ------   -----------   -----------
#         footsteps                   1          3.00          3.00
#         speech                      2          8.00          4.00

meta_container_tags.show()
# MetaDataContainer :: Class
#   Items                             : 3
#   Unique
#     Files                           : 3
#     Scene labels                    : 0
#     Event labels                    : 0
#     Tags                            : 3
#
#   Tag statistics
#         Tag                     Count
#         --------------------   ------
#         cat                         1
#         dog                         3
#         horse                       1
```

- 显示内容和每个元数据项目：

```
meta_container_scenes.show_all()
# MetaDataContainer :: Class
#   Items                             : 3
#   Unique
#     Files                           : 3
#     Scene labels                    : 3
#     Event labels                    : 0
#     Tags                            : 0
#
#   Meta data
#         Source                  Onset   Offset   Scene             Event             Tags              Identifier
#         --------------------   ------   ------   ---------------   ---------------   ---------------   -----
#         file1.wav                   -        -   office            -                 -                 -
#         file2.wav                   -        -   street            -                 -                 -
#         file3.wav                   -        -   home              -                 -                 -
#
#   Scene statistics
#         Scene label             Count
#         --------------------   ------
#         home                        1
#         office                      1
#         street                      1

meta_container_events.show_all()
# MetaDataContainer :: Class
#   Items                             : 3
#   Unique
#     Files                           : 2
#     Scene labels                    : 0
#     Event labels                    : 2
#     Tags                            : 0
#
#   Meta data
#         Source                  Onset   Offset   Scene             Event             Tags              Identifier
#         --------------------   ------   ------   ---------------   ---------------   ---------------   -----
#         file1.wav               10.00    15.00   -                 speech            -                 -
#         file1.wav               23.00    26.00   -                 footsteps         -                 -
#         file2.wav                2.00     5.00   -                 speech            -                 -
#
#   Event statistics
#         Event label             Count   Tot. Length   Avg. Length
#         --------------------   ------   -----------   -----------
#         footsteps                   1          3.00          3.00
#         speech                      2          8.00          4.00
```

### 5.2 加载和保存

- 将元数据保存到文件中：

```
meta_container_events.save(filename='events.txt')
```

- 从注释文件加载元数据：

```
meta_container_events = dcase_util.containers.MetaDataContainer().load(
    filename='events.txt'
)
```

### 5.3 访问数据
- 获取元数据中提及的音频文件及其数量：

```
print(meta_container_events.unique_files)
# ['file1.wav', 'file2.wav']

print(meta_container_events.file_count)
# 2
```
- 获取独特的场景标签及其数量：

```
print(meta_container_scenes.unique_scene_labels)
# ['home', 'office', 'street']

print(meta_container_scenes.scene_label_count)
# 3
```

- 获取元数据中使用的特定事件标签及其数量：

```
print(meta_container_events.unique_event_labels)
# ['footsteps', 'speech']

print(meta_container_scenes.event_label_count)
```

- 获取元数据中使用的独特标签及其数量：

```
print(meta_container_tags.unique_tags)
# ['cat', 'dog', 'horse']

print(meta_container_tags.tag_count)
# 3
```
### 5.4  过滤（Filtering）
- 基于文件名过滤元数据：

```
filtered = meta_container_events.filter(filename='file1.wav')
filtered.show_all()
# MetaDataContainer :: Class
#   Items                             : 2
#   Unique
#     Files                           : 1
#     Scene labels                    : 0
#     Event labels                    : 2
#     Tags                            : 0
#
#   Meta data
#         Source                  Onset   Offset   Scene             Event             Tags              Identifier
#         --------------------   ------   ------   ---------------   ---------------   ---------------   -----
#         file1.wav               10.00    15.00   -                 speech            -                 -
#         file1.wav               23.00    26.00   -                 footsteps         -                 -
#
#   Event statistics
#         Event label             Count   Tot. Length   Avg. Length
#         --------------------   ------   -----------   -----------
#         footsteps                   1          3.00          3.00
#         speech                      1          5.00          5.00
```
- 基于事件标签过滤元数据：

```
filtered = meta_container_events.filter(event_label='speech')
filtered.show_all()
# MetaDataContainer :: Class
#   Items                             : 2
#   Unique
#     Files                           : 2
#     Scene labels                    : 0
#     Event labels                    : 1
#     Tags                            : 0
#
#   Meta data
#         Source                  Onset   Offset   Scene             Event             Tags              Identifier
#         --------------------   ------   ------   ---------------   ---------------   ---------------   -----
#         file1.wav               10.00    15.00   -                 speech            -                 -
#         file2.wav                2.00     5.00   -                 speech            -                 -
#
#   Event statistics
#         Event label             Count   Tot. Length   Avg. Length
#         --------------------   ------   -----------   -----------
#         speech                      2          8.00          4.00
```
- 基于文件和事件标签过滤元数据：

```
filtered = meta_container_events.filter(filename='file1.wav', event_label='speech')
filtered.show_all()
# MetaDataContainer :: Class
#   Items                             : 1
#   Unique
#     Files                           : 1
#     Scene labels                    : 0
#     Event labels                    : 1
#     Tags                            : 0
#
#   Meta data
#         Source                  Onset   Offset   Scene             Event             Tags              Identifier
#         --------------------   ------   ------   ---------------   ---------------   ---------------   -----
#         file1.wav               10.00    15.00   -                 speech            -                 -
#
#   Event statistics
#         Event label             Count   Tot. Length   Avg. Length
#         --------------------   ------   -----------   -----------
#         speech                      1          5.00          5.00
```

- 基于时间段过滤，并使段开始新的零时间：

```
filtered = meta_container_events.filter_time_segment(filename='file1.wav', start=12, stop=24)
filtered.show_all()
# MetaDataContainer :: Class
#   Items                             : 2
#   Unique
#     Files                           : 1
#     Scene labels                    : 0
#     Event labels                    : 2
#     Tags                            : 0
#
#   Meta data
#         Source                  Onset   Offset   Scene             Event             Tags              Identifier
#         --------------------   ------   ------   ---------------   ---------------   ---------------   -----
#         file1.wav                0.00     3.00   -                 speech            -                 -
#         file1.wav               11.00    12.00   -                 footsteps         -                 -
#
#   Event statistics
#         Event label             Count   Tot. Length   Avg. Length
#         --------------------   ------   -----------   -----------
#         footsteps                   1          1.00          1.00
#         speech                      1          3.00          3.00
```

### 5.5 处理（Processing）
- 将时间偏移量添加到元数据项中设置的开始和偏移量：

```
meta_container_events.add_time(time=10)
meta_container_events.show_all()
# MetaDataContainer :: Class
#   Items                             : 3
#   Unique
#     Files                           : 2
#     Scene labels                    : 0
#     Event labels                    : 2
#     Tags                            : 0
#
#   Meta data
#         Source                  Onset   Offset   Scene             Event             Tags              Identifier
#         --------------------   ------   ------   ---------------   ---------------   ---------------   -----
#         file1.wav               20.00    25.00   -                 speech            -                 -
#         file1.wav               33.00    36.00   -                 footsteps         -                 -
#         file2.wav               12.00    15.00   -                 speech            -                 -
#
#   Event statistics
#         Event label             Count   Tot. Length   Avg. Length
#         --------------------   ------   -----------   -----------
#         footsteps                   1          3.00          3.00
#         speech                      2          8.00          4.00
```

- 删除非常短的事件并合并它们之间有小间隙的事件（相同的事件标签）：

```
meta_container_events = dcase_util.containers.MetaDataContainer(
    [
        {
            'filename': 'file1.wav',
            'event_label': 'speech',
            'onset': 1.0,
            'offset': 2.0,
        },
        {
            'filename': 'file1.wav',
            'event_label': 'speech',
            'onset': 2.05,
            'offset': 2.5,
        },
        {
            'filename': 'file1.wav',
            'event_label': 'speech',
            'onset': 5.1,
            'offset': 5.15,
        },
    ]
)
processed = meta_container_events.process_events(minimum_event_length=0.2, minimum_event_gap=0.1)
processed.show_all()
# MetaDataContainer :: Class
#   Items                             : 1
#   Unique
#     Files                           : 1
#     Scene labels                    : 0
#     Event labels                    : 1
#     Tags                            : 0
#
#   Meta data
#         Source                  Onset   Offset   Scene             Event             Tags              Identifier
#         --------------------   ------   ------   ---------------   ---------------   ---------------   -----
#         file1.wav                1.00     2.50   -                 speech            -                 -
#
#   Event statistics
#         Event label             Count   Tot. Length   Avg. Length
#         --------------------   ------   -----------   -----------
#        speech                      1          1.50          1.50
```

### 5.6 事件滚动（Event roll）
将事件列表转换为事件滚动（具有事件活动的二进制矩阵）：


```
meta_container_events = dcase_util.containers.MetaDataContainer(
    [
        {
            'filename': 'file1.wav',
            'event_label': 'speech',
            'onset': 1.0,
            'offset': 2.0,
        },
        {
            'filename': 'file1.wav',
            'event_label': 'speech',
            'onset': 2.05,
            'offset': 2.5,
        },
        {
            'filename': 'file1.wav',
            'event_label': 'speech',
            'onset': 5.1,
            'offset': 5.15,
        },
        {
            'filename': 'file1.wav',
            'event_label': 'footsteps',
            'onset': 3.1,
            'offset': 4.15,
        },
        {
            'filename': 'file1.wav',
            'event_label': 'dog',
            'onset': 2.6,
            'offset': 3.6,
        },
    ]
)
event_roll = meta_container_events.to_event_roll()

# Plot
event_roll.plot()
```

## 6. Datasets
数据库类提供在库中为许多不同组织的音频数据集创建统一的接口。数据集将在第一次使用时下载，提取并准备好使用情况。

提供了四种类型的数据集：

- 声场景数据集，从`dcase_util.datasets.AcousticSceneDataset`类继承的类。
- 声音事件数据集，从`dcase_util.datasets.SoundEventDataset`类继承的类。
- 声音事件数据集合成数据创建，从`dcase_util.datasets.SyntheticSoundEventDataset`类继承的类。
- 音频标记数据集，从`dcase_util.datasets.AudioTaggingDataset`类继承的类。

获取所有可用数据集的列表：

```
print(dcase_util.datasets.dataset_list())
#  Dataset list
#  Class Name                                 | Group | Remote | Local  | Audio | Scenes | Events | Tags
#  ------------------------------------------ | ----- | ------ | ------ | ----- | ------ | ------ | ----
#  DCASE2013_Scenes_DevelopmentSet            | scene | 344 MB | 849 MB | 100   | 10     |        |
#  TUTAcousticScenes_2016_DevelopmentSet      | scene | 7 GB   | 23 GB  | 1170  | 15     |        |
#  TUTAcousticScenes_2016_EvaluationSet       | scene | 2 GB   | 5 GB   | 390   | 15     |        |
#  TUTAcousticScenes_2017_DevelopmentSet      | scene | 9 GB   | 21 GB  | 4680  | 15     |        |
#  TUTAcousticScenes_2017_EvaluationSet       | scene | 3 GB   | 7 GB   |       |        |        |
#  DCASE2017_Task4tagging_DevelopmentSet      | event | 5 MB   | 24 GB  | 56700 | 1      | 17     |
#  DCASE2017_Task4tagging_EvaluationSet       | event | 823 MB | 1 GB   |       |        |        |
#  TUTRareSoundEvents_2017_DevelopmentSet     | event | 7 GB   | 28 GB  |       |        | 3      |
#  TUTRareSoundEvents_2017_EvaluationSet      | event | 4 GB   | 4 GB   |       |        | 3      |
#  TUTSoundEvents_2016_DevelopmentSet         | event | 967 MB | 2 GB   | 954   | 2      | 17     |
#  TUTSoundEvents_2016_EvaluationSet          | event | 449 MB | 989 MB | 511   | 2      | 17     |
#  TUTSoundEvents_2017_DevelopmentSet         | event | 1 GB   | 2 GB   | 659   | 1      | 6      |
#  TUTSoundEvents_2017_EvaluationSet          | event | 370 MB | 798 MB |       |        |        |
#  TUT_SED_Synthetic_2016                     | event | 4 GB   | 5 GB   |       |        |        |
#  CHiMEHome_DomesticAudioTag_DevelopmentSet  | tag   | 3 GB   | 9 GB   | 1946  | 1      |        | 7
```
### 6.1  初始化数据集
要下载，提取和准备数据集（在这种情况下，数据集将放置在临时目录中，并且只下载与元数据相关的文件）：

```
import tempfile

db = dcase_util.datasets.TUTAcousticScenes_2016_DevelopmentSet(
    data_path=tempfile.gettempdir(),
    included_content_types=['meta']
)
db.initialize()
db.show()
# DictContainer :: Class
#   audio_source                      : Field recording
#   audio_type                        : Natural
#   authors                           : Annamaria Mesaros, Toni Heittola, and Tuomas Virtanen
#   licence                           : free non-commercial
#   microphone_model                  : Soundman OKM II Klassik/studio A3 electret microphone
#   recording_device_model            : Roland Edirol R-09
#   title                             : TUT Acoustic Scenes 2016, development dataset
#   url                               : https://zenodo.org/record/45739
#
# MetaDataContainer :: Class
#   Filename                          : /tmp/TUT-acoustic-scenes-2016-development/meta.txt
#   Items                             : 1170
#   Unique
#     Files                           : 1170
#     Scene labels                    : 15
#     Event labels                    : 0
#     Tags                            : 0
#
#   Scene statistics
#         Scene label             Count
#         --------------------   ------
#         beach                      78
#         bus                        78
#         cafe/restaurant            78
#         car                        78
#         city_center                78
#         forest_path                78
#         grocery_store              78
#         home                       78
#         library                    78
#         metro_station              78
#         office                     78
#         park                       78
#         residential_area           78
#         train                      78
#         tram                       78
```
### 6.2 交叉验证步骤
通常数据集通过交叉验证设置提供。

获得fold训练材料1：

```
training_material = db.train(fold=1)
training_material.show()
# MetaDataContainer :: Class
#   Filename                          : /tmp/TUT-acoustic-scenes-2016-development/evaluation_setup/fold1_train.txt
#   Items                             : 880
#   Unique
#     Files                           : 880
#     Scene labels                    : 15
#     Event labels                    : 0
#     Tags                            : 0
#
#   Scene statistics
#         Scene label             Count
#         --------------------   ------
#         beach                      59
#         bus                        59
#         cafe/restaurant            60
#         car                        58
#         city_center                60
#         forest_path                57
#         grocery_store              59
#         home                       56
#         library                    57
#         metro_station              59
#         office                     59
#         park                       58
#         residential_area           59
#         train                      60
#         tram                       60
```
- 获取fold1的测试材料（无参考数据的材料）：

```
testing_material = db.test(fold=1)
testing_material.show()
# MetaDataContainer :: Class
#   Filename                          : /tmp/TUT-acoustic-scenes-2016-development/evaluation_setup/fold1_test.txt
#   Items                             : 290
#   Unique
#     Files                           : 290
#     Scene labels                    : 0
#     Event labels                    : 0
#     Tags                            : 0
```

- 使用完整的参考数据获取fold1的测试材料：

```
eval_material = db.eval(fold=1)
eval_material.show()

# MetaDataContainer :: Class
#   Filename                          : /tmp/TUT-acoustic-scenes-2016-development/evaluation_setup/fold1_evaluate.txt
#   Items                             : 290
#   Unique
#     Files                           : 290
#     Scene labels                    : 15
#     Event labels                    : 0
#     Tags                            : 0
#
#   Scene statistics
#         Scene label             Count
#         --------------------   ------
#         beach                      19
#         bus                        19
#         cafe/restaurant            18
#         car                        20
#         city_center                18
#         forest_path                21
#         grocery_store              19
#         home                       22
#         library                    21
#         metro_station              19
#         office                     19
#         park                       20
#         residential_area           19
#         train                      18
#         tram                       18
```
- 要将所有数据集合折叠为无：（To get all data set fold to None）

```
all_material = db.train(fold=None)
all_material.show()
# MetaDataContainer :: Class
#   Filename                          : /tmp/TUT-acoustic-scenes-2016-development/meta.txt
#   Items                             : 1170
#   Unique
#     Files                           : 1170
#     Scene labels                    : 15
#     Event labels                    : 0
#     Tags                            : 0
#
#   Scene statistics
#         Scene label             Count
#         --------------------   ------
#         beach                      78
#         bus                        78
#         cafe/restaurant            78
#         car                        78
#         city_center                78
#         forest_path                78
#         grocery_store              78
#         home                       78
#         library                    78
#         metro_station              78
#         office                     78
#         park                       78
#         residential_area           78
#         train                      78
#         tram                       78
```

- 迭代所有fold：

```
for fold in db.folds:
    train_material = db.train(fold=fold)
```

大多数数据集不提供验证集拆分。 但是，数据集类提供了几种方法从训练集中生成它，同时保持数据统计数据并确保在训练集和验证集中不会有来自同一源的数据。

为fold1生成平衡的验证集（平衡完成，以便将来自相同位置的记录分配给同一集）：

```
training_files, validation_files = db.validation_split(
    fold=1,
    split_type='balanced',
    validation_amount=0.3
)
```

- 为fold1生成随机验证集（不平衡）：

```
training_files, validation_files = db.validation_split(
    fold=1,
    split_type='random',
    validation_amount=0.3
)
```

- 获取数据集提供的验证集（示例中使用的数据集不提供它，这会引发错误。）：

```
training_files, validation_files = db.validation_split(
    fold=1,
    split_type='dataset'
)
```
### 6.3 元数据（Meta data）
- 获取与该文件关联的元数据：

```
items = db.file_meta(filename='audio/b086_150_180.wav')
print(items)
# MetaDataContainer :: Class
#   Items                             : 1
#   Unique
#     Files                           : 1
#     Scene labels                    : 1
#     Event labels                    : 0
#     Tags                            : 0
#
#   Meta data
#         Source                  Onset   Offset   Scene             Event             Tags              Identifier
#         --------------------   ------   ------   ---------------   ---------------   ---------------   -----
#         audio/b086_150_180..        -        -   grocery_store     -                 -                 -
#
#   Scene statistics
#         Scene label             Count
#         --------------------   ------
#         grocery_store               1
```

## 7. 处理链（Processing chain）
除了基本的实用程序外，该库还提供了将各种数据处理类链接在一起的机制。这样可以更轻松地构建复杂的数据处理管道。数据处理在包含在链中的处理器（处理器列表）中完成。

所有处理器类都使用`dcase_util.processors.ProcessorMixin mixin`和特定的实用程序类。例如，`dcase_util.processors.AudioReadingProcessor`从`dcase_util.processors.ProcessorMixin`和`dcase_util.containers.AudioContainer`继承。

- 音频相关处理器：

`dcase_util.processors.AudioReadingProcessor`，音频读取，支持多声道音频。
`dcase_util.processors.MonoAudioReadingProcessor`，在多声道音频通道的情况下，音频读取将混合到单个通道中。

- 数据处理处理器：

`dcase_util.processors.AggregationProcessor`，通过滑动处理窗口聚合数据。
`dcase_util.processors.SequencingProcessor`，将数据矩阵分割成序列。
`dcase_util.processors.NormalizationProcessor`，规范数据矩阵。
`dcase_util.processors.RepositoryNormalizationProcessor`，规范存储在仓库中的数据。
`dcase_util.processors.StackingProcesso`r根据存储在存储库中的数据堆叠新的特征矩阵。

- 数据编码处理器：

`dcase_util.processors.OneHotEncodingProcessor`，one-hot编码（分类）。
`dcase_util.processors.ManyHotEncodingProcessor`，many-hot编码（标记）。
`dcase_util.processors.EventRollEncodingProcessor`，事件滚动编码（检测）。

- 特征提取处理器：

    - `dcase_util.processors.RepositoryFeatureExtractorProcessor`，同时提取许多要素类型，并将它们存储到单个存储库中。支持多声道音频输入。
    
    - `dcase_util.processors.MelExtractorProcessor`，提取梅尔带能量。仅支持单声道音频。
    
    - `dcase_util.processors.MfccStaticExtractorProcessor`，提取静态MFCC。仅支持单声道音频。
    
    - `dcase_util.processors.MfccDeltaExtractorProcessor`，提取delta MFCC。仅支持单声道音频。
    
    - `dcase_util.processors.MfccAccelerationExtractorProcessor`，提取加速MFCC。仅支持单声道音频。
    
    - `dcase_util.processors.ZeroCrossingRateExtractorProcessor`，提取过零率。仅支持单声道音频。
    - `dcase_util.processors.RMSEnergyExtractorProcessor`，提取RMS能量。仅支持单声道音频。
    - `dcase_util.processors.SpectralCentroidExtractorProcessor`，提取光谱质心。仅支持单声道音频。

- 元数据处理器：

`dcase_util.processors.MetadataReadingProcesso`r，从文件中读取元数据。

### 7.1 特征提取和处理
- 为音频文件提取梅尔带能量： 


```
# Define processing chain
chain = dcase_util.processors.ProcessingChain([
    {
        'processor_name': 'dcase_util.processors.MonoAudioReadingProcessor',
        'init_parameters': {
            'fs': 44100
        }
    },
    {
        'processor_name': 'dcase_util.processors.MelExtractorProcessor',
        'init_parameters': {}
    }
])
# Run the processing chain
data = chain.process(filename=dcase_util.utils.Example().audio_filename())
data.show()
# FeatureContainer :: Class
#   Data
#     data                            : matrix (40,501)
#     Dimensions
#       time_axis                     : 1
#       data_axis                     : 0
#     Timing information
#       time_resolution               : 0.02 sec
#   Meta
#     stats                           : Calculated
#     metadata                        : -
#     processing_chain                : ProcessingChain :: Class
#                                       [0]
#                                          DictContainer :: Class
#                                            init_parameters
#                                              _focus_channel                  : None
#                                              _focus_start                    : 0
#                                              _focus_stop                     : None
#                                              channel_axis                    : 0
#                                              data_synced_with_file           : False
#                                              filename                        : None
#                                              fs                              : 44100
#                                              mono                            : True
#                                              time_axis                       : 1
#                                            input_type                        : NONE
#                                            output_type                       : AUDIO
#                                            process_parameters
#                                              filename                        : dcase_util/utils/example_data/acoustic_scene.flac
#                                              focus_channel                   : None
#                                              focus_duration_samples          : (None,)
#                                              focus_duration_sec              : None
#                                              focus_start_samples             : None
#                                              focus_start_sec                 : None
#                                              focus_stop_samples              : None
#                                              focus_stop_sec                  : None
#                                            processor_name                    : dcase_util.processors.MonoAudioReadingProcessor
#
#                                       [1]
#                                          DictContainer :: Class
#                                            init_parameters
#                                              eps                             : 2.22044604925e-16
#                                              fmax                            : None
#                                              fmin                            : 0
#                                              fs                              : 44100
#                                              hop_length_samples              : 882
#                                              hop_length_seconds              : 0.02
#                                              htk                             : False
#                                              log                             : True
#                                              n_mels                          : 40
#                                              normalize_mel_bands             : False
#                                              win_length_samples              : 1764
#                                              win_length_seconds              : 0.04
#                                            input_type                        : AUDIO
#                                            output_type                       : DATA_CONTAINER
#                                            process_parameters
#                                              filename                        : dcase_util/utils/example_data/acoustic_scene.flac
#                                            processor_name                    : dcase_util.processors.MelExtractorProcessor
#
#
#   Duration
#       Frames                        : 501
#       Seconds                       : 10.02 sec
```

- 关注音频的某些部分：

```
# Define processing chain
chain = dcase_util.processors.ProcessingChain([
    {
        'processor_name': 'dcase_util.processors.MonoAudioReadingProcessor',
        'init_parameters': {
            'fs': 44100
        }
    },
    {
        'processor_name': 'dcase_util.processors.MelExtractorProcessor',
        'init_parameters': {}
    }
])
# Run the processing chain
data = chain.process(
    filename=dcase_util.utils.Example().audio_filename(),
    focus_start_seconds=1.0,
    duration_seconds=2.0
)
print(data.shape)
# (40, 101)

# Run the processing chain
data = chain.process(
    filename=dcase_util.utils.Example().audio_filename(),
    focus_start_samples=44100,
    focus_stop_samples=44100*2
)
print(data.shape)
# (40, 51)
```
- 为音频文件提取几个不同的声学特征，并形成数据矩阵：

```
# Define processing chain
chain = dcase_util.processors.ProcessingChain([
    {
        'processor_name': 'dcase_util.processors.MonoAudioReadingProcessor',
        'init_parameters': {
            'fs': 44100
        }
    },
    {
        'processor_name': 'dcase_util.processors.RepositoryFeatureExtractorProcessor',
        'init_parameters': {
            'parameters': {
                'mel': {},
                'mfcc': {}
            }
        }
    },
    {
        'processor_name': 'dcase_util.processors.StackingProcessor',
        'init_parameters': {
            'recipe': 'mel;mfcc=1-19'
        }
    }
])
# Run the processing chain
data = chain.process(filename=dcase_util.utils.Example().audio_filename())
data.show()
# FeatureContainer :: Class
#   Data
#     data                            : matrix (59,501)
#     Dimensions
#       time_axis                     : 1
#       data_axis                     : 0
#     Timing information
#       time_resolution               : 0.02 sec
#   Meta
#     stats                           : Calculated
#     metadata                        : -
#     processing_chain                : ProcessingChain :: Class
#                                       [0]
#                                          DictContainer :: Class
#                                            init_parameters
#                                              _focus_channel                  : None
#                                              _focus_start                    : 0
#                                              _focus_stop                     : None
#                                              channel_axis                    : 0
#                                              data_synced_with_file           : False
#                                              filename                        : None
#                                              fs                              : 44100
#                                              mono                            : True
#                                              time_axis                       : 1
#                                            input_type                        : NONE
#                                            output_type                       : AUDIO
#                                            process_parameters
#                                              filename                        : dcase_util/utils/example_data/acoustic_scene.flac
#                                              focus_channel                   : None
#                                              focus_duration_samples          : (None,)
#                                              focus_duration_sec              : None
#                                              focus_start_samples             : None
#                                              focus_start_sec                 : None
#                                              focus_stop_samples              : None
#                                              focus_stop_sec                  : None
#                                            processor_name                    : dcase_util.processors.MonoAudioReadingProcessor
#
#                                       [1]
#                                          DictContainer :: Class
#                                            init_parameters
#                                              parameters
#                                                mel
#                                                mfcc
#                                            input_type                        : AUDIO
#                                            output_type                       : DATA_REPOSITORY
#                                            process_parameters
#                                            processor_name                    : dcase_util.processors.RepositoryFeatureExtractorProcessor
#
#                                       [2]
#                                          DictContainer :: Class
#                                            init_parameters
#                                              hop                             : 1
#                                              recipe                          : list (2)
#                                                [0]
#                                                  label                       : mel
#                                                [1]
#                                                  label                       : mfcc
#                                                  vector-index
#                                                    full                      : False
#                                                    selection                 : False
#                                                    start                     : 1
#                                                    stop                      : 20
#                                                    stream                    : 0
#                                            input_type                        : DATA_REPOSITORY
#                                            output_type                       : DATA_CONTAINER
#                                            process_parameters
#                                            processor_name                    : dcase_util.processors.StackingProcessor
#
#
#   Duration
#       Frames                        : 501
#       Seconds                       : 10.02 sec
data.plot()
```

- 为音频文件提取几个不同的声学特征，对它们进行归一化处理，形成数据矩阵，沿时间轴聚合（上下文窗口化），以及将数据拆分成序列：

```
import numpy
# Normalization factors
mel_mean = numpy.array([
    -3.26094211, -4.20447522, -4.57860912, -5.11036974, -5.33019526,
    -5.48390484, -5.50473626, -5.54014946, -5.28249358, -5.12090705,
    -5.21508926, -5.3824216 , -5.37758142, -5.38829567, -5.4912112 ,
    -5.55352419, -5.72801733, -6.02412347, -6.41367833, -6.64073975,
    -6.80493457, -6.8717373 , -6.88140949, -6.91464104, -7.00929399,
    -7.13497673, -7.36417664, -7.73457445, -8.25007518, -8.79878143,
    -9.22709866, -9.28843908, -9.57054527, -9.82846299, -9.85425306,
    -9.90253041, -9.85194976, -9.62786338, -9.38480217, -9.18478766
    ])
mel_std = numpy.array([
    0.3450398 ,  0.47330394,  0.53112192,  0.57607313,  0.66710664,
     0.70052532,  0.79045046,  0.81864229,  0.79422025,  0.76691708,
     0.64798516,  0.59340713,  0.57756029,  0.64032687,  0.70226395,
     0.75670044,  0.80861907,  0.79305124,  0.7289238 ,  0.75346821,
     0.77785602,  0.7350573 ,  0.75137917,  0.77171676,  0.80314121,
     0.78965339,  0.79256442,  0.82524546,  0.84596991,  0.76430333,
     0.69690919,  0.69591269,  0.54718615,  0.5277196 ,  0.61271734,
     0.54482468,  0.42716334,  0.25561558,  0.08991936,  0.06402002
])

mfcc_mean = numpy.array([
    -1.89603847e+02,   4.88930395e+01,  -8.37911555e+00,
      2.58522036e+00,   4.51964497e+00,  -3.87312873e-01,
      8.97250541e+00,   1.61597737e+00,   1.74111135e+00,
      2.50223131e+00,   3.03385048e+00,   1.34561742e-01,
      1.04119803e+00,  -2.57486399e-01,   7.58245525e-01,
      1.11375319e+00,   5.45536494e-01,   7.62699140e-01,
      9.34355519e-01,   1.57158221e-01
])
mfcc_std = numpy.array([
    15.94006483,   2.39593761,   4.78748908,   2.39555341,
      2.66573364,   1.75496556,   2.75005027,   1.5436589 ,
      1.81070379,   1.39476785,   1.22560606,   1.25575051,
      1.34613239,   1.46778281,   1.19398649,   1.1590474 ,
      1.1309816 ,   1.12975486,   0.95503429,   1.01747647
])

# Define processing chain
chain = dcase_util.processors.ProcessingChain([
    {
        'processor_name': 'dcase_util.processors.MonoAudioReadingProcessor',
        'init_parameters': {
            'fs': 44100
        }
    },
    {
        'processor_name': 'dcase_util.processors.RepositoryFeatureExtractorProcessor',
        'init_parameters': {
            'parameters': {
                'mel': {},
                'mfcc': {}
            }
        }
    },
    {
        'processor_name': 'dcase_util.processors.RepositoryNormalizationProcessor',
        'init_parameters': {
            'parameters': {
                'mel': {
                    'mean': mel_mean,
                    'std': mel_std
                },
                'mfcc': {
                    'mean': mfcc_mean,
                    'std': mfcc_std
                }
            }
        }
    },
    {
        'processor_name': 'dcase_util.processors.StackingProcessor',
        'init_parameters': {
            'recipe': 'mel;mfcc=1-19'
        }
    },
    {
        'processor_name': 'dcase_util.processors.AggregationProcessor',
        'init_parameters': {
            'recipe': ['flatten'],
            'win_length_frames': 5,
            'hop_length_frames': 1,
        }
    },
    {
        'processor_name': 'dcase_util.processors.SequencingProcessor',
        'init_parameters': {
            'frames': 20,
            'hop_length_frames': 20,
            'padding': True
        }
    },
])
data = chain.process(filename=dcase_util.utils.Example().audio_filename())
data.show()
# DataMatrix3DContainer :: Class
#   Data
#     data                            : matrix (295,20,26)
#     Dimensions
#       time_axis                     : 1
#       data_axis                     : 0
#       sequence_axis                 : 2
#     Timing information
#       time_resolution               : None
#   Meta
#     stats                           : Calculated
#     metadata                        : -
#     processing_chain                : ProcessingChain :: Class
#                                       [0]
#                                          DictContainer :: Class
#                                            init_parameters
#                                              _focus_channel                  : None
#                                              _focus_start                    : 0
#                                              _focus_stop                     : None
#                                              channel_axis                    : 0
#                                              data_synced_with_file           : False
#                                              filename                        : None
#                                              fs                              : 44100
#                                              mono                            : True
#                                              time_axis                       : 1
#                                            input_type                        : NONE
#                                            output_type                       : AUDIO
#                                            process_parameters
#                                              filename                        : dcase_util/utils/example_data/acoustic_scene.flac
#                                              focus_channel                   : None
#                                              focus_duration_samples          : (None,)
#                                              focus_duration_sec              : None
#                                              focus_start_samples             : None
#                                              focus_start_sec                 : None
#                                              focus_stop_samples              : None
#                                              focus_stop_sec                  : None
#                                            processor_name                    : dcase_util.processors.MonoAudioReadingProcessor
#
#                                       [1]
#                                          DictContainer :: Class
#                                            init_parameters
#                                              parameters
#                                                mel
#                                                mfcc
#                                            input_type                        : AUDIO
#                                            output_type                       : DATA_REPOSITORY
#                                            process_parameters
#                                            processor_name                    : dcase_util.processors.RepositoryFeatureExtractorProcessor
#
#                                       [2]
#                                          DictContainer :: Class
#                                            init_parameters
#                                              parameters
#                                                mel
#                                                  mean                        : matrix (40,1)
#                                                  std                         : matrix (40,1)
#                                                mfcc
#                                                  mean                        : matrix (20,1)
#                                                  std                         : matrix (20,1)
#                                            input_type                        : DATA_REPOSITORY
#                                            output_type                       : DATA_REPOSITORY
#                                            process_parameters
#                                            processor_name                    : dcase_util.processors.RepositoryNormalizationProcessor
#
#                                       [3]
#                                          DictContainer :: Class
#                                            init_parameters
#                                              hop                             : 1
#                                              recipe                          : list (2)
#                                                [0]
#                                                  label                       : mel
#                                                [1]
#                                                  label                       : mfcc
#                                                  vector-index
#                                                    full                      : False
#                                                    selection                 : False
#                                                    start                     : 1
#                                                    stop                      : 20
#                                                    stream                    : 0
#                                            input_type                        : DATA_REPOSITORY
#                                            output_type                       : DATA_CONTAINER
#                                            process_parameters
#                                            processor_name                    : dcase_util.processors.StackingProcessor
#
#                                       [4]
#                                          DictContainer :: Class
#                                            init_parameters
#                                              hop_length_frames               : 1
#                                              recipe                          : list (1)
#                                                [0]                           : flatten
#                                              win_length_frames               : 5
#                                            input_type                        : DATA_CONTAINER
#                                            output_type                       : DATA_CONTAINER
#                                            process_parameters
#                                            processor_name                    : dcase_util.processors.AggregationProcessor
#
#                                       [5]
#                                          DictContainer :: Class
#                                            init_parameters
#                                              frames                          : 20
#                                              hop_length_frames               : 20
#                                              padding                         : True
#                                              shift                           : 0
#                                              shift_border                    : roll
#                                              shift_max                       : None
#                                              shift_step                      : 0
#                                            input_type                        : DATA_CONTAINER
#                                            output_type                       : DATA_CONTAINER
#                                            process_parameters
#                                            processor_name                    : dcase_util.processors.SequencingProcessor
#
#
#   Duration
#       Frames                        : 20
```
### 7.2 元数据处理（Meta data processing）
- 获得事件卷（event roll）：

```
import tempfile
tmp = tempfile.NamedTemporaryFile('r+', suffix='.txt', delete=False)
dcase_util.utils.Example.event_metadata_container().save(filename=tmp.name)

# Define processing chain
chain = dcase_util.processors.ProcessingChain([
    {
        'processor_name': 'dcase_util.processors.MetadataReadingProcessor',
        'init_parameters': {}
    },
    {
        'processor_name': 'dcase_util.processors.EventRollEncodingProcessor',
        'init_parameters': {
            'label_list': dcase_util.utils.Example.event_metadata_container().unique_event_labels,
            'time_resolution': 0.02,
        }
    }
])

# Do the processing
data = chain.process(
    filename=tmp.name,
    focus_filename='test1.wav'
)

# Plot data
data.plot()
```

- 获取用于焦点段事件卷：

```
import tempfile
tmp = tempfile.NamedTemporaryFile('r+', suffix='.txt', delete=False)
dcase_util.utils.Example.event_metadata_container().save(filename=tmp.name)

# Define processing chain
chain = dcase_util.processors.ProcessingChain([
    {
        'processor_name': 'dcase_util.processors.MetadataReadingProcessor',
        'init_parameters': {}
    },
    {
        'processor_name': 'dcase_util.processors.EventRollEncodingProcessor',
        'init_parameters': {
            'label_list': dcase_util.utils.Example.event_metadata_container().unique_event_labels,
            'time_resolution': 0.02,
        }
    }
])

# Do the processing
data = chain.process(
    filename=tmp.name,
    focus_filename='test1.wav',
    focus_start_seconds=2.0,
    focus_stop_seconds=6.5,
)

# Plot data
data.plot()
```

## 应用的例子
声场景分类器
本教程演示如何使用dcase_util中的实用程序构建简单的声场景分类器。 声场景分类器应用程序通常包含以下阶段：

- 数据集初始化阶段，使用数据集已下载并准备使用。
- 特征提取阶段将为开发数据集中的所有音频文件提取声学特征，并存储到磁盘以便稍后访问
- 特征标准化阶段，通过交叉验证折叠训练材料并计算声学特征的均值和标准差以稍后对特征数据进行归一化
- 学习阶段，通过交叉验证折叠的培训材料，并学习声学模型。
- 测试阶段，通过交叉验证折叠测试材料，并估计每个样本的场景类别。
- 评估，评估系统输出与地面真相。

本例使用DCASE2013（10场景类），静态MFCC作为特征，GMM作为分类器发布的声场景数据集。 例子只显示了最少的代码，通常的开发系统需要更好的参数化来使系统开发更容易。

完整的代码示例可以找到`examples / asc_gmm_simple.py`。

- 数据集初始化
本示例使用针对DCASE2013发布的声场景数据集，处理此类的数据集类随`dcase_utils：dcase_util.datasets.DCASE2013_Scenes_DevelopmentSet`一起提供。

数据集需要先下载，解压缩到磁盘，并准备好使用：

```
import os
import dcase_util
# Setup logging
dcase_util.utils.setup_logging()

log = dcase_util.ui.FancyLogger()
log.title('Acoustic Scene Classification Example / GMM')

# Create dataset object and set dataset to be stored under 'data' directory.
db = dcase_util.datasets.DCASE2013_Scenes_DevelopmentSet(
    data_path='data'
)

# Initialize dataset (download, extract and prepare it).
db.initialize()

# Show dataset information
db.show()
# DictContainer :: Class
#   audio_source                      : Field recording
#   audio_type                        : Natural
#   authors                           : D. Giannoulis, E. Benetos, D. Stowell, and M. D. Plumbley
#   microphone_model                  : Soundman OKM II Klassik/studio A3 electret microphone
#   recording_device_model            : Unknown
#   title                             : IEEE AASP CASA Challenge - Public Dataset for Scene Classification Task
#   url                               : https://archive.org/details/dcase2013_scene_classification
#
# MetaDataContainer :: Class
#   Filename                          : data/DCASE2013-acoustic-scenes-development/meta.txt
#   Items                             : 100
#   Unique
#     Files                           : 100
#     Scene labels                    : 10
#     Event labels                    : 0
#     Tags                            : 0
#
#   Scene statistics
#         Scene label             Count
#         --------------------   ------
#         bus                        10
#         busystreet                 10
#         office                     10
#         openairmarket              10
#         park                       10
#         quietstreet                10
#         restaurant                 10
#         supermarket                10
#         tube                       10
#         tubestation                10
```

- 特征提取
通常，为所有音频文件提取特征并将其存储在磁盘上是最有效的，而不是在每次需要声学特征时都提取它们。 示例如何执行此操作：

```
log.section_header('Feature Extraction')

# Prepare feature extractor
extractor = dcase_util.features.MfccStaticExtractor(
    fs=44100,
    win_length_seconds=0.04,
    hop_length_seconds=0.02,
    n_mfcc=14
)
# Define feature storage path
feature_storage_path = os.path.join('system_data', 'features')

# Make sure path exists
dcase_util.utils.Path().create(feature_storage_path)

# Loop over all audio files in the dataset and extract features for them.
for audio_filename in db.audio_files:
    # Show some progress
    log.line(os.path.split(audio_filename)[1], indent=2)

    # Get filename for feature data from audio filename
    feature_filename = os.path.join(
        feature_storage_path,
        os.path.split(audio_filename)[1].replace('.wav', '.cpickle')
    )

    # Load audio data
    audio = dcase_util.containers.AudioContainer().load(
        filename=audio_filename,
        mono=True,
        fs=extractor.fs
    )

    # Extract features and store them into FeatureContainer, and save it to the disk
    features = dcase_util.containers.FeatureContainer(
        filename=feature_filename,
        data=extractor.extract(audio.data),
        time_resolution=extractor.hop_length_seconds
    ).save()

log.foot()
```

- 特征标准化（Feature normalization）
在这个阶段，每个交叉验证折叠都会通过培训材料，并计算声学特征的均值和标准差。 这些归一化因子用于在特征数据在学习和测试阶段使用之前对其进行归一化。

```
log.section_header('Feature Normalization')

# Define normalization data storage path
normalization_storage_path = os.path.join('system_data', 'normalization')

# Make sure path exists
dcase_util.utils.Path().create(normalization_storage_path)

# Loop over all cross-validation folds and calculate mean and std for the training data
for fold in db.folds():
    # Show some progress
    log.line('Fold {fold:d}'.format(fold=fold), indent=2)

    # Get filename for the normalization factors
    fold_stats_filename = os.path.join(
        normalization_storage_path,
        'norm_fold_{fold:d}.cpickle'.format(fold=fold)
    )

    # Normalizer
    normalizer = dcase_util.data.Normalizer(filename=fold_stats_filename)

    # Loop through all training data
    for item in db.train(fold=fold):
        # Get feature filename
        feature_filename = os.path.join(
            feature_storage_path,
            os.path.split(item.filename)[1].replace('.wav', '.cpickle')
        )

        # Load feature matrix
        features = dcase_util.containers.FeatureContainer().load(
            filename=feature_filename
        )

        # Accumulate statistics
        normalizer.accumulate(features.data)

    # Finalize and save
    normalizer.finalize().save()

log.foot()
```
- 模型学习
在这个阶段，虽然通过交叉验证折叠了训练材料，并且学习和存储声学模型。

```
log.section_header('Learning')

# Imports
from sklearn.mixture import GaussianMixture
import numpy

# Define model data storage path
model_storage_path = os.path.join('system_data', 'model')

# Make sure path exists
dcase_util.utils.Path().create(model_storage_path)

# Loop over all cross-validation folds and learn acoustic models
for fold in db.folds():
    # Show some progress
    log.line('Fold {fold:d}'.format(fold=fold), indent=2)

    # Get model filename
    fold_model_filename = os.path.join(
        model_storage_path,
        'model_fold_{fold:d}.cpickle'.format(fold=fold)
    )

    # Get filename for the normalizer
    fold_stats_filename = os.path.join(
        normalization_storage_path,
        'norm_fold_{fold:d}.cpickle'.format(fold=fold)
    )

    # Normalizer
    normalizer = dcase_util.data.Normalizer().load(filename=fold_stats_filename)

    # Collect class wise training data
    class_wise_data = {}
    for scene_label in db.scene_labels():
        class_wise_data[scene_label] = []

        # Loop through all training items from specific scene class
        for item in db.train(fold=fold).filter(scene_label=scene_label):
            # Get feature filename
            feature_filename = os.path.join(
                feature_storage_path,
                os.path.split(item.filename)[1].replace('.wav', '.cpickle')
            )

            # Load all features.
            features = dcase_util.containers.FeatureContainer().load(
                filename=feature_filename
            )

            # Normalize features.
            normalizer.normalize(features)

            # Store feature data.
            class_wise_data[scene_label].append(features.data)

    # Initialize model container.
    model = dcase_util.containers.DictContainer(filename=fold_model_filename)

    # Loop though all scene classes and train acoustic model for each
    for scene_label in db.scene_labels():
        # Show some progress
        log.line('[{scene_label}]'.format(scene_label=scene_label), indent=4)

        # Train acoustic model
        model[scene_label] = GaussianMixture(
            n_components=8
        ).fit(
            numpy.hstack(class_wise_data[scene_label]).T
        )

    # Save model to the disk
    model.save()

log.foot()
```

- 测试
在这个阶段，测试材料通过每次交叉验证折叠，并且针对每个测试样本估计场景类别。

```
log.section_header('Testing')

# Define model data storage path
results_storage_path = os.path.join('system_data', 'results')

# Make sure path exists
dcase_util.utils.Path().create(results_storage_path)

# Loop over all cross-validation folds and test
for fold in db.folds():
    # Show some progress
    log.line('Fold {fold:d}'.format(fold=fold), indent=2)

    # Get model filename
    fold_model_filename = os.path.join(
        model_storage_path,
        'model_fold_{fold:d}.cpickle'.format(fold=fold)
    )

    # Load model
    model = dcase_util.containers.DictContainer().load(
        filename=fold_model_filename
    )

    # Get filename for the normalizer
    fold_stats_filename = os.path.join(
        normalization_storage_path,
        'norm_fold_{fold:d}.cpickle'.format(fold=fold)
    )

    # Normalizer
    normalizer = dcase_util.data.Normalizer().load(filename=fold_stats_filename)

    # Get results filename
    fold_results_filename = os.path.join(results_storage_path, 'res_fold_{fold:d}.txt'.format(fold=fold))

    # Initialize results container
    res = dcase_util.containers.MetaDataContainer(filename=fold_results_filename)

    # Loop through all test files from the current cross-validation fold
    for item in db.test(fold=fold):
        # Get feature filename
        feature_filename = os.path.join(
            feature_storage_path,
            os.path.split(item.filename)[1].replace('.wav', '.cpickle')
        )

        # Load all features.
        features = dcase_util.containers.FeatureContainer().load(
            filename=feature_filename
        )

        # Normalize features.
        normalizer.normalize(features)

        # Initialize log likelihoods matrix
        logls = numpy.ones((db.scene_label_count(), features.frames)) * -numpy.inf

        # Loop through all scene classes and get likelihood for each per frame
        for scene_label_id, scene_label in enumerate(db.scene_labels()):
            logls[scene_label_id] = model[scene_label].score_samples(features.data.T)

        # Accumulate log likelihoods
        accumulated_logls = dcase_util.data.ProbabilityEncoder().collapse_probabilities(
            probabilities=logls,
            operator='sum'
        )

        # Estimate scene label based on max likelihood.
        estimated_scene_label = dcase_util.data.ProbabilityEncoder(
            label_list=db.scene_labels()
        ).max_selection(
            probabilities=accumulated_logls
        )

        # Store result into results container
        res.append(
            {
                'filename': item.filename,
                'scene_label': estimated_scene_label
            }
        )

    # Save results container
    res.save()
log.foot()
```
- 评估
在这个阶段，系统输出是根据数据集提供的地面实况来评估的。

```
log.section_header('Evaluation')

# Imports
import sed_eval

all_res = []
overall = []
class_wise_results = numpy.zeros((len(db.folds()), len(db.scene_labels())))
for fold in db.folds():
    # Get results filename
    fold_results_filename = os.path.join(
        results_storage_path,
        'res_fold_{fold:d}.txt'.format(fold=fold)
    )

    # Get reference scenes
    reference_scene_list = db.eval(fold=fold)
    for item_id, item in enumerate(reference_scene_list):
        # Modify data for sed_eval
        reference_scene_list[item_id]['file'] = item.filename

    # Load estimated scenes
    estimated_scene_list = dcase_util.containers.MetaDataContainer().load(
        filename=fold_results_filename
    )
    for item_id, item in enumerate(estimated_scene_list):
        # Modify data for sed_eval
        estimated_scene_list[item_id]['file'] = item.filename

    # Initialize evaluator
    evaluator = sed_eval.scene.SceneClassificationMetrics(scene_labels=db.scene_labels())

    # Evaluate estimated against reference.
    evaluator.evaluate(
        reference_scene_list=reference_scene_list,
        estimated_scene_list=estimated_scene_list
    )

    # Get results
    results = dcase_util.containers.DictContainer(evaluator.results())

    # Store fold-wise results
    all_res.append(results)
    overall.append(results.get_path('overall.accuracy')*100)

    # Get scene class-wise results
    class_wise_accuracy = []
    for scene_label_id, scene_label in enumerate(db.scene_labels()):
        class_wise_accuracy.append(results.get_path(['class_wise', scene_label, 'accuracy', 'accuracy']))
        class_wise_results[fold-1, scene_label_id] = results.get_path(['class_wise', scene_label, 'accuracy', 'accuracy'])

# Form results table
cell_data = class_wise_results
scene_mean_accuracy = numpy.mean(cell_data, axis=0).reshape((1, -1))
cell_data = numpy.vstack((cell_data, scene_mean_accuracy))
fold_mean_accuracy = numpy.mean(cell_data, axis=1).reshape((-1, 1))
cell_data = numpy.hstack((cell_data, fold_mean_accuracy))

scene_list = db.scene_labels()
scene_list.extend(['Average'])
cell_data = [scene_list] + (cell_data*100).tolist()

column_headers = ['Scene']
for fold in db.folds():
    column_headers.append('Fold {fold:d}'.format(fold=fold))

column_headers.append('Average')

log.table(
    cell_data=cell_data,
    column_headers=column_headers,
    column_separators=[0, 5],
    row_separators=[10],
    indent=2
)
log.foot()
```
- Results:

Scene | Fold 1 | Fold 2 | Fold 3 | Fold4 | Fold 5 | header 2 | header 2
---|---|---|---|---|---|---|---
row 1 col 1 | row 1 col 2 | row 1 col 2 | row 1 col 2 | row 1 col 2 | row 1 col 2 | row 1 col 2
row 2 col 1 | row 2 col 2




Scene                | Fold 1 | Fold 2 | Fold 3 | Fold 4 | Fold 5 | Average
---|---|---|---|---|---|---
bus                  | 100.00 | 100.00 | 100.00 | 100.00 | 100.00 |  100.00
busystreet           | 100.00 |  33.33 |  33.33 |  100.00|  66.67 |   66.67
office               |  66.67 | 100.00 | 100.00 |  66.67 | 100.00 |   86.67
openairmarket        |  66.67 | 100.00 |  0.00  |  66.67 | 100.00 |   66.67
park                 |  33.33 | 33.33  | 0.00   | 33.33  | 33.33  |   26.67
quietstreet          |  66.67 | 100.00 |  33.33 |  66.67 |  66.67 |   66.67
restaurant           |  66.67 |  0.00  | 66.67  |  0.00  | 33.33  |   33.33
supermarket          |  33.33 |   0.00 |  33.33 |   0.00 |  33.33 |   20.00
tube                 | 100.00 |  33.33 |  33.33 |  66.67 |  66.67 |   60.00
tubestation          |   0.00 |  66.67 |  66.67|   0.00  |   0.00 |   26.67
**Average**          |  63.33 |  56.67 |  46.67 |  50.00 |  60.00 |   55.33
