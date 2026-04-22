# Design and Implementation of Kalman Filter on FPGA

## Objective

The primary objective of this project is to design a Kalman filter in Verilog HDL. This filter will be used to estimate the state of a system (specifically, a vehicle's position and velocity) using fixed-point arithmetic. You will then implement this design on an FPGA. After implementation, you must analyze the design's performance by reporting its hardware utilization, power consumption, and timing , and by calculating its maximum clock frequency and throughput. 

## Design

* Logic: Implement the Kalman filter "Prediction" and "Update" equations provided in the manual.
* Numeric Format: All calculations must use 16-bit signed fixed-point numbers with 10 fractional bits.
* Timing (Input): A new set of inputs (n, u, z) arrives every clock cycle.
* Timing (Output): Your outen signal must be high for only one clock cycle to signal a valid output. The output no must match the index   n of the input it was calculated from.
* Performance: You must produce at least 10 outputs within the 1000 cycle input window. This means your latency for a single              calculation cannot be too long.
* Reset: When rst is 0, all data elements and outen must be reset to 0.
* FPGA IP: You must generate three Block Memory IPs (blk_mem_gen_0, blk_mem_gen_1, blk_mem_gen_2) with the exact port, width, and depth   settings specified in the lab manual.

## Implementation

An Finite State Machine (FSM) was designed to manage the multi-cycle "Prediction" and "Update" calculations. All arithmetic logic was implemented using 16-bit signed fixed-point numbers with 10 fractional bits. Asynchronous reset logic was included to clear all registers and outputs to 0 when rst was low. Finally, output control logic was added to assert the outen signal for only one clock cycle when a valid output was ready.
The design was built by clicking "Generate Bitstream," which ran synthesis and implementation. After the build, the implemented design was opened to gather the Hardware Utilization, Timing Summary, and Power reports. From the Timing Summary, the Maximum Clock Frequency (Fmax) was calculated based on the Worst Negative Slack (WNS) and the target clock period. The Maximum Throughput was then calculated by dividing Fmax by the FSM's total clock cycles per sample. Finally, the hardware (including the bitstream) was exported, a boot image was created in Vitis, and the design was successfully tested on the Zybo board.

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
