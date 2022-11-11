# Specifying a default Docker storage directory by using bind mount - IBM Documentation

[https://www.ibm.com/docs/en/cloud-private/3.1.1?topic=pyci-specifying-default-docker-storage-directory-by-using-bind-mount](https://www.ibm.com/docs/en/cloud-private/3.1.1?topic=pyci-specifying-default-docker-storage-directory-by-using-bind-mount)

![11.png](Specifying%20a%20default%20Docker%20storage%20directory%20by%20u%20db8151c7818440e99b8c5e54c39b5fb8/11.png)

If you want to use a different default Docker storage directory, you must change it before you install IBM® Cloud Private by using a bind mount.

IBM Cloud Private requires Docker. You can either install Docker on each node or configure your nodes so that the IBM Cloud Private installation process includes Docker installation. See [Installing Docker on IBM Cloud Private](https://www.ibm.com/docs/en/SSBS6K_3.1.1/installing/install_docker.html).

## Specifying a default Docker storage directory for manually installed Docker

If you installed Docker on your nodes, take the following steps:

1.  
    
    Remove all Docker containers and images.
    
    ```
    sudo docker rm -f $(docker ps -aq); docker rmi -f $(docker images -q)
    
    ```
    
2. 
    
    Remove the Docker storage directory.
    
3.  
    
    Create a new `/var/lib/docker` storage directory.
    
    **Note:** A `/var/lib/docker` directory with less than 50 GB disk space isn’t supported.
    
4.  
    
    Use bind mount to set the new location. For example, to set the new location as `/mnt/docker` run the following commands:
    
    ```
    sudo mkdir /mnt/docker
    sudo mount --rbind /mnt/docker /var/lib/docker
    
    ```
    

## Specifying a default Docker storage directory for automatically installed Docker

If the IBM Cloud Private installation process includes Docker installation, take the following steps:

1. 
    
    Create a new `/var/lib/docker` storage directory.
    
2. 
    
    Use bind mount to set the new location. For example, to set the new location as `/mnt/docker` run the following commands: