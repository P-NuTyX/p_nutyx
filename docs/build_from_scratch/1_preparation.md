# Preparation

## Host Requirements

This installation is based on [Linux From Scratch](http://www.linuxfromscratch.org/lfs/) check out the
[Linux From Scratch - Host Requirements](http://www.linuxfromscratch.org/lfs/view/development/prologue/hostreqs.html).


1. Use only the **BASH interpreter**

2. Make sure the tools **git**, **tar** and **wget** are installed, under NuTyX:

```bash
cards install git
cards install wget
cards install tar
```

3. If you are under a recent NuTyX-Linux make sure the package `cards.devel` is installed.

```bash
cards install cards.devel
```


## Remove Anything From A Previous Installation

To compile *everything from scratch* it is best to remove any `user`, `group` and `directories` created by a previous
installation.


### Remove User `LFS` And Home-Directory

```bash
userdel --force --remove lfs
```

*Options:*

* `-f, --force`: force removal of files, even if not owned by user
* `-r, --remove`: remove home directory and mail spool


### Remove The Group `LFS`

Normally, by removing the user, the group is removed as well. If not sure run the following command.

```bash
groupdel lfs
```

### Remove Any Existing `LFS` Target Directory

```bash
rm -rf /mnt/lfs
```


## Installation Target

It is possible to build the new linux distribution in a folder, or a dedicated partition.


### Create The Target Directory

```bash
mkdir /mnt/lfs
```

* If you decided to use a dedicated partition: create it, format it and mount it on: `/mnt/lfs`

    * TIP: Make sure this partition is always mounted prior to the start of `pass 1 or 2`.


-----------------------------------------------------------------------------------------------------------------------


## Speed Up Compilation (OPTIONAL) RAM Disk, SAWP File

This is part is OPTIONAL.


### Set Up The RAM Disk

One way to reduce disk access and prolonging the life of the disk as well as to speed up compilation, is by using a
RAM Disk.

Note you need a decent amount of ram that is dynamically used (this won't be used when you are not building a package).
Recommended are 4 GiB or more.


#### 1. Create the build directory

The `scripts/pkgmk.conf_ram_disk.passes` uses as build directory `PKGMK_WORK_DIR="/var/pkgmk/work"`.

```bash
mkdir -p /var/pkgmk
```


#### 2. Mount the tmpfs.

Set **size=80%** or a defined number **size=3G**


```bash
mount -t tmpfs -o defaults,noatime,size=80%  pkgmk /var/pkgmk
```

#### 3. Verify it is enabled

```bash
df -h
```

#### 4. OPTIONAL: Add this line to the end of /etc/fstab to make the change permanent.

```
pkgmk   /var/pkgmk tmpfs  defaults,noatime,size=80%  0 0
```

**NOTE**: since you are now compiling in memory there is no point in keeping -pipe as this will decrease performance.


### Add An Additional SWAP File

#### 1. Create the file to be used for swap. example uses 6GiB

```bash
fallocate -l 6G /6GiB.swap
```

If fallocate fails or is not installed, use the `dd` command to create the file.


#### 2. Format the file for swap.

```bash
mkswap /6GiB.swap
```


#### 3. Optional chmod the swapfile

```
chmod 600 /6GiB.swap
```


#### 4. Enable the swap file

```bash
swapon /6GiB.swap
```


#### 5. Verify it is enabled by viewing the output of the command 

```bash
free -h
```

#### 6. OPTIONAL: Add this line to the end of /etc/fstab to make the change permanent.

```
/6GiB.swap  none  swap  sw 0  0
```


### Useful commands

One can run this in another terminal during compilation.


#### To Check RAM/SWAP usage

```bash
free -h
```


#### To Check TMPFS usage

```bash
df -h
```
