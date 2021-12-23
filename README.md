### How to build kernel/module prebuilt for Android build

* Init repo:
```
repo init -u https://github.com/EssentialPhone/kernel-manifest -b refs/tags/QQ1A.200105.088
```
* Sync repo:
```
repo sync -j4 -c
```
* Download [Android NDK r16b](https://dl.google.com/android/repository/android-ndk-r16b-linux-x86_64.zip) and extract it in a toolchain folder
* Download [clang-r377782d](https://android.googlesource.com/platform/prebuilts/clang/host/linux-x86/) and extract it in the toolchain folder
* Export global variables
```
export ARCH=arm64
export CROSS_COMPILE=`pwd`/toolchain/android-ndk-r16b/toolchains/aarch64-linux-android-4.9/prebuilt/linux-x86_64/bin/aarch64-linux-android-
# Build with CLANG
export CLANG_TRIPLE=aarch64-linux-gnu-
export CLANG_PREBUILT_BIN=`pwd`/toolchain/clang-r377782d/bin
export CC_CMD=${CLANG_PREBUILT_BIN}/clang
```
* Build the kernel (For PH-1)
```
cd kernel
make CC=${CC_CMD} mata_defconfig
make CC=${CC_CMD} -j32
```
* Build the WiFi module
```
cd ../qcacld-3.0/
make CC=${CC_CMD} -j32
${CROSS_COMPILE}strip --strip-debug wlan.ko
```
* Use qcacld-3.0/wlan.ko and arch/arm64/boot/Image.gz-dtb as prebuilt for your Android boot image

## Build Essential PH-1 kernel with KASAN/KCOV support

To allow KASAN testing and fuzzing (like with syzkaller), you can compile
PH-1 kernel with mata_kasan_defconfig

```
cd kernel
make CC=${CC_CMD} mata_kasan_defconfig
make CC=${CC_CMD} -j32
```

KCOV, KASAN and additional debug configuration flags (like kernel memory leak detector)
are enabled
