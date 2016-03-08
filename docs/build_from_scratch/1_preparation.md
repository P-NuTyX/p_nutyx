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
