# NyvaOS

**A small, glass-styled desktop operating system for x86-64, written from scratch in C.**

NyvaOS has its own kernel, window compositor and apps. It includes a real web browser (built on the NetSurf engine), an email client, secure HTTPS connections and saving to disk. The whole system boots from a 13 MB ISO in a few seconds.

![The NyvaOS Dashboard](docs/screenshot-dashboard.png)

![The Web app showing the NyvaOS start page](docs/screenshot-web.png)

---

## Features

### Desktop
- A glass-style desktop with a taskbar, dock, Dashboard, notifications and widgets.
- **Personalisation**:
  - Day, Night and Midnight modes, accent colours and themes, including a random theme.
  - A background picture or colour, and night light.
  - A 12- or 24-hour clock.
  - Your own greeting, widgets, taskbar and dock layout, and pinned apps.
- **Multiple user accounts**: administrators and standard users, each with their own files, settings and mail account.
- **Passwords**: add, change or remove a password for any account. Passwords are stored as salted hashes, never as plain text.
- Lock, sign out, restart and shut down.

### Apps
| App | What it does |
|---|---|
| **Web** | Browser built on NetSurf. Tabs (on top or down the side), HTTPS with full certificate checking, a padlock in the address bar, search from the address bar, forms, cookies, images, zoom and downloads. |
| **Mail** | Reads your Inbox over IMAP and sends over SMTP, both encrypted with TLS. Presets for Gmail, Yahoo and iCloud (these need an app password), plus any other IMAP/SMTP server. |
| **Files** | Browse and organise your files and downloads. |
| **Paper** | Write notes and text files. |
| **Sketch** | Draw and paint. |
| **Utilities** | Calculator, stopwatch and timer. |
| **Gaming** | Snake, 2048, Minesweeper and Tic-tac-toe. |
| **System Manager** | Live CPU and memory graphs and a list of open apps. |
| **Net Tools** | Connection status, ping, DNS lookup and a page fetcher. |
| **Settings** | System, display, date & time, network, storage, appearance, taskbar & dock, accounts and more. |

### Under the hood
- x86-64 kernel booted by [Limine](https://github.com/limine-bootloader/limine), on both BIOS and UEFI.
- Drivers for the Intel PRO/1000 and AMD PCnet network cards, IDE and SATA (AHCI) disks, and PS/2 keyboard and mouse.
- The [lwIP](https://savannah.nongnu.org/projects/lwip/) TCP/IP stack, providing DHCP, DNS, TCP, UDP and ICMP.
- [mbedTLS](https://github.com/Mbed-TLS/mbedtls) for TLS, using the Mozilla list of trusted certificates.
- The [musl](https://musl.libc.org/) C library, running inside the kernel.
- Crash-safe saving. Two copies are kept on disk, and NyvaOS never writes to a disk that holds other data unless you tell it to.

### Limitations
- The browser does not run JavaScript, so very interactive websites won't work. Simple and text-focused sites such as Wikipedia, DuckDuckGo, Hacker News and news sites' text-only editions work well.
- There is no Wi-Fi or sound. NyvaOS is designed to run in a virtual machine.
- NyvaOS is a hobby operating system. It hasn't had a security audit, so don't use it for anything sensitive.

---

## Try it

Download `NyvaOS-Native-M3.iso` from the [Releases](../../releases) page.

**Default sign-in:** account **Admin**, password **nyva**. Change the password in Settings › Accounts.

### VirtualBox
1. Create a new VM with **Type: Other** and **Version: Other/Unknown (64-bit)**.
2. Give it **512 MB of RAM** or more. 128 MB is the minimum for the browser.
3. Add a virtual hard disk (VDI, 64 MB or larger). NyvaOS sets up a blank disk by itself the first time it boots. Without a disk, nothing is saved when you shut down.
4. Under **Network**, leave Adapter 1 on **NAT**. Both *PCnet-FAST III* and *Intel PRO/1000 MT Desktop* work.
5. Attach the ISO to the optical drive and start the VM.

### QEMU
```sh
qemu-img create -f raw disk.img 256M
qemu-system-x86_64 -m 1G -cdrom NyvaOS-Native-M3.iso -boot d \
  -drive file=disk.img,format=raw -nic user,model=e1000
```
To use a SATA disk instead, replace the `-drive` option with `-device ahci,id=a -drive id=d,file=disk.img,if=none,format=raw -device ide-hd,drive=d,bus=a.0`.

### System requirements
- x86-64 processor with SSE2.
- 128 MB of RAM minimum, 512 MB or more recommended.
- A virtual hard disk of 64 MB or more, optional but needed to save your work.
- An Intel PRO/1000 (e1000) or AMD PCnet network card for internet access.

---

## Building from source

You need an x86-64 Linux machine with:
- `gcc`
- `make`
- `python3`
- a [Limine 8.x](https://github.com/limine-bootloader/limine) binary release

1. Set the path to Limine in the `Makefile`:
   ```make
   LIMINE := /path/to/limine
   ```
2. Build:
   ```sh
   make
   ```

This produces `nyvaos.iso`. The prebuilt musl library is in `third_party/musl`; to rebuild it, run `tools/build-musl.sh`.

### Project layout
| Folder | Contents |
|---|---|
| `kernel/` | Boot, memory, interrupts, drivers, networking |
| `posix/` | File system and system calls for the C library, plus the TLS connection layer and HTTP client |
| `gfx/` | Drawing, fonts and icons |
| `ui/` | Window manager, desktop, widgets, accounts and saving |
| `apps/` | All the apps |
| `res/` | Files built into the system (certificates, browser resources) |
| `third_party/` | NetSurf, mbedTLS, musl, zlib and other libraries |
| `tools/` | Build scripts |
| `docs/` | Screenshots |

---

## Licence

NyvaOS is free software, released under the **GNU General Public License, version 2** (GPL-2.0-only). See [`LICENSE`](LICENSE).

Copyright © 2026 Visturn.

NyvaOS includes third-party code, and each part keeps its own licence. They are listed in [`third_party/README.md`](third_party/README.md). The whole system is distributed under GPL-2.0 because NetSurf uses that licence.

## Credits

NyvaOS is built on these open-source projects:
- [NetSurf](https://www.netsurf-browser.org/)
- [Limine](https://github.com/limine-bootloader/limine)
- [lwIP](https://savannah.nongnu.org/projects/lwip/)
- [mbedTLS](https://github.com/Mbed-TLS/mbedtls)
- [musl](https://musl.libc.org/)
- [zlib](https://zlib.net/)
- [stb](https://github.com/nothings/stb)
- [Liberation fonts](https://github.com/liberationfonts/liberation-fonts)
