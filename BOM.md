# Kestrel: the whole drone's bill of materials

## The three boards (JLC assembly, every part with an LCSC number)

| Board | Assembly BOM and placement | Priced, line by line |
|---|---|---|
| Kestrel H7 | [`kestrel_fc_bom.csv`](https://github.com/HeyPCB/kestrel-fc/blob/main/fab/kestrel_fc_bom.csv), [`kestrel_fc_cpl_jlc.csv`](https://github.com/HeyPCB/kestrel-fc/blob/main/fab/kestrel_fc_cpl_jlc.csv) | `costs/kestrel_fc_10.md` |
| Kestrel 4in1 | [`kestrel_esc_bom.csv`](https://github.com/HeyPCB/kestrel-esc/blob/main/fab/kestrel_esc_bom.csv), [`kestrel_esc_cpl_jlc.csv`](https://github.com/HeyPCB/kestrel-esc/blob/main/fab/kestrel_esc_cpl_jlc.csv) | `costs/kestrel_esc_10.md` |
| Kestrel GPS | [`kestrel_gps_bom.csv`](https://github.com/HeyPCB/kestrel-gps/blob/main/fab/kestrel_gps_bom.csv), [`kestrel_gps_cpl_jlc.csv`](https://github.com/HeyPCB/kestrel-gps/blob/main/fab/kestrel_gps_cpl_jlc.csv) | `costs/kestrel_gps_10.md` |

The totals per board are in `RESULTS.md`. They are live JLC catalogue prices at a 10-board build's tier,
read when the boards were built, plus JLC's Extended-part loading fee spread over the batch. The bare PCB
and the assembly labour are quoted by JLC at order time and are not in them. Solder pads and test points
are footprints, not parts, and are marked do-not-place.

Key parts, with the reason each was chosen in `SPEC.md`:

| Part | LCSC | Board | Per board |
|---|---|---|---|
| STM32H743VIT6 | C114409 | H7 | 1 |
| ICM-20602 | C97633 | H7 | 1 |
| BMI088 | C194919 | H7 | 1 |
| MS561101BA03-50 (MS5611) | C15639 | H7 | 1 |
| W25Q128JVSIQ | C97521 | H7 | 1 |
| LMR51430YFDDCR | C5219261 | H7 (2), 4in1 (1) | |
| STM32G071G8U6N | C707495 | 4in1 | 4 |
| LM5109BSD/NOPB | C544701 | 4in1 | 12 |
| CSD18540Q5B | C86513 | 4in1 | 24 |
| INA180A2IDBVR | C192764 | 4in1 | 1 |
| RE2512F3R0R5L (0.5 mOhm) | C46961744 | 4in1 | 2 |
| MAX-M10S-00B | C4153167 | GPS | 1 |
| LIS3MDLTR | C478483 | GPS | 1 |

## Bought separately

| Qty | Part | Notes |
|---|---|---|
| 1 | 5-inch frame with a 30.5 mm stack | room for the VTX and its camera |
| 4 | 2207 / 2306 motors, 1750-1950 KV, 12N14P | 6S; `motor_poles = 14` |
| 4+ | 5.1-inch tri-blade props | two CW, two CCW, plus spares |
| 1+ | 6S 1100-1300 mAh LiPo, XT60 | |
| 1 | XT60 pigtail, 12 AWG | to BAT+ / BAT- |
| 1 | 1000 uF / 35 V low-ESR electrolytic | across BAT+ / BAT- |
| 1 | Digital VTX with camera (DJI O3 Air Unit, Walksnail Avatar HD or HDZero) | 6-pin JST-SH; check its pin order |
| 1 | ExpressLRS 2.4 GHz receiver | JST-SH 4 pigtail soldered to it |
| 1 | 25 x 25 x 4 mm passive ceramic GNSS patch (1575 MHz, or 1561-1602 MHz wideband) | feed pin through the GPS board's centre hole |
| 1 | JST-SH 8 harness, 1:1, 80 mm | FC to 4in1 |
| 1 | JST-SH 6 harness, 1:1 | FC to GPS |
| 4 | M3 soft-mount grommets for 4 mm holes, M3 x 30 mm screws, nylon nuts | the stack |
| 1 | 5 V active buzzer | optional, BZ+ / BZ- |
| 1 | WS2812 LED strip | optional, LED / 5V / GND |
| 1 | ST-Link (or any SWD probe) | once, for the four AM32 bootloaders |
