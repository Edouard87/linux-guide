# Configure Snap

Snap is a kind of "app store." It has some applications which are not available on pacman.

## Some apps you might want to install with snap

```
$ sudo snap install code --classic 
```
VSCode is one of my favorite general-purpose code editors.


https://snapcraft.io/install/intellij-idea-ultimate/arch

## Configure AUR

<!-- TODO: Expand this section -->

Somehow, you can get Google Chrome on Arch through the AUR.

> [!DANGER]
> NEVER run makepkg as root. `makepkg` is just a bash script, and could seriously harm your system if run arbitrarily.

https://itsfoss.com/install-chrome-arch-linux/
https://itsfoss.com/aur-arch-linux/

## Configure GitHub SSH Keys

<!-- TODO: Clean this up -->

```
$ ssh-keygen -t ed25519 -C "your_email@example.com"
$ exec ssh-agent bash
$ ssh-add ~/.ssh/id_ed25519
```
Test your connection with

```
$ ssh -T git@github.com
```

Note that the syntax for cloning over SSH is different. Use:

```
$ git clone git@github.com:<username>/<repo name>
```

https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent?platform=linux