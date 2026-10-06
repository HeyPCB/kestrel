# Kestrel: measured results

Generated on 2026-10-06 from each board repository's `report.json`, written by the heypcb-kicad pipeline. Nothing here is typed by hand.

| Board | Frame | ERC | Parts (LCSC) | Routing | Tracks / vias | DRC | DFM | Fab gate |
|---|---|---|---|---|---|---|---|---|
| Kestrel H7 flight controller | 40.0 x 40.0 mm, 6 layers | 0 errors, 0 warnings | 93 bound, 0 unsourced | 277 -> 0 open | 1258 / 125 | 0 errors, 0 warnings, 0 unconnected, parity 0 (fresh) | 0 errors (jlcpcb_6layer) | ready |
| Kestrel 4in1 ESC | 48.0 x 52.0 mm, 6 layers | 0 errors, 4 warnings | 148 bound, 0 unsourced | 443 -> 0 open | 1830 / 215 | 0 errors, 0 warnings, 0 unconnected, parity 0 (fresh) | 0 errors (jlcpcb_6layer) | ready |
| Kestrel GPS | 28.0 x 28.0 mm, 4 layers | 0 errors, 1 warnings | 12 bound, 0 unsourced | 38 -> 0 open | 116 / 6 | 0 errors, 0 warnings, 0 unconnected, parity 0 (fresh) | 0 errors (jlcpcb_4layer) | ready |

## ERC warnings

Each warning in each board repository's `fab/erc.json`, with the reason the design means it.

| Board | Type | Pins | Why |
|---|---|---|---|
| Kestrel 4in1 ESC | pin_to_pin | Symbol U8 Pin 10 [PA4, Bidirectional, Line]; Symbol #FLG118 Pin 1 [Power output, Line] | AM32's per-motor current input, tied to GND: the board measures battery current once, for the FC |
| Kestrel 4in1 ESC | pin_to_pin | Symbol U12 Pin 10 [PA4, Bidirectional, Line]; Symbol #FLG118 Pin 1 [Power output, Line] | AM32's per-motor current input, tied to GND: the board measures battery current once, for the FC |
| Kestrel 4in1 ESC | pin_to_pin | Symbol U16 Pin 10 [PA4, Bidirectional, Line]; Symbol #FLG118 Pin 1 [Power output, Line] | AM32's per-motor current input, tied to GND: the board measures battery current once, for the FC |
| Kestrel 4in1 ESC | pin_to_pin | Symbol U4 Pin 10 [PA4, Bidirectional, Line]; Symbol #FLG118 Pin 1 [Power output, Line] | AM32's per-motor current input, tied to GND: the board measures battery current once, for the FC |
| Kestrel GPS | pin_to_pin | Symbol U2 Pin 9 [SDO/SA1, Bidirectional, Line]; Symbol U3 Pin 2 [VO, Power output, Line] | the LIS3MDL's SA1 tied to Vdd_IO selects I2C address 0x1E |

## Parts cost per board at a 10-board build

From each board's exported BOM (live JLC catalogue prices at the batch's tier; JLC's Extended-part loading fee spread over the batch). The bare PCB and assembly labour are not included.

| Board | Parts | BOM per board incl. Extended fees | BOM lines | Unpriced |
|---|---|---|---|---|
| Kestrel H7 flight controller | $40.49 | $46.49 | 41 (20 Extended) | none |
| Kestrel 4in1 ESC | $41.10 | $44.70 | 25 (12 Extended) | none |
| Kestrel GPS | $14.41 | $15.31 | 8 (3 Extended) | none |

Not measured: anything on a built board. None has been made, powered or flown.
