# Filesystem layout

A brief overview follows of the filesystem as it exists at
runtime. This does not discuss the various partitions and slots used
to boot the robot.

## system partition

The system partition contains all the code and configuration to run
the underlying variant of the Android operating system and the
Userspace Anki code that powers Vector.

When you perform an upgrade of the Operating System the system
partition is completely overwritten and all changes are lost.

By default it is read-only and expected to be unmodifiable.

## /anki directory

This is not strictly a partition but it is where all of the Vector
specific code is located. Various services, utilities, configuration
settings that are not relevant to the Operating System but to Vector
are located here. If you're trying to track down something that you
wouldn't expect to find on a normal Linux operating system it is
probably located here.

Inside you will see some familiar folders such as `/anki/etc/`
`/anki/bin` and others that follow the general configuration of a
Linux operating system as well as a `/data/` folder which holds
various thing that don't quite fit in with the normal file layout.

## /data partition

The `/data` partition is the only partition that is mounted read-write
on a production Vector. All user settings are saved here. When an
system upgrade is installed the data partition remains unmodified and
intact and all old data is available for use by the new version of the
Operating System.

This is where you will want to put any files, scripts, executables,
etc that you want to survive a system upgrade.

## Editing the system partition

Although the system partition is initially mounted read only it is
trivial to mount it in read-write mode since you are the root user. To
do so just run:

    mount -o remount,rw /

And you will now be able to edit the filesystem.
