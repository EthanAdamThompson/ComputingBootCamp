---
layout: page
toc: true
title: "Booting an MPSoC"
slug: mpsoc_boot
type: fpga_commercial
order: 6
---
# Booting an MPSoC

This guide will go into depth about how to create a boot on an Ultra96v2 MPSoC. It will be using JTAG and Xilinx version 2024.2

## Setup
1. Open a terminal and Source Vivado

         source /tools/Xilinx/Vivado/2024.2/settings64.sh
         vivado

    Note: sudo access is required.
    
3. Click Create Project
4. Name the project (e.g. ultra96v2_hello_world) and choose a location
Select RTL Project, check "Do not specify sources at this time"
On the Default Part screen, switch to the Boards tab and search for Ultra96-V2

>If missing, run:
>
>      git clone https://github.com/Avnet/bdf.git
>      sudo cp -r bdf/ultra96v2 /tools/Xilinx/Vivado/2024.2/data/boards/board_files/
>
>Restart Vivado, then the board should appear


4. Click Finish

## Block Design

5. In the Flow Navigator, under IP Integrator, click Create Block Design (name it e.g. design_1)
6. Add the Zynq UltraScale+ MPSoC IP (press Ctrl+I, search for it)
7. Click Run Block Automation in the green banner, leave defaults, OK
8. Fix clock errors: connect pl_clk0 to both maxihpm0_fpd_aclk and maxihpm1_fpd_aclk
9. Validate the design (click the checkmark or press F6) — should pass cleanly

## HDL Wrapper & Bitstream

10. In the Sources tab, right-click design_1, Create HDL Wrapper, choose "Copy generated wrapper to allow user edits"
11. In the Flow Navigator, click Run Synthesis, set Number of Jobs to 6, OK
12. After synthesis completes, Run Implementation
13. After implementation completes, Generate Bitstream, when done, hit Cancel
14. Go to File, Export, Export Hardware, check Include Bitstream → OK

## Open in Vitis

15. Open Vitis Classic (or Vitis Unified, depending on your install) separately from the terminal:

        source /tools/Xilinx/Vitis/2024.2/settings64.sh
        vitis &

16. In Vitis, go to File, New, Platform Project, point it to the exported .xsa file 
17. Set CPU to psu_cortexa53_0, OS to standalone, click Finish
18. In platform settings, navigate to standalone, BSP Settings and change both stdin and stdout from psu_uart_0 to psu_uart_1 (UART0 is tied to the WiFi/BT chip)
19. Go to File, New, Application Project, select your platform, name it Hello_World, language C
20. Click Next and select the Hello World template, Finish
21. Make sure both switches on the board are in the ON position
22. Build and run/debug the application — output should appear on your UART1 debug console
