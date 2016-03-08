# Pass1 - Building Of The CHROOT

In this part we will build up a CHROOT environment which will be used to build a Base-NuTyX-Linux in a second pass.

We start by creating folders, links and the right user account.

The first part of preparation is done via the root account of the host and the second directly in the 
unpriviledge account `LFS`.

The build time of the first pass takes about 1 hour( with a 8 cores processor).


## Set Up the `LFS` User

**REMEMBER:** This part is done via the *root account* of the host.


### 1. Export The LFS Configuration Variable

```bash
export LFS=/mnt/lfs
```


### 2. Creation Of Folders And Links

```bash
mkdir -vp $LFS/{sources,tools}
ln -svf $LFS/tools /
ln -svf $LFS/sources /
```


### 3. Creation Of The LFS User

```bash
groupadd lfs
useradd -s /bin/bash -g lfs -m -k /dev/null lfs
passwd lfs
```
Enter the password of your choice.


### 4. Set Owner and Mode Of The Folders

```bash
chown -v lfs $LFS/{tools,sources}
chmod -v a+wt $LFS/sources
chown -v lfs $LFS
```

### 5. Create LFS User Files

**bash_profile**

```bash
cat > /home/lfs/.bash_profile << "EOF"
exec env -i HOME=$HOME TERM=$TERM PS1='\u:\w\$ ' /bin/bash
EOF
```

**bashrc**

```bash
echo "set +h
umask 022
LFS=$LFS
LC_ALL=POSIX
LFS_TGT=$(uname -m)-lfs-linux-gnu
PATH=/home/lfs/bin:/tools/bin:/bin:/usr/bin
export LFS LC_ALL LFS_TGT PATH " > /home/lfs/.bashrc
```

6. Change to the LFS unprivildge user part

```bash
su - lfs
```


## Create the CHROOT

**REMEMBER:** From now on, you should type all the commands as *lfs user*.

Checkout the latest NuTyX-Linux source code

```bash
git clone git://github.com/P-NuTyX/p_nutyx.git current
cd current
```

### Launch The First Script

The script will run some checks and NO error should occur. The downloading of the sources will start automatically. 

NOTE: one can re-run this scripts in case it was interrupted.

```bash
scripts/runmebeforepass1
```

If everything went well, you should read the following messages:

```
=====> Successfull configured
```

If your read this, congratulations you are ready to launch the first pass.


### Run The First PASS Script

```bash
cd /home/lfs/current/chroot
pass
```

**NOTE** you can follow the build and check that everything goes well by consulting the folder 
`/home/lfs/logs/chroot/`. For each *package* a corresponding logfile is created.

We will see in the next section how to enter the CHROOT and to build a Base-NuTyX-Linux.
