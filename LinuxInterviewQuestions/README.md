# Linux Interview Questions 
## Explain the linux kernel compilation steps 
#### Step 1. Get the latest Linux kernel source code
            : wget https://cdn.kernel.org/pub/linux/kernel/v5.x/linux-5.4.1.tar.xz
                 # Result  -> will get linux-5.4.1.tar.xz
##### Step 2. Extract tar.xz file
           : unxz -v linux-5.4.1.tar.xz
                # Result  -> will get linux-5.4.1.tar
            Step 3. Verify Linux kernel tartball with pgp :
            3.1 : First get  the PGP signature for linux-5.4.1.tar:  
                  wget https://cdn.kernel.org/pub/linux/kernel/v5.x/linux-5.4.1.tar.sign
            3.2 : Try to verify it:   gpg --verify linux-5.4.1.tar.sign
                # Result : 
                    "amit@amit-OptiPlex-7050:~/Linuxkernel$ gpg --verify linux-5.4.1.tar.sign
                    gpg: keybox '/home/amit/.gnupg/pubring.kbx' created
                    gpg: assuming signed data in 'linux-5.4.1.tar'
                    gpg: Signature made 2019年11月29日 18時11分32秒 JST
                    gpg:                using RSA key 647F28654894E3BD457199BE38DBBDC86092693E
                    gpg: Can't check signature: No public key"
##### Step 4.  Get the public key from the PGP keyserver in order to verify the signature 
             i.e. RSA key ID 647F28654894E3BD457199BE38DBBDC86092693E (from the above outputs):
              : gpg --recv-keys 647F28654894E3BD457199BE38DBBDC86092693E
               #  Result : 
                    amit@amit-OptiPlex-7050:~/Linuxkernel$ gpg --recv-keys 647F28654894E3BD457199BE38DBBDC86092693E
                    gpg: key 38DBBDC86092693E: 5 duplicate signatures removed
                    gpg: key 38DBBDC86092693E: 174 signatures not checked due to missing keys
                    gpg: /home/amit/.gnupg/trustdb.gpg: trustdb created
                    gpg: key 38DBBDC86092693E: public key ""Greg Kroah-Hartman <gregkh@linuxfoundation.org>"" imported
                    gpg: no ultimately trusted keys found
                    gpg: Total number processed: 1
                    gpg:               imported: 1"
##### Step 5. Now verify gpg key again with the gpg command: 
             : gpg --verify linux-5.4.1.tar.sign
                # Result : 
                    "amit@amit-OptiPlex-7050:~/Linuxkernel$ gpg --verify linux-5.4.1.tar.sign
                    gpg: assuming signed data in 'linux-5.4.1.tar'
                    gpg: Signature made 2019年11月29日 18時11分32秒 JST
                    gpg:                using RSA key 647F28654894E3BD457199BE38DBBDC86092693E
                    gpg: Good signature from ""Greg Kroah-Hartman <gregkh@linuxfoundation.org>"" [unknown]
                    gpg:                 aka ""Greg Kroah-Hartman <gregkh@kernel.org>"" [unknown]
                    gpg:                 aka ""Greg Kroah-Hartman (Linux kernel stable release signing key) <greg@kroah.com>"" [unknown]
                    gpg: WARNING: This key is not certified with a trusted signature!
                    gpg:          There is no indication that the signature belongs to the owner.
                    Primary key fingerprint: 647F 2865 4894 E3BD 4571  99BE 38DB BDC8 6092 693E"
##### Step 6. If you do not get “BAD signature” output from the “gpg –verify” command,
                then untar/extract the Linux kernel tarball using the tar command, enter:
                    :  tar xvf linux-5.4.1.tar
##### Step 7. Configure the Linux kernel features and modules 
            :  cd linux-5.4.1/
                :cp -v /boot/config-$(uname -r) .config "
##### Step 8. Take a back up earlier config file :
                '/boot/config-5.0.0-23-generic' -> '.config'"
##### Step 9 .  Install the required compilers and other tools
             : $ sudo apt-get install build-essential libncurses-dev bison flex libssl-dev libelf-dev"
##### Step 10 . Configuring the kernel 
             $ make menuconfig – Text based color menus, radiolists & dialogs.
                        This option also useful on remote server if you wanna compile kernel remotely.
                    $ make xconfig – X windows (Qt) based configuration tool, works best under KDE desktop
                    $ make gconfig – X windows (Gtk) based configuration tool, works best under Gnome Dekstop."
##### Step 11.  Compile the Kernel : 
              make   OR  make -j 4
                # Result Generates:
                      vmlinux, the raw uncompressed kernel image, in the ELF format, useful for debugging purposes, but cannot be booted
                      arch/<arch>/boot/*Image, the final, usually compressed, kernel image that can be booted
                      bzImage for x86, zImage for ARM, vmlinux.bin.gz for ARC, etc.
                      arch/<arch>/boot/dts/*.dtb, compiled Device Tree files (on some architectures)
                      All kernel modules, spread over the kernel source tree, as .ko (Kernel Object) files."
##### Step 12.  Install the Linux kernel modules
              $ sudo make modules_install
                # Result 
                    "Does the installation for the host system by default
                    Installs all modules in /lib/modules/<version>/
                     kernel/ Module .ko (Kernel Object) files, in the same directory structure as in the sources.
                    modules.alias, modules.aliases.bin
                    Aliases for module loading utilities. Used to find drivers for devices. Example line:
                    alias usb:v066Bp20F9d*dc*dsc*dp*ic*isc*ip*in* asix"
##### Step 13. Install the Linux kernel
            : sudo make install 
                /boot/vmlinuz-<version> Compressed kernel image. Same as the one in arch/<arch>/boot
                /boot/System.map-<version> Stores kernel symbol addresses for debugging purposes 
                (obsolete: such information is usually stored in the kernel itself)
                /boot/config-<version> Kernel configuration for this version"
##### Step 14 . Reboot 
                  reboot
# -------------------------------------------------------------------------------------------------------------------------------------
## Advanced 
### What is boot loading steps in Linux 
#### 1. Power Button Pressed : 
            BIOS / UEFI is the first software code that is hard-coded on board and runs after we press power button. 
            BIOS runs in real (16 bit) mode of processor, thus it can not address more than 2^20 bytes of RAM i.e. routines can't access more than 1 MiB of RAM, which is a strict limitation and a major inconvenience.
#### 2. Power On Self Test (POST); 
            identify the devices present and to report any problems
#### 3. BIOS / UEFI
#### 4. Necessary hardware initialization (keyboard, disk etc.)
#### 5. Disk (MBR)
     - 1st 512 bytes (1 sector) at the start of 1st valid disk
     - Bootstrap code (446 bytes) + Partition Table (64 bytes)
     - Executable code: Bootloader 1st stage scans partition table and finds 1st sector of active partition (or may point towards stage 1.5)
     - Partition table provides inormation about active/bootable partition (and all others as well)
     - Small size of 64 bytes limits the number of maximum (primary) partitions to 4
     - Since bootloader unable to understand filesystem (inodes etc.) yet, so MBR is itself executable
     - Last 2 bytes are boot signatures i.e. to find immediately if disk/drive is bootable or not and hence switch to the next
#### 6. DOS Compatibility Region code (optional)
#### 7. Bootloader
     - Loaded by stage 2 (or possibly 1 or 1.5) from the filesystem of same partition
     -Loads all necessary filesystem drivers
     - Configuration is read from database e.g. /boot/grub/ on Linux (GRUB) 
    6.1  Linux:
              : GRUB makes use of modules to offer extra functionality for complex boot processes
              : It can show a boot menu to user if needed or configured e.g. for multi-booting or in safe/recovery mode or boot from USB/Network etc.
              : Locates and loads the kernel of desired OS and ramdisk in RAM
              : 'os-prober' helps 'grub-install' and 'grub-update' finding Windows boot partition (System Reserved) by reading bootloader configuration in that partition
              : Kernel
              : 1st MB of kernel from same partition (/boot) loaded in RAM by bootlader in read mode, then switch to protected mode (32-bit) and move 1MB ahead clearing 1st MB
              : Then swith back to real mode and do same with initrd (if it's separate from kernel)
              : Kernel contain ramfs drivers to read rootfs from initrd and mount it
            6.2 Initramfs:
             - Contains minimal filesystem and modules (required drivers which aren't carried by kernel) to access real rootfs (hard driver, NFS etc.)
             - udev or specific scripts load required modules
             - <ramdisk>/init is usually a script which loads necessary drivers and mounts real rootfs
             - finally init switch_root's to real rootfs and executes <real rootfs>/sbin/init; sysV (traditional), upstart (Ubuntu's            initiative) or systemD (the latest widely accepted)


### What it means when it is said "Linux is GNU General

            When you receive or buy a device with Linux on it, you should receive the Linux sources, with the right to study, modify and redistribute them.
    When you produce Linux based devices, you must release the sources to the recipient, with the same rights, with no restriction.
### What is Kernel in Linux 
            The kernel image is a single file, resulting from the linking of all object files that correspond to features enabled in the configuration (.config during "make menuconfig")
            This is the file that gets loaded in memory by the bootloader
            All included features are therefore available as soon as the kernel starts, at a time where no filesystem exists
            
            "It will install three files into /boot directory as well as modification to your kernel grub configuration file:
            initramfs-5.4.1.img   -> the final, usually compressed, kernel image that can be booted
System.map-5.4.
            vmlinuz-5.4.1   -> the raw uncompressed kernel image, in the ELF format, useful for debugging purposes, but cannot be booted"

### Why C Library is not used in Linux kernel 

     The kernel has to be standalone and can’t use user space code.
     #Architectural reason:
       user space is implemented on top of kernel services, not the opposite.
     #Technical reason:
       The kernel is on its own during the boot up phase, before it has   accessed a root filesystem.
       Hence, kernel code has to supply its own library implementations (string utilities, cryptography, uncompression...)
       So, you can’t use standard C library functions in kernel code. (printf(), memset(), malloc(),...).
       Fortunately, the kernel provides similar C functions for your convenience, like printk(), memset(), kmalloc(), ...
### What are device controllers/adapter drivers in device model
     The Device Controller works like an interface between a device and a device driver. 
     I/O units (Keyboard, mouse, printer, etc.) typically consist of a mechanical component and an electronic component where electronic component is called the device controller.
     There is always a device controller and a device driver for each device to communicate with the Operating Systems.
     A device controller may be able to handle multiple devices.
     As an interface its main task is to convert serial bit stream to block of bytes, perform error correction as necessary.
     Any device connected to the computer is connected by a plug and socket, and the socket is connected to a device controller.
     The presence of the controller means that the OS programmer does not need to explicitly program the electrial field of the screen!
     The controller has registers (similar to CPU registers, but for the device) and the OS can write these registers to "give orders" to the device (e.g., "shut down" or "accept data") or read its state
     Ex; PCI devce controller i.e ddbinder 
### Why we need device model in Linux kernel  for driver development 
            The Linux kernel runs on a wide range of architectures and hardware platforms, and therefore needs to maximize the reusability of code between platforms.
            Eg. For example, we want the same USB device driver to be usable on a x86 PC, or an ARM platform, even though the USB controllers used on these platforms are different.
            --> Thus Device Model allows provides  a clean organization of the code, with the device drivers separated from the controller drivers, the hardware description separated from the drivers themselves, etc.

### How device model is organized
     The device model is organized around three main data structures:
       The struct bus_type  -->  which represent one type of bus (USB, PCI, I2C, etc.)
       The struct device_driver -->  which represents one driver capable of handling certain devices on a certain bus.
       The struct device -->  which represents one device connected to a bus
### What are peculiar features of Linux kernel
     1. The Kernel cant use userspace lib or c library because of below reasons :
     2. kernel code is portable ,  i.e All code outside arch/ should be portable 
     3. Never use floating point numbers in kernel code. Your code may need to run on a low-end processor without a floating point unit. Using float in code  will prevent it from being portable 
### What are kernel memory constraints 
     1. No memory protection
     2. The kernel doesn’t try to recover from attemps to access illegal memory locations. 
            It just dumps oops messages on the system console.
     3.  Fixed size stack (8 or 4 KB). Unlike in user space, no mechanism was implemented to make it grow
     4.  Swapping is not implemented for kernel memory either.
### User space drivers are possiable , if yes when this kind of driver is used  . If no what is restriction ?
      it is possible to implement device drivers in user space!
      Can be used in below situation :  
      The kernel provides a mechanism that allows user space applications to directly access the hardware.
      There is no need to leverage an existing kernel subsystem such as the networking stack or filesystems.
      There is no need for the kernel to act as a “multiplexer” for the device: only one application accesses the device.

            Certain classes of devices (printers, scanners, 2D/3D graphics acceleration) are typically handled partly in kernel space, partly in user space.
            Ex: USB with libusb, SPI with spidev,,I2C with i2cdev,Memory-mapped devices with UIO, including interrupt handling, driver-api/uio-howto
### What are the advantages and dis advantages of user space drivers 
   #### Advantages
            No need for kernel coding skills. Easier to reuse code between devices.
            Drivers can be written in any language, even Perl!
            Drivers can be kept proprietary.
            Driver code can be killed and debugged. Cannot crash the kernel.
            Can be swapped out (kernel code cannot be).
            Can use floating-point computation.
            Less in-kernel complexity.
            Potentially higher performance, especially for memory-mapped devices, thanks to the
avoidance of system calls.
   #### Drawbacks
            Less straightforward to handle interrupts.
            Increased interrupt latency vs. kernel code."
### What are modules and why kernel cant access them during boot time 
            Some features (device drivers, filesystems, etc.) can however be compiled as modules
            These are plugins that can be loaded/unloaded dynamically to add/remove features to the kernel
            Each module is stored as a separate file in the filesystem, and therefore access to a filesystem is mandatory to use modules
            This is not possible in the early boot procedure of the kernel, because no filesystem is available.
###  What is the flow of control from app to device in Linux 
       |             User space     |                   Kernel space                                                  |                                                
       App <- ->  C Library   <- -> |  System Call interface  <- ->  Framework  <- ->  Driver  <- ->  Device controller/Adapter <- -> Bus Infrastructure    <- ->  HW
###  What is sysfs 
             The sysfs virtual filesystem offers a mechanism to export such information to user space such as bus, device, drivers, etc. structures are internal to the kernel
            Used for example by udev to provide automatic module loading, firmware loading, mounting of external media, etc.
            sysfs is usually mounted in /sys
            /sys/bus/ contains the list of buses
            /sys/devices/ contains the list of devices
            /sys/class enumerates devices by class (net, input, block...), whatever the bus they are connected to.
            
### What is I2C 
            Inter-Integrated Circuit, known as I2C, is a two-wire data transfer bus:
            A very commonly used low-speed bus to connect on-board and external devices to the processor.
            Uses only two wires: SDA for the data, SCL for the clock.
            It is a master/slave bus: only the master can initiate transactions, and slaves can only reply to transactions initiated by masters.
            In a Linux system, the I2C controller embedded in the processor is typically the master, controlling the bus.
            Each slave device is identified by a unique I2C address. Each transaction initiated by the master contains this address, which allows the relevant slave to recognize that it should reply to this particular transaction.
### Why is the probe method needed in Linux device drivers in addition to init? 
            When a driver module is loaded into a running kernel, it requests resources and offers facilities.
            The driver should probe for its device and its hardware location (I/O ports and IRQ line)—but without registering them.
            eg. PCI and USB devices both have probe() functions.
            In PCI The driver's init function calls pci_register_driver() which gives the kernel a list of devices it is able to service, along with a pointer to the probe() function. 
            The kernel then calls the driver's probe() function once for each device.
            This probe function starts the per-device initialization:
                   initializing hardware, allocating resources, and registering the device with the kernel as a block or network device or whatever it is.
            That makes it easier for device drivers, because they never need to search for devices or worry about finding a device that was hot-plugged. 
            The kernel handles that part and notifies the right driver when it has a device for you to handle.
