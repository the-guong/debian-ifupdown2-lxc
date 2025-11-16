
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

- Local builds still need an arm64 host (or arm64 VM) because the tooling does not cross-compile userland binaries.
- A GitHub Actions workflow (`build-arm64-rootfs`) now runs on an `ubuntu-24.04-arm` runner so you can produce arm64 artifacts on demand without provisioning hardware yourself.
- See the `BUILD` file and `debian.yaml` for concrete build steps and the set of packages included in the generated image.

## Automated builds via GitHub Actions

1. Navigate to the **Actions** tab and select the **build-arm64-rootfs** workflow.
2. Click **Run workflow**, pick the Debian `release` (defaults to `bookworm`), the distrobuilder `variant` (`default` or `cloud`), and optionally change the Debian mirror URL.
3. The job runs on GitHub-hosted `ubuntu-24.04-arm` hardware, installs distrobuilder from source, and executes the same command documented in `BUILD` with the parameters you provided.
4. When the workflow completes, download the artifact named `debian-rootfs-<release>-<variant>-arm64` from the run summary. It contains the `rootfs` tarball, metadata tarball, and a `SHA256SUMS.txt` file with checksums for both outputs.
5. Use this workflow for quick testing or ad-hoc builds—no Git tags or releases are touched.

You can also call this workflow from another workflow via `workflow_call`, reusing the same inputs.

### Publishing release artifacts

When you are ready to publish a build and attach it to a GitHub Release:

1. Open the **publish-arm64-rootfs-release** workflow under **Actions**.
2. Click **Run workflow** and fill in the inputs:
	- `release`, `variant`, and `source_url` match the parameters in the manual build workflow.
	- `tag_name` is the Git tag that will be created or updated for the release (for example, `v2025.11.16`).
	- Optionally set `release_name`, `release_body`, `draft`, and `prerelease` to match how you want the GitHub Release to appear.
3. The workflow reuses `build-arm64-rootfs` on arm64 hardware, downloads the resulting artifact, and publishes a GitHub Release with the files attached.
4. Continue using **build-arm64-rootfs** for everyday testing—the release workflow is only needed when you want downloadable assets on the Releases page.

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
