# Installation

This section covers the installation of Arch Linux.

---

## Installing ISO

This section is inspired from the Arch installation page available on the Arch Wiki[^1].

The first step is downloading the ISO file. The ISO file is what's called an *image* of the operating system.

I am installing Arch from a Windows device. First, go to the Arch Linux downloads page (<https://archlinux.org/download/>). I prefer downloading things over HTTP, so I'll go ahead and scroll down to the HTTP mirrors.

Go ahead and download the `.iso` file. Since we should be careful when downloading things over the internet, also download the `.iso.sig` file.

Once both files are downloaded, they should be in your downloads folder. This is a `pgp` signature. Since I'm on a Windows, I need to use `mingw` to verify the signature. Open `mingw` and run:

```bash
$ cd Downloads
$ gpg --keyserver-options auto-key-retrieve --verify archlinux-version-x86_64.iso.sig
```

So in my case, I would run:

```bash
$ gpg --keyserver-options auto-key-retrieve --verify archlinux-2024.10.01-x86_64.iso.sig
```

`gpg` should output something like `Good signature from ...`. Don't worry if it says `WARNING: This key is not certified with a trusted signature!`.

## Flashing USB

> [!WARNING]
> Flashing the ISO on the USB will delete all data on it. Make sure there is nothing important on the USB.

We will need to use software to flash the ISO. Since I'm using Windows, I like to use Rufus. It does almost everything for you, but the installation process is a little sketch.

## Installing onto the Host Machine

> [!WARNING]
> Installing Linux on your host device will override all data currently on it. If you are using Windows, this also might include your Windows license key[^2].

Take your USB and plug it into your host computer. Then, you will need to figure out what your host computer's BIOS key is. The BIOS key is a special key on your keyboard that takes you to your motherboard's Basic Input Output Setup page before your computer boots.

This may take some trial and error. If it makes you feel better, I always forget what my BIOS key is. A few keys to try are F2, F10, DEL.

Since the BIOS is different from one motherboard manufacturer to the next, I'll do my best to generally describe what the buttons look like. The names may be slightly different for your configuration. You will need to tell your BIOS to boot into the USB you plugged in.

Press save and exit 

> [!NOTE]
> If you get a `Secure Boot Violation` error, you will need to disable secure boot. See the disable secure boot below.

Once you boot, wait for the installer to copy the filesystem to RAM. Once it's done, you will be presented with a shell. 

### Connect to the Internet

```
$ iwctl
[iwd]# device list
[iwd]# device name set-property Powered on
[iwd]# station name scan
[iwd]# station name get-networks
[iwd]# station name connect SSID
```

Where `name` is the name of the network adapter and `SSID` is the SSID of the network you want to connect to (i.e. the network's "name").

Ensure you have an IP address by running:

```bash
$ ip addr show
```

You can also ping the Google DHCP server just to be sure:

```bash
$ ping 8.8.8.8
```

Synchronize the system clock. To do this, you need to be connected to the internet so the application can ask its timeservers what the actual time it. It is considered good practice to do this.

```bash
timedatectl
```

### Partition the Drive

You will need to partition the host drive to install Arch onto it. There is an entire field of software development and computer science dedicated to this one thing. Here, I'll provide a general overview of how partitioning works.

## Optonal Steps

### Disable Secure Boot

> [!WARNING]
> Enabling Secure Boot may cause issues if you try to install Windows on this device again. For instance, some competitive videogames cannot be played with secure boot turned off. Secure Boot can be turned back on, but it may get complicated because of the way secure boot keys work.

In my BIOS, Secure Boot can be disabled by going into Security, scrolling down, expanding the Secure Boot context, and setting Secure Boot to disabled on the dropdown next to Secure Boot. Then, press F10 and enter to save and exit.

[^2]: See https://support.microsoft.com/en-us/windows/activate-windows-c39005d4-95ee-b91e-b399-2820fda32227
[^1]: See <https://wiki.archlinux.org/title/Installation_guide>.