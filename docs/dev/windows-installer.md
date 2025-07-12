# Windows Installer Guide

This guide explains how to build and customize the MSI installer for the Wazuh Agent on Windows.

## Requirements

- Visual Studio Community 2022 with the MSVC toolset
- [Chocolatey](https://chocolatey.org/) package manager
- The following packages:
  - `make`
  - `cmake` (install with `--installargs 'ADD_CMAKE_TO_PATH=System'`)
  - `openssl`

## Compile the Agent

From the repository root run:

```powershell
cd packages/windows
./generate_compiled_windows_agent.ps1 -MSI_NAME wazuh-agent -CMAKE_CONFIG Release
```

The script configures CMake and builds the binaries in the `build` directory. Use `-BUILD_TESTS 1` to include test binaries or `-TOKEN_VCPKG` to enable binary caching.

## Generate the MSI Package

After compilation run:

```powershell
./generate_wazuh_msi.ps1 -MSI_NAME wazuh-agent_6.0.0-0_windows -SIGN no
```

Important options include:

- `-SIGN yes|no` – Sign the installer and related files.
- `-DEBUG yes|no` – Produce debug symbol archives.
- `-CERTIFICATE_PATH` and `-CERTIFICATE_PASSWORD` – Signing certificate information.

The resulting `.msi` file is placed in `packages/windows`.

## Custom Actions and UI

The installer uses the WiX patch file `wix-patch.xml` to schedule custom actions:

- `postinstall.ps1` creates data folders, installs the service and registers the event log source.
- `cleanup.ps1` removes the service and cleans the data directory on uninstall.

UI assets such as `bannrbmp.jpg`, `dlgbmp.jpg` and `favicon.ico` reside in `packages/windows/ui`. Replace these files to customize the installer look.

## Install and Uninstall

Install the generated package silently:

```powershell
msiexec /i wazuh-agent_x.y.z.msi /q
```

Uninstall and log the process:

```powershell
msiexec /x wazuh-agent_x.y.z.msi /l*v uninstall.log
```
