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

使用 compose 来运行多个 runner 是最好不过的选择了。compose file 本身与运行其他服务没有什么区别。