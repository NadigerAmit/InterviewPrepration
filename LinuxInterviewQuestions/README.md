# Linux Interview Questions 
## Advanced 
### 1 What it means when it is said "Linux is GNU General

    When you receive or buy a device with Linux on it, you should receive the Linux sources, with the right to study, modify and redistribute them.
    When you produce Linux based devices, you must release the sources to the recipient, with the same rights, with no restriction.
### 2 Why C Library is not used in Linux kernal 

     The kernel has to be standalone and can’t use user space code.
     Architectural reason:
     user space is implemented on top of kernel services, not the opposite.
     #### Technical reason:
      ▶ The kernel is on its own during the boot up phase, before it has   accessed a root filesystem.
      ▶ Hence, kernel code has to supply its own library implementations (string utilities, cryptography, uncompression...)
      ▶ So, you can’t use standard C library functions in kernel code. (printf(), memset(), malloc(),...).
      ▶ Fortunately, the kernel provides similar C functions for your convenience, like printk(), memset(), kmalloc(), ...
