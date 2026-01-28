# OS Architecture & Kernel Build Lab — Detailed Guide

**Tamilselvan | Cyber Security Researcher | Ethical Hacker**  
*Search: [Tamilselvan Cybersecurity](https://www.google.com/search?q=Tamilselvan+Cybersecurity)*

---

## 1. Overview

**OS Architecture & Kernel Build Lab** is a **Python-based CLI (command-line interface)** for learning:

- **Linux OS architecture** — user/kernel space, system calls, subsystems
- **Kernel building** — obtain source, configure, compile, install, boot
- **Boot process** — BIOS/UEFI, bootloader, initramfs, init
- **Build environment** — tools, packages, cross-compilation
- **Example source code** — kernel modules, userspace C, scripts, config files

It is **educational**: it explains concepts and shows commands. Real kernel builds should be done in a **VM or spare machine**, not on your main system.

---

## 2. How It Works

### 2.1 Startup Flow

1. **Banner** — ASCII art and credit.
2. **Main menu** — Choose 1–5 for a module, or 0/exit/quit/q to quit.
3. **Module** — Each module is a step‑by‑step tutorial with **Press Enter to continue** between sections.
4. **Back to menu** — After a module, you return to the menu until you exit.

### 2.2 Dependencies

- **Runtime**: `rich>=13.0.0` (panels, markdown, tables, prompts).  
  If `rich` is missing, the CLI falls back to plain text.
- **Standard library**: `sys`, `getpass`, `hashlib`, `os`, `subprocess`, etc.

---

## 3. Requirements

- **Python** 3.8 or newer
- **OS**: Windows, Linux, or macOS (CLI runs everywhere; some examples and commands are Linux-specific)
- **Disk**: Minimal for the lab; ~20–30 GB free if you do real kernel builds in a VM

---

## 4. Installation

### 4.1 From Source

```bash
# 1) Enter the project folder
cd os-archi-lab

# 2) Optional: virtual environment
python -m venv venv
# Windows:
venv\Scripts\activate
# Linux/macOS:
source venv/bin/activate

# 3) Install runtime dependencies
pip install -r requirements.txt

# 4) Run
python main.py
# or
python -m os_archi_lab
```

### 4.2 As Installed Package (Optional)

```bash
pip install -e .
# Then from anywhere:
os-archi-lab
```

### 4.3 From .exe (Windows)

- Build once (see **Building the .exe** below), then run `dist\OS-Archi-Lab.exe`.
- No Python needed on the machine where you run the .exe.

---

## 5. Usage

### 5.1 Ways to Run

| Method              | Command                          | Notes                          |
|---------------------|----------------------------------|--------------------------------|
| Script              | `python main.py`                 | From project root              |
| Module              | `python -m os_archi_lab`         | From project root              |
| Installed command   | `os-archi-lab`                   | After `pip install -e .`       |
| Windows .exe        | `dist\OS-Archi-Lab.exe`          | After building with PyInstaller |

### 5.2 Main Menu

```
  [1] Linux OS Architecture    — Layers, syscalls, kernel subsystems
  [2] Kernel Build Lab         — Source, config, compile, install, boot
  [3] Boot Process             — BIOS/UEFI, bootloader, initrd, init
  [4] Build Environment        — Tools, deps, cross-compilation
  [5] Example Source Code      — Kernel modules, userspace C, scripts, config
  [0] Exit                     — or type: exit | quit | q
```

- **Valid choices**: `1`, `2`, `3`, `4`, `5` — run that module.  
- **Exit**: `0`, `exit`, `quit`, `q` (case-insensitive).  
- **Invalid**: Message “Invalid. Choose 1–5, or 0/exit/quit/q to quit.” and Press Enter to try again.

### 5.3 Inside a Module

- Sections are shown one by one.  
- At the end of each: **Press Enter to continue**.  
- **Ctrl+C** or **EOF** at “Press Enter” exits the program with code `0`.  
- When the module ends, you return to the main menu.

### 5.4 Exit Codes (for Scripts)

When the process exits:

| Code | Meaning                                                                 |
|------|-------------------------------------------------------------------------|
| `0`  | Normal: you chose Exit, or pressed Ctrl+C at “Press Enter to continue”  |

Example (Windows CMD): `python main.py` then `echo %ERRORLEVEL%`.  
Example (Bash): `python main.py; echo $?`

---

## 6. Help — Modules in Detail

### 6.1 [1] Linux OS Architecture

**Purpose:** Understand the layout of the Linux kernel and how user programs talk to it.

**Contents:**
- **Overview** — Monolithic kernel, user vs kernel space, system calls.
- **Layered diagram** — ASCII: User space → syscall interface → Kernel (syscall, VFS, scheduler, MM, net, drivers) → Hardware.
- **Kernel subsystems** — Process, Memory, VFS, Net, Block, Driver; short role of each.
- **System-call flow** — libc → register setup → `int 0x80`/`syscall` → kernel handler → return. Example: `strace -e write echo hello`.
- **Kernel source paths** — `arch/`, `kernel/`, `mm/`, `fs/`, `net/`, `drivers/`, `include/`, `init/`.
- **Security** — Syscall surface, KASLR, SMEP, SMAP, rootkits, containers.
- **Extra examples** — Syscall numbers (x86-64), `/proc` layout, `printk`/`pr_info` and `dmesg`.

---

### 6.2 [2] Kernel Build Lab

**Purpose:** Learn the **steps** to build a Linux kernel (source → config → compile → install → boot).

**Contents:**
- **Overview** — 5 steps; real builds should be in a VM.
- **Step 1 — Obtain source** — kernel.org, distro (`apt source`), git. Commands: `wget`, `tar`. Note: verify PGP.
- **Step 2 — Configure** — `defconfig`, `menuconfig`, `xconfig`, `oldconfig`, `localmodconfig`. Commands: `make defconfig`, `make menuconfig`.
- **Step 3 — Compile** — `make`, `make -j$(nproc)`, `make modules`, `make bzImage`, `make bindeb-pkg`.
- **Step 4 — Install** — `make modules_install`, `make install`, `INSTALL_MOD_PATH` for a separate root.
- **Step 5 — Bootloader** — GRUB, `update-grub`.
- **Cross-compilation** — e.g. `ARCH=arm64`, `CROSS_COMPILE=aarch64-linux-gnu-`.
- **Checklist** — Table: get source, config, build, modules_install, kernel install, initrd, bootloader, reboot.
- **Examples** — Kbuild `obj-m` Makefile for out-of-tree module, `.config` snippets, build-script sequence.

---

### 6.3 [3] Boot Process

**Purpose:** From power-on to login: firmware → bootloader → kernel → init.

**Contents:**
- **Overview** — Four stages.
- **Diagram** — BIOS/UEFI → Bootloader → Kernel → init → user space.
- **BIOS vs UEFI** — MBR vs GPT/ESP, legacy vs UEFI boot.
- **Bootloader (GRUB)** — `grub.cfg`, `linux`/`initrd` lines, `root=`, `init=`, `rdinit=`. Command: `cat /boot/grub/grub.cfg`.
- **Initramfs/Initrd** — cpio, `/init`, loading drivers, mounting root, `pivot_root`, `exec /sbin/init`. Tools: `mkinitramfs`, `dracut`, `mkinitcpio`. Command: `lsinitramfs`.
- **Kernel command line** — `root=`, `ro`/`rw`, `init=`, `console=`, `mitigations=`.
- **Init (systemd)** — `/sbin/init`, targets, `systemctl get-default`.
- **Security** — Secure Boot, initramfs integrity, protecting GRUB.
- **Examples** — GRUB menuentry, minimal `/init` for initramfs.

---

### 6.4 [4] Build Environment

**Purpose:** What to install to build the kernel; check if tools exist; cross-compilation and lab setup.

**Contents:**
- **What you need** — gcc, make, ncurses, OpenSSL, libelf, flex, bison, bc, rsync.
- **Packages by distro** — Debian/Ubuntu, Fedora/RHEL, Arch: package names and `apt`/`dnf`/`pacman` examples.
- **Tool check** — Runs `gcc --version`, `make --version`, `flex --version`, `bison --version` and shows a table (OK / Not found).
- **Cross-compilation** — aarch64, ARM; `gcc-aarch64-linux-gnu`, `ARCH`, `CROSS_COMPILE`.
- **Lab setup** — VM, disk, RAM; never replace the host kernel blindly.
- **Examples** — Dockerfile for a build image, QEMU one-liner to boot bzImage+initrd, installing `linux-headers-$(uname -r)` for modules.

---

### 6.5 [5] Example Source Code

**Purpose:** Browse and copy **real** source you can build and run.

**Contents:**
- **1. hello.c** — Minimal kernel module (init/exit, `pr_info`). Build: `make` in `examples/kernel_module/hello`. Load: `sudo insmod hello.ko`; unload: `sudo rmmod hello`.
- **2. proc_hello.c** — Kernel module that adds `/proc/os_archi_lab`. Build in `examples/kernel_module/proc_example`. Test: `cat /proc/os_archi_lab`.
- **3. raw_syscall.c** — Userspace: `syscall(SYS_getpid)`, `syscall(SYS_write, ...)`. Build: `gcc -o raw_syscall raw_syscall.c`.
- **4. read_proc.c** — Read `/proc/version`, `/proc/self/status`, `/proc/cpuinfo`.
- **5. sysfs_example.c** — Read `/sys/kernel/version`, hostname, cpu0 cpufreq, module refcnt.
- **6. build_kernel.sh** — Script: defconfig, make, modules, modules_install. Set `KERNEL_SRC`; use in a VM.
- **7. initramfs_init.sh** — Minimal `/init` example: mount /proc, /sys, /dev; placeholder for root mount and `switch_root`.
- **8. minimal_config_snippet.txt** — Example `CONFIG_*` for `.config`.
- **9. grub_entry_example.cfg** — GRUB menuentry template; use with `/etc/grub.d/40_custom` and `update-grub`.

The lab reads from the `examples/` folder. When run as **PyInstaller .exe**, `examples/` is bundled and read from `sys._MEIPASS/examples`.

---

## 7. Example Source Code (examples/ Folder)

### 7.1 Layout

```
examples/
├── kernel_module/
│   ├── hello/           # Hello World .ko
│   │   ├── hello.c
│   │   └── Makefile
│   └── proc_example/    # /proc entry
│       ├── proc_hello.c
│       └── Makefile
├── userspace/
│   ├── raw_syscall.c
│   ├── read_proc.c
│   ├── sysfs_example.c
│   └── Makefile
├── scripts/
│   ├── build_kernel.sh
│   └── initramfs_init.sh
├── config/
│   ├── minimal_config_snippet.txt
│   └── grub_entry_example.cfg
└── README.md
```

### 7.2 Build & Run (on Linux)

**Kernel modules** (need kernel build env and `linux-headers-$(uname -r)`):

```bash
# hello.ko
cd examples/kernel_module/hello
make
sudo insmod hello.ko
dmesg | tail
sudo rmmod hello

# proc_hello.ko
cd examples/kernel_module/proc_example
make
sudo insmod proc_hello.ko
cat /proc/os_archi_lab
sudo rmmod proc_hello
```

**Userspace** (only `gcc`):

```bash
cd examples/userspace
make
./raw_syscall
./read_proc
./sysfs_example
```

**Scripts** — `build_kernel.sh`: set `KERNEL_SRC`, run in a VM.  
**Config** — Copy from `config/` into your kernel `.config` or GRUB as needed.

---

## 8. Building the .exe (Windows)

### 8.1 Steps

```batch
# From project root (os-archi-lab\)

# 1) Install PyInstaller
pip install -r requirements-build.txt

# 2) Build (one of these)
build_exe.bat
# or
.\build_exe.ps1
# or
pyinstaller --noconfirm os_archi_lab.spec
```

### 8.2 Output

- **File:** `dist\OS-Archi-Lab.exe`
- **Icon:** `logo/logo.png` (Windows uses it for the .exe).
- **Bundled:** `examples/` is inside the .exe; the Example Source Code module works without an external `examples/` folder.
- **Console:** Runs in a console window; menus work like `python main.py`.

### 8.3 If the Icon Does Not Show

- PyInstaller on Windows works best with `.ico`.  
- Convert `logo/logo.png` to `logo/logo.ico` and in `os_archi_lab.spec` set:  
  `icon='logo/logo.ico'`

---

## 9. File Reference

| File | Role |
|------|------|
| `main.py` | Runs `os_archi_lab.cli.main` and `sys.exit(main())`. |
| `os_archi_lab/__main__.py` | Enables `python -m os_archi_lab`; calls `cli.main`. |
| `os_archi_lab/cli.py` | Banner, menu loop, `_parse_choice`, `_do_exit`, dispatches to modules. |
| `os_archi_lab/utils/helpers.py` | `banner`, `print_banner`, `print_section`, `print_step`, `print_code`, `print_table`, `wait_continue`, `EXIT_OK`, `EXIT_AUTH`, `CREDIT`. |
| `os_archi_lab/utils/auth.py` | Auth gate at startup; exits on failure. |
| `os_archi_lab/modules/architecture.py` | `run_architecture_lab`: user/kernel diagram, subsystems, syscall flow, paths, security, examples. |
| `os_archi_lab/modules/kernel_build.py` | `run_kernel_build_lab`: source, config, compile, install, bootloader, cross-compile, checklist, Kbuild/config/script examples. |
| `os_archi_lab/modules/boot_process.py` | `run_boot_process_lab`: BIOS/UEFI, GRUB, initramfs, kernel cmdline, init, GRUB/init examples. |
| `os_archi_lab/modules/build_env.py` | `run_build_env_lab`: deps table, package names, tool check (gcc, make, flex, bison), cross-compile, Dockerfile/QEMU/headers examples. |
| `os_archi_lab/modules/examples.py` | `run_examples_lab`: `_read()` from `examples/` or `sys._MEIPASS/examples` when frozen; shows all 9 example sources and build/run steps. |
| `os_archi_lab.spec` | PyInstaller: `main.py`, `datas=('examples','examples')`, `icon='logo/logo.png'`, hiddenimports, excludes. |

---

## 10. Useful Tips

- **Kernel builds:** Do them only in a VM or on a spare machine. Keep a snapshot before building.  
- **Example modules:** Need a Linux system and kernel headers; `linux-headers-$(uname -r)` or full kernel source.  
- **Userspace examples:** Need only `gcc`; run on Linux (or WSL) for `/proc` and `/sys`.  
- **.exe:** First build can take a few minutes; `excludes` in the spec help reduce size and time.  
- **Rich:** If `rich` is not installed, the CLI still works with plain text.

---

## 11. License & Disclaimer

**Educational use.**  
Respect your system: run real kernel builds only in VMs or dedicated lab machines.  
The lab does not modify your OS; you alone are responsible for any commands you run on real hardware.

---

*OS Architecture & Kernel Build Lab — Learn. Build. Secure.*
