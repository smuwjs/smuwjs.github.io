---
title: 分布式版本管理Git
category: 微服务
tag: 基础流程
date: 2016-09-11 11:12:58
---
> Git是一款免费、开源的分布式版本控制系统，用于敏捷高效地处理任何或小或大的项目。
> Git是一个开源的分布式版本控制系统，可以有效、高速的处理从很小到非常大的项目版本管理。Git 是 Linus Torvalds 为了帮助管理 Linux 内核开发而开发的一个开放源码的版本控制软件。

<!--more-->
本节不是Git的教程，而是介绍一下项目中Git的分支策略以及流程管理，同时也涉及一种广泛应用的商用Git工具BitBucket，顺便也会涉及一些代码托管Github以及这两年开始流行的Gitlab的使用方法，当然Github与Gitlab已不单单是版本控制，也包括后文将会提到的知识管理和持续集成。
[Git使用教程](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)
上一张比较经典的分支策略的图，涵盖版本发布，新Sprint开发，bug fix。
![Alt text](http://i1.piimg.com/567571/49e29c353a01bd6b.png)
### 分布式而非集中式
![](http://opesdt6ii.bkt.clouddn.com/17-5-9/97310373-file_1494300279665_15a71.png)
对于这种分支模型，我们设置了一个版本库称为原始库（origin）。每个开发者都对原始库（origin）库拉取（pull）代码和推送（push）代码。但是除了集中式的存取代码关系，每个开发者也可以从子团队的其他队友那里获得代码版本变更。例如，对于2个或多个开发者一起完成的大版本变更，为了防止过早地向origin库提交工作内容，这种机制就变得非常有用。在上述途中，有如下子团队：Alice和Bob，Alice和David，Clair和David。从技术上将，这意味着，Alice创建了一个Git的远程节点，而对于Bob，该节点指向了Bob的版本库，反之亦然。
### 主分支
![Alt text](http://i4.buimg.com/567571/d1de6b8510508e39.png)
中心库有2个分支（branch）：
- master分支
- develop分支

每个Git用户都要熟悉原始的master分支。与master分支并行的另一个分支，我们称之为develop分支。我们把origin/master库认作为主分支，HEAD默认指向master。我们把origin/develop库认为是主分支，该分支HEAD默认指向develop。当develop分支的源码到达了一个稳定状态待发布，所有的代码变更需要以某种方式合并到master分支，然后标记一个版本号。如何操作将在稍后详细介绍。所以，每次变更都合并到了master，这就是新产品的定义，并为之打上标签（tag）。
###辅助性分支
我们的开发模型使用了各种辅助性分支，这些分支与关键分支（master和develop）一起，用来支持团队成员们并行开发，使得易于追踪功能，协助生产发布环境准备，以及快速修复实时在线问题。与关键分支不同，这些分支总是有一个有限的生命期，因为他们最终会被移除。
我们用到的分支类型包括：
- 功能分支
- 发布分支
- 热修复分支

每一种分支有一个特定目的，并且受限于严格到规则，比如：可以用哪些分支作为源分支，哪些分支能作为合并目标。我们马上将进行演练。
从技术角度来看，这些分支绝不是特殊分支。分支的类型基于我们使用的方法来进行分类。它们理所当然是普通的Git分支。
###功能分支（feature）
![Alt text](http://i4.buimg.com/567571/1182a9364475e706.png)
可能是develop分支的分支版本，最终必须合并到develop分支中。
分支命名规则：除了master、develop、release-*或者hotfix-*之外，其他命名均可。
feature分支通常为即将发布或者未来发布版开发新的功能。当新功能开始研发，包含该功能的发布版本在这时还是无法确定发布时间的。feature版本的实质是只要这个功能处于开发状态它就会存在，但是最终会合并到develop分支或取消。功能分支通常存在于开发者的软件库，而不是在源代码库中。
### 创建一个功能分支
开始一项功能的开发工作时，基于develop创建分支。
```
$ git checkout -b myfeature develop
Switched to a new branch "myfeature"
```
### 合并一个功能到develop分支
完成的功能可以合并进develop分支，以明确加入到未来的发布：
```
$ git checkout develop
Switched to branch 'develop'
$ git merge --no-ff myfeature
Updating ea1b82a..05e9557
(Summary of changes)
$ git branch -d myfeature
Deleted branch myfeature (was 05e9557).
$ git push origin develop
```
--no-ff标志导致合并操作创建一个新commit对象，即使该合并操作可以fast-forward。这避免了丢失这个功能分支存在的历史信息，将该功能的所有提交组合在一起。 比较:
![Alt text](http://i4.buimg.com/567571/3f1412e5286bb99e.png)
后一种情况，不可能从Git历史中看到哪些提交一起实现了一个功能，你必须手工阅读全部的日志信息。如果对整个功能进行回退 (比如一组提交)，后一种方式会是一种真正头痛的问题，而使用--no-ff的情况则很容易。
### Release 分支
Release分支可能从develop分支分离而来，但是一定要合并到develop和master分支上，它的习惯命名方式为：release-*。
Release分支是为新产品的发布做准备的。它允许我们在最后时刻做一些细小的修改。他们允许小bugs的修改和准备发布元数据（版本号，开发时间等等）。
从develop分支创建新的Release分支的关键时刻是develop分支达到了发布的理想状态。至少所有这次要发布的features必须在这个点及时合并到develop分支。对于所有未来准备发布的features必须等到Release分支创建以后再合并。
在Release分支创建的时候要为即将发行版本分配一个版本号。直到那时，develop分支反映的变化都是为了下一个发行版，但是在Release分支创建之前，下一个发行版到底叫0.3还是1.0是不明确的。这个决定是在Release分支创建时根据项目在版本号上的规则制定的。
#### 创建一个release分支
Release分支是从develop分支创建的。例如，当前产品的发行版本号为1.1.5，同时我们有一个大的版本即将发行。develop 分支已经为下次发行做好了准备，我们得决定下一个版本是1.2（而不是1.1.6或者2.0）。所以我们将Release分支分离出来，给一个能够反映新版本号的分支名。
```
$ git checkout -b release-1.2 develop
Switched to a new branch "release-1.2"
$ ./bump-version.sh 1.2
Files modified successfully, version bumped to 1.2.
$ git commit -a -m "Bumped version number to 1.2"
[release-1.2 74d9424] Bumped version number to 1.2
1 files changed, 1 insertions(+), 1 deletions(-)
```
创建新分支以后，切换到该分支，添加版本号。这里，bump-version.sh 是一个虚构的shell脚本，它可以复制一些文件来反映新的版本（这当然可以手动改变--目的就是修改一些文件）。然后版本号被提交。
这个新分支可能会存在一段时间，直到该发行版到达它的预定目标。在此期间，bug的修复可能被提交到该分支上（而不是提交到develop分支上）。在这里严格禁止增加大的新features。他们必须合并到develop分支上，然后等待下一次大的发行版。
#### 完成一个release分支
当一个release分支准备好成为一个真正的发行版的时候，有一些工作必须完成。首先，release分支要合并到master上（因为每一次提交到master上的都是一个新定义的发行版，记住）。然后，提交到master上必须打一个标签，以便以后更加方便的引用这个历史版本。最后，在release分支上的修改必须合并到develop分支上，以便未来发行版也包含这些bugs的修复。
```
$ git checkout master
Switched to branch 'master'
$ git merge --no-ff release-1.2
Merge made by recursive.
(Summary of changes)
$ git tag -a 1.2
```
为了是修改保持在release分支上，我们需要合并这些到develop分支上去。
```
$ git checkout develop
Switched to branch 'develop'
$ git merge --no-ff release-1.2
Merge made by recursive.
(Summary of changes)
```
这个步骤可能会导致合并冲突（可能由于改变版本号更是如此）。如果是这样，修复它然后提交。
现在我们真正的完成了，这个release分支将被删除，因为我们不再需要它了。
```
$ git branch -d release-1.2
Deleted branch release-1.2 (was ff452fe).
```
### 热修复分支
![Alt text](http://i4.buimg.com/567571/43750bfba0712739.png)
可以基于master分支，必须合并回develop和master分支。
分支名约定：hotfix-*
热修复分支与发布分支很相似，他们都为新的生成环境发布做准备，尽管这是未经计划的。他们来自生产环境的处于异常状态压力。当生成环境验证缺陷必须马上修复是，热修复分支可以基于master分支上对应与线上版本的tag创建。
其本质是团队成员（在develop分支上）的工作可以继续，而另一个人准备生产环境的快速修复。
#### 创建修补bug分支
hotfix branch（修补bug分支）是从Master分支上面分出来的。例如，1.2版本是当前生产环境的版本并且有bug。但是开发分支（develop）变化还不稳定。我们需要分出来一个修补bug分支（hotfix branch）来解决这种情况。
```
$ git checkout -b hotfix-1.2.1 master
Switched to a new branch "hotfix-1.2.1"
$ ./bump-version.sh 1.2.1
Files modified successfully, version bumped to 1.2.1.
$ git commit -a -m "Bumped version number to 1.2.1"
[hotfix-1.2.1 41e61bb] Bumped version number to 1.2.1
1 files changed, 1 insertions(+), 1 deletions(-)
```
分支关闭的时侯不要忘了更新版本号(bump the version)
然后，修复bug，一次提交或者多次分开提交。
```
$ git commit -m "Fixed severe production problem"
[hotfix-1.2.1 abbe5d6] Fixed severe production problem
5 files changed, 32 insertions(+), 17 deletions(-)
```
#### 完成一个hotfix分支
完成一个bugfix之后，需要把bugfix合并到master和develop分支去，这样就可以保证修复的这个bug也包含到下一个发行版中。这一点和完成release分支很相似。
首先，更新master并对release打上tag：
```
$ git checkout master
Switched to branch 'master'
$ git merge --no-ff hotfix-1.2.1
Merge made by recursive.
(Summary of changes)
$ git tag -a 1.2.1
```
下一步，把bugfix添加到develop分支中：
```
$ git checkout develop
Switched to branch 'develop'
$ git merge --no-ff hotfix-1.2.1
Merge made by recursive.
(Summary of changes)
```
规则的一个例外是： 如果一个release分支已经存在，那么应该把hotfix合并到这个release分支，而不是合并到develop分支。当release分支完成后， 将bugfix分支合并回release分支也会使得bugfix被合并到develop分支。（如果在develop分支的工作急需这个bugfix，等不到release分支的完成，那你也可以把bugfix合并到develop分支）
最后，删除临时分支：
```
$ git branch -d hotfix-1.2.1
Deleted branch hotfix-1.2.1 (was abbe5d6).
```