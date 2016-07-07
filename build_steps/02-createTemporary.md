# 02 - createTemporary.md
- This step is to build a minimal Linux system.This system will contain **just enough tools** to start constructing
the final LFS system and allow a working environment with more user convenience than a minimum
environment would.
- There are two steps in building this minimal system. The first step is to build a **new and host-independent toolchain
(compiler, assembler, linker, libraries, and a few useful utilities)**. The second step uses this toolchain to build the other
**essential tools**.    
\* When building packages there are several assumptions made within the instructions:   
\*\* Several of the packages are patched before compilation, but only when the patch is needed to circumvent a
problem. A patch is often needed in both this and the next chapter, but sometimes in only one or the other.
Therefore, do not be concerned if instructions for a downloaded patch seem to be missing. Warning messages
about offset or fuzz may also be encountered when applying a patch. Do not worry about these warnings, as the
patch was still successfully applied.   
\*\*During the compilation of most packages, there will be several warnings that scroll by on the screen. These are
normal and can safely be ignored. These warnings are as they appear—warnings about deprecated, but not invalid,
use of the C or C++ syntax. C standards change fairly often, and some packages still use the older standard. This is
not a problem, but does prompt the warning.
- Check one last time that the `LFS environment variable` is set up properly:
```bash
    echo $LFS
```

## General Build Process for Each Package
- Place all the sources and patches in a directory that will be accessible from the chroot environment
such as $LFS/sources/. **Do not put sources in $LFS/tools/**.
- Change to the sources directory.
- For each package: 
    - Using the `tar` program, extract the package to be built. **Ensure you are the lfs user
        when extracting the package.**
    - Change to the directory created when the package was extracted.
    - Follow the instructions for building the package.
    - Change back to the sources directory and delete the extracted source directory unless instructed otherwise.
    - In order for calculating time easily, wrap the build commands in a time command like this: `time { ./configure && ... && make && make install; }`.

***Before each package, there shall be a simple table for its name and version, sample:***

| package       | Version       | Package Name
| ------------- |-------------  |-------------
| binutils      | 2.26          | binutils-2.26.tar.bz2


***Now, you have got the basic knowings of the building process, let us start it from zero to hero.***

***You must follow the right order below to keep things right***

---
## binutils-2.26 - Pass 1
The binutils package contains a linker, an assembler, and other tools for handling object files.

| package       | Version       | Package Name
| ------------- |-------------  |-------------
| binutils      | 2.26          | binutils-2.26.tar.bz2

It is important that `binutils` be the first package compiled because both `glibc` and `gcc` perform various tests on the
available linker and assembler to determine which of their own features to enable.

The `binutils` documentation recommends building `binutils` in a dedicated build directory:
```bash
    mkdir -v ./build
    cd ./build
```
\* If you do not know the way of preparing the build process, please jump to General Build Process and read carefully. 

**Now prepare `binutils` for compilation:**
```bash
    ../configure --prefix=/tools   \
        --with-sysroot=$LFS        \
        --with-lib-path=/tools/lib \
        --target=$LFS_TGT          \
        --disable-nls              \
        --disable-werror
```
The meaning of the configure options:

`--prefix=/tools`   
This tells the configure script to prepare to install the Binutils programs in the /tools directory.

`--with-sysroot=$LFS`     
For cross compilation, this tells the build system to look in $LFS for the target system libraries as needed.

`--with-lib-path=/tools/lib`
This specifies which library path the linker should be configured to use.

`--target=$LFS_TGT`  

Because the machine description in the LFS_TGT variable is slightly different than the value returned by the config.guess script, this switch will tell the configure script to adjust Binutil's build system for building a cross linker.

`-disable-nls`      
This disables internationalization as i18n is not needed for the temporary tools.

`--disable-werror`    
This prevents the build from stopping in the event that there are warnings from the host's compiler.

***All the opinions of {configure;make;make install} are really similar, and you need not to know all the meanings of which. For futher knowings please refer to their Offical Websites***

Continue with compiling the package for x86_64 arch:
```bash
    make
    case $(uname -m) in
        x86_64) mkdir -v /tools/lib && ln -sv lib /tools/lib64 ;;
    esac
```
Install the package:
```bash
    make install
```
***Well Done. You have done the first package. So it is easy to describe the process of the configuring and installing, the structure of which is just `./configure && ... && make && make install;`***
## gcc-5.3.0 - Pass 1
The gcc package contains the GNU compiler collection, which includes the C and C++ compilers.
***The configuration process of `gcc` needs `gmp`, `mpfr` and `mpc`***

| package       | Version       | Package Name
| ------------- |-------------  |-------------
| gcc           | 5.3.0         | gcc-5.3.0.tar.bz2
| mpfr          | 3.1.3         | mpfr-3.1.3.tar.xz
| gmp           | 6.1.0         | gmp-6.1.0.tar.xz
| mpc           | 1.0.3         | mpc-1.0.3.tar.gz
\*  The procedures are the same as every other build process as explained earlier, First extract the gcc tarball from the sources directory and then change to the directory created. Only then should you proceed with the instructions below.
```bash
    tar -xf ../mpfr-3.1.3.tar.xz
    cp -vr ../mpfr-3.1.3 ./mpfr
    tar -xf ../gmp-6.1.0.tar.xz
    cp -vr ../gmp-6.1.0 ./gmp
    tar -xf ../mpc-1.0.3.tar.gz
    cp -vr ../mpc-1.0.3 ./mpc
```
***Check again they are at the right position firet.***

The following command will change the location of gcc's default dynamic linker to use the one installed in /tools. It also removes /usr/include from gcc's include search path.
```bash
    for file in \
        $(find gcc/config -name linux64.h -o -name linux.h -o -name sysv4.h)
        do
            cp -uv $file{,.orig}
            sed -e 's@/lib\(64\)\?\(32\)\?/ld@/tools&@g' \
                -e 's@/usr@/tools@g' $file.orig > $file
            echo '
                #undef STANDARD_STARTFILE_PREFIX_1
                #undef STANDARD_STARTFILE_PREFIX_2
                #define STANDARD_STARTFILE_PREFIX_1 "/tools/lib/"
                #define STANDARD_STARTFILE_PREFIX_2 ""' >> $file
            touch $file.orig
        done
```
The gcc documentation recommends building gcc in a dedicated build directory:
```bash
    mkdir -v ./build
    cd       ./build
```
Prepare gcc for compilation:
```bash
../configure                                       \
    --target=$LFS_TGT                              \
    --prefix=/tools                                \
    --with-glibc-version=2.11                      \
    --with-sysroot=$LFS                            \
    --with-newlib                                  \
    --without-headers                              \
    --with-local-prefix=/tools                     \
    --with-native-system-header-dir=/tools/include \
    --disable-nls                                  \
    --disable-shared                               \
    --disable-multilib                             \
    --disable-decimal-float                        \
    --disable-threads                              \
    --disable-libatomic                            \
    --disable-libgomp                              \
    --disable-libquadmath                          \
    --disable-libssp                               \
    --disable-libvtv                               \
    --disable-libstdcxx                            \
    --enable-languages=c,c++
```
The meaning of the configure options:

`--with-newlib`     
Since a working C library is not yet available, this ensures that the inhibit_libc constant is defined when building libgcc. This prevents the compiling of any code that requires libc support.

`--without-headers`     
When creating a complete cross-compiler, gcc requires standard headers compatible with the target system. For our purposes these headers will not be needed. This switch prevents gcc from looking for them.

`--with-local-prefix=/tools`    
The local prefix is the location in the system that gcc will search for locally installed include files. The default is /usr/local. Setting this to /tools helps keep the host location of /usr/local out of this gcc's search path.

`--with-native-system-header-dir=/tools/include`        
By default gcc searches /usr/include for system headers. In conjunction with the sysroot switch, this would translate normally to $LFS/usr/include. However the headers that will be installed in the next two sections will go to $LFS/tools/include. This switch ensures that gcc will find them correctly. In the second pass of gcc, this same switch will ensure that no headers from the host system are found.

`--disable-shared`      
This switch forces gcc to link its internal libraries statically. We do this to avoid possible issues with the host system.

`--disable-decimal-float, --disable-threads, --disable-libatomic, --disable-libgomp, --disable-libquadmath, --disable-libssp, --disable-libvtv, --disable-libstdcxx`            
These switches disable support for the decimal floating point extension, threading, libatomic, libgomp, libquadmath, libssp, libvtv, and the C++ standard library respectively. These features will fail to compile when building a cross-compiler and are not necessary for the task of cross-compiling the temporary libc.

`--disable-multilib`    
On x86_64, LFS does not yet support a multilib configuration. This switch is harmless for x86.

`--enable-languages=c,c++`  
This option ensures that only the C and C++ compilers are built. These are the only languages needed now.

When the process is done, compile and install:
```bash
    make
    make install
```
## linux-4.4.2 API Headers
The Linux API Headers (in linux-4.4.2.tar.xz) expose the kernel's API for use by glibc.

| package       | Version       | Package Name
| ------------- |-------------  |-------------
| linux-4.4.2 API Headers       |     | linux-4.4.2.tar.xz

The Linux Kernel needs to expose an Application Programming Interface (API) for the system's C library (glibc in our LFS) to use. This is done by way of sanitizing various C header files that are shipped in the Linux Kernel source tarball.

Make sure there are no stale files embedded in the package:
```bash
    make mrproper
```
Now extract the user-visible kernel headers from the source. They are placed in an intermediate local directory and copied to the needed location because the extraction process removes any existing files in the target directory.
```bash
    make INSTALL_HDR_PATH=dest headers_install
    cp -rv dest/include/* /tools/include
```
## glibc-2.23
The glibc package contains the main C library. This library provides the basic routines for allocating memory, searching directories, opening and closing files, reading and writing files, string handling, pattern matching, arithmetic, and so on.

| package       | Version       | Package Name
| ------------- |-------------  |-------------
| glibc      | 2.23          | glibc-2.23.tar.xz

The glibc documentation recommends building glibc in a dedicated build directory:
```bash
    mkdir -v ./build
    cd       ./build
```
Next, prepare glibc for compilation:
```bash
../configure                             \
      --prefix=/tools                    \
      --host=$LFS_TGT                    \
      --build=$(../scripts/config.guess) \
      --disable-profile                  \
      --enable-kernel=2.6.32             \
      --enable-obsolete-rpc              \
      --with-headers=/tools/include      \
      libc_cv_forced_unwind=yes          \
      libc_cv_ctors_header=yes           \
      libc_cv_c_cleanup=yes
```
The meaning of the configure options:

`--host=$LFS_TGT, --build=$(../scripts/config.guess)`   
The combined effect of these switches is that Glibc's build system configures itself to cross-compile, using the cross-linker and cross-compiler in /tools.

`--disable-profile`         
This builds the libraries without profiling information. Omit this option if profiling on the temporary tools is necessary.

`--enable-kernel=2.6.32`       
This tells Glibc to compile the library with support for 2.6.32 and later Linux kernels. Workarounds for older kernels are not enabled.

`--enable-obsolete-rpc`     
This installs NIS and RPC related headers that are not installed by default. They are required to build gcc and by several BLFS packages.

`--with-headers=/tools/include`     
This tells Glibc to compile itself against the headers recently installed to the tools directory, so that it knows exactly what features the kernel has and can optimize itself accordingly.

`libc_cv_forced_unwind=yes`     
This means that the configure test for force-unwind support will fail, as it relies on a working linker. The libc_cv_forced_unwind=yes variable is passed in order to inform configure that force-unwind support is available without it having to run the test.

`libc_cv_c_cleanup=yes`         
Similarly, we pass libc_cv_c_cleanup=yes through to the configure script so that the test is skipped and C cleanup handling support is configured.

`libc_cv_ctors_header=yes`      
Similarly, we pass libc_cv_ctors_header=yes through to the configure script so that the test is skipped and gcc constructor support is configured.

When the process is done, compile and install:
```bash
    make
    make install
```
## Check before your next steps
At this point, it is imperative to stop and ensure that the basic functions (compiling and linking) of the new toolchain are working as expected. To perform a sanity check, run the following commands:
```bash
    echo 'int main(){}' > test.c
    $LFS_TGT-gcc test.c
    readelf -l a.out | grep ': /tools'
```
If everything is working correctly, there should be ***no errors***, and the output of the last command will be of the form:
```bash
    [Requesting program interpreter: /tools/lib/ld-linux-x86-64.so.2]
```
If the output is not shown as above or there was no output at all, then something is wrong. Investigate and retrace the steps to find out where the problem is and correct it. This issue ***must*** be resolved before continuing on.Once all is well, clean up the test files:
```bash
    rm -v test.c a.out
```
## libstdc++-5.3.0
`libstdc++` is the standard C++ library. It is needed for the correct operation of the `g++` compiler.libstdc++ is part of the gcc sources. You should first unpack the gcc tarball and change to the gcc-5.3.0 directory.

| package       | Version       | Package Name
| ------------- |-------------  |-------------
| libstdc++     | 5.3.0         | gcc-5.3.0.tar.bz2

Create a ***separate*** build directory, it is another build process, for libstdc++ and enter it:
```bash
    mkdir -v ./build
    cd       ./build
```
Prepare gcc for compilation:
```bash
../libstdc++-v3/configure           \
    --host=$LFS_TGT                 \
    --prefix=/tools                 \
    --disable-multilib              \
    --disable-nls                   \
    --disable-libstdcxx-threads     \
    --disable-libstdcxx-pch         \
    --with-gxx-include-dir=/tools/$LFS_TGT/include/c++/5.3.0
```

All of the configure opinions of which have been appeared in previous process, and typically you shall have a basic known, if it is hard, carry on, you do not need to understand all in first time.

When the process is done, compile and install:
```bash
    make
    make install
```
## gcc-5.3.0 - Pass 2
The gcc package contains the GNU compiler collection, which includes the C and C++ compilers.

| package       | Version       | Package Name
| ------------- |-------------  |-------------
| gcc           | 5.3.0         | gcc-5.3.0.tar.bz2
| mpfr          | 3.1.3         | mpfr-3.1.3.tar.xz
| gmp           | 6.1.0         | gmp-6.1.0.tar.xz
| mpc           | 1.0.3         | mpc-1.0.3.tar.gz

Our first build of `gcc` has installed a couple of ***internal system headers***. Some of the internal headers that gcc installed are partial, self-contained files and ***does not*** include the extended features of the system header. This was adequate for building the temporary libc, but this build of gcc now requires the full internal header. Create a full version of the internal header using a command that is identical to what the gcc build system does in normal circumstances:    
Once again, change the location of `gcc`'s default dynamic linker to use the one installed in /tools.
```bash
    for file in \
        $(find gcc/config -name linux64.h -o -name linux.h -o -name sysv4.h)
        do
            cp -uv $file{,.orig}
            sed -e 's@/lib\(64\)\?\(32\)\?/ld@/tools&@g' \
                -e 's@/usr@/tools@g' $file.orig > $file
            echo '
                #undef STANDARD_STARTFILE_PREFIX_1
                #undef STANDARD_STARTFILE_PREFIX_2
                #define STANDARD_STARTFILE_PREFIX_1 "/tools/lib/"
                #define STANDARD_STARTFILE_PREFIX_2 ""' >> $file
            touch $file.orig
        done
```
***The configuration process of `gcc` needs `gmp`, `mpfr` and `mpc`***
```bash
    tar -xf ../mpfr-3.1.3.tar.xz
    cp -vr ../mpfr-3.1.3 ./mpfr
    tar -xf ../gmp-6.1.0.tar.xz
    cp -vr ../gmp-6.1.0 ./gmp
    tar -xf ../mpc-1.0.3.tar.gz
    cp -vr ../mpc-1.0.3 ./mpc
```
Create a ***separate new*** build directory, it is a rebuild process:
```bash
    mkdir -v ./build
    cd       ./build
```
Now prepare gcc for compilation again:
```bash
CC=$LFS_TGT-gcc                                    \
CXX=$LFS_TGT-g++                                   \
AR=$LFS_TGT-ar                                     \
RANLIB=$LFS_TGT-ranlib                             \
../configure                                       \
    --prefix=/tools                                \
    --with-local-prefix=/tools                     \
    --with-native-system-header-dir=/tools/include \
    --enable-languages=c,c++                       \
    --disable-libstdcxx-pch                        \
    --disable-multilib                             \
    --disable-bootstrap                            \
    --disable-libgomp
```
The meaning of a special opinion:

`--disable-bootstrap`   
For native builds of gcc, the default is to do a "bootstrap" build. This does not just compile gcc, but compiles it several times. It uses the programs compiled in a first round to compile itself a second time, and then again a third time. The second and third iterations are compared to make sure it can reproduce itself flawlessly. This also implies that it was compiled correctly. However, the LFS build method should provide a solid compiler without the need to bootstrap each time.


When the process is done, compile and install:
```bash
    make
    make install
```
As a finishing touch, create a symlink. Many programs and scripts run `cc` instead of `gcc`, which is used to keep programs generic and therefore usable on all kinds of UNIX systems where the GNU C compiler is not always installed.
```bash
    ln -sv gcc /tools/bin/cc
```
## Check again before your next steps
At this point, it is imperative to stop and ensure that the basic functions (compiling and linking) of the new toolchain are working as expected. To perform a sanity check, run the following commands:
```bash
    echo 'int main(){}' > test.c
    cc test.c
    readelf -l a.out | grep ': /tools'
```
If everything is working correctly, there should be ***no errors***, and the output of the last command will be of the form:
```bash
    [Requesting program interpreter: /tools/lib/ld-linux-x86-64.so.2]
```
If the output is not shown as above or there was no output at all, then something is wrong. Investigate and retrace the steps to find out where the problem is and correct it. This issue ***must*** be resolved before continuing on.Once all is well, clean up the test files:
```bash
    rm -v test.c a.out
```
## tcl-core8.6.4
The `tcl` package contains the Tool Command Language which is a cross platform tool language working well with `C`.

| package       | Version       | Package Name
| ------------- |-------------  |-------------
| tcl-core      | 8.6.4         | tcl-core8.6.4-src.tar.gz

Note that the tcl package used here is a minimal version needed to run the LFS tests.

Prepare tcl for compilation:
```bash
    cd ./unix
    ./configure --prefix=/tools
```
Build the package:
```bash
    make
```
Compilation is now complete. As discussed earlier, running the test suite is not mandatory for the temporary tools here in this chapter. To run the tcl test suite anyway, issue the following command:
```bash
    TZ=UTC make test
```
The result may shown as below, and watch for failures:

![tcl_test_result](/resources/tcl_test_result.png)

Install the package:
```bash
    make install
```
Make the installed library writable so debugging symbols can be removed later:
```bash
    chmod -v u+w /tools/lib/libtcl8.6.so
```
Install tcl's headers. The next package, Expect, requires them to build. Then make a necessary symbolic link:
```bash
    make install-private-headers
    ln -sv tclsh8.6 /tools/bin/tclsh
```