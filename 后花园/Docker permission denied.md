If you want to run docker as non-root user then you need to add it to the docker group.

1. Create the docker group if it does not exist

```bash
$ sudo groupadd docker
```

2. Add your user to the docker group.

```bash
$ sudo usermod -aG docker $USER
```

3. Log in to the new `docker` group (to avoid having to log out / log in again; but if not enough, try to reboot):

```bash
$ newgrp docker
```

4. Check if docker can be run without root

```bash
$ docker run hello-world
```

Reboot if still got error

```bash
$ reboot
```

[how-to-fix-docker-got-permission-denied-issue](https://stackoverflow.com/questions/48957195/how-to-fix-docker-got-permission-denied-issue)