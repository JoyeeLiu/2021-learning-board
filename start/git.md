#### 一、Git初始化本地已有项目,并推送到远端Git仓库操作

1. 创建本地项目，在项目根目录执行git init命令
` git init `

2. 在git服务器上创建一个仓库，这里使用GitHub创建一个仓库。
     例如这个git仓库
       https://github.com/ios-zhouyu/VueDemo.git

3. 执行`git remote add origin` ，git path 可以在【Clone or download】中获取。
     `git remote add origin`

      https://github.com/ios-zhouyu/VueDemo.git  

     ` git add . `
     
4. 从远程分支拉取master分支并与本地master分支合并。
     `git pull origin master:master `

5. 提交本地分支到远程分支
     `git push -u origin master`

6. 将现有项目添加并提交上传

      ```
       git add -A
       git commit -m '初始化git项目'
       git push --set-upstream origin master
      ```

#### 二、push

`git add .`

add后面加了一个点，是想要提交所有文件，如果想提交指定的文件，可以写文件名，执行完增加命令后，要执行提交命令，如下：

`git commit –m “NowToDo_v1.0版本”`

`-m`后面跟提示信息，这个提示信息是一定要写的，不仅是规则，同时也方便我们记录我们提交的过程，写清晰为什么提交或修改了什么是非常有用的，提交完成后，我们就要把它推送到远程仓库上去了，命令如下：

`git push git@github.com:bxxfighting/NowToDo.git`

``` git push git@github.com:JoyeeLiu/2021-learning-board.git ```   

#### 三、start主要参考：

开始：

https://www.cnblogs.com/ldq2016/p/5468987.html

分支：

https://www.cnblogs.com/chrischennx/p/6653774.html

个人博客搭建：

https://zhuanlan.zhihu.com/p/28321740



 

  
