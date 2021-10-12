# Appendix 3: Living with Windows

Vector runs on a variant of the Android Operating System which in turn
is based on Linux. Because of this the entire toolchain assumes that
the developer is also running a Unix based system. Most Vector
Developers use Macs which run Unix under the hood, and our build
systems run Ubuntu 16.04. Because of this all the documentation
assumes that you are on a Unix system. This can cause problems for
people using Windows-based computers. Here are some tips to help out.

## PuTTY - A good SSH shell for Windows

If you're only interested in connecting to Vector and working from
there you can use
[PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)
to easily connect to Vector and then work from there. It is widely
used and there is plenty of documentation available on the web.

One complication is that PuTTY uses a different key format than the
one provided by Vector when you download its SSH key. To convert the
SSH key in the robot logs you downloaded to the format used by PuTTY:

1. Install PuTTY from the link above. Open `PuTTYgen`.

2. Click **Conversions -> Import** and select id_rsa file from the Vector
   logs inside the `/data/ssh` folder
   `id_rsa_Victor-X1Y1`

3. Go to **File -> Save private key** to save the PuTTY version of the
   key.

## Full Linux Environment

As we release the OSKR source code you will need a full Linux
environment to compile the code. There are a few options here.

### WSL - Windows Subsystem for Linux

If you simply want a command line interface and can work from that WSL is
a tool provided by Microsoft that allows you to run Ubuntu
natively. This is the best way to run things if you want to use your
normal Windows environment as much as possible.

Instructions for installing are located at
<https://docs.microsoft.com/en-us/windows/wsl/install-win10>
We currently recommend using **Ubuntu 20.04 LTS** as the Linux
distribution to install.

### VirtualBox - Virtual Linux

[VirtualBox](https://www.virtualbox.org/) is a free program that
allows you to run entire operating systems within your existing
operating system. If you want a full version of Linux with its own
desktop and tools you will want to install VirtualBox and install
**Ubuntu 20.04 LTS** . Refer to the [VirtualBox User
Manual](https://www.virtualbox.org/manual/) for more details.
