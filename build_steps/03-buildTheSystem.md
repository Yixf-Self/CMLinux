#03 - buildTheSystem.md

- Currently, the $LFS/tools directory is owned by the user lfs, a user that exists only on the host system. If the $LFS/
tools directory is kept as is, the files are owned by a user ID without a corresponding account. This is dangerous
because a user account created later could get this same user ID and would own the $LFS/tools directory and all
the files therein, thus exposing these files to possible malicious manipulation.

## Starting
- ***Backup $LFS/tools directory***
- Change the ownership of the $LFS/tools directory to superuser by running the following command:   
```bash
    chown -R root:root $LFS/tools
```
- ***Preparing Virtual Kernel File Systems***   
```bash
    mkdir -pv $LFS/{dev,proc,sys,run}
```
- ***Creating Initial Device Nodes***   
```bash
    mknod -m 600 $LFS/dev/console c 5 1
    mknod -m 666 $LFS/dev/null c 1 3
```
- ***Mounting to the localhost***
```bash
    mount -v --bind /dev $LFS/dev
    mount -vt devpts devpts $LFS/dev/pts -o gid=5,mode=620
    mount -vt proc proc $LFS/proc
    mount -vt sysfs sysfs $LFS/sys
    mount -vt tmpfs tmpfs $LFS/run
    if [ -h $LFS/dev/shm ]; then
        mkdir -pv $LFS/$(readlink $LFS/dev/shm)
    fi
```
- Enter the chroot environment to begin building and installing `the final LFS system`
```bash
    chroot "$LFS" /tools/bin/env -i \
    HOME=/root                  \
    TERM="$TERM"                \
    PS1='\u:\w\$ '              \
    PATH=/bin:/usr/bin:/sbin:/usr/sbin:/tools/bin \
    /tools/bin/bash --login +h
```
