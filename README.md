# Digital Forensic Disk Image Analysis

## Overview

This project presents a comprehensive digital forensic investigation conducted on a Windows disk image as part of a capstone Digital Forensics and Incident Analysis project.

The objective was to identify, recover, and analyze digital evidence related to a simulated criminal investigation involving the suspected murder of Mrs. Clara Benson. The investigation focused on artifact recovery, deleted file analysis, encoded communications, steganographic techniques, and timeline reconstruction to uncover evidence of coordinated activity and deliberate concealment.

## Objectives

- Recover and analyze deleted artifacts from a forensic disk image.

- Identify evidence of user activity and intent.

- Examine encoded and encrypted communications.

- Detect potential steganographic concealment techniques.

- Correlate recovered evidence to reconstruct a timeline of events.

- Produce a forensic report documenting findings and conclusions.

## Skills Demonstrated

- Digital Forensics

- Incident Investigation

- Evidence Acquisition

- Artifact Recovery

- Timeline Analysis

- File Carving

- Steganography Detection

- Cryptographic Analysis

- Security Reporting

- Documentation and Chain of Custody

- Analytical Problem Solving

 
## Tools Used

- Autopsy

- The Sleuth Kit

- Binwalk

- Hexdump

- Strings

- Base64 Decoder

- Zlib Decompression Utilities

- Manual Caesar Cipher Analysis

## Investigation Methodology

### Evidence Acquisition

- Examined a forensic disk image in a controlled environment.

- Verified evidence integrity using hash values.

- Maintained forensic handling procedures throughout the investigation.

 
### Artifact Recovery

- Analyzed NTFS file system structures.

- Examined Recycle Bin artifacts linked to the primary user account.

- Recovered deleted files and metadata.

### Data Analysis

- Performed file carving and content extraction.

- Conducted encoding and encryption analysis.

- Investigated hidden and embedded data.

- Examined image files for steganographic indicators.

### Correlation

- Cross-referenced recovered artifacts.

- Analyzed timestamps and user activity.

- Reconstructed investigative timelines.

## Key Findings

### Deleted Artifact Recovery

Recovered multiple deleted files from the user's Recycle Bin, including:

- Murder_plan.docx

- Hotel_surveillance.jpg

- Encrypted_letter.docx

- Confession.txt

- Audio_recording_transcript.txt

- Group_pic.bmp

These artifacts provided valuable evidence regarding user activity and potential criminal intent.

### Encrypted Communication Analysis

Identified a Caesar Cipher encrypted document that, when decoded, revealed a structured operational plan including:

- Unauthorized hotel room access

- Camera surveillance avoidance

- Concealment of evidence

- Planned escape route

### Encoded Message Discovery

Recovered a hidden Base64-encoded message that contradicted an apparent confession and suggested involvement of additional parties.

### Steganographic Investigation

Analysis of image artifacts revealed:

- Embedded Zlib-compressed data

- Indicators of possible data concealment

- Evidence of intentional obfuscation techniques

### Activity Reconstruction

Correlated recovered artifacts and timestamps to reconstruct a timeline of events and identify patterns consistent with coordinated activity.

## Challenges Encountered

- Incomplete recovery of supporting audio evidence.

- Presence of corrupted or partially recoverable artifacts.

- Multiple layers of encoding and concealment.

- Limited plaintext recovery from embedded compressed data.

These challenges reflected realistic forensic scenarios involving anti-forensic and evidence-hiding techniques.

## Project Outcomes

The investigation demonstrated:

- Successful recovery of deleted evidence.

- Identification of concealed communications.

- Recognition of potential steganographic behavior.

- Reconstruction of user activity and event timelines.

- Development of professional forensic reporting skills.

 
The findings indicated intentional efforts to conceal evidence and suggested coordinated activity involving multiple participants.

## Repository Structure
```text
Digital-Forensic-Disk-Image-Analysis/
│
├── README.md
├── Report/
│ └── Digital_Forensic_Investigation_Report.pdf
├── Screenshots/
│ ├── RecycleBin_Analysis.png
│ ├── Artifact_Recovery.png
│ ├── Binwalk_Output.png
│ └── Timeline_Analysis.png
│
└── Evidence/
└── Sanitized_Artifacts/
```
## Lessons Learned 
This project strengthened my practical understanding of: 
- Digital forensic workflows
- Evidence preservation
- Artifact analysis
- Hidden data detection
- Timeline reconstruction
- Professional forensic reporting
- It also reinforced the importance of critical thinking when analyzing encoded communications, deleted artifacts, and potential anti-forensic techniques.
---
  ## Disclaimer 
  This project was conducted in a controlled educational environment for learning and professional development purposes. All evidence and scenarios were part of a forensic training exercise.
