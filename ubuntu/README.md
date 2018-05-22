## myUbuntu16 dockerfile
这是一个Ubuntu16.04的dockerfile
修改自docker[ubuntu](https://store.docker.com/images/ubuntu),[dockerfile地址](https://github.com/tianon/docker-brew-ubuntu-core/blob/dist-amd64/xenial/Dockerfile)

### 添加的内容
* locale设置为zh_CN.UTF-8
* 中文字体（文泉驿）
* 时区为Shanghai
* Python2，Python3环境
* vim、curl、wget、tree
