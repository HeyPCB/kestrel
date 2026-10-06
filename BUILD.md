# Building a Kestrel quad

A 5-inch 6S freestyle / long-range quad around the three Kestrel boards. The boards are designs, not yet
made or flown (see `SPEC.md`, "What is not done"): build the first one on a bench supply, not a battery.

## 1. The off-the-shelf parts

These are the common choices in each class; any part with the same interface works.

| Part | A common choice | Interface to Kestrel |
|---|---|---|
| Frame | 5-inch, 30.5 mm stack, room for a digital VTX (e.g. the open-source TBS Source One V5); the 4in1 is 48 x 52 mm, so check it clears the frame's standoffs | four M3 x 30 mm screws through the stack |
| Motors (4) | 2207 / 2306 at 1750-1950 KV for 6S (12N14P: 14 poles) | three wires each through the 4in1's plated motor holes |
| Props | 5.1-inch tri-blade (e.g. 51433) | - |
| Battery | 6S 1100-1300 mAh, XT60 | XT60 pigtail (12 AWG) to BAT+ / BAT- |
| Bus capacitor | 1000 uF / 35 V low-ESR electrolytic | across BAT+ / BAT- on the 4in1 |
| Digital VTX | DJI O3 Air Unit, Walksnail Avatar HD or HDZero | JST-SH 6 to the FC's VTX port |
| Receiver | ExpressLRS 2.4 GHz nano receiver | JST-SH 4 to the FC's RX port (solder the pigtail to the receiver) |
| GPS antenna | 25 x 25 x 4 mm passive ceramic patch, 1575 MHz (a 1561-1602 MHz wideband patch covers BeiDou and GLONASS too) | its feed pin through the GPS board's centre hole |
| Buzzer | 5 V active buzzer (optional) | BZ+ / BZ- pads on the FC |
| LED strip | WS2812 (optional) | LED, 5V, GND pads on the FC |
| Stack hardware | M3 soft-mount grommets (the boards' holes are 4 mm), nylon nuts, 8-pin SH harness 80 mm | - |

## 2. The stack and the harnesses

```
 top     Kestrel H7 (arrow forward)           JST-SH 8 harness, 1:1, pin 1 = VBAT on both ends
         ── soft-mount grommets ──
 bottom  Kestrel 4in1 (battery pads to the rear)
```

* **FC to ESC**: a 1:1 JST-SH 8 harness (pin 1 to pin 1). Both boards use 1 VBAT, 2 GND, 3 CURR, 4 TLM,
  5-8 M1-M4. The FC's port is on its rear edge and the 4in1's on its front edge; an 80 mm harness folds
  between them. Do not plug in another brand's harness without checking its order.
* **Motors**: Betaflight's quad-X order. M1 rear-right, M2 front-right, M3 rear-left, M4 front-left; each
  motor's three wires go through its three plated holes (M1-M4 is printed by the middle one) in any
  order, soldered so the solder fills the hole: the hole joins the high side's copper on top to the low
  side's on the bottom. Direction is set in the AM32 configurator afterwards.
* **Battery**: XT60 pigtail to the 4 x 4 mm pads on the 4in1's rear edge: seen from the top, the left pad
  is BAT+ and the right one BAT- (printed "-"; the "+" did not fit on the silkscreen). Solder the 1000 uF
  capacitor across the same pads. Check the polarity twice: a reversed pack destroys the board.
* **VTX**: the FC's 6-pin port is 1 power (10 V, switchable), 2 GND, 3 FC TX, 4 FC RX, 5 GND, 6 SBUS.
  DJI, Walksnail and HDZero 6-pin cables do not all share one order: check yours against this port pin by
  pin and re-pin the cable if it differs. UART3 carries MSP for the OSD; SBUS (DJI goggles as a controller) lands on UART6 RX, which
  is also the R6 pad: use one or the other.
* **Receiver**: the FC's 4-pin port is 1 5 V, 2 GND, 3 FC RX (to the receiver's TX), 4 FC TX (to the
  receiver's RX), on UART7.
* **GPS**: a 1:1 JST-SH 6 harness. The GPS board's pin 3 receives the FC's TX, pin 4 drives the FC's RX,
  pins 5 / 6 are the compass's I2C. Mount it on top at the rear, patch side up, away from the VTX and the
  battery leads; note its arrow for `align_mag`.

### The FC's solder pads (bottom face)

The pads sit at 2.65-2.8 mm pitch, closer than a silk name needs, so only the end pads of each row are
named on the board (M5, R6, GND, LED, RST). Seen from the bottom, front edge up:

```
            front edge
   GND  10V  5V  BZ+  BZ-  LED        <- row along the front edge
                                  M5
                                  M6
                                  M7
                                  M8   <- column along the right edge
                                  T1     (seen from the bottom; the
                                  R1      FC's left edge from the top)
                                  T6
                                  R6
                 RST CLK DIO          <- SWD pads at the rear edge
            rear edge
```

M5-M8 are motor outputs 5-8 (octocopter or servos); T1 / R1 are UART1; T6 / R6 are UART6 (R6 is also
the VTX port's SBUS pin); BZ+ is 5 V and BZ- the switched side of the buzzer; LED is the WS2812 data
through 33 ohm.

## 3. Ordering the boards

Each board's repository holds its fab files in `fab/` ([kestrel-fc](https://github.com/HeyPCB/kestrel-fc), [kestrel-esc](https://github.com/HeyPCB/kestrel-esc), [kestrel-gps](https://github.com/HeyPCB/kestrel-gps)):
gerbers (zip), JLC BOM and CPL, STEP, schematic PDF. Every part has an LCSC number (`BOM.md`).

| Board | Stackup to order | Assembly |
|---|---|---|
| Kestrel H7 | 6 layers, 1.6 mm, 1 oz | both faces |
| Kestrel 4in1 | 6 layers, 1.6 mm; 2 oz outer copper recommended | both faces |
| Kestrel GPS | 4 layers, 1.6 mm, 1 oz | bottom face (top is the antenna's ground) |

## 4. First power-up, one board at a time

1. **ESC alone** on a bench supply at 12 V with a 0.3 A current limit, no motors. Measure 10 V at the
   gate rail and 3.3 V at an MCU's decoupling capacitor. Then flash the four AM32 bootloaders over SWD
   ([kestrel-esc: `firmware/am32/README.md`](https://github.com/HeyPCB/kestrel-esc/blob/main/firmware/am32/README.md)).
2. **FC alone** on USB: hold BOOT while plugging in for DFU, then flash Betaflight with the `KESTRELH7`
   target ([kestrel-fc: `firmware/betaflight/KESTRELH7/`](https://github.com/HeyPCB/kestrel-fc/tree/main/firmware/betaflight/KESTRELH7)) and paste its `defaults.txt`. Check that the gyro, baro
   and flash appear, and that the 3D model follows the board (fix `gyro_1_sensor_align` if not).
3. **FC + ESC** on the bench supply at 16 V, current limit 1 A: the FC's 5 V and 10 V pads should read
   5.0 V and about 9.85 V; Betaflight's battery voltage should match the supply.
4. **Motors, no props**: AM32 configurator through Betaflight passthrough; flash the `MAMBA_F55_G071`
   firmware to all four, set 14 poles, spin each motor in the Motors tab and fix its direction.
5. **GPS**: plug it in, wait out the 23-28 s cold start outdoors, check satellites and the compass
   heading in Betaflight.
6. **Before the first battery**: failsafe test with the props off, a smoke stopper on the first plug-in,
   then a short hover. Watch the 4in1's temperature after the first pack.
