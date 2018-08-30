## 1.1 Prerequisites
Used ubuntu 18.04 for build system. you need to install following packages.

```bash
$ sudo apt-get install android-tools-adb android-tools-fastboot autoconf \
	automake bc bison build-essential cscope curl device-tree-compiler \
	expect flex ftp-upload gdisk iasl libattr1-dev libc6:i386 libcap-dev \
	libfdt-dev libftdi-dev libglib2.0-dev libhidapi-dev libncurses5-dev \
	libpixman-1-dev libssl-dev libstdc++6:i386 libtool libz1:i386 make \
	mtools netcat python-crypto python-serial python-wand unzip uuid-dev \
	xdg-utils xterm xz-utils zlib1g-dev
```

## 1.2 Install Android repo
Note that here you don't install a huge SDK, it's simply a Python script that
you download and put in your `$PATH`, that's it. Exactly how to "install" repo,
could be found in the Google [repo] pages, so follow those instructions before
continuing.

## 1.3 Get the source code
Choose the manifest corresponding to the platform you intend to use.

```bash
$ mkdir -p $HOME/devel/optee
$ cd $HOME/devel/optee
$ repo init -u https://github.build.ge.com/212576259/manifest.git -m nano.xml -b master
$ repo sync
```
This will take lot of time as it will fetch all the different repo contents and create copies of it in your local.

## 1.4 Get the toolchains
OP-TEE is using different toolchains for different targets (depends on
ARMv8-A 64/32bit solutions). In any case start by downloading the
toolchains by:
```bash
$ cd build
$ make toolchains
```
also NanoPi uses dirrent toolchain so if you want to use one/ same toolchain modify the Makefiles accordigly else do following
```bash
$ mkdir -p /opt/FriendlyARM/toolchain
$ tar xf arm-cortexa9-linux-gnueabihf-4.9.3.tar.xz -C /opt/FriendlyARM/toolchain/
$ export PATH=/opt/FriendlyARM/toolchain/4.9.3/bin:$PATH
$ export GCC_COLORS=auto
$ . ~/.bashrc
```
to make the changes effective immidiately 

## 1.5 Build the solution
I've configured our repo manifests, so that repo will always automatically
symlink the `Makefile` to the correct device specific makefile, that means that
you simply start the build by running:

```bash
$ make -j12 
```
This step will also take some time.

## 1.6 Flash the device
On the device :
```bash
$ make flash
```
This is just to make sd card ready.

## 1.7 Copy rootfs to sd card 

```bash
$ cd out-br 
$ sudo cp -r rootfs.cpio.gz /media/rootfs
```
##1.8 Copy boot files to sd card 


here you have two ways : 

1) you can copy all the files to sd card 
2) create a boot.img and copy img to sd card
 

#Preparing for boot image 
```bash
$ sudo cp -r ../linux/arch/arm64/boot/dts/allwinner/sun50i-h5-nanopi*.dtb .
$ sudo cp -r ../linux/arch/arm64/boot/dts/allwinner/overlay* .
$ sudo cp -r ../linux/arch/arm64/boot/Image .
$ sudo cp ../build/boot.cmd .
$ sudo mkimage -C none -A arm -T script -d boot.cmd boot.scr 

```
#creating boot image
```bash
$ cd ..
$ sudo mkisofs -lJR -o boot.iso out-br/
$ sudo dd if=boot.iso of=boot.img

```

now copy the image to /media/boot partition on sd card 
remove the card and put it in nanopi neo plus 2 board 

## 1.8  Boot up the device
once the device boots from sd card login using 
```bash
$ root 
$fa

```


## 1.10 Load tee-supplicant
On some solutions tee-supplicant is already loaded (`$ ps aux | grep
tee-supplicant`) on other not. If it's not loaded, then start it by running:
```bash
$ tee-supplicant &
```
here is where I am currently debugging the issue. 


## 1.11 Run xtest
The entire xtest test suite has been deployed when you we're running `$ make
run` in previous step, i.e, in general there is no need to copy any binaries
manually. Everything has been put into the root FS automatically. So, to run
xtest, you simply type:
```bash
$ xtest
```



## Some Userfull notes and learning details for all the modules in the framework 


1) Creating optee_os for support of Nanopi neo plus 2 board

2) Creating U-boot for  Nanopi neo plus 2 board

3) Creating Arm-Trusted Firmware for Nanopi neo plus 2 board






