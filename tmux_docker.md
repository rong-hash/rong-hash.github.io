# tmux and docker

最近慢慢在开始做些实验了，但是总是苦于实验并行度不高，然后慢慢尝试发现了tmux+docker的组合。

如果只有docker的话，往往detach之后重新进入就看不到之前的历史记录了。虽然可以写log到文件，但还是没有那么方便。

但是tmux就可以很好解决这个问题，开多个session，每个session里面用docker exec进入容器，可以实现多terminal的session管理。

结合两个工具用的还是挺爽的，比较适合需要开同个容器多次做实验的场景。
