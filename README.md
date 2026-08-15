# screen-hat-defcon

This is a collection of all the rpositories for the DEFCON screen hat

Name                                                                      | Description
--------------------------------------------------------------------------|------------------------------------------------
[screen-hat-rs](https://github.com/abc123me/screen-hat-rs)                | Userspace rust animation app
[fbgl-rs](https://github.com/abc123me/fbgl-rs)                            | Userspace rust graphics library
[ddmafb](https://github.com/abc123me/ddmafb)                              | Linux kernel direct DMA framebuffer driver
[Vivado project](https://github.com/abc123me/MicroPhaseZ7_Lite_ScreenHat) | Vivado project for the screen hat
[Vivado IP](https://github.com/abc123me/VivadoIP)                         | Vivado IP for the screen hat
[jlbsp](https://github.com/abc123me/jlbsp)                                | BSP for the screen-hat

## Hardware information

Wiring and other information is contained in the `ScreenHat.ods` spreadsheet. See the V2 page or picture below for complete information.

![WiringInfo.png](https://raw.githubusercontent.com/abc123me/screen-hat-defcon/refs/heads/master/WiringInfo.png)

## GPIO expandsion PCB information

The 595pcb KiCAD project is the core GPIO expansion PCB used for backlight and reset 
control, gerber files for version 1 of the PCB are inside of `595pcb/grb_v1.zip` in this
repository, the BOM for the PCB is below:

Digikey part no                                                                                                 | Quantity | Description
----------------------------------------------------------------------------------------------------------------|----------|----------------------------------------------
[AP63203WU-7](https://www.digikey.com/en/products/detail/diodes-incorporated/AP63203WU-7/9858426)               | 1        | 3.3V 2A Buck converter
[74HC595DCT-ND](https://www.digikey.com/en/products/detail/toshiba-semiconductor-and-storage/74HC595D/5879984)  | 6        | 8-Bit Serial-In, Parallel-Out shift register
[T491S106K010AT](https://www.digikey.com/en/products/detail/toshiba-semiconductor-and-storage/74HC595D/5879984) | 4        | 10uF, 10V Tantalum capacitor
[478-KGM21NR71H104KTCT-ND](https://www.digikey.com/en/products/detail/kyocera-avx/KGM21NR71H104KT/563505)       | 10       | 100nF, 50V MLCC capacitor

## Displays used

 - The displays are 14x ILI9341 based 2.4" TFT LCDs, they're 240x320 and use a three-wire SPI interface. The SPI interface is unique as a word is 9-bits wide as opposed to the typical 8-bits.
 - The linux kernel driver always sends multiples of 9 bytes in order to meet this strange requirement of the SPI interface while using a typical 8-bit SPI driver.
 - The FPGA IP just does 9-bit SPI natively since it's an FPGA and I can make the hardware do whatever I want.

## Development board used

 - The MicroPhase Z7 lite is a cheap $100 chinese Zynq 7000 series development board, sometimes can be found for as cheap as $70
 - I am using the Zynq 7010 variant, the Zynq 7000 series has two 32-bit ARM V7 cores, this particular development board has 2Gib of RAM which.
 - This equates to 512MB of RAM in Linux (since 1B = 8b)
 - They don't provide a Vivado example for this board, I had to figure it all out myself, so videos on that below:
 - [Microphase Z7 lite DDR3 buildroot configuration](https://www.youtube.com/watch?v=MMN_5UOYHE4)
 - [Ethernet on MicroPhase Z7 lite](https://www.youtube.com/watch?v=It98w_9iQJE)
 - The DDR3 RAM on these chips is LPDDR3, I tried to get it to go as fast as I could but it is still pretty slow.
 - Never got USB to work, tried once, and gave up on it as I didn't have much interest in it since I never use USB anyways

### Potential improvements

The version 1 on the 595 expansion PCB worked almost perfectly however I suspect it could be improved.

 - Add a GPIO line to pin 6 of the AP632XX buck converter, so FPGA can contreol power for all screens, this would improve the initialization by making sure screens the screens are all turned on after the FPGA is fully loaded.
 - I2C ADC so voltages can be monitored
 - Global output enable controls for both shift register banks

# Building

## Dependencies

This is a buildroot project, meaning the entire linux kernel and all apps are built from source.

I use Arch btw, but last I checked Fedora is recommended for Linux kernel / Buildroot builds. Arch Linux typically has a GCC version that is too new, meaning you will be fighting GCC - so I wouldn't recommend it.

[Buildroot required dependencies](https://buildroot.org/downloads/manual/manual.html#requirement-mandatory)

My custom packages all use `git` so it is also a required package.

`fd-find` is required for my custom BSP repository and `bsp` script, if you have not used this unix `find` replacement, try it!

For any questions on how to modify packages, see [the buildroot manual](https://buildroot.org/downloads/manual/manual.html)

## Instructions

 1. Clone [jlbsp](https://github.com/abc123me/jlbsp)
 2. Enter the repository (`cd jlbsp`)
 3. Checkout the develop branch for latest (`git checkout develop`)
 4. Setup the screen hat BSP (`./bsp setup screen-hat`)
 5. Enter the screen hat BSP (`cd ../builds/screen-hat`)
 6. Turn off the `JL_SECRET_KEYS` repository via `make menuconfig`
 7. Build the BSP using `make all`
 8. Flash the SD card using `dd if=images/sdcard.img of=<my-sd-card> bs=4M oflag=sync status=progress`

## Troubleshooting

The `jlbsp` contains an upgrade script you can use to remotely upgrade the screen hat via `make deploy`.
The `bsps/screen-hat.bsp` file contains the IP address that would be used here. You'll also have to
setup SSH keys yourself by adding your SSH keys to the rootfs or enabling root login via SSH.

Serial via the USB UART may also be used, 115200 baud, the username is root, no password.
