# opencore-amr-lib
Build script and shared libraries for opencore-amr-iOS

---

### Build a static library with bitcode enabled

1. Download the source code [opencore-amr-iOS](https://github.com/feuvan/opencore-amr-iOS)
2. Copy and Save the code below to `build_ios_bitcode.sh`
3. Open Terminal，enter and exec `sh build_ios_bitcode.sh` to build

```
#!/bin/sh

set -xe

VERSION="0.1.3"
SDKVERSION="9.2"
LIBSRCNAME="ios-opencore-amr"

CURRENTPATH=`pwd`

mkdir -p "${CURRENTPATH}/src"
cd "${CURRENTPATH}/"

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
--host=$arch \
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

### References
> [https://github.com/xinyzhao/opencore-amr-lib](https://github.com/xinyzhao/opencore-amr-lib)<br>
> [https://github.com/feuvan/opencore-amr-iOS](https://github.com/feuvan/opencore-amr-iOS)<br>
> [http://blog.csdn.net/chaoyuan899/article/details/51722496](http://blog.csdn.net/chaoyuan899/article/details/51722496)
