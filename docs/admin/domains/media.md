<h1>Administer Media</h1>

[TOC]

# Upload media

There is an **Add new** button that will open a new form to make IsardVDI download media from an URL:

- **URL**: Paste here any ISO file download URL or floppy. (Floppies can be used to add storage drivers while installing some OSs in hard disk).
- **NAME**: It will propose by default the file name extracted from the URL. Change it if you prefer another one.
- **TYPE**: Select the type of media being uploaded (ISO/CDROM or Floppy)
- **DESCRIPTION**: Optional
- **ALLOWS**: Allowed roles, categories, groups and users to use this media. Please refer to [allows form](../../user/allows.md#allows-form).

When you click the **Upload** button it will start and you will see progress.

## Uploading media from local storage

If you have your ISO locally in your storage and you want to upload it to IsardVDI you can create a simple webserver which url to the file can be filled in the upload media form in IsardVDI.

**Python webserver example**

If you have *mycdrom.iso* file in a folder you can start a python webserver:

```
$ ls .
mycdrom.iso
$ python -m SimpleHTTPServer
Serving HTTP on 0.0.0.0 port 8000 ...
```

The URL to download mycdrom.iso in IsardVDI upload media will be **http://localhost:8000/mcdrom.iso**

