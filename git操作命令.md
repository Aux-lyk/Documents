### 1.本地新建分支 

```javascript
git checkout -b 分支名 //创建+切换
git branch 分支名      //创建
git checkout 分支名    //切换
```

### 2.将本地新建分支push到服务器上 

```javascript
git push -u origin 分支名
```

### 3.查看所有本地或远端分支 

```javascript
git branch -a  //查看本地
git branch -r  //查看远端
git pull       //更新分支
```

### 4.从远端获取项目 

```javascript
git clone SSH地址或HTTP地址
```

### 5.提交代码：

```javascript
git add * //将工作区内容添加到暂存区
git add 文件1 文件2 //添加指定文件
git add .  //添加所有文件

git commint -m '版本描述信息' //将暂存区内容提交到版本库

git pull origin 分支名   //从远程更新内容到本地
git push origin 分支名   //将本地推送到远程
```
### 6.删除远程库上多余的分支

```javascript
git branch -d 分支名              //删除本地
git branch -r -d origin/分支名    //删除暂存区
git push origin --delete 分支名   //删除远程库
```

### 7.提交远程或更新到本地冲突的解决

- 冲突的时候文件内会有：
- <<<<<<< HEAD 到 ======= 中间的内容是local提交的。
- ======= 到 >>>>>>> commit-id 是远程仓库中的内容。
- 手动选择你要的部分，然后把这些边界代码删掉后

```javascript
git reset --hard HEAD    //撤销merge
//或
git merge --abort
```

- 然后执行提交远程或更新到本地就可以了


### 8.版本回退

```javascript
//获取commitid
git log
//回退
git reset --hard commitid
//将修改push到远程服务器分支上
git push -f -u origin 分支名
```

### 9.git的分支与合并

```
//开发分支 bugFix_develop 合并到 develop 分支

//处于develop分支
//更新develop代码
git pull origin develop
//合并bugFix_develop分支
git merge bugFix_develop
//本地合并后提交到远程
git push
```

