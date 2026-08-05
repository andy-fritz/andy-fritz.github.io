---
layout: post
title:  "Fixing GRUB & Login Screen Resolution on Ubuntu"
date:   2026-08-03 12:58:37 +0100
categories: linux how-to
---

A few months back, I set up Ubuntu 24.04 on my PC and immediately noticed an annoying issue: while my monitor supports 5120x1440, both the boot menu and login screen were stuck at much lower resolutions—640x480 for GRUB and 1920x1080 for GDM3. This post covers how to get both displaying at full native resolution.

## Fixing the Login Screen

The easier of the two fixes. GNOME Display Manager (GDM3) should respect your monitor configuration if you copy it to the right location.

Start by copying your monitor configuration:

```bash
# For Ubuntu 25.10 and newer
sudo cp ~/.config/monitors.xml /var/lib/gdm3/seat0/config/

# For Ubuntu older than 25.10
sudo cp ~/.config/monitors.xml /var/lib/gdm3/.config/
```

Next, verify and correct the file ownership. Check which user owns the other config files in that directory:

```bash
# For Ubuntu 25.10 and newer
ls -l /var/lib/gdm3/seat0/config/

# For Ubuntu older than 25.10
ls -l /var/lib/gdm3/.config/
```

You should see output like this:

```bash
total 24
drwx------ 2 60578 nogroup 4096 Aug  5 09:28 dconf
drwx------ 3 60578 nogroup 4096 Jan  1  2026 ibus
-rw-r--r-- 1 60578 nogroup  620 Aug  5 09:56 monitors.xml
drwx------ 2 60578 nogroup 4096 Jan  1  2026 pulse
-rw------- 1 60578 nogroup  574 Jan  1  2026 user-dirs.dirs
-rw-r--r-- 1 60578 nogroup    5 Jan  1  2026 user-dirs.locale
```

Update the ownership of `monitors.xml` to match (in this example, `60578:nogroup`):

```bash
sudo chown 60578:nogroup /var/lib/gdm3/.config/monitors.xml
# or for 25.10+:
sudo chown 60578:nogroup /var/lib/gdm3/seat0/config/monitors.xml
```

> **Note**: If the `monitors.xml` file is missing, you can regenerate it by changing your monitor settings in Settings > Displays, then try copying it again.

## Configuring GRUB for Native Resolution

GRUB requires manual configuration. Make a backup first, then edit `/etc/default/grub`:

```bash
sudo cp /etc/default/grub /etc/default/grub.backup
sudo nano /etc/default/grub
```

Find or add these lines:

```bash
# Graphics settings
GRUB_GFXMODE=5120x1440
GRUB_GFXPAYLOAD=keep
GRUB_TERMINAL=gfxterm
```

Here's what each setting does:

**GRUB_GFXMODE** — Sets the resolution for the GRUB menu. GRUB relies on VBE/GOP/UGA modes supported by your graphics card; not every resolution will work. If your chosen resolution doesn't boot, GRUB will fall back to a lower mode automatically.

**GRUB_GFXPAYLOAD** — Instructs the kernel to maintain the graphics mode set by GRUB before the native graphics driver loads. Setting it to `keep` ensures consistency from boot menu through to the desktop.

**GRUB_TERMINAL** — Switches the bootloader to graphical output mode instead of text-only.

After saving your changes, regenerate the GRUB configuration:

```bash
sudo update-grub
```

Reboot and you should see both GRUB and your login screen at full resolution.

### Finding Your Supported Resolutions

If 5120x1440 doesn't work, you'll need to find what your graphics card supports. To see available modes, boot into GRUB, press `c` to enter command mode, and type:

```bash
videoinfo
```

This will list all VBE/GOP modes your card supports.

> **Note**: The `videoinfo` command only works with Secure Boot disabled. If you can't access GRUB's command mode, check your firmware settings and disable Secure Boot temporarily.
