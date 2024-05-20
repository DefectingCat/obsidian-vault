### Jail Utility

Jail Utility 用来管理 jails。



```shell
# sysrc jail_enable="YES"
# sysrc jail_parallel_start="YES"
```

> 使用 `jail_parallel_start` 使所有配置过的 jail 在后台启动。

### Networking

- Host Networking Mode (IP Sharing)

In host networking mode, a jail shares the same networking stack as the host system. When a jail is created in host networking mode it uses the same network interface and IP address. This means that the jail doesn’t have a separate IP address, and its network traffic is associated with the host’s IP.

- Virtual Networks (VNET)

Virtual Networks are a feature of FreeBSD jails that offer more advanced and flexible networking solutions than a basic networking mode like host networking. VNET allows the creation of isolated network stacks for each jail, providing them with their own separate IP addresses, routing tables, and network interfaces. This offers a higher level of network isolation and allows jails to function as if they are running on separate virtual machines.

- The netgraph system

[netgraph(4)](https://man.freebsd.org/cgi/man.cgi?query=netgraph&sektion=4&format=html) is a versatile kernel framework for creating custom network configurations. It can be used to define how network traffic flows between jails and the host system and between different jails.

### Setting Up the Jail Directory Tree

Apart from /usr/local/jails other directories will be created:

- media will contain the compressed files of the downloaded userlands.
    
- templates will contain the templates when using Thin Jails.
    
- containers will contain the jails.
    

When using OpenZFS, execute the following commands to create datasets for these directories:

```
# zfs create -o mountpoint=/usr/local/jails zroot/jails
# zfs create zroot/jails/media
# zfs create zroot/jails/templates
# zfs create zroot/jails/containers
```