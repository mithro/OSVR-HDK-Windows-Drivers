# OSVR HDK Windows Drivers
(and related code/data for improving the hardware experience)

> Maintained at <https://github.com/OSVR/OSVR-HDK-Windows-Drivers>
>
> For details, see <http://osvr.github.io>
>
> For support, see <http://support.osvr.com>

## Dependencies

Most of the scripts here require the Windows Driver Kit - version 10 is the default, so if that's what you have, you'll probably be able to minimize how many files you edit.

The executable installer for the driver requires the latest NSIS 2.x release.

To fully enjoy these files, especially on Windows 8/8.1, you'll need a code-signing certificate (self-signed if you're using them on a test-signing-mode machine). Microsoft makes the rules for signed drivers, sorry.

## Directories and Output Files

### `/CDC/`

- `osvr_cdc.inf`
- `osvr_cdc.cat` - generated by batch file from inf and code signing.

Signed inf and catalog files to enable the CDC serial port on the HDK (used for firmware upgrades, etc - not needed for basic usage). Suitable for Windows 8.1 and older - Windows 10 includes a universal CDC driver. Can be installed on any Windows-supported architecture (including ARM) using `pnputil` or the "have-disk" method in Device Manager.

### `/CDC-NSIS-Installer/`

- `OSVR-HMD-CDC-Driver.exe` - generated by batch file using inf and cat from above, NSIS 3.x compiler, and code signing tools.

This is a simple installer/bootstrap tool that supports installing the `inf` and `cat` described above into the system driver store (and onto any matching connected device). It supports x86 and x64, but not ARM or Itanium because it's a native-code-compiled binary.

By default it will pop up some dialog boxes as installers tend to do. For a fully silent install, run with the argument `/S` (*case sensitive*), and if you don't want even a UAC prompt either, you should run it as admin/elevated.

Uses `dpinst` internally, so it will append to the log in `%SystemRoot%\DPINST.LOG` if you're curious. DPInst, not the NSIS wrapper, is the one that will create the uninstall item in "Programs and Features" aka "Add/Remove Programs".

### `/Metadata/`

Device Metadata (and Device Stage data) - Provides a nicer experience in Devices and Printers. The actual metadata files (cab files in disguise) are generated with Python and Jinja2 from the source files there.

Includes its own standalone NSIS installer, but...

### `/Combined-Installer/`

An NSIS installer for both the CDC driver and metadata.

No uninstaller for the device metadata because it's tiny, harmless, and installed by a PowerShell script using some Win32 API functions so figuring out an uninstaller would be a pain.

## License and Vendored Projects

This project: Licensed under the Apache License, Version 2.0.

- `/vendor/atmel_usb_dfu` - Atmel USB DFU drivers (based on libusb-win32 1.2.2.0) - under the GPL or LGPL (their documentation conflicts)
