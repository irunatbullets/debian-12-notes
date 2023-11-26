# Debian 12 installation notes

I am documenting the steps I took to get Debian installed the way I wanted it. There are a number of steps I had to take, and some of it is already forgotten, but as I remember things I'll add them to this document.

## Which iso?

I actually installed Debian 12 several times over the last couple of days. Partly because I kept hopping between Fedora 39, and partially because Debian 12 installs **a lot** of weird language tools and games by default if you use the Debian 12 Gnome live iso - **so don't!**

It is best to go to https://www.debian.org/ and hit the big Download button to get the netinst iso.

Once I downloaded that, I just used Raspberry Pi imager to write it to an sd card/usb stick.

The installer is pretty straight forward, but, once it got to the desktop environment section. I chose these options:

```
[*] Debian desktop environment
[ ] ... GNOME
[ ] ... Xfce
[ ] ... etc, etc
[*] Standard system utilities
```
To my surprise, this still installed GNOME, which I wanted, but it still installed a lot of games. Luckily, though, it didn't install all of the weird input methods! The games are easy to deal with.

```
sudo apt purge iagno lightsoff four-in-a-row gnome-robots pegsolitaire gnome-2048 hitori gnome-klotski gnome-mines gnome-mahjongg gnome-sudoku quadrapassel swell-foop gnome-tetravex gnome-taquin aisleriot gnome-chess five-or-more gnome-nibbles tali ; sudo apt autoremove
```
Apparently this also works, but I didn't try it.
```
apt purge --autoremove gnome-games
```

I also very carefully removed LibreOffice.

## Start up

I wanted the computer to boot as cleanly into Debian 12 as I could.

### Grub
I wanted to hide everything and remove as much boot text as possible.
```
sudo vim /etc/default/grub
```
Replace all of the options with these (there may be redundant things in here, but I don't really know.)
```
GRUB_DEFAULT=0
GRUB_HIDDEN_TIMEOUT=0
GRUB_HIDDEN_TIMEOUT_QUIET=true
GRUB_TIMEOUT=0
GRUB_DISTRIBUTOR=`lsb_release -i -s 2> /dev/null || echo Debian`
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash mce=off vt.global_cursor_default=0 loglevel=3"
GRUB_CMDLINE_LINUX=""
```
After saving the file, run
```
sudo update-grub
```

### Plymouth

Plymouth is the loading/splash screen. I didn't really like the default Debian 12 theme, so I found something that was simple and black. It's a cheesy mac inspired theme, but it's not bad.

[Debian - Mac OS Style Boot Splash](https://www.gnome-look.org/p/1888173)

After downloading and extracting it, I ran
```
mv debian-logo /usr/share/plymouth/themes/
```
followed by 
```
sudo plymouth-set-default-theme -R debian-logo
```

I then had a pretty annoying problem where the Debian 12 logo shows as a watermark on my new theme, and there didn't seem to be a way to remove it.

The solution that I found was to
```
mv /usr/share/desktop-base/debian-logos /usr/share/desktop-base/debian-logos.bak
```
I don't know if this could cause an issue later because there are a lot of other logos in this folder, but **OH WELL**.

### Welcome to Grub

This tidies things up pretty well, apart from a very annoying rectangle that would flash on my screen for a split second. I had to make a video recording of the boot sequence to finally see that it was a white rectangle with black text saying `Welcome to Grub`.

To remove this annoying thing, you have to do something that could prevent your machine from booting. There's a gist [here](https://gist.github.com/yousefvand/0f831f9148270445bf7022486a15b418), but I'll add the quick steps here:

```
sudo su -
cd /boot/efi/EFI/debian
cp gubx64.efi grubx64.efi.bak
echo -n -e \\x00 > patch && cat grubx64.efi | strings -t d | grep "Welcome to GRUB!" | awk '{print $1;}' | xargs -I{} dd if=patch of=grubx64.efi obs=1 conv=notrunc seek={}
```
## Nvidia graphics

I just followed an online guide to enable the non-free repositories and installed the nvidia drivers. Perhaps it was as simple as `sudo apt install nvidia-driver`, it's a bit hard to remember because Fedora 39 was quite a big hassle.

## Extra command-line tools

`sudo apt install neofetch` - Show distro and system information in the terminal.
`sudo apt install htop` - A nicer version of top!



