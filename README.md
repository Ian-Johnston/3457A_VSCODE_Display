**HP 3457A TFT Display Upgrade (VS Code)**

This repository contains the **Visual Studio Code** version of my **HP 3457A TFT Display Upgrade** firmware.

The project replaces the original LCD display with a modern high-resolution TFT display driven by an **STM32F103C8T6 (Blue Pill)** and an **LT7680 TFT controller**.

It is built with a standard **Visual Studio Code + CMake + GNU Arm Embedded Toolchain** development environment.

**Features**

- Full HP 3457A display decoding
- High-resolution TFT graphics
- Original display faithfully reproduced
- Fast screen updates
- Modular embedded C source
- STM32 HAL-based firmware

**Development Environment**

- Visual Studio Code
- CMake
- Ninja
- GNU Arm Embedded Toolchain
- Cortex-Debug extension
- STM32CubeCLT
- ST-LINK GDB Server
- STM32Cube HAL

**Hardware**

- STM32F103C8T6 Blue Pill
- Levetop LT7680A-R TFT controller (128Mb built-in SDRAM), driven by STM32 SPI1
- BuyDisplay/EastRising ER-TFT3.71-1 TFT LCD, 240x960, ST7701S driver
- Custom interface PCB

**Project Structure**

The project is split into a number of modules, including:

- Main application
- LT7680 TFT controller driver
- TFT graphics library
- GPIO
- SPI
- DMA
- Timer / Display decoder
- STM32 HAL support

**Fixes - November 2026 (random pixels and display area alignment)**

1. SDRAM auto-refresh was never switched on (main cause of the random pixels). `SDRAM_Init_LT()` no longer sets REG[E4h] bit 2 first, so E0h-E3h now get the attribute, CAS latency and refresh interval. Matches Levetop AP-Note V1.2 page 27.
2. The Active Window was one pixel short in each direction. REG[5Ah-5Dh] are width and height, not end coordinates, so `ConfigureActiveDisplayArea_LT()` now writes 240x960 instead of size-1. The boot edge wipe in main.c is no longer needed and is commented out (re-enable it if edge pixels return).
3. LT7680 busy and write-FIFO waits added: `WaitForLT7680Ready()`, `WaitForLT7680WriteFifoNotFull()` and `WaitForLT7680WriteComplete()`. Used in `DrawText()`, `SetTextColors()`, `ConfigureFontAndPosition()` and `DrawLine()`. `DrawLine()` now waits for each line to finish. All waits have timeouts so a fault cannot hang the meter.
4. A commented-out 1-pixel test box around the full 240x960 frame (TEMP TEST block) is in main.c for checking alignment.

---

Ian Johnston