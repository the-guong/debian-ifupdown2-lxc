
# debian-ifupdown2-lxc

Lightweight Debian ARM64 root filesystem prepared for use with LXC containers. This repository contains the build/configuration artifacts and notes used to assemble an arm64 Debian rootfs with ifupdown2 installed and systemd-networkd disabled.

Why this repo exists

- Provide a reproducible Debian arm64 rootfs tailored for LXC where interfaces are managed by ifupdown2 instead of systemd-networkd.
- Support development, testing, and deployment workflows that require the older ifupdown-style networking stack inside containers.

What you'll find here

- BUILD — high-level build instructions and scripts used to create the rootfs.
- `debian.yaml` — configuration used by the build tooling (image definition, packages, etc.).
- README.md — this file with repository context and usage notes.

High level build notes

- The rootfs must be built on an arm64 host. The build process in this repository is not configured to run on GitHub Actions (x86_64 runners will not produce a valid arm64 rootfs).
- See the `BUILD` file and `debian.yaml` for concrete build steps and the set of packages included in the generated image.

Using the rootfs with LXC

1. Build or download the rootfs artifact following the instructions in `BUILD`.
2. Create an LXC container and import the rootfs as its filesystem. Example (host-side):

```sh
# create a container from an existing rootfs tarball
lxc-create -n my-container -t local -- --fstree path/to/rootfs.tar.gz
```

3. Configure the container's LXC profile/networking as needed. This rootfs expects ifupdown2 inside the container, so disable systemd-networkd if it would otherwise be enabled by the host/container template.

Limitations and notes

- Built artifacts are arm64-only. Cross-building on non-arm64 systems is not supported by the included scripts.
- This repository focuses on preparing the rootfs; orchestration, CI runners, or platform-specific deployment scripts are out of scope.

Files of interest

- `BUILD` — primary build steps and requirements.
- `debian.yaml` — package and image configuration.

Contributing

If you want to contribute fixes or improvements:

1. Open an issue describing the change you propose.
2. Prepare a small, focused patch and include build verification notes (what host you used, command output, and any differences observed).
3. If you update build logic, ensure you document required host prerequisites (e.g., OS, toolchain versions, available disk space).

License and contact

See repository root for license information. For questions, open an issue or contact the repository owner.

---
Edited to provide clearer repository context, build notes, and usage guidance for LXC.
