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

