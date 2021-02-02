---
title: linux
date: 2020-06-05 09:36:52
author: TnTZard
tags: linux 
---

```shell
# cp命令用来复制文件。下面的命令将文件file1复制到文件file2：
cp file1 file2
# 下面的命令将多个文件（ file1 ... fileN）复制到目录dir：
cp file1 ... fileN dir
```





## docker

| 参数                    | 作用         |
| ----------------------- | ------------ |
| exec -it <id> /bin/bash | 进入容器内部 |
| ps -a                   |              |
| ps                      |              |
| start                   |              |
| restart                 |              |
| kill                    |              |
|                         |              |
|                         |              |

```doc
# 在Dockerfile中添加，保持容器和宿主机时间同步
RUN /bin/cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime && echo 'Asia/Shanghai' >/etc/timezone
设置docker系统启动：systemctl enable docker
启动docker:  systemctl start docker
查看docker状态： systemctl status docker
停止: systemctl stop docker
重启: systemctl restart docker
查看所有容器列表：docker ps –a
查看启动的容器列表：docker ps
启动容器：docker start 容器ID或容器名
重启容器：docker restart 容器ID或容器名
限时关闭容器：docker -t stop 容器ID或容器名,如60秒以内关闭容器：docker -60 stop,容器60秒以内未关闭，则直接关闭容器。
直接关闭容器：docker kill 容器ID或容器名
删除终止状态的容器：docker rm 容器id
删除运行中的容器：docker rm -f 容器id
停止所有容器：docker stop $(docker ps -a -q)
删除所有终止状态的容器：docker rm $(docker ps -a -q)
查看镜像列表：docker images
删除镜像：docker rmi <image id> 或者docker rmi 镜像名称：版本（TAG）
删除id为none的镜像：docker rmi $(docker images | grep "^<none>" | awk "{print $3}")
删除所有未使用的镜像：docker rmi $(docker images -q)
查看容器日志：docker logs 容器id
```



## grep 

| 参数 | 作用         |
| ---- | ------------ |
| -i   | 不区分大小写 |
| -v   | 反转匹配     |
|      |              |
|      |              |
|      |              |
|      |              |
|      |              |
|      |              |

## less

当要查看的文件过大或者内容多得需要滚动屏幕的时候，可以使用less命令。  

| 参数          | 作用                                  |
| ------------- | ------------------------------------- |
| b/d           | 向前翻一页/向后翻半页                 |
| /<查询字符串> | 向下搜索"字符串"的功能                |
| ?<查询字符串> | 向上搜索"字符串"的功能                |
| 空格键        | 滚动一页                              |
| -N            | 显示每行的行号                        |
| -o <文件名>   | 将less 输出的内容在指定文件中保存起来 |
| [pagedown]    | 向下翻动一页                          |
| [pageup]      | 向上翻动一页                          |
| G/g           |                                       |

```shell
# ps查看进程信息并通过less分页显示
ps -ef | less
# 查看命令历史使用记录并通过less分页显示
history | less
```



## tail

| 参数     | 作用                                  |
| -------- | ------------------------------------- |
| -f       | 循环读取                              |
| -q       | 不显示处理信息                        |
| -v       | 显示详细的处理信息                    |
| -c<数目> | 显示的字节数                          |
| -n<行数> | 显示文件的尾部 n 行内容               |
| -pid     | 与-f合用,表示在进程ID,PID死掉之后结束 |
| -q       | 从不输出给出文件名的首部              |
| -s       | 与-f合用,表示在每次反复的间隔休眠S秒  |

```shell
tail -n 100 /etc/cron  #显示最后100行数据
tail -n -100 /etc/cron #除了前99行不显示外，显示第100行到末尾行
```

| 参数              | 作用                |
| ----------------- | ------------------- |
| HOME/END          | 移动光标到行首/行尾 |
| Page Up/Page Down | 上/下翻页           |
|                   |                     |
|                   |                     |
|                   |                     |
|                   |                     |
|                   |                     |
|                   |                     |

