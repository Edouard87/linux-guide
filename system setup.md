# System Setp

This section covers the system setup steps that must be completed before Arch can be installed.  

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

You will need to partition the host drive to install Arch onto it. There is an entire field of software development and computer science dedicated to this one thing. Here, I'll provide a general overview of how partitioning works. I'll also be configuring a very basic setup. For further details, I invite the reader to check the drive-partitioning apendix.

Before we continue, it is a good idea to determine how much ram your system has. To do so, run:

```
$ top
```

This will open a menu showing all active processes. On the top left of your screen, you should see `MiB Mem:` followed by a number. Mine says `7791.5 total`. This means that I have 7791.5 MiB of RAM, or about 8.2 GB. This makes sense, since I installed 8 GB of ram onto this system.

Note that MiB and MB are *not* the same thing. The ram I installed is not actually 8GB of ram. [^6]

My BIOS supports EFI boot with GPT. As such, the configuration I'm going for is the following:[^3]

> [!NOTE]
> It is recommended to make the swap size the same as the amount of ram available.

| Mount Point | Partition | Partition Type | Size |
| -- | -- | -- | - |
`/boot` | `/dev/efi_system_partition` | EFI system partition | 1 GiB |
`[SWAP]` | `/dev/swap_partition` | Linux swap | 8 GiB | 
`/` | `/dev/root_partition` | Linux root | Remainder of the drive

We will be using `fdisk` to edit the partition tables. First, we list the disks mounted on the system:

```bash
fdisk -l
```

On my device, this outputs two disks, `sda` and `sdb`. You may notice that each partition is mounted in the `/dev` directory. Remember that everything in Linux is a file, which includes all hardware. Every hardware abstraction is mounted in the `sda` directory. These are called *Device Files*. [^4]

`sdb` is a `Flash Disk`, so this is the USB I have plugged into the host computer. `sda` is the internal ssd of my host computer. This is the drive I want to partition. Since I already had Windows installed on it, it already has 5 partitions. I'm going to go ahead and delete them all.

Since `fdisk` is dialogue-driven, you start by running `fdisk` to open the interpreter.

```bash
$ fdisk
```

I want to delete all partitions and create a new `GPT` partition table, so I run:

```
Command (m for help): g
```

Now, if I run `p`, I'll see that the partition table is now empty.

I want to create the `boot` partition, so I run:

```
Command (m for help): n
Partition number: 1
First sector:
```
Press return to use the default first sector.
```
Last sector: +1G
```

This creates the first partition with a size of 1 GiB. Note that `fdisk` measures size in bytes and the `iB` is optional. In other words, `G` has the same meaning as `GiB`[^4].

Now, I create the `swap` partition.

```
Command (m for help): n
Partition number: 2
First sector:
```
Press return again.
```
Last sector: +8GiB
```

Finally, I create the `root` system partition:

```
Command (m for help): n
Partition number: 3
First sector:
Last sector:
```
Press return to have `fdisk` format this partition all the way to the last possible sector.

We're not done yet. We still need to set the type of the partitions. This is because EFI booting requires the partitions to have a special UUID so it can find them.

```
Command (m for help): t
Partition number: 1
```
You can press `L` to see all of the codes and their associated `UUID`. The one we want for EFI is `EFI System` with UUID `C12A7328-F81F-11D2-BA4B-00A0C93EC93B`. To get out of the `L` menu, press `q`. Then, type:
```
Partition type or alias: 1
```
Now, we configure the swap partition:
```
Command (m for help): t
Partition number: 2
Partition type or alias: 19
```
Finally, we configure the root partition:
```
Command (m for help): t
Partition number: 3
Partition type or alias: 23
```
Now, write the changes to the table and quit.
```
w
```

## Format the Drive

Now, we need to format the drive's filesystem. There are different recommend filesystem types for different types of partitions. For instance, the filesystem for EFI must be `FAT32`. In my case, my EFI system partition is mounted at `/dev/sda1`, so I format it with:

```bash
$ mkfs.fat -F 32 /dev/sda1
```

Now, we make the swap partition. In my case, the swap partition is mounted on `/dev/sda2`

```bash
$ mkswap /dev/sda2
```

Finally, we set up the filesystem for the main Linux partition. I opted to use `ext4`.

```bash
$ mkfs.ext4 /dev/sda3
```

In my case, it warns that there is already data in this partition. I'm okay with that data being override, so I type `y`.

### Mount Filesystems

We will now mount the newly created filesystems.

```bash
$ mount /dev/sda3 /mnt
```
This mounts the root filesystem to the `/mnt` directory itself.
```
$ mount --mkdir /dev/sda1 /mnt/boot
$ swapon /dev/sda2
```

We can confirm that the filesystems have been correctly mounted by running `findmnt`. You should see that `/dev/sda3` is mounted to `/mnt` and `/dev/sda1` is mounted to `/mnt/boot`. 

## Optonal Steps

### Disable Secure Boot

> [!WARNING]
> Enabling Secure Boot may cause issues if you try to install Windows on this device again. For instance, some competitive videogames cannot be played with secure boot turned off. Secure Boot can be turned back on, but it may get complicated because of the way secure boot keys work.

In my BIOS, Secure Boot can be disabled by going into Security, scrolling down, expanding the Secure Boot context, and setting Secure Boot to disabled on the dropdown next to Secure Boot. Then, press F10 and enter to save and exit.

[^2]: See https://support.microsoft.com/en-us/windows/activate-windows-c39005d4-95ee-b91e-b399-2820fda32227
[^1]: See <https://wiki.archlinux.org/title/Installation_guide>.
[^3]: Configuration inspired from <https://wiki.archlinux.org/title/Partitioning#GUID_Partition_Table>.
[^4]: See <https://wiki.archlinux.org/title/Device_file#Block_devices> for more information on Block Devices.
[^5]: see the manual page for `fdisk` available at <https://man7.org/linux/man-pages/man8/fdisk.8.html>.
[^6]: See <https://kb.wisc.edu/researchdata/page.php?id=101840> for further discussion on the units used.