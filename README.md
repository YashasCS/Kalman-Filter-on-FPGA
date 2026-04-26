# Design and Implementation of Kalman Filter on FPGA

## Objective

The primary objective of this project is to design a Kalman filter in Verilog HDL. This filter will be used to estimate the state of a system (specifically, a vehicle's position and velocity) using fixed-point arithmetic. We shall then implement this design on an FPGA. After implementation, we will analyze the design's performance by reporting its hardware utilization, power consumption, and timing , and by calculating its maximum clock frequency and throughput. 

## Design

* Logic: Implement the Kalman filter "Prediction" and "Update" equations provided in the manual.
* Numeric Format: All calculations must use 16-bit signed fixed-point numbers with 10 fractional bits.
* Timing (Input): A new set of inputs (n, u, z) arrives every clock cycle.
* Timing (Output): Your outen signal must be high for only one clock cycle to signal a valid output. The output no must match the index   n of the input it was calculated from.
* Performance: You must produce at least 10 outputs within the 1000 cycle input window. This means your latency for a single              calculation cannot be too long.
* Reset: When rst is 0, all data elements and outen must be reset to 0.
* FPGA IP: You must generate three Block Memory IPs (blk_mem_gen_0, blk_mem_gen_1, blk_mem_gen_2) with the exact port, width, and depth   settings specified in the reference manual.

## Implementation

An Finite State Machine (FSM) was designed to manage the multi-cycle "Prediction" and "Update" calculations. All arithmetic logic was implemented using 16-bit signed fixed-point numbers with 10 fractional bits. Asynchronous reset logic was included to clear all registers and outputs to 0 when rst was low. Finally, output control logic was added to assert the outen signal for only one clock cycle when a valid output was ready.
The design was built by clicking "Generate Bitstream," which ran synthesis and implementation. After the build, the implemented design was opened to gather the Hardware Utilization, Timing Summary, and Power reports. From the Timing Summary, the Maximum Clock Frequency (Fmax) was calculated based on the Worst Negative Slack (WNS) and the target clock period. The Maximum Throughput was then calculated by dividing Fmax by the FSM's total clock cycles per sample. Finally, the hardware (including the bitstream) was exported, a boot image was created in Vitis, and the design was successfully tested on the Zybo board.

Board Used: ```Xilinx XC7Z010-1CLG400C``` (Required for this project)

## How to implement the design on FPGA?

* Right click `“top_kalman.v”` in the “source” panel and click “set as top” (If this file is already in bold font, it is already the top module).

* Now, ```“top_kalman.v”``` needs a dual port RAM. We add the RAM for ```“top_kalman.v”```.

* On the left panel, click IP catalog, on the top right corner, search “ram”. Double click “block
memory generator”.

* If there is a window popped up, asking if you want to add IP to block design or customize IP,
choose customize IP.

* We need a two ports ram for “top_kalman.v”. For memory type, select ```“True Dual Port
Ram”```, the component name should be ```blk_mem_gen_0```.

* In both port A and port B options, change write width to 8, and write depth to 20000.

* Operation mode ```“Read First”```, enable port type ```“Always Enabled”```. Click ```“ok”```.

* In the pop-up window, select ```“Global”``` in the synthesis option, and click “generate”.

* We also need a single port ram for ```“top_kalman.v”```. For memory type, select ```“Single Port
Ram”```, the component name should be ```blk_mem_gen_1```.

* In port A option, change write width to 96, and write depth to 1000.

* Operation mode ```“Read First”```, enable port type ```“Always Enabled”```. Click ```“ok”```.

* In the pop-up window, select ```“Global”``` in the synthesis option, and click ```“generate”```.

* We also need another single port ram for ```“top_kalman.v”```. For memory type, select ```“Single
Port Ram”```, the component name should be ```blk_mem_gen_2```.

* In port A option, change write width to 32, and write depth to 1000.

* Operation mode ```“Read First”```, enable port type ```“Always Enabled”```. Click ```“ok”```.

* In the pop-up window, select ```“Global”``` in the synthesis option, and click ```“generate”```.

* Now, click ```generate bitstream```. After the bitstream is generated, click ```file->export->export
hardware```. Check include bitstream, click “ok”.

* Now, use Vitis to create the boot image.

* Copy ```BOOT.bin```, ```uramdisk.image.gz```, ```uImage```, ```devicetree.dtb```, ```lab7_kalman_test```, ```lab7_data``` to
SD card. Insert SD card to Zybo board. For JP5 pins on the Zybo board, connect two SD pins.

* Boot the Linux system on the Zybo board.

* Run the test

                           ./lab7_kalman_test [Clock cycles between each sampling]

* The `[Clock cycles between each sampling]` means how many clocks cycles between each time
your design samples the input.

* The matrices and inputs needed by the Kalman filter are automatically inputted to your design
by lab7_kalman_test program. The outputs from your design are read and compared with the
correct results. If your design is correct, the absolute value of the error should be less than 1.5.
The results of the FPGA clock cycles are shown in the terminal.

## Results

<img width="837" height="502" alt="image" src="https://github.com/user-attachments/assets/87528637-4e06-46da-8a28-e329ff4e7970" />

<br><br>
<img width="837" height="502" alt="image" src="https://github.com/user-attachments/assets/74c8fd31-3b5c-4f59-a160-7bd7d6f4b294" />


### Terminal FPGA Output:

<img width="837" height="297" alt="image" src="https://github.com/user-attachments/assets/707796b5-8ed0-4828-b386-e372428f60bb" />

<br><br>
In the behavioural simulation, we can see that we got multiple outputs within less than 100 cycles, hence we can get 10 outputs within 1000 clock cycles. And from the terminal output we can see that the error of the filtered data compared to the original data is <1.5

### Hardware Utilization

<img width="837" height="502" alt="image" src="https://github.com/user-attachments/assets/57093971-bbe6-4800-b064-71ef887e4183" />

### Timing Report

<img width="837" height="502" alt="image" src="https://github.com/user-attachments/assets/17f9a204-4d6a-4e5e-a327-c2e4256d831c" />

### Power Report

<img width="837" height="502" alt="image" src="https://github.com/user-attachments/assets/95b7df05-4cf9-4b8d-83de-becdc41bbb10" />

<br><br>
* From the timing report the maximum clock frequency:

  Maximum Clock Frequency = 1/(Total clock time - WNS)

  = 1/(33 - 26.575)

  = 155MHz

* Maximum Throughput of the design:

  Max Clock Frequency x (O/P) / Clock

  = 155MHz x 16bits / 6 clock

  = 415 Mbps
