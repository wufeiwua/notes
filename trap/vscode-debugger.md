# vscode 前端调试页面

## 踩坑

官方说的是安装 `Debugger for Chrome`;但是怎么研究怎么也不行。但是我想实现 HBuilder 那样的本地服务。经过一些列研究发现此插件貌似只能打开网页或者调用服务，如果没有启动的服务那就会 `about:black` 然后给一个 localhost 拒绝服务。
官方给的 launch.js 如下:

``` json
{
  "name": "Launch localhost",
  "type": "chrome",
  "request": "launch",
  "url": "http://localhost",
  "webRoot": "${workspaceFolder}",
  "port":8080
}
```

## 爬坑

实际上还是得用其他插件或者node启动一个本地服务

```shell
$ npm install live-server
```

或者搜索插件 `live server`

## 总结
其他 node 服务也可以
