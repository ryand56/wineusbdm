# wineusbdm

> [!NOTE]
> This doesn't fully integrate with CodeWarrior yet, but I am working on packages for both Nix and Arch. WINEDLLPATH should be to the /lib/wine folder of the built library. I've mostly experienced bugs with CodeWarrior and Wine however when ran somewhere else than directly.

A DLL for use with Wine to allow use of the USBDM with CodeWarrior.

USBDM is software and hardware for flashing and debugging some
microcontrollers. I need to use it for a class I'm taking.

The IDE and toolchain run fine in Wine, except for the debugging and
flashing part. It seems like the USB support in Wine isn't really there
yet.

The Wine project implements Windows DLLs using code that runs on Linux.
I can use the same tools to implement the DLL that the IDE uses to talk
to the hardware.

This repository contains some code that forwards calls from the DLL
to the native Linux library.

The two symbols `USBDM_ControlInterface` and `USBDM_ICP_SetCallback`
are currently unimplemented. The first doesn't seem to exist in the
Linux library, and the second would need a simple wrapper to translate
between calling conventions (but I haven't needed it yet).

## Building

This assumes you have the 32-bit version of the USBDM library in the
library search path.

```
winegcc usbdm.spec -shared -m32 -o usbdm.4.dll usbdm.c -lusbdm
```

Alternatively, a Nix flake and derivation is included in this
repository.

## Usage

One way is to replace the usbdm.4.dll file in
`C:\Program Files\Program Files\Freescale\CWS12v5.1\Prog\gdi`.

Another way, without modifying any files, is running the program with
`WINEDLLOVERRIDES` set:
```sh
WINEDLLPATH=<path> WINEDLLOVERRIDES=usbdm.4=b wine IDE.exe
```

# Cache

There is a binary cache for this repository:

```nix
nix = {
    package = pkgs.nixVersions.latest;

    settings = {
      experimental-features = [
        "nix-command"
        "flakes"
      ];

      substituters = [
        "https://cache.ryand.ca/nixfiles/"
      ];

      trusted-public-keys = [
        "nixfiles:lY5Oo9DfxKPS6cQR66JdFuXFUjYps9Mep/hbqJ0uo+Q="
      ];
    };
};
```

## License

It seems like USBDM itself is GPL, so this is GPL.
