---
published: true
title: NMF
category: Research
tags: 
  - Research
	
layout: post
---

# Overview

## Definition

>A set of processes is deadlocked if each process in the set is waiting for an event that only another process in the set can cause.

我们可以把resource分成preemtive的和non-preemtive的，如果resource是preemtive的，那么deadlock是不会发生的，只有non-preemtive的资源才可能发生deadlock。

而deadlock也可以分成不同的种类，其中最常见的就是resource deadlock。

## Conditions for resource deadlocks

Coffman等人在1971提出了资源死锁的四种必要条件:
1. Mutual exclusion。资源只能同时被一个process占有而不能被多个processes占有。
2. Hold-and-Wait. 进程正处于一种“已经拥有一定的资源但还在请求其它资源”的状态。
3. No-preemtive. 前面提到的，resource是non-preemtive的。
4. Circular wait. 两个或更多的processes处于某种回路。

## Deadlock Modeling

可以对resource和进程进行建模，如下图所示，可以很清晰的看出来是否有死锁发生（判断是否存在回路）。

![0](https://raw.githubusercontent.com/Logos23333/Logos23333.github.io/master/_posts/image/os/16.png)

正方形表示resource，圆形表示process。图(a)中process A占有R resource,图(b)中process B请求S resource，图(c)中发生了deadlock。

# Deadlock detection and recovery

其实前面还有个鸵鸟算法，英文名叫**ostrich algorithm**，就不提了。

这里要说的是死锁的检测和恢复，这种技术并不是用来避免死锁的发生，而是检测到它的发生之后让它发生然后采取行动恢复。

## Deadlock detection

对于one resource per type来说，检测很简单，判断是否有回路即可，对于multi-resource per type的话就麻烦一点。

![0](https://raw.githubusercontent.com/Logos23333/Logos23333.github.io/master/_posts/image/os/17.png)

左上角的E向量表示系统的全部资源数（包括已经分配的和未分配的），右上角的A向量表示未分配的资源数，左下角的矩阵表示
各个进程已经占有的资源数，右下角的矩阵表示各个进程的请求资源数。

接下来一步步接受资源释放资源即可，很简单。这样就能判断死锁是否会发生了。

## Deadlock recovery

有三种方式recovery：preemption/rollback/killing process

### Recovery through Preemption

这种方法其实就是人为的解除占用：Temporarily take a resource away from its current owner and give it to another process。
缺点是难以实现。

### Recovery through Rollback

**回滚**，系统了解到deadlock有可能发生，于是定期的设置**checkpoint**，process的状态被写进一个新文件里以便在之后发生死锁的时候回滚到之前的状态。

### Recovery through Killing process

通过杀死占有资源的进程来恢复，要么是杀死处于回路的进程，但是杀死回路之外的进程也是有可能的，原因是这样的进程也可能占用资源，一旦将这个资源释放，也有可能recovery。

另外还需要注意的一点是尽量选择那些可以被重新启动的进程，有些进程一旦被杀死就没办法启动了，可能会造成不良后果。

## Deadlock avoidance

前面的deadlock detection是检测到了，在发生之后进行recovery，而deadlock avoidance则是通过判断分配资源给process是否“safe”来避免进入deadlock。

### Resource Trajectories

书本上给出了一种directly、易于理解的图来帮助理解安全状态和不安全状态，如图所示。为了避免进入不安全状态，在t时刻，不能往上走了。

![0](https://raw.githubusercontent.com/Logos23333/Logos23333.github.io/master/_posts/image/os/18.png)

A state is said to be safe if there is some scheduling order in which every process can run to completion even if all of them suddenly request their maximum number of resources immediately.

请注意，不安全状态和死锁状态是有所不同的，处于不安全状态不一定会发生死锁，比如某个进程释放了资源，此时就进入了安全状态，安全状态则是能保证死锁不发生。

### The Banker's Algorithm

其实前面的deadlock detection里判断是否会发生死锁就已经用了银行家算法了，这个算法也挺简单的，就不赘述了。

## Deadlock prevention

虽然deadlock avoidance看起来有效，但实际上它是难以实现的，因为它需要实现知道process需要哪些资源，实际上是不可能的。
我们可以通过攻击deadlock的四种必要条件来prevent deadlock，只要这四个条件的任意之一不满足，死锁就不会发生。

### Attacking the Mutual-Exclusive Condition

让一个资源同时能被两个或多个Processes占用？听起来好像不太可能，但是在某些情况下是可以做到的，比如read data，一个data可以同时被多个Processes读取，但是write就不行。

### Attacking the Hold-and-Wait Condition

攻击持有等待条件，一种做法是：只有当某进程想使用资源并且有足够多的资源让它运行时才分配，否则不分配。有点像mutual exclusion里的atomic operation。但是和deadlock avoidance一样，这也需要事先知道进程所需要的资源
数，实际上是不可能的。并且，对于某些process来说，一次性把资源都给它使用是不现实的。

另一种实现方法是，在process请求资源前，需要把自己已经占有的资源先释放掉。

### Attacking the No-preemtive Condition

这个顾名思义，允许抢占式的资源分配。

### Attacking the Circular Wait Condition

一种实现方法是process只能占用单一的一种资源，如果它想占有第二种资源的话，需要先释放掉第一种资源，这种方法对于某些进程来说适用，有着特殊性。

另一种实现方法是提供一个全局的资源的序号，只能按照序号请求响应的资源，这样就不会Circular Wait了。





研究生国家奖学金是由国家[教育部](https://baike.baidu.com/item/教育部)、[财政部](https://baike.baidu.com/item/财政部/1250873)设立，中央财政出资，用于奖励普通高等学校（以下简称高等学校）中表现优异的[全日制研究生](https://baike.baidu.com/item/全日制研究生/10588952)，为发展中国特色研究生教育，促进研究生培养机制改革，提高研究生培养质量，从2012年9月1日起，中国研究生可享受国家奖学金，每年奖励4.5万名在读研究生，其中，博士研究生1万名，硕士研究生3.5万名。博士研究生国家奖学金奖励标准为每生每年3万元；硕士研究生国家奖学金奖励标准为每生每年2万元。高等学校于每年11月30日前将当年研究生国家奖学金一次性发放给获奖学生，颁发由教育部统一印制的荣誉证书，并记入学籍档案。该奖学金不仅有利于激励研究生“学习成绩优异，科研能力显著，发展潜力突出”，而且有利于国家为各行各业培养大量高素质的研究生人才，从而为“科教强国”战略的实施提供新的有力[支撑](https://baike.baidu.com/item/支撑/9887193)。

- 中文名

  研究生国家奖学金

- 设立时间

  2012年10月22日

- 设立部门

  国家教育部、财政部

- 出资部门

  中央财政

- 用  处

  奖励高校表现优异的全日制研究生

- 实际实行时间

  2012年9月1日

## 目录

1. 1 [简要介绍](https://baike.baidu.com/item/研究生国家奖学金/5451984#1)
2. 2 [设立目的](https://baike.baidu.com/item/研究生国家奖学金/5451984#2)
3. 3 [奖励标准](https://baike.baidu.com/item/研究生国家奖学金/5451984#3)
4. 4 [评选流程](https://baike.baidu.com/item/研究生国家奖学金/5451984#4)
5. 5 [相关点评](https://baike.baidu.com/item/研究生国家奖学金/5451984#5)

## 简要介绍

[编辑](javascript:;)

2012年10月，财政部、教育部联合下发《关于印发<[研究生国家奖学金管理暂行办法](https://baike.baidu.com/item/研究生国家奖学金管理暂行办法)>的通知》。中央财政出资设立研究生国家奖学金，用于奖励普通高等学校(以下简称高等学校)中表现优异的[全日制研究生](https://baike.baidu.com/item/全日制研究生)。

通知称，每年评审一次，所有符合本办法规定条件的攻读硕士、博士学位的全日制研究生均有资格申请。研究生国家奖学金的评审工作，应坚持公开、公平、公正、择优的原则，严格执行国家有关教育法规，杜绝弄虚作假。

通知强调，各省(自治区、直辖市、[计划单列市](https://baike.baidu.com/item/计划单列市))、有关中央主管部门和高等学校必须严格执行国家相关财经法规和本办法的规定，对研究生国家奖学金资金加强管理，专款专用，不得截留、挤占、挪用，并接受财政、审计、纪检监察等部门的检查和监督。 [1] 

[![img](https://gss1.bdstatic.com/9vo3dSag_xI4khGkpoWK1HF6hhy/baike/s%3D220/sign=7564130938292df593c3ab178c305ce2/d000baa1cd11728ba69a5a04cafcc3cec3fd2c65.jpg)](https://baike.baidu.com/pic/研究生国家奖学金/5451984/0/d000baa1cd11728ba69a5a04cafcc3cec3fd2c65?fr=lemma&ct=single)

## 设立目的

[编辑](javascript:;)

2012年10月22日，中国政府网发布《[财政部](https://baike.baidu.com/item/财政部)、[教育部](https://baike.baidu.com/item/教育部)关于印发〈研究生国家奖学金管理暂行办法〉的通知》，为发展中国特色研究生教育，促进研究生培养机制改革，提高研究生培养质量，从2012年9月1日起，实施研究生国家奖学金制度。

## 奖励标准

[编辑](javascript:;)

通知规定，研究生国家奖学金每年奖励4.5万名在读研究生。其中，博士研究生1万名，硕士研究生3.5万名。博士研究生国家奖学金奖励标准为每生每年3万元；硕士研究生国家奖学金奖励标准为每生每年2万元。 [2] 

## 评选流程

[编辑](javascript:;)

**研究生国家奖学金评选流程**

每年3月底前

[全国学生资助管理中心](https://baike.baidu.com/item/全国学生资助管理中心)提出研究生国家奖学金名额分配建议方案，报财政部、教育部审定。

每年4月底前

财政部、教育部将研究生国家奖学金分配名额和预算下达中央主管部门和省级财政、教育部门。

每年5月底前

研究生国家奖学金分配名额和预算下达相关高校。获得推荐的学生，须在所在单位进行5个工作日的公示。

每年11月底前

高等学校将当年研究生国家奖学金一次性发放给获奖学生。 [1] 

## 相关点评

[编辑](javascript:;)

应该说，建立研究生国家奖学金制度，给广大硕士、博士研究生学子提供适当的财政支持，不仅有利于激励研究生“学习成绩优异，科研能力显著，发展潜力突出”，而且有利于国家为各行各业培养大量高素质的研究生人才，从而为“科教强国”战略的实施提供新的有力[支撑](https://baike.baidu.com/item/支撑)。

需要注意的是，研究生国家奖学金制度的建立，特别需要创造一个公平公正的竞争机制，保证每一个获奖者都名符其实。[毋庸讳言](https://baike.baidu.com/item/毋庸讳言)，在巨额奖学金的利益面前，[弄虚作假](https://baike.baidu.com/item/弄虚作假)和故意打压等卑劣做法一定会有。比如，如果优秀的研究生因为没有给导师完成好任务，而埋首于自己的学习与研究，那么就会有导师故意不让申请获得研究生国家奖学金，而推荐那些不是最优秀的研究生获得奖学金。类似的问题，尤其需要规章制度有进一步[明确](https://baike.baidu.com/item/明确)的规定。

财政部、教育部联合下发的这一通知中，第五章是“评审程序”，并用5条具体做出规定。比如，第十五条，“对研究生国家奖学金评审结果有异议的学生，可在基层单位公示阶段向所在基层单位评审委员会提出申诉，评审委员会应及时研究并予以答复。如学生对基层单位作出的答复仍存在异议，可在高校公示阶段向研究生国家奖学金评审领导小组提请[裁决](https://baike.baidu.com/item/裁决)。”但是，这一通知中的《研究生国家奖学金管理暂行办法》却没有对弄虚作假相关当事人和责任人的惩处做出规定。而众所周知，没有惩罚和[问责](https://baike.baidu.com/item/问责)，也就是无法杜绝弄虚作假。

毫无疑问，建立研究生国家奖学金制度的确是一件大大的好事。但要把这个好事实施好，还需要完善的规章制度，特别是申诉、仲裁与惩罚机制，不能为了评选而评选，同时要设定研究生国奖资格门槛，对真正做出高水平的学术研究成果的研究生予以奖励，宁缺毋滥。不然，就会让一些人有可乘之机，把国家的奖学金中饱私囊。故此，笔者希望财政部和教育部能进一步完善《研究生国家奖学金管理暂行办法》，堵住漏洞，真正造福于“学习成绩优异，科研能力显著，发展潜力突出”的研究生。