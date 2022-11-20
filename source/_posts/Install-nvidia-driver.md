---
layout: post
title:  "Install nvidia driver on Arch Linux"
date:   2021-07-13 09:48:52 -0600
categories: Os
tags: Linux
---
# Install and configure nvidia driver on Arch Linux
## Find the right driver
To install your driver you should read the wiki, my solution may not work for you.
[NVIDIA - ArchWiki](https://wiki.archlinux.org/title/NVIDIA)
### To see what graphics card you have:
```
lspci -k | grep -A 2 -E "(VGA|3D)"
```
Output
```
00:02.0 VGA compatible controller: Intel Corporation CoffeeLake-H GT2 [UHD Graphics 630]
	Subsystem: Lenovo Device 39fe
	Kernel driver in use: i915
--
01:00.0 VGA compatible controller: NVIDIA Corporation GP107M [GeForce GTX 1050 Mobile] (rev a1)
	Subsystem: Lenovo Device 39fe
	Kernel driver in use: nvidia
```
### Wish driver you need:
[CodeNames](https://nouveau.freedesktop.org/CodeNames.html)
This is what i have:
```NV137 (GP107) GeForce GTX (1050, 1050 Ti)``` 
I'm above NV110 so I install the ```nvidia``` drinver and not the ```nvidia-lts``` one.
```
sudo pacman -S nvidia nvidia-utils nvidia-prime nvidia-settings
```
I use ```prime-run``` for the moment to use my GPU only when I need, but I will try NVIDIA ```Optimus```.
D'ont create a conf file for Xorg, the conf is inplemented in the driver.
## Configure mkinitcpio
The doc:
[Early KMS start](https://wiki.archlinux.org/title/Kernel_mode_setting#Early_KMS_start)
[DRM_Kernel_mode_setting](https://wiki.archlinux.org/title/NVIDIA#DRM_kernel_mode_setting)
In the file ```/etc/mkinitcpio.conf```
Replace ```MODULES=()``` by ```MODULES=(i915 nvidia)```.

## Tests

Enable ssh:
```sudo systemctl enable sshd```
First thing to do is reboot:
It can work now but if it's broken after the rebbot it's useless.

Download glxinfo from AUR:
```prime-run glxinfo```
If you have an output without errror it's good.
The nvidia setting:
```
nvidia-setting
```
You should download nvtop via pacman:

```
sudo pacman -S nvtop
```
And run nvtop to see the consumption.





