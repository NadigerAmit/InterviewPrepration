# Linux Interview Questions 
## Explain the linux kernel compilation steps 
            Step 1. Get the latest Linux kernel source code
            : wget https://cdn.kernel.org/pub/linux/kernel/v5.x/linux-5.4.1.tar.xz
                 # Result  -> will get linux-5.4.1.tar.xz
            Step 2. Extract tar.xz file
           : unxz -v linux-5.4.1.tar.xz
                # Result  -> will get linux-5.4.1.tar
            Step 3. Verify Linux kernel tartball with pgp :
            3.1 : First grab the PGP signature for linux-5.4.1.tar:  wget https://cdn.kernel.org/pub/linux/kernel/v5.x/linux-5.4.1.tar.sign
            3.2 : Try to verify it:   gpg --verify linux-5.4.1.tar.sign
                # Result : 
                    "amit@amit-OptiPlex-7050:~/Linuxkernel$ gpg --verify linux-5.4.1.tar.sign
                    gpg: keybox '/home/amit/.gnupg/pubring.kbx' created
                    gpg: assuming signed data in 'linux-5.4.1.tar'
                    gpg: Signature made 2019年11月29日 18時11分32秒 JST
                    gpg:                using RSA key 647F28654894E3BD457199BE38DBBDC86092693E
                    gpg: Can't check signature: No public key"
            Step 4.  Get the public key from the PGP keyserver in order to verify the signature i.e. RSA key ID 647F28654894E3BD457199BE38DBBDC86092693E (from the above outputs):
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
           Step 5. Now verify gpg key again with the gpg command: 
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
           Step 6. If you do not get “BAD signature” output from the “gpg –verify” command,
                then untar/extract the Linux kernel tarball using the tar command, enter:
                    :  tar xvf linux-5.4.1.tar
          Step 7. Configure the Linux kernel features and modules 
                :  cd linux-5.4.1/
                :cp -v /boot/config-$(uname -r) .config "
          Step 8. Take a back up earlier config file :
                '/boot/config-5.0.0-23-generic' -> '.config'"
          Step 9 .  Install the required compilers and other tools
                : $ sudo apt-get install build-essential libncurses-dev bison flex libssl-dev libelf-dev"
          Step 10 . Configuring the kernel 
                    $ make menuconfig – Text based color menus, radiolists & dialogs.
                        This option also useful on remote server if you wanna compile kernel remotely.
                    $ make xconfig – X windows (Qt) based configuration tool, works best under KDE desktop
                    $ make gconfig – X windows (Gtk) based configuration tool, works best under Gnome Dekstop."
          Step 11.  Compile the Kernel : make   OR  make -j 4
                # Result Generates:
                      vmlinux, the raw uncompressed kernel image, in the ELF format, useful for debugging purposes, but cannot be booted
                      arch/<arch>/boot/*Image, the final, usually compressed, kernel image that can be booted
                      bzImage for x86, zImage for ARM, vmlinux.bin.gz for ARC, etc.
                      arch/<arch>/boot/dts/*.dtb, compiled Device Tree files (on some architectures)
                      All kernel modules, spread over the kernel source tree, as .ko (Kernel Object) files."
          Step 12.  Install the Linux kernel modules
                 $ sudo make modules_install
                # Result 
                    "Does the installation for the host system by default
                    Installs all modules in /lib/modules/<version>/
                     kernel/ Module .ko (Kernel Object) files, in the same directory structure as in the sources.
                    modules.alias, modules.aliases.bin
                    Aliases for module loading utilities. Used to find drivers for devices. Example line:
                    alias usb:v066Bp20F9d*dc*dsc*dp*ic*isc*ip*in* asix"
         Step 13. Install the Linux kernel
            : sudo make install 
                /boot/vmlinuz-<version> Compressed kernel image. Same as the one in arch/<arch>/boot
                /boot/System.map-<version> Stores kernel symbol addresses for debugging purposes 
                (obsolete: such information is usually stored in the kernel itself)
                /boot/config-<version> Kernel configuration for this version"
        Step 14 . Reboot 
                  reboot

## Advanced 
### 1 What it means when it is said "Linux is GNU General

    When you receive or buy a device with Linux on it, you should receive the Linux sources, with the right to study, modify and redistribute them.
    When you produce Linux based devices, you must release the sources to the recipient, with the same rights, with no restriction.
### 2 Why C Library is not used in Linux kernel 

     The kernel has to be standalone and can’t use user space code.
     #Architectural reason:
       user space is implemented on top of kernel services, not the opposite.
     #Technical reason:
       The kernel is on its own during the boot up phase, before it has   accessed a root filesystem.
       Hence, kernel code has to supply its own library implementations (string utilities, cryptography, uncompression...)
       So, you can’t use standard C library functions in kernel code. (printf(), memset(), malloc(),...).
       Fortunately, the kernel provides similar C functions for your convenience, like printk(), memset(), kmalloc(), ...
### 3 What are device controllers/adapter drivers in device model
     The Device Controller works like an interface between a device and a device driver. 
     I/O units (Keyboard, mouse, printer, etc.) typically consist of a mechanical component and an electronic component where electronic component is called the device controller.
     There is always a device controller and a device driver for each device to communicate with the Operating Systems.
     A device controller may be able to handle multiple devices.
     As an interface its main task is to convert serial bit stream to block of bytes, perform error correction as necessary.
     Any device connected to the computer is connected by a plug and socket, and the socket is connected to a device controller.
     The presence of the controller means that the OS programmer does not need to explicitly program the electrial field of the screen!
     The controller has registers (similar to CPU registers, but for the device) and the OS can write these registers to "give orders" to the device (e.g., "shut down" or "accept data") or read its state
     Ex; PCI devce controller i.e ddbinder 
### 4 How device model is organized
     The device model is organized around three main data structures:
       The struct bus_type  -->  which represent one type of bus (USB, PCI, I2C, etc.)
       The struct device_driver -->  which represents one driver capable of handling certain devices on a certain bus.
       The struct device -->  which represents one device connected to a bus
### 5    
