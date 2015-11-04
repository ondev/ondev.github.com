---
layout: post
title: "编译opencore-amr for iOS8并支持bitcode"
date: 2015-10-23 09:27:30 +0800
comments: true
categories: [iOS]
keywords: [amr, iOS, bitcode]
description: support bitcode, static library
---

由于很多网友向我反应，[这儿](http://www.ifun.cc/blog/2014/02/09/amr-for-ios7/)这个编译脚本不能成功编译iOS8的库，所以在空闲时间搞了一下, 让其支持iOS8与bitcode.

直接上菜

```
#!/bin/sh

set -xe

VERSION="0.1.3"
SDKVERSION="8.4"
LIBSRCNAME="opencore-amr"

CURRENTPATH=`pwd`

mkdir -p "${CURRENTPATH}/src"
tar zxvf ${LIBSRCNAME}-${VERSION}.tar.gz -C "${CURRENTPATH}/src"
cd "${CURRENTPATH}/src/${LIBSRCNAME}-${VERSION}"

DEVELOPER=`xcode-select -print-path`
DEST="${CURRENTPATH}/lib-ios"
mkdir -p "${DEST}"

ARCHS="armv7 armv7s arm64 i386 x86_64"
# ARCHS="armv7"
LIBS="libopencore-amrnb.a libopencore-amrwb.a"

DEVELOPER=`xcode-select -print-path`

for arch in $ARCHS; do
case $arch in
arm*)

IOSV="-miphoneos-version-min=7.0"
if [ $arch == "arm64" ]
then
IOSV="-miphoneos-version-min=7.0"
fi

echo "Building for iOS $arch ****************"
SDKROOT="$(xcrun --sdk iphoneos --show-sdk-path)"
CC="$(xcrun --sdk iphoneos -f clang)"
CXX="$(xcrun --sdk iphoneos -f clang++)"
CPP="$(xcrun -sdk iphonesimulator -f clang++)"
CFLAGS="-isysroot $SDKROOT -arch $arch $IOSV -isystem $SDKROOT/usr/include -fembed-bitcode"
CXXFLAGS=$CFLAGS
CPPFLAGS=$CFLAGS
export CC CXX CFLAGS CXXFLAGS CPPFLAGS

./configure \
--host=arm-apple-darwin \
--prefix=$DEST \
--disable-shared --enable-static
;;
*)
IOSV="-mios-simulator-version-min=7.0"
echo "Building for iOS $arch*****************"

SDKROOT=`xcodebuild -version -sdk iphonesimulator Path`
CC="$(xcrun -sdk iphoneos -f clang)"
CXX="$(xcrun -sdk iphonesimulator -f clang++)"
CPP="$(xcrun -sdk iphonesimulator -f clang++)"
CFLAGS="-isysroot $SDKROOT -arch $arch $IOSV -isystem $SDKROOT/usr/include -fembed-bitcode"
CXXFLAGS=$CFLAGS
CPPFLAGS=$CFLAGS
export CC CXX CFLAGS CXXFLAGS CPPFLAGS
./configure \
--prefix=$DEST \
--disable-shared
;;
esac
make > /dev/null
make install
make clean
for i in $LIBS; do
mv $DEST/lib/$i $DEST/lib/$i.$arch
done
done

for i in $LIBS; do
input=""
for arch in $ARCHS; do
input="$input $DEST/lib/$i.$arch"
done
lipo -create -output $DEST/lib/$i $input
done
```

