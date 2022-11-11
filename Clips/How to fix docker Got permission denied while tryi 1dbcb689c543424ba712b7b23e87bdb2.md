# How to fix docker: Got permission denied while trying to connect to the Docker daemon socket | DigitalOcean

[https://www.digitalocean.com/community/questions/how-to-fix-docker-got-permission-denied-while-trying-to-connect-to-the-docker-daemon-socket](https://www.digitalocean.com/community/questions/how-to-fix-docker-got-permission-denied-while-trying-to-connect-to-the-docker-daemon-socket)

I’ve just installed docker but I have to run it with sudo every time. If I don’t add sudo I get the following error:

```
Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Get http://%2Fvar%2Frun%2Fdocker.sock/v1.40/containers/json: dial unix /var/run/docker.sock: connect: permission denied

```

Is there a way around that? I want to be able to run docker without having to type my password each time…

<div pseudo="-webkit-input-placeholder" id="placeholder" style="display: block !important;">Answer a question...</div><div></div>

This textbox defaults to using **Markdown** to format your answer.

You can type **!ref** in this text area to quickly search our full set of tutorials, documentation & marketplace offerings and insert the link!

[Sign In or Sign Up to Answer](https://www.digitalocean.com/api/dynamic-content/v1/login?success_redirect=https%3A%2F%2Fwww.digitalocean.com%2Fcommunity%2Fquestions%2Fhow-to-fix-docker-got-permission-denied-while-trying-to-connect-to-the-docker-daemon-socket&error_redirect=https%3A%2F%2Fwww.digitalocean.com%2Fauth_error)

These answers are provided by our Community. If you find them useful, show some love by clicking the heart. If you run into issues leave a comment, or add your own answer to help others.

Hello,

According to the official Docker docs here:

You need to do the following:

To create the docker group and add your user:

- Create the docker group.

```
sudo groupadd docker

```

- Add your user to the docker group.

```
sudo usermod -aG docker ${USER}

```

- You would need to loog out and log back in so that your group membership is re-evaluated or type the following command:

```
su -s ${USER}

```

- Verify that you can run docker commands without sudo.

```
docker run hello-world

```

- 
    
    This command downloads a test image and runs it in a container. When the container runs, it prints an informational message and exits.
    
- 
    
    If you initially ran Docker CLI commands using sudo before adding your user to the docker group, you may see the following error, which indicates that your `~/.docker/` directory was created with incorrect permissions due to the sudo commands.
    

```
WARNING: Error loading config file: /home/user/.docker/config.json -
stat /home/user/.docker/config.json: permission denied

```

- To fix this problem, either remove the `~/.docker/` directory (it is recreated automatically, but any custom settings are lost), or change its ownership and permissions using the following commands:

```
sudo chown "$USER":"$USER" /home/"$USER"/.docker -R
sudo chmod g+rwx "$HOME/.docker" -R

```

Here’s also a quick video demo on how to do this:

<A>View YouTube video</A>

Hope that this helps! Regards, Bobby

![How%20to%20fix%20docker%20Got%20permission%20denied%20while%20tryi%201dbcb689c543424ba712b7b23e87bdb2/callout-community.7335fe40.svg](callout-community.7335fe40.svg)

Join our DigitalOcean community of over a million developers for free! Get help and share knowledge in Q&A, subscribe to topics of interest, and get courses and tools that will help you grow as a developer and scale your project or business.

**While doing production config i got the permission issue.I tried below solution to resolve the issue.**

**Error Message****

```
    ubuntu@node1:~$ docker run hello-world
    docker: Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Post http://%2Fvar%2Frun%2Fdocker.sock/v1.38/containers/create: dial unix /var/run/docker.sock: connect: permission denied.
    See 'docker run --help'.

```

**Solution:permissions of the socket indicated in the error message, /var/run/docker.sock:**

```
    ubuntu@ip-172-31-21-106:/var/run$ ls -lrth docker.sock
    srw-rw---- 1 root root 0 Oct 17 11:08 docker.sock
    ubuntu@ip-172-31-21-106:/var/run$ sudo chmod 666 /var/run/docker.sock
    ubuntu@ip-172-31-21-106:/var/run$ ls -lrth docker.sock
    srw-rw-rw- 1 root root 0 Oct 17 11:08 docker.sock

```

**After changes permission for docket.sock then execute below command to check permissions.**

```
ubuntu@ip-172-31-21-106:/var/run$ docker run hello-world
    Unable to find image 'hello-world:latest' locally
    latest: Pulling from library/hello-world
    1b930d010525: Pull complete
    Digest: sha256:c3b4ada4687bbaa170745b3e4dd8ac3f194ca95b2d0518b417fb47e5879d9b5f
    Status: Downloaded newer image for hello-world:latest

    Hello from Docker!
    This message shows that your installation appears to be working correctly.

    To generate this message, Docker took the following steps:
     1. The Docker client contacted the Docker daemon.
     2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
        (amd64)
     3. The Docker daemon created a new container from that image which runs the
        executable that produces the output you are currently reading.
     4. The Docker daemon streamed that output to the Docker client, which sent it
        to your terminal.

    To try something more ambitious, you can run an Ubuntu container with:
     $ docker run -it ubuntu bash

    Share images, automate workflows, and more with a free Docker ID:
     https://hub.docker.com/

    For more examples and ideas, visit:
     https://docs.docker.com/get-started/

```