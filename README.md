# moNa2-v2 PAW3222 with zw3021 branch

This branch configures moNa2-v2 for a PixArt PAW3222 trackball sensor.

## PAW3222 wiring

The firmware assumes the `7pin-to-6FFC_Adapter-for-AroundForty` adapter board
is used between the moNa2 7-pin connector and the PAW3222 breakout board v3
6-pin FFC connector (reversed FFC cable).

### Signal path through adapter

The adapter + reversed FFC cable shuffles SCLK/CS/MOTION:

| MCU pin | Keyboard signal | → Adapter → reversed FFC → | PAW3222 pin |
| --- | --- | --- | --- |
| `P0.09` (NFC1) | CS | 7pin-2 → FFC-2 → FFC-5 | **SCLK** |
| `P0.04` (D4) | SDIO | 7pin-6 → FFC-4 → FFC-3 | **SDIO** |
| `P0.02` (D0) | MOTION | 7pin-7 → FFC-3 → FFC-4 | **NCS** |
| `P0.05` (D5) | SCLK | 7pin-1 → FFC-5 → FFC-2 | **MOTION** |

### Firmware pin assignment

`boards/shields/mona2/mona2_r.overlay` compensates for the adapter routing:

| PAW3222 function | MCU pin | Reason |
| --- | --- | --- |
| SCLK | `P0.09` | Keyboard CS pin reaches PAW3222 SCLK through adapter |
| SDIO | `P0.04` | Keyboard SDIO pin reaches PAW3222 SDIO through adapter |
| NCS | `P0.02` | Keyboard MOTION pin reaches PAW3222 NCS through adapter |
| MOTION | `P0.05` | Keyboard SCLK pin reaches PAW3222 MOTION through adapter |

### PAW3222 breakout board v3 FFC connector

| FFC Pin | Signal |
| --- | --- |
| 1 | GND |
| 2 | MOTION |
| 3 | SDIO |
| 4 | NCS |
| 5 | SCLK |
| 6 | VCC 3.3V |

### Direct wiring (without adapter)

If connecting the PAW3222 directly without the adapter, change the pin assignment to:

| PAW3222 function | MCU pin |
| --- | --- |
| SCLK | `P0.05` |
| SDIO | `P0.04` |
| NCS | `P0.09` |
| MOTION | `P0.02` |
