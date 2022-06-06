# PS3 Linux Kit

This is a (soon-to-be-) complete kit containing all of the materials and instructions required to convert a retail PS3 into a general-purpose Linux machine. At the time of writing, guides are few and far between, filled with broken links, outdated instructions, and contradictions. With the console becoming more and more outdated, it is unlikely that this will ever change. Therefore, I am compiling all of the useful and relevant information I have found over the past couple months into a comprehensive guide, in hopes that it will help others and/or aid us in the creation of a PS3 cluster.

## Console Compatibility

All "fat" PS3s are compatible, some "slim" PS3s are compatible, and no "super slim" PS3s are compatible. Anything built pre-2011 is fine; i.e., CECHAxx-CECHQxx, CECH-20xxx, CECH-21xxx, and certain CECH-25xxx models. Your best bet is to find a "fat" or 2000/2100 series "slim". 

The slim models (especially 2100-series and pre-2011 2500-series) have the smallest CPU and GPU die sizes out of all the compatible consoles, and thus have lower power consumption, slightly stronger performance, and better thermals. If you have the choice, it might be nicer to get one of these.

Source: https://www.psdevwiki.com/ps3/SKU_Models

## Jailbreaking your PS3

This [video](https://www.youtube.com/watch?v=QldjWRGH0wA), by MrMario2011, explains the initial jailbreaking process quite well. You will need to install the latest version of the [Rebug toolbox](https://github.com/Joonie86/Rebug-Toolbox/releases) via the technique demonstrated towards the end of the tutorial.

## Setting up OtherOS++

### Downgrading from EvilNat 4.88 to 3.55 Rebug CFW

With the Rebug toolbox installed, you will now need to enable QA flags, load the [3.55 Rebug CFW](http://xanthe.cc/?a=download&file=355_60G_COLDBOOT_PS3UPDAT.PUP) onto your flash drive at `/PS3/UPDATE/PS3UPDAT.PUP`, and install the CFW "update" via the PS3 System Update menu > Install from Storage Media.

### Repartitioning the HDD to make space for Linux

Once it finishes installing, you will need to reboot the PS3 into safe mode, and select the "Restore PS3 System" option. **This will format, repartition, and reinstall the CFW on your PS3's hard drive, *wiping everything* in the process. Make sure you have backed up any valuable data on the hard drive before performing this step!!** 

### Installing Petitboot

In order to install the bootloader, you will need to first determine whether your PS3 has a NAND flash or a NOR flash. If it's a CECHAxx-CECHGxx, you will need the [NAND version of Petitboot](http://xanthe.cc/?a=download&file=dtbImage.ps3.bin.minimal). Otherwise, you will need the [NOR version of Petitboot](http://xanthe.cc/?a=download&file=dtbImage.ps3). Make sure the file is named `dtbImage.ps3` and placed on the root of your USB drive.

The Rebug toolbox file should still be on your USB drive. If it somehow isn't, put it back on the root. Additionally, copy [this script](http://xanthe.cc/?a=download&file=create_hdd_region.sh) onto the root of the USB drive.

Now, enter Rebug toolbox, and resize the VFLASH by navigating to Utilities > Resize VFLASH/NAND Regions. Once this completes, reboot the PS3. Go back into Rebug toolbox, and navigate to Utilities > Install Petitboot.

### Loading Petitboot for the first time

In the Rebug toolbox, navigate to System > Boot OtherOS, and select "use current". At this point, **PS3 controllers will no longer work, and you will need to plug in a USB keyboard.** Once Petitboot loads, select "Exit to Shell". `cd` to `/var/petitboot/mnt`, and check which device contains the `create_hdd_region.sh` script. On NOR devices, it should be `sda1`, and on NAND devices, it will probably be `sdb1`. Once you have located it, run the script. It should do everything automatically at this point.

This step is optional, but may be useful. You can configure Petitboot to automatically select the first option after a specified timeout, by running `ps3-bl-option -O 5` (5 second timeout; you can change this to whatever you want). 

## Installing Linux

If you've successfully followed all of the steps above, congratulations, you're now ready to install Linux! :partying_face: 

### History of PS3-supported Linux distros

#### So many outdated distros...

Historically, [Yellow Dog Linux](https://en.wikipedia.org/wiki/Yellow_Dog_Linux) was the distro of choice for PS3, even being officially supported by Sony. However, after Sony pulled the plug on OtherOS, and Apple stopped producing PowerPC machines, there wasn't much of a market for the distro anymore, and it was discontinued in 2012.

There is a [fairly popular guide](https://www.psx-place.com/threads/tutorial-read-warning-installing-red-ribbon-linux-on-rebug-4-81-2-4-84-2-d-rex.16419/) on the PSX-place forums describing the installation of [Red Ribbon Linux](https://sourceforge.net/p/redribbon/wiki/Home/), however Red Ribbon hasn't been touched since 2014, and its own official website has been down for years. The guide I linked even warns you not to proceed with the installation due to the lack of support; I wouldn't bother with Red Ribbon personally, but you technically can use it if you so desire.

Some of the more popular general Linux distros (e.g. Arch, Fedora, Ubuntu, etc.) have versions that reportedly work on the PS3, but they all seem to be seriously outdated and not really worth trying. Feel free to experiment with these, but YMMV...

#### T2 SDE (System Development Environment)

The only distro I'm aware of that is actively receiving updates for the PS3 is the [T2 System Development Environment](http://t2sde.org). If you're not an experienced Linux hobbyist, you will likely not find it very easy to troubleshoot things with this distro, as it requires quite a bit of UNIX and command-line knowledge. T2 is primarily command-line based, and it does not have a graphical desktop as almost every other modern distro would out of the box. However, it is actively being developed and officially supports PowerPC64 (i.e. PS3) systems, with many platform-specific patches to compensate for the general Linux community's lack of support for less popular CPU architectures. 

T2, in addition to not featuring a graphical interface as the primary mode of interaction, does not have a package manager with online repositories containing pre-built packages. Any software installed in T2 must be built from scratch, and this can make things quite difficult at times. Moreover, its [documentation](http://t2sde.org/documentation/) is not exactly newbie-friendly, meaning getting up to speed with the distro and learning how to use the T2 build toolchain might be very difficult. This makes it less enticing as an option for the PS3, but seeing as there are literally no other active PS3 Linux projects (that I'm aware of at least), this is the best we've got. Besides, with a bit more community support for the project, much of the difficulty involved with this OS can be reduced.

### Performing the installation of your preferred Linux distro

Now that you've decided on a Linux distro, go ahead and grab a PowerPC64 ISO of your distro and burn it to either a DVD or USB flash drive. Plug your DVD/USB into the PS3 and enter the Petitboot menu. If you've turned off the PS3, it should automatically boot to Petitboot. If it's still on from the previous instructions, run the command `petitboot` in the terminal to go back to the main menu. You should see your installation media listed on the menu, but before you select it, you may need to verify that the currently selected video mode is compatible with your TV. You can press 'e' to enter the menu item editor, and make the changes you need. [This documentation](https://manpages.debian.org/wheezy/ps3-utils/ps3-video-mode.8.en.html) explains the video modes very well.

With the video mode sorted out, you can now select the installation media item and proceed with the Linux installation. If you're installing T2, [this video](https://www.youtube.com/watch?v=-Mk8Wgp9UOs) demonstrates the installation on the PS3, and you can pretty much use the same config as the uploader. [This video, by the creator of T2 himself](https://www.youtube.com/watch?v=dQee4IDoUow) may also be helpful. 

### Obtaining packages for your Linux distro

If you're using something other than T2, you're (sort of) in luck, as you probably have a built-in package manager. Refer to your distro's documentation for more information.

If you're using T2 (as you should), you will need to grab the latest version of the T2 source from Subversion, by running `svn co https://svn.exactcode.de/t2/trunk t2-trunk` in your terminal. Make sure you are logged in as root, and `cd` into the `t2-trunk` folder. You should now be able to download and build [packages](http://t2sde.org/packages) by using `./scripts/Emerge-Pkg`. Refer to the [documentation](https://t2sde.org/handbook/html/t2.build.package.single.html) for more information on Emerge-Pkg. 

## Final notes

If there are any issues with this guide, let me know and I will attempt to resolve them on both the guide and your end if needed. I hope that this is helpful to at least someone out there! :-)
