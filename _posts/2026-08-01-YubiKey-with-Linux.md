---
layout: post
title:  "YubiKey with Linux"
date:   2026-08-01 12:58:37 +0100
categories: linux how-to
thumbnail: /assets/thumbnails/linux.png
---

The **YubiKey** is one of the most secure method to allow access to your Linux system. Instead of only entering a password, you can use the **YubiKey** as a **second factor** or as the only possible login method.

I will describe how to configure the **YubiKey** as a **second factor** login method.

## Prerequisites

Before starting, you need to fulfill the following prerequisites:

- Linux (Ubuntu, Mint, Debian, etc)
- A **YubiKey** (YubiKey 5, YubiKey 5C, etc)
- Admin privileges

## YubiKey Software Installation

Open a terminal and install the following packages

```bash
sudo apt update && sudo apt install libpam-yubico libpam-u2f yubikey-personalization
```

This will install the following:

- **libpam-yubico**: used for Yubico OTP
- **libpam-u2f**: universal 2nd factor
- **yubikey-personalization**: configuration of the yubikey

## Associate your YubiKey(s) with your account

- Insert your **YubiKey** and open a new terminal:

  ```bash
  mkdir -p ~/.config/Yubico
  pamu2fcfg > ~/.config/Yubico/u2f_keys
  ```

  > **Note:** You may be prompted for a PIN when running pamu2fcfg. If you are, note that this is your YubiKey's `FIDO2 PIN` you need to enter.

- When your device begins flashing, touch the metal contact to confirm the registration
- To associate further devices run the following command

  ```bash
  pamu2fcfg -n >> ~/.config/Yubico/u2f_keys
  ```

## Configure the system to use the YubiKey

> **Warning:** Misconfigurations can cause you to be locked out of your system. Use caution!
>
> It is recommended to open an admin shell and a second shell for testing purposes. In case something doesn't work, you can undo any changes in the admin shell.

### 2FA for sudo

- In admin shell open `/etc/pam.d/sudo`

  ```bash
  sudo nano /etc/pam.d/sudo
  ```

- Add the following line before `@include common-auth`

  ```text
  auth     sufficient pam_u2f.so cue
  ```

  - **sufficient**: Less restrict, if Yubikey is acceptated, no password is needed. Use **required** if both factors should be used.
  - **cue**: Set to prompt a message to remind to touch the FIDO authenticator.

  Example:

  ```text
  #%PAM-1.0

  # Set up user limits from /etc/security/limits.conf.
  session    required   pam_limits.so

  session    required   pam_env.so readenv=1 user_readenv=0
  session    required   pam_env.so readenv=1 envfile=/etc/default/locale user_readenv=0

  auth     sufficient pam_u2f.so cue 

  @include common-auth
  @include common-account
  @include common-session-noninteractive
  ```

- Test in second shell

  ```bash
  sudo echo test
  ```
  
  When prompted, enter your password and press **Enter**. Then, touch the capacitive touch sensor on your YubiKey when it begins flashing.

If the password was accepted this time, you have configured the system correctly and can continue on to the next section for requiring the YubiKey to log in.

#### Adding other commands like su

The PAM module differentiates between various states of the command sudo as they have different authentication pathways. This means that depending on your version you may have to edit another file with the PAM information to make it valid. In Ubuntu 22.04, the following commands have the following files you can edit to add authentication:

|Command|File Location|
|---|---|
|su|/etc/pam.d/su|
|sudo-i|/etc/pam.d/sudo-i|

### 2FA for Login

- In admin shell open `/etc/pam.d/gdm-password`

  ```bash
  sudo nano /etc/pam.d/gdm-password
  ```

- Add the following line before `@include common-auth`

  ```text
  auth     required pam_u2f.so cue
  ```

### Troubleshooting

If you are unable to login and are unsure why, you can enable debugging on the Yubico PAM-U2F module using the steps below. This provides insight into why the module is not allowing the login.

- Open Terminal and run the following command:

  ```bash
    sudo touch /var/log/pam_u2f.log
  ```

- Open your pam.d file which causes the trouble (e.g. /etc/pam.d/gdm-password, /etc/pam.d/sudo, /etc/pam.d/su)
  and add the following:

  ```bash
    sudo nano /etc/pam.d/gdm-password
    auth       required   pam_u2f.so debug_file=/var/log/pam_u2f.log
  ```

Each subsequent login event will have the debug log saved in the /var/log/pam_u2f.log file.
