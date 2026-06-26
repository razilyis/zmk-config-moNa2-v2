# moNa2-v2 PAW3222 branch

This branch configures moNa2-v2 for a PixArt PAW3222 trackball sensor.

## PAW3222 wiring

The current firmware assumes the `7pin-to-6FFC_Adapter-for-AroundForty`
adapter board is used between the moNa2 7-pin connector and the PAW3222
6-pin FFC connector.

### moNa2 7-pin connector

| moNa2 pin | Signal |
| --- | --- |
| 1 | SCLK |
| 2 | CS |
| 3 | GND |
| 4 | 3.3V |
| 5 | NC |
| 6 | SDIO |
| 7 | MOTION |

### AroundForty adapter net mapping

| moNa2 7-pin | Adapter net | PAW3222 signal |
| --- | --- | --- |
| 1 SCLK | `sck` | MOTION |
| 2 CS | `SS` | SCLK |
| 3 GND | `GND` | GND |
| 4 3.3V | `vcc` | VCC |
| 5 NC | unconnected | unconnected |
| 6 SDIO | `MISO` | SDIO |
| 7 MOTION | `DR` | NCS |

Because the adapter swaps the signals, the firmware pin assignment is not the
same as a direct PAW3222 connection.

## Firmware pin assignment

`boards/shields/mona2/mona2_r.overlay` is configured as follows:

| PAW3222 function | MCU pin used by firmware | Reason |
| --- | --- | --- |
| SCLK | `P0.09` | moNa2 CS pin reaches PAW3222 SCLK through the adapter |
| SDIO | `P0.04` | moNa2 SDIO pin reaches PAW3222 SDIO through the adapter |
| NCS | `P0.02` | moNa2 MOTION pin reaches PAW3222 NCS through the adapter |
| MOTION | `P0.05` | moNa2 SCLK pin reaches PAW3222 MOTION through the adapter |

Do not change these back to the direct wiring values while using the
AroundForty adapter.

## Direct PAW3222 wiring warning

If the PAW3222 board is connected directly without the AroundForty adapter, the
firmware pin assignment must be changed to:

| PAW3222 function | Direct MCU pin |
| --- | --- |
| SCLK | `P0.05` |
| SDIO | `P0.04` |
| NCS | `P0.09` |
| MOTION | `P0.02` |

The direct wiring values are not the active configuration on this branch.
