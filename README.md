# Cimg

Cimg is a bash script to help create, mount, unmount, and check encrypted dm-crypt/LUKS disk image files.

# Requirements

- Linux or compatible system, bash, cryptsetup, dm-crypt, LUKS.

# Usage

$ cimg (command) [(args)]

# Commands

## Create an image

$ cimg create (filename) (size_in_MB)

## Check the filesystem on an image

$ cimg check (filename)

## Mount an image

$ cimg mount (filename) [(mount_options)]

## Check image status

$ cimg status (filename)

## Unmount an image

$ cimg unmount|umount (filename)

## Dump the LUKS header of an image

# cimg dump (filename)

# Notes

This script needs to be run with root permission. I suggest using the sudo command.

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

## Check an image filesystem

```
$ sudo ./cimg check foo.cimg
check_image: file='foo.cimg' name='foo'
--> cryptsetup luksOpen foo.cimg foo
Enter passphrase for foo.cimg: 
--> fsck.ext2 /dev/mapper/foo
e2fsck 1.42.12 (29-Aug-2014)
/dev/mapper/foo: clean, 11/2048 files, 306/8192 blocks
--> cryptsetup luksClose foo
```

## Mount an image

```
$ sudo ./cimg mount foo.cimg
mount_image: file='foo.cimg' name='foo' mnt='/mnt/foo' opts='noatime,nodiratime'
--> cryptsetup luksOpen foo.cimg foo
Enter passphrase for foo.cimg: 
--> fsck.ext2 /dev/mapper/foo
e2fsck 1.42.12 (29-Aug-2014)
/dev/mapper/foo: clean, 11/2048 files, 306/8192 blocks
file system is OK
--> mkdir /mnt/foo
--> mount -t ext2 -o noatime,nodiratime /dev/mapper/foo /mnt/foo
```

## Check the status of an image

```
$ sudo ./cimg status foo.cimg
/dev/mapper/foo is active and is in use.
  type:    LUKS1
  cipher:  aes-xts-plain64
  keysize: 512 bits
  device:  /dev/loop2
  loop:    /path/to/foo.cimg
  offset:  4096 sectors
  size:    16384 sectors
  mode:    read/write
```

## Unmount an image

```
$ sudo ./cimg umount foo.cimg
--> umount /mnt/foo
--> rmdir /mnt/foo
--> cryptsetup luksClose foo
```

## Dump the LUKS header of an image:

```
$ sudo ./cimg dump foo.cimg
LUKS header information for foo.cimg

Version:       	1
Cipher name:   	aes
Cipher mode:   	xts-plain64
Hash spec:     	sha512
Payload offset:	4096
MK bits:       	512
...output truncated...
```




