http://192.168.1.57:7080/workspaces

coder 账号 `docs@rua.plus` 专门为文档创作设置。

docker `coder-port` 用来专门为 coder 转发端口的容器。

```shell
docker run --name=coder-port --restart=unless-stopped --network=host -itd ubuntu
```

在启动后只需要安装下 coder，然后登录到对应的 coder 服务器，最后使用 coder 提供的转发端口服务。

```shell
coder port-forward docs/showyun-docs --tcp 0.0.0.0:5000:3000
```

```shell
http://192.168.1.57:7080/

docs@rua.plus

http://192.168.1.57:7080/@docs/showyun-docs.main/apps/code-server/?folder=/home/docs/projects/showyun-docs

http://192.168.1.57:5000/
```