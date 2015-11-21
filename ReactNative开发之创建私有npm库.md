### 搭建本地npm服务器
`Sinopia` 可以创建私有的`npm`注册服务器,利用它你可以你的本机或局域网内的某台服务器上创建属于自己的npm服务器. 它可以缓存已经下载过的`npm`包,当下一次需要这个包时,会自动从本地(或局域网服务器)下载.

#### 安装过程

1. 全局安装`sinopia`
```bash
$ npm install -g sinopia
$ sinopia # 启动sinopia
```
- sinopia的配置文件地址
```
/Users/你的登陆名/.config/sinopia/config.yaml
```
- sinopia的http地址
```
http://localhost:4873/
```
- sinopia的本地缓存路径
```
# config.yaml

# path to a directory with all packages
storage: /Users/你的登陆名/.local/share/sinopia/storage
```
**配置sinopia**

```bash
$ open /Users/你的登陆名/.config/sinopia/config.yaml
```
修改 url 让sinopia使用 淘宝的npm镜像地址
```
# config.yaml

# a list of other known repositories we can talk to
uplinks:
  npmjs:
    url: http://registry.npm.taobao.org/
```
保存配置然后`重新启动sinopia`


2. 全局安装`nrm`—nrm 可以快速修改,切换,增加`npm镜像地址`
```
$ npm install -g nrm
$ nrm add local http://localhost:4873
$ nrm use local
```

- nrm 的其它命令
```
$ nrm --help  # 查看nrm命令帮助
$ nrm list # 列出可用的 npm 镜像地址
$ nrm use taobao # 使用`淘宝npm`镜像地址
```

3. 安装完成.之后你通过npm install 安装的包,sinopia都会帮你缓存到本地了.试一下吧

```
mkdir test && cd test
npm install lodash # sinopia发现本地没有 lodash包,就会从 taobao镜像下载
rm -rf node-modules
npm insatll lodash # 第二次安装就会从缓存下载了,速度很快
```


