# BitBake in Yocto Project 

# Overview

BitBake is the:
```text
task execution engine and build tool
```

used in:
- Yocto Project
- Poky
- OpenEmbedded

BitBake is responsible for:
- parsing recipes
- resolving dependencies
- scheduling tasks
- cross-compiling packages
- generating Linux images

It is one of the CORE technologies in:
```text
Embedded Linux build systems
```

---

# 1. What is BitBake?

BitBake is:
```text
a build automation tool
```

similar to:
- make
- cmake
- ninja

but specifically designed for:
```text
Embedded Linux distribution building
```

---

# 2. Main Purpose of BitBake

BitBake automates:
- downloading sources
- patching
- compilation
- packaging
- root filesystem creation

---

# 3. High-Level BitBake Architecture

```text
+----------------------+
| User Configuration   |
+----------------------+
           ↓
+----------------------+
| BitBake              |
+----------------------+
           ↓
+----------------------+
| Recipes (.bb)        |
+----------------------+
           ↓
+----------------------+
| Tasks                |
+----------------------+
           ↓
+----------------------+
| Cross Toolchain      |
+----------------------+
           ↓
+----------------------+
| Linux Image          |
+----------------------+
``` id="arch1"

---

# 4. Relationship with Yocto

| Component | Purpose |
|-----------|---------|
| Yocto Project | Entire ecosystem |
| Poky | Reference build system |
| BitBake | Build engine |

---

# 5. BitBake Responsibilities

BitBake handles:
- dependency management
- task scheduling
- incremental builds
- cross-compilation
- package generation

---

# 6. What BitBake Builds

BitBake can build:
- applications
- libraries
- Linux kernel
- bootloaders
- root filesystems
- SDKs

---

# 7. BitBake Input Files

BitBake processes:
- recipes
- classes
- configurations
- layers

---

# 8. Important File Types

| File Type | Extension |
|-----------|------------|
| Recipe | .bb |
| Append file | .bbappend |
| Class | .bbclass |
| Config | .conf |

---

# 9. What is a Recipe?

Recipe describes:
```text
how to build software
```

---

# 10. Recipe Example

```bitbake
DESCRIPTION = "Hello Application"

SRC_URI = "file://hello.c"
``` id="recipe1"

---

# 11. What Recipes Contain

Recipes define:
- source location
- dependencies
- build steps
- install instructions

---

# 12. BitBake Build Workflow

```text
Parse Recipes
      ↓
Resolve Dependencies
      ↓
Create Task Graph
      ↓
Execute Tasks
      ↓
Generate Packages
      ↓
Create Images
``` id="flow1"

---

# 13. Main BitBake Command

```bash
bitbake core-image-minimal
``` id="cmd1"

---

# 14. Meaning of This Command

BitBake will:
- parse metadata
- build dependencies
- compile packages
- generate minimal Linux image

---

# 15. BitBake Parsing Stage

BitBake first parses:
- recipes
- classes
- configuration files

---

# 16. Dependency Resolution

BitBake determines:
```text
which packages depend on others
```

---

# 17. Dependency Graph

```text
Application
     ↓
Library
     ↓
Compiler
``` id="dep1"

---

# 18. Task Graph Generation

BitBake creates:
```text
Directed Acyclic Graph (DAG)
```

of tasks.

---

# 19. Why DAG is Important

Ensures:
- proper build order
- parallel execution
- dependency correctness

---

# 20. Build Task Flow

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
    ↓
do_package
``` id="task1"

---

# 21. Common Tasks

| Task | Purpose |
|------|---------|
| do_fetch | Download source |
| do_unpack | Extract source |
| do_patch | Apply patches |
| do_configure | Configure build |
| do_compile | Compile source |
| do_install | Install files |
| do_package | Create package |

---

# 22. do_fetch

Downloads:
- source code
- archives
- git repositories

---

# 23. Example SRC_URI

```bitbake
SRC_URI = "git://github.com/app/project.git"
``` id="src1"

---

# 24. do_unpack

Extracts:
```text
downloaded archives
```

---

# 25. do_patch

Applies:
```text
patch files
```

to source code.

---

# 26. do_configure

Runs:
- configure scripts
- cmake
- autotools

---

# 27. do_compile

Compiles source using:
```text
cross compiler
```

---

# 28. do_install

Copies files into:
```text
temporary root filesystem
```

---

# 29. do_package

Creates packages:
- rpm
- deb
- ipk

---

# 30. Cross-Compilation

BitBake automatically handles:
```text
cross compilation
```

---

# 31. Cross-Compilation Flow

```text
Host PC
    ↓
Cross Compiler
    ↓
Target ARM Binary
``` id="cross1"

---

# 32. BitBake Variables

Recipes use:
```text
variables
```

---

# 33. Example Variables

```bitbake
PN = "myapp"
PV = "1.0"
``` id="var1"

---

# 34. Important Variables

| Variable | Meaning |
|----------|---------|
| PN | Package name |
| PV | Package version |
| SRC_URI | Source location |
| LICENSE | Software license |

---

# 35. Layer Concept

Metadata organized into:
```text
layers
```

---

# 36. Layer Architecture

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

# 37. Configuration Files

Located in:
```text
build/conf/
```

---

# 38. local.conf

Defines:
- machine type
- parallel jobs
- image settings

---

# 39. Example local.conf

```conf
MACHINE = "qemuarm"
``` id="conf1"

---

# 40. bblayers.conf

Defines:
```text
active layers
```

---

# 41. Example bblayers.conf

```conf
BBLAYERS += "meta-custom"
``` id="conf2"

---

# 42. Shared State Cache (sstate)

BitBake uses:
```text
sstate-cache
```

for faster builds.

---

# 43. Incremental Build

Only changed components rebuilt.

---

# 44. Parallel Execution

BitBake supports:
```text
parallel task execution
```

---

# 45. Build Output Directory

Generated files stored in:
```text
tmp/
```

---

# 46. Image Output

Images stored in:
```text
tmp/deploy/images/
```

---

# 47. Package Output

Packages stored in:
```text
tmp/deploy/rpm/
```

or:
```text
deb/
ipk/
```

---

# 48. SDK Generation

```bash
bitbake core-image-minimal -c populate_sdk
``` id="cmd2"

---

# 49. Clean Build Commands

---

## Clean

```bash
bitbake -c clean myapp
``` id="clean1"

---

## Clean All

```bash
bitbake -c cleanall myapp
``` id="clean2"

---

# 50. Development Shell

```bash
bitbake myapp -c devshell
``` id="dev1"

---

# 51. View Environment Variables

```bash
bitbake -e
``` id="env1"

---

# 52. Debugging Builds

---

## Verbose Mode

```bash
bitbake -v
``` id="dbg1"

---

## Dependency Graph

```bash
bitbake -g
``` id="dbg2"

---

# 53. BitBake Classes

Reusable functionality stored in:
```text
.bbclass files
```

---

# 54. Common Classes

| Class | Purpose |
|-------|----------|
| autotools | GNU autotools support |
| cmake | CMake support |
| kernel | Kernel build support |

---

# 55. Inheriting Classes

```bitbake
inherit cmake
``` id="class1"

---

# 56. Package Dependencies

Recipe dependencies:

```bitbake
DEPENDS = "openssl"
``` id="dep2"

---

# 57. Runtime Dependencies

```bitbake
RDEPENDS:${PN} = "bash"
``` id="dep3"

---

# 58. BitBake Scheduler

Scheduler determines:
```text
optimal task execution order
```

---

# 59. Build Acceleration

Features:
- parallel builds
- shared cache
- incremental rebuilds

---

# 60. Real Embedded Linux Build Flow

```text
Developer Runs BitBake
        ↓
Recipes Parsed
        ↓
Dependencies Resolved
        ↓
Cross Compiler Invoked
        ↓
Packages Built
        ↓
RootFS Created
        ↓
Image Generated
``` id="real1"

---

# 61. BitBake Internals

Internally uses:
- Python
- metadata parser
- task scheduler

---

# 62. BitBake vs Make

| Feature | BitBake | Make |
|---------|----------|------|
| Dependency handling | Advanced | Basic |
| Cross compilation | Native support | Manual |
| Package generation | Yes | No |
| Incremental builds | Excellent | Limited |

---

# 63. Advantages of BitBake

| Advantage | Description |
|-----------|-------------|
| Powerful dependency management | Yes |
| Cross-compilation support | Yes |
| Reproducible builds | Yes |
| Scalable | Yes |

---

# 64. Disadvantages

| Issue | Description |
|------|-------------|
| Steep learning curve | Yes |
| Complex metadata | Yes |
| Long initial builds | Possible |

---

# 65. Complete BitBake Workflow

```text
User Executes bitbake
          ↓
Metadata Parsed
          ↓
Dependency Graph Built
          ↓
Tasks Scheduled
          ↓
Sources Downloaded
          ↓
Cross Compilation
          ↓
Packages Generated
          ↓
Root Filesystem Created
          ↓
Final Linux Image Produced
``` id="final1"

---

# 66. Final Core Concept

```text
BitBake =
Task Execution Engine
for Embedded Linux Builds
```

---

# 67. Summary

- BitBake is the core build engine of Yocto/Poky
- Executes tasks defined in recipes
- Handles dependencies automatically
- Supports cross-compilation
- Generates packages, SDKs, and Linux images
- Uses metadata and layers
- Essential for Embedded Linux distribution building

---


````
