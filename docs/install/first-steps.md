<h1>First steps</h1>

Once you have IsardVDI working you will be able to test demo desktops from updates, but also create new ones with any ISO you can upload.

[TOC]

# Updates

From **updates** menu administrator can download example desktops that will allow checking if you have a fully working virtualization system.

- **TetrOS**: Downloaded and ready by default after finishing wizard.
- Game:
- **Slax**: Fully working slax with network connectivity

# Create your desktop

Administrators and Advanced Users are able to create their own desktops by [uploading ISO](../admin/media.md#upload-media) install file to Media. Administrators can make use of User Media menu and also Administrator Domain Media menu.

## Linux guest

Just [upload an ISO](media.md#upload-media)  to IsardVDI system using the *Add new* top right button in Media menu. When it finishes uploading a desktop icon will be shown to the right of the uploaded file. That desktop button icon will open a [new desktop form](media.md#create-new-desktop-from-uploaded-media)  that will create an install from your linux ISO.

Once you finish installing it, shutdown guest and go to [edit desktop properties](desktops.md#edit-desktop) and change boot device from CD/DVD to HARD DISK.

## Win guest

Follow the same process used for a Linux Guest but, for best Win guest performance, you will need to add some drivers manually before starting the creation steps:

1. From *Uploads* menu download the virtio win ISO and wait till it finishes. This special ISO has Virtio and QXL graphics drivers for Win.
2. [upload a Win ISO](media.md#upload-media)  to IsardVDI . If you want to upload it from a local computer [start a simple webserver](media.md#python-webserver-example) to serve your ISO fle an upload to IsardVDI.
3. Now go to create a new desktop from the desktop icon button next to your media downloaded iso and fill [new desktop form](media.md#create-new-desktop-from-uploaded-media). Be sure to tick *this is a win install iso* checkbox and select appropiate hardware template according to the Win version being installed.

During installation you will have both cd/dvd ISO files available, your Win install and the Virtio Drivers. That will allow you to add Virtio Storage drivers during install and get maximum performance.

Once you finish installing it remember to install inside Win guest all the other drivers contained in Virtio Drivers ISO. After that you can remove all the ISOs from your desktop by [editing desktop properties](desktops.md#edit-desktop) and change boot device from CD/DVD to HARD DISK.

# Template creation

