---
title: DCASE挑战赛原始提案文件（详细信息）
date: 2017-09-02 10:55:59
tags: [DCASE]
categories: [DCASE]
---

本文是根据DCASE2013挑战赛的提案文件，加上个人的理解做了相应的翻译，可能有不对的地方，在之后的会慢慢改善。

# 背景

　　在过去的十年里，人们对在代码公布和公共评估中提出方法的语音和音频处理社区的兴趣越来越浓厚。公共评估可以作为提出方法性能的参考点，也可以用于性能提升的研究。例如，盲源分离和自动音乐音译已经定义，它们已经建立了自己的性能指标，并且每一个都执行公共评估（详见用于信号分离的**[SiSEC](http://sisec.wiki.irisa.fr)**评估的第一个和用于音乐信息检索的**[MIREX](http://www.music-ir.org/mirex)**竞赛的第二个）。然而，对于研究计算听觉场景分析(CASA)领域的研究人员来说，特别是包含非语音和非音乐和探测音频事件的声学场景建模和识别的任务，在这个领域还没有一个统一的国际挑战赛。因此我们建议组织一个对声学事件的检测和分类系统的性能评估的挑战赛。这挑战将帮助这个研究社区在更好地定义具体的任务,向前迈进一步，也将为研究人员提供激励，让他们积极从事这一领域的研究。最后，它将有助于阐明目前存在于该任务中的争议，并为实现部分任务的系统开发提供参考点。

　　我们应该提到的是目前我们提出的最接近的挑战是集中于视听，在录像的多模态事件检测的**TRECVID多媒体事件检测**（详见：[TRECVID 2011 MED Evaluation track]( http://www.nist.gov/itl/iad/mig/med11.cf)）。有研究人员只使用来自TRECVID挑战的音频来评估他们的系统，为音频挑战而明确开发的数据集将提供一个更好的评估框架，因为它在音频方面的变化要大得多。另外，将会产生能够满足音频分析系统对一个更全面评估的需求，并且可能会得到更广泛的应用和成为一个标准的数据集。

　　我们还应该注意到被提出的一个公共评估——**音频分割和扬声器二值化**(详见：[Albayzin 2010音频分割和讲者二化评估任务](http://fala2010.uvigo.es/index.php?option=com_content&view=article&id=60\%3Aaass&catid=36&Itemid=65&lang=en))是建议。这个提出的评估任务包括将一个广播新闻音频文档分割成几个特定的内容课程:音乐，演讲，和音乐/噪音背景音乐或其他。因此，这是一个很解决明确任务的，它与当前的提议没有重叠。

　　最后，一个与本次提议的挑战赛有关的公开评估是在2006年和2007年进行的，是CHIL项目资助的[**CLEAR评估**](http://clear-evaluation.org/)。关于音频、视频或多模式和事件检测的几个任务被踢出，这些是一个“声音事件检测和分类”的评估。这些数据集是在几个交互式研讨会上记录下来的，并包含与研讨会相关的事件(演讲、掌声、椅子移动等)。从为评估而创建的数据集来看，FBK-Irst声学事件数据库([CHIL，“隔离会议室声学事件的FBK-Irst数据库”，欧洲语言资源协会](http://catalog.elra.info/product_info.php?products_id=1093,2008))已经广泛应用事件检测文献中，但是, 前面提到的数据集只包含不重叠的事件。CLEAR评估尽管在当时很有前景和创新性，但是并没有建立一个针对这类任务公认的评估挑战，主要是因为这些数据集仅限于特定类型的事件和声音场景。这些评估随着CHIL项目的结束，已经停止了。

# 挑战赛介绍

　　在计算听觉场景分析(CASA)中有两个密切相关的任务是声学场景分类和在一个场景中检测声音事件。涉及第一个任务的系统的目标是表征或是 “标记”音频录制的环境，但是旨在检测声音事件的系统尝试去将音频分割成音频段，这个音频段能够表示在每个事件有起止时间和需要将它与其他重叠事件分开的特定事件类别的单个事件的。

　　提出挑战赛是为检测在单声道录音中对声学场景和事件进行分类构建一组特定的子挑战集。我们的目标是聚焦于CASA开发系统的科学社区，鼓励分享想法和改进这种艺术的状态，可能会推动系统的发展从而达到接近于人类感知的性能。

　　第一个挑战赛将解决音频场景识别问题，第二个挑战赛解决识别单个声音事件的问题声场景。两个截然不同的实验将用于声音事件识别，一个用于没有重复声音的简单的声学场景中，而另一些场景使用有复调的复杂场景。在日常情境中，大多数的声音都多源的，所以复音场景会更有趣，但也更具挑战性。

# 评估数据

-  数据集
　　总共有**4个数据集**，**一个用于场景分类**，3个用于事件检测。第一个为现场分类(SC)挑战，将包括30秒的各种声音场景。数据集将由两个相同比例的部分组成，一个部分有10个场景，每个场景(类)有10个音频记录组成，总计每个部分的100个录音。其中一个部分将发送给参赛者用于建立并研究他们的性能。他们的系统和其他的系统将被保密，用于训练/测试场景的分类任务。场景是:

![scene_class](/images/scene_class.png)

　　第二个数据集将包含三个子集(一个培训、一个开发和一个测试数据集)。培训 set1将包含每个类的单个事件的实例化。开发(验证)和测试数据集，表示为office live(OL)，将包含大约1分钟的所有日常音频事件的大约1分钟录音。办公环境(不同尺寸和吸引人的房间，房间里不同的人，不同的人噪音水平)。这些录音的音频事件将被注释，它们将包括:

![event_class](/images/event_class.png)

　　将会发布有两种不同的注释，它们分别来自不同的人和都检查一致性和错误。特别是在长软尾巴的情况下，在一些事件的偏移量上人类不可能提取出一个有意义和准确的偏移点，这通常是主观的注释器的观点，即那个事件的偏移量（原文：Especially in the case of long soft tails in the offset of some events it is
humanly impossible to extract a meaningful and accurate offset point and it usually comes down to the subjective
opinion of the annotator where the offset for that event is. ）。因此，包含多个注释将有助于通过允许在复杂的测试过程的中进行一个小的权衡来概括系统评估。欢迎参与者同时使用这两种，这两种都是平均值或者两者之一。测试计划是两个都执行。这训练集将包括每个类别有24个不同的录音，然后是他们在秒上的起始和偏移的注释。开发集将包括来自办公室环境的一系列事件的3份记录。这些记录还将伴随事件的起始和偏移量的注释。第三个将不会发布的数据集将录制所有办公环境中的声音事件，不将这个用于开发集。

　　第三个数据集将包含由IRCAM的分析-合成团队提供的人工排序的声音，即所谓办公室合成(Office Synthetic，OS)。OS任务的数据将由三个如前一个任务一样的子集组成。那训练数据集将由单个事件的音频记录组成，并且将与现实任务相同。开发和测试数据集将包括由个人排序记录建立的人工场景事件(用于培训数据集的不同记录)和通过C4DM提供的背景记录。由于这些数据被**QMUL(伦敦玛丽女王大学)**记录下来，因此可以确保机密性。这个子任务的目的是研究测试算法面对不同层次的复杂性时，比如事件背景能量比，单个事件的重叠程度等时的反应。使用这个数据集的好处是这样的实验比使用真实的录音更容易控制和更真实。除此之外, 即使是复音混合，在不同的声音之间也有很多重叠的地方，地表真值（ground truth）是最精确的。我们期望系统在这个数据集里表现得更好，但是它可以帮助我们基于“增大化现实”技术测量系统性能。


　　第4个也是最后一个数据集将包含由[**IRCAM**](https://www.ircam.fr/)的分析-合成团队提供的人工排序的声音，被称为世界合成(WS)。训练集将包括一系列广泛的音频记录。来自[**FreeSound数据库**](http://www.freesound.org/)预定义事件集合中广泛的不同事件的音频记录。与在OS任务中一样，开发和测试数据集将根据相同类型的不同记录构建事件(也从FreeSound数据库中获取)。在这个任务中没有确保机密性，因为我们需要确保参赛者不使用用于构建测试集的FS样例来训练和开发他们的系统。因为是用于构建测试集的示例的Freesound id将被提供给参赛者。这个子任务的目的是研究在面对更多样化的事件时测试算法的行为背景。待测的的事件类别将包括海洋场景:波浪、鸟、水花、汽艇、雾角，脚步声(沙子，碎石)，孩子们玩耍，风吹，和航行的声音。森林场景:风吹，树叶沙沙作响，脚步声(人类，动物)，鸟叫声，昆虫，哺乳动物叫声，狩猎声音，树枝折断。

> 注意:挑战的所有数据集将在一个知识共享(CC BY)许可下发布。

- 录音设备

　　伦敦玛丽皇后大学数字音乐中心（Centre for Digital Music at Queen Mary University of London）收集环境音频数据专门用于挑战。录音设备包括两种设置。***第一种***是高质量的声场麦克风系统——SPS422B模式（[SPS422B Microphone System](http://www.soundfield.com/products/sps422b.php)），能够清晰地捕捉4声道环绕声音，如果需要的话，也可以在以后的状态中映射到立体声或单声道。***第二种***是一套音效师双耳麦克风（Soundman binaural microphones），OKM II模型（[SoundMan, Binaural Microphone system](http://dev.soundman.de/)），是专门制造以致于他们可以模仿了一对耳朵的可穿戴的耳机。该系统的可移植性和精妙之处在于，用户不会因为录制吸引的环境中人们的注意。因此，我们可以畅通无阻地获取日常的录音。此外, 录音的声音与人类听觉系统的声音非常相似在被与头相关的传输功能(HRTF)过滤后，设备被记录下来。因此, 所得到的数据还携带了关于声音的双耳信息，这些声音可以作为线索声音事件和场景检测从音频或简单的被完全忽略通过将两个通道加在一起获得单声道录音。

　　第一个任务(声音场景分类)的声音文件，用双耳麦克风记录下来，以下规格:PCM、44100 Hz、16位、双通道(CD质量)。声音文件的规范另一项任务是用声场麦克风系统录制的，是双声道立体声(混合从4通道b格式)，44100 Hz，24位。b格式也会和立体声一起发布版本但是挑战将会以立体声而不是b格式运行。挑战的参与者将会混合记录的灵活性。

　　最后要注意的是，在训练和测试中，录音水平都是保持不变的录音和所有任务(在不控制录音条件的情况下)。

# 度量

-  场景识别

　　对于对声音场景进行分类，每个运行单个文件的输出只包含类标签。作为在MIREX训练/测试任务中，将计算的指标将是原始分类(标识) 准确性，每个类的规格化的分类精度，标准偏差，和一个混乱矩阵提交。对于这个测试/测试任务，将使用5倍的交叉验证来评估参与算法。

-  事件检测
　　对于事件检测，将进行三种类型的评估。基于框架的，基于事件的，和类的基于事件的评价。我们相信这两种方法可以对各种不同的方法进行全面的评估系统，基于事件的评估捕获整个事件检测的准确性，以及基于框架的评估提供更详细的细节，每一系统的准确性。

　　每个运行的输出将是一个文件，该文件应该包含启动、偏移和由一个选项卡分隔的事件ID，按时间顺序排列:

![event_dataset](/images/event_dataset.png)

　　基于框架的评估将使用10 ms步骤进行。基于框架的主要度量标准评估将是基于帧的声音事件错误率的版本：`AEER = (D + I + S)/N · 100`。此处N是用于检测特定帧的事件数量时，D是删除(丢失事件)的数量，I 是插入的数量(额外的事件)，S是事件替换的数量，定义为S=min{D,I}。帧级别的度量在记录的持续时间内是平均的。

　　可以使用精度、回忆和f-度量(p-r-f)来给出额外的度量标准。通过表示为r，e，和在给定的10ms框架中，基本事实的数量，估计和正确的事件，前面提到的度量定义为:`Pre = c/e`, `Rec = c/r`,`F = (2 · Pre · Rec)/(Pre + Rec)`。对于基于只有起点的基于事件的评估，每个事件被认为在100毫秒内被正确地检测到。宽容窗口。对于基于起点-偏移事件的评估，如果发生在内部，则每个事件都被正确地检测到。 100ms的容忍窗口和它的偏移量在地面真相事件的50%范围内事件的持续时间。在基于框架的任务中，对于仅有起点和起点-偏移事件检测任务的AEER和p-r-f指标可以相应地定义。还应该注意的是，重复的事件将会是认为是假警报。

　　最后，为了确保重复的事件发生，还将进行类基于事件的基于事件的评估。不控制算法的准确性。该算法的输出将与基于事件的评估相同，但是在AEER和p-r-f指标将分别在一个类中单独计算阶段。记录和将是一个记录的平均accoss。例如，阶级智慧的f度量被定义为:`F = sum(Fk/K)`，Fk表示计算的f-度量，考虑到对类k的检测事件。

# 网站

　　目前,我们正在举办一个[网页的任务](:http://www.elec.qmul.ac.uk/digitalmusic/sceneseventschallenge/),。网页包含了对挑战和一些样本录音的简要描述。在在不久的将来，鉴于任务使它进入下一个步骤，我们将会对指标进行进一步的详细介绍，数据集和整体评估任务是为了促进小组和研究人员之间的讨论表达他们对参与挑战的兴趣，并帮助更好地定义任务的某些方面。

　　这个挑战赛还包括一个[专门的邮件列表来促进讨论](http://www.eecs.qmul.ac.uk/mailman/listinfo/aasp-challenge)

# 联系组

　　在过去的几年里，许多研究领域的研究人员已经取得了联系和通知。对于他们提出的挑战，他们中的大多数已经表达了对参与的兴趣。如果有的话, 这是为了收集来自不同群体和研究人员的兴趣的最初表达来参与这些挑战讨论，以及评估的全部或部分。在讨论挑战和呼吁的过程中参与我们鼓励研究人员表达他们在所有的方面对这一挑战的兴趣并共同努力。

# 时间计划表

　　拟议挑战的时间表如下：

1）2012年6月：对相关邮件列表（AUDITORY，IEEE SPS Newsletter，IEEE AASP会员和关联公司，机器收听）的公开呼吁进行参与和讨论， ）。挑战网站将相应更新。

2）2012年8月：鼓励参与者在有关挑战规范的讨论中作出贡献的截止日期（将为任何与挑战有关的讨论创建一个邮寄名单）。

3）2013年3月：提交代码的截止日期。代码可以由挑战组织者或参与者本身运行。该代码应附有最多3页的工作描述，以IEEE双列会议格式（模板将上传到挑战网站）。

4）2013年5月：WASPAA提交截止日期13.鼓励与挑战有关的小说作者的作者向研讨会提交定期报告。

5）2013年10月：3页面的描述将与评估结果一起公布。作者被邀请提交相机准备版本的说明，反映了评估结果。在WASPAA 13期间，每次提交的作品将由参与者在其中一个常规海报会议期间提交。在同一研讨会上也将进行20分钟的口头介绍和具体挑战的讨论。

> 我们已经联系了WASPAA 2013年的主席，他们已经同意分配一个时间段来展示这个挑战赛和它的结果。由于研讨会的时间表还没有确定，所以我们必须在以后的日子里确定会议的确切形式。

6）2013年11月：邀请选定的参与者在IEEE TASLP / JSTSP关于AASP挑战的特刊中为挑战提交小作品。挑战组织者还将撰写关于挑战和当前趋势的概述文章（本概述文章也可能是信号处理杂志提交的一部分，以提高可见性）。

---
# 参考文献

- [1] D.L. Wang and G. J. Brown (Eds), “Computational auditory scene analysis: Principles, algorithms and applications,” IEEE Press/WileyInterscience, 2006.

- [2] [MIREX Campaign](http://www.music-ir.org/mirex)

- [3] [SiSEC Evaluation](http://sisec.wiki.irisa.fr)

- [4] [TRECVID 2011 MED Evaluation track](http://www.nist.gov/itl/iad/mig/med11.cf)

- [5] [Albayzin 2010 Audio Segmentation and Speaker Diarization Evaluation Task](http://fala2010.uvigo.es/index.php?option=com_content&view=article&id=60\%3Aaass&catid=36&Itemid=65&lang=en)

- [6] J.J. Aucouturier, B. Defreville, and F. Pachet, “The bag-of-frames approach to audio pattern recognition: a sufficient model for urban soundscapes but not for polyphonic music,” Journal of the Acoustical Society of America, Vol. 122, No. 2, pp. 881-891, 2007.

- [7] [CHIL, “FBK-Irst database of isolated meeting-room acoustic events,” European Language Resources Association](http://catalog.elra.info/product_info.php?products_id=1093,2008)

- [8] [CLEAR Evaluation](http://clear-evaluation.org/)

- [9] Corey I. Cheng, and Gregory H. Wakefield, “Introduction to Head-Related Transfer Functions (HRTFs): Representations of HRTFs in Time, Frequency, and Space,” Journal Audio Eng Soc, Vol 49, No 4, 2001 April.
[10] [SPS422B Microphone System](http://www.soundfield.com/products/sps422b.php)
[11] [SoundMan, Binaural Microphone system](http://dev.soundman.de/)

---
# 本文来源

-  文件来源：[http://c4dm.eecs.qmul.ac.uk/sceneseventschallenge/AASP_CASA.pdf](http://c4dm.eecs.qmul.ac.uk/sceneseventschallenge/AASP_CASA.pdf)

-  作者：DCASE2013的组织者(5个)

	- Dimitrios Giannoulis †

		- 机构：Centre for Digital Music, **Queen Mary University of London**, Mile End Rd., London E1 4NS, UK
		- 邮箱：dimitrios@eecs.qmul.ac.uk

	- Emmanouil Benetos §

		- 机构：Department of Computer Science, **City University London**, Northampton Square, London EC1V 0HB, UK
		- 邮箱：emmanouil.benetos.1@city.ac.uk

	- Dan Stowell †

		- 机构：Centre for Digital Music, **Queen Mary University of London**, Mile End Rd., London E1 4NS, UK
		- 邮箱：dans@eecs.qmul.ac.uk

	- Mathias Rossignol ‡

		- 机构：Sound Analysis/Synthesis Team, **IRCAM**, 1 place Igor stravinsky, 75004, Paris, France
		- 邮箱：mathias.rossignol@gmail.com

	- Mathieu Lagrange ‡ 

		- 机构：Sound Analysis/Synthesis Team, [**IRCAM**](https://www.ircam.fr/), 1 place Igor stravinsky, 75004, Paris, France
		- 邮箱：mathieu.lagrange@ircam.fr

	- Mark Plumbley †

		- 机构：Centre for Digital Music, **Queen Mary University of London**, Mile End Rd., London E1 4NS, UK
		- 邮箱：markp@eecs.qmul.ac.uk
