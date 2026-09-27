# Lab Ideas — Linux Forensics

Not part of the syllabus. A scratch list of hands-on lab exercises to draw
from. Each lab should run in a disposable VM or an isolated lab network, on
data the instructor prepared. All work is analysis and detection from the
investigator's chair, not building live tooling for use outside the lab.

---

## 1. LKM implant detection

**Idea:** Give students a memory image (and/or a live VM snapshot) from a host
whose kernel has an out-of-tree loadable kernel module hiding a process, a
port, or a file. Students find it.

**What they practice**
- Comparing the in-memory module list against the on-disk expectation.
- Spotting hooked syscalls / tampered function pointers (e.g. Volatility 3
  `linux.check_syscall`, `linux.check_afinfo`, `linux.hidden_modules`).
- Cross-checking `/proc`, `ps`, and `netstat` output against ground truth
  gathered out-of-band, so hidden objects stand out as discrepancies.
- Reasoning about persistence: where a module would be loaded from at boot.

**Setup notes:** the instructor prepares the compromised image ahead of time;
students receive only the artifact to examine. Deliverable is a short findings
write-up naming the hidden objects and the evidence for each.

---

## 2. Fuzzy hashing for similarity

**Idea:** Students receive a bag of files (benign look-alikes, a few known-bad
samples, and variants of the bad ones) and must cluster them by similarity
rather than exact hash.

**What they practice**
- Why SHA-256 fails here: one flipped byte gives a completely different digest.
- `ssdeep` (context-triggered piecewise hashing) and `sdhash` to score how
  close two files are.
- Building a similarity matrix and clustering; explaining false positives.
- Comparing fuzzy hashing to `tlsh` and discussing when each is appropriate.

**Deliverable:** a ranked list of which unknown files are variants of which
known sample, with similarity scores and a paragraph on the limits of the
method.

---

## 3. Timeline reconstruction from a disk image

Build a super-timeline from a prepared disk image using `log2timeline`/`plaso`,
then answer specific questions: when was a user created, when did a file first
appear, what ran at a given minute. Teaches MACB timestamps, timezone pitfalls,
and timestamp forgery detection.

## 4. File carving from a corrupted image

Hand out a disk image with a damaged filesystem. Students recover files with
`scalpel`/`foremost`/`photorec` using header-footer signatures, then validate
what they recovered. Reinforces the day-3 carving module.

## 5. Log correlation across sources

Provide `auth.log`, `journald` output, shell history, and `wtmp`/`btmp` from
one incident. Students correlate a login, a privilege escalation, and a data
access into a single narrative, and note where an attacker tried to erase
tracks.

## 6. Deleted-file recovery on ext4

Prepare an ext4 image with recently deleted files. Students use the journal and
inode analysis (`extundelete`, `debugfs`) to recover content and timestamps,
and discuss why deletion is not erasure.

## 7. Memory forensics for a running process

From a RAM capture, students extract a process's memory, pull out strings,
open network sockets, and loaded libraries, and reconstruct what the process
was doing. Volatility 3 `linux.pslist`, `linux.bash`, `linux.proc.Maps`.

## 8. Persistence-mechanism hunt

Give students a mounted image and have them enumerate every place Linux
persistence can live: cron, systemd units and timers, `.bashrc`/profile,
`ld.so.preload`, init scripts, `authorized_keys`. Produce a checklist and flag
the one planted entry.

## 9. Hash-set triage

Students build a known-good hash set from a clean reference system, then diff a
suspect system's files against it to shrink the review set to only what
changed. Teaches the NSRL concept and why hash allow-listing scales.

## 10. Chain-of-custody and reporting

A non-technical capstone: given the findings from an earlier lab, students
write the incident report and a mock chain-of-custody form, then defend their
conclusions in a short viva. Ties into the day-6 report-writing modules.

---

## Backlog / smaller ideas

- **USB and mount history** from `udev` logs and `lsblk` metadata in an image.
- **Browser artifact recovery** (history, cache, downloads) from a user home.
- **Steganography spotting** with entropy analysis on a set of images.
- **Encrypted-volume triage:** identify LUKS containers and reason about what
  can and cannot be done without the key.
- **Anti-forensics awareness:** show timestamp-stomping and log wiping in a
  prepared image and have students detect the traces they leave behind.
