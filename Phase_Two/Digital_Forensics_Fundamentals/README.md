# Digital Forensics Fundamentals Laboratory

![Field](https://img.shields.io/badge/Field-Digital%20Forensics-blue)
![Focus](https://img.shields.io/badge/Focus-DFIR-darkred)
![Python](https://img.shields.io/badge/Python-Scripting-yellow)
![Linux](https://img.shields.io/badge/Linux-Kali%20Linux-black)
![Windows](https://img.shields.io/badge/Windows-Command%20Line-0078D4)
![Status](https://img.shields.io/badge/Status-Completed-brightgreen)

## Overview

This repository documents a practical laboratory project completed as part of **DFIA101: Digital Forensics Fundamentals**.

The project introduces essential computing concepts used in digital forensics and incident response, including number-system conversion, disk-storage calculations, timestamp interpretation, memory addressing, endianness, ASCII and UTF-8 encoding, and command-line operations.

The laboratory combines manual calculations with practical verification using Python, Kali Linux, Bash, Linux command-line utilities, Windows Command Prompt, and batch scripting.

---

## Project Objectives

The objectives of this laboratory were to:

- Understand binary, decimal, and hexadecimal number systems.
- Perform number-system conversions manually and programmatically.
- Interpret ASCII and UTF-8 encoded data.
- Calculate disk, partition, and memory storage sizes.
- Examine MBR and GPT partitioning concepts.
- Convert Unix, Mac Absolute, and WebKit timestamps.
- Understand byte order and endianness.
- Perform memory-address calculations.
- Use Windows and Linux command-line utilities.
- Create and execute basic Bash and Windows batch scripts.
- Connect foundational computing concepts to digital forensic investigations.

---

## Topics Covered

### 1. Number-System Conversion

The laboratory covers manual and automated conversion between:

- Decimal and binary
- Decimal and hexadecimal
- Binary and decimal
- Binary and hexadecimal
- Hexadecimal and decimal
- Hexadecimal and binary

Example:

```text
Decimal:      197
Binary:       11000101
Hexadecimal:  C5
```

Python verification:

```python
decimal_value = 197

binary_value = bin(decimal_value)[2:]
hexadecimal_value = hex(decimal_value)[2:].upper()

print(f"Decimal: {decimal_value}")
print(f"Binary: {binary_value}")
print(f"Hexadecimal: {hexadecimal_value}")
```

Linux verification:

```bash
echo "obase=2; 197" | bc
echo "obase=16; 197" | bc
```

---

### 2. ASCII and UTF-8 Encoding

The project examines how characters are represented using decimal and hexadecimal values.

Examples:

```text
Character:   $
Decimal:     36
Hexadecimal: 24
```

```text
Hexadecimal 4E represents the character N.
Decimal 97 represents the character a.
```

Python example:

```python
character = "$"

decimal_value = ord(character)
hexadecimal_value = hex(decimal_value)

print(f"Character: {character}")
print(f"Decimal value: {decimal_value}")
print(f"Hexadecimal value: {hexadecimal_value}")

print(chr(0x4E))
print(chr(97))
```

The project also compares ASCII and UTF-8:

- Standard ASCII represents 128 characters.
- ASCII primarily supports English letters, numbers, punctuation marks, and control characters.
- UTF-8 supports the complete Unicode character set.
- UTF-8 can represent international writing systems, technical symbols, and emojis.
- UTF-8 uses between one and four bytes per character.
- ASCII characters maintain the same byte values in UTF-8.

Understanding character encoding is important during forensic analysis because incorrectly decoded data may conceal or distort filenames, messages, logs, web artifacts, or extracted strings.

---

### 3. Binary Arithmetic

The laboratory includes binary addition and decimal verification.

Example:

```text
  101101
+ 011011
--------
 1001000
```

Decimal verification:

```text
101101₂ = 45₁₀
011011₂ = 27₁₀

45 + 27 = 72

72₁₀ = 1001000₂
```

---

### 4. Disk-Storage Calculations

The project explores disk-capacity calculations using:

- Cylinders
- Heads
- Sectors
- Bytes per sector
- Logical Block Addressing
- Binary storage units

The general CHS calculation is:

```text
Total sectors = Cylinders × Heads × Sectors per track
```

Disk size in bytes is calculated as:

```text
Disk size = Total sectors × Bytes per sector
```

Storage values are subsequently converted into:

- Kilobytes
- Megabytes
- Gigabytes
- Terabytes
- Petabytes
- Exabytes
- Zettabytes

---

### 5. MBR Partition Analysis

The Master Boot Record section demonstrates how hexadecimal partition values can be interpreted.

Topics include:

- Converting a starting LBA from hexadecimal to decimal.
- Converting partition sector counts to decimal.
- Calculating partition size from the number of sectors.
- Converting partition capacity into larger storage units.
- Understanding the disk-size limitation associated with MBR.

General partition-size formula:

```text
Partition size in bytes = Number of sectors × Bytes per sector
```

MBR traditionally uses 32-bit sector-addressing fields. With 512-byte sectors, this limits the addressable disk space to approximately 2 TiB.

---

### 6. GPT Partition Analysis

The laboratory compares the GUID Partition Table with the Master Boot Record.

GPT provides several advantages:

- Support for disks larger than the traditional 2 TiB MBR limit.
- Support for more partitions.
- Backup partition-table information.
- Improved integrity checking through CRC values.
- Better compatibility with modern UEFI-based systems.

Example partition calculation:

```text
Number of sectors: 83,886,080
Bytes per sector:  512

Partition size:
83,886,080 × 512 = 42,949,672,960 bytes
```

Using binary conversion units:

```text
42,949,672,960 bytes = 40 GiB
```

---

### 7. Timestamp Conversion

Timestamp interpretation is essential for creating accurate forensic timelines. The laboratory covers three major timestamp formats.

#### Unix Epoch Time

Unix time counts seconds from:

```text
1970-01-01 00:00:00 UTC
```

Linux example:

```bash
date -d @1678886400 --utc
```

Python example:

```python
from datetime import datetime, timezone

timestamp = 1678886400
converted_time = datetime.fromtimestamp(timestamp, tz=timezone.utc)

print(converted_time)
```

#### Mac Absolute Time

Mac Absolute Time counts seconds from:

```text
2001-01-01 00:00:00 UTC
```

The offset between the Unix and Mac Absolute epochs is:

```text
978,307,200 seconds
```

Conversion formula:

```text
Unix timestamp = Mac Absolute Time + 978,307,200
```

#### WebKit Time

WebKit timestamps are commonly stored as microseconds since:

```text
1601-01-01 00:00:00 UTC
```

The offset between the WebKit and Unix epochs is:

```text
11,644,473,600 seconds
```

Conversion formula:

```text
Unix timestamp =
(WebKit timestamp ÷ 1,000,000) - 11,644,473,600
```

Understanding WebKit time is particularly useful when examining Chromium-based browser artifacts such as:

- Browsing history
- Cookies
- Downloads
- Login records
- Cache information
- Visited URLs

---

### 8. Memory and Endianness

The laboratory explains how multi-byte values are arranged in memory.

Given the value:

```text
0x12345678
```

Little-endian byte order:

```text
78 56 34 12
```

Big-endian byte order:

```text
12 34 56 78
```

Understanding endianness is critical when interpreting:

- Memory dumps
- Disk images
- File headers
- Network data
- Timestamps
- Memory addresses
- File offsets
- Multi-byte integers

Incorrect byte-order interpretation can result in invalid addresses, inaccurate timestamps, incorrect file sizes, or improperly reconstructed evidence.

---

### 9. Memory-Address Calculation

The project includes calculations for determining the final address occupied by a data structure.

Formula:

```text
Ending address = Starting address + Size - 1
```

Example:

```text
Starting address: 0x00400000
Structure size:   256 bytes
Ending address:   0x004000FF
```

---

### 10. Windows Command-Line Fundamentals

The Windows command-line exercises include:

- Navigating directories.
- Creating folders and files.
- Displaying file contents.
- Copying, renaming, and deleting files.
- Gathering system information.
- Examining network configuration.
- Identifying MAC addresses.
- Listing disks and partitions.
- Creating and executing batch scripts.
- Temporarily modifying the system PATH.

Commands used include:

```cmd
cd
mkdir
echo
type
copy
ren
del
systeminfo
findstr
ipconfig
tasklist
hostname
diskpart
wmic
```

---

### 11. Windows Batch Scripting

A basic batch script was created to collect system information.

Example:

```batch
@echo off
cls

echo =========================================
echo      SYSTEM INFORMATION COLLECTION
echo =========================================

echo.
echo Current date:
date /t

echo.
echo Current time:
time /t

echo.
echo Hostname:
hostname

echo.
echo IP configuration:
ipconfig

echo.
echo Running processes:
tasklist

pause
```

The script demonstrates how repetitive system-information collection tasks can be automated using Windows batch scripting.

---

### 12. Bash Scripting

A Bash script was used to retrieve the current Unix timestamp and convert it to a human-readable date.

```bash
#!/bin/bash

epoch_time=$(date +%s)
human_readable=$(date -u -d @"$epoch_time" "+%d-%m-%Y %H:%M:%S UTC")

echo "Current Epoch Time: $epoch_time"
echo "Current Human-Readable Time: $human_readable"
```

---

## Tools and Technologies

The following tools were used during the laboratory:

- Python
- Kali Linux
- Bash
- GNU `bc`
- Linux `date`
- Linux `printf`
- Windows Command Prompt
- Windows PowerShell
- DiskPart
- WMIC
- Batch scripting
- Basic calculator
- ASCII reference table

---

## Suggested Repository Structure

```text
digital-forensics-fundamentals-lab/
│
├── README.md
├── LICENSE
├── DISCLAIMER.md
│
├── documentation/
│   └── DFIA101-Lab-Report.pdf
│
├── scripts/
│   ├── timestamp_converter.py
│   ├── number_converter.py
│   ├── epoch_time.sh
│   └── sys_info.bat
│
├── screenshots/
│   ├── number-conversions/
│   ├── storage-analysis/
│   ├── timestamp-conversions/
│   ├── windows-cmd/
│   └── batch-script/
│
└── results/
    └── laboratory-findings.md
```

You can modify this structure to match the files currently available in your repository.

---

## Getting Started

### Clone the Repository

```bash
git clone https://github.com/YOUR-USERNAME/YOUR-REPOSITORY.git
cd YOUR-REPOSITORY
```

Replace `YOUR-USERNAME` and `YOUR-REPOSITORY` with your actual GitHub username and repository name.

### Make the Bash Script Executable

```bash
chmod +x scripts/epoch_time.sh
```

Run it with:

```bash
./scripts/epoch_time.sh
```

### Run the Python Scripts

```bash
python3 scripts/number_converter.py
```

```bash
python3 scripts/timestamp_converter.py
```

### Run the Batch Script

Open Windows Command Prompt and execute:

```cmd
scripts\sys_info.bat
```

Some system-information and disk-management commands may require an administrator Command Prompt.

---

## Key Learning Outcomes

By completing this laboratory, I developed a practical understanding of:

- Representing and converting digital data across number systems.
- Using Python and command-line utilities for verification.
- Calculating disk and partition capacities.
- Distinguishing MBR and GPT partitioning structures.
- Interpreting timestamps from different operating systems and applications.
- Understanding endianness and its effect on digital evidence.
- Performing basic memory-address calculations.
- Automating system-information collection.
- Applying command-line skills in digital forensic investigations.
- Documenting practical laboratory activities and results.

---

## Digital Forensics Relevance

The concepts demonstrated in this repository provide a foundation for more advanced digital forensic activities, including:

- Disk-image examination
- Memory forensics
- File-system analysis
- Browser-artifact analysis
- Timeline reconstruction
- Metadata interpretation
- Data carving
- Incident investigation
- Evidence validation

Accurate interpretation of number systems, timestamps, character encodings, byte order, storage structures, and memory addresses is necessary for maintaining the reliability of forensic findings.

---

## Ethical Use and Disclaimer

This repository is intended strictly for educational, academic, and authorized digital forensic research.

The commands, scripts, examples, and techniques documented here should only be used on systems, storage devices, accounts, and environments that you own or have explicit permission to examine.

The repository owner is not responsible for unauthorized, illegal, unethical, or harmful use of the information provided. Users are responsible for complying with applicable laws, institutional policies, professional standards, and rules governing digital evidence.

Screenshots or laboratory outputs included in this repository should be reviewed before publication to ensure that they do not expose usernames, IP addresses, MAC addresses, device identifiers, registration details, private file paths, or other sensitive information.

---

## Author

**Udam Akume Gabriel**  
Digital Forensics and Incident Response Practitioner

- **Email:** [udamgabrielakume99@gmail.com](mailto:udamgabrielakume99@gmail.com)
- **LinkedIn:** [Udam Akume Gabriel](https://www.linkedin.com/in/udam-akume-gabriel-09a8bb334)

---

## Contributions

This repository primarily documents an academic laboratory exercise. Constructive suggestions that improve the accuracy, clarity, security, or organization of the project are welcome.

To contribute:

1. Fork the repository.
2. Create a new branch.
3. Make the proposed improvements.
4. Commit the changes.
5. Open a pull request with a clear explanation.

---

## License

This project is provided for educational and portfolio purposes.

If a license file is added, the reuse, modification, and distribution of the repository will be governed by the terms stated in that license.

---

## Acknowledgements

Special acknowledgement goes to the course instructor and the academic program responsible for providing the laboratory exercises and guidance.

---

## Project Status

**Completed**

Future improvements may include:

- Adding reusable Python conversion utilities.
- Improving timestamp-validation scripts.
- Organizing screenshots by exercise.
- Adding sample forensic datasets.
- Introducing automated calculation tests.
- Expanding the project to include file-system and browser-artifact analysis.

---

## Support

If you find this repository useful:

- Star the repository.
- Share it with other digital forensics students.
- Submit constructive feedback.
- Connect with me on LinkedIn.

> Accurate digital forensic analysis begins with understanding how computers represent, store, and interpret data.
