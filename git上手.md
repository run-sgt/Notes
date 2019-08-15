## 日常上传：

```
git add .
git commit -m '更新1'
git pull origin master
git push origin master
```



### 密钥路径 

```
C:\Users\Administrator\.ssh
```

### 创建本地工作区

```
git init
```

工作区文件夹下,右键`git bash`

	# 工作区文件夹下,右键git bash
​	git init

### 添加到暂存区

```
添加所有 git add .
添加指定 git add a.txt
```

### 提交到本地版本库

```
git commit -m '提交注释信息'
```

### 查看当前仓库状态、修改记录

```
git status
git log
git log --pretty=oneline
git reflog
```

### 回到历史版本

**上一版本**

```
git reset --hard HEAD^
```

**指定版本**

```
git reset --hard e3e6	
```

### 初次连接`github`

```
git remote add origin git@github.com:sgt-2019/Notes.git
git push -u origin master	
```

### 以后连接`github`

```
git push origin master
```

### 从`github`克隆数据到本地

```
git clone git@github.com:sgt-2019/Notes.git
```

### 创建并切换`dev`分支

```
git checkout -b dev
```

**它实际上是执行了下面两步命令**

**创建`dev`分支**

```
git branch dev
```

**切换到`dev`分支**

```
git checkout dev
```

### 查看当前分支

```
git branch
```

### 将`dev`分支提交的修改与master分支合并

**切换到master分支**

```
git checkout master
```

**合并**	

```
git merge dev
```

### 删除`dev`分支	

```
git branch -d dev
```

