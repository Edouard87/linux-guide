# Hardware Selection

This section covers the materials needed throughout this guide. Each subsequent section provides additional description for that component.

---

## Required Materials

- [Host Device](#host-device)
- [Working Device](#working-device)
- [USB Stick](#usb-stick)

## Host Device

What I call the "Host Device" is the computer on which the Linux operating system will be installed. Note that the installation process will erase everything currently on the device.

For this guide, I'll be using a slightly modified ASUS TUF Gaming FX505G.

<table>
    <tr>
        <td><strong>Manufacturer</strong></td>
        <td>ASUS</td>
    </tr>
    <tr>
        <td><strong>Model</strong></td>
        <td>FX505G</td>
    </tr>
    <tr>
        <td><strong>RAM</strong></td>
        <td>SK HYNIX 8GB 1RX8 DDR4 SO-DIMM PC4-21300 2666MHZ Memory Module HMA81GS6JJR8N-VK</td>
    </tr>
    <tr>
        <td><strong>Storage</strong></td>
        <td>Crucial MX500 1TB</td>
    </tr>
</table>

I don't mean to suggest that this exact hardware setup is required, since Linux works on almost everything.

The configuration of your installation, however, will depend on the hardware you choose to use. For this reason, it's important to choose wilsely, as some choices will result in more issues down the road.

In any case, it is very likely that the reader will need to adapt some steps of the installation process to their particular setup. This is because each piece of hardware tends to have its own driver and options. Even if you were to replicate my hardware setup, manufacturers sometimes update their driver configurations, so if you're reading this in future, it will liekly be different. I will include notes specifing when steps of the configuration must be adapted. It is recommended to refer to the documentation provided by the manufacturer, if provided, for exact configuration details.

## Working Device

To install Linux on the Host Device, another device, which I will call the "Working Device," is needed. The operating system of the device, as well as it's hardware setup, has minimal importance. The only requirements are that it must have a USB port and a working operating system.

## USB Stick

A USB stick is needed only to install the operating system on the host computer. The USB stick in question does not need to have very much storage. 8 Gb is more than sufficient. The reader is advised to select a USB with no more than 16 Gb of storage.

[< Previous Section](./readme.md) | [Next Section >](./flashing-usb.md)

<!-- With the above being said, I highly recommend *against* using devices from the following manufacturers

- Microsoft Surface Pro
- Apple MacBook

I don't mean to suggest that the above are good computers, but their intended purpose is not to run an operating system like Linux. As such, attempting to set Linux up on them tends to cause more problems in the long run. In addition, they tend to be more expensive.

In addition, I do not recommend using dedicated graphics on Linux, espetially NVIDIA. It is possible, but the added benefit of having dedicated graphics will likely be  -->