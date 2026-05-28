# Metadata in Yocto / BitBake 

# Overview

Metadata is one of the MOST IMPORTANT concepts in:
- Yocto Project
- Poky
- BitBake
- OpenEmbedded

Metadata tells BitBake:
```text
WHAT to build
HOW to build
WHEN to build
WHERE to install
```

Without metadata:
```text
BitBake cannot build anything
```

Metadata defines:
- recipes
- dependencies
- configurations
- package information
- build instructions

It is the:
```text
core knowledge database
```

of the Yocto build system.

---

# 1. What is Metadata?

Metadata is:
```text
structured build information
```

used by:
```text
BitBake
```

to generate Embedded Linux systems.

---

# 2. Main Purpose of Metadata

Metadata describes:
- source code locations
- dependencies
- compiler options
- install steps
- package rules

---

# 3. High-Level Metadata Architecture

```text
+----------------------+
| User Configuration   |
+----------------------+
           ↓
+----------------------+
| Metadata             |
| (.bb/.conf/.class)   |
+----------------------+
           ↓
+----------------------+
| BitBake              |
+----------------------+
           ↓
+----------------------+
| Build Tasks          |
+----------------------+
           ↓
+----------------------+
| Linux Image          |
+----------------------+
``` id="arch1"

---

# 4. Why Metadata is Needed

Embedded Linux systems are complex.

Need to manage:
- thousands of packages
- dependencies
- architectures
- patches
- toolchains

Metadata automates this.

---

# 5. Metadata Responsibilities

Metadata defines:
- package recipes
- dependencies
- build tasks
- machine configurations
- distro settings

---

# 6. Types of Metadata

| Metadata Type | Extension |
|---------------|-----------|
| Recipe | .bb |
| Append file | .bbappend |
| Class | .bbclass |
| Configuration | .conf |
| Include file | .inc |

---

# 7. Metadata Organization

Metadata stored in:
```text
layers
```

---

# 8. Layer Architecture

```text
+----------------------+
| Custom Layer         |
+----------------------+
| BSP Layer            |
+----------------------+
| OpenEmbedded Core    |
+----------------------+
``` id="layer1"

---

# 9. What is a Recipe?

Recipe describes:
```text
how to build a package
```

---

# 10. Recipe File Extension

```text
.bb
```

---

# 11. Recipe Example

```bitbake
DESCRIPTION = "Hello App"

LICENSE = "MIT"

SRC_URI = "file://hello.c"
``` id="recipe1"

---

# 12. Recipe Responsibilities

Recipe defines:
- source location
- dependencies
- compilation steps
- install steps

---

# 13. Common Recipe Variables

| Variable | Meaning |
|----------|---------|
| DESCRIPTION | Package description |
| LICENSE | License type |
| SRC_URI | Source location |
| DEPENDS | Build dependencies |
| RDEPENDS | Runtime dependencies |

---

# 14. DESCRIPTION Variable

```bitbake
DESCRIPTION = "My Application"
``` id="desc1"

---

# 15. LICENSE Variable

```bitbake
LICENSE = "GPLv2"
``` id="lic1"

---

# 16. SRC_URI Variable

Defines:
```text
source code location
```

---

# 17. SRC_URI Example

```bitbake
SRC_URI = "git://github.com/app/project.git"
``` id="src1"

---

# 18. DEPENDS Variable

Defines:
```text
build-time dependencies
```

---

# 19. DEPENDS Example

```bitbake
DEPENDS = "openssl"
``` id="dep1"

---

# 20. RDEPENDS Variable

Defines:
```text
runtime dependencies
```

---

# 21. RDEPENDS Example

```bitbake
RDEPENDS:${PN} = "bash"
``` id="dep2"

---

# 22. Tasks in Metadata

Recipes define:
```text
tasks
```

---

# 23. Common Tasks

| Task | Purpose |
|------|---------|
| do_fetch | Download source |
| do_unpack | Extract source |
| do_patch | Apply patches |
| do_compile | Compile |
| do_install | Install |

---

# 24. Task Flow

```text
do_fetch
    ↓
do_unpack
    ↓
do_patch
    ↓
do_configure
    ↓
do_compile
    ↓
do_install
``` id="task1"

---

# 25. do_compile Example

```bitbake
do_compile() {
    make
}
``` id="compile1"

---

# 26. do_install Example

```bitbake
do_install() {
    install -d ${D}/usr/bin
}
``` id="install1"

---

# 27. What are Classes?

Classes provide:
```text
reusable build functionality
```

---

# 28. Class File Extension

```text
.bbclass
```

---

# 29. Common Classes

| Class | Purpose |
|-------|----------|
| autotools | GNU autotools |
| cmake | CMake support |
| kernel | Kernel build |

---

# 30. Using Classes

```bitbake
inherit cmake
``` id="class1"

---

# 31. What are Configuration Files?

Configuration files define:
- machine settings
- distro settings
- global variables

---

# 32. Configuration File Extension

```text
.conf
```

---

# 33. Important Config Files

| File | Purpose |
|------|---------|
| local.conf | Local build settings |
| bblayers.conf | Active layers |
| machine.conf | Machine settings |

---

# 34. local.conf Example

```conf
MACHINE = "qemuarm"
``` id="conf1"

---

# 35. bblayers.conf Example

```conf
BBLAYERS += "meta-custom"
``` id="conf2"

---

# 36. What are Include Files?

Include files:
```text
share common metadata
```

---

# 37. Include File Extension

```text
.inc
```

---

# 38. Include Example

```bitbake
require common.inc
``` id="inc1"

---

# 39. bbappend Files

Used to:
```text
modify existing recipes
```

without changing original recipe.

---

# 40. bbappend Example

```text
myapp.bbappend
```

---

# 41. Metadata Parsing

BitBake parses metadata during:
```text
build startup
```

---

# 42. Metadata Parsing Flow

```text
Read Config Files
       ↓
Read Layers
       ↓
Read Recipes
       ↓
Build Dependency Graph
``` id="parse1"

---

# 43. Variable Expansion

BitBake expands:
```text
variables dynamically
```

---

# 44. Variable Example

```bitbake
bindir = "/usr/bin"
``` id="var1"

---

# 45. Special Variables

| Variable | Meaning |
|----------|---------|
| ${PN} | Package name |
| ${PV} | Package version |
| ${WORKDIR} | Working directory |
| ${D} | Install directory |

---

# 46. Package Naming Example

```bitbake
${PN}
```

expands to:
```text
package name
```

---

# 47. Machine Metadata

Machine metadata defines:
- architecture
- kernel
- bootloader
- DTBs

---

# 48. Machine Config Example

```conf
TARGET_ARCH = "arm"
``` id="mach1"

---

# 49. Distro Metadata

Defines:
- package manager
- init system
- distro policies

---

# 50. Metadata and Dependency Graph

BitBake creates:
```text
Directed Acyclic Graph (DAG)
```

using metadata.

---

# 51. DAG Flow

```text
Recipe A
   ↓
Library B
   ↓
Compiler C
``` id="dag1"

---

# 52. Metadata and Cross Compilation

Metadata defines:
- target architecture
- compiler options
- sysroot

---

# 53. Metadata Storage Locations

Typical structure:

```text
meta/
├── recipes-core/
├── recipes-kernel/
├── recipes-devtools/
``` id="meta1"

---

# 54. Example Recipe Path

```text
meta/recipes-core/bash/bash.bb
```

---

# 55. Metadata and Packaging

Metadata controls:
- package splitting
- runtime dependencies
- install locations

---

# 56. Image Metadata

Images themselves defined by:
```text
recipes
```

---

# 57. Image Recipe Example

```bitbake
IMAGE_INSTALL += "bash"
``` id="img1"

---

# 58. Metadata Processing Workflow

```text
Metadata Parsed
       ↓
Variables Expanded
       ↓
Dependencies Calculated
       ↓
Tasks Generated
       ↓
Build Executed
``` id="flow1"

---

# 59. Debugging Metadata

---

## View Environment

```bash
bitbake -e
``` id="dbg1"

---

## Show Layers

```bash
bitbake-layers show-layers
``` id="dbg2"

---

## Show Recipes

```bash
bitbake-layers show-recipes
``` id="dbg3"

---

# 60. Common Metadata Problems

| Problem | Description |
|---------|-------------|
| Missing dependency | Build failure |
| Incorrect variable | Wrong output |
| Layer priority issue | Unexpected recipe |
| Syntax errors | Parse failure |

---

# 61. Metadata Priority

Layers may override metadata using:
```text
layer priorities
```

---

# 62. Metadata and Overrides

Overrides customize builds for:
- machines
- distros
- architectures

---

# 63. Example Override

```bitbake
SRC_URI:append = " file://patch.patch"
``` id="ovr1"

---

# 64. Real Embedded Linux Example

```text
Recipe Defines BusyBox
        ↓
BitBake Reads Metadata
        ↓
BusyBox Downloaded
        ↓
Cross Compiled
        ↓
Installed into RootFS
``` id="real1"

---

# 65. Metadata Advantages

| Advantage | Description |
|-----------|-------------|
| Modular | Yes |
| Reusable | Yes |
| Scalable | Yes |
| Customizable | Yes |

---

# 66. Metadata vs Source Code

| Metadata | Source Code |
|----------|-------------|
| Build instructions | Actual program |
| Dependencies | Logic |
| Packaging rules | Functionality |

---

# 67. Complete Metadata Workflow

```text
User Starts BitBake
        ↓
Metadata Parsed
        ↓
Recipes Loaded
        ↓
Dependencies Resolved
        ↓
Tasks Generated
        ↓
Cross Compilation
        ↓
Packages Built
        ↓
Linux Image Created
``` id="final1"

---

# 68. Final Core Concept

```text
Metadata =
Build Instructions and Dependency Information
for BitBake
```

---

# 69. Summary

- Metadata defines how software is built in Yocto
- Stored in recipes, classes, and configuration files
- Parsed by BitBake
- Controls dependencies, tasks, and packaging
- Organized into layers
- Essential for Embedded Linux builds
- Core foundation of Yocto and Poky

---


````
