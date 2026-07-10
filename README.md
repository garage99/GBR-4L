# GBR-4L

GBR-4L is a self-powered USB 2.0 carrier for four Semtech (formerly Sierra
Wireless) AirPrime EM7565 LTE modules. It is intended for use with
OpenMPTCProuter and for manufacture by JLCPCB.

## Rev 1.0 baseline

- Four EM7565 modules in M.2 Key B 3042 sockets
- One nano-SIM per modem
- USB 2.0 High-Speed upstream through a USB Type-C receptacle
- Four-port USB 2.0 hub
- 12 V external input
- Four independently controlled 3.3 V modem power rails
- Per-modem `RESET#`, `W_DISABLE1#`, status indication, and test points
- Four-layer PCB: signal / ground / power / signal
- Preferred outline: 160 mm x 100 mm

> EM7565 is an M.2 module, not a Mini PCIe card. The USB Type-C connector is an
> upstream-facing USB device port (UFP), even though it connects to the host PC.

## Repository layout

- `Hardware/KiCad/GBR-4L/` — KiCad 10 project
- `Hardware/Design/` — requirements, design decisions, and interface definitions
- `Hardware/BOM/` — preliminary and released bills of materials
- `Firmware/` — reserved for optional control firmware
- `Case/` — enclosure and mechanical data

## Current milestone

M1 (part selection and electrical architecture) is in progress. The initial
architecture is recorded in
[`Hardware/Design/GBR-4L_Rev1_Design_Baseline.md`](Hardware/Design/GBR-4L_Rev1_Design_Baseline.md).
No Gerber files are released yet.
