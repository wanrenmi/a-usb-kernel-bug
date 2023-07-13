# a-usb-kernel-bug
The usb_giveback_urb function in the linux kernel has a logic loophole in its implementation. Due to the inappropriate judgment condition of the goto statement, the function cannot return under the input of a specific malformed descriptor file, and it falls into an endless loop and occupies CPU resources, resulting in a denial of service attack.
The input used as the USB descriptor is as follows:
![image](https://github.com/wanrenmi/a-usb-kernel-bug/assets/42407501/428df7af-df9f-4eca-8111-5e174d7d13a6)
Among them, the first 18 bytes are the device descriptor, and the latter is the config descriptor.
Insert the above file as a real or simulated USB device into a host using the linux kernel (It is currently certain that kernel version <=6.3.7 will be affected by this vulnerability, the latest kernel version has not been tested, but because there is no such part of code update, so there is a high probability that this vulnerability also exists).
The result of the vulnerability triggering situation is shown in the figure below:
![N2(@@_%60N~L_F88~%_JDCR](https://github.com/wanrenmi/a-usb-kernel-bug/assets/42407501/2c3d25d0-75d4-429d-a665-b564986913ec)
![(K%8S82PV6DX}_9U4C8)PZ7_tmb](https://github.com/wanrenmi/a-usb-kernel-bug/assets/42407501/dcd4b177-356b-4b7a-a152-d28753eee17d)
It can be seen from the figure that after inserting, it falls into a loop, repeatedly calls imon to print logs, and takes up a lot of CPU resources, and this loop is an infinite endless loop, which will cause the kernel thread to be locked.
