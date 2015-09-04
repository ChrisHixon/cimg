# Cimg

Cimg is a simple bash script to help create, mount, unmount, and check encrypted dm-crypt/LUKS disk image files.

# Usage

$ cimg <command> <args>

## Creating an image

$ cimg create <filename> <size_in_MB>

## Check an image

$ cimg check <filename>

## Mount an image

$ cimg mount <filename> <mount_options>

## Unmount an image

$ cimg (umount|unmount) <filename>

## Check image status

$ cimg status <filename>

# Defaults

At this time, there are a few defaults that can be changed in the script code itself:

