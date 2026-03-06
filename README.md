Vector OS V5

A lightweight, educational x86 operating system built in assembly language. Vector OS features a custom filesystem, user authentication system, built-in text editor, scripting language, and command-line interface.

🚀 Overview

Vector OS V5 is a personal learning project developed over ~4–5 months of x86 assembly and OS development experience. It demonstrates core operating system concepts including:

protected mode switching

interrupt handling

filesystem design

device drivers

user authentication and permissions

command shell and scripting runtime

The OS runs in 32-bit protected mode and provides a small but usable environment with persistent storage.

Note: This OS was developed with assistance from AI and LLMs to accelerate development. It is a personal assessment project rather than a production OS.

✨ Features
Core System

16-bit → 32-bit protected mode transition

Custom GDT with code and data segments

PIC remapping for interrupt management

IDT setup with keyboard and timer handlers

RTC (Real-Time Clock) integration

VGA text mode interface

🔐 User Authentication System

Vector OS V5 includes a persistent login and user permission system.

Features

First-boot setup wizard

Persistent user registry stored on disk

Multiple user accounts

Root administrator account

Per-file ownership enforcement

Permission checks for file operations

Login Flow

On boot the kernel:

Load user registry
↓
Validate registry signature
↓
Run first-time setup if registry missing
↓
Display login prompt
↓
Authenticate user
↓
Enter shell

Example login screen:

Vector OS Login
Username:
Password:
User Registry

User accounts are stored on disk in a registry sector.

Sector: LBA 50
Size: 1024 bytes
Max users: 32
Record size: 32 bytes
User Record Structure
Offset  Field       Size
0       UID         1
1       Username    11 bytes
12      Password    11 bytes

User ID 0 is reserved for root, which bypasses permission checks.

Permissions Model

Filesystem entries store an owner UID.

Example metadata field:

owner = user id that created the file

Operations enforce permissions:

Operation	Behavior
read	allowed if root OR owner
write	allowed if root OR owner
delete	allowed if root OR owner

If access is denied:

Permission denied.
Filesystem

Vector OS uses a custom sector-based filesystem designed for simplicity and speed.

Features

32-byte metadata entries

Sector-based storage

Hierarchical directory system

File ownership tracking

File and directory operations

Filesystem Layout
LBA 0        Boot sector
LBA 1-99     Kernel / reserved
LBA 50       User registry
LBA 100-120  Metadata sectors
LBA 200+     File data sectors
Metadata Entry Structure (32 bytes)
Offset  Field       Size  Description
0       type        1     'f'=file, 'd'=directory
1-11    name        11    filename
12      perms       1     permissions
13      parent      1     parent directory id
14      owner       1     owner UID
16-19   size        4     file size

Each metadata sector contains 16 entries.

Shell Commands
help     - Display available commands
ls       - List files
lsd      - List directories
mkdir    - Create directory
write    - Create file
read     - Display file contents
cd       - Change directory
rmf      - Delete file
rmd      - Delete directory
clear    - Clear screen
edit     - Open Vector Editor
run      - Execute Vector script
dump     - Dump filesystem metadata
format   - Wipe disk and reset filesystem
compile  - Compile Vector scripts
VED — Vector Editor

A built-in fullscreen text editor.

Features

File loading

File saving

Cursor movement

Backspace + newline handling

ESC to exit and save

4KB editing buffer

Example header:

Vector Editor (ESC to exit)
---------------------------------------
Vector Scripting Language

Vector OS includes a lightweight scripting language.

Features

integer variables

arithmetic operations

basic control flow

command execution

Variables are prefixed with /.

Example:

set /counter 5
print /counter
add /counter 3
print "Now: /counter"

Supported commands:

print
set
add
sub
sleep
label
goto
input
clear
Hardware Support

Drivers implemented directly in assembly:

Disk

ATA PIO disk driver

Sector read/write

Cache flush

Input

PS/2 keyboard driver

Scancode → ASCII translation

Display

VGA text mode

Cursor control

Screen scrolling

Time

RTC hardware access

BCD time conversion

Dynamic taskbar clock

UI Features

Taskbar with real-time clock

Command prompt with username and directory

Scrollable terminal

Colored VGA text output

Example prompt:

root@vector(root) >
dev@vector(docs) >
🛠️ Building and Running
Requirements

NASM

QEMU

Windows PowerShell (for build script)

Build Script (PowerShell)
nasm -f bin boot.asm -o boot.bin
nasm -f bin kernel.asm -o kernel.bin

if (!(Test-Path "vector_os.img")) {
    echo "Creating new 1MB disk image..."
    fsutil file createnew vector_os.img 1048576
}

cmd /c "copy /b boot.bin + kernel.bin system_temp.bin"

$fileStream = [System.IO.File]::OpenWrite("$PWD\vector_os.img")
$bytes = [System.IO.File]::ReadAllBytes("$PWD\system_temp.bin")
$fileStream.Write($bytes, 0, $bytes.Length)
$fileStream.Close()

del boot.bin
del kernel.bin
del system_temp.bin

qemu-system-i386 -hda vector_os.img
📁 Project Structure
boot.asm            Bootloader (16-bit)
kernel.asm          Main OS kernel
disk_driver.asm     ATA disk driver
vector.asm          Vector script runtime
veccompiler.asm     Vector compiler
build.ps1           Build script
🔧 Memory Layout
0x7C00   BIOS bootloader load address
0x1000   Kernel load address
0x90000  Stack
0xB8000  VGA text buffer
🎯 Project Goals

Vector OS was built to explore:

x86 architecture

protected mode

interrupt handling

filesystem implementation

shell design

scripting engines

The goal was to create a small but functional operating system entirely in assembly.

🐛 Known Limitations

Max ~320 filesystem entries

File size limited to one sector (512 bytes)

No memory protection

Single-task execution model

Minimal error recovery

🔮 Possible Future Improvements

multitasking

larger files

memory management

GUI

networking

Built with NASM, QEMU, and pure x86 assembly

Development time: ~4–5 months
Lines of code: ~2500+ assembly lines
