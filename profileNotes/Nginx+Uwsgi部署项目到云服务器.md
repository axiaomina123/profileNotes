### *Nginx+Uwsgi*部署项目到云服务器

前端代码：

1.将代码上传到github上，项目上传过程中如需验证身份  可输入指令

git config --global user.email "you@example.com"
git config --global user.name "Your Name"

![git命令集](D:\Documents\Pictures\git命令集.jpg)

遇到的问题：

在执行git push -u origin master命令时，报错，提示如下：

$ git push -u origin master
git@github.com: Permission denied (publickey).
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists.

解决方法：

登录GitHub账号，在个人中心setting的当中选择SSH and GPG keys

进入如下界面：

![å¨è¿éæå¥å¾çæè¿°](https://img-blog.csdnimg.cn/2019061101513048.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDM5NDc1Mw==,size_16,color_FFFFFF,t_70)

Key从需要上传的项目文件夹的根目录，右键Git Base Here，在命令行中输入ssh-keygen -t rsa -C ["自己的邮箱](mailto:"876818551@qq.com)"。

之后输入命令cat ~/.ssh/id_rsa.pub，复制粘贴key之后保存就可以了。

uwsgi

由于python2和python3支持不同版本的uwsgi， 所以用配置文件启动uwsgi时报错

uwsgi: error while loading shared libraries: libpcre.so.1: cannot open shared object file: No such file or directory

按照网上的方法创建软连接之后依旧报错

解决方法，卸载uwsgi   检查虚拟环境的python版本   为Python3时   执行：pip3 install uwsgi

安装成功后，测试  用配置文件启动即可
