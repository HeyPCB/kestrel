# Kestrel: a 5-inch 6S FPV quad's electronics, built with heypcb-kicad

Three boards, designed here, plus the off-the-shelf parts every FPV build already buys:

| Board | What it is | Size | Layers |
|---|---|---|---|
| **Kestrel H7** ([kestrel-fc](https://github.com/HeyPCB/kestrel-fc)) | Flight controller: STM32H743, two IMUs, baro, 16 MB blackbox, 5 V and switchable 10 V BECs | 40 x 40 mm, 30.5 mm stack | 6 |
| **Kestrel 4in1** ([kestrel-esc](https://github.com/HeyPCB/kestrel-esc)) | 4-in-1 ESC: four AM32 motor controllers, 24 FETs, battery current sensing | 48 x 52 mm, 30.5 mm stack | 6 |
| **Kestrel GPS** ([kestrel-gps](https://github.com/HeyPCB/kestrel-gps)) | GNSS + compass on a 25 mm ceramic patch | 28 x 28 mm | 4 |

The off-the-shelf parts are the most popular modules in their class: a digital HD VTX (DJI O3 Air Unit,
or a Walksnail Avatar or HDZero unit on the same 6-pin harness), an ExpressLRS 2.4 GHz receiver,
22xx motors, 5-inch props and a 6S pack. `BUILD.md` lists them; `BOM.md` prices the boards.

Measured results (ERC, routing, DRC, fab gate) live in `RESULTS.md`, generated from each board
repository's `report.json`. Nothing in this file is a measurement of a built board: none has been made.

```
 6S pack ──XT60──┬── BAT+/BAT- pads ─ Kestrel 4in1 ── 12 motor wires ── 4 motors
                 │                        │ JST-SH 8: VBAT, GND, CURR, TLM, M1-M4
                 │                   Kestrel H7 ──JST-SH 6── digital VTX (10 V, MSP OSD, SBUS)
                 │                        ├──JST-SH 4── ELRS receiver (5 V, CRSF on UART7)
                 │                        ├──JST-SH 6── Kestrel GPS (5 V, UART2, I2C1)
                 │                        └── USB-C (configuration, DFU)
```

## Kestrel H7 flight controller

| | |
|---|---|
| MCU | STM32H743VIT6 (LQFP-100, 480 MHz, 2 MB flash), 8 MHz crystal, BOOT button, SWD pads |
| IMU 1 | ICM-20602 on SPI1, its own interrupt (Betaflight's gyro) |
| IMU 2 | BMI088 on SPI4 (INAV and ArduPilot; Betaflight has no BMI088 driver) |
| Gyro supply | Both IMUs on their own AP2112K LDO (`3V3_GYRO`), away from the BECs' switching |
| Baro | MS5611 on I2C1 at 0x77 (cover it with open-cell foam); chosen over the BMP280, whose KiCad 10 footprint's silk overlaps its own pad |
| Blackbox | W25Q128JV, 16 MB, on SPI3 |
| Power in | 2-6S from the ESC harness; SMAJ30A TVS; VBAT x 1/11 to an ADC (`vbat_scale 110`) |
| 5 V BEC | LMR51430YF buck, 1.1 MHz, 3.3 uH; 2 A budget for receiver, GPS, LED strip, LDOs |
| 10 V BEC | LMR51430YF, 6.8 uH, for the VTX; an AO3400A on PINIO1 (PE2) pulls its EN low to switch the VTX off |
| Ports | JST-SH: ESC 8-pin, VTX 6-pin, receiver 4-pin, GPS 6-pin; USB-C with USBLC6 ESD |
| Pads | M5-M8, UART1 and UART6, LED strip (33 ohm), buzzer (5 V + low-side switch), 5 V, 10 V, GND |

**Why these parts.** The H743 is the MCU of every current high-end FC (Betaflight, INAV and ArduPilot all
build for it), and the pin map follows Betaflight's `MATEKH743` target wherever the functions match, so
its timer and DMA assignments are proven ([kestrel-fc: `firmware/betaflight/KESTRELH7/config.h`](https://github.com/HeyPCB/kestrel-fc/blob/main/firmware/betaflight/KESTRELH7/config.h)). The ICM-42688-P,
today's most common FC gyro, has no KiCad 10 symbol, and this project never invents a symbol; the
ICM-20602 is the most-flown gyro that has one, and the BMI088 is ArduPilot's preferred IMU. JLC's stock
on 2026-10-05: STM32H743VIT6 1742, where the F405RGT6 had 29.

**Two IMUs on separate buses** so INAV and ArduPilot can use both and Betaflight loses nothing.
**The 10 V rail is a buck**: from 4S up it holds 9.85 V; on a nearly empty 3S pack it drops toward the
pack voltage, and on 2S it is the pack minus the dropout. The DJI O3 is specified down to 7.4 V input, so
it keeps running.

## Kestrel 4in1 ESC

| | |
|---|---|
| Motor control | 4 x STM32G071G8U6N, each running AM32 (`HARDWARE_GROUP_G0_H`, the `MAMBA_F55_G071` build) |
| Gate drive | 12 x LM5109B (WSON-8), 1 A peak, internal bootstrap diode, from an always-on 10 V LMR51430 rail |
| Power stage | 24 x CSD18540Q5B (60 V, SON 5 x 6), high side on the top face over its low side on the bottom |
| Back-EMF | 20k / 2.2k per phase (2.5 V at 25.2 V) against a 20k x 3 / 750 ohm virtual neutral |
| Battery sense | 2 x 0.5 mOhm 2512 low-side shunt + INA180A2: 12.5 mV/A on CURR (Betaflight `ibata_scale 125`) |
| Telemetry | All four MCUs' USART1 TX (open drain) on one TLM wire to the FC's UART4 |
| Protection | SMAJ30A on VBAT; six 10 uF / 50 V on the bus; solder a 1000 uF / 35 V low-ESR across the pads |
| Harness | JST-SH 8 in the Kestrel H7's order: VBAT, GND, CURR, TLM, M1-M4 |
| Motor and battery pads | 3 x 3 mm plated holes (1.5 mm drill) per phase; 4 x 4 mm battery pads marked + and - |

**Why AM32 on the G071.** BLHeli_32 has stopped; AM32 is the open firmware now shipped on new ESCs, and
`G0_H` is one of the three G071 pin groups that fit the 28-pin package (the others need PB1 or PB4,
which it lacks) and the one shipped on Mamba F55 and F65 ESCs. The board needs no custom firmware:
[kestrel-esc: `firmware/am32/README.md`](https://github.com/HeyPCB/kestrel-esc/blob/main/firmware/am32/README.md) maps every pin to its net.

**Why three half-bridge drivers per motor.** KiCad 10 has no symbol for the Fortior FD6288, the 3-phase
driver most ESCs use, and no other 3-phase driver in its library is rated for 6S. The LM5109B is, in a
4 x 4 mm WSON (JLC: 1412 in stock on 2026-10-05, 12 per board).

**Layout.** Each half bridge is stacked through the board: the phase (high-side source, low-side drain)
lands on the edge side with its motor pad, VBAT and GND on the middle side. Each phase has a copper zone
on F.Cu (the high side's source pads to the motor pad) and one on B.Cu (the low side's drain tab to the
same pad). The motor pad is a plated hole: the motor wire soldered through it joins the two faces, which
via stitching could not (the FETs' and pads' courtyards cover the zones, and the stitcher keeps vias off
courtyards). VBAT is poured on In3, an In2 zone over the bridges and F.Cu zones over the drain tabs; GND
is the In1 plane and the In2, In4, F.Cu and B.Cu pours. 3V3 for the MCUs comes from an L78L33 (SOT-89).
The board is 52 mm deep so the 10 V BEC fits along its front edge, under the FC harness.

**Current rating: not measured.** Conduction in the two FETs carrying a motor's current at 30 A is
2 x 30^2 x 1.8 mOhm = 3.2 W per motor before switching loss and copper. That is where a bench test
starts, not a rating. The board's default stackup is 1 oz copper; order 2 oz outer layers for a racing
build, and do a thermal run with a current-limited supply and one motor before the first flight.

## Kestrel GPS

| | |
|---|---|
| Receiver | u-blox MAX-M10S: GPS, Galileo, BeiDou and GLONASS at once; integrated LNA and SAW |
| Antenna | 25 x 25 mm passive ceramic patch on the top face's unbroken ground; feed pin in a 1.0 mm plated hole |
| Compass | ST LIS3MDL on I2C at 0x1E (Betaflight, INAV and ArduPilot drivers) |
| Power | XC6206 3.3 V LDO from the port's 5 V |
| Connector | JST-SH 6, 1:1 with the Kestrel H7's GPS port |

The RF feed from the patch's pin to RF_IN is one 2.5 mm track on B.Cu (a 42nd of a wavelength). The
compass sits in the corner farthest from the receiver. **Cold start every power-up (23-28 s by the data
sheet):** V_BCKP is left open because JLC stocks no backup cell with a KiCad footprint. **The IST8310
was planned and dropped**: its data sheet is not public, so its VPP, C1 and address pins could not be
wired from a source; the LIS3MDL's is.

## Firmware

* [kestrel-fc: `firmware/betaflight/KESTRELH7/config.h`](https://github.com/HeyPCB/kestrel-fc/blob/main/firmware/betaflight/KESTRELH7/config.h): a Betaflight unified target for the betaflight/config repo.
* [kestrel-fc: `firmware/betaflight/KESTRELH7/defaults.txt`](https://github.com/HeyPCB/kestrel-fc/blob/main/firmware/betaflight/KESTRELH7/defaults.txt): CLI defaults (ports, DShot, sensors, scales).
* [kestrel-esc: `firmware/am32/README.md`](https://github.com/HeyPCB/kestrel-esc/blob/main/firmware/am32/README.md): the ESC's pin map, first flash over SWD, and settings.

## What is not done

* No board has been made, flown or powered. Every rating above is derived from parts and layout.
* IMU alignment in the Betaflight target is derived from the footprint, not measured.
* Silkscreen names are partial. The 4in1 prints only "FC" and "-" at BAT-; the motor numbers and "+"
  found no free spot among its vias. The H7 names only the end pads of each pad row. `BUILD.md` maps
  every pad.
* INAV and ArduPilot targets are not written; the boards are wired so both can be (BMI088, flash, baro,
  compass, eight motor outputs).
* The patch antenna and the off-the-shelf modules are bought separately (see `BUILD.md`).
