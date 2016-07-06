# 01 - localhost
-  At the begining of the building process, you may first check your localhost with the packages and settings. All process below are carried out on `Ununtu 16.04`, if you are using `rpm` based distributions, you may only change the `apt-get` into `yum`, `dpkg` into `rpm` and so on.If you have the experience of deploying a Linux into your localhost, it is easy to complete the basic steps.Fix the problems by installing the packages and make the links right.
```bash
    ./scripts/version-check.sh | grep not```

- Make a dedicated partition for our tiny Linux, the minimal system requires a partition of `4GB` fir storing all the source tarballs and compiling the packages, use `df` to see your status of storages.
```bash
    df -h
```
Use `fdisk` to modify the partration table and create mounting process, you should be cautious on modifying the `fstab`.
```bash
    man fdisk
```
Recommend using the ext3/ext4 as the format.
If you are more satisfied with the X Window (init 5), it is much safer by using `GParted`.
- Install the `Oracle VirtualBox` to create a VM for test and deploy.
```bash
    sudo apt-get install virtualbox
```
As the test field of our Linux, you may create a VM with a storage at least 10GB.
```bash
    export LFS=<Your dir of the LFS>
```
Export the environment variable, and the variable `LFS` will be used several times. You should ensure that this variable is always defined throughout the LFS build process.
Recommending write it in the .bash_profile 
```bash
    echo "export LFS=<Your dir of the LFS>" >> ~/.bash_profile;. ~/.bash_profile
```
 You may comlete the process not only in your home dir, but also the superuser's.
-  Retrieve all the source packages recommending for or building process, the list of which lies in `/resources/src_pkg.list`, you may use wget the list of URLs to make it easier.In addition to the packages, several patches are also required. These patches correct any mistakes in the packages that should be fixed by the maintainer. The patches also make small modifications to make the packages easier to work with.
```bash
    sudo mkdir -v $LFS/sources
    sudo chmod -v a+wt $LFS/sources
    wget --input-file=./resources/src_pkg_wget.list --continue --directory-prefix=$LFS/sources
    wget --input-file=./resources/src_patch_wget.list --continue --directory-prefix=$LFS/sources
```