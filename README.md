# CMLinux
DIY a really tiny Linux from Sctrach

![flowchart](/resources/flowchart_0.5.20160706 .png)

Build a Linux from **zero** to **hero**, I will follow the oringinal book of [Linux-From-Sctrach](http://www.linuxfromscratch.org/lfs/downloads/stable/LFS-BOOK-7.9-NOCHUNKS.html) version 7.9 to build a tiny but strong Linux, and I shall call it as CMLinux.

> 
Our Goal is to make a Linux-From-Sctrach with basic tools and keeps it with high perfermance for ***VM*** usage.
> 
Our LFS need to satisfy the LSB Requirements by integrating these paskages:
> 
Bash, Bc, Binutils, Coreutils, Diffutils, File, Findutils, Gawk, Grep, GTK+2, Gzip, M4, Man-DB, Ncurses, Procps, Psmisc, Sed, Shadow, Tar, Util-linux, Zlib
>
So join us, and you will learn and build your Linux for your *VM*

# Project Progress
- 20160707  
    - Comleting the build of the temporary system.
    - Some bugs shall be repaired

# Steps
- Form all scripts for installing and integrating a tiny and strong Linux in ***localhost*** starting with the localhost Linux.

> The standards we shall follow as closely as possible:
>> 
POSIX.1-2008.
>> 
Filesystem Hierarchy Standard (FHS) Version 3.0
>> 
Linux Standard Base (LSB) Version 5.0

- Package the local tiny Linux into a Distribution and optimize it for ***VM*** usages.
- Integrate and test it with other popular open source tools like MariaDB/Apache to form an avaliable Linux for usage or teaching.

# Need Your Help
- You may fork it and create your Linux as your favor.
- Any Problems and questions, **please create an issue**, and we may discuss it and solve it.

# Requirements for Partcipators
> 
No MORE requirements, and we will learn more from it.  -- from Linux with LOVE.

- First thing first, `git` and `GitHub`.
- AMD64 localhost, VT or VT-x better.
- VM Platform, KVM better. `Cent OS 7` and its previous versions are more friendly for building.
- ***DO NOT USE UBUNTU x86_64 12.02+ as the localhost, there exists a series of fatal errors in pending cross-compile***

![VM_for_build](/resources/VM_for_build.png)

> If your localhost is Windows you may first deploy a Linux in VM as the platform for developing, recommending `Ubuntu`, a really friendly Linux with `apt`.

- For developing, you should have some basic knowings about `OS`/`Linux`/`C`/`bash`, you may learn them from:

> 
**Computer Systems: A Programmer's Perspective** -  Randal E.Bryant
> 
**Linux System Programming** - Robert Love 

\* For writing documents, you shall have some knowings about `Markdown`.

# *Enjoy Yourself*

Cherry Mill - My Original building process of CMLinux is performed on `Ubuntu Kylin` with `VS Code` and `bash`. With some bugs and failures, I choose a VM in `Cent OS 7` to cintinue the process.

![build_env_sample](/resources/build_env_sample.png)

---
README.md - 0.6.20160708