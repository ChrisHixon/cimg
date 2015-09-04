# Cimg

Cimg is a simple bash script to help create, mount, unmount, and check encrypted dm-crypt/LUKS disk image files.

# Usage

$ cimg (command) [(args)]

## Create an image

$ cimg create (filename) (size_in_MB)

## Check an image

$ cimg check (filename)

## Mount an image

$ cimg mount (filename) [(mount_options)]

## Unmount an image

$ cimg unmount|umount (filename)

## Check image status

$ cimg status (filename)

# Notes

This script needs to be run with root permissions. I suggest using the sudo command.

For best results, use the configured filename extension (.cimg by default) for the encrypted image files. This extension will be stripped off to obtain the name used for the device mapper and mount point. For example, for the filename private.cimg, the mapper name will be /dev/mapper/private, and mount point will be /mnt/private). Any file extension will work, but it'll be included in the mapper/mount point name.

# Defaults

There are a few defaults that can be changed in the script code itself:

```
EXT=cimg # encrypted image file extension
MNT=/mnt # base mount directory (images will be mounted at $MNT/image_name)
FS=ext2  # file system used on encrypted image
LUKS_FORMAT_OPTIONS="-v --hash sha512 --cipher aes-xts-plain64 --key-size 512 --use-random" # LUKS format options
```

# Examples

## Create an image

The following will create a 10 MB encrypted disk image named foo.cimg:

````
$ sudo ./cimg create foo.cimg 10
create_image: file='foo.cimg' name='foo' size_m='10'
--> truncate --size 10M foo.cimg
--> cryptsetup luksFormat -v --hash sha512 --cipher aes-xts-plain64 --key-size 512 --use-random foo.cimg

WARNING!
========
This will overwrite data on foo.cimg irrevocably.

Are you sure? (Type uppercase yes): YES
Enter passphrase: 
Verify passphrase: 
Command successful.
--> cryptsetup luksOpen foo.cimg foo
Enter passphrase for foo.cimg: 
offset=4096 blocks=16384
--> dd if=/dev/zero of=/dev/mapper/foo bs=512 count=16384
16384+0 records in
16384+0 records out
8388608 bytes (8.4 MB) copied, 0.0752992 s, 111 MB/s
--> mkfs.ext2 /dev/mapper/foo
mke2fs 1.42.12 (29-Aug-2014)
Creating filesystem with 8192 1k blocks and 2048 inodes

Allocating group tables: done                            
Writing inode tables: done                            
Writing superblocks and filesystem accounting information: done

--> cryptsetup luksClose foo
DONE creating image file 'foo.cimg'
````
