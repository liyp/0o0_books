[Golang](https://golang.org/)
=============================

## 从源码安装Golang

- https://golang.org/doc/install/source

```
# go gccgo
sudo apt-get install gccgo-5

# source
# git clone ...
# cd go/src

# build
export GOROOT_BOOTSTRAP=/usr
GOOS=linux GOARCH=amd64 ./bootstrap.bash
# or
./all.bash
```

If all goes well, it will finish by printing output like:
```
ALL TESTS PASSED

---
Installed Go for linux/amd64 in /home/you/go.
Installed commands in /home/you/go/bin.
*** You need to add /home/you/go/bin to your $PATH. ***
```

设置 `.profile`
```
# go 1.5
export GOROOT=$HOME/etc/go
export PATH=$GOROOT/bin:$PATH

export GOPATH=$HOME/go_work
export PATH=$GOPATH/bin:$PATH
```
