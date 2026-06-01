RadeonTop
=========

View your GPU utilization, both for the total activity percent and individual blocks.

Requires access to /dev/dri/cardN files or the PCI sysfs resourceN node (root privileges).

![Screenshot from 2019-10-19 01-34-50_cut](https://user-images.githubusercontent.com/11575/67134324-fdec5300-f211-11e9-8597-394d9c062fe7.png)

Supported cards
---------------

R600 and up, even Southern Islands should work fine.
R300-class integrated graphics (RS400/RS480/RS482/RS485) are also supported:
they report engine-busy in `RBBM_STATUS` rather than `GRBM_STATUS`.  The radeon
DRM read-reg ioctl rejects every register on this pre-R600 class, so RadeonTop
reads `RBBM_STATUS` (`0x0E40`) directly through the BAR2 PCI sysfs resourceN
node; `radeontop -m` forces that path on any card.  On this class the total GPU
gauge is the reliable signal and the per-block gauges are a coarse R300-family
map, not the richer R600+ counter set.  In dump mode, RS480-class systems also
emit a one-line `#` header with `AGP_BASE_2`, `GART_FEATURE_ID`, and
`GART_BASE` when the steinmarder `radeon_rs480_candidate_gart_mc_regs` debugfs
surface is present and readable.
Works with both the open drivers and AMD Catalyst.

For the Catalyst driver, only the direct-MMIO path is supported, read through
the PCI sysfs resourceN node.

The total GPU utilization is also valid for OpenCL loads; the other blocks
are only useful in GL loads.

Translations
------------

If you'd like to translate RadeonTop to your own language, please go here:

https://translations.launchpad.net/radeontop

Running
-------

#### Prerequisites

* libdrm
* libncurses
* libpciaccess
* libxcb


Simply start radeontop and it auto-selects the first supported GPU:

    ./radeontop


Running radeontop on a bus 0f:

    ./radeontop -b 0f


Writing values to stdout instead of showing a GUI:

    ./radeontop -d -


Getting all options:

    ./radeontop --help


Building
--------

#### Prerequisites
* all run time prerequisites with dev files
* gcc / clang
* pkgconf

### Building
If all prerequisites are fullfilled, it can be build by simply running:

    make

#### Build options

Build options can be specified to having the following variables being set to "1"

    nls     enable translations, default on
    debug   enable debug symbols, default off
    nostrip disable stripping, default off
    plain   apply neither gcc's -g nor -s.
    xcb     enable libxcb to run unprivileged in Xorg, default on
    amdgpu  enable amdgpu usage reporting, default auto (requires libdrm >= 2.4.63)


Example:

    make amdgpu=1 xcb=1

This will build radeontop with amdgpu reporting and xcb support.
