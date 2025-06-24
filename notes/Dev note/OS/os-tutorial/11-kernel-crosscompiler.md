**Goal: Create a development environment to build your kernel**

Follow the instruction in this site.

Preparation
1. add the ENVs
```
export PREFIX="/usr/local/i386elfgcc"
export TARGET=i386-elf
export PATH="$PREFIX/bin:$PATH"
```
2. binutils
```
mkdir /tmp/src
cd /tmp/src
curl -O http://ftp.gnu.org/gnu/binutils/binutils-2.24.tar.gz # If the link 404's, look for a more recent version
tar xf binutils-2.24.tar.gz
mkdir binutils-build
cd binutils-build
../binutils-2.24/configure --target=$TARGET --enable-interwork --enable-multilib --disable-nls --disable-werror --prefix=$PREFIX 2>&1 | tee configure.log
make all install 2>&1 | tee make.log
```
3. Build GCC
```
cd /tmp/src
curl -O https://ftp.gnu.org/gnu/gcc/gcc-4.9.1/gcc-x.y.z.tar.bz
tar xf gcc-x.y.z.tar.bz
mkdir gcc-build
cd gcc-build
../gcc-x.y.z/configure --target=$TARGET --prefix="$PREFIX" --disable-nls --disable-libssp --enable-languages=c --without-headers
make all-gcc 
make all-target-libgcc 
make install-gcc 
make install-target-libgcc
```

if dependencies are missing, run this command:
```
../gcc-x.y.z/contrib/download_prerequisites
```

That's it! You should have all the GNU binutils and the compiler at `$PREFIX/bin`, prefixed by `i386-elf-` to avoid collisions with your system's compiler and binutils.
commands that are useful:
`i386-elf-gcc`
`i386-elf-ld`
`i386-elf-objdump`