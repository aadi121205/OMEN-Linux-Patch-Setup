# OMEN Linux ACPI / DSDT Patch Setup

This guide documents the **working and reliable method** to fix boot and hardware issues (notably ACPI-related) on HP OMEN laptops under Linux using a custom DSDT override.

⚠️ **Prerequisite:**
This assumes **GRUB2 is installed and functioning correctly**.

If you are using **NixOS**, you may need to:

* Install Linux initially using `noapic` or `pci=nobar`
* Boot once with the same kernel parameters
* Switch from `systemd-boot` to **GRUB2**
  (Search for *“NixOS bootloader”* in the official documentation for exact steps.)

Once GRUB is active, follow the steps below.

---

## Overview

The solution involves:

* Dumping your system’s **native DSDT**
* Applying known fixes
* Recompiling the table
* Loading it via GRUB using `acpi /boot/dsdt.aml`

⚠️ **Important:**
The `acpi /boot/dsdt.aml` line **must be placed above the `linux` line** in the GRUB entry, or the override will not be detected.

---

## Step-by-Step Instructions

### 1. Install Linux

Install Linux using **one** of the following kernel parameters:

* `noapic`
* `pci=nobar`

---

### 2. Boot Linux

Boot the installed system using the **same kernel parameter** you used during installation.
(Keep an external keyboard/mouse handy if input devices are unstable.)

---

### 3. Update the System

Update to the **latest kernel** available for your distribution.

---

### 4. Install ACPI Tools

Install the required ACPI/DSDT tools.

On NixOS:

```bash
coreboot-tools.x64
```

On most other distros:

```bash
iasl (ACPICA tools)
```

---

### 5. Dump Your DSDT

Extract your system’s current DSDT:

```bash
cat /sys/firmware/acpi/tables/DSDT > dsdt.dat
```

---

### 6. Decompile the DSDT

```bash
iasl -d dsdt.dat
```

This will generate `dsdt.dsl`.

---

### 7. Apply Required Fixes

Edit `dsdt.dsl` and apply the changes from the following commit:

🔗
[https://github.com/j0hnwang/OMEN-Transcend-16-ACPI-fix/commit/2e4feda9529c09133f5f7e9623ec11226db581](https://github.com/j0hnwang/OMEN-Transcend-16-ACPI-fix/commit/2e4feda9529c09133f5f7e9623ec11226db581)

Use a text editor such as:

```bash
nano dsdt.dsl
# or
vim dsdt.dsl
```

---

### 8. Recompile the DSDT

```bash
iasl -tc dsdt.dsl
```

If successful, this will produce:

```
dsdt.aml
```

---

### 9. Copy the AML File to /boot

```bash
sudo cp dsdt.aml /boot/
```

---

### 10. Reboot

Reboot your system normally.

---

### 11. Modify GRUB Entry (Temporary Test)

At the GRUB menu:

1. Highlight your Linux entry
2. Press **`e`** to edit
3. Add the following line **above** the `linux` line:

   ```
   acpi /boot/dsdt.aml
   ```
4. Boot with **Ctrl + X** or **F10**

---

### 12. Verify Boot

If the system boots normally **without `noapic` / `pci=nobar`**, the patch is working.

🎉 **Success**

---

### 13. Optional: Speaker Fix

You can optionally apply John’s additional patch for speaker functionality (not tested here yet).

---

## ⚠️ Important Warning About Precompiled AML Files

Avoid using the precompiled `dsdt.aml` provided in:
[https://github.com/j0hnwang/OMEN-Transcend-16-ACPI-fix](https://github.com/j0hnwang/OMEN-Transcend-16-ACPI-fix)

**Reasons:**

* That AML is compiled specifically for **one laptop**
* Your ACPI tables **will differ**
* Using it caused GRUB to hang and prevented disk detection

✅ **Always dump, edit, and compile your own DSDT** from:

```bash
/sys/firmware/acpi/tables/DSDT
```

This ensures compatibility and avoids serious boot issues.

