# BSDWorkshop2023


## Pre-Installation:

Come to the Workshop with the following pre-downloaded:

For Apple m1/m2:
VMWare Fusion Pro - https://www.vmware.com/products/fusion/fusion-evaluation.html
FreeBSD-13.2-RELEASE-aarch64-disc1.iso - https://download.freebsd.org/releases/arm64/aarch64/ISO-IMAGES/13.2/

For All other Hardware:
VirtualBox - https://www.virtualbox.org/wiki/Downloads
FreeBSD-13.2-RELEASE-amd64-disc1.iso - https://download.freebsd.org/releases/arm64/aarch64/ISO-IMAGES/13.2/



## VirtualBox Installation:

* In VirtualBox, click the New button to start the wizard that will walk you through creating a virtual machine. The important options to keep in mind are the following:

* Type of OS we'll choose FreeBSD 64 bit,

* Use RAM greater than 512MB and less than any number that puts your virtualbox wizard “slider” into the red. Keep your “slider” in the green, and you should be fine giving the machine a decent amount of memory to work with.

* Accept the defaults for the other settings.

* Once the wizard completes, you can start up the virtual machine. Another wizard will appear asking you to point VirtualBox to the installer image for the new virtual machine. 

* Select Install by pressing Enter/Return on the keyboard. At this point, you will have noticed VirtualBox trying to explain that it is capturing your mouse and keyboard to be used inside the virtual machine. Go ahead and dismiss these messages. To use the mouse and keyboard outside of the virtual machine again, you need to press the host key. VirtualBox displays the current host key in the bottom right of the window. This install says Right Ctrl which means you need to press the right control button on the keyboard first to get back control over the mouse and keyboard from the virtual machine.



## VMWare Fusion Installation:

* In VMWare Fusion, click the ‘+’ symbol in the top left corner, then click New to create a new virtual machine.
Choose Create a custom virtual machine and click Continue to proceed. Select Other as the Operating System and FreeBSD 13 64-bit Arm as the Version when prompted:

* Select “create a new virtual disk” and click continue.

* Finish and save the vm, using either the default name, or your own naming convention.

* Before booting, the new virtual machine needs to be given a CD drive to boot from or it will fail on boot. Open it to see this message and do not suspend it, it will need to be running to attach a drive.

* Press command+E to open virtual machine settings and click CD/DVD.

* Choose FreeBSD ISO image, this is the FreeBSD-13.2-RELEASE-aarch64-disc1.iso image that we downloaded earlier. THEN Check the box to connect a CD/DVD drive.

* Right click “virtual machine” in the top bar menu, and select “restart”.




## User Accounts:

* Continue through the installer, hit enter to start installation:

* Continue with the default US keyboard layout, name the computer freebsd-workshop and select the default settings on the Distribution. 

* Select screen (if there is one, it depends on the version of .iso you downloaded) by just pressing ok 

* Next, we need to decide how to partition the disc. On the Partitioning screen, there are options for Auto (ZFS) and Auto (UFS). Select Auto (ZFS), choose stripe as the ZFS pool type and select the disc to use for the pool with the space-bar, name the pool, and proceed with installation. 

* Set a password for the root account on the system. Don’t fret when there are no *** appearing while typing in your root password. Rest assured, the keystrokes are being recorded; they’re just not being shown on the Screen.

* For the Network Configuration section, stay with the em0 interface on the virtual machine.

* Yes, configure IPv4. Yes, use DHCP. Yes, use IPv6. Yes, try SLAAC. 

* The resolver configuration should be populated with at least one DNS value.Use the “Tab” key on the keyboard to move to select ok to continue. 

* For the Time Zone section go with America – North and South, United States of America, Pacific, Skip on both Time & Date screens. 

* For the System Configuration section, select sshd, select powerd and choose ok. 

* Go with the default settings under System Hardening and leave all options blank.

* Say “yes” to the prompt about adding a new user. Use an all lowercase username. Enter the users Full name. Add the user account to the groups wheel, operator, video by responding to the question Login group is .. Invite .. into other groups? []: with wheel operator video then press Enter. Users in the wheel group can run “privileged” commands. The operator group allows users to do things like shutdown and restart the computer without needing to invoke the special privileges from the wheel group. It's a good idea to add users to the video group that is planning on using FreeBSD as a desktop. We chose tcsh for our shell and used defaults for the other fields while finally providing an account password. If all looks ok, proceed without creating any more user accounts.

* For the Final Configuration section, select Handbook. The default of en and choose ok. Once back on the configuration screen, select Exit to finish and yes to proceed with doing manual configuration. The only manual configuration we need to do is to shut down the machine so we can take out the CD. On the virtual machine, the power needs to be off to not get an error when removing the CD, so we’ll issue the shutdown command below to turn off the computer. Type either init 0 or shutdown -p now to shut down. 

### Remove Boot Disk

To take a CD out of a virtual machine in VirtualBox, click on the name of the virtual machine, click on Settings, click on Storage, and click on the tiny CD icon. Notice another tiny CD icon with a tiny down arrow below it next to the Optical Drive section. Use this second CD icon to Remove Disk from Virtual Drive. Click Ok.

For VMWare Fusion, hold down command-E. Click on CD/DVD, and then Un-check the box that say “connect CD/DVD Drive''. Exit out of this screen. Click on Processors and Memory, select more RAM and cores, I personally do 2 cores and 2048 RAM.





# Break





Boot up the first VM, use the Start button to boot the machine and log in as the root user.


## Package Installation / Text Configuration

Type the following commands on the virtual machine:

> pkg

respond to the prompt with y to bootstrap pkg if you didn't install the handbook earlier. We're installing 6 packages in one line. 

> pkg install -y xorg sudo xfce firefox vim-x11 virtualbox-ose-additions

#### remove “virtualbox-ose-additions if using VMWare Fusion, and skip the following 2 commands

> sysrc vboxguest_enable=YES

> sysrc vboxservice_enable=YES

> sysrc moused_enable=YES

> visudo


Type the following to edit the one line of text and get out of the editor:

> /wheel

Type enter or hit return and then type

> j0xxZZ 

(once you hit the first search term, j goes down, 0 goes to the beginning of the line, xx deletes the comment, ZZ saves and quits vi)

Run the following commands:

> sysrc dbus_enable=YES

> dbus-uuidgen > /etc/machine-id

> reboot

After your machine reboots, Log in again, this time as the regular user account that was created. Next type the following command.

> vim .xinitrc

This time we use the vim editor, VI iMproved, which we installed in the previous steps. To begin typing you need to be in “insert mode” and must press i to enter insert mode and begin typing in text. Type in the following one-line configuration:

> exec startxfce4

Exit vim by pressing “Esc” on the keyboard, then type ZZ or :wq followed by Enter/Return.

### Additional Step for VMWare Fusion:

> sudo vim /boot/loader.conf

And add the following:

> efi_max_resolution="1920x1200"

> ums_load="YES"

> reboot

At this point the virtual machine desktop can be started with the following command:

> startx

### Known issues
On OSX, don't use the green button to make the window larger, instead drag from the corner of the window to increase the size.
If you notice that your mouse is kinda slow, or you notice you have a small screen that doesn't fill your VirtualBox window, or if startx just isn't working for you, or your desktop loads but is frozen, shutdown your machine with the Machine menu then the ACPI Shutdown option and then do the following:
Open your VM Settings -> Display tab
Confirm VBoxVGA is selected, ignore the invalid settings alert as we need to use this particular display driver
Click OK and start your machine again.


## FreeBSD Update and package

### Update FreeBSD:

> sudo -i

> freebsd-update fetch install

Now that FreeBSD itself is up to date, we should also update the packages.

> pkg update

> pkg upgrade -y


## Jails Using IOCAGE 

### Install iocage 

> pkg install -y py39-iocage

The following command assumes your zpool is named zroot adjust as necessary. Type zpool status to see the current name of your zpool.

> iocage activate zroot

> iocage list

We should now see an empty table as the output of iocage list, we’ll do some additional housekeeping below to improve performance of iocage. There are many things you can do to optimize performance of various applications. One such performance trick can be done with:

> mount -t fdescfs null /dev/fd

> vi /etc/fstab

Add the following line to the file and exit when finished. Type j until you're at the bottom of the file, the o to open a new line and begin typing, ESC then ZZ to save and quit the file. Press TAB between each of the fields for the fstab file rather than using spaces to separate fields.

> fdescfs /dev/fd fdescfs rw 0 0

Download a Release branch of FreeBSD:

> iocage fetch

We use [Enter] to fetch the default release iocage wants to provide. Verify the download with the following command

> iocage list -r

View the help documentation

> iocage --help

Any subcommands can also have the --help flag appended

> iocage create --help

Create a base jail

> iocage create -T -n JAILNAME ip4_addr="10.0.2.16" -r 13.2-RELEASE

List current jails

> iocage list

Startup the jail, and jump into the console

> iocage console JAILNAME -f

The console command will start the jail if it isn't already started. To manually start a jail use this

> iocage start JAILNAME

Similarly to stop a jail issue

> iocage stop JAILNAME

Verify networking is working

> pkg

Say yes to the pkg bootstrap prompt. Exit the jail console with ctrl + d.

To delete a jail

> iocage destroy JAILNAME


## FreeBSD Handbook
Here is how to open a local copy of the handbook we installed in the Final Configuration:
Look for Firefox in the applications menu. Click on Applications > Internet > Firefox Web Browser
Open Firefox and navigate to the following URL: 

> file:///usr/local/share/doc/freebsd/en/books/handbook/handbook_en.pdf 

This is a local copy of the FreeBSD Handbook, and it will always be available as it doesn't require an active internet connection. That’s all it takes to install FreeBSD. 
