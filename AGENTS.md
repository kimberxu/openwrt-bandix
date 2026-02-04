# AGENTS.md - OpenWrt Package Development Guide

## Project Overview
This repository (`openwrt-bandix`) is an OpenWrt package feed for **Bandix**, a network traffic monitoring tool. 
**Crucially, this is a wrapper package.** It does not compile the binary from source; instead, it downloads pre-compiled Rust binaries from GitHub Releases during the build process.

## Environment & Prerequisites
- **Platform**: OpenWrt Build System (Linux-based).
- **Language**: GNU Make (Makefiles), POSIX Shell (`/bin/sh`), UCI configuration.
- **Context**: This directory is intended to be a package source within an OpenWrt SDK or buildroot environment (e.g., `package/openwrt-bandix`).

## Build & Verification
Since this is a package definition and not a standalone application, "building" implies integrating with the OpenWrt build system.

### Verification Commands
Run these from the repository root to verify syntax and integrity:

1. **Shell Script Syntax Check**:
   ```bash
   # Check init script for syntax errors
   sh -n openwrt-bandix/files/bandix.init
   ```

2. **Linting (Recommended)**:
   If `shellcheck` is available:
   ```bash
   shellcheck openwrt-bandix/files/bandix.init
   ```

### Simulation / Debugging
To simulate how the package builds variables (requires `make`):
*Note: This usually fails without `TOPDIR` defined, but is the standard way OpenWrt checks Makefiles.*

## Code Style & Conventions

### 1. Makefiles (`openwrt-bandix/Makefile`)
- **Indentation**: **MUST use TABS**. Never spaces. (Make will fail with spaces).
- **Variables**: Use `UPPER_CASE` for package variables (`PKG_NAME`, `PKG_VERSION`).
- **Target Architecture**: Use `$(ARCH)` and `$(CONFIG_*)` to handle cross-compilation logic (as seen in the `PKG_HASH` selection logic).
- **Downloads**: `PKG_SOURCE_URL` should point to the upstream release.
- **Hash**: Always update `PKG_HASH` when bumping `PKG_VERSION`. Use `dummy` for unsupported archs if necessary, or fail gracefully.

### 2. Init Scripts (`files/bandix.init`)
- **Interpreter**: `#!/bin/sh /etc/rc.common` (Use the OpenWrt rc wrapper).
- **Indentation**: Use **TABS** for consistency with OpenWrt scripts.
- **Service Management**: Use `procd` (Process Control Daemon) directives.
  - `start_service()`: Main entry point.
  - `procd_open_instance`: Start defining the service.
  - `procd_set_param command`: Set the binary and arguments.
  - `procd_close_instance`: Finish definition.
- **Configuration**: Use `config_load 'bandix'` and `config_get` to read from `/etc/config/bandix`.
- **Local Variables**: Declare `local varname` to avoid polluting the global namespace.

### 3. UCI Configuration (`files/bandix.config`)
- **Format**: Standard UCI format (`config section 'name'`, `option key 'value'`).
- **Naming**: Use snake_case for options.
- **Booleans**: Use `'1'` for true, `'0'` for false.

## Project Structure
```
.
├── LICENSE                     # Apache 2.0 License
├── README.md                   # Documentation
├── openwrt-bandix/             # The OpenWrt Package Directory
│   ├── Makefile                # Package definition & build logic
│   └── files/                  # Runtime files installed to target
│       ├── bandix.config       # Default config -> /etc/config/bandix
│       ├── bandix.init         # Init script -> /etc/init.d/bandix
│       └── bandix.keep         # Backup list -> /lib/upgrade/keep.d/bandix
```

## Workflow for Updating Package
1. **Bump Version**: Update `PKG_VERSION` and `RUST_BANDIX_VERSION` in `Makefile`.
2. **Update Hashes**: Update `PKG_HASH` for all supported architectures.
   - `aarch64`, `arm` (various variants), `mips`, `mipsel`, `x86_64`.
3. **Verify URL**: Ensure `PKG_SOURCE_URL` is valid for the new version.
4. **Commit**: Use Conventional Commits (e.g., `feat: bump version to 0.12.3`).

## Error Handling
- **Init Script**: Return `1` if essential configuration is missing (e.g., see line 47 in `bandix.init`).
- **Makefile**: Use `ifeq` / `else` to handle architecture differences gracefully.

## Git Instructions
- **Commits**: Atomic commits.
- **Message**: "type: description". Types: `feat`, `fix`, `docs`, `style`, `refactor`.
