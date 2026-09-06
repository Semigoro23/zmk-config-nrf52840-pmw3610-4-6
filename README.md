# DYA2 LiPo / XIAO nRF52840 Plus ZMK configuration

This repository builds firmware for the modified DYA2 controller documented in
the accompanying hardware project. It uses a 4-row by 6-column matrix on each
half, BLE split transport, and a SideraKB PMW3610 on the right half.

## Artifacts

- `dya2_left.uf2`: flash to the left/peripheral half.
- `dya2_right.uf2`: flash to the right/central half (the half that connects to the host).
- `settings_reset.uf2`: erase ZMK settings when re-pairing is required.

## Matrix wiring (both halves)

| Matrix | XIAO Plus | nRF52840 |
|---|---|---|
| C1 | D0 | P0.02 |
| C2 | D2 | P0.28 |
| C3 | D11 | P0.15 |
| C4 | D12 | P0.19 |
| C5 | D13 | P1.01 |
| C6 | D14 | P0.09 |
| R1 | D15 | P0.10 |
| R2 | D17 | P1.03 |
| R3 | D18 | P1.05 |
| R4 | D19 | P1.07 |
| R5 (thumb row) | D1 | P0.03 |

The eight thumb keys use the new R5 row. Wire the left thumb keys to R5+C1 through R5+C5, and the right thumb keys to R5+C1 through R5+C3. R5 is not present on the original four-row FFC, so it must be wired separately to XIAO D1. All switches still require the same diode direction as the main matrix.

The configuration assumes diodes point from columns to rows (`col2row`). Verify
the physical diode direction before applying power. D16/P0.31 is reserved for
the XIAO battery voltage measurement and is not used by the matrix.

## PMW3610 direct wiring (right half only)

| XIAO | SideraKB J1 | Signal |
|---|---|---|
| 3V3 | 1 | VIN |
| GND | 2 | GND |
| D9 / P1.14 | 3 | SDIO |
| D8 / P1.13 | 4 | SCLK |
| D10 / P1.15 | 5 | nCS |
| D5 / P0.05 | 6 | MOTION |

Do not connect 5V or BAT to the PMW3610 board. nRESET is intentionally unused.
The SPI clock is limited to 2 MHz.

## First flash

Flash `dya2_left.uf2` to the left XIAO and `dya2_right.uf2` to the right XIAO.
Reset both halves at approximately the same time so the split can pair. The
right half, which contains the PMW3610, advertises to the host as `DYA2`.

The first key on the Function layer is `&bootloader`, and the second is
`&sys_reset`. The Function layer is held from the sixth key of the bottom-left
row in the placeholder keymap.

## Upstream references and licenses

- ZMK: https://github.com/zmkfirmware/zmk
- PMW3610 driver: https://github.com/badjeff/zmk-pmw3610-driver
- Original DYA2 ZMK definition: https://github.com/cormoran/zmk-keyboard-dya2
- Charybdis keymap adapted for this 56-key layout: https://github.com/Semigoro23/zmk-config/blob/main/config/charybdis.keymap

This configuration is MIT-licensed. Retain upstream license notices for copied
or redistributed dependencies.
