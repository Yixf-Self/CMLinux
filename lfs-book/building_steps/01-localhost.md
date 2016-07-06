# 01 - localhost
1. At the begining of the building process, you may first check your localhost with the packages and settings.
```bash
    ./scripts/version-check.sh | grep not```
> Fix the problems by installing the packages and make the links right.
2. Make a dedicated partition for our tiny Linux, the minimal system requires a partition of `4GB` fir storing all the source tarballs and compiling the packages, use `df` to see your status of storages.
```bash
    df -h```
Use `fdisk` to modify the partration table and create mounting process, you should be cautious on modifying the `fstab`
```bash
    man fdisk```
If you are using the X Window mode(init 5), it is much safer by using `GParted`.
3. 