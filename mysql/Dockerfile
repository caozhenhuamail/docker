[docker sudo]


FATA[0000] Get http:///var/run/docker.sock/v1.18/images/json: dial unix /var/run/docker.sock: permission denied. Are you trying to connect to a TLS-enabled daemon without TLS?

于是考虑如何免 sudo 使用 docker，经过查找资料，发现只要把用户加入 docker 用户组即可，具体用法如下。

免 sudo 使用 docker

如果还没有 docker group 就添加一个：

sudo groupadd docker
将用户加入该 group 内。然后退出并重新登录就生效啦。

sudo gpasswd -a ${USER} docker
重启 docker 服务

sudo service docker restart
切换当前会话到新 group 或者重启 X 会话

newgrp - docker

OR

pkill X
注意，最后一步是必须的，否则因为 groups 命令获取到的是缓存的组信息，刚添加的组信息未能生效，所以 docker images 执行时同样有错。

原因分析

因为 /var/run/docker.sock 所属 docker 组具有 setuid 权限

$ sudo ls -l /var/run/docker.sock
srw-rw---- 1 root docker 0 May  1 21:35 /var/run/docker.sock


获取官方仓库镜像

Docker中得pull命令是用来获取镜像的，执行下面的命令，就会从官方仓库里获取Ubuntu 14.04版本的系统:
docker pull ubuntu:14.04


运行镜像
docker run -it ubuntu:14.04

进入镜像后安装nginx, 不会对宿主机器有任何影响
sudo apt-get install -y nginx

nginx -v
nginx version: nginx/1.4.6 (Ubuntu)

exit 退出终端；

查看运行的镜像进程

ps命令可以查看我们当前都运行了哪些容器，加上-a参数后就表示运行过哪些容器，因为我们刚刚已经退出了安装nginx的容器，因此我现在想查看它的话，需要使用-a参数，执行如下命令:
docker ps -a 

richard@richard:~$ docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                      PORTS               NAMES
739fe74b5eb8        ubuntu:14.04        "/bin/bash"         4 minutes ago       Exited (0) 51 seconds ago           fervent_mcclintock   
9dd1ee9025cf        training/sinatra    "/bin/bash"         5 days ago          Exited (0) 4 days ago                           adoring_meitner      
700e60663a96        ubuntu:12.04        "/bin/bash"         5 days ago          Exited (0) 5 days ago                           furious_sinoussi     
669e25142b0d        hello-world         "/hello"            5 days ago          Exited (0) 5 days ago                           sleepy_cray 

提交镜像

commit命令用来将容器转化为镜像，运行下面的命令，我们可以讲刚刚的容器转换为镜像:

sudo docker commit -m "Added nginx from ubuntu14.04" -a "richard" 739fe74b5eb8 richard/ubuntu-nginx:v1

其中，-m参数用来来指定提交的说明信息；-a可以指定用户信息的；79c761f627f3代表的时容器的id；richard/ubuntu-nginx:v1
指定目标镜像的用户名、仓库名tag 信息。创建成功后会返回这个镜像的 ID 信息。注意的是，你一定要将richard 改为你自己的用户名。因为下文还会用到此用户名
richard@richard:~$ sudo docker commit -m "Added nginx from ubuntu14.04" -a "richard" 739fe74b5eb8 richard/ubuntu-nginx:v1
[sudo] password for richard: 
5c3b99e52ed74ffa83b0220b083eba29f4dd176a70f6deffbd87fc7cb3b709b1



查看本地镜像

这时我们再次使用docker images命令就会发现此时多出了一个我们刚刚创建的镜像:
richard@richard:~$ docker images
REPOSITORY             TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
richard/ubuntu-nginx   v1                  5c3b99e52ed7        43 seconds ago      206.5 MB
hello-world            latest              975b84d108f1        7 days ago          960 B
ubuntu                 14.04               a005e6b7dd01        8 days ago          188.4 MB
ubuntu                 12.04               61994089e28e        12 days ago         135.4 MB
training/sinatra       latest              f0f4ab557f95        16 months ago       447 MB



此时，如果运行docker run -it richard/ubuntu-nginx:v1就会是一个已经安装了nginx的容器:

存储镜像
我们刚刚已经创建了自己的第一个镜像，尽管它很简单，但这已经非常棒了，现在，我们希望它能够被更多的人使用到，此时，我们就需要将这个镜像上传到镜像仓库，Docker的官方Docker Hub应该是目前最大的Docker镜像中心，所以，我们就将我们的镜像上传到Docker Hub。
首先，我们需要成为Docker Hub的用户，前往https://hub.docker.com/进行注册。需要注意的是，为了方便下面的操作，你需要将你的用户名设为和我刚刚在上文提到的自定义用户名相同，例如我的刚刚将镜像的名字命名为是caozhenhua/ubuntu-nginx:v2,所以我的用户名为caozhenhua、注册完成后记住用户名、密码、邮箱。
login默认是用来登陆Docker Hub的，因此，输入如下命令来尝试登陆Docker Hub:
docker login
此时，就会输出交互，让我们输入Username、Password、Email,成功输入我们刚才注册的信息后就会返回Login Success提示:
richard@richard:~$ docker login
Username: caozhenhua
Password: 
Email: caozhenhua@parllay.cn
WARNING: login credentials saved in /home/richard/.docker/config.json
Login Succeeded

运行命令:
docker push caozhenhua/ubuntu-nginx:v1
这就是我们为什么将刚刚的镜像命名为caozhenhua/ubuntu-nginx:v1的原因，如果你上面步骤都操作正确的正确的话，是会得到下面的内容:此时，不出意外的话，我们的镜像已经被上传到Docker Hub上面了，去Docker Hub上面看看：

果然，我们在Docker Hub上有了我们的第一个镜像，此时，其它的用户就可以通过命令docker pull caozhenhua/ubuntu-nginx来直接获取一个安装了nginx的ubuntu系统了。不信？那就自己实践一下吧！

Dockerfile使用

----------------------------------------------------------------------
如何使用
Dockerfile用来创建一个自定义的image,包含了用户指定的软件依赖等。当前目录下包含Dockerfile,使用命令build来创建新的image,并命名为edwardsbean/centos6-jdk1.7:
docker  build -t edwardsbean/centos6-jdk1.7  .
Dockerfile关键字
如何编写一个Dockerfile,格式如下：
# CommentINSTRUCTION arguments
FROM
基于哪个镜像
RUN
安装软件用
MAINTAINER
镜像创建者
CMD
container启动时执行的命令，但是一个Dockerfile中只能有一条CMD命令，多条则只执行最后一条CMD.
CMD主要用于container时启动指定的服务，当docker run command的命令匹配到CMD command时，会替换CMD执行的命令。如:
Dockerfile:
CMD echo hello world
运行一下试试:
edwardsbean@ed-pc:~/software/docker-image/centos-add-test$ docker run centos-cmd
hello world
一旦命令匹配：
edwardsbean@ed-pc:~/software/docker-image/centos-add-test$ docker run centos-cmd echo hello edwardsbean
hello edwardsbean
ENTRYPOINT
container启动时执行的命令，但是一个Dockerfile中只能有一条ENTRYPOINT命令，如果多条，则只执行最后一条
ENTRYPOINT没有CMD的可替换特性
USER
使用哪个用户跑container
如：
ENTRYPOINT ["memcached"]
USER daemon
EXPOSE
container内部服务开启的端口。主机上要用还得在启动container时，做host-container的端口映射：
docker run -d -p 127.0.0.1:33301:22 centos6-ssh
container ssh服务的22端口被映射到主机的33301端口
ENV
用来设置环境变量，比如：
ENV LANG en_US.UTF-8
ENV LC_ALL en_US.UTF-8
ADD
将文件<src>拷贝到container的文件系统对应的路径<dest>
所有拷贝到container中的文件和文件夹权限为0755,uid和gid为0
如果文件是可识别的压缩格式，则docker会帮忙解压缩
如果要ADD本地文件，则本地文件必须在 docker build <PATH>，指定的<PATH>目录下
如果要ADD远程文件，则远程文件必须在 docker build <PATH>，指定的<PATH>目录下。比如:
docker build github.com/creack/docker-firefox
docker-firefox目录下必须有Dockerfile和要ADD的文件
注意:使用docker build - < somefile方式进行build，是不能直接将本地文件ADD到container中。只能ADD url file.
ADD只有在build镜像的时候运行一次，后面运行container的时候不会再重新加载了。
VOLUME
可以将本地文件夹或者其他container的文件夹挂载到container中。
WORKDIR
切换目录用，可以多次切换(相当于cd命令)，对RUN,CMD,ENTRYPOINT生效
ONBUILD
ONBUILD 指定的命令在构建镜像时并不执行，而是在它的子镜像中执行

-------------------------------------------------------------------

通过上面的学习，我们掌握了如何创建镜像、获取镜像、上传镜像、运行容器等等内容。有了上面的知识，我们来次实战。
我们刚刚使用了commit命令创建了一个安装nginx的镜像，但其实Docker创建镜像的命令还有build,build命令可以通过指定一个Dockerfile文件来实现将镜像创建过程自动化。Dockerfile文件有着特定的编写规则，但语法都还比较容易理解。这次我们不仅使用Dockerfile文件来创建一个像上文一样安装nginx的ubuntu镜像，还要发挥nginx的老本行来运行一个网页吧！DockFile可以很轻松的完成这个问题。首先将新建一个名字为www的文件夹，文件夹下面可以放一些HTML网页，比如新建一个index.html文件，随便写点内容:
<html><head>Learn Docker</head><body><h1>Enjoy Docker!</h1></body></html>
在www的同级目录下新建一个名为Dockerfile的文件，将DockerFile文件改写如下：
FROM ubuntu:14.04
MAINTAINER caozhenhua caozhenhua@parllay.cn
RUN apt-get update
RUN apt-get install -y nginx
COPY ./www /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
我来整体的解释下这个Dockerfile文件，第一行是用来声明我们的镜像是基于什么构建的，这里我们指定为ubuntu14.04 ,第二行的作用在于告诉别人你的大名。第三行和第四行的RUN命令用来在容器内部的shell里执行命令。第五行将当前系统的www文件夹拷贝到容器的/usr/share/nginx/html目录下，第六行声明当前需要对外开放80端口，最后一行表示运行容器时开启nginx。不理解没关系，因为这都是固定的语法，感兴趣可以多看相关内容。此时我们通过build命令来构建镜像，运行：
docker build -t="caozhenhua/ubuntu-nginx:v2" .
注意，最后的.表示Dockerfile在当前目录，也可指定其它目录。此时，再次运行docker images就会看到刚刚生成的镜像：
richard@richard:~/www$ docker images
REPOSITORY                TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
caozhenhua/ubuntu-nginx   v2                  8473f97792be        11 seconds ago      206.5 MB
<none>                    <none>              1dd2944195cf        About an hour ago   188.4 MB
caozhenhua/ubuntu-nginx   v1                  091cdb9e1b72        2 hours ago         206.5 MB
richard/ubuntu-nginx      v1                  5c3b99e52ed7        2 hours ago         206.5 MB
hello-world               latest              975b84d108f1        7 days ago          960 B
ubuntu                    14.04               a005e6b7dd01        8 days ago          188.4 MB
ubuntu                    12.04               61994089e28e        12 days ago         135.4 MB
training/sinatra          latest              f0f4ab557f95        16 months ago       447 MB
现在我们就可以运行刚刚的镜像了，和前面运行稍有不同，此时我们需要对外指定80端口，该行为通过-p参数指定，运行:
docker run -p 80:80 caozhenhua/ubuntu-nginx:v2
此时，终端会卡住，这是正常的，因为Docker的思想是每个容器最好只开一个线程做一件事，此时我们打开了nginx服务器，所以终端卡住也没关系（当然是有办法来解决这个问题，但这里不做介绍）。现在我们可以通过浏览器访问localhost查看效果，如果是虚拟主机则需输入主机ip地址,然后就能看到了如下的页面:



docker 日志


richard@richard:~/www$ docker ps -l
CONTAINER ID        IMAGE                        COMMAND                CREATED             STATUS              PORTS                  NAMES
6a6e7e066cf0        caozhenhua/ubuntu-nginx:v2   "nginx -g 'daemon of   5 minutes ago       Up 5 minutes        0.0.0.0:8081->80/tcp   sad_swartz   
       
richard@richard:~/www$ docker logs -f -t --tail="all" 6a6e7e066cf0　［CONTAINER ID］
