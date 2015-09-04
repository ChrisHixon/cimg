# Cimg

Cimg is a simple bash script to help create, mount, unmount, and check encrypted dm-crypt/LUKS disk image files.

# Usage

$ cimg (command) [(args)]

## Creating an image

$ cimg create (filename) (size_in_MB)

## Check an image

$ cimg check (filename)

## Mount an image

$ cimg mount (filename) [(mount_options)]

## Unmount an image

$ cimg unmount|umount (filename)

## Check image status

$ cimg status (filename)

# Tips

For best results, use the configured filename extension (.cimg by default) for the encrypted image files. This extension will be stripped off to obtain the name used for the device mapper and mount point (e.g. for filename private.cimg, the mapper will be /dev/mapper/private, and mount point will be /mnt/private). However, any file extension will work but it'll be included in the mapper/mount point name.

# Defaults

There are a few defaults that can be changed in the script code itself:

EXT=cimg # encrypted image file extension

MNT=/mnt # base mount directory (images will be mounted at $MNT/image_name)

FS=ext2  # file system used on encrypted image

LUKS_FORMAT_OPTIONS="-v --hash sha512 --cipher aes-xts-plain64 --key-size 512 --use-random" # LUKS format options

