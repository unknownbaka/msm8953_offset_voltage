### cpu voltage offset on msm8953
The shell script use for msm8953(Snapdragon 625) undervolt

```
#include <std_disclaimer.h>

/*
 * Your warranty is now void.
 *
 * We are not responsible for bricked devices, dead SD cards,
 * thermonuclear war or you getting fired because the alarm app failed. Please
 * do some research if you have any concerns about features included in this ROM
 * before flashing it! YOU are choosing to make these modifications and if
 * you point the finger at us for messing up your device, we will laugh at you. Hard & a lot.
 *
 */
```

### prebuilt tools
There are 3 prebuilt tools in prebuilt/.  
1. magiskboot used to pack/unpack boot.img.
2. dtp used to split kernel_dtb into sub kernel_dtb-*.(one kernel_dtb exract from boot.img contains some dtbs, only one dtb is vaild)
3. dtc used to decompile dtb file to dts file.

### processes
1. use `dd` command to get boot.img
2. use `magiskboot` to unpack boot.img into kernel+kernel_dtb
3. use `dtp` to split kernel_dtb into sub kernel_dtb-*
4. find the adapted dtb according to `qcom, board-id` and `qcom, msm-id`, and then use `dtc` to decompile the selected dtb(binary) into .dts(source)
5. Modify screen refresh rate by change `qcom,mdss-dsi-panel-framerate` `qcom,mdss-dsi-max-refresh-rate` in device-tree source file
6. undervolt by change `qcom,cpr-open-loop-voltage-fuse-adjustment` `qcom,cpr-closed-loop-voltage-fuse-adjustment` `qcom,cpr-closed-loop-voltage-adjustment` in device-tree source file
7. compile dts to dtb and pack boot.img

### prepare
1. Termux: [Google Play](https://play.google.com/store/apps/details?id=com.termux)(or other)
2. Your device is rooted

### usage
```
./dtb_process.sh -i -c -f [-u undervolt] [-b overvolt] 
    -i              install boot.img after generation
    -c              does not cleanup workspace after finished(you wanna debug)
    -f              force backup the current boot(milestone) to /sdcard/Android/, otherwise only backup boot on first time
    -u              cpu undervolt value, default 0, range(0-125), unit mv
    -b              cpu overvolt value, default 0, range(0-125), unit mv
    -s              screen refresh rate value, default unchanged, unit hz
```

### Let's go
#### get the srcipt

open termux bash and get script:
```
$ su
:/data/data/com.termux/files/home # curl https://github.com/unknownbaka/msm8998_offset_voltage/blob/msm8953/dtb_process.sh > dtb_process.sh
```

undervolt!!
~~if not set `-u` and `-g`, default undervolt cpu 100mv~~(default 0 mv)

```
:/data/data/com.termux/files/home # sh dtb_process.sh -u 100
```

install the new boot  
```
:/data/data/com.termux/files/home # dd if=./new-boot.img of=/dev/block/bootdevice/by-name/boot
```

or use option `-i`(then you don't need step `install the new boot`)

```
:/data/data/com.termux/files/home # ./dtb_process.sh -u 100 -i
```

same as overvolt
~~please remember set `-u` and `-b` 0, because~~ (no longer need) the final `offset=(-b value)-(-u value)`

```
:/data/data/com.termux/files/home # ./dtb_process.sh -b 100
```

if you want to change the screen refresh rate

```
:/data/data/com.termux/files/home # ./dtb_process.sh -s 90
```

if you are boring

```
:/data/data/com.termux/files/home # ./dtb_process.sh -u 90 -b 100    # same as ./dtb_process.sh -b 10
```

### restore
if something goes wrong, you can restore your origin boot, please check /sdcard/Android/ for the original boot image name.    

do remember to delete old boot.img after flashed a new ROM, otherwise the script will not backup the new rom boot.

```
su
dd if=/sdcard/Android/boot-*.img of=/dev/block/bootdevice/by-name/boot  # change it!
```

### special thanks
* [asto18089](https://github.com/asto18089)
* 南昌狗头人(coolapk)