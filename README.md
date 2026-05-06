# Yocto Project Cheatsheet 

[![License: GPL v2](https://img.shields.io/badge/License-GPL%20v2-blue.svg)](https://www.gnu.org/licenses/old-licenses/gpl-2.0.en.html)
[![Yocto Compatible](https://img.shields.io/badge/Yocto-3.1+-green.svg)](https://www.yoctoproject.org)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](http://makeapullrequest.com)
[![Documentation](https://img.shields.io/badge/docs-reference-blue.svg)](https://docs.yoctoproject.org)

> **One-stop reference** for Yocto Project: definitions, syntax, workflows, block diagrams, debugging, and interview prep.

---

## Table of Contents
1. [What is Yocto? (Definitions)](#what-is-yocto-definitions)
2. [Why Use Yocto?](#why-use-yocto)
3. [Core Components](#core-components)
4. [Block Diagram](#block-diagram)
5. [Working Flow](#working-flow)
6. [Syntax & Key Files](#syntax--key-files)
7. [How to Check / Validate Your Setup](#how-to-check--validate-your-setup)
8. [Common Recipes & Variables](#common-recipes--variables)
9. [Debugging & Troubleshooting](#debugging--troubleshooting)
10. [Interview Preparation](#interview-preparation)
11. [Cheatsheet Quick Reference](#cheatsheet-quick-reference)

---

## What is Yocto? (Definitions)

| Term | Definition |
|------|-------------|
| **Yocto Project** | An open-source collaboration project that provides templates, tools, and methods to create custom Linux-based systems for embedded products. |
| **OpenEmbedded Core (OE-Core)** | The shared metadata (recipes, classes, configurations) used by Yocto. |
| **Metadata** | The collection of recipes, configuration files, and classes that tell BitBake what to build and how. |
| **BitBake** | The task scheduler and executor (similar to `make`) that parses metadata and runs builds. |
| **Recipe (`.bb`)** | A file that describes how to fetch, configure, compile, and install a piece of software. |
| **Layer (`.bbappend`)** | A collection of related recipes and configurations. Layers allow you to customize without modifying core metadata. |
| **Poky** | The reference distribution of Yocto, includes BitBake, OE-Core, and documentation. |
| **HOB** | Human-Oriented BitBake – a graphical interface for building images. |
| **SDK** | Software Development Kit – generated toolchain and libraries for cross-compiling applications. |

---

## Why Use Yocto?

| Challenge | How Yocto Solves It |
|-----------|----------------------|
| **Embedded fragmentation** | Provides a consistent build system across multiple architectures (ARM, x86, RISC-V, MIPS). |
| **Binary distribution bloat** | You build only what you need – no unused packages. |
| **Reproducibility** | BitBake uses checksums, shared state (sstate) cache, and strict versioning. |
| **Hardware variation** | Board Support Packages (BSPs) isolate hardware specifics into layers. |
| **Corporate compliance** | Generates license manifests, SPDX files, and source archives. |
| **Cross-compilation complexity** | Built-in cross-toolchain generation (e.g., `arm-poky-linux-gnueabi-gcc`). |

>  **Use Yocto when:** You need a production-grade, customizable, minimal Linux for embedded devices (IoT gateways, automotive, medical, industrial).

---

## Core Components

```
┌─────────────────────────────────────────────────────────────┐
│                      Your Custom Distribution               │
├─────────────────────────────────────────────────────────────┤
│   Layer A (BSP)   │   Layer B (Middleware)  │  Layer C (App) │
├─────────────────────────────────────────────────────────────┤
│                     OpenEmbedded Core (OE-Core)             │
├─────────────────────────────────────────────────────────────┤
│                          BitBake                            │
├─────────────────────────────────────────────────────────────┤
│                      Host Build System                      │
│                  (Ubuntu, Fedora, CentOS)                   │
└─────────────────────────────────────────────────────────────┘
```

---

## Block Diagram

```
                              ┌─────────────────┐
                              │   User Input     │
                              │ (bitbake core-image-minimal) │
                              └────────┬────────┘
                                       ▼
┌────────────────────────────────────────────────────────────────────┐
│                           BITBAKE PARSER                           │
│  • Parse .bb, .bbappend, .conf files   • Resolve dependencies      │
└────────────────────────────┬───────────────────────────────────────┘
                             ▼
┌────────────────────────────────────────────────────────────────────┐
│                        TASK GENERATOR                              │
│  (do_fetch → do_unpack → do_patch → do_configure → do_compile →    │
│   do_install → do_package → do_rootfs)                             │
└────────────────────────────┬───────────────────────────────────────┘
                             ▼
              ┌──────────────┴──────────────┐
              │      Shared State Cache      │
              │   (sstate-cache/)            │
              │   • Skip already built tasks │
              └──────────────┬──────────────┘
                             ▼
              ┌──────────────────────────────┐
              │      WORKDIR (tmp/work/)     │
              │  • Extracted source          │
              │  • Build artifacts           │
              └──────────────┬──────────────┘
                             ▼
              ┌──────────────────────────────┐
              │    DEPLOY DIR (tmp/deploy/)  │
              │  • Images ( .ext4, .sdcard ) │
              │  • Packages ( .ipk, .rpm )   │
              │  • Toolchain SDK             │
              └──────────────────────────────┘
```

---

## Working Flow

### Step-by-Step Build Process

```bash
# 1. Initialize the build environment
source oe-init-build-env build

# 2. Configure local.conf and bblayers.conf
vim conf/local.conf          # Set MACHINE, DL_DIR, SSTATE_DIR, etc.
vim conf/bblayers.conf       # Add your custom layers

# 3. Build an image
bitbake core-image-minimal

# 4. Run in emulator (QEMU)
runqemu qemux86-64

# 5. Generate SDK for application developers
bitbake -c populate_sdk core-image-minimal

# 6. Clean specific recipe
bitbake -c clean busybox

# 7. Rebuild (after code change)
bitbake busybox
```

### Task Pipeline (do_* functions)

```
do_fetch    → Download source (git, tarball, http)
do_unpack   → Extract into ${S}
do_patch    → Apply patches from recipe or layer
do_configure → Run ./configure, cmake, or meson
do_compile  → Run make, ninja, etc.
do_install  → Copy to ${D} (temporary install dir)
do_package  → Split into runtime packages
do_rootfs   → Assemble final root filesystem
do_image    → Create flashable image formats
```

---

## Syntax & Key Files

### Recipe (`.bb`) – Example

```bitbake
SUMMARY = "Simple hello world application"
DESCRIPTION = "Prints hello world on console"
HOMEPAGE = "https://example.com"
LICENSE = "MIT"
LIC_FILES_CHKSUM = "file://${COMMON_LICENSE_DIR}/MIT;md5=0835ade698e0bcf8506ecda2f7b4f302"

SRC_URI = "git://github.com/user/hello.git;branch=main"
SRCREV = "a1b2c3d4e5f6"

S = "${WORKDIR}/git"

inherit autotools pkgconfig

do_compile() {
    oe_runmake
}

do_install() {
    install -d ${D}${bindir}
    install -m 0755 hello ${D}${bindir}/
}

RDEPENDS_${PN} += "libc6"
```

### Layer Configuration (`layer.conf`)

```bitbake
BBPATH .= ":${LAYERDIR}"
BBFILES += "${LAYERDIR}/recipes-*/*/*.bb \
            ${LAYERDIR}/recipes-*/*/*.bbappend"

BBFILE_COLLECTIONS += "mylayer"
BBFILE_PATTERN_mylayer = "^${LAYERDIR}/"
BBFILE_PRIORITY_mylayer = "6"

LAYERDEPENDS_mylayer = "core"
LAYERSERIES_COMPAT_mylayer = "kirkstone"
```

### Local Configuration (`conf/local.conf`)

```bitbake
MACHINE = "raspberrypi4-64"
DL_DIR = "/yocto/downloads"
SSTATE_DIR = "/yocto/sstate-cache"
TMPDIR = "/yocto/build/tmp"

PACKAGE_CLASSES = "package_rpm"
EXTRA_IMAGE_FEATURES += "debug-tweaks ssh-server-dropbear"

INHERIT += "rm_work"   # Remove workdir after build to save space

IMAGE_INSTALL_append = " htop i2c-tools"
```

### Append File (`.bbappend`)

```bitbake
# recipes-core/busybox/busybox_1.36.bbappend
FILESEXTRAPATHS_prepend := "${THISDIR}/${PN}:"

SRC_URI += "file://my_config.cfg"

do_configure_append() {
    echo "EXTRA_CFLAGS += -DMY_FEATURE" >> ${B}/.config
}

PACKAGECONFIG_append = " i2c"
```

---

## How to Check / Validate Your Setup

### 1. Check Build Environment

```bash
# Show Yocto version
bitbake --version

# Show all configured layers
bitbake-layers show-layers

# Show all recipes
bitbake-layers show-recipes

# Show recipe details (version, dependencies, provider)
bitbake -e busybox | grep ^PV=
```

### 2. Validate Recipe Syntax

```bash
# Parse recipe without building
bitbake -p -R

# Check specific recipe
bitbake -c checkpkg busybox

# Show variable expansion
bitbake -e busybox | grep ^SRC_URI
```

### 3. Validate Dependencies

```bash
# Build dependency graph
bitbake -g core-image-minimal
# Generates task-depends.dot

# Show reverse dependencies
bitbake -e busybox | grep "^RDEPENDS:"

# List all providers for a virtual package
bitbake -s | grep virtual/kernel
```

### 4. Validate Image Content

```bash
# Show what's inside the rootfs
bitbake -e core-image-minimal | grep ^IMAGE_ROOTFS

# List installed packages in image
bitbake -g core-image-minimal -u taskexp   # Graphical UI

# Using oe-pkgdata-util
oe-pkgdata-util list-pkg-files -p busybox
```

### 5. Cross-check Toolchain

```bash
# Generate and test SDK
bitbake -c populate_sdk core-image-minimal
cd tmp/deploy/sdk/
./poky-glibc-x86_64-core-image-minimal-cortexa72-toolchain-*.sh

# Test compiler
source environment-setup-cortexa72-poky-linux
$CC --version
```

### 6. CI Validation Script

```bash
#!/bin/bash
# validate-yocto.sh

set -e

echo "==> Cleaning old build"
rm -rf build/tmp

echo "==> Parsing all recipes"
bitbake -p

echo "==> Building core-image-minimal"
bitbake core-image-minimal

echo "==> Checking for missing runtime deps"
bitbake -s | grep -i "skipping"

echo "==> License manifest check"
test -f build/tmp/deploy/licenses/core-image-minimal-*/license.manifest

echo " Validation passed"
```

---

## Common Recipes & Variables

### Pre-defined Image Recipes

| Image Recipe | Content |
|---------------|---------|
| `core-image-minimal` | Just enough to boot (tiny) |
| `core-image-base` | Minimal + hardware support |
| `core-image-full-cmdline` | Bash, coreutils, networking |
| `core-image-sato` | Graphical UI (X11 + Sato desktop) |
| `core-image-weston` | Wayland/Weston display server |

### Critical Variables

| Variable | Purpose |
|----------|---------|
| `PN` | Package Name (recipe name) |
| `PV` | Package Version |
| `PR` | Package Revision |
| `S` | Source directory (after unpack) |
| `B` | Build directory (may different from S) |
| `D` | Destination dir (temporary install) |
| `WORKDIR` | Recipe's working area |
| `DEPENDS` | Build-time dependencies |
| `RDEPENDS_${PN}` | Runtime dependencies |
| `PACKAGECONFIG` | Feature toggles |
| `FILESEXTRAPATHS` | Additional search paths for files |

### Useful Classes to Inherit

```bitbake
inherit autotools        # For GNU autotools projects
inherit cmake            # For CMake projects
inherit systemd          # Install systemd service files
inherit update-rc.d      # For SysV init scripts
inherit kernel-module    # For out-of-tree kernel modules
inherit pypi             # For Python packages from PyPI
```

---

## Debugging & Troubleshooting

### Enable Detailed Logging

```bash
# Increase verbosity
bitbake -v -D core-image-minimal

# Show tasks being executed
bitbake -v core-image-minimal | grep Running
```

### Debug Specific Recipe

```bash
# BitBake shell – drop into build environment
bitbake -c devshell busybox

# Run specific task manually
bitbake -c unpack busybox
bitbake -c patch busybox
bitbake -c compile busybox -f   # force recompile
```

### Common Errors & Fixes

| Error | Likely Cause | Fix |
|-------|--------------|-----|
| `Nothing PROVIDES 'virtual/kernel'` | No kernel recipe for MACHINE | Check BSP layer, set `PREFERRED_PROVIDER` |
| `do_fetch: Failed to fetch URL` | Network or missing SRC_URI | Check checksum, try `bitbake -c cleanall` |
| `QA Issue: ELF binary 'foo' has relocations in .text` | Bad assembly or linker script | Add to `INSANE_SKIP_${PN} += "textrel"` |
| `Recipe is using deprecated syntax` | Old .bbappend | Update to new variable syntax (`RDEPENDS:${PN}` not `_` after Kirkstone) |
| `Nothing RPROVIDES 'libfoo.so.1'` | Shared library undeclared | Add `RPROVIDES_${PN} += "libfoo.so.1"` |

### sstate (Shared State) Debug

```bash
# Show signature differences
bitbake-diffsigs tmp/stamps/*/busybox/*do_compile.sigdata.*

# Dump task hash
bitbake -e busybox | grep ^BB_TASKHASH

# Force rebuild ignoring sstate
bitbake -f -c compile busybox
```

---

## Interview Preparation

### Q1: How does Yocto differ from Buildroot?
**A:**  
- **Buildroot**: Simple, single-pass makefile style. Good for very simple projects.  
- **Yocto**: Layer-based, powerful dependency resolution (BitBake), supports multiple machines, SDK generation, license compliance. Steeper learning curve but enterprise-ready.

### Q2: Explain the role of `PACKAGECONFIG`.
**A:** `PACKAGECONFIG` enables/disables optional features of a recipe without editing its source. Example:  
`PACKAGECONFIG_append_pn-openssl = " cryptodev"`  
It adds corresponding `DEPENDS`, `RDEPENDS`, and `EXTRA_OECONF` flags.

### Q3: How do you add a custom application?
**A:**  
1. Create your own layer (`bitbake-layers create-layer meta-myapp`).  
2. Create recipe `recipes-myapp/myapp/myapp_1.0.bb`.  
3. Set `SRC_URI`, `do_compile`, `do_install`.  
4. Add `IMAGE_INSTALL_append = " myapp"` in `local.conf`.

### Q4: What is `rm_work`? When would you disable it?
**A:** `INHERIT += "rm_work"` deletes `tmp/work` after build to save disk space. Disable when debugging (so you can inspect source or compile artifacts) or when using `devshell`.

### Q5: How to handle kernel patches in Yocto?
**A:**  
- Add `.patch` files to `SRC_URI`.  
- Or use `defconfig` fragment:  
  `SRC_URI += "file://my_config.cfg"`  
  Then in recipe: `KERNEL_CONFIG_FRAGMENTS += "${WORKDIR}/my_config.cfg"`

### Q6: Explain task hashing and sstate reuse.
**A:** BitBake computes a checksum for each task based on:  
- Recipe content  
- Dependencies (DEPENDS)  
- Variables (BB_BASEHASH_IGNORE_VARS)  
If the hash matches a previous build's sstate object, the task is skipped. Works across different builds even on different machines.

### Q7: How do you debug a boot failure on target?
**A:**  
- Add `IMAGE_FEATURES += "serial-console debug-tweaks"`  
- Enable kernel debug: `KERNEL_EXTRA_ARGS += "debug ignore_loglevel"`  
- Build a -dbg package: `IMAGE_INSTALL_append = " busybox-dbg gdb-dbg"`  
- Boot with init=/bin/sh to bypass init system.

---

## Cheatsheet Quick Reference

```bash
# Setup
source oe-init-build-env build

# Build commands
bitbake <target>                     # Build target
bitbake -c clean <recipe>            # Clean
bitbake -c cleanall <recipe>         # Clean including downloads
bitbake -c listtasks <recipe>        # List all tasks
bitbake -g <image>                   # Generate dependency graph

# Layers
bitbake-layers show-layers           # List layers
bitbake-layers add-layer <path>      # Add layer
bitbake-layers remove-layer <name>   # Remove layer
bitbake-layers create-layer <name>   # Create new layer skeleton

# Environment
bitbake -e | grep ^MACHINE=          # Show machine
bitbake -e <recipe> | grep ^FILES    # Show recipe variables

# SDK
bitbake -c populate_sdk <image>      # Generate SDK
bitbake -c populate_sdk_ext <image>  # Extensible SDK (with BitBake)

# Utility
oe-pkgdata-util list-pkg-files <pkg> # List files in package
oe-pkgdata-util find-path /bin/bash  # Which package owns file
```

### Variables Quick Card

| Use Case | Variable |
|----------|----------|
| Set architecture | `MACHINE = "..."` |
| Add pkg to image | `IMAGE_INSTALL_append = " pkg"` |
| Override recipe | `FILESEXTRAPATHS_prepend := "${THISDIR}/files:"` |
| Enable feature | `PACKAGECONFIG_append = " ssl"` |
| Set compiler flags | `CFLAGS_append = " -O2"` |
| Add kernel module | `KERNEL_MODULE_AUTOLOAD += "mykmod"` |

---

## Resources

- [Yocto Project Mega Manual](https://docs.yoctoproject.org/)
- [BitBake User Manual](https://docs.yoctoproject.org/bitbake/)
- [OpenEmbedded Layer Index](https://layers.openembedded.org/)
- [Yocto Mailing List](https://lists.yoctoproject.org/)

---

## Contributing

Found a typo or missing command?  
Pull requests welcome! Please keep entries concise and practical.

