# Arduino config (PSA Diag cable)

Quick reference for building the standard **Arduino Uno / Nano + MCP2515** cable
used with the PSA Diag tool. Applies to `arduino-psa-diag/arduino-psa-diag.ino`.

## Firmware settings (top of the .ino)
```cpp
#define CS_PIN_CAN0   10          // MCP2515 CS on D10
#define SERIAL_SPEED  115200      // fixed - do not change
#define CAN_SPEED     CAN_500KBPS // diagnostic CAN = 500 kbps (HS-CAN)
#define CAN_FREQ      MCP_16MHZ   // 16 MHz crystal on the module
                                  //  -> use MCP_8MHZ if your module is 8 MHz
```
Leave the rest at default (`CAN_RCV_BUFFER 40`, `CAN_DEFAULT_DELAY 5`,
`MAX_DATA_LENGTH 512`). The CAN IDs (e.g. `764:664`) are set by the **app**, not
the firmware.

## Wiring - SPI (MCP2515 -> Arduino)
| MCP2515 | Arduino |
|---|---|
| CS  | **D10** |
| SI (MOSI) | D11 |
| SO (MISO) | D12 |
| SCK | D13 |
| INT | D2 |
| VCC / GND | 5V / GND |

## Wiring - CAN side (MCP2515 -> OBD2)
| MCP2515 | OBD2 pin |
|---|---|
| CANH | 6 |
| CANL | 14 |
| GND  | 4 and/or 5 |

Power over USB from the laptop. To power from the car use OBD2 pin 16 (+12V) via a
proper 12V->5V regulator, never straight onto 5V.

## Libraries
- [autowp/arduino-mcp2515](https://github.com/autowp/arduino-mcp2515) (MIT)
- [ivanseidel/ArduinoThread](https://github.com/ivanseidel/ArduinoThread)

## App side (must match)
- 115200 baud, 8N1, **LF only** line ending
- Pick the port: `npm run diag -- ports`, then `--port /dev/tty.usbserial-XXXX`
  (or `export PSA_PORT=/dev/tty.usbserial-XXXX`)

## The only two things that go wrong
1. Wrong `CAN_FREQ` (8 vs 16 MHz) - check the crystal on your MCP2515 module.
2. `CS_PIN_CAN0` not matching your wiring (must be 10 on Arduino).

Either one -> `7F3E03 / connection lost`.

## Full build manual
Wiring, bill of materials, ESP32 variant and troubleshooting:
[docs/PSA-Diag-Cable-Manual.pdf](docs/PSA-Diag-Cable-Manual.pdf)
