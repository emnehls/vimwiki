# Plans to switch to Void Linux

I would like to convert my home desktop and home/work laptop from Arch Linux to Void Linux.

## Why Void

I am interested in Void for two main reasons:

- It uses Runit instead of Systemd
- The community seems really friendly and unpretentious

In addition to these reasons, the reasons that I chose Arch in the first place are still satisfied with Void.
I want a minimal OS that will allow me to continue using my 12 yo Thinkpad X220 like a modern laptop,
and I want to build the environment that I use from the ground up. 
Honestly, I think most Linux distros sans DE would fit this bill,
but I like the idea of really having only what I need installed and only the services I need running.
I like the OS to challenge me to understand Linux better and be in charge of system maintenance.
I hope to use Linux as my home OS for the rest of my life,
so I would like to get to know it so that I can eventually be at the level of making contributions 
or at least hacking my system to be exactly as I like.
A final minor point of interest is that Void sounds like it is slightly more stable than Arch,
with packages in the main repos undergoing testing before release.
The only reason this is a minor point instead of major is that I have used Arch for about 2
years now and have never had an issue with stability.
I think I have been very lucky in this, as I am not an expert in system maintenance.
This may be a topic for another page, but I don't have a very good backup system in place,
so this is really a gift that I haven't needed to use my shitty blaze backup to restore my system...

## How Void

There are a few things I need to figure out before I commit to and execute this plan:

- Will I be able to install the programs that I use for work easily?
    - I really need R and Rstudio with the packages I use for scRNAseq and general scientific computing up and running
        - They are both in the repo of Void, so I think I should be OK
    - I also need Cisco AnyConnect or an equivalent - it looks like the vpnc package might work
    - I need to be able to mount cifs drives - this is in the repo
- I still need Windows software for Flow Cytometry analysis, so will Void play OK in a dual-boot system
    - I have minimized by Windows use to basically only this flow software and word when I am collaborating with my boss
    - I don't ever access my Linux drive from Windows, I just use cloud and mounted storage and sync code via Github
- How do I install a second operating system on my computer and mount my current home directory to it?
    - This is obviously something that many other people know how to do, so I hope it is easy to figure out

My plan to confirm that all of this is possible is to install Void in a virtualmachine on my home desktop
and document the process of getting it up and running and confirming that all of my must-have software works.
I want to use KVM-QEMU for this instead of VirtualBox.
I have done this on my laptop, but I don't normally document well (hopefully vimwiki helps with that).
I will document my whole process in this vimwiki so that I can replicate it as easily as possible on my desktop/laptop.

## Notes from my descent

### Installings KVM-QEMU on Arch

I'm following a [Veronica Explains video](https://www.youtube.com/watch?v=BgZHbCDFODk), 
because I like her.
I also ended up using this [octetz video](https://www.youtube.com/watch?v=HfNKpT2jo7U) for Arch specific requirements.
I am worried that it is a bad sign that I require so much Arch specific content to be able to do tasks like this.
I know the Void documentation is pretty good, and that the Arch wiki will still be there for me,
but I definitely will likely have to dedicate more time to adapting resources for other distros to Void. 
I will try to document along the way so that I learn better and can help others!
I got 5 minutes into the octetz video about installing the required packages and realized I had already done this at some point...
so back to the Veronica Explains video for the general process of spinning up a VM!
I was able to spin it up after I remembered to start the libvirtd service.
I then made a VM with 40 gb of storage, 4 Gb of RAM, and 4 CPU threads.

### Installing Void in a VM

I am using the 20221001 base live image.
After logging in to the live image as root with the voidlinux pswd and initiating the installer using the `void-installer` command,
I am partitioning the disk with just a EFI system 512M boot partition and the rest for my general use.
When I do this on bare metal I will hopefully keep my current home partition and may include a swap partition on my laptop as well.
I am then setting the boot partition to be FAT32 with a mount point of /boot/efi and the rest as Linux Filesystem with a mount point of /.
Installation was really quick!
But I got a failed to install GRUB to /dev/vda error...
I didn't know how to check tty8 as requested at first, so I just re-ran the installer.
This failed because I already had my filesystems mounted from the first attempt.
My guess as to what failed is that I had selected the wrong filesystem type.
Lets try again! I had to make a new VM.
I am pretty sure it is actually because my motherboard is old and doesn't have EFI.
I should have checked...
Installing with dos with a single root partition worked and I am now in!
That's enough for today. Next time I will get set-up with a wm and basic environment.

### Getting a basic environment up and running

- xorg
- i3-gaps - I've never used i3, but I would like to use it instead of dwm
- all the work apps listed above

First, update and sync xbps with `sudo xbps-install -Syu`. 
This worked with no issue!
Then `sudo xbps-install xorg`. This takes a minute.
After checking that startx is present and using `sudo xbps-query -Rs [package]` to identify some package names,
`sudo xbps-install alacritty i3-gaps i3status i3blocks neovim`.
Finally, `sudo xbps-install dmenu` for a launcher. 
`sudo nvim /etc/X11/xinit/xinitrc` to remove the last block of code and replace it with `exec i3`,
and I'm in!
I have i3 set up with dmenu and alacritty! I think I could start using this now!
I installed firefox and can use it to troubleshoot the rest of the installation,
So these are the steps to set up a minimally usable Void installation with i3 and alacritty!!!
Don't need sudo for xbps-query commands.

A few hours later, I am back at it. 
Installed R and rstudio.
When I open Rstudio and run any command in the console, it gives a warning that 
"R graphics engine version 15 is not supported by this version of Rstudio."
I probably need to solve that. 
I started to play around by just installing tidyverse,
but I got an error and nothing saved.
I started by scrolling through the errors and found one requiring make,
so I isntalled make.
I remember on Arch having to install gcc and maybe gcc-fortran or something,
so I also installed gcc.
I got errors saying that I needed zlib-devel and libxml2-devel.
Still got errors saying I was missing curl and openssl.
I have openssl isntalled, maybe I need openssl-devel?
Yep, and also libcurl-devel.
So far, this is similar to my experience using Arch,
though the rstudio package isn't even in the Arch repos.
It is a AUR package. 
That is it! I got tidyverse installed and can load it!
ggplot and plot work, 
though they are opening a new window instead of using the plot 
window within Rstudio.
I'm satisfied that I can get this to work.

In the meantime, I am playing around with getting a VPN working.
When I search packages on the Void website for AnyConnect,
openconnect comes up. 
It is command line only, so I am reading the man page.
I don't know how to get that to work,
and I am remembering the instructions from the university are to download the cisco client directly from them.
I have to login to get the script. 
I think this is the first time I will come up against the systemd vs. runit issue.

Yeah. I spent about 2 hours trying to edit the install script for the cisco anyconnect client
to include a runit option.
Right now it has systemd and sysvinit.
I was unsuccessful, but I learned a bit.
If a package is installed from the Void repos that required a service,
it will automatically generate a run file in the /etc/sv folder.
To enable the service on startup,
that just needs to be linked to the /var/service folder:
`sudo ln -s /etc/sv/<service-name> /var/service/`.
To make a custom service,
a run script, just a bash script that executes the service, is still needed.
I followed [this](https://www.youtube.com/watch?v=PRpcqj9QR68) video for advice on how to set the run script 
with daemon abilities and sane defaults.
I still wasn't able to get it to work in the end.
I think it is because I don't understand the options for the vpnagentd binary command
that needs to be executed to start the service.
I will have to try again later, because it got late!!!

But I figured out how to shutdown the system!!! It requires the -P flag and needs to be run as root:
`sudo shutdown -P now`

Once I get everything up and running, 
I will make a package list of everything that I need to install and email it to myself or something.
I also want to figure out a system for syncing my dotfiles.


### Getting a basic environment up and running - continued

Upon logging back in some days later,
it appears that my vpnagentd runit script works!
I can launch the vpnui script from Cisco,
and it takes my network, password, and 2FA method.
Then it gives me an error saying it 
failed to perform required client update checks.
What a bummer!

Ok. I gave [openconnect](https://www.infradead.org/openconnect/connecting.html) another try and figured it out!
Basically, with open connect and vpnc-script installed,
you can just run `sudo openconnect --protocol=anyconnect https://<your-work-server>`.
It walked me through username, password, and 2FA method (just as a second password).
Now I'm in! I can check that off my list!

The last thing I need to confirm in the VM is whether I can get my work's
cifs fileshare to mount!
Using my super unsecure script that literally just mounts it as a cifs,
I am able to get this to work.
I just installed cifs-utils and typed my script (which includes my password...) 
into the command line!

That is all I need! 
I used the following command to get all of the packages that I have installed:
`sudo xbps-query -l | awk '{ print $2 }' | xargs -n1 xbps-uhelper getpkgname > ~/packages.txt`.
I am ready to go on to the next step of figuring out the logistics of
backing up my current system,
installing Void on my desktop and laptop,
and remounting my current home directory in each instance!

I will start with my desktop, because it is less essential to my work.
I have a backup script that should encrypt and backup my whole system to backblaze,
so I am running that.
I think mounting my current home directory without formatting it is possible
and easy in the void isntaller,
so once the system is backed-up I'm going to give it a shot!!!

### Actually installing Void on my desktop

This was insanely easy.
I just used the installer and told it not to create a new filesystem
on my home partition.
I installed neovim and immediately regained access to this very wiki.
I needed xclip to be able to interact with the x11 clipboard in nvim.
Then I installed xorg, i3-gaps, i3blocks, i3status, and alacritty.
Then I edited my .xinitrc to exec i3 instead of dwm.
I'm in with a minimally funtional system.
I followed the Void wiki to install chrony,
enable it on start,
and edit my /etc/rc.conf to set hardware clock to localtime
(for dual-booting with Windows).


### A note on Audio

I have only used either ALSA alone or in combination with PulseAudio.
PipeWire is supposed to be a drop-in replacement,
and it is supposed to be really good.
I am going to give it a shot!

I am following the Void wiki page on 
[PipeWire](https://docs.voidlinux.org/config/media/pipewire.html) to do this.
I got an error about xdg_portal search returning null,
and some reading lead me to think that I should
enable dbus and install elogind or a seat daemon.
I will give it a shot!

I couldn't get PipeWire to work,
so I uninstalled everything related to it using 
`sudo xbps-remove -R pipewire`.
Then I installed alsa-utils, pulseaudio,
alsa-pulseaudio-utils, and pavucontrol.
I enabled the alsa service.
Now it works!

### Updating mirrors

The default mirror is in Europe, from what I have heard.
I want to use the official mirror in Chicago,
so I followed the [instructions](https://docs.voidlinux.org/xbps/repositories/mirrors/changing.html) 
in the Void docs to accomplish this.

### Ongoing issues

I played around in the i3 and i3status config files, 
and was able to change the keybindings to what I know from dwm and vim.
I am running into a dependency issue with the drc package in R that I haven't been able to solve.
It appears to have to do with the quantreg package requiring the /bin/ld -lopenblas
command that I it can't find?
I have /bin/ld.
I will have to keep trouble shooting this later. 

I installed Steam successfully after adding the nonfree, multilib, and nonfree-multilib repos,
but it isn't launching games correctly except *No time to explain*.
I suspect it is either a driver issue or a graphics library issue.
In the end, it was because I did not have the right graphics drivers.
I installed mesa-vulkan-radeon (and 32 bit),
mesa-vaapi (and 32 bit, for gpu acceleration),
and mesa-vdpau (and 32 bit, for gpu acceleration).
Now all of my games run!!

Zoom is not available in the repos, 
but it looks like someone has figured out how to install
it using the xbps-src command.
Via is the other package that is not present in
any of the repos that I probably want.

### TexLive

The instructions on the void wiki for texlive installation and use of tlmgr
were unfortunately incomplete.
I cross-referenced the ArchWiki and did some casual hacking.
There were multiple errors in the tlmgr.pl script relating to paths.
I brute forced functionality by directly editing the script
to hard-code the paths as they appear on my computer
and saved a copy of the tlmgr.pl script in 
~/src/scripts/.
The issue has to do with the definition of
`$Master` in the original script.
For some reason, it doesn't concatenate with the rest of the path string
that is defined as `@INC`.
There was an additional typo in the paths in the `@INC` definition.
Initially this appeared to work,
and the `tlmgr init-usertree` command ran without error,
but the `tlmgr paper letter` command is giving me an error
based on the same stupid path concatenation issue.
This seems like something upstream should fix,
but I will try to hack it and may even contribute to the issue.

Upon closer reading,
it looks like I can install the TexLive-bin package
if I want to be able to use tlmgr.
So I uninstalled texlive and installed 
texlive-bin.
