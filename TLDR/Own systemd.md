https://wiki.archlinux.org/title/Systemd#Writing_unit_files

```shell
systemctl show --property=UnitPath
```

- `/usr/lib/systemd/system/`: units provided by installed packages
- `/etc/systemd/system/`: units installed by the system administrator