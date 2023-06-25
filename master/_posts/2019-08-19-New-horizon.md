---
title: "know docker quickly"
date: 2019-08-19
image: /assets/img/blog/IMG_0382.JPG
description: >
  what is docker?
author: author2
comments: true
---

docker!  提供一个“虚拟”的软件运行环境。它能够将你的软件打包成一个镜像系统，通过提供容器来运行镜像文件中相应的软件项目。来达到发布、测试与共享的目的。

# 启动docker

```bash
$ sudo service docker start
```

# 进入根目录

## .dockerignore

类似`.gitignore`，这里可以添加你不希望加入到镜像中的工程目录。

如：

```nginx
.git
.vscode
.idea
node_modules
docs
```

## Dockerfile

```dockerfile
FROM node: 10
COPY ./app
WORKDIR /app
RUN npm install --registry=https://registry.npm.taobao.org
EXPOSE 3000
CMD ["node","app.js"]
```

# 创建镜像

```bash
$ docker image build -t demo
$ docker image build -t demo:1.0.0
```

![docker](/assets/img/blog/docker_images.png)

```bash
$ docker images
$ docker image ls
$ docker image ls -all
$ docker image rm [imageName]
```

# 容器

```bash
$ docker container run --rm -p 3000:3000 -it demo /bin/bash
```

![](/assets/img/blog/active_container.png)

由于在 docker 容器的虚拟环境里面，进程接触到的文件系统和网络接口都是虚拟的，与本机的文件系统和网络接口是隔离的，因此需要定义容器与物理机的端口映射(map)。

```bash
$ docker container ls
$ docker container ls -al
$ docker container  kill [container-id] 
$ docker container  rm [container-id] 
```

![](/assets/img/blog/docker_life.png)

# 发布镜像

此时需要注册并登录[dockerhub](https://hub.docker.com/)

```bash
$ docker login
```

登录成功后就可以将image 镜像文件发布到远端。

![](/assets/img/blog/docker_login.png)

```bash
$ docker image push [username]/[repository]:[tag]
```

对于需要以后继续使用的容器，可以在容器创建时将 `--rm` 去掉，这样便可以通过`docker container start [container_id]`启动原先的容器了，这样就不用每次都创建新的容器了。