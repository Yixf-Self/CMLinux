# 01 - localhost
1. At the begining of the building process, you may first check your localhost with the packages and settings. All process below are carried out on `Ununtu 16.04`, if you are using `rpm` based distributions, you may only change the `apt-get` into `yum`, `dpkg` into `rpm` and so on.If you have the experience of deploying a Linux into your localhost, it is easy to complete the basic steps.
```bash
    ./scripts/version-check.sh | grep not```
> Fix the problems by installing the packages and make the links right.
2. Make a dedicated partition for our tiny Linux, the minimal system requires a partition of `4GB` fir storing all the source tarballs and compiling the packages, use `df` to see your status of storages.
```bash
    df -h```
Use `fdisk` to modify the partration table and create mounting process, you should be cautious on modifying the `fstab`
```bash
    man fdisk```
If you are more satisfied with the X Window (init 5), it is much safer by using `GParted`.
3. Install the `Oracle VirtualBox` to create a VM for test and deploy.
```bash
sudo apt-get install virtualbox```
As the test field of our Linux, you may create a VM with a storage at least 10GB.
4. 