### Dockerfile最佳实践



##### 1. dockerfile介绍

- Dockerfile描述了组装镜像的步骤，其中每条指令都是单独执行的。除了FROM指令，其他每一条指令都会在上一条指令所生成镜像的基础上执行，执行完后会生成一个新的镜像层，新的镜像层覆盖在原来的镜像之上从而形成了新的镜像。Dockerfile所生成的最终镜像就是在基础镜像上面叠加一层层的镜像层组建的。

##### 2. Dockerfile指令

- 基本格式

  ```
  #Comment
  指令 参数
  ```

  Docker会顺序执行Dockerfile中的指令，第一条指令必须是FROM指令，用于指定构建镜像的基础镜像。在Dockerfile中以#开头的行是注释，而在其他位置出现的#会被当成参数。

- Dockerfile中指令有：FROM、MAINTAINER、RUN、CMD、EXPOSE、ENV、ADD、COPY、ENTRYPOINT、VOLUME、USER、WORKDIR、ONBUILD，错误指令会被忽略。

- ENV

  ```
  格式：ENV <key> <value> 或者 ENV <key>=<value>
  ```

  ENV为镜像创建出来的容器声明环境变量。使用格式：$variable_name 或者 ${variable_name}

- FROM

  ```
  格式： FROM <image> 或者 FROM <image>:<tag>
  ```

  FROM指令的功能是为后面的指令提供基础镜像，因此一个有效的Dockerfile必须以FROM指令作为第一条非注释指令

- COPY

  ```
  格式： COPY <src> <dest>
  ```

  COPY指令本地文件复制，复制 src 所指向的文件或者目录，将它添加到新镜像中，复制的文件或目录在镜像中的路径是 dest

- ADD

  ```
  格式：ADD <src> <dest>
  ```

  ADD指令 本地文件复制，网络文件复制，src 指向本地压缩归档文件，该文件在复制到容器中时会被解压提取，但是若URL中的文件为归档文件则不会被解压提取。

  ```
  eg.
  ADD http://xxx.com/hello.txt / 
  会创建 /hello.txt
  
  ADD xxx.tar.gz /
  镜像内会解压提取
  ```

- RUN

  ```
  RUN <command> (shell格式)
  RUN ["executable", "param1", "param2"] （exec格式，推荐格式）
  ```

- CMD

  ```
  CMD <command> （shell格式）
  CMD ["executable", "param1", "param2"] （exec格式，推荐格式）
  CMD ["param1", "param2"] (为ENTRYPOINT)
  ```

  CMD指令提供容器运行时默认值，这些默认值可以是一条指令，也可以是一些参数。一个Dockerfile中可以有多条CMD指令，但只有最后一条CMD指令有效。

  RUN和CMD区别：RUN指令在构建镜像时执行命令，并生成新的镜像，CMD在构建镜像时，并不执行任何命令，而是在容器启动时默认将CMD指令作为第一条执行的命令。如果用户在命令行界面运行docker run 命令时指定了命令参数，则会覆盖CMD指令中的命令。

- ENTRYPOINT

  ```
  ENTRYPOINT <command> （shell格式）
  ENTRYPOINT ["executable", "param1", "param2"] (exec格式，推荐格式)
  ```

  
