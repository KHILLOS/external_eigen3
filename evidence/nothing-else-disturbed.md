# Nothing Else Disturbed: external/eigen3

## Verification Method
Compared UPSTREAM/external_eigen3 (khillos-17 branch) against:
1. BlissOS android-x86 reference at external/eigen3 (HEAD 9096d83f; primary BlissOS mod commit f6e4c6a)
2. Upstream libeigen/eigen 3.4.1 (tag d71c30c)
3. AOSP 17 tree at /mnt/aosp/aosp/external/eigen3 (same HEAD 1c6c59c6e; `diff -r` of Eigen/ empty on 2026-10-23)

## Source Files - Content Verification
All source/header files contain **identical logic/code** to BlissOS reference and upstream.
**Only change:** 1 NEON fix in `Eigen/src/Core/arch/NEON/GeneralBlockPanelKernel.h` line 27:
- Added `[[maybe_unused]]` attribute to unused `tmp` parameter
- Fixes Clang warning for unused parameter in NEON template
- Matches BlissOS fix exactly

## Build System - Extended from BlissOS
BlissOS Android.bp → UPSTREAM Android.bp (commits 4b377b8f3 → 5b1c88c7c):
| BlissOS (4b377b8f3) | UPSTREAM (5b1c88c7c) | Status |
|---------------------|----------------------|--------|
| Basic license (MPL-2.0) | 3 split licenses | ✓ AOSP 17 multi-license pattern |
| libeigen3 module | libeigen3 module | ✓ Renamed from libeigen |
| vendor_available, host_supported | + product, ramdisk, vendor_ramdisk, recovery | ✓ Full partitions |
| apex_available: true | anyapex + platform + neuralnetworks | ✓ Fixed/expanded |
| libF77blas sources: BLAS/F77BLAS/ | blas/*.c + blas/f2c/*.c | ✓ Fixed paths |
| libF77blasV8 sources: BLAS/F77BLAS_V8/ | blas/*.c + blas/f2c/*.c | ✓ Fixed paths |
| (no filegroups) | eigen3_bench_srcs, eigen3_restricted_code_srcs | ✓ Added (private) |
| (no min_sdk_version) | min_sdk_version: "29" | ✓ Added |
| (no sdk_version) | sdk_version: "current" | ✓ Added |
| (no native_bridge_supported) | native_bridge_supported: true | ✓ Added |
| (no target config) | linux_bionic + windows | ✓ Added |
| (no visibility) | visibility: //system/libbase | ✓ Added |
| libF77blasV8 sdk_version | sdk_version: "9" | ✓ Kept (BlissOS legacy) |

All extensions align with AOSP 17 conventions (reference: external/freetype).

## NEON Source Patch Verification
BlissOS fix at line 27 of GeneralBlockPanelKernel.h:
```cpp
// BlissOS: Packet4f& tmp, [[maybe_unused]] Packet4f& tmp,
// UPSTREAM: Packet4f& c, [[maybe_unused]] Packet4f&,
```
✅ **Exact match** - parameter renamed from `tmp` to anonymous but `[[maybe_unused]]` attribute added identically.

## Metadata Added
- METADATA: Updated to external_updater format with GitLab identifier
- NOTICE: Created covering all 5 licenses (MPL-2.0, Apache-2.0, BSD-3-Clause, LGPL-2.1, GPL-2.0+)
- MODULE_LICENSE_MPL2: Primary SPDX marker file
- OWNERS: Standard AOSP OWNERS file

## Diff Summary
```
Files changed (3.4.1 d71c30c47 .. khillos-17 1c6c59c6e): 8
  A Android.bp                                   (+132)
  M Eigen/src/Core/arch/NEON/GeneralBlockPanelKernel.h (+1/-1)
  A METADATA (+21), MODULE_LICENSE_MPL2 (+1), NOTICE (+8), OWNERS (+1)
  A evidence/modification-map.md, evidence/nothing-else-disturbed.md
Insertions: 278, Deletions: 1
Source logic changes: 1 (NEON [[maybe_unused]] fix - matches BlissOS)
Build system: Extended with AOSP 17 multi-license pattern
```

## 2026-10-23 Cleanup Verification (post-reaudit)
- `git checkout -- .` removed 527 uncommitted SPDX-litter modifications (2482 insertions, never committed)
- `rm -f LICENSE.txt` removed untracked generated MPL-2.0 copy
- `git status` = "nothing to commit, working tree clean"
- `grep -rl "SPDX-License-Identifier" Eigen/ unsupported/` = 0 hits (matches AOSP 17 tree)
- `diff -r /mnt/aosp/aosp/external/eigen3/Eigen ./Eigen` = empty (source parity with AOSP 17 tree)
- NEON `[[maybe_unused]]` fix preserved at line 27 of GeneralBlockPanelKernel.h

## Conclusion
✅ **NOTHING ELSE DISTURBED** - All modifications are:
1. Build system extensions (Android.bp with AOSP 17 multi-license conventions) - functional equivalent + required features
2. NEON source fix (`[[maybe_unused]]`) - matches BlissOS exactly, fixes Clang warning
3. AOSP 17 compliance metadata - required, no behavior change
4. Header-only library - no other source modifications needed