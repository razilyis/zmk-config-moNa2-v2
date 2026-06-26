# moNa2-v2 PAW3222 branch

This branch configures moNa2-v2 for a PixArt PAW3222 trackball sensor.

## PAW3222 wiring

Pin assignment is based on the roBa_R schematic (same XIAO sensor connector layout).

### Firmware pin assignment

`boards/shields/mona2/mona2_r.overlay` is configured as follows:

| PAW3222 function | MCU pin | XIAO pin |
| --- | --- | --- |
| SCLK | `P0.05` | D5 |
| SDIO | `P0.04` | D4 |
| NCS | `P0.09` | NFC1 |
| MOTION | `P0.02` | D0 |

### PAW3222 breakout board v3 FFC connector

| FFC Pin | Signal |
| --- | --- |
| 1 | GND |
| 2 | MOTION |
| 3 | SDIO |
| 4 | NCS |
| 5 | SCLK |
| 6 | VCC 3.3V |
