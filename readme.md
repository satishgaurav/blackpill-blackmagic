


Here are the following steps to flash the blackpill board into blackmagic probe:

1. clone the blackmagic repo: git clone https://github.com/blackmagic-debug/blackmagic

2. do the following steps 

```
cd blackmagic
make
```


Before starting the programming, we have to unlock to memory: 

```
openocd -f interface/stlink-v2.cfg -f target/stm32f4x.cfg \
        -c "init; reset halt; stm32f4x unlock 0; reset halt; exit"
```

Now erase everything: 

```
st-flash erase
```

Just running make will create an universal build which which generate all the files: 

at the end of the build you will see build ends with the following patterns: 

  CC      platforms/common/swdptap.c
  CC      platforms/common/usb.c
  CC      platforms/common/usb_serial.c
  CC      platforms/common/usb_dfu_stub.c
  CC      platforms/common/aux_serial.c
  LD      blackmagic.elf
Memory region         Used Size  Region Size  %age Used
             rom:      127704 B       128 KB     97.43%
             ram:        3440 B        20 KB     16.80%
  OBJCOPY blackmagic.bin
  CC      platforms/native/usbdfu.c
  CC      platforms/common/stm32/dfucore.c
  CC      platforms/common/stm32/dfu_f1.c
  LD      blackmagic_dfu.elf
  OBJCOPY blackmagic_dfu.bin
  OBJCOPY blackmagic_dfu.hex



Navigate to the src/ directory where 
running ls should list the following files: 
blackmagic.bin     
blackmagic.elf     
blackmagic_dfu.bin 
blackmagic_dfu.elf 


Building target without uploading the dfu will result in error: 
First, we have to flash the blackmagic_dfu.bin file into the chip: 

st-link utility can be found at : https://github.com/stlink-org/stlink
macOS support will be removed in the future

```
st-flash write blackmagic_dfu.bin 0x8000000
```

Once the blackmagic -dfu is uploaded, go back to the folder blackmagic/ 


now run
```
make clean
make PROBE_HOST=blackpill-f411ce 
```


Alternatively, you follow this link: https://github.com/blackmagic-debug/blackmagic/tree/main/src/platforms/common/blackpill-f4



this time, you will only see the build ending with files like this: 

```
  CC      platforms/common/usb_serial.c
  CC      platforms/common/usb_dfu_stub.c
  CC      platforms/common/aux_serial.c
  LD      blackmagic.elf
Memory region         Used Size  Region Size  %age Used
             rom:      131976 B       512 KB     25.17%
             ram:        5928 B       128 KB      4.52%
  OBJCOPY blackmagic.bin
```

Now, go to src/ directory and run 

```
st-flash --reset write blackmagic.bin 0x8000000
```

Now, disconnect the blackpill board. Connect with avialable usb-c/usb-micro connector. 

now run

```
dfu-util -l 
```

if everything goes well then you will see something like this: 

```
dfu-util 0.11

Copyright 2005-2009 Weston Schmidt, Harald Welte and OpenMoko Inc.
Copyright 2010-2021 Tormod Volden and Stefan Schmidt
This program is Free Software and has ABSOLUTELY NO WARRANTY
Please report bugs to http://sourceforge.net/p/dfu-util/tickets/

Found Runtime: [1d50:6018] ver=0100, devnum=1, cfg=1, intf=4, path="0-1", alt=0, name="Black Magic DFU", serial="3179357F3335"

```

Alternatively, if you have PlatformIO CLI installed then run

```
pio device list 
```

you will see the following

```
/dev/cu.wlan-debug
------------------
Hardware ID: n/a
Description: n/a

/dev/cu.JBLTUNE510BT
--------------------
Hardware ID: n/a
Description: n/a

/dev/cu.Bluetooth-Incoming-Port
-------------------------------
Hardware ID: n/a
Description: n/a

/dev/cu.usbmodem3179357F33351
-----------------------------
Hardware ID: USB VID:PID=1D50:6018 SER=3179357F3335 LOCATION=0-1
Description: Black Magic Probe (BlackPill-F411CE) v1.9.0-705-g6c395e51

/dev/cu.usbmodem3179357F33353
-----------------------------
Hardware ID: USB VID:PID=1D50:6018 SER=3179357F3335 LOCATION=0-1
Description: Black Magic Probe (BlackPill-F411CE) v1.9.0-705-g6c395e51
```



Best ref: 
https://ciesie.com/post/black_magic_probe_stlink/

board pin out ref: 
https://github.com/WeActStudio/WeActStudio.MiniSTM32F4x1











