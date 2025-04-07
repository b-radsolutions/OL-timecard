# Driver

Driver is based on a kernel module for CentOS and Ubunutu. 
This repository adds easy compile and install support to Oracle Linux Systems.  
Kernel 5.12+ is recommended

## Oracle Linux Instructions
Steps to install on OL systems, loaded on Ubuntu 20.04+ by default
1. Make sure vt-d option is enabled in BIOS.
2. Install kernel-devel, get your kernel version  
   `$ uname -r`  
   Ex. => `5.15.0-206.153.7.1.el8uek.x86_64`  
   Yum Package will be in following format, change for your running kernel:   
   `$ yum install kernel-uek-devel-5.15.0-206.153.7.1.el8uek.x86_64`
    
3. Download Driver from github, this fork has Native Oracle Linux Support  
   `$ git clone --depth 1 -b download https://github.com/b-radsolutions/OL-timecard.git`  
    
4. Install Driver:  
   $ `cd OL-timecard/`  
   $ `./remake`  
   $ `modprobe ptp_ocp`. 

## Outcome
```
$ ls -g /sys/class/timecard/ocp0/
total 0
-r--r--r-- 1 root 4096 Apr  3 18:01 available_sma_inputs
-r--r--r-- 1 root 4096 Apr  3 18:01 available_sma_outputs
-rw-r--r-- 1 root 4096 Apr  3 18:01 clock_source
lrwxrwxrwx 1 root    0 Apr  3 18:01 device -> ../../../0000:e1:00.0
-rw-r--r-- 1 root  144 Apr  3 18:01 disciplining_config
lrwxrwxrwx 1 root    0 Apr  3 18:01 i2c -> ../../ocores-i2c.230400/i2c-4
lrwxrwxrwx 1 root    0 Apr  3 18:01 mro50 -> ../../../../../virtual/misc/mro50.0
drwxr-xr-x 2 root    0 Apr  3 18:01 power
lrwxrwxrwx 1 root    0 Apr  3 18:01 pps -> ../../../../../virtual/pps/pps0
lrwxrwxrwx 1 root    0 Apr  3 18:01 ptp -> ../../ptp/ptp2
-r--r--r-- 1 root 4096 Apr  3 18:01 serialnum
-rw-r--r-- 1 root 4096 Apr  3 18:01 sma1
-rw-r--r-- 1 root 4096 Apr  3 18:01 sma2
-rw-r--r-- 1 root 4096 Apr  3 18:01 sma3
-rw-r--r-- 1 root 4096 Apr  3 18:01 sma4
lrwxrwxrwx 1 root    0 Apr  3 18:01 subsystem -> ../../../../../../class/timecard
-rw-r--r-- 1 root  368 Apr  3 18:01 temperature_table
-rw-r--r-- 1 root 4096 Apr  3 18:01 ts_window_adjust
drwxr-xr-x 2 root    0 Apr  3 18:01 tty
-rw-r--r-- 1 root 4096 Apr  3 15:25 uevent
-rw-r--r-- 1 root 4096 Apr  3 18:01 utc_tai_offset
```

The main resource directory is accessed through the /sys/class/timecard/ocpN directory, which provides links to the various TimeCard resources.  The device links can easily be used in scripts:

```
  tty=$(basename $(readlink /sys/class/timecard/ocp0/ttyGNSS))
  ptp=$(basename $(readlink /sys/class/timecard/ocp0/ptp))

  echo "/dev/$tty"
  echo "/dev/$ptp"
```

After successfully loading the driver, one will see:
* PTP POSIX clock, linking to the physical hardware clock (PHC) on the Time Card (`/dev/ptp4`) 
* GNSS serial `/dev/ttyS5` 
* Atomic clock serial `/dev/ttyS6`
* NMEA Master serial `/dev/ttyS7`
* i2c (`/dev/i2c-*`) device

Now, one can use standard `linuxptp` tools such as `phc2sys` or `ts2phc` to copy, sync, tune, etc... See more in [software](/Software) section

## Driver is included in the mainstream Linux Kernel
* Initial primitive version ([5.2](https://git.kernel.org/pub/scm/linux/kernel/git/netdev/net-next.git/commit/?id=a7e1abad13f3f0366ee625831fecda2b603cdc17))
* Exposing all devices version ([5.15](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=773bda96492153e11d21eb63ac814669b51fc701)) 
