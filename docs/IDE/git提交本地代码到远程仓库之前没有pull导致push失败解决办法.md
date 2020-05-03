### git提交本地代码到远程仓库之前没有pull导致push失败解决办法

#### git push错误failed to push some refs to的解决
当我们在github版本库中发现一个问题后，你在github上对它进行了在线的修改；  
或者你直接在github上的某个库中添加readme文件或者其他什么文件，但是没有对本地库进行同步。  
这个时候当你再次有commit想要从本地库提交到远程的github库中时就会出现push失败的问题。

如下图所示
我在github库中对某个文件进行了在线的编辑，并且没有同步到本地库，  
之后我在本地库添加了文件test.txt，并想提交到github，  
出现以下错误：error：failed to push some refs to。
![gitpusherror](../../media/pictures/tools/git/gitpusherror.png)

#### 解决方案
这个问题是因为远程库与本地库不一致造成的，那么我们把远程库同步到本地库就可以了。
使用指令

* git pull --rebase origin master
* 这条指令的意思是把远程库中的更新合并到本地库中，–rebase的作用是取消掉本地库中刚刚的commit，并把他们接到更新后的版本库之中。

![gitpusherror1](../../media/pictures/tools/git/gitpusherror1.png)



> 下面我用图形象的解释下错误情况的发生和解决
![gitpusherror2](../../media/pictures/tools/git/gitpusherror2.png)

> git pull –rebase origin master意为先取消commit记录，并且把它们临时 保存为补丁(patch)(这些补丁放到”.git/rebase”目录中)，之后同步远程库到本地，最后合并补丁到本地库之中

![gitpusherror3](../../media/pictures/tools/git/gitpusherror3.png)

> 接下来就可以把本地库push到远程库当中了。

![gitpusherror4](../../media/pictures/tools/git/gitpusherror4.png)
