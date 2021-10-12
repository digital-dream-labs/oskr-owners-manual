# Cross-compiling Binaries for Vector

To cross compile binaries for Vector, we need a compiler that can produce arm
binaries and matches Vector's libc version and expected application binary
interface (abi).

Vector uses the Qualcomm APQ8009, which is a 32bit ARMv7l rev 5 processor.

A suitable cross compiler is available from Linaro, [version 6.3.1](https://releases.linaro.org/components/toolchain/binaries/6.3-2017.05/)
produces binaries that are compatible with Vector.

If you will be using an installation of Linux, either natively or in a virtual
machine download the [x86_64 (recommended)](https://releases.linaro.org/components/toolchain/binaries/6.3-2017.05/arm-linux-gnueabi/gcc-linaro-6.3.1-2017.05-x86_64_arm-linux-gnueabi.tar.xz
)
or [i686](https://releases.linaro.org/components/toolchain/binaries/6.3-2017.05/arm-linux-gnueabi/gcc-linaro-6.3.1-2017.05-i686_arm-linux-gnueabi.tar.xz) release.

If you will be using a Windows machine, download the [i686-mingw](https://releases.linaro.org/components/toolchain/binaries/6.3-2017.05/arm-linux-gnueabi/gcc-linaro-6.3.1-2017.05-i686-mingw32_arm-linux-gnueabi.tar.xz)
release.  This toolchain is tested and works on Windows, though the commands
below will need tweaking unless run under WSL.

## Build Environment

The instructions below assume an Ubuntu Linux installation.  The same steps
should work for any Linux distribution, minor modifications will be needed
for Windows.

1. Extract the cross-compiler somewhere convenient:

    ```
    tar xJf arm-linux-gnueabi/gcc-linaro-6.3.1-2017.05-x86_64_arm-linux-gnueabi.tar.xz
    ```

## Building for Vector

1. Augment your path to include the `bin/` directory for the cross-compiler:

    ```
    export PATH="${PATH}:/path/to/gcc-linaro-6.3.1-2017.05-x86_64_arm-linux-gnueabi/bin"
    ```

    This can be done each time you want to compile for Vector or added to your
    environment files.

2. Create a simple hello world program:

    ```
    cat > hello.c << _DONE_
    #include <stdio.h>

    void main() {
        printf("hello, vector!\r\n");
    }
    _DONE_
    ```

3. Compile hello world for Vector:

    ```
    arm-linux-gnueabi-gcc -o hello hello.c
    ```

4. Check the resulting build's architecture details with `file hello`.  The
    expected output from this command should match:

    ```
    hello: ELF 32-bit LSB executable, ARM, EABI5 version 1 (SYSV), dynamically linked, interpreter /lib/ld-linux.so.3, for GNU/Linux 2.6.32, BuildID[sha1]=<...>, with debug_info, not stripped
    ```

## Running on Vector

It's important to note that Vector's filesystems have a bit of extra protection on
them.  Partitions which were not expected to contain executable code are mounted
with the `noexec` option set.  This will result in binaries failing to execute
with `Permission denied` on such partitions.

To remount a partition, such as `/data`, as executable for testing you can run:

```
mount -o remount,exec /data
```

Note that this will only affect Vector until a reboot.  If you will be writing
programs you wish to be able to always run, you'll want to place your programs
in the root partition somewhere or modify `/etc/initscripts/mount-data` to
mount `/data` without the `noexec` option.

1. Copying the binary over to Vector:

    ```
    scp -i ~/.ssh/id_rsa_Vector-AAAA hello root@1.2.3.4:/data/
    ```

    Replacing `AAAA` and `1.2.3.4` with your Vector's identifier and ip address.

2. ssh into your Vector and remount `/data` as executable:

    ```
    mount -o remount,exec /data
    ```

3. Run the application!

    ```
    # /data/hello
    hello, vector!
    ```

