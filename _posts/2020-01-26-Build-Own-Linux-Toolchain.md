---
layout  : post
title   : "[Build Own Linux] Toolchain"
date    : 2020-01-26 23:40:49 +0900
category: Raspberry-Pi
---

이 글에서는 라즈베리 파이(**Raspberry Pi 3**)를 타겟으로한 리눅스 빌드를 위해 소스코드 다운로드와 툴체인(Toolchain)을 빌드 하는 방법을 설명합니다.

# 개발 환경 설정

개발 환경은 리눅스(Ubuntu 18.04)에서 진행합니다.
`sudo apt install gcc g++ make wget` 명령어로 `gcc`, `g++`, `make`, `wget`를 설치합니다.

그리고, `piclfs`라는 폴더를 생성하고, 그 안에서 작업을 진행합니다.

> CLFS는 Cross Linux From Scratch의 약자입니다. 필자는 CLFS 문서의 도움을 많이 받았습니다.

# Source Code Download

리눅스를 빌드하기 위해서는 다양한 프로그램이 필요합니다. 아래 내용을 `wget-list`로 저장합니다.

```
ftp://sourceware.org/pub/elfutils/0.178/elfutils-0.178.tar.bz2
http://ftp.debian.org/debian/pool/main/f/fakeroot/fakeroot_1.24.orig.tar.gz
https://busybox.net/downloads/busybox-1.31.1.tar.bz2
https://downloads.sourceforge.net/project/e2fsprogs/e2fsprogs/v1.45.4/e2fsprogs-1.45.4.tar.gz
https://ftp.gnu.org/gnu/autoconf/autoconf-2.69.tar.xz
https://ftp.gnu.org/gnu/automake/automake-1.16.1.tar.xz
https://ftp.gnu.org/gnu/binutils/binutils-2.33.1.tar.xz
https://ftp.gnu.org/gnu/bison/bison-3.5.tar.xz
https://ftp.gnu.org/gnu/gawk/gawk-5.0.1.tar.xz
https://ftp.gnu.org/gnu/gcc/gcc-9.2.0/gcc-9.2.0.tar.xz
https://ftp.gnu.org/gnu/glibc/glibc-2.30.tar.xz
https://ftp.gnu.org/gnu/gmp/gmp-6.1.2.tar.xz
https://ftp.gnu.org/gnu/libtool/libtool-2.4.6.tar.xz
https://ftp.gnu.org/gnu/m4/m4-1.4.18.tar.xz
https://ftp.gnu.org/gnu/mpc/mpc-1.1.0.tar.gz
https://ftp.gnu.org/gnu/mpfr/mpfr-4.0.2.tar.xz
https://ftp.gnu.org/gnu/mtools/mtools-4.0.23.tar.bz2
https://ftp.openssl.org/source/openssl-1.1.1d.tar.gz
https://github.com/dosfstools/dosfstools/releases/download/v4.1/dosfstools-4.1.tar.xz
https://github.com/martinh/libconfuse/releases/download/v3.2.2/confuse-3.2.2.tar.xz
https://github.com/pengutronix/genimage/releases/download/v11/genimage-11.tar.xz
https://github.com/raspberrypi/firmware/raw/master/boot/bootcode.bin
https://github.com/raspberrypi/firmware/raw/master/boot/fixup.dat
https://github.com/raspberrypi/firmware/raw/master/boot/start.elf
https://github.com/raspberrypi/linux/archive/raspberrypi-kernel_1.20200114-1.tar.gz
https://github.com/westes/flex/releases/download/v2.6.3/flex-2.6.3.tar.gz
https://kernel.org/pub/linux/utils/util-linux/v2.35/util-linux-2.35.tar.xz
https://releases.pagure.org/pkgconf/pkgconf/pkgconf-1.6.3.tar.xz
https://www.zlib.net/zlib-1.2.11.tar.xz
```

`wget -c -i wget-list -P sources` 명령어로 방금 작성한 `wget-list`의 파일들을 `sources` 폴더에 다운로드 받습니다.

![Source Code Download]({{ site.url }}/assets/image/2020-01-26-Build-Own-Linux-Toolchain/2020-01-26-Build-Own-Linux-Toolchain_1.png)

# Toolchain Build

툴체인(Toolchain)은 타겟 시스템(여기서는 Raspberry Pi를 의미합니다)에서 동작하는 프로그램 개발에 필요한 소프트웨어와 개발 환경의 묶음이라고 할 수 있습니다.

우리가 만들 Toolchain에는 ARM Cross Compiler(Assembler, Linker, C/C++ Compiler, C Library)와 Pkgconf, M4, Libtool, Autoconf, Automake, Zlib, Util-linux, E2fsprogs, Fakeroot, Bison, Gawk, Elfutils, Dosfstools, libconfuse, Genimage, Flex, Mtools, Openssl을 설치하게 됩니다. 빌드 되는 프로그램들은 Cross Compile와 Raspberry Pi SDCard Image 생성에 사용됩니다.

아래는 제가 작성한 Toolchain Build Script입니다. `toolchain.sh`로 아래 내용을 저장한 뒤, 실행하면 Toolchain이 빌드 됩니다. 빌드 시간은 대략 20~30분 정도 소요됩니다.

스크립트의 각 환경 변수를 설명하면 아래와 같습니다:
- `PARALLEL_JOBS` : `make`시 최적화된 Parallel Option(`-j`)의 값
- `CONFIG_TARGET` : Cross Compiler와 관련된 환경 변수. Build된 파일이 실행됐을 때 내놓을 바이너리 포맷
- `CONFIG_HOST` : Cross Compiler와 관련된 환경 변수. Build 하고 나서 실행 파일이 실행될 시스템
- `CONFIG_LINUX_ARCH` : Linux Kernel Architecture
- `WORKSPACE_DIR` : 작업을 하는 공간 (이 글에서는 `piclfs` 폴더입니다)
- `SOURCES_DIR` : 소스코드 파일들이 저장되는 공간
- `OUTPUT_DIR` : 생성되는 파일 및 폴더들이 저장되는 공간
- `BUILD_DIR` : 소스코드의 압축이 풀리고, 빌드가 진행되는 공간
- `TOOLS_DIR` : Toolchain이 설치되는 위치
- `SYSROOT_DIR` : Target 시스템의 헤더 및 라이브러리가 있는 디렉터리

```bash
#!/bin/bash
#
# PiCLFS toolchain build script
# Optional parameteres below:
set +h
set -o nounset
set -o errexit
umask 022

export LC_ALL=POSIX
export PARALLEL_JOBS=`cat /proc/cpuinfo | grep cores | wc -l`
export CONFIG_LINUX_ARCH="arm64"
export CONFIG_TARGET="aarch64-linux-gnu"
export CONFIG_HOST=`echo ${MACHTYPE} | sed -e 's/-[^-]*/-cross/'`

export WORKSPACE_DIR=$PWD
export SOURCES_DIR=$WORKSPACE_DIR/sources
export OUTPUT_DIR=$WORKSPACE_DIR/out
export BUILD_DIR=$OUTPUT_DIR/build
export TOOLS_DIR=$OUTPUT_DIR/tools
export SYSROOT_DIR=$TOOLS_DIR/$CONFIG_TARGET/sysroot

export CFLAGS="-O2 -I$TOOLS_DIR/include"
export CPPFLAGS="-O2 -I$TOOLS_DIR/include"
export CXXFLAGS="-O2 -I$TOOLS_DIR/include"
export LDFLAGS="-L$TOOLS_DIR/lib -Wl,-rpath,$TOOLS_DIR/lib"
export PATH="$TOOLS_DIR/bin:$TOOLS_DIR/sbin:$PATH"

export PKG_CONFIG="$TOOLS_DIR/bin/pkg-config"
export PKG_CONFIG_SYSROOT_DIR="/"
export PKG_CONFIG_LIBDIR="$TOOLS_DIR/lib/pkgconfig:$TOOLS_DIR/share/pkgconfig"
export PKG_CONFIG_ALLOW_SYSTEM_CFLAGS=1
export PKG_CONFIG_ALLOW_SYSTEM_LIBS=1

CONFIG_STRIP_AND_DELETE_DOCS=1

# End of optional parameters
function step() {
    echo -e "\e[7m\e[1m>>> $1\e[0m"
}

function success() {
    echo -e "\e[1m\e[32m$1\e[0m"
}

function error() {
    echo -e "\e[1m\e[31m$1\e[0m"
}

function extract() {
    case $1 in
        *.tgz) tar -zxf $1 -C $2 ;;
        *.tar.gz) tar -zxf $1 -C $2 ;;
        *.tar.bz2) tar -jxf $1 -C $2 ;;
        *.tar.xz) tar -Jxf $1 -C $2 ;;
    esac
}

function check_environment_variable {
    if ! [[ -d $SOURCES_DIR ]] ; then
        error "Please download tarball files!"
        error "Run 'make download'."
        exit 1
    fi
}

function check_tarballs {
    LIST_OF_TARBALLS="
      autoconf-2.69.tar.xz
      automake-1.16.1.tar.xz
      binutils-2.33.1.tar.xz
      bison-3.5.tar.xz
      confuse-3.2.2.tar.xz
      dosfstools-4.1.tar.xz
      e2fsprogs-1.45.4.tar.gz
      elfutils-0.178.tar.bz2
      fakeroot_1.24.orig.tar.gz
      flex-2.6.3.tar.gz
      gawk-5.0.1.tar.xz
      gcc-9.2.0.tar.xz
      genimage-11.tar.xz
      glibc-2.30.tar.xz
      gmp-6.1.2.tar.xz
      libtool-2.4.6.tar.xz
      m4-1.4.18.tar.xz
      mpc-1.1.0.tar.gz
      mpfr-4.0.2.tar.xz
      mtools-4.0.23.tar.bz2
      openssl-1.1.1d.tar.gz
      pkgconf-1.6.3.tar.xz
      raspberrypi-kernel_1.20200114-1.tar.gz
      util-linux-2.35.tar.xz
      zlib-1.2.11.tar.xz
    "

    for tarball in $LIST_OF_TARBALLS ; do
        if ! [[ -f $SOURCES_DIR/$tarball ]] ; then
            error "Can't find '$tarball'!"
            exit 1
        fi
    done
}

function do_strip {
    set +o errexit
    if [[ $CONFIG_STRIP_AND_DELETE_DOCS = 1 ]] ; then
        strip --strip-debug $TOOLS_DIR/lib/*
        strip --strip-unneeded $TOOLS_DIR/{,s}bin/*
        rm -rf $TOOLS_DIR/{,share}/{info,man,doc}
    fi
}

function timer {
    if [[ $# -eq 0 ]]; then
        echo $(date '+%s')
    else
        local stime=$1
        etime=$(date '+%s')
        if [[ -z "$stime" ]]; then stime=$etime; fi
        dt=$((etime - stime))
        ds=$((dt % 60))
        dm=$(((dt / 60) % 60))
        dh=$((dt / 3600))
        printf '%02d:%02d:%02d' $dh $dm $ds
    fi
}

check_environment_variable
check_tarballs
total_build_time=$(timer)

step "[1/25] Create toolchain directory."
rm -rf $BUILD_DIR $TOOLS_DIR
mkdir -pv $BUILD_DIR $TOOLS_DIR
ln -svf . $TOOLS_DIR/usr

step "[2/25] Create the sysroot directory"
mkdir -pv $SYSROOT_DIR
ln -svf . $SYSROOT_DIR/usr
mkdir -pv $SYSROOT_DIR/lib
if [[ "$CONFIG_LINUX_ARCH" = "arm" ]] ; then
    ln -snvf lib $SYSROOT_DIR/lib32
fi
if [[ "$CONFIG_LINUX_ARCH" = "arm64" ]] ; then
    ln -snvf lib $SYSROOT_DIR/lib64
fi

step "[3/25] Pkgconf 1.6.1"
extract $SOURCES_DIR/pkgconf-1.6.3.tar.xz $BUILD_DIR
( cd $BUILD_DIR/pkgconf-1.6.3 && \
    ./configure \
    --prefix=$TOOLS_DIR \
    --disable-static \
    --enable-shared \
    --disable-dependency-tracking )
make -j$PARALLEL_JOBS -C $BUILD_DIR/pkgconf-1.6.3
make -j$PARALLEL_JOBS install -C $BUILD_DIR/pkgconf-1.6.3
cat > $TOOLS_DIR/bin/pkg-config << "EOF"
#!/bin/sh
PKGCONFDIR=$(dirname $0)
DEFAULT_PKG_CONFIG_LIBDIR=${PKGCONFDIR}/../@STAGING_SUBDIR@/usr/lib/pkgconfig:${PKGCONFDIR}/../@STAGING_SUBDIR@/usr/share/pkgconfig
DEFAULT_PKG_CONFIG_SYSROOT_DIR=${PKGCONFDIR}/../@STAGING_SUBDIR@
DEFAULT_PKG_CONFIG_SYSTEM_INCLUDE_PATH=${PKGCONFDIR}/../@STAGING_SUBDIR@/usr/include
DEFAULT_PKG_CONFIG_SYSTEM_LIBRARY_PATH=${PKGCONFDIR}/../@STAGING_SUBDIR@/usr/lib

PKG_CONFIG_LIBDIR=${PKG_CONFIG_LIBDIR:-${DEFAULT_PKG_CONFIG_LIBDIR}} \
	PKG_CONFIG_SYSROOT_DIR=${PKG_CONFIG_SYSROOT_DIR:-${DEFAULT_PKG_CONFIG_SYSROOT_DIR}} \
	PKG_CONFIG_SYSTEM_INCLUDE_PATH=${PKG_CONFIG_SYSTEM_INCLUDE_PATH:-${DEFAULT_PKG_CONFIG_SYSTEM_INCLUDE_PATH}} \
	PKG_CONFIG_SYSTEM_LIBRARY_PATH=${PKG_CONFIG_SYSTEM_LIBRARY_PATH:-${DEFAULT_PKG_CONFIG_SYSTEM_LIBRARY_PATH}} \
	exec ${PKGCONFDIR}/pkgconf @STATIC@ "$@"
EOF
chmod 755 $TOOLS_DIR/bin/pkg-config
sed -i -e "s,@STAGING_SUBDIR@,$SYSROOT_DIR,g" $TOOLS_DIR/bin/pkg-config
sed -i -e "s,@STATIC@,," $TOOLS_DIR/bin/pkg-config
rm -rf $BUILD_DIR/pkgconf-1.6.3

step "[4/25] M4 1.4.18"
extract $SOURCES_DIR/m4-1.4.18.tar.xz $BUILD_DIR
( cd $BUILD_DIR/m4-1.4.18 && \
    ./configure \
    --prefix=$TOOLS_DIR \
    --disable-static \
    --enable-shared )
make -j$PARALLEL_JOBS -C $BUILD_DIR/m4-1.4.18
make -j$PARALLEL_JOBS install -C $BUILD_DIR/m4-1.4.18
rm -rf $BUILD_DIR/m4-1.4.18

step "[5/25] Libtool 2.4.6"
extract $SOURCES_DIR/libtool-2.4.6.tar.xz $BUILD_DIR
( cd $BUILD_DIR/libtool-2.4.6 && \
    ./configure \
    --prefix=$TOOLS_DIR \
    --disable-static \
    --enable-shared )
make -j$PARALLEL_JOBS -C $BUILD_DIR/libtool-2.4.6
make -j$PARALLEL_JOBS install -C $BUILD_DIR/libtool-2.4.6
rm -rf $BUILD_DIR/libtool-2.4.6

step "[6/25] Autoconf 2.69"
extract $SOURCES_DIR/autoconf-2.69.tar.xz $BUILD_DIR
( cd $BUILD_DIR/autoconf-2.69 && \
    ./configure \
    --prefix=$TOOLS_DIR \
    --disable-static \
    --enable-shared )
make -j$PARALLEL_JOBS -C $BUILD_DIR/autoconf-2.69
make -j$PARALLEL_JOBS install -C $BUILD_DIR/autoconf-2.69
rm -rf $BUILD_DIR/autoconf-2.69

step "[7/25] Automake 1.16.1"
extract $SOURCES_DIR/automake-1.16.1.tar.xz $BUILD_DIR
( cd $BUILD_DIR/automake-1.16.1 && \
    ./configure \
    --prefix=$TOOLS_DIR \
    --disable-static \
    --enable-shared )
make -j$PARALLEL_JOBS -C $BUILD_DIR/automake-1.16.1
make -j$PARALLEL_JOBS install -C $BUILD_DIR/automake-1.16.1
mkdir -p $SYSROOT_DIR/usr/share/aclocal
rm -rf $BUILD_DIR/automake-1.16.1

step "[8/25] Zlib 1.2.11"
extract $SOURCES_DIR/zlib-1.2.11.tar.xz $BUILD_DIR
( cd $BUILD_DIR/zlib-1.2.11 && ./configure --prefix=$TOOLS_DIR )
make -j1 -C $BUILD_DIR/zlib-1.2.11
make -j1 install -C $BUILD_DIR/zlib-1.2.11
rm -rf $BUILD_DIR/zlib-1.2.11

step "[9/25] Util-linux 2.35"
extract $SOURCES_DIR/util-linux-2.35.tar.xz $BUILD_DIR
( cd $BUILD_DIR/util-linux-2.35 && \
    ./configure \
    --prefix=$TOOLS_DIR \
    --disable-static \
    --enable-shared \
    --without-python \
    --enable-libblkid \
    --enable-libmount \
    --enable-libuuid \
    --without-ncurses \
    --without-ncursesw \
    --without-tinfo \
    --disable-makeinstall-chown \
    --disable-agetty \
    --disable-chfn-chsh \
    --disable-chmem \
    --disable-login \
    --disable-lslogins \
    --disable-mesg \
    --disable-more \
    --disable-newgrp \
    --disable-nologin \
    --disable-nsenter \
    --disable-pg \
    --disable-rfkill \
    --disable-schedutils \
    --disable-setpriv \
    --disable-setterm \
    --disable-su \
    --disable-sulogin \
    --disable-tunelp \
    --disable-ul \
    --disable-unshare \
    --disable-uuidd \
    --disable-vipw \
    --disable-wall \
    --disable-wdctl \
    --disable-write \
    --disable-zramctl )
make -j$PARALLEL_JOBS -C $BUILD_DIR/util-linux-2.35
make -j$PARALLEL_JOBS install -C $BUILD_DIR/util-linux-2.35
rm -rf $BUILD_DIR/util-linux-2.35

step "[10/25] E2fsprogs 1.45.4"
extract $SOURCES_DIR/e2fsprogs-1.45.4.tar.gz $BUILD_DIR
( cd $BUILD_DIR/e2fsprogs-1.45.4 && \
    ac_cv_path_LDCONFIG=true \
    ./configure \
    --prefix=$TOOLS_DIR \
    --disable-static \
    --enable-shared \
    --disable-defrag \
    --disable-e2initrd-helper \
    --disable-fuse2fs \
    --disable-libblkid \
    --disable-libuuid \
    --disable-testio-debug \
    --enable-symlink-install \
    --enable-elf-shlibs \
    --with-crond-dir=no )
make -j$PARALLEL_JOBS -C $BUILD_DIR/e2fsprogs-1.45.4
make -j$PARALLEL_JOBS install -C $BUILD_DIR/e2fsprogs-1.45.4
rm -rf $BUILD_DIR/e2fsprogs-1.45.4

step "[11/25] Fakeroot 1.24"
extract $SOURCES_DIR/fakeroot_1.24.orig.tar.gz $BUILD_DIR
( cd $BUILD_DIR/fakeroot-1.24 && \
    ac_cv_header_sys_capability_h=no \
    ac_cv_func_capset=no \
    ./configure \
    --prefix=$TOOLS_DIR \
    --disable-static \
    --enable-shared )
make -j$PARALLEL_JOBS -C $BUILD_DIR/fakeroot-1.24
make -j$PARALLEL_JOBS install -C $BUILD_DIR/fakeroot-1.24
rm -rf $BUILD_DIR/fakeroot-1.24

step "[12/25] Bison 3.5"
extract $SOURCES_DIR/bison-3.5.tar.xz $BUILD_DIR
( cd $BUILD_DIR/bison-3.5 && \
    ./configure \
    --prefix=$TOOLS_DIR \
    --disable-static \
    --enable-shared )
make -j$PARALLEL_JOBS -C $BUILD_DIR/bison-3.5
make -j$PARALLEL_JOBS install -C $BUILD_DIR/bison-3.5
rm -rf $BUILD_DIR/bison-3.5

step "[13/25] Gawk 5.0.1"
extract $SOURCES_DIR/gawk-5.0.1.tar.xz $BUILD_DIR
( cd $BUILD_DIR/gawk-5.0.1 && \
    ./configure \
    --prefix=$TOOLS_DIR \
    --disable-static \
    --enable-shared \
    --without-readline \
    --without-mpfr )
make -j$PARALLEL_JOBS -C $BUILD_DIR/gawk-5.0.1
make -j$PARALLEL_JOBS install -C $BUILD_DIR/gawk-5.0.1
rm -rf $BUILD_DIR/gawk-5.0.1

step "[14/25] Binutils 2.33.1"
extract $SOURCES_DIR/binutils-2.33.1.tar.xz $BUILD_DIR
mkdir -pv $BUILD_DIR/binutils-2.33.1/binutils-build
( cd $BUILD_DIR/binutils-2.33.1/binutils-build && \
    MAKEINFO=true \
    $BUILD_DIR/binutils-2.33.1/configure \
    --prefix=$TOOLS_DIR \
    --target=$CONFIG_TARGET \
    --disable-multilib \
    --disable-werror \
    --disable-shared \
    --enable-static \
    --with-sysroot=$SYSROOT_DIR \
    --enable-poison-system-directories \
    --disable-sim \
    --disable-gdb )
make -j$PARALLEL_JOBS configure-host -C $BUILD_DIR/binutils-2.33.1/binutils-build
make -j$PARALLEL_JOBS -C $BUILD_DIR/binutils-2.33.1/binutils-build
make -j$PARALLEL_JOBS install -C $BUILD_DIR/binutils-2.33.1/binutils-build
rm -rf $BUILD_DIR/binutils-2.33.1

step "[15/25] Gcc 9.2.0 - Static"
tar -Jxf $SOURCES_DIR/gcc-9.2.0.tar.xz -C $BUILD_DIR
extract $SOURCES_DIR/gmp-6.1.2.tar.xz $BUILD_DIR/gcc-9.2.0
mv -v $BUILD_DIR/gcc-9.2.0/gmp-6.1.2 $BUILD_DIR/gcc-9.2.0/gmp
extract $SOURCES_DIR/mpfr-4.0.2.tar.xz $BUILD_DIR/gcc-9.2.0
mv -v $BUILD_DIR/gcc-9.2.0/mpfr-4.0.2 $BUILD_DIR/gcc-9.2.0/mpfr
extract $SOURCES_DIR/mpc-1.1.0.tar.gz $BUILD_DIR/gcc-9.2.0
mv -v $BUILD_DIR/gcc-9.2.0/mpc-1.1.0 $BUILD_DIR/gcc-9.2.0/mpc
mkdir -pv $BUILD_DIR/gcc-9.2.0/gcc-build
( cd $BUILD_DIR/gcc-9.2.0/gcc-build && \
    MAKEINFO=missing \
    CFLAGS_FOR_TARGET="-D_LARGEFILE_SOURCE -D_LARGEFILE64_SOURCE -D_FILE_OFFSET_BITS=64 -Os" \
    CXXFLAGS_FOR_TARGET="-D_LARGEFILE_SOURCE -D_LARGEFILE64_SOURCE -D_FILE_OFFSET_BITS=64 -Os" \
    $BUILD_DIR/gcc-9.2.0/configure \
    --prefix=$TOOLS_DIR \
    --build=$CONFIG_HOST \
    --host=$CONFIG_HOST \
    --target=$CONFIG_TARGET \
    --with-sysroot=$SYSROOT_DIR \
    --disable-static \
    --enable-__cxa_atexit \
    --with-gnu-ld \
    --disable-libssp \
    --disable-multilib \
    --disable-decimal-float \
    --disable-libquadmath \
    --enable-tls \
    --enable-threads \
    --without-isl \
    --without-cloog \
    --with-abi="lp64" \
    --with-cpu=cortex-a53 \
    --enable-languages=c \
    --disable-shared \
    --without-headers \
    --disable-threads \
    --with-newlib \
    --disable-largefile )
make -j$PARALLEL_JOBS gcc_cv_libc_provides_ssp=yes all-gcc all-target-libgcc -C $BUILD_DIR/gcc-9.2.0/gcc-build
make -j$PARALLEL_JOBS install-gcc install-target-libgcc -C $BUILD_DIR/gcc-9.2.0/gcc-build
rm -rf $BUILD_DIR/gcc-9.2.0

step "[16/25] Raspberry Pi Linux 4.19.93 API Headers"
extract $SOURCES_DIR/raspberrypi-kernel_1.20200114-1.tar.gz $BUILD_DIR
make -j$PARALLEL_JOBS ARCH=$CONFIG_LINUX_ARCH mrproper -C $BUILD_DIR/linux-raspberrypi-kernel_1.20200114-1
make -j$PARALLEL_JOBS ARCH=$CONFIG_LINUX_ARCH headers_check -C $BUILD_DIR/linux-raspberrypi-kernel_1.20200114-1
make -j$PARALLEL_JOBS ARCH=$CONFIG_LINUX_ARCH INSTALL_HDR_PATH=$SYSROOT_DIR headers_install -C $BUILD_DIR/linux-raspberrypi-kernel_1.20200114-1
rm -rf $BUILD_DIR/linux-raspberrypi-kernel_1.20200114-1

step "[17/25] glibc 2.30"
extract $SOURCES_DIR/glibc-2.30.tar.xz $BUILD_DIR
mkdir $BUILD_DIR/glibc-2.30/glibc-build
( cd $BUILD_DIR/glibc-2.30/glibc-build && \
    CC="$TOOLS_DIR/bin/$CONFIG_TARGET-gcc" \
    CXX="$TOOLS_DIR/bin/$CONFIG_TARGET-g++" \
    AR="$TOOLS_DIR/bin/$CONFIG_TARGET-ar" \
    AS="$TOOLS_DIR/bin/$CONFIG_TARGET-as" \
    LD="$TOOLS_DIR/bin/$CONFIG_TARGET-ld" \
    RANLIB="$TOOLS_DIR/bin/$CONFIG_TARGET-ranlib" \
    READELF="$TOOLS_DIR/bin/$CONFIG_TARGET-readelf" \
    STRIP="$TOOLS_DIR/bin/$CONFIG_TARGET-strip" \
    CFLAGS="-O2 " CPPFLAGS="" CXXFLAGS="-O2 " LDFLAGS="" \
    ac_cv_path_BASH_SHELL=/bin/sh \
    libc_cv_forced_unwind=yes \
    libc_cv_ssp=no \
    $BUILD_DIR/glibc-2.30/configure \
    --target=$CONFIG_TARGET \
    --host=$CONFIG_TARGET \
    --build=$CONFIG_HOST \
    --prefix=/usr \
    --enable-shared \
    --without-cvs \
    --disable-profile \
    --without-gd \
    --enable-obsolete-rpc \
    --enable-kernel=4.19 \
    --with-headers=$SYSROOT_DIR/usr/include )
make -j$PARALLEL_JOBS -C $BUILD_DIR/glibc-2.30/glibc-build
make -j$PARALLEL_JOBS install_root=$SYSROOT_DIR install -C $BUILD_DIR/glibc-2.30/glibc-build
rm -rf $BUILD_DIR/glibc-2.30

step "[18/25] Gcc 9.2.0 - Final"
tar -Jxf $SOURCES_DIR/gcc-9.2.0.tar.xz -C $BUILD_DIR
extract $SOURCES_DIR/gmp-6.1.2.tar.xz $BUILD_DIR/gcc-9.2.0
mv -v $BUILD_DIR/gcc-9.2.0/gmp-6.1.2 $BUILD_DIR/gcc-9.2.0/gmp
extract $SOURCES_DIR/mpfr-4.0.2.tar.xz $BUILD_DIR/gcc-9.2.0
mv -v $BUILD_DIR/gcc-9.2.0/mpfr-4.0.2 $BUILD_DIR/gcc-9.2.0/mpfr
extract $SOURCES_DIR/mpc-1.1.0.tar.gz $BUILD_DIR/gcc-9.2.0
mv -v $BUILD_DIR/gcc-9.2.0/mpc-1.1.0 $BUILD_DIR/gcc-9.2.0/mpc
mkdir -v $BUILD_DIR/gcc-9.2.0/gcc-build
( cd $BUILD_DIR/gcc-9.2.0/gcc-build && \
    MAKEINFO=missing \
    CFLAGS_FOR_TARGET="-D_LARGEFILE_SOURCE -D_LARGEFILE64_SOURCE -D_FILE_OFFSET_BITS=64 -Os" \
    CXXFLAGS_FOR_TARGET="-D_LARGEFILE_SOURCE -D_LARGEFILE64_SOURCE -D_FILE_OFFSET_BITS=64 -Os" \
    $BUILD_DIR/gcc-9.2.0/configure \
    --prefix=$TOOLS_DIR \
    --build=$CONFIG_HOST \
    --host=$CONFIG_HOST \
    --target=$CONFIG_TARGET \
    --with-sysroot=$SYSROOT_DIR \
    --enable-__cxa_atexit \
    --with-gnu-ld \
    --disable-libssp \
    --disable-multilib \
    --disable-decimal-float \
    --disable-libquadmath \
    --enable-tls \
    --enable-threads \
    --with-abi="lp64" \
    --with-cpu=cortex-a53 \
    --enable-languages=c,c++ \
    --with-build-time-tools=$TOOLS_DIR/$CONFIG_TARGET/bin \
    --enable-shared \
    --disable-libgomp )
make -j$PARALLEL_JOBS gcc_cv_libc_provides_ssp=yes -C $BUILD_DIR/gcc-9.2.0/gcc-build
make -j$PARALLEL_JOBS install -C $BUILD_DIR/gcc-9.2.0/gcc-build
if [ ! -e $TOOLS_DIR/bin/$CONFIG_TARGET-cc ]; then
    ln -vf $TOOLS_DIR/bin/$CONFIG_TARGET-gcc $TOOLS_DIR/bin/$CONFIG_TARGET-cc
fi
rm -rf $BUILD_DIR/gcc-9.2.0

step "[19/25] Elfutils 0.178"
extract $SOURCES_DIR/elfutils-0.178.tar.bz2 $BUILD_DIR
( cd $BUILD_DIR/elfutils-0.178 && \
    ./configure \
    --prefix=$TOOLS_DIR \
    --disable-static \
    --enable-shared \
    --disable-debuginfod )
make -j$PARALLEL_JOBS -C $BUILD_DIR/elfutils-0.178
make -j$PARALLEL_JOBS install -C $BUILD_DIR/elfutils-0.178
rm -rf $BUILD_DIR/elfutils-0.178

step "[20/25] Dosfstools 4.1"
extract $SOURCES_DIR/dosfstools-4.1.tar.xz $BUILD_DIR
( cd $BUILD_DIR/dosfstools-4.1 && \
    ./configure \
    --prefix=$TOOLS_DIR \
    --disable-static \
    --enable-shared \
    --enable-compat-symlinks )
make -j$PARALLEL_JOBS -C $BUILD_DIR/dosfstools-4.1
make -j$PARALLEL_JOBS install -C $BUILD_DIR/dosfstools-4.1
rm -rf $BUILD_DIR/dosfstools-4.1

step "[21/25] libconfuse 3.2.2"
extract $SOURCES_DIR/confuse-3.2.2.tar.xz $BUILD_DIR
( cd $BUILD_DIR/confuse-3.2.2 && \
    ./configure \
    --prefix=$TOOLS_DIR \
    --disable-static \
    --enable-shared )
make -j$PARALLEL_JOBS -C $BUILD_DIR/confuse-3.2.2
make -j$PARALLEL_JOBS install -C $BUILD_DIR/confuse-3.2.2
rm -rf $BUILD_DIR/confuse-3.2.2

step "[22/25] Genimage 11"
extract $SOURCES_DIR/genimage-11.tar.xz $BUILD_DIR
( cd $BUILD_DIR/genimage-11 && \
    ./configure \
    --prefix=$TOOLS_DIR \
    --disable-static \
    --enable-shared )
make -j$PARALLEL_JOBS -C $BUILD_DIR/genimage-11
make -j$PARALLEL_JOBS install -C $BUILD_DIR/genimage-11
rm -rf $BUILD_DIR/genimage-11

step "[23/25] Flex 2.6.4"
extract $SOURCES_DIR/flex-2.6.3.tar.gz $BUILD_DIR
( cd $BUILD_DIR/flex-2.6.3 && \
    ./configure \
    --prefix=$TOOLS_DIR \
    --disable-static \
    --enable-shared \
    --disable-doc )
make -j$PARALLEL_JOBS -C $BUILD_DIR/flex-2.6.3
make -j$PARALLEL_JOBS install -C $BUILD_DIR/flex-2.6.3
rm -rf $BUILD_DIR/flex-2.6.3

step "[24/25] Mtools 4.0.23"
extract $SOURCES_DIR/mtools-4.0.23.tar.bz2 $BUILD_DIR
( cd $BUILD_DIR/mtools-4.0.23 && \
    ac_cv_lib_bsd_gethostbyname=no \
    ac_cv_lib_bsd_main=no \
    ac_cv_path_INSTALL_INFO= \
    ./configure \
    --prefix=$TOOLS_DIR \
    --disable-static \
    --enable-shared \
    --disable-doc )
make -j1 -C $BUILD_DIR/mtools-4.0.23
make -j1 install -C $BUILD_DIR/mtools-4.0.23
rm -rf $BUILD_DIR/mtools-4.0.23

step "[25/25] Openssl 1.1.1d"
extract $SOURCES_DIR/openssl-1.1.1d.tar.gz $BUILD_DIR
( cd $BUILD_DIR/openssl-1.1.1d && \
    ./config \
    --prefix=$TOOLS_DIR \
    --openssldir=$TOOLS_DIR/etc/ssl \
    --libdir=lib \
    no-tests \
    no-fuzz-libfuzzer \
    no-fuzz-afl \
    shared \
    zlib-dynamic )
make -j$PARALLEL_JOBS -C $BUILD_DIR/openssl-1.1.1d
make -j$PARALLEL_JOBS install -C $BUILD_DIR/openssl-1.1.1d
rm -rf $BUILD_DIR/openssl-1.1.1d

do_strip

success "\nTotal toolchain build time: $(timer $total_build_time)\n"
```

빌드에 성공하면 아래와 같은 화면이 출력됩니다.

![Toolchain Build]({{ site.url }}/assets/image/2020-01-26-Build-Own-Linux-Toolchain/2020-01-26-Build-Own-Linux-Toolchain_2.png)

[다음 글]({{ site.url }}/article/raspberry-pi/2020/01/26/Build-Own-Linux-Root-File-System)에서는 Raspberry Pi의 Root File System을 빌드 해봅시다.
