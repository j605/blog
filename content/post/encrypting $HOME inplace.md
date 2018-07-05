---
title: Encrypting /home in place
date: 2018-07-05T20:55:57+02:00
tags:
  - archlinux
  - encryption
categories:
  - dgplug
---

# Raison d'être
When I first installed this system, I was oblivious to disk encryption and didn't
factor it in. This resulted in a non-[LVM](https://en.wikipedia.org/wiki/Logical_Volume_Manager_%28Linux%29)
system with some haphazard partitions from subsequent resizing and nuking of Windows
in the past few years. One good decision I took when installing was to separate
`/` and `/home` so this turned out to be easier than I thought.

# Preparation
I was apprehensive of ending up with a dead system before today's DGPLUG session,
so I managed to get `weechat` up and running inside `tmux` in a virtual console.
Next I had the [wiki article](https://wiki.archlinux.org/index.php/Dm-crypt/Device_encryption#Re-encrypting_devices)
opened on `elinks`, a TUI browser. I don't want to repeat any commands that are
already available in the wiki. I would like to make a more cogent story mentioning some caveats
in the process. First the `resize2fs` command to shrink `/home` took some time.
This was done to make space for `LUKS` header which would be written during encryption. I
wish there was warning in the wiki about how long this would take. At least I could've
enabled the progress option for `resize2fs` 😀

# Encrypting the disk device
After `resize2fs` completes we can start encrypting the partition. It is interesting
to note that `cryptsetup-reencrypt` is quite recent and I found another project that
was developed in parallel; [`luiksipc`](https://johndoe31415.github.io/luksipc/introduction.html),
which as the name suggests does `luks` in-place. But since this functionality is
available upstream I didn't explore the alternative.

`cryptsetup-reencrypt` doesn't emit any warnings like the wiki alludes to and this gives me hope
that the functionality was stabilized recently. For the passphrase I took [Kushal's](https://kushaldas.in/)
suggestion to use XKCD style passwords. `gopass` includes an option to do it quite
easily and it generated a strong yet easy to remember password.

![password comic from xkcd.com](https://imgs.xkcd.com/comics/password_strength.png)

This step took more than 4 hours, but I was attending the class and doing something
productive in the meantime. The next steps were to grow the filesystem again and then
test it out.

## Encrypted swap
Since swap holds the data when hibernating the system we have to secure this as well.
A `swapfile` is the easiest approach in my case. Follow the [wiki article](https://wiki.archlinux.org/index.php/Swap#Swap_file_creation)
to create swap but do it under `/home` since that is what we have encrypted.

# Integrating it with Systemd and Syslinux
I would like to skip all the mistakes and go straight to the wonderful solution.
The mistakes in this case would only confuse the reader. Thanks to Khorne from
\#archlinux-newbie I was able to recover quickly after a few reboots.

## Add the appropriate hooks
I assume you are using a `systemd` based initramfs, if not switch to it for the
fancy `systemd-analyze` output.

```bash
HOOKS=(systemd autodetect keyboard sd-vconsole modconf block sd-encrypt sd-lvm2 resume filesystems fsck)
```
Things to lookout for:

1. `keyboard` for early userspace functionality to enter the password.
2. `sd-vconsole` for different keymaps and fonts which is the `systemd` equivalent
for the `keymap` hook.
3. `sd-encrpyt` and `sd-lvm2` are the respective equivalents for `encrpyt` and `lvm2`.
The former is what prompts for password when booting early on.

## Modify kernel parameters in `syslinux.cfg`
This should be boot loader agnostic but I just like `syslinux` better after being
scarred by `grub-mkconfig`. If you use grub, I highly suggest using your own `grub.cfg`
[like so](https://ptpb.pw/mk7y) which was taken from \#archlinux. Append the following
to kernel parameters,

```conf
rd.luks.name=UIID=home resume=/dev/mapper/home resume_offset=OFFSET
```
`resume_offset` is obtained from
```sh
$ sudo filefrag -v /home/swapfile | awk '{if($1=="0:"){print $4}}'
```
taken from the wiki article on swap encryption.

## fstab changes
The previous section will add the device `/dev/mapper/home` which we will use in
`/etc/fstab`.
```
# mounting home
/dev/mapper/home  /home       ext4        rw,relatime,lazytime,data=ordered,nodev,nosuid  0 2

# swapfile
/home/swapfile    none        swap        defaults                                        0 0
```
Most of these settings are opinionated, so I would suggest looking at `fstab(5)`
and `ext4(5)` for explanations. This completes the loop to mount the encrypted device
opened by `sd-encrypt` hook. Like most other things `systemd` takes care of dependencies and
generates appropriate `.mount` and `.device` files.

# Last step
Generate initramfs, reboot and rejoice!

In Arch Linux,
```sh
$ sudo mkinitcpio -P
```
to get initramfs and initrd.

At the end I would say my understanding of all the different systems made it
difficult to get it running quickly. Systemd probably made some of this bearable
by integrating disparate systems and providing much-needed dependency tracking.
