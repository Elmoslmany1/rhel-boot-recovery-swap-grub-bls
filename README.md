## Problem

The system failed to boot because the original swap LV was removed, while the boot configuration still referenced the old swap/resume information.

## Troubleshooting and Recovery

1. Booted the system using:

   ```bash
   init=/bin/bash
   ```

2. The root filesystem was mounted read-only, so it was remounted as read-write:

   ```bash
   mount -o rw,remount /
   ```

3. Removed the old swap entry from `/etc/fstab` and removed the old swap-related configuration.

4. Verified the GRUB BLS configuration:

   ```bash
   /etc/default/grub
   ```

   and confirmed:

   ```text
   GRUB_ENABLE_BLSCFG=true
   ```

5. Mounted the required filesystems before modifying files under `/boot`:

   ```bash
   mount -a
   mount -o rw,remount /boot
   ```

6. Updated the kernel BLS entry under:

   ```text
   /boot/loader/entries/
   ```

   and removed the obsolete:

   ```text
   resume=UUID=<old-UUID>
   ```

   then replaced it with the correct value.

7. Because the VM was accessed through a text-based KVM console, terminal capabilities caused issues with `nano`/`vim`. This was resolved with:

   ```bash
   export TERM=xterm-256color
   ```

8. Updated the GRUB configuration:

   ```bash
   grub2-mkconfig -o /boot/grub2/grub.cfg
   ```

9. Triggered SELinux relabeling for the next boot:

   ```bash
   touch /.autorelabel
   ```

10. Since the system had been started with `init=/bin/bash` rather than normal systemd initialization, normal reboot/systemd operations were not available. The systemd process was started with:

    ```bash
    exec /usr/lib/systemd/systemd
    ```

11. After the system booted successfully, a new swap LV was created/configured and registered correctly in `/etc/fstab`.

## Result

The system successfully returned to a normal boot process after repairing the swap configuration, boot entries, filesystem access, and required boot metadata.

## Video Demonstration

The complete troubleshooting process is demonstrated in the following video, from the boot failure and recovery environment through configuration repair and successful system boot:

**[▶ Watch the troubleshooting demonstration](../../releases/tag/v1.0)**
