# Net and schematic sheet plan

## Sheet mapping

| Existing KiCad sheet | Rev 1 responsibility |
|---|---|
| `Power_sch.kicad_sch` | 12 V input protection, two dual modem bucks, auxiliary 3.3 V, sequencing |
| `USB.kicad_sch` | Type-C UFP, ESD, USB2514B, crystal/clock, hub straps |
| `LTE_SLOT1.kicad_sch` | M.2 Key B socket 1, local power, controls, USB |
| `LTE_SLOT2.kicad_sch` | M.2 Key B socket 2, local power, controls, USB |
| `LTE_SLOT3.kicad_sch` | M.2 Key B socket 3, local power, controls, USB |
| `LTE_SLOT4.kicad_sch` | M.2 Key B socket 4, local power, controls, USB |
| `SIM.kicad_sch` | Four nano-SIM sockets, ESD, decoupling |
| `LED_RESET.kicad_sch` | Status LEDs, reset switches, W_DISABLE controls, GPIO header |

## Global net naming

- Input/system: `+12V_IN`, `+3V3_SYS`, `GND`, `USB_VBUS_SENSE`
- Modem rails: `+3V3_M1` through `+3V3_M4`
- Hub downstream USB: `USB_M1_D+/-` through `USB_M4_D+/-`
- Hub upstream USB: `USB_UP_D+`, `USB_UP_D-`
- Per-slot control: `M1_PWR_EN`, `M1_PWR_GOOD`, `M1_RESET#`,
  `M1_W_DISABLE#`, `M1_FULL_CARD_POWER_OFF#`, `M1_WAN_LED#` (repeat M2-M4)
- Per-slot SIM: `M1_UIM_PWR`, `M1_UIM_DATA`, `M1_UIM_CLK`,
  `M1_UIM_RESET`, `M1_SIM_DETECT` (repeat M2-M4)

Active-low names retain `#` to match the module documentation.
