相对于其他提供 SaaS 的 CI 来说，Gitlab 最大的好处就是可以安装 gitlab-runner 来使用自己的机器作为 runner，从而不用再额外购买 CI 的执行时长。
## Compose

```yml
version: "3.6"
services:
  runner:
    container_name: gitlab-runner-1
    image: "gitlab/gitlab-runner:latest"
    restart: unless-stopped
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - ./runner-1:/etc/gitlab

  runner-2:
    container_name: gitlab-runner-2
    image: "gitlab/gitlab-runner:latest"
    restart: unless-stopped
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - ./runner-2:/etc/gitlab
```

使用 compose 来运行多个 runner 是最好不过的选择了。compose file 本身与运行其他服务没有什么区别，唯一需要注意的就是注册 runner 了。

在 compose 启动所有的 runner 后，可以使用 `docker exec` 来为运行中的容器执行命令，从而执行 runner 中的 `gitlab-runner` 命令来注册 runner。其注册命令为 `gitlab-runner register`。

```bash
docker exec -it gitlab-runner-1 gitlab-runner register
```

这里是映射了容器中的 `/etc/gitlab` 目录到本地，runner 注册的配置文件就在该目录。所有除了直接执行容器命令，还可以单独运行一个实例，挂载 `./runner-1` 目录来注册。注册完成后配置文件就在该目录下，随后再启动 compose，新启动的 runner 就能读取到刚刚注册的配置文件，从而变成它了。
## Locally test

```bash
docker run --entrypoint bash --rm -w $PWD -v $PWD:$PWD -v /var/run/docker.sock:/var/run/docker.sock gitlab/gitlab-runner:latest -c 'git config --global --add safe.directory "*";gitlab-runner exec docker build:linux-musl-amd64'
```

在本地运行并测试刚修改的 `.gitlab-ci.yml` 是必不可少的，不能每次的修改都需要 push 以后再交给 runner 运行。这样不仅麻烦，还会导致 commit 的杂乱无章。

而通过启动一个新的 runner 实例，可以直接将项目挂载到 runner 的容器中来本地执行当前的 `.gitlab-ci.yml`。本地执行的 runner 是不需要注册的。