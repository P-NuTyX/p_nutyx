# Pass2 - Building Of A Base-NuTyX-Linux

To procceed with this step you MUST have finished the previous part: *Pass1 - Building Of The CHROOT*.


## General Notes

**NOTE**: If you have an error when compiling a package, it's not a big deal, when you restart the command `pass` the 
script will restart where it has stoped. 

**NOTE**: You can build the *pass 2* serveral times. If you went out of the CHROOT, you MUST make sure to 

* Enter AGAIN the CHROOT (step 9)
* Define AGAIN the PATH variable (step 10)

If you rebooted the PC you MUST first mount the needed Folders and Filesystems: (step 6).


## Execute The Required Steps

This part is done via the root account of the host: be very carefull.

**To avoid any confusion there is a short explanation prior each command.**


### 1. Exit From The 'lfs' Account

Its very important that everything is done via the root account.

```bash
exit
```


### 2. Check The `LFS` Variable

MAKE SURE THE **LFS VARIABLE** IS PROPERLY SET OTHERWISE YOU MAY BREAK YOUR HOST DISTRIBUTION.

The LFS variable is used throughout the guide, it MUST be set correctly and that it point always to the same folder. 

```bash
echo $LFS
```

MUST return: `/mnt/lfs`

If the result is correct: we change the owner and group of the folders content.

```bash
chown -R root:root $LFS
```


### 3. Set Up The Port Folders And Links

We create the folders for the NuTyX build-recipes and log files. We create also a link so that the script 
`pass` is found. Afterwards we move the build-recipes (ports) to the right place.

```bash
install -dv -m0750  $LFS/root
ln -sv current/scripts $LFS/root/bin

mv /home/lfs/current $LFS/root/
```


### 4. Build The `nutyx` Package 

We will now compile the first package which is **nutyx*. For this we change first to it's port folder.
This package does not contain any code - it just creates all the folders for a Base-NuTyX-Linux distribution.

Option `pkgmk`: -cf,  --config-file <file>:  use alternative configuration file

```bash
cd $LFS/root/current/base/nutyx
/tools/bin/pkgmk -cf ../../../bin/pkgmk.conf.passes
```

**Install the package.**

Option `pkgadd`: -r, --root <path>:   specify alternative installation root

```bash
/tools/bin/pkgadd -r $LFS nutyx1*
/tools/bin/pkgadd -r $LFS nutyx.man1*
```

**Confirm that it is correctly installed.**

Option `pkginfo`: -r, --root <path>:   specify alternative installation root


```bash
/tools/bin/pkginfo -r $LFS -i
```

This command should return:

```
nutyx 8.0...
nutyx.man 8.0...
```


### 5. Add Default Configuration Files

We add all the defaults configuration files.

```bash
bash $LFS/root/current/scripts/install-nutyx -ic
```


### 6. Mount Needed Folders / Filesystems

```bash
mount -v -B /dev $LFS/dev
mount -vt devpts devpts $LFS/dev/pts
mount -vt proc proc $LFS/proc
mount -vt sysfs sysfs $LFS/sys
if [ -h /dev/shm ]; then rm -f $LFS/dev/shm; mkdir $LFS/dev/shm; fi
mount -vt tmpfs shm $LFS/dev/shm
chmod 1777 /dev/shm
```


### 7. Copy The `resolv.conf` File 

In case of a package needs to download something (for exemple the kernel) we copy the `resolv.conf` file from the host.

```bash
cp -v /etc/resolv.conf $LFS/etc
```


### 8. Recheck The Setup

We check that all the folders/filesystems are correctly mounted via the command: 

```bash
mount|grep $LFS
```

which should return:

```
/dev/sda2 on /mnt/lfs type ext4 (rw)
/dev on /mnt/lfs/dev type none (rw,bind)
devpts on /mnt/lfs/dev/pts type devpts (rw)
proc on /mnt/lfs/proc type proc (rw)
sysfs on /mnt/lfs/sys type sysfs (rw)
shm on /mnt/lfs/dev/shm type tmpfs (rw)
```


### 9. Enter The New NuTyX CHROOT

We can now enter the CHROOT of the new NuTyX-Linux. As it has no program at the right place, we get all kind of 
messages which can be ignored. This will be solved when we redefine the PATH variable.


```bash
chroot "$LFS" /usr/bin/env -i HOME=/root TERM="$TERM" PS1='\u:\w\$ ' \
/bin/bash --login +h
```

The result will show not found commands but we can ignored it:

```
bash: id: command not found
bash: id: command not found
bash: date: command not found
bash: uname: command not found
bash: uname: command not found
bash: whoami: command not found
bash: uname: command not found
bash: tput: command not found
...
```


### 10. Redefine The PATH Variable

```bash
export PATH=/bin:/usr/bin:/sbin:/usr/sbin:/tools/bin:/root/bin
```


### 11. Run The Second PASS

This will launch the compilation of the packages.

```bash
cd /root/current/base
pass
```

return

```
tar -p -o -C /tmp/work/src -xf /sources/linux-3.19.3.tar.xz
...
```

**Wait for the following message to appear**

```
need to install bash manually
```


### 12. Install Bash Manually

TODO: Find a better solution???

This has to be done from outside the CHROOT.

```bash
exit
```

**Now we go to the right place and install the bash packages:**

```bash
cd $LFS/root/current/base/bash
for PACK in *.xz; do /tools/bin/pkgadd -r $LFS $PACK;done
```

**We check if it has been added:**

```
bash 4.3.33
bash.fr 4.3.33
bash.de 4.3.33
...
```


### 13. Enter AGAIN The CHROOT

```bash
chroot "$LFS" /usr/bin/env -i HOME=/root TERM="$TERM" PS1='\u:\w\$ ' \
/bin/bash --login +h
```

Some errors will still show up..

```
bash: id: command not found
bash: id: command not found
bash: date: command not found
bash: uname: command not found
bash: uname: command not found
bash: whoami: command not found
bash: uname: command not found
bash: wc: command not found
...
```


### 14. Define AGAIN The PATH Variable

```bash
export PATH=/bin:/usr/bin:/sbin:/usr/sbin:/tools/bin:/root/bin
```


### 15. Run AGAIN The Second PASS Script


```bash
cd /root/current/base
pass
```

which will return

```
tar -p -o -C /tmp/work/src -xf /sources/bc-1.06.95.tar.bz2
....
```

The compilation ends with the `ca-certificates package:`

```
ADD: ca-certificates-20150725, 1282 files: 100%
=======> Installing 'ca-certificates1418739487x86_64.cards.tar' succeeded.
=======> compress ca-certificates1418739487x86_64.cards.tar
```



