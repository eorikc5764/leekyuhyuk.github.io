---
layout  : post
title   : "[Build Own Linux] Image"
date    : 2020-01-26 23:51:16 +0900
category: Raspberry-Pi
---

[이전 글]({{ site.url }}/article/raspberry-pi/2020/01/26/Build-Own-Linux-Kernel)에서 우리는 커널을 빌드 하였습니다. 이번 시간에는 앞에서 만든 Root File System과 Kernel을 합쳐 Raspberry Pi용 Image를 빌드 해보겠습니다.

# Raspberry Pi Image Build

아래는 제가 작성한 이미지를 빌드하는 스크립트입니다. `image.sh`로 아래 내용을 저장한 뒤, 실행하면 이미지가 빌드됩니다.

빌드 과정은 간단합니다. `mkfs`를 사용하여 Root File System을 ext 파일시스템 이미지를 만든 뒤, `GenImage`로 `boot.vfat`(Kernel과 `dtb` 파일이 포함되어 있습니다)와 생성한 Root File System 합쳐주게 됩니다. `GenImage` Config 파일에 대한 내용은 '[GenImage Readme](https://github.com/pengutronix/genimage/blob/master/README.rst)'를 읽어보시기 바랍니다.

```bash
#!/bin/bash
#
# PiCLFS image generate script
# Optional parameteres below:
set -o nounset
set -o errexit

export WORKSPACE_DIR=$PWD
export SOURCES_DIR=$WORKSPACE_DIR/sources
export OUTPUT_DIR=$WORKSPACE_DIR/out
export BUILD_DIR=$OUTPUT_DIR/build
export TOOLS_DIR=$OUTPUT_DIR/tools
export ROOTFS_DIR=$OUTPUT_DIR/rootfs
export KERNEL_DIR=$OUTPUT_DIR/kernel
export IMAGES_DIR=$OUTPUT_DIR/image

export PATH="$TOOLS_DIR/bin:$TOOLS_DIR/sbin:$PATH"
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

function check_environment {
  if ! [[ -d $SOURCES_DIR ]] ; then
    error "Please download tarball files!"
    error "Run './01-download-packages.sh'"
    exit 1
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

check_environment
total_build_time=$(timer)

rm -rf $IMAGES_DIR $BUILD_DIR
mkdir -pv $IMAGES_DIR $BUILD_DIR
echo '#!/bin/sh' > $BUILD_DIR/_fakeroot.fs
echo "set -e" >> $BUILD_DIR/_fakeroot.fs
echo "chown -h -R 0:0 $ROOTFS_DIR" >> $BUILD_DIR/_fakeroot.fs
echo "$TOOLS_DIR/sbin/mkfs.ext2 -d $ROOTFS_DIR $IMAGES_DIR/rootfs.ext2 120M" >> $BUILD_DIR/_fakeroot.fs
chmod a+x $BUILD_DIR/_fakeroot.fs
$TOOLS_DIR/usr/bin/fakeroot -- $BUILD_DIR/_fakeroot.fs
ln -svf rootfs.ext2 $IMAGES_DIR/rootfs.ext4
mkdir -pv $IMAGES_DIR/boot
cp -Rv $KERNEL_DIR/* $IMAGES_DIR/boot/
cp -v $SOURCES_DIR/{bootcode.bin,fixup.dat,start.elf} $IMAGES_DIR/boot/
echo "root=/dev/mmcblk0p2 rootwait console=tty1 console=ttyAMA0,115200" > $IMAGES_DIR/boot/cmdline.txt
cat > $IMAGES_DIR/boot/config.txt << "EOF"
# Please note that this is only a sample, we recommend you to change it to fit
# your needs.
# You should override this file using a post-build script.
# See http://buildroot.org/manual.html#rootfs-custom
# and http://elinux.org/RPiconfig for a description of config.txt syntax
kernel=Image
# To use an external initramfs file
#initramfs rootfs.cpio.gz
# Disable overscan assuming the display supports displaying the full resolution
# If the text shown on the screen disappears off the edge, comment this out
disable_overscan=1
# How much memory in MB to assign to the GPU on Pi models having
# 256, 512 or 1024 MB total memory
gpu_mem_256=100
gpu_mem_512=100
gpu_mem_1024=100
# enable 64bits support
arm_64bit=1
# enable rpi3 ttyS0 serial console
enable_uart=1
EOF
cat > $BUILD_DIR/genimage.cfg << "EOF"
image boot.vfat {
  vfat {
    files = {
      "boot/bcm2710-rpi-3-b.dtb",
      "boot/bcm2710-rpi-3-b-plus.dtb",
      "boot/bcm2837-rpi-3-b.dtb",
      "boot/bootcode.bin",
      "boot/cmdline.txt",
      "boot/config.txt",
      "boot/fixup.dat",
      "boot/start.elf",
      "boot/overlays",
      "boot/Image"
    }
  }
  size = 18M
}
image sdcard.img {
  hdimage {
  }
  partition boot {
    partition-type = 0xC
    bootable = "true"
    image = "boot.vfat"
  }
  partition rootfs {
    partition-type = 0x83
    image = "rootfs.ext4"
  }
}
EOF
$TOOLS_DIR/usr/bin/genimage \
--rootpath "$ROOTFS_DIR" \
--tmppath "$BUILD_DIR/genimage.tmp" \
--inputpath "$IMAGES_DIR" \
--outputpath "$IMAGES_DIR" \
--config "$BUILD_DIR/genimage.cfg"

success "\nTotal image build time: $(timer $total_build_time)\n"
```

빌드에 성공하면 아래와 같은 화면이 출력됩니다.

![Image Build]({{ site.url }}/assets/image/2020-01-26-Build-Own-Linux-Image/2020-01-26-Build-Own-Linux-Image_1.png)

# SDCard Flash

`out/image`에 들어가면, `sdcard.img` 파일이 생성되어 있습니다.

```
$ sudo dd if=sdcard.img of=/dev/sdX bs=4M
$ sync
```

위의 명령어로 SDCard에 Flash 한 뒤, 라즈베리 파이를 부팅해봅시다.

![minicom]({{ site.url }}/assets/image/2020-01-26-Build-Own-Linux-Image/2020-01-26-Build-Own-Linux-Image_2.png)
