# Configure Network Manager
This section covers how to configure a network manager. This section is heavily inspired from the ArchWiki page on `iwd`: <https://wiki.archlinux.org/title/Iwd>.

---

## Configure iwctl
First, we need to edit the config for `iwctl`. Run `$ nvim /etc/iwd/main.conf`. Put the following in this config file:
```
[General]
EnableNetworkCOnfiguration=true

[Network]
EnableIPv6=false
```
This allows `iwd` to use DHCP by itself.

First, we enable `iwd`. This ensures that it will run on startup automatically.
```bash
$ systemctl enable iwd
```
Now, we need to start the daemon:
```
$ systemctl start iwd
```

Then, we can connect using the same way we did before:
```
$ iwctl
$ 
```

The network is automatically stored in our known networks. We can get a full list of these networks:
```
[iwd]# known-networks list
```

Network configurations are stored in `/var/lib/iwd`.

## Configure Hostname Resolution

We need a mechanism to resolve hostnames. On Arch, hostname resolution is handled by `systemd-resolved`, which is part of the `systemd` package which comes with Arch. [^1] Run `nvim /etc/resolv.conf`. At the end of the file, add:[^2]

```
nameserver 8.8.8.8
```

Save the file. Now, if you run `ping www.google.com`, for example, it should resolve the hostname.

[^1]: See <https://wiki.archlinux.org/title/Systemd-resolved>
[^2]: See *Arch Linux Handbook* by Dusty Phillips (2009), page 31
