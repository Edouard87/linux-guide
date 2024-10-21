# Configuring the Bootloader
This section covers the steps involved in installing and configuring the bootloader[^1].

--

## Installing GRUB

I'll be using GRUB as a bootloader.

```bash
$ pacman -S grub efibootmgr
$ grub-install
```

```bash
$ grub-install --target=x86_64-efi --efi-directory=boot --bootloader-id=GRUB
```

After the installation is complete, you'll notice that some files have been created in the `/boot` directory. These files are `/boot/EFI` and `/boot/grub`.

## Configuring GRUB

Now that grub has been installed, we need to configure it to load the kernel. We can do this automatically using:

```bash
$ grub-mkconfig -o /boot/grub/grub.cfg
```

## Rebooting the System

Run `exit` to leave the `chroot`ed environment and type `reboot` to reboot the system.

Once the system reboot is complete, you should see a GRUB menu asking which kernel to load. Ensure `Arch Linux` is highlighted and press on `ENTER`.

At this point, the basic kernal setup is complete. The next set is to setup the window manager.

[^1]: Page primarily inspired from the ArchWiki entry on installing a bootloader, available at: <https://wiki.archlinux.org/title/Arch_boot_process#Boot_loader>.