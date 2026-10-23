# Modification Map: external/eigen3

## Source: BlissOS android-x86 reference (external/eigen3)
- BlissOS reference tree: /mnt/aosp/android-x86/external/eigen3 @ 9096d83fcb88a2bb80a37417d389972c3c766226
  (mirror of android-generic/eigen; primary BlissOS mod commit: f6e4c6a "Rename to libeigen3")
- KhillOS port commits (this repo, branch khillos-17):
  - 4b377b8f3 (initial KhillOS port - basic Android.bp)
  - 5b1c88c7c (AOSP 17 license splitting + extended Android.bp)
  - 1c6c59c6e (AOSP 17 compliance metadata + NEON [[maybe_unused]] fix)
- Upstream base: libeigen/eigen 3.4.1 (tag d71c30c)

## Modifications Applied to UPSTREAM/external_eigen3 (khillos-17 branch)

### 1. Android.bp Build System (EXTENDED from BlissOS in 2 commits)
**Commit 4b377b8f3 - Basic port:**
- `cc_library_headers` libeigen3 with vendor_available, host_supported
- `cc_library_static` libF77blas-eigen3 and libF77blasV8-eigen3
- `-DEIGEN_MPL2_ONLY` flag
- APEX availability for neuralnetworks

**Commit 5b1c88c7c - AOSP 17 license splitting (reference: external/freetype):**
- Split licenses per AOSP 17 multi-license pattern:
  * `external_eigen3_license`: MPL-2.0 + Apache-2.0 (BFloat16.h) + BSD-3-Clause (MINPACK) + MINPACK
  * `external_eigen3_bench_license`: GPL-2.0-or-later (bench/ only, private visibility)
  * `external_eigen3_restricted_code_license`: LGPL-2.1-only (2 IterativeSolver headers, private)
- Added filegroups for bench and restricted code (visibility:private)
- Fixed BLAS source paths: `blas/*.c` + `blas/f2c/*.c` (not BLAS/F77BLAS/)
- Added full partition availability (product, ramdisk, vendor_ramdisk, recovery)
- Added native_bridge_supported, target (linux_bionic + windows)
- Added min_sdk_version/sdk_version/apex_available
- Added visibility: //system/libbase
- libF77blasV8-eigen3 keeps sdk_version: 9 (legacy RenderScript support per BlissOS)

### 2. NEON Source Patch (NEW - BlissOS fix)
**File:** `Eigen/src/Core/arch/NEON/GeneralBlockPanelKernel.h` line 27
- Added `[[maybe_unused]]` attribute to unused `tmp` parameter
- Fixes Clang warning for unused parameter in NEON template

### 3. AOSP 17 Compliance Metadata Files (NEW)
**Files:** `METADATA`, `NOTICE`, `MODULE_LICENSE_MPL2`, `OWNERS`
- METADATA: Package metadata per AOSP external_updater format
- NOTICE: Short license notice covering MPL-2.0, Apache-2.0, BSD-3-Clause, LGPL-2.1, GPL-2.0+
- MODULE_LICENSE_MPL2: Primary SPDX identifier for Soong license module
- OWNERS: Standard AOSP OWNERS file

### 4. SPDX Headers - NOT REQUIRED (and litter removed 2026-10-23)
Eigen is a header-only template library with multiple licenses handled via license modules in Android.bp. Per-file SPDX headers not standard for this project structure.
On 2026-10-23, 527 uncommitted `/* SPDX-License-Identifier: MPL-2.0 */` header insertions (add_spdx.py-style script litter, never part of any commit) plus an untracked generated `LICENSE.txt` were found in the working tree and removed via `git checkout -- .` + `rm -f LICENSE.txt`. These were never present in upstream 3.4.1, the AOSP 17 tree, or any khillos-17 commit.

## Summary
- **Total files changed vs upstream 3.4.1 (d71c30c47):** 8 = 6 port files (Android.bp new, NEON fix, METADATA, MODULE_LICENSE_MPL2, NOTICE, OWNERS) + 2 evidence files (this file, nothing-else-disturbed.md)
- **Source files unchanged except:** 1 NEON fix ([[maybe_unused]])
- **Logic/behavior changes:** None (fix only)
- **Build system changes:** Extended Android.bp with AOSP 17 multi-license pattern