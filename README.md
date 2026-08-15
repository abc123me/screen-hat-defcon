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

### Potential improvements

The version 1 on the 595 expansion PCB worked almost perfectly however I suspect it could be improved.

 - Add a GPIO line to pin 6 of the AP632XX buck converter, so FPGA can contreol power for all screens, this would improve the initialization by making sure screens the screens are all turned on after the FPGA is fully loaded.
 - I2C ADC so voltages can be monitored
 - Global output enable controls for both shift register banks

# Building

Use jlbsp, Good luck!
