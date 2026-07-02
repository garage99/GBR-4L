# GBR-4L Rev 1.0 design baseline

Status: engineering baseline for schematic capture<br>
Target CAD: KiCad 10<br>
Target fabrication: JLCPCB, four layers

## 1. Purpose

GBR-4L connects four AirPrime EM7565 LTE modems to one OpenMPTCProuter host over
USB 2.0. Each modem has its own nano-SIM, controllable power rail, reset,
wireless-disable signal, status LED, and measurement points.

This document resolves ambiguities in the original project brief. It is the
source of truth until superseded by an approved revision.

## 2. Corrections to the original brief

1. **Module form factor:** EM7565 is M.2 Key B, size 3042. It is not Mini PCIe.
2. **USB role:** the Type-C receptacle is an upstream-facing device port (UFP).
   The attached computer/OpenMPTCProuter is the USB host.
3. **RF routing:** cellular RF remains on the EM7565 module and exits through
   its miniature coaxial connectors. The carrier PCB does not route cellular
   RF transmission lines. Placement must preserve antenna-cable access and
   keep switching regulators away from the module antenna-connector end.
4. **Data-rate expectation:** Rev 1 uses USB 2.0 High-Speed. Aggregate practical
   throughput is therefore bounded by the single USB 2.0 upstream link and
   will be far below the sum of four Cat-12 modem peak rates.

## 3. Electrical architecture

### 3.1 Power tree

Nominal input is 12 V DC. Design input range is 10.8 V to 13.2 V.

```text
12V_IN
  |
  +-- input fuse / reverse-polarity protection / TVS
  |
  +-- dual 3 A buck A --> MODEM_1_3V3, MODEM_2_3V3
  +-- dual 3 A buck B --> MODEM_3_3V3, MODEM_4_3V3
  +-- auxiliary buck --> SYS_3V3 (USB hub, indicators, control)
```

Each modem supply:

- nominal 3.3 V;
- sized for at least 1.5 A continuous;
- tolerates 2.5 A inrush;
- independently enabled;
- includes local bulk and high-frequency decoupling at the M.2 socket;
- exposes voltage, ground, enable, and power-good test points.

Two TPS54394 dual 3 A synchronous buck regulators are the initial choice. Each
channel feeds only one modem and its `EN` pin provides hard power control.
TPS22990 load-switch footprints are therefore not populated in the baseline;
they remain a fallback if regulator sequencing or discharge behavior proves
unsuitable during prototype testing.

The system must not draw modem operating power from USB VBUS. VBUS is used only
for attach detection and hub requirements.

### 3.2 USB

Initial hub controller: Microchip USB2514B, four downstream ports, USB 2.0
High-Speed, self-powered configuration.

Type-C UFP requirements:

- USB 2.0-only receptacle;
- 5.1 kOhm pull-down on CC1 and CC2;
- low-capacitance ESD protection on D+, D-, CC1, CC2, and VBUS;
- A6/B6 tied as D+ and A7/B7 tied as D- at the receptacle;
- controlled 90 Ohm differential routing over uninterrupted ground;
- no USB Power Delivery controller.

All four hub downstream pairs route directly to the corresponding EM7565 USB
2.0 pins. Test pads must be stubs of minimum practical length and are optional
for production.

### 3.3 Modem control

For each slot:

- `FULL_CARD_POWER_OFF#`: default deasserted; available to the control header;
- `RESET#`: normally deasserted, with local momentary reset and header access;
- `W_DISABLE1#`: normally deasserted, with header access;
- `WAN_LED#`: drives an external LED through a transistor or buffer;
- unused/reserved M.2 pins remain no-connect as required by the EM7565
  specification.

Controls must have deterministic defaults so all four modems enumerate without
an external controller. A header permits later GPIO control from the router or
an optional controller board.

### 3.4 SIM

One front-entry push-pull nano-SIM socket is assigned to the primary SIM interface of each
EM7565.

- Protect `UIM_PWR`, `UIM_DATA`, `UIM_CLK`, and `UIM_RESET` with a
  low-capacitance SIM ESD array placed at the socket.
- Place SIM decoupling at the socket.
- Keep `UIM_DATA` and `UIM_CLK` capacitance low.
- Keep total UIM power and ground path impedance below 1 Ohm.
- Route each SIM only to its associated modem; do not multiplex in Rev 1.

## 4. Power budget

| Load | Quantity | Design current each | Total at rail |
|---|---:|---:|---:|
| EM7565 continuous maximum | 4 | 1.5 A at 3.3 V | 6.0 A |
| EM7565 inrush allowance | 4 | 2.5 A transient | sequence turn-on |
| USB hub and logic | 1 set | 0.5 A budget at 3.3 V | 0.5 A |

The four modem rails represent 19.8 W at full continuous load. Including
conversion loss, logic, margin, and start-up behavior, the external adapter
shall be rated **12 V, 4 A minimum**; **12 V, 5 A recommended**.

All four modem rails must not start simultaneously. Default sequencing is
approximately 250 ms between channels, implemented by RC delays or the optional
controller. The exact method is finalized during schematic review.

## 5. PCB constraints

- Four layers: Top / solid GND / power / Bottom.
- Keep USB pairs on a single layer where possible and never cross a plane split.
- Calculate USB geometry from the selected JLCPCB stack-up before routing.
- Keep buck-converter switch nodes compact and away from M.2 antenna connectors,
  SIM traces, USB pairs, and board edges used by antenna cables.
- Use wide copper pours and multiple vias for every modem supply and return.
- Provide thermal copper and via arrays under exposed-pad regulators.
- Keep a mechanical keep-out above and below each 42 mm x 30 mm module.
- Preserve access for three module antenna connectors and their cables.
- Preferred board size is 160 mm x 100 mm; increase only after placement review.

## 6. Verification gates

Before Gerber release:

1. Confirm exact EM7565 SKU and current product technical specification.
2. Confirm M.2 connector contact height and mounting hardware against enclosure.
3. Review every EM7565 reserved pin and power/control default.
4. Run ERC with no unexplained errors.
5. Validate power converters for 2.5 A load steps and staggered start-up.
6. Confirm USB2514B self-powered strap/EEPROM configuration.
7. Run USB differential-impedance and return-path review.
8. Run PCB DRC with the selected JLCPCB four-layer rules.
9. Generate and inspect BOM, CPL, Gerber, drill, and fabrication drawings.
10. Bench-test one modem first, then all four at maximum transmit duty cycle.

## 7. Reference documents

- Semtech/Sierra Wireless, AirPrime EM7565 Product Technical Specification.
- Microchip, USB251xB/xBi USB 2.0 Hi-Speed Hub Controller Data Sheet.
- Texas Instruments, TPS54394 3 A Dual-Channel Synchronous Step-Down Switcher
  Data Sheet.
- USB Type-C Cable and Connector Specification.
