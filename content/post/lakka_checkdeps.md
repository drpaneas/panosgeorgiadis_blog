+++
categories = ["devstories"]
date = "2019-10-26T23:47:35+02:00"
tags = ["devstory", "lakka", "checkdeps", "libreelec", "compile", "development"]
title = "Lakka checkdeps"

+++

## Start compiling

```bash
$ DISTRO=Lakka PROJECT=Generic ARCH=x86_64 make image
./scripts/image mkimage

 =================================================================================
 Configuration for Lakka (community)
 =================================================================================

 Buildoptions:
 ======================================================
 - CPU (ARCH):                           x86-64 (x86_64)
 - FLOAT:                                
 - FPU:                                  
 - SIMD support:                         yes
 - LTO (Link Time Optimization) support: yes
 - GOLD (Google Linker) Support:         yes
 - LLVM support:                         yes

 Graphic configuration:
 ======================================================
 - OpenGL (GLX) support (provider):      yes (mesa)
 - OpenGLES support (provider):          no (no)
 - Vulkan support (provider):            no ()
 - uvesafb support:                      yes

 Hardware decoder configuration:
 ======================================================
 - Kodi Player driver:                   default
 - VAAPI Support:                        yes
 - VDPAU Support:                        yes

 Input device configuration:
 ======================================================
 - Remote support:                       no
 - ATV Remote support:                   no
 - CEC Adapter support:                  yes
 - Kodi Joystick support:                yes

 Misc. hardware configuration:
 ======================================================
 - ALSA support:                         yes
 - Pulseaudio support:                   no
 - Bluetooth support:                    yes
 - Include driver:                       RTL8192CU
 - Include driver:                       RTL8192DU
 - Include driver:                       RTL8192EU
 - Include driver:                       RTL8188EU
 - Include driver:                       RTL8812AU
 - Include driver:                       bcm_sta
 - Include driver:                       media_build
 - Include driver:                       intel_nuc_led
 - Include driver:                       applespi
 - Include firmware:                     misc-firmware
 - Include firmware:                     wlan-firmware
 - Include firmware:                     dvb-firmware
 - Include firmware:                     iwlwifi-firmware

 Network service configuration:
 ======================================================
 - Avahi (Zeroconf) support:             yes
 - NFS mounting support:                 yes
 - SAMBA mounting support:               yes
 - SAMBA server support:                 yes
 - SFTP server support:                  yes
 - OpenVPN support:                      yes

 OS configuration:
 ======================================================
 - OEM Support:                          yes
 - Default ROOT Password:                root
 - Bootloader:                           syslinux
 - UDevil support:                       yes
 - Installer support:                    yes

 Misc. Filesystems:
 ======================================================
 - Swap Support:                         no
 - exFAT Support (via Fuse):             yes
 - NTFS Support (via Fuse):              yes
 - Install HFS Tools:                    yes

 Kodi configuration:
 ======================================================
 - Kodi version:                         no
 - Kodi nonfree support:                 yes
 - Kodi Blu-Ray support:                 yes
   - Bluray BD+ support:                 yes
   - Bluray AACS support:                yes
 - Kodi DVDCSS support:                  yes
 - Kodi Airplay support:                 yes
 - Kodi Airtunes support:                yes
 - Kodi NFS support:                     yes
 - Kodi MySQL support:                   yes
 - Kodi Optical Drive support:           yes
 - Kodi SAMBA client support:            yes
 - Kodi SSH client support:              yes
 - Kodi UPNP support:                    yes
 - Kodi Webserver support:               yes
 - Include Skin:                         Confluence
 - Default Skin:                         Confluence
 - Include extra fonts:                  yes

 =================================================================================
 End Configuration for Lakka
 =================================================================================

 **** Your system lacks the following tools needed to build Lakka ****
lsdiff gperf zip makeinfo perl/JSON.pm provided by patchutils gperf zip texinfo libjson-perl
**** You seem to use a debian system ****
would you like to install the needed tools ? (y/n)
 ```

### Make what?

Using `-n` we can see a `dry-run` of the make file:

```bash
DISTRO=Lakka PROJECT=Generic ARCH=x86_64 make -n image
./scripts/image mkimage
```

because of:

```makefile
image:
    ./scripts/image mkimage
```

### Global & Local packages

First thing that happens is calling [scripts/image](https://github.com/libretro/Lakka-LibreELEC/blob/master/scripts/image). At first it **un**sets two variables related to packages:

```bash
unset _CACHE_PACKAGE_LOCAL _CACHE_PACKAGE_GLOBAL
```

* `_CACHE_PACKAGE_LOCAL` points to specific packages related to the `projects` (that is the hardware architecture) you are compiling against. Some of those do not need any special package. One of those examples is the `86_64` PC build (it goes by the name of: `Generic` project) which is has **not** any packages at: `/projects/Generic/packages`.

* `_CACHE_PACKAGE_GLOBAL` includes pretty much all the packages for the distribution (they are about _854_). You can find those by running `find $(pwd)/packages -iname package.mk | wc -l`. For example, [linux](https://github.com/libretro/Lakka-LibreELEC/blob/master/packages/linux/package.mk) kernel is one of those packages.

These two variables are set **later** at [config/path](https://github.com/libretro/Lakka-LibreELEC/blob/master/config/path) settings. So, the first thing happening here is to clear (unset) those variables and start fresh.


### Load configuration options

The second thing that is happening is to `source` the [config/options](https://github.com/libretro/Lakka-LibreELEC/blob/master/config/options) file.

```bash
. config/options $1
```

It is quite usual to _load_ some environment variables and make use of them in _shell_ script. You either define them right there or if these are going to be used by others scripts as well, then it would make sense to have a file with all of those, instead of defining them into all the scripts they need them. Also the `$1` positional parameter is the one passed earlier by the *makefile*. So the command looks like this:

```bash
. config/options mkimage
```

It starts with basic configuration:

```bash
LC_ALL=C # Safety to avoid the user's settings to interfere with your script
DISTRO="Lakka" # That is the default, even if you don't specify it
PROJECT="Generic" # That is the default, even if you don't specify it
ARCH="x86_64" # That is the default, even if you don't specify it
```

That means, we could have done:

```diff
- $ DISTRO=Lakka PROJECT=Generic ARCH=x86_64 make image
+ $ make image
```

It continues with more settings, such as:

```bash
ROOT=`pwd` # This is where you cloned it, e.g. /home/drpaneas/github/Lakka-LibreELEC
DISTRO_DIR="$ROOT/distributions" # https://github.com/libretro/Lakka-LibreELEC/tree/master/distributions
PROJECT_DIR="$ROOT/projects" # https://github.com/libretro/Lakka-LibreELEC/tree/master/projects
LINUX_DEPENDS='/home/drpaneas/github/Lakka-LibreELEC/projects/Generic/linux \
               /home/drpaneas/github/Lakka-LibreELEC/packages/linux/package.mk \
               /home/drpaneas/github/Lakka-LibreELEC/packages/linux-firmware/intel-ucode/package.mk \
               /home/drpaneas/github/Lakka-LibreELEC/packages/linux-firmware/x86-firmware/package.mk'
```

The [projects/Generic/linux](https://github.com/libretro/Lakka-LibreELEC/blob/master/projects/Generic/linux) contains the [kernel configuration](https://github.com/libretro/Lakka-LibreELEC/blob/master/projects/Generic/linux/linux.x86_64.conf) for a typical 64-bit system. This is currently (at the time of writing) based on Kernel _4.11.1_ and it would be great if you don't touch this file -- as this is auto-generated. The other entry is the [kernel package definition](https://github.com/libretro/Lakka-LibreELEC/blob/master/packages/linux/package.mk) itself. The other two files are intel and x86 firmware packages.

### Set the version

It loads anotherer configuration file, by `source`-ing it using the `.` operator:

```bash
. config/version
```

That is basically reading the [config/version](https://github.com/libretro/Lakka-LibreELEC/blob/master/config/version) populating another three extra vars:

```bash
LIBREELEC_VERSION=8.2.5
OS_VERSION=8.2
ADDON_VERSION=8.2
```

Then it tries to read the distro version, but this is not available. e.g. It is looking for `github/Lakka-LibreELEC/distributions/Lakka/versions` which doesn't exist.

### More Options

#### Lakka options

We need to pay attentions here. The script is reading a _big_ file that contains numerous [options](https://github.com/libretro/Lakka-LibreELEC/blob/master/distributions/Lakka/options) related to Lakka. Such options are like _setting the root password_, select _what skins to install_, a bunch (literally) of KODI options, support for various _services_ (such as NFS, Samba, Avahi, etc), graphics drivers, editors, libretro cores, playlist names, playlist cores and so on...

Here, it seems that one of the previously defined variables is not changing its value.

```diff
- LIBREELEC_VERSION=8.2.5
+ LIBREELEC_VERSION="devel"
```

#### Hardware options

There is also another set of [options, related to hardware optimizations](https://github.com/libretro/Lakka-LibreELEC/blob/master/projects/Generic/options) such as what flags should be used for compiling, filesystem's size, audio and graphics support, firmware and other drivers to install, and so on ...

#### Corner case board options

Sometimes there might be configuration needed for specific boards, such as [these boot options](https://github.com/libretro/Lakka-LibreELEC/blob/master/projects/RPi2/boards/RPi4/config/distroconfig.txt) for the RPi4.

#### Your own options

If you want to change any of the previous options, I would highly recommend you to create a _custom_ configuration at `$HOME/.libreelec/options`. This config file is the last one that gets read by the script, as a result it will take precedence compared to the others. Feel free to _overwrite_ any option you didn't like before, via using this personal option file.

### C/C++ compilers and dev tools

To move forward, make sure you have `which gcc` and `which g++` compilers installed. Otherwise, the script will end up with `error 127`. In addition to those it sets a variable `DEVTOOLS=yes` expecting something to get assign there.

### Graphics options

Once again it `source`s the `. config/graphic` that returns the following from a PC:

```bash
mesa = no
OPENGL_SUPPORT=yes
OPENGLES_SUPPORT=no
VULKAN_SUPPORT=no
```

### mkimage

Last but not least, it returns `. config/path mkimage`.