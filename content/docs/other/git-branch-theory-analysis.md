---
weight: 3
bookFlatSection: true
title: "Git分支原理解析"
---

### 前言
1991年[Linus Torvalds](https://baike.baidu.com/item/Linus%20Torvalds/9336769)发明了linux内核,从此不断发展壮大,成为当前最流行操作系统~~之一~~
2002年,linus选用商业版本控制系统BitKeeper——Bitmover公司,来管理代码
2005年,linux花费十天时间用C写了一个分布式版本控制系统——git,一个月之内linux源码已用git管理
2016年,BitKeeper开源
### git分支
```
// 进行一次提交,看git的工作原理
$ git add README test.rb LICENSE
$ git commit -m 'The initial commit of my project'
// 一个commit对象 包含指向快照的指针,作者姓名,邮箱,提交信息等,还包括指向树对象的指针
// 现在，Git 仓库中有五个对象：
// 三个 blob 对象（保存着文件快照）
// 一个 tree 对象 （记录着目录结构和 blob 对象索引）
// 一个commit对象 包含指向快照的指针,作者姓名,邮箱,等所有提交信息,还包括指向tree对象的指针,父对象的指针
```
![image.png](/images/1595494028041-788d4657-c2be-4e5a-9d4d-b9afa43832d2.png)


第一次提交没有父对象,之后提交父对象指向上次提交
![image.png](/images/1595494094005-4d7ad2dd-6eab-4b9a-aa0a-83e20888723c.png)
### 创建分支
```
$ git branch testing
```
![image.png](/images/1595494106162-948db8a5-9d42-4f07-b281-d6f97db8723b.png)
git有一个特殊的HEAD指针,指向当前分支
![image.png](/images/1595494116171-e7bd5123-fee4-423f-a50f-ec7764622536.png)
```
$ git switch testing //切换分支只是更改了HEAD指针的指向
```


![image.png](/images/1595494125592-0229c7ef-e6eb-4b86-a881-825355ea4948.png)
```
// 不妨提交一次
$ vim test.rb
$ git commit -a -m 'made a change'
// testing分支向前移动
```
![image.png](/images/1595495360507-8efe5af9-7885-40b7-8519-67a34c99a611.png)
```
// 切回master分支, HEAD指向master,工作区恢复到master所指向的快照内容
$ git switch master
```
![image.png](/images/1595495455513-3f59000c-5e6c-4c01-9e9b-5eff3d5d210d.png)
```
// 不妨再做次提交
$ vim test.rb
$ git commit -a -m 'made other changes'
// 出现了历史性的分叉
```
![image.png](/images/1595495606034-991bad49-99ae-4f29-b032-7fdb0e6c9cd7.png)
### 分支合并
![image.png](/images/1595496208856-26c6fd6e-e4f2-4e6c-b009-62774cc1d91e.png)
```
// 新建一个分支iss53,并切换到该分支
$ git checkout -b iss53
Switched to a new branch "iss53"
```
![image.png](/images/1595496260273-90e10a20-7c47-4086-bd8a-292ef180ce0a.png)
```
$ vim index.html
$ git commit -a -m 'added a new footer [issue 53]'
// 做一次提交,iss53向前推进
```
![image.png](/images/1595496290078-34ac726c-6e5f-412d-817e-47802ba6a0aa.png)
```
$ git checkout master
$ git checkout -b hotfix
$ git commit -a -m 'fixed the broken email address'
 // 切回master分支
 // 创建新分支hotfix,并切换到该分支
 // 做次提交
```
![image.png](/images/1595496620651-f0581364-9444-4283-9f48-171485070a32.png)
```
$ git checkout master
$ git merge hotfix
// 切换到master, 合并hotfix分支
// 当你试图合并两个分支时， 如果顺着一个分支走下去能够到达另一个分支
// 那么 Git 在合并两者的时候， 只会简单的将指针向前推进（指针右移）
// 因为这种情况下的合并操作没有需要解决的分歧——这就叫做 “快进（fast-forward）”。
```
![image.png](/images/1595496652662-8a322c46-bff5-4a11-a388-fbb2eb915990.png)
```
$ git branch -d hotfix
// 合并过后该分支已无用,应删除
```
```
$ git checkout iss53
$ git commit -a -m 'finished the new footer [issue 53]'
// 切回iss53分支,继续我们的工作
```
![image.png](/images/1595496764111-ab43fef2-9e78-4f47-828c-87e5cddea568.png)
```
$ git checkout master
$ git merge iss53
// 现已经修复了iss53,把它合并到master上
```
![image.png](/images/1595496853355-a5ebaee0-dfdf-48c1-8bec-669853ca77b4.png)
你的开发历史从一个更早的地方开始分叉开来（diverged）。 因为，`master` 分支所在提交并不是 `iss53` 分支所在提交的直接祖先，Git 不得不做一些额外的工作。 出现这种情况的时候，Git 会使用两个分支的末端所指的快照（`C4` 和 `C5`）以及这两个分支的公共祖先（`C2`），做一个简单的三方合并。
![image.png](/images/1595497039584-4f27a639-fe4c-46f1-9ed7-85b733bb3aab.png)
```
$ git branch -d iss53
// 删除该分支
```
### 合并方式


```
$ git merge --no-ff {branch}
// 使用--no-ff参数后，会执行正常合并，在Master分支上生成一个新节点。为了保证版本演进的清晰，我们希望采用这种做法
```


![image.png](/images/1595498418942-938fa146-a441-4bba-9b27-c56ffc940e49.png)![image.png](/images/1595498426283-2ed721a5-7860-4960-9291-9d95bcdd4623.png)

