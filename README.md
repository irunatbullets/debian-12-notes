# Debian 12 installation notes

I am documenting the steps I took to get Debian installed the say I wanted it. There are a number of steps I had to take, and some of it is already forgotten, but as I remember things I'll add them to this document.

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



