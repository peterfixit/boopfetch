# Boopfetch 2.0.1 · Fortran / Linux 🐈

A **standalone native system dashboard**, primarily written in Fortran 2008.
The program uses a small C POSIX bridge for filesystem and network discovery,
then renders its own Unicode/ANSI true-colour UI directly in any modern terminal.
The executable requires **no separately installed libraries, language runtimes,
external commands, sensor packages, or Internet connection** for its normal operation.

**No legacy business branding** — just Boopfetch and its original cat logo.

## Install on Arch Linux or another x86-64 Linux distro

Download and extract `boopfetch-2.0.1.zip` (or the `.tar.gz` archive), then run:

```sh
cd boopfetch-2.0.1
./install.sh
source ~/.bashrc
boopfetch --health
```

The installer copies the included **static executable** to `~/.local/bin/boopfetch`
and adds `~/.local/bin` to your Bash startup files (or `.zshrc` if your login
shell is Zsh). The changes are idempotent: rerunning the installer won't add
duplicate PATH lines. **Existing terminal sessions cannot inherit PATH changes
from an executed script**, so run `source ~/.bashrc` once in your current Bash
terminal (or open a new terminal). A login shell should pick it up automatically.

Use `./install.sh --no-path` to skip shell configuration edits. In that case,
run `~/.local/bin/boopfetch` or configure your PATH yourself.

**Direct executable without installation:**

```sh
chmod +x boopfetch-2.0.1-linux-x86_64
./boopfetch-2.0.1-linux-x86_64 --health
```

This release has **no additional runtime packages or dynamic library dependencies**.
You do not need GCC or gfortran to install the supplied executable. The
installer uses standard POSIX shell and basic filesystem utilities.

The downloadable executable is **fully static for x86-64 Linux**. It contains
the Fortran runtime, C runtime, and all linked program libraries. We verified
this with `file`, `ldd`, and ELF metadata inspection. Linux kernel interfaces
(`/proc` and `/sys`) are required, as for all Linux monitoring utilities.
A particular sensor or graphical field may be unavailable on some machines.

## Features

- Polished cyan/purple terminal dashboard with boxed sections and original
  cat or loader ASCII logo; 24-bit ANSI colour and automatic terminal resizing
- OS, host model, kernel, architecture, uptime, and 1/5/15 minute loads
- CPU model, logical threads, live/rated clock, GPU (PCI sysfs), RAM and swap
- **Every regular mount**, including Btrfs subvolumes, local disks, removable
  media under `/run/media`, NFS/SMB, and common FUSE mounts; excludes pseudo
  mountpoints such as `/run` and `/sys`; disk usage/warnings per mount
- **Every assigned IP interface**: WLAN, Ethernet, and VPN shown independently,
  without up/down indicators; IPv4 by default and IPv6 if requested (IPv6-only
  interfaces are shown even without the flag)
- CPU, GPU/APU, NVMe and other temperature sensors if reported by sysfs/hwmon;
  battery charge and design-capacity where available
- Pacman/Dpkg and Flatpak user/system app counts directly from local databases
- Desktop, session, terminal, shell, locale, and any available display modes
- Optional health summary; low disk warning default 5%, temperatures >=90°C
  and high RAM usage. Missing metrics are not treated as successful checks.
- `--json` structured output, `--brief`, adjustable layout width, custom logo

The **failed systemd services** row is shown as *not queried* rather than
falsely reporting zero: accurate unit-state queries normally require D-Bus or
`systemctl`, neither of which is a runtime requirement of this build.

## Commands

```sh
boopfetch
boopfetch --health
boopfetch --logo=boop
boopfetch --logo=loader
boopfetch --logo=none
boopfetch --logo-file=cat.txt
boopfetch --warn-disk=10 --health
boopfetch --ipv6
boopfetch --width=160
boopfetch --plain
boopfetch --brief
boopfetch --json
boopfetch --temp-help
boopfetch --install-temps
```

`--temp-help` prints Linux-distribution-specific commands to optionally
install **lm_sensors**. The monitoring binary does not require it and can
already read the kernel-provided sensors without it.

`--install-temps` explicitly asks for permission before running the
appropriate package-manager command. The package manager and `sudo` are
needed **only if you choose this optional installation action**, not to
run Boopfetch. You can instead install lm_sensors yourself. `sensors-detect`
may help enable kernel modules on supported systems; follow its prompts and
consider hardware compatibility before changing sensor configuration.

## Build from source (optional)

Building requires GCC and gfortran (normal development-time requirements):

```sh
make static
./build/boopfetch-static
make test
```

From the full ZIP archive, `./install.sh` installs the included static binary and configures your shell PATH. Run `source ~/.bashrc` in an existing Bash session, or open a new terminal.
The default `make` target builds a dynamically linked development binary;
`make static` builds the dependency-free distribution executable.

## Supported platforms and security

- Linux x86-64 tested; source can be adapted for other Linux architectures
- No telemetry, no outbound connections, no background daemons
- Normal execution never launches other programs, never writes system settings,
  and needs no root privileges
- Reads local `/etc`, `/proc`, `/sys`, local package directories, and POSIX
  interfaces. GPU names depend on the PCI IDs database being present; otherwise
  a generic vendor/device ID is displayed.
- Desktop details depend on your environment variables and kernel DRM state;
  KDE version is read from the local pacman database when installed.
- The ANSI approximation cannot reproduce photographic glow or rounded
  graphics in a plain terminal, but retains the cyan/purple theme and sections.

## MIT licence

See `LICENSE`. This is a new Linux-focused implementation, not a verbatim
translation of Fastfetch. Source and tests are included.
