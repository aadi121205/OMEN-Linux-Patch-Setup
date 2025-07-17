# OMEN-Linux-Patch-Setup

'''

This is ABSOLUTELY the way! I've been looking for the missing piece of the puzzle, and this post very succinctly pieced it all together for me.

 

This is assuming of course that you have Grub2 running properly. If you're attempting this in NixOS like I did, you will need to install with either "noapic" or "pci=nobar", reboot with the same kernel parameters, then switch from systemd-boot to grub2 (search the web for "NixOS bootloader" to see their instruction page on how to do this). Then all you need to do if you have this problem is to follow the steps at the Arch Linux DSDT page, tweak your DSDT table, recompile, and save to your /boot directory. Be sure to add "acpi /boot/dsdt.aml" to the line ABOVE the linux kernel line or it won't find it. 

 

Summary of the steps:

1. Install linux using "noapic" or "pci=nobar" as kernel parameters. 

2. Boot linux with either "noapic" or "pci=nobar" (be sure to have keyboard and mouse handy)

3. Update to latest kernels

4. install coreboot-tools.x64 (or at least that's what it's called in nixland)

5. Dump your DSDT file with 

cat /sys/firmware/acpi/tables/DSDT > dsdt.dat
6. Decompile with "iasl -d dsdt.dat"

7. Edit your file with the changes listed at https://github.com/j0hnwang/OMEN-Transcend-16-ACPI-fix/commit/2e4feda9529c09133f5f7e9623ec11226db581...

8. Recompile the file with "iasl -tc dsdt.dsl"

9. Copy the resulting dsdt.aml file to /boot as sudo.

10. Reboot

11. Modify grub entry on the fly with "acpi /boot/dsdt.aml" above the "linux" line.

12. Profit.

13. Use John's patch to get your speakers working (haven't tried yet, but that's next).

 

As a final note, I would actually avoid using the already compiled aml file over at https://github.com/j0hnwang/OMEN-Transcend-16-ACPI-fix for a couple of reasons. First, that aml is compiled for his laptop, and your DSDT table will be different. Using his aml file caused my grub to hang because it could no longer find my hard drives. Capture your DSDT table from /sys/firmware/DSDT like the Arch page says, edit it, and recompile. 

'''
