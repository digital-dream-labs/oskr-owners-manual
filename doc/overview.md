# Unlocking your Robot

The first step in your new adventure is unlocking your robot and
transforming it from a normal Vector to and OSKR enabled Vector where
you will be able to directly access all internals.

## What is Unlocking? Why do we do it?

By default the operating system of Vector is secure and unchangeable
with digital signatures to ensure system integrity. The security starts
with encryption keys baked in to the hardware itself and works its way
down to the final operating system image. In each phase of the boot
process the previous system verifies the next system loads. If even
one bit, byte, letter, etc change in any of the phases then the system
will stop loading and come to a halt. This is done for:

* **Security** We want to make sure a malicious user hasn't modified
    the code to do something that it shouldn't be doing, like sending spam
    emails. Even if a hacker or bot network is somehow able to access
    the Vector they won't be able to make any changes to turn it in to
    the wrong kind of bot.

* **Reliability** We don't want mistakes to break the device. There
    are currently hundreds of thousands of Vectors out there, and we're
    hoping for millions and billions. At that level even small
    problems can add up quickly. Ensuring the Operating System hasn't
    been changed ensures reliability.

At boot time the following verification happens.

1. The hardware chip itself verifies the cryptographic signature on
    the low level ABOOT partition. It is for all practical purposes
    impossible to override this check.

2. The ABOOT partition has another security key baked in that it uses
    to verify the BOOT partition and load it. The BOOT partition is a
    skeleton linux operating system to bootstrap the main system.

3. The BOOT partition loads the appropriate SYSTEM partition which is
    the final software to run.

    In the case of a normal Vector the
    SYSTEM partition is protected by an Android-specific system called
	`dm-verity` to ensure that this partition isn't modified.

    Development Vectors used internally by the company, and now OSKR
    Vectors do not enforce `dm-verity` security on the system
    partition allowing development of new features.

In addition to these layers of cryptographic security a new Vector
comes with a **recovery filesystem** installed for reliability
purposes. This has been loaded on at the factory and is the boot
system of last resort when no other software will load.

We then set aside two slots for day-to-day software usage called **A**
and **B**. When you install an OTA update the system:

1. Downloads the update.

2. Performs some basic verification to see if the image 'fits' with
    the current configuration.

3. Decides if the A or B slot is the available one.

4. Copies the new filesystems on to the slot.

5. Flags the new slot as the 'good' one.

6. Reboots and loads the software. If the software fails to load it
    marks the slot as invalid.

    If there is a previously valid slot, such as **A** after an update
    to **B** was installed, it reverts to that.

    If there is now good **A** or **B** slot it reverts back to the
    **recovery filesystem** so you can try to install again. This is
    important. No matter how bad the running system gets messed up we
    should **always** be able to fall back to the **recovery filesystem**
	
So what does all this mean? We've got two conflicting goals:

1. Normal Vectors should have all the security and reliability that
    are expected for a consumer electronic device and need to remain
    locked down.

2. OSKR Vectors should have some ability to lock down the software
    installed but some ability to modify software for development.

Because of this each type of robot need to enforce different
requirements each needs a different set of cryptographic signatures so
that a normal Vector behaves the way it should and a OSKR unit behaves
the way it should.

To accomplish this and unlock a OSKR robot we need to:

1. Reprogram the ABOOT image with a new cryptographic signature.

2. Sign the new BOOT images with this signature.

3. Create new **recovery filesystems** signed by the new keys because
    the old ones will be considered 'bad' when we swap out the signatures.

This is where things get tricky. The normal assumption is that the
ABOOT and recovery filesystems get installed directly at the factory
and physically written directly to the chips before final assembly of
the robot, and never touched again.

But to do this outside of the factory we need to do this with our OTA
(Over The Air) update scripts. And we need to modify software
partitions that were never expected to be written in an environment
much less reliable than a factory with physical connections to the
hardware. So we must be very careful to make sure that we generate
correct images, and the installation process completes without
interruption.

Enough of the theory- let's dive in to the process!

