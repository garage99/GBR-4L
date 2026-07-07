# GBR-4L Rev 1 PCB placement plan

Status: functional placement and plane baseline before detailed routing
Board: 160 mm x 100 mm, 1.6 mm, four copper layers

## Layer intent

| Layer | Primary use |
|---|---|
| F.Cu | Components, USB differential pairs, short local power routing, GND pour |
| In1.Cu | Uninterrupted ground plane |
| In2.Cu | 12 V and five 3.3 V power regions |
| B.Cu | Low-speed control, secondary power routing, GND pour |

Do not split the ground plane below USB, SIM, clock, or modem control traces.
Any layer transition for USB requires adjacent ground stitching vias.

`In1.Cu` now contains the filled `IN1_SOLID_GND` zone, inset 0.5 mm from the
board edge with 0.25 mm local clearance.

`F.Cu` and `B.Cu` also contain filled GND pours inset 0.5 mm from the board
edge. They use 0.25 mm local clearance, 0.30 mm thermal gaps and spokes, and
remove isolated copper islands below 5 mm². The 44 GND stitching vias connect
both outer pours to the uninterrupted `In1.Cu` reference plane.

`In2.Cu` now contains six isolated filled regions:

- `+12V_PROTECTED` and `+3V3_SYS` in the upper half;
- `+3V3_M1` through `+3V3_M4` in four independent modem columns.

Adjacent regions have a 1.0 mm nominal boundary gap and 0.30 mm zone
clearance.

## Via baseline

- Each modem rail (`+3V3_M1` through `+3V3_M4`) has a four-via connection
  array below its M.2 socket: 1.20 mm diameter with a 0.60 mm finished drill.
- `+12V_PROTECTED` has a four-via 1.50/0.80 mm array and `+3V3_SYS` has a
  four-via 1.20/0.60 mm array in their respective upper power regions. A
  second two-via 1.20/0.60 mm `+3V3_SYS` feed connects the USB hub locally,
  with one additional local feed at its reset pull-up.
- The ground plane has 67 stitching and thermal vias around the board
  perimeter, beside the four modem power arrays, and at both Type-C GND
  contacts: 0.60 mm
  diameter with a 0.30 mm finished drill. This count includes a dedicated via
  and short 0.50 mm ground connection at the USB ESD clamp plus individual
  returns for the shield termination parts and the USB VBUS detector divider.
- Three additional 0.60/0.30 mm vias connect both Type-C shell ends and the
  R3/C1 termination node between the outer layers on `USB_SHIELD`. This net
  remains intentionally separate from digital GND.
- Two 0.45/0.20 mm signal vias route `USB_VBUS_DET` from the hub to the local
  divider while avoiding the already-routed reset and decoupling area.
- Two additional 0.45/0.20 mm signal vias route `USB_VBUS` from the Type-C
  entry and ESD clamp to the detector divider.
- Four additional 0.45/0.20 mm signal vias route the two Type-C CC pins to
  their pull-down resistors while leaving the USB2 D+/D- corridor open.
- All 107 vias are through vias from `F.Cu` to `B.Cu`; all nine copper zones
  have been refilled after placement.

These are plane-access points, not completed load connections. The power
arrays must be tied to their regulator and M.2 power pads with short, wide
copper during detailed routing. Add local ground return vias beside USB layer
transitions and decoupling capacitors when their final positions are frozen.

The five 3.3 V contacts on each M.2 socket are now fanned out on `F.Cu` to its
four-via power array. The contact exits use 0.25 mm neck-downs, merge through
0.50 mm branches, and transition to 1.00 mm trunks. This completes the local
socket-to-plane connection for `+3V3_M1` through `+3V3_M4`; the regulator side
of each power region remains to be routed.

## Provisional net classes

- `USB2_HS_PROVISIONAL`: 0.20 mm width / 0.20 mm pair gap. Replace these
  provisional values with the 90-ohm geometry calculated from the selected
  JLCPCB stack-up before routing.
- `MODEM_3V3_POWER`: 1.00 mm track, 1.20/0.60 mm via.
- `INPUT_12V_POWER`: 1.50 mm track, 1.50/0.80 mm via.

These are routing defaults, not proof of current capacity or impedance. Power
rails should use pours where practical and must be validated thermally.

## Mechanical baseline

- Rectangular 160 mm x 100 mm Edge.Cuts outline.
- Four M3 mounting holes, each centered 5 mm from its two adjacent edges.
- Maintain mounting-hole copper and component keep-outs after enclosure review.
- User-accessible connectors, SIM sockets, LEDs, and buttons face board edges.

## Functional placement

1. Place the USB Type-C receptacle and ESD device at one board edge.
2. Place USB2514B, its 24 MHz crystal, and upstream passives immediately behind
   the Type-C receptacle.
3. Arrange the four M.2 3042 sockets so module antenna connectors and coax
   cables remain accessible.
4. Place each nano-SIM socket near its associated modem and at an accessible
   edge.
5. Group the 12 V protection stage and five buck regulators away from USB,
   SIM, and the antenna-connector ends of all modem modules.
6. Keep each buck switch-node copper compact; do not route it on an inner
   layer.

## Footprint status

- TE Connectivity 2199230-1 M.2 Key B footprint is captured from customer
  drawing C-2199230 Rev B4.
- TE Connectivity 2452808-1 nano-SIM footprint is captured from customer
  drawing C-2452808 Rev C1; verify the first incoming part against the land
  pattern before assembly release.
- GCT USB4105 signal and shell tails are captured from the manufacturer
  drawing with the four physically merged contact pairs represented as single
  pads.
- Final 12 V input connector, blade-fuse holder, PMOS, and inductor choices.
- Crystal load-capacitance and package selection.

The current PCB contains the layer stack, outline, mounting holes, all 188
schematic footprints, and all 287 schematic nets. Four M.2 sockets are anchored
on a common line with 30 mm x 42 mm module envelopes reserved below them. The
12 V input and USB Type-C receptacle are anchored at opposite board edges. All
remaining parts are collision-free in the upper component band.

The USBLC6-2SC6 ESD clamp (`U1`) is now placed directly behind the Type-C
receptacle. Its ground pin has a dedicated plane via. The `+3V3_SYS` inductor
`L801` was relocated within the same upper power region to open this protected
USB entry corridor.

The 1 MΩ / 4.7 nF shield termination (`R3`/`C1`) is placed below the Type-C
receptacle. Both parts have individual GND vias, while their `USB_SHIELD` pads
join at a dedicated via and connect to the shell on `B.Cu`.

The two 5.1 kΩ Type-C pull-downs (`R1`/`R2`) are grouped below the connector
and each has an individual GND via. Their `USB_CC1` and `USB_CC2` sides are
routed to the Type-C receptacle with short `F.Cu` escapes and `B.Cu` vertical
runs, keeping the future USB2 D+/D- routing corridor clear.

The USB2514B hub (`U2`) is placed at (145 mm, 55 mm), between the Type-C entry
and four modem sockets. Its 4.1 mm exposed GND pad contains a 2 x 2 array of
0.60/0.30 mm thermal vias. These vias are open on the component side and
tented on `B.Cu`; confirm the selected assembler accepts this via-in-pad
construction before release.

The 24 MHz crystal (`Y1`) and 12 pF C0G load capacitors (`C4`/`C5`) are placed
immediately left of `U2`. Both oscillator traces are routed directly on
`F.Cu`, without vias, and each load capacitor has an individual GND via.

The hub reference filter and bias parts (`C2`, `C3`, and `R6`) are also placed
at their associated `U2` pins. Each sensitive node is routed directly on
`F.Cu`, and every part has its own short GND return and plane via.

Four 100 nF capacitors (`C7` through `C10`) and the 4.7 µF bulk bypass (`C11`)
surround `U2`. Their 3.3 V branches are routed directly to the nearest hub
power pins, and all five parts use individual GND vias. The resulting
decoupling network is tied to the `+3V3_SYS` inner region through two local
power vias and a 1.00 mm `F.Cu` trunk.

The hub reset network (`R7`/`C6`) is placed above the right side of `U2`.
`HUB_RESET#` uses a short `B.Cu` escape between two vias to pass the
decoupling area, with a local `+3V3_SYS` pull-up via and a dedicated capacitor
GND return.

The USB VBUS detector divider (`R4`/`R5`) is placed in the same USB support
area near `U2`. Its midpoint is routed to `U2` pin 27 (`USB_VBUS_DET`) using
two small signal vias and a short `B.Cu` segment. `R5` has a local GND return
via. The `USB_VBUS` side of `R4` is now tied to both Type-C VBUS contacts and
the USB ESD clamp VBUS pin through a short `F.Cu` entry trunk and a `B.Cu`
escape to the divider.

This is the functional anchor baseline, not the final local placement. Buck
converter loops, hub passives, SIM protection, modem decoupling, buttons, and
LEDs must next be pulled tightly around their associated primary components.

The two 0.65 mm USB4105 locating holes are presently shown on `F.Fab` rather
than emitted as NPTH holes. KiCad 10.0.4 CLI aborts during DRC when those NPTH
holes are combined with the adjacent fine-pitch pads. They must be restored as
NPTH holes and verified in a newer KiCad release before fabrication output.

Electrical nets remain authoritative in the schematic. The current PCB has
all 287 schematic nets imported through KiCad's **Update PCB from Schematic**
workflow.

## Placement review gate

- Verify the imported set remains at 287 schematic nets after any schematic
  change and **Update PCB from Schematic** operation.
- Verify zero missing footprint assignments.
- Check M.2 module and antenna-cable keep-outs in 3D.
- Confirm SIM insertion direction and finger clearance.
- Confirm all edge controls against the enclosure.
- Calculate 90-ohm USB geometry from the selected JLCPCB stack-up.
- Run PCB DRC before beginning routing.
