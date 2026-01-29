# OS Architecture & Kernel Build Lab

**A Python-based CLI learning lab for Linux OS architecture and kernel building methods.**

---

## Developed By

**Tamilselvan**  
Cyber Security Researcher | Ethical Hacker  
*Search: [Tamilselvan Cybersecurity](https://www.google.com/search?q=Tamilselvan+Cybersecurity)*

---

## Overview

This lab provides an interactive, educational CLI environment to learn Linux OS architecture and kernel building. Perfect for students, security researchers, and anyone exploring OS development.

**Key Learning Areas:**
- **Linux OS Architecture** — User/kernel space, system calls, memory management, process management, filesystem
- **Kernel Building** — Source acquisition, configuration, compilation, installation, bootloader integration
- **Boot Process** — BIOS/UEFI, bootloader (GRUB), initramfs, init system
- **Build Environment** — Required tools, dependencies, cross-compilation setup
- **Example Source Code** — Real kernel modules, userspace C programs, build scripts, configuration files

---

## Advantages

| Feature | Advantage | Benefit |
|---------|-----------|---------|
| **Interactive CLI** | Rich console interface with colors, tables, and formatted output | Enhanced learning experience with clear visual presentation |
| **Modular Design** | Separate modules for architecture, kernel build, boot process, build environment | Focused learning on specific topics without distraction |
| **Real Source Code** | Complete, runnable examples (kernel modules, userspace C, scripts) | Hands-on practice with actual code you can build and run |
| **Password Protection** | Secure authentication with PBKDF2-HMAC-SHA256 hashing | Protects lab content and ensures authorized access |
| **Cross-Platform** | Works on Linux, Windows (WSL), and macOS | Learn OS concepts regardless of your development environment |
| **PyPI Package** | Installable via `pip install os-archi-lab` | Easy distribution and integration into other projects |
| **Standalone Executable** | Build Windows `.exe` with PyInstaller | Run without Python installation, perfect for sharing |
| **Programmatic API** | Import and use modules/helpers in your own code | Integrate into custom learning tools or automation |
| **Comprehensive Examples** | 9+ example files covering kernel modules, syscalls, /proc, /sys | Learn by example with production-ready code patterns |
| **Educational Focus** | Designed specifically for learning OS internals | Structured content progression from basics to advanced topics |

---

## OS Working Principles

### 1. User Space vs Kernel Space

**User Space (Ring 3):**
- Applications, libraries, shells run here
- Restricted access to hardware
- Cannot directly access kernel memory
- Uses system calls to request kernel services

**Kernel Space (Ring 0):**
- Full hardware access
- Manages system resources (CPU, memory, I/O)
- Handles interrupts, device drivers, scheduling
- Protected from user space access

**System Call Interface:**
- The only sanctioned path from user → kernel
- Applications call library functions (e.g., `write()`)
- Library invokes system call instruction (`int 0x80` or `syscall`)
- Kernel validates and executes request
- Returns result to user space

### 2. Kernel Subsystems

| Subsystem | Purpose | Key Components |
|-----------|---------|----------------|
| **Process Management** | Task scheduling, creation, termination | Scheduler, `task_struct`, PID namespace, signals, cgroups |
| **Memory Management** | Virtual memory, paging, allocation | Virtual memory manager, page tables, SLUB/SLAB allocators, swap |
| **Virtual File System (VFS)** | Unified interface for filesystems | `inode`, `dentry`, `super_block`, mount points |
| **Network Stack** | TCP/IP, sockets, protocols | `sk_buff`, netfilter, network device drivers |
| **Block I/O** | Disk access, I/O scheduling | Request queue, block device layer, I/O schedulers |
| **Device Drivers** | Hardware abstraction | Character, block, network drivers; PCI, USB, platform buses |

### 3. Boot Process Flow

```
[ BIOS / UEFI ]
      │
      │ POST, find boot device, load boot sector / EFI stub
      ▼
[ Bootloader: GRUB, systemd-boot ]
      │
      │ Load vmlinuz + initrd/initramfs; pass cmdline (root=, init=)
      ▼
[ Linux Kernel ]
      │
      │ Decompress, early init, mount root, execute /sbin/init
      ▼
[ init: systemd, SysV, OpenRC ]
      │
      │ /etc/inittab or units; bring up services, getty, X/Wayland
      ▼
[ User Space — Login, Desktop, Services ]
```

**Key Stages:**
1. **Firmware (BIOS/UEFI)**: Power-on self-test, locate boot device
2. **Bootloader**: Load kernel image and initial RAM disk (initrd/initramfs)
3. **Kernel**: Initialize hardware, mount root filesystem, start init process
4. **Init System**: Launch system services, getty (login prompts), desktop environment

### 4. System Call Flow

```
User Application
      │
      │ calls write()
      ▼
C Library (glibc)
      │
      │ puts syscall number in register (rax), args in rdi, rsi, rdx...
      │ executes syscall instruction
      ▼
Kernel Entry (entry_64.S)
      │
      │ saves user state, switches to kernel stack
      │ dispatches via sys_call_table[syscall_number]
      ▼
Kernel Handler (sys_write)
      │
      │ validates arguments, performs operation
      │ returns result in rax
      ▼
Return to User Space
      │
      │ restores user state, returns to libc
      ▼
Application receives result
```

---

## Installation

### Method 1: Install from PyPI (Recommended)

```bash
pip install os-archi-lab
```

After installation, run:
```bash
os-archi-lab
```

### Method 2: Install from Source

```bash
# Clone or download the project
cd os-archi-lab

# Create virtual environment (recommended)
python -m venv venv
source venv/bin/activate   # Linux/macOS
# or: venv\Scripts\activate  # Windows

# Install dependencies
pip install -r requirements.txt

# Install in development mode
pip install -e .
```

### Method 3: Install from Wheel

```bash
# Download or build wheel file
pip install dist/os_archi_lab-1.0.0-py3-none-any.whl
```

---

## Usage

### Command Line Interface

```bash
# After installation
os-archi-lab

# Or from source
python code.py
python main.py
python -m os_archi_lab
```

**Authentication:**
- Default password: `tamilselvan`
- Password is hashed using PBKDF2-HMAC-SHA256 with a secret pepper
- Input is hidden for security
- Maximum 3 attempts before exit

**Exit Options:**
- Choose `0` from menu
- Type `exit`, `quit`, or `q` (case-insensitive)
- Press `Ctrl+C` at "Press Enter to continue" prompt

**Exit Codes:**
- `0` — Normal exit
- `1` — Access denied (wrong password or too many attempts)

---

## Working Code Examples

### Example 1: Run the Full CLI Programmatically

```python
#!/usr/bin/env python3
"""
Run the complete interactive CLI
"""
from os_archi_lab.cli import main

if __name__ == "__main__":
    main()  # Shows banner, password prompt, and main menu
```

**Save as:** `run_lab.py`  
**Run:** `python run_lab.py`

---

### Example 2: Run a Specific Module Directly

```python
#!/usr/bin/env python3
"""
Run individual learning modules (bypasses password and menu)
"""
from os_archi_lab.modules.architecture import run_architecture_lab
from os_archi_lab.modules.kernel_build import run_kernel_build_lab
from os_archi_lab.modules.boot_process import run_boot_process_lab
from os_archi_lab.modules.build_env import run_build_env_lab
from os_archi_lab.modules.examples import run_examples_lab

# Run architecture module
run_architecture_lab()

# Or run other modules
# run_kernel_build_lab()
# run_boot_process_lab()
# run_build_env_lab()
# run_examples_lab()
```

**Save as:** `run_module.py`  
**Run:** `python run_module.py`

---

### Example 3: Use Helper Functions for Custom Output

```python
#!/usr/bin/env python3
"""
Use helper functions to create custom learning materials
"""
from os_archi_lab.utils.helpers import (
    print_banner,
    print_section,
    print_step,
    print_code,
    print_table,
)

# Display banner
print_banner()

# Print formatted section
print_section(
    "Linux System Calls",
    "System calls are the interface between user space and kernel space.",
    "cyan"
)

# Print numbered steps
print_step(1, "User application calls library function (e.g., write())")
print_step(2, "Library invokes system call instruction")
print_step(3, "Kernel handles request and returns result")

# Display code block
print_code(
    "strace -e write echo hello  # Trace write system calls",
    "bash"
)

# Display table
print_table(
    ["Syscall", "Number (x86-64)", "Purpose"],
    [
        ["read", "0", "Read from file descriptor"],
        ["write", "1", "Write to file descriptor"],
        ["open", "2", "Open file"],
        ["getpid", "39", "Get process ID"],
    ]
)
```

**Save as:** `custom_output.py`  
**Run:** `python custom_output.py`

---

### Example 4: Access Example Source Code Files

```python
#!/usr/bin/env python3
"""
Read example source code files programmatically
"""
import os
from pathlib import Path

def find_examples_dir():
    """Find the examples directory."""
    # Check current directory (development)
    if Path("examples").exists():
        return Path("examples")
    
    # Check package installation location
    try:
        import os_archi_lab
        pkg_path = Path(os_archi_lab.__file__).parent.parent
        examples_path = pkg_path / "examples"
        if examples_path.exists():
            return examples_path
    except:
        pass
    
    # Check PyInstaller bundle
    if hasattr(os, "_MEIPASS"):
        examples_path = Path(os._MEIPASS) / "examples"
        if examples_path.exists():
            return examples_path
    
    return None

def read_example(rel_path: str) -> str:
    """Read an example file."""
    examples_dir = find_examples_dir()
    if not examples_dir:
        return f"Examples directory not found: {rel_path}"
    
    file_path = examples_dir / rel_path
    if file_path.exists():
        with open(file_path, "r", encoding="utf-8") as f:
            return f.read()
    return f"File not found: {file_path}"

# Read kernel module example
hello_c = read_example("kernel_module/hello/hello.c")
print(hello_c)

# Read userspace example
syscall_c = read_example("userspace/raw_syscall.c")
print(syscall_c)
```

**Save as:** `read_examples.py`  
**Run:** `python read_examples.py`

---

### Example 5: Create Custom Learning Tool

```python
#!/usr/bin/env python3
"""
Integrate os-archi-lab components into your own application
"""
from os_archi_lab.modules.architecture import ARCH_DIAGRAM
from os_archi_lab.utils.helpers import print_section, print_code, print_step

def my_custom_tool():
    """Custom learning tool using os-archi-lab components."""
    
    print_section(
        "My Custom OS Learning Tool",
        "Learn Linux OS architecture with interactive examples.",
        "green"
    )
    
    # Use the architecture diagram
    print_section("Linux Architecture", ARCH_DIAGRAM, "blue")
    
    # Add custom content
    print_step(1, "Install os-archi-lab: pip install os-archi-lab")
    print_code("pip install os-archi-lab", "bash")
    
    print_step(2, "Import and use modules")
    print_code(
        "from os_archi_lab.modules.architecture import run_architecture_lab\n"
        "run_architecture_lab()",
        "python"
    )
    
    print("\n✅ Custom tool created!")

if __name__ == "__main__":
    my_custom_tool()
```

**Save as:** `custom_tool.py`  
**Run:** `python custom_tool.py`

---



### Package Metadata

The package is configured in `pyproject.toml`:
- **Name:** `os-archi-lab`
- **Version:** `1.0.0`
- **Author:** Tamilselvan
- **License:** Educational Use License
- **Python:** 3.8+
- **Dependencies:** `rich>=13.0.0`

---

## Requirements

- **Python:** 3.8 or higher
- **Operating System:** Linux (recommended), Windows (WSL), or macOS
- **Dependencies:** `rich>=13.0.0` (for enhanced console output)

**Optional (for building):**
- `pyinstaller>=6.0.0` (for creating `.exe` files)
- `build` (for building Python packages)
- `twine` (for uploading to PyPI)

---


## Example Source Code

The lab includes **9+ complete, runnable examples**:

### Kernel Modules
- **`hello.c`** — Basic "Hello World" kernel module
- **`proc_hello.c`** — Kernel module creating `/proc/os_archi_lab` entry

### Userspace C Programs
- **`raw_syscall.c`** — Direct system calls without libc wrappers
- **`read_proc.c`** — Reading `/proc` filesystem entries
- **`sysfs_example.c`** — Interacting with `/sys` filesystem

### Build Scripts
- **`build_kernel.sh`** — Complete kernel build script
- **`initramfs_init.sh`** — Initramfs initialization script

### Configuration Files
- **`minimal_config_snippet.txt`** — Minimal kernel `.config` example
- **`grub_entry_example.cfg`** — GRUB bootloader configuration

All examples are in the `examples/` directory and can be accessed via the interactive CLI or programmatically.

---

## License

Educational Use License — See [LICENSE](LICENSE) for details.

**Important:** This lab is for educational purposes. Always use virtual machines or dedicated lab machines for kernel building experiments. Respect your system and follow security best practices.

---

*OS Architecture & Kernel Build Lab — Learn. Build. Secure.*  
*Developed by Tamilselvan | Cyber Security Researcher | Ethical Hacker*
