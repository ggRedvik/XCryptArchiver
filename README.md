# XCryptArchiver
Windows archiver with AES-256 encryption, custom .xca format and Explorer context menu integration

# XCrypt Archiver

A modern file archiver for Windows with AES-256 encryption, a custom `.xca` format, and a clean dark UI built with PyQt5.

![Python](https://img.shields.io/badge/Python-3.8%2B-blue)
![Platform](https://img.shields.io/badge/Platform-Windows-lightgrey)
![License](https://img.shields.io/badge/License-MIT-green)

---

## Features

- **Custom `.xca` format** — own binary format with per-file compression and optional AES-256-CBC encryption
- **Strong key derivation** — PBKDF2-SHA256 with 600 000 iterations (OWASP 2023 recommendation)
- **Multiple formats** — open and extract `.xca`, `.zip`, `.7z`, `.tar`, `.tar.gz`, `.tar.bz2`, `.tar.xz`
- **Explorer integration** — right-click context menu (like WinRAR) with subcommands: *Add to archive*, *Quick pack as .xca*, *Open*
- **File associations** — registers icons for `.xca`, `.zip`, `.7z`, `.tar`, `.gz`, `.bz2` in Windows Explorer
- **Drag & drop** — drop files onto the window to create an archive instantly
- **Archive integrity check** — CRC-32 verification for every file entry
- **Windows installer** — Inno Setup 6 script included (`installer.iss`)

## Screenshots

<img width="1064" height="727" alt="image" src="https://github.com/user-attachments/assets/3d7af4ba-da32-43d5-9597-a22bc0f1d624" />


## Requirements

```
Python 3.8+
PyQt5
cryptography
py7zr
Pillow        # only for building the icon
pyinstaller   # only for building the EXE
```

Install dependencies:

```bash
pip install PyQt5 cryptography py7zr
```

## Usage

**Run from source:**

```bash
python архиватор.py
```

**Open an archive from CLI:**

```bash
python архиватор.py archive.xca
```

**Add files via CLI (context menu calls this):**

```bash
python архиватор.py --add file1.txt file2.txt
python архиватор.py --add-quick folder\
```

## Build EXE + Installer

1. Install [Inno Setup 6](https://jrsoftware.org/isdl.php)
2. Run:

```bat
build.bat
```

This will:
- Install all Python dependencies
- Generate `logo.ico` / `logo.png`
- Build `dist\XCryptArchiver.exe` with PyInstaller
- Compile `dist\XCryptArchiver_Setup.exe` with Inno Setup

## XCA Format

The `.xca` format is a simple, open binary format:

```
[magic: XCA\x01]
[header: flags(2) nfiles(4) salt(16)]
  per file:
    [pathlen(2)] [path(utf-8)] [origsize(8)] [compsize(8)] [crc32(4)] [mtime(8)] [method(1)]
    [data: optionally AES-256-CBC encrypted, then LZMA/Deflate/BZIP2/Store compressed]
[footer: XCA_END\x00]
```

Encryption: `key = PBKDF2HMAC(SHA-256, password, salt, iterations=600_000, length=32)`

## Security Notes

- Passwords are never stored — only a 16-byte random salt is saved in the archive header
- Path traversal protection: extraction rejects any entry whose resolved path escapes the destination directory
- Without a password the encrypted payload cannot be decompressed (no silent fallback)

## License

MIT
