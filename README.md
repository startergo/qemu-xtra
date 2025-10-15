# QEMU Extras

[![Build OpenGLide](https://github.com/startergo/qemu-xtra/actions/workflows/build.yml/badge.svg)](https://github.com/startergo/qemu-xtra/actions/workflows/build.yml)

Old PC Games Addons for QEMU
## Content
    openglide - OpenGLide fork optimized for QEMU (Support Glide2x & Glide3x)
    g2xwrap   - GLIDE.DLL & GLIDE3X.DLL that wrap into Glide2x APIs
    dosbox    - DOSBox SVN Games essentials

## Building OpenGLide

### Manual Build
```bash
$ mkdir ~/myxtra && cd ~/myxtra
$ git clone https://github.com/startergo/qemu-xtra.git
$ cd qemu-xtra/openglide
$ bash ./bootstrap
$ mkdir ../build && cd ../build
$ ../openglide/configure --disable-sdl && make
```

### Automated Builds (CI/CD)

This project includes GitHub Actions workflows that automatically build OpenGLide for multiple platforms:

- **Linux** (Ubuntu 22.04, x86_64) - with and without SDL
- **macOS** (x86_64 and ARM64) - with and without SDL  
- **Windows** (MSYS2, x86_64 and i686) - MinGW builds

Build artifacts are automatically generated for each platform and can be downloaded from the [Actions tab](https://github.com/startergo/qemu-xtra/actions).

For detailed information about the CI system, see [.github/CI-README.md](.github/CI-README.md).

## Building G2Xwrap

**Requires OpenGLide compiled first!**

### Manual Build
```bash
$ mkdir ~/myxtra && cd ~/myxtra
$ git clone https://github.com/startergo/qemu-xtra.git
$ cd qemu-xtra/openglide
$ bash ./bootstrap
$ mkdir ../build && cd ../build
$ ../openglide/configure --disable-sdl && make
$ cd ../g2xwrap
$ make
```

### Automated Builds (CI/CD)

G2Xwrap is automatically built as part of the CI workflow for Windows platforms after OpenGLide compilation completes. The wrapper DLLs (`glide.dll` and `glide3x.dll`) are included in the Windows build artifacts.
