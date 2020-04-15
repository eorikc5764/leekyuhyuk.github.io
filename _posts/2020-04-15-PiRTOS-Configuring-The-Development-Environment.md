---
layout  : post
title   : "[PiRTOS] 개발 환경 구성하기"
date    : 2020-04-15 02:51:00 +0900
category: Raspberry-Pi
---

PiRTOS를 개발하기 전에 개발 환경을 구성해보겠습니다.  
이 글에서는 GNU ARM Embedded Toolchain을 빌드하고, QEMU를 설치합니다.  
(Ubuntu 18.04.4 LTS를 기준으로 작성되었습니다.)

# Cross Compiler

> 크로스 컴파일러(Cross Compiler)는 컴파일러가 실행되는 플랫폼이 아닌 다른 플랫폼에서 실행 가능한 코드를 생성할 수 있는 컴파일러이다.  
크로스 컴파일러 툴은 임베디드 시스템 혹은 여러 플랫폼에서 실행파일을 생성하는데 사용된다.
이것은 운영 체제를 지원하지 않는 마이크로컨트롤러와 같이 컴파일이 실현 불가능한 플랫폼에 컴파일하는데 사용된다.  
이것은 시스템이 사용하는데 하나 이상의 플랫폼을 쓰는 반가상화에 이 도구를 사용하는 것이 더 일반적이게 되었다.  
[Wikipedia - 크로스 컴파일러](https://ko.wikipedia.org/wiki/크로스%20컴파일러)

![GNU GCC Cross Compiler]({{ site.url }}/assets/image/2020-04-15-PiRTOS-Configuring-The-Development-Environment/2020-04-15-PiRTOS-Configuring-The-Development-Environment_1.png)  
Picture Source : *[Preshing on Programming - How to Build a GCC Cross-Compiler
](https://preshing.com/20141119/how-to-build-a-gcc-cross-compiler)*

## 미리 빌드되어있는 Cross Compiler 사용하기

Cross Compiler를 직접 빌드 할 수도 있지만, 빌드 하는데 시간이 소요되고 귀찮기도 합니다.  
그래서 필자는 Github에 `arm-none-eabi` Cross Compiler를 공유해놓았습니다.  
아래의 명령어들을 터미널에 입력하여 진행합니다.

```sh
$ mkdir /opt/arm-none-eabi
$ cd /opt/arm-none-eabi
$ wget https://github.com/LeeKyuHyuk/arm-none-eabi/releases/download/arm-none-eabi-9.3.0/arm-none-eabi-9.3.0-x86_64-linux.tar.xz
$ tar -Jxf arm-none-eabi-9.3.0-x86_64-linux.tar.xz
$ rm arm-none-eabi-9.3.0-x86_64-linux.tar.xz
```

그리고 마무리 작업으로 `PATH`에 경로를 추가해야 합니다.  
`vi ~/.bashrc` 명령어로 `.bashrc`를 수정합니다.
맨 아래 줄에 `export PATH=/opt/arm-none-eabi/bin:$PATH`를 추가하고 저장합니다.
`source ~/.bashrc` 명령어로 수정된 `.bashrc`를 적용하고, `arm-none-eabi-gcc --version`을 입력하여 실행이 되는지 확인합니다.

## Cross Compiler를 직접 빌드하기

위의 방법을 사용하지 않고 Cross Compiler를 직접 빌드 할 수 있습니다.  
아래의 스크립트를 저장하고 실행한 뒤, 위와 같이 `PATH`에 추가해 주면 됩니다.

```sh
#!/bin/sh
set -o nounset
set -o errexit

TARGET=arm-none-eabi
WORKSPACE=`cd "$(dirname "$0")" && pwd`
PREFIX=${WORKSPACE}/arm-none-eabi-9.3.0

BINUTILS=binutils-2.34
GCC=gcc-9.3.0
GMP=gmp-6.2.0
MPC=mpc-1.1.0
MPFR=mpfr-4.0.2
GDB=gdb-9.1

if [ ! -e ${BINUTILS}.tar.xz ]; then
    echo "Download ${BINUTILS}.tar.xz"
    wget ftp://ftp.gnu.org/gnu/binutils/${BINUTILS}.tar.xz
fi
if [ ! -e ${GCC}.tar.xz ]; then
    echo "Download ${GCC}.tar.xz"
    wget ftp://ftp.gnu.org/gnu/gcc/${GCC}/${GCC}.tar.xz
fi
if [ ! -e ${GMP}.tar.xz ]; then
    echo "Download ${GMP}.tar.xz"
    wget ftp://ftp.gnu.org/gnu/gmp/${GMP}.tar.xz
fi
if [ ! -e ${MPC}.tar.gz ]; then
    echo "Download ${MPC}.tar.gz"
    wget ftp://ftp.gnu.org/gnu/mpc/${MPC}.tar.gz
fi
if [ ! -e ${MPFR}.tar.xz ]; then
    echo "Download ${MPFR}.tar.xz"
    wget ftp://ftp.gnu.org/gnu/mpfr/${MPFR}.tar.xz
fi
if [ ! -e ${GDB}.tar.xz ]; then
    echo "Download ${GDB}.tar.xz"
    wget ftp://ftp.gnu.org/gnu/gdb/${GDB}.tar.xz
fi

echo -n "Extracting Binutils... "
tar -Jxf ${BINUTILS}.tar.xz
echo "Done"
echo -n "Extracting GCC... "
tar -Jxf ${GCC}.tar.xz
echo "Done"
echo -n "Extracting GMP... "
tar -Jxf ${GMP}.tar.xz
echo "Done"
echo -n "Extracting MPC... "
tar -xzf ${MPC}.tar.gz
echo "Done"
echo -n "Extracting MPFR... "
tar -Jxf ${MPFR}.tar.xz
echo "Done"
echo -n "Extracting GDB... "
tar -Jxf ${GDB}.tar.xz
echo "Done"

# Build Binutils
mkdir ${WORKSPACE}/binutils-build
cd ${WORKSPACE}/binutils-build
${WORKSPACE}/${BINUTILS}/configure --target=${TARGET} --prefix=${PREFIX} --disable-nls --enable-interwork --enable-multilib --disable-werror
make all install

export PATH=$PATH:${PREFIX}:${PREFIX}/bin

# Build GCC
mv -v ${WORKSPACE}/${GMP} ${WORKSPACE}/${GCC}/gmp
mv -v ${WORKSPACE}/${MPC} ${WORKSPACE}/${GCC}/mpc
mv -v ${WORKSPACE}/${MPFR} ${WORKSPACE}/${GCC}/mpfr
mkdir ${WORKSPACE}/gcc-build
cd ${WORKSPACE}/gcc-build
${WORKSPACE}/${GCC}/configure --target=${TARGET} --prefix=${PREFIX} --with-newlib --with-gnu-as --with-gnu-ld --disable-nls --disable-libssp --disable-gomp --disable-libstcxx-pch --enable-threads --disable-shared --disable-libmudflap --enable-interwork --enable-languages=c
make all install

# Build GDB
mkdir ${WORKSPACE}/gdb-build
cd ${WORKSPACE}/gdb-build
../${GDB}/configure --target=${TARGET} --prefix=${PREFIX} --disable-interwork --enable-multilib --disable-werror
make all install

echo ""
echo "Cross GCC for ${TARGET} installed to ${PREFIX}"
echo ""
```

# QEMU

QEMU를 설치하여 ARM 환경을 가상 머신으로 사용할 수 있습니다. Raspberry Pi가 실제로 있더라도 손쉽게 디버깅을 하기 위해 개발할 때는 QEMU를 사용할 것입니다.  
`sudo apt install qemu-system-arm`으로 QEMU를 설치합니다.  
설치가 완료되면, `qemu-system-arm --version` 명령으로 버전을 확인합니다. (필자는 2.11.1 버전으로 설치가 되었습니다)  
`qemu-system-arm -M ?` 명령어로 'Raspberry Pi 2'를 지원하는지도 확인해봅니다.  
![QEMU Supported Machines]({{ site.url }}/assets/image/2020-04-15-PiRTOS-Configuring-The-Development-Environment/2020-04-15-PiRTOS-Configuring-The-Development-Environment_2.png)
