# Fixing CFG Lock
#### WARNING: Before attempting this, make sure that you can install <a href="https://dl.dell.com/FOLDER06655259M/1/Inspiron_5570_5770_131.exe">this</a> BIOS on your PC.

## What exactly is CFG Lock?

CFG Lock is a setting in your BIOS that allows for a specific register (in this case the MSR 0xE2) to be written to. By default, most motherboards lock this variable with many even hiding the option outright in the GUI. And why we care about it is that macOS actually wants to write to this variable, and not just one part of macOS. Instead both the **Kernel(XNU) and AppleIntelPowerManagement** want this register.

One way of fixing it is by enabling the `AppleCpuPmCfgLock` and `AppleXcpuPmCfgLock` quirks in OpenCore's config.plist. However, this approach is only meant to be temporary and can be buggy--crashes and kernic panics will happen if you do this for a long period of time.

Another way is disabling CFG Lock in the system's BIOS. However, the 5570 doesn't have an option to do so, as stated above. To fix this, we can use `modGRUBShell.efi` (can be found in my Inspiron-5570-KBR repo) to unlock it.

## 1. Getting ready

Download modGRUBShell.efi and place it in your EFI/OC/Tools folder, then add an entry into your config.plist. It's best if you **make sure it's not auxiliary**, however as long as HideAuxiliary is disabled, you'll be alright. 

Update your BIOS to the version posted there. It's the latest one. If it doesn't work, **DO NOT DO IT.** You can brick your system with an incorrect BIOS.

Do a quick reboot and see if the shell EFI shows up in the picker. If it doesn't check your config.plist again.

## 2. Disabling CFG Lock on your 5570

I've already gone through the torture of trying to disable it and know what needs to be changed, so now we can just dive in.

Reboot to the `modGRUBShell.efi` file and wait until you see the `grub>` line on the screen.

Type `setup_var 0x4ED` to see what returns. It may say something about an error, but that's alright, it doesn't affect anything in the userspace. If it says 0x01, your register is locked. Now, we can continue. **If it says anything else, abort the mission immediately as changing it might not be okay for your BIOS!**

Next, type `setup_var 0x4ED 0x00` to disable CFG lock. 

Reboot the computer, diable the two quirks I mentioned before, and you're all set to continue!
