# Installation and Configuration

This section covers the installation of the actual operating system, assuming everything is propertly mounted.

---
## Install Base Kernel
The operating system we are running is actually a "live" system. In other words,  it's running from the USB we have plugged in. We want to install packages into the filesystems we have just mounted. To do so, `arch` provides the `pacstrap` command.

```bash
$ pacstrap -K /mnt base linux linux-firmware neovim iwd sudo
```

Here are some further details on these packages. `linux` is the linux kernel, which is the program which effectively runs the operating system. It also includes kernel modules, which are like drivers. [^1] The package `neovim` is optional but it's a text editor I recommend using. `iwd` is to allow you to connect to wireless internet. `sudo` lets us run commands as the root user from another user user profile, which is very practical. Note that the packages installed on the USB WILL NOT necessarily be available on the system we installed. 

[^1]: See <https://archlinux.org/packages/core/x86_64/linux/>

## Configure the System

We need to generate an `fstab` file. This ensures that everything is automatically mounted.

```bash
$ genfstab -U /mnt >> /mnt/etc/fstab
```

Now, we want to change our root directory so we're effectively in the system we just installed, and not the current system.

```bash
$ arch-chroot /mnt
```

### Set Timezone

```bash
$ ln -sf /usr/share/zoneinfo/America/Toronto /etc/localtime
```

```
$ hwclock --systohc
```

### Localization
We need to setup the proper locale information. This allows programs to correctly render text in the appropriate regional language. Since I'm in Canada, I'll be using Canadian English.

```bash
$ nvim /etc/locale.gen 
```

Since I'm in Canada, I'll uncomment `en_CA.UTF-8 UTF-8`.

```
$ locale-gen
```

```bash
$ nvim /etc/locale.conf
```

In this file put `LANG=en_CA.UTF-8`.

## Network Configuration

Create the `hostname` file.

```bash
$ nvim /etc/hostname 
```

In this file, create a unique hostname for your machine.

## Root Password

Set the root password:

```bash
$ passwd
```

## Add user

It's not a good idea to exclusively use the root user.

```bash
$ useradd -m ed
$ passwd ed
```

## Configure Sudo

We want this new user (in my case, the user `ed`) to be able to use sudo.

```bash
$ EDITOR=nvim visudo
```

Then, add the following line:

```
ed ALL=(ALL) ALL
```

This gives the user `ed` access to all admin priveledges. The reason why it's better to run things as `ed` and not as `root` is that things that need admin priveledges must be prefixed with `sudo`.

Run `exit` and log back into user `ed` (in my case). Then run:

```bash
$ sudo pacman -S xdg-user-dirs
$ xdg-user-dirs-update
```

This creates the default directories that a user would expect to see.

https://wiki.archlinux.org/title/Users_and_groups