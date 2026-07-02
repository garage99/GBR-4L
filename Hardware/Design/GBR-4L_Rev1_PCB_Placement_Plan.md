# GBR-4L Rev 1 PCB placement plan

Status: mechanical baseline before footprint freeze  
Board: 160 mm x 100 mm, 1.6 mm, four copper layers

## Layer intent

| Layer | Primary use |
|---|---|
| F.Cu | Components, USB differential pairs, short local power routing |
| In1.Cu | Uninterrupted ground plane |
| In2.Cu | 12 V and five 3.3 V power regions |
| B.Cu | Low-speed control and secondary power routing |

Do not split the ground plane below USB, SIM, clock, or modem control traces.
Any layer transition for USB requires adjacent ground stitching vias.

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

## Footprints required before electrical placement

- Exact M.2 socket height and manufacturer footprint.
- TE Connectivity 2452808-1 nano-SIM footprint from its controlled drawing.
- Final 12 V input connector, blade-fuse holder, PMOS, and inductor choices.
- Final Type-C receptacle footprint and shell-hole geometry.
- Crystal load-capacitance and package selection.

The current PCB contains only the layer stack, outline, mounting holes, and
placement annotations. Schematic-to-PCB footprint import and routing must wait
until the items above are frozen; placeholder connector footprints would make
the mechanical design misleading.

## Placement review gate

- Import all schematic footprints and verify zero missing assignments.
- Check M.2 module and antenna-cable keep-outs in 3D.
- Confirm SIM insertion direction and finger clearance.
- Confirm all edge controls against the enclosure.
- Calculate 90-ohm USB geometry from the selected JLCPCB stack-up.
- Run PCB DRC before beginning routing.
