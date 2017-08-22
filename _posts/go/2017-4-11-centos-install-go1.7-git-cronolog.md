
# install git

```
sudo yum install git-all
```

# install go1.7

## method1 

go download page https://golang.org/dl/

```
curl -LO https://storage.googleapis.com/golang/go1.7.5.linux-amd64.tar.gz
```

```
shasum -a 256 go1.7*.tar.gz
```

```
Output

2e4dd6c44f0693bef4e7b46cc701513d74c3cc44f2419bf519d7868b12931ac3 go1.7.5.linux-amd64.tar.gz
```

```
sudo tar -C /usr/local -xvzf go1.7.5.linux-amd64.tar.gz
```

## mehtod2

```
yum install golang
```

## config go env
```
sudo vi /etc/profile.d/path.sh
```

```
export PATH=$PATH:/usr/local/go/bin
```

```
vi ~/.bash_profile
```

```
GOROOT=/usr/local/go
export GOROOT

GOPATH=/data/go
export GOPATH
PATH=$PATH:$HOME/bin:$GOROOT/bin:$GOPATH/bin

export PATH

```

```
source /etc/profile && source ~/.bash_profile
```


# 参考资料

https://www.digitalocean.com/community/tutorials/how-to-install-go-1-7-on-centos-7
