---
title: 12 Things to Do After Installing Fedora Workstation 41
date: 2024-10-31
description: Configure Fedora Workstation 41 on your computer.
categories:
  - Linux
tags:
  - post-installation
  - fedora
  - gnome
image: fedora.jpg
---

<!-- cspell:words ArcaneSavant allowerasing appmenu devel flathub flatpak freeworld groupupdate gsettings gstreamer libav libdvdcss libva noarch nvdec nvenc openh264 ptyxis rpmfusion setopt touchpad upstreams vaapi vdpau -->

<!-- markdownlint-disable MD033 -->

## Introduction

Fedora Workstation comes with vanilla GNOME as its desktop environment, which is, in my opinion, a bit too bare-bones for everyday tasks.
A few actions can be taken to improve this experience.
This article will examine what to do following the installation of Fedora Workstation 41.
Keep in mind that this is by no means an exhaustive list.

## To-Do

### Assign Keyboard Shortcut to Launch Terminal

Out of the box, there is no keyboard shortcut available to open a terminal.
To assign a keyboard shortcut to open the terminal of your choice,

Go to **Settings** > **Keyboard** > Click on **View and Customize Shortcuts** from the "Keyboard Shortcuts" Section > Scroll down to **Custom Shortcuts** > Click **Add Shortcut**.

A pop-up window requesting a Name, Command, and Shortcut will appear.

- In the Name field, enter **Ptyxis** as the name.
- In the Command field, enter `ptyxis`.
- In the Shortcut field, press the key combination that you want to use to launch GNOME Terminal.
  For example, <kbd>Ctrl</kbd> + <kbd>Alt</kbd> + <kbd>T</kbd>.
- Click the **Add** button.

### Change Hostname

The default hostname is "fedora".
Change it to your liking to make it easily discoverable in your local network.

To change the hostname, open a terminal and issue the following command:

```shell
sudo hostnamectl set-hostname "Penguin"
```

In the example above, "Penguin" is the desired hostname.

### Change Swappiness

The swappiness sysctl parameter represents the kernel's preference for swap space.
A lower swappiness value means that the kernel will be less likely to swap memory to disk.
This can improve performance, but it can also lead to memory pressure if you do not have enough RAM.

The optimal swappiness value will vary depending on your system and your workload.
You may need to experiment to find the value that works best for you.

You can check the current swappiness value by running the following command:

```shell
sysctl vm.swappiness
```

To set the swappiness value permanently, create a sysctl.d configuration file.
For example:

```shell
sudo mkdir -p /etc/sysctl.d
sudo touch /etc/sysctl.d/local.conf
printf '%s\n' 'vm.swappiness=10' | sudo tee /etc/sysctl.d/local.conf
```

Run the following command to apply the changes:

```shell
sudo sysctl -p /etc/sysctl.d/local.conf
```

### Enable Minimize or Maximize Button

If you are not comfortable without the Minimize and Maximize window buttons, there is a way to bring them back.

Open a terminal and issue the following command to enable the minimize and maximize buttons:

```shell
gsettings set org.gnome.desktop.wm.preferences button-layout "appmenu:minimize,maximize,close"
```

The minimize and maximize buttons will now be enabled in all application windows.

### Change Appearance

Fedora 41 Workstation offers both Light and Dark modes.
Choose one from **Settings > Appearance** according to your liking.

You might as well change the desktop background if you want a more appealing look.

### Tweak Privacy & Security Options

#### File History & Trash

"File History" feature makes it easier for you to find files that you might want to use.
If you do not like the feature, go to **Settings** > **Privacy & Security** > **File History & Trash** and turn it off from the "File History" section.

Trash and temporary files can sometimes include personal or sensitive information.
Automatically deleting them can help to protect your privacy.
I highly recommend you enable this feature.

Go to **Settings** > **Privacy & Security** > **File History & Trash** > Enable **Automatically Delete Trash Content** and **Automatically Delete Temporary Files** from the "Trash & Temporary Files" section.

### Mouse & Touchpad

Since Fedora has a reputation for staying as close to upstream as possible, they follow the various defaults selected by the desktop environment upstreams.
Generally, this entails a disabled touchpad click by default.

To enable tap to click, Go to **Settings** > **Mouse & Touchpad** > Enable **Tap to Click** from the "Touchpad" section.

### Configure DNF

DNF configuration file is located at `/etc/dnf/dnf.conf`.
Behavior of DNF can be configured by editing this file.

The number of parallel downloads can be modified to get faster downloads if your internet connection is suitably fast.

Firstly, open the DNF configuration file with `nano`:

```shell
sudo nano /etc/dnf/dnf.conf
```

Then append the following line and save:

```shell
max_parallel_downloads=6
```

### Enable 3rd Party Repositories

#### Enable RPM Fusion

RPM Fusion is a third-party repository that provides access to a wider range of software than what is available in the Fedora repositories.

To enable RPM Fusion, open a terminal and type the following command:

```shell
sudo dnf install https://mirrors.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm
sudo dnf install https://mirrors.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm
```

After that, install the `appstream-data` package so that Gnome Software or KDE Discover may be used to install GUI programs from RPM Fusion.

The following command will install the required packages:

```shell
sudo dnf update @core
```

#### Enable Flathub

Flatpak is installed by default on Fedora Workstation.
To begin, simply enable Flathub, which is the best way to obtain Flatpak apps.

Run the following command to enable it:

```shell
flatpak remote-add --if-not-exists flathub https://dl.flathub.org/repo/flathub.flatpakrepo
```

That is it! You now have access to unfiltered Flathub on Fedora.

### Update System

You should run a system update as soon as possible after installing Fedora.
This will install the latest security patches and bug fixes, and it will also ensure that you have the latest version of all of your software.

To do this, open a terminal and run the following command:

```shell
sudo dnf upgrade
```

### Install Media Codecs

Fedora is a great Linux distribution, but it does not come with all the media codecs you might need out of the box.
If you try to play a video or audio file that uses a codec that is not installed, you will get an error message.

To fix this, you need to install the necessary codecs.
There are a few different ways to do this, but the easiest way is to use the RPM Fusion repository.

Use a terminal to install packages that provide multimedia libraries from the RPM Fusion repository.

#### Switch to full FFmpeg

The FFmpeg library that is shipped with Fedora 41 does not support H.264 decoding. Switch to the RPM Fusion's better supported FFmpeg build and follow the next section for additional codecs or plugins.

```shell
sudo dnf swap ffmpeg-free ffmpeg --allowerasing
```

#### Install Additional Codec

Install the additional multimedia packages required to enable the gstreamer framework-based application and other multimedia tools to playback other restricted codecs.

To do this, open a terminal and run the following commands:

```shell
sudo dnf update @multimedia --setopt="install_weak_deps=False" --exclude=PackageKit-gstreamer-plugin
```

#### Enable DVD Playback

Install the `libdvdcss` package from the RPM Fusion repository to enable DVD playback.

```shell
sudo dnf install rpmfusion-free-release-tainted
sudo dnf install libdvdcss
```

### Enable VA-API

VA-API stands for Video Acceleration API.
It is an open-source API that allows applications to use hardware video acceleration capabilities, usually provided by the GPU.

VA-API is supported by a wide range of hardware vendors, including Intel, NVIDIA, and AMD.
To enable VA-API on your system, you will need to install the appropriate drivers and libraries.

Please be aware that for VA-API to function as intended, the full FFmpeg build is required. Before continuing, make sure `ffmpeg` from the RPM Fusion repository is installed.

#### Intel

VA-API support for newer GPUs are provided by `intel-media-driver`.
Visit [upstream](https://github.com/intel/media-driver#supported-platforms) to see which platforms are supported by this driver.

Run the following command to install it:

```shell
sudo dnf install intel-media-driver
```

VA-API support for older GPUs are provided by `libva-intel-driver`.
Run the following command to install it:

```shell
sudo dnf install libva-intel-driver
```

#### AMD

For AMD, run the following commands to enable VA-API:

```shell
sudo dnf swap mesa-va-drivers mesa-va-drivers-freeworld
sudo dnf swap mesa-vdpau-drivers mesa-vdpau-drivers-freeworld
```

If using i686 compatibility libraries (e.g. Steam):

```shell
sudo dnf swap mesa-va-drivers.i686 mesa-va-drivers-freeworld.i686
sudo dnf swap mesa-vdpau-drivers.i686 mesa-vdpau-drivers-freeworld.i686
```

#### Nvidia

The Nvidia proprietary driver does not support VA-API, but there is a wrapper that can bridge NVDEC/NVENC with VA-API

Run the following command to install it:

```shell
sudo dnf install libva-nvidia-driver
```

You can also install both 32bit and 64bit flavor in one command as needed.

```shell
sudo dnf install libva-nvidia-driver.{i686,x86_64}
```

## Conclusion

I hope this gives you a good starting point for getting started with Fedora 41.
If you have any questions, please feel free to ask in the comments below.
