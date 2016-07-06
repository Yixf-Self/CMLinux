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

***Before each package, there shall be a simple table for its name and version, sample:***

| package       | Version       
| ------------- |-------------
| binutils      | 2.26

***Now, you have got the basic knowings of the building process, let us start it from zero to hero.***

