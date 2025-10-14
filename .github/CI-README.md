# GitHub Actions CI Documentation

This repository includes comprehensive GitHub Actions workflows for building the OpenGLide library across multiple platforms and configurations.

## Workflows

### 1. `build.yml` - Main Build Workflow

This workflow builds OpenGLide on multiple platforms with different configurations:

**Supported Platforms:**
- **Linux (Ubuntu 22.04)**
  - x86_64 with and without SDL support
  - Includes OpenGL, GLU, X11, and SDL2 dependencies

- **macOS**
  - x86_64 (macOS 12) and ARM64 (macOS 14) 
  - With and without SDL support
  - Includes XQuartz for X11 support

- **Windows (MSYS2)**
  - x86_64 and i686 architectures
  - Uses MinGW-w64 toolchain
  - Includes OpenGL and related dependencies

**Build Configurations:**
- `--disable-sdl`: Builds without SDL dependency (lighter, X11 only)
- Default: Builds with SDL support when available

**Triggers:**
- Push to `master` or `main` branches
- Pull requests to `master` or `main` branches  
- Manual trigger via `workflow_dispatch`

### 2. `validate.yml` - Workflow Validation

Simple workflow to validate the YAML syntax of all workflow files.

## Build Process

Each build follows these steps:

1. **Checkout** - Gets the source code
2. **Setup Dependencies** - Installs platform-specific build tools and libraries
3. **Bootstrap** - Runs the autotools bootstrap script
4. **Configure** - Runs the configure script with appropriate flags
5. **Build** - Compiles the libraries using make
6. **Package Artifacts** - Collects build outputs
7. **Upload Artifacts** - Stores build results for download

## Artifacts

The workflow produces several types of artifacts:

- **Individual Platform Builds**: 
  - `openglide-{os}-{arch}-{config}` - Per-platform build outputs
  - Contains `.so`, `.dylib`, or `.dll` files depending on platform
  - Also includes `.la` libtool archive files

- **Combined Release Bundle**:
  - `openglide-all-platforms` - All platform builds in one archive
  - Only created for pushes to main branches
  - Retained for 90 days vs 30 days for individual builds

## Dependencies

### Linux (Ubuntu)
```bash
# Build tools
build-essential autotools-dev automake libtool pkg-config

# OpenGL libraries  
libgl1-mesa-dev libglu1-mesa-dev

# X11 libraries
libx11-dev libxext-dev libxxf86vm-dev

# SDL (optional)
libsdl2-dev
```

### macOS
```bash
# Build tools (via Homebrew)
autoconf automake libtool pkg-config

# X11 support
xquartz

# SDL (optional)
sdl2
```

### Windows (MSYS2)
```bash
# Build tools
mingw-w64-{arch}-gcc
mingw-w64-{arch}-autotools  
mingw-w64-{arch}-libtool
mingw-w64-{arch}-pkg-config

# OpenGL libraries
mingw-w64-{arch}-freeglut
mingw-w64-{arch}-glew
```

## Usage

### Automatic Builds

The CI will automatically run on:
- Any push to the `master` or `main` branch
- Any pull request targeting `master` or `main`

### Manual Builds

You can manually trigger a build by:
1. Going to the "Actions" tab in GitHub
2. Selecting the "Build OpenGLide" workflow
3. Clicking "Run workflow"

### Downloading Build Results

1. Go to the "Actions" tab
2. Click on a completed workflow run
3. Scroll down to the "Artifacts" section
4. Download the desired platform build

## Customization

### Adding New Platforms

To add support for a new platform:

1. Add a new entry to the `matrix.include` section in `build.yml`
2. Specify the appropriate `os`, `arch`, and dependency installation commands
3. Test the new configuration

### Modifying Build Flags

To change configure options:

1. Modify the `configure_flags` in the matrix entry
2. Ensure any new dependencies are installed in the `install_deps` step

### Example: Adding FreeBSD Support

```yaml
- name: "FreeBSD x86_64"
  os: ubuntu-22.04  # Use cross-compilation or VM
  arch: x86_64
  configure_flags: "--disable-sdl"
  install_deps: |
    # Setup FreeBSD cross-compilation environment
    # Install FreeBSD OpenGL libraries
```

## Troubleshooting

### Build Failures

1. **Missing Dependencies**: Check the `install_deps` step logs
2. **Configure Failures**: Review the configure script output
3. **Compilation Errors**: Check the make step for specific error messages

### Platform-Specific Issues

- **macOS**: XQuartz installation may require manual intervention
- **Windows**: MSYS2 environment setup can be fragile
- **Linux**: Mesa development packages may vary by distribution

### Common Solutions

- **Autotools Issues**: Ensure `bootstrap` script has execute permissions
- **OpenGL Issues**: Verify Mesa/OpenGL development packages are installed
- **SDL Issues**: Check SDL2 development package installation

## Security Considerations

- All workflows use pinned action versions (e.g., `@v4`)
- No secrets or credentials are required for building
- Artifacts are automatically cleaned up after retention period
- Only builds from authorized repository contributors are allowed

## Performance

- Builds use parallel compilation (`make -j`)
- Artifact retention is optimized (30 days individual, 90 days combined)
- Build matrix allows concurrent execution across platforms
- Workflow can be canceled if newer commits are pushed