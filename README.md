# Auto-File-Uploader

## Overview

This setup turns a **Raspberry Pi Zero 2 W** into a smart flash drive that:

1. Pretends to be a USB stick for the **Allen & Heath SQ6** mixer.
2. Stores recordings temporarily.
3. Automatically uploads new files to **Google Drive**.
4. Wipes them afterward.
5. Keeps credentials and scripts locked away in an encrypted partition.

---

## Hardware Required

| Item                                           | Notes                                 |
| ---------------------------------------------- | ------------------------------------- |
| Raspberry Pi Zero 2 W                          | Must support USB gadget mode.         |
| MicroSD Card (16–32 GB, Class 10 A1 or better) | OS + storage image + encrypted vault. |
| USB-A → Micro-B Cable                          | Mixer → Pi (data + power).            |
| Optional: USB hub / power adapter              | For initial setup or longer power.    |

---

## Software Stack

| Component                     | Purpose                           |
| ----------------------------- | --------------------------------- |
| Raspberry Pi OS Lite (64-bit) | Base OS.                          |
| `rclone`                      | Upload to Google Drive.           |
| `cryptsetup`                  | LUKS encryption.                  |
| `dosfstools` + `util-linux`   | FAT32 creation and loop-mounting. |
| `systemd` timers              | Automation scheduler.             |

---

## Maintenance and Safety

- **Passwords:** Change default `pi` password.
- **Encryption:** All secrets live in `/secure` (LUKS-encrypted).
- **Dedicated Drive account:** Limits damage if token leaks.
- **Corruption check:**
  ```bash
  /usr/local/sbin/usb-gadget-detach.sh
  LOOP=$(losetup -fP --show /srv/sq6.img)
  fsck.vfat -a ${LOOP} || true
  losetup -d $LOOP
  /usr/local/sbin/usb-gadget-attach.sh
  ```

---

## Quick Reference

```
Mixer USB Port → USB-A → Micro-B Cable → Pi “USB” Port
Pi MicroSD:
 ├─ Raspberry Pi OS (root)
 ├─ /srv/sq6.img → exposed to SQ6
 └─ /srv/secure.luks → encrypted vault
```

---

## Expected Setup Time

| Phase                | Time       |
| -------------------- | ---------- |
| Flash OS + Boot      | 30–60 min  |
| Security + Updates   | 30 min     |
| Storage + Encryption | 1–2 hr     |
| USB Gadget + Scripts | 2 hr       |
| Testing + Debugging  | 1–2 hr     |
| **Total**            | **~1 Day** |

---

## Outcome

- SQ6 records directly to the Pi as if it’s a normal USB stick.
- Pi uploads new recordings to Google Drive automatically.
- Encrypted partition protects credentials and scripts.
- Fully hands-off once powered.

---
