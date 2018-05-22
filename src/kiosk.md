Running a kiosk on Ubuntu Core
This example is ideal for someone seeking to build a kiosk-style device with Ubuntu Core. It showcases a set of Qt applications as examples apps you could run on a kiosk.
These examples have been tested on the following devices: amd64 kvm-qemu based VM, armhf/Raspberry Pi 3 and arm64/dragonboard 410c. To install Ubuntu Core on one of these devices, see the Get Started section.
Note: for RPi2 and RPi3, GPU support is not yet in the stable Ubuntu Core image, you will need to use the daily edge image.
Quick Demo
Download the latest Ubuntu Core stable image or the latest daily edge image for RPi2/3 and install it. Once you have your Ubuntu Core image running on your desired device/VM, SSH into it and install the mir-kiosk and mir-kiosk-apps snaps in this particular order:
snap install mir-kiosk --channel=beta
snap install mir-kiosk-apps --channel=beta


Once this is done, the photoviewer application should start automatically.
There are other examples included. To change applications you need to use the snap set command. For instance, to bring up the RSSNews app:
snap set mir-kiosk-apps app=rssnews


To see the list of available apps, just enter the snap set command with an empty value, like this:
snap set mir-kiosk-apps app=" "


Once you set an app, it will start on the display connected to your device:

Step by step example
Introduction
The mir-kiosk-apps snap is an example of a mir client, which you can use as a template to create your own client applications.
Ubuntu Core provides a solution for GL/GLES drivers to be hosted by the core and accessed by snaps. With this solution in place, you can build an app using GLES on one platform and run on another. For instance, build on Intel and run on Nvidia, or vice versa. Please let the snap team know if you discover any issues around this.
The mir-kiosk snap is only targeted at Ubuntu Core, not other snapd supported OSes. It is intended for kiosk-like products and applications.
This document targets Ubuntu Core devices and assumes your host machine is running an up to date version of Ubuntu 16.04 LTS (or above), using the latest versions of snapcraft and snapd.
Prerequisites
While you can install the mir snaps on a bare metal install of Ubuntu Core following these instructions, this example focuses on using a virtual machine to help people have a quick experience without needing a separate device.
Make sure "Virtual Machine Manager" is installed to have a local display for your VM.
sudo apt install virt-manager


You will also need an Ubuntu SSO account to create the first user on the Ubuntu Core installation during first boot.
Start by creating an Ubuntu SSO account
Import an SSH Key into your Ubuntu SSO account on this page. Instructions to generate an SSH Key on your computer can be found here.
1. Download the Ubuntu Core image and set up your VM environment
Download the latest stable Ubuntu Core image for amd64 and uncompress it with the following commands:
wget http://cdimage.ubuntu.com/ubuntu-core/16/stable/current/ubuntu-core-16-amd64.img.xz
unxz ubuntu-core-16-amd64.img.xz


Then, launch Virtual Machine Manager, select the icon for “New Virtual Machine” (or through the menu: "File" -> "New Virtual Machine").
Before beginning, you may need to set the virtual machine engine. Go to "File" -> "Add Connection", select "QEMU/KVM".
Now, to import the image you just uncompressed:
In the "New Virtual Machine" dialog select the radio button for “Import Existing Disk Image”, browse to your .img file and select it.
You can leave the other defaults. Once you select your way forward, it should launch another window with your Ubuntu Core image VM running in it.
Once the system boots, it will provide you a prompt to walk you through the Ubuntu Core first boot setup. At the end of the process, an IP address and user name will be provided for you to SSH into your device or VM.
2. Install the mir-kiosk snap
Install the mir-kiosk snap with the following commands via SSH into your device or VM.
ssh$ snap install mir-kiosk --channel=beta


mir-kiosk should launch, resulting in a black screen with a mouse cursor.
3. Get the mir-kiosk-apps snap running
This section assumes the Mir server is up and running, and you’ve followed all of the setup steps from the previous section.
On your host, if you haven’t already, install the snapcraft tools.
sudo apt install snapcraft


Now clone the mir-kiosk-apps snap branch.
git clone https://github.com/MirServer/mir-kiosk-apps
cd mir-kiosk-apps


For the purposes of building your own client-application to run on mir-kiosk, I recommend reading through 2 files in this branch: snapcraft.yaml and mir-kiosk-app-daemon. The snapcraft.yaml can be inspected for guidelines on what stage packages are being used. The mir-kiosk-app-daemon file can be used to determine which environment variables need to be set and you may also modify the last lines to change the example application called.
Run snapcraft to build the snap - remember we are building this on a Xenial 16.04 host:
snapcraft


Copy your snap over to your Ubuntu Core device or VM:
scp mir-kiosk-apps*.snap <username>@<IP address>:/home/<username>


Then SSH to the device or VM and install it:
ssh$ snap install mir-kiosk-apps*.snap --dangerous


Once this is done, the photoviewer application should start automatically.
Tips
Check the target's systemd journal journalctl if something isn’t launching or running as expected.
To stop or start the app snap:
ssh$ sudo systemctl stop snap.mir-kiosk-apps.mir-kiosk-app-daemon.service
ssh$ sudo systemctl start snap.mir-kiosk-apps.mir-kiosk-app-daemon.service


To stop or start the Mir snap:
ssh$ sudo systemctl stop snap.mir-kiosk.mir-kiosk.service
ssh$ sudo systemctl start snap.mir-kiosk.mir-kiosk.service


You can check interfaces are connecting with:
ssh$ snap interfaces


You may find that cursor/mouse input is better behaved if you forward the mouse to the VM. To do this, in the VMM menu, click on "Virtual Machine" then "Redirect USB device".
Resources
The projects used for building mir-kiosk and mir-kiosk-apps are located here:
https://github.com/MirServer/mir-kiosk-apps
https://github.com/MirServer/mir-kiosk

