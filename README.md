# pranay_Week2
SoC workshop week 2 deliverables.
<details>
<summary>Part 1- Theory</summary>

## What is a System-on-Chip (SoC)?

A System-on-Chip (SoC) is an entire electronic system on a single piece of silicon. Rather than having numerous standalone ICs (CPU, memory, I/O, peripherals) scattered around, an SoC crowns those building blocks together in close quarters to conserve area, power and cost while enhancing performance and latency. SoCs drive everything from small sensors and wearables to smartphones and embedded appliances.


## Core components of a typical SoC

A bare minimum SoC typically has the following logical blocks:

* **CPU / processor core**
  The programmable brain. Runs software, manages peripherals, and executes OS or bare-metal tasks. Examples are small microcontrollers up to large multi-core processors.

* **Memory**

  * *On-chip RAM* for high-speed temporary storage (data/stack).
  * *ROM/Flash* for boot code and firmware.
    Memory hierarchy influences performance, boot behavior and power.

* **Peripherals & I/O**
Allows the SoC to communicate with the outside world. This helps interfacing of the SoC, with the outside world, it includes the DAC and ADC, to get the input and converting the output into analog signal which can be used by devices like speaker and monitor.

* **Interconnect / Bus / MMU**
The wiring system that lets the CPU and other controllers share data with memory and peripherals.

* **Clocking & Power**
This part manages how the chip runs and uses energy. PLLs or clock generators create the timing signals that keep all parts of the SoC in sync. Power domains divide the chip into sections that can be turned on or off independently, helping save energy. Voltage regulators ensure each block receives a stable power supply, maintaining reliable performance while reducing overall power consumption.

* **Analog & Mixed-Signal blocks (optional)**
  ADCs, DACs, PLLs — necessary when SoC needs to interface with analog world (audio, video, sensors).

* **Security & Management (optional)**
  Watchdogs, secure boot, hardware crypto, debug and test blocks. All of these are required because multiple tasks go on simultaneously, for example if a payment is going on along with a communication through the network, both these need to be protected and isolated to protect them.

---

## Why BabySoC is a good simplified learning model

BabySoC (RVMYTH + 8× PLL + 10-bit DAC) purposefully an SoC down to a manageable, hands-on scale without sacrificing vital concepts:

**Simple structure** — BabySoC uses a single RISC-V core (RVMYTH), making it easy to understand how the processor works and communicates with other parts.

**Clock and timing clarity** — The built-in PLL generates stable timing signals, helping students see how synchronized clocks keep all components working together.

**Digital–analog integration** — The 10-bit DAC converts processed digital data into analog signals, showing how digital logic can drive real-world outputs like sound or video.

**Quick experimentation** — With fewer modules, it’s easier to test, simulate, and refine the design quickly on Sky130.

**Clear learning focus** — BabySoC highlights the connection between timing, processor control, and mixed-signal operation without unnecessary complexity.


## The role of functional modelling prior to RTL & physical design

Functional modelling is in the initial stages of designing, simple representation of the system. 

Purpose and advantages:

**Design exploration**
Functional modeling allows designers to quickly try out different architectures, clock frequencies, or DAC configurations without the time and complexity of full RTL or layout design. This stage helps identify the most efficient structure for the SoC while keeping experimentation low-cost and flexible.

**Early verification**
By defining memory registers, interrupts, and communication interfaces early, both hardware and software teams can start working simultaneously. Test vectors and preliminary firmware can be developed alongside the model, ensuring smooth integration and fewer surprises later in the RTL stage.

**System behavior & tradeoffs**
Functional models make it easier to observe and measure system-level performance factors like data throughput, latency, and clock synchronization. For example, designers can test whether the CPU should wait for a DAC acknowledgment or simply poll a ready bit — helping make better architectural decisions early on.

**Co-simulation planning**
This stage identifies which components need detailed analog simulation (like PLLs, VCOs, or DAC ladders) and which can remain high-level behavioral models. It ensures a balance between simulation speed and accuracy, especially important in mixed-signal SoCs like BabySoC.

**Shorter iterations later**
Catching logic or interface issues early at the functional model level prevents rework in RTL or physical design stages. This early debugging drastically shortens the overall design cycle and improves final reliability, making the transition from model to silicon smoother and more predictable.

</details>
<details>
<summary>Part 2- Lab</summary>

## Cloning, compiling, and analysis of the output.

**Setting up the BabySoC directory**:
```bash
git clone https://github.com/manili/VSDBabySoC.git
```
cloning the VSDBabySoC directory from github

```bash
cd VSDBabySoC
make pre_synth_sim
```
This command compiles the verilog files and simulates it and further forms the .vcd files.

<img width="1107" height="351" alt="image" src="https://github.com/user-attachments/assets/9d32fae2-0ac8-4d94-a762-12df3f1feadc" />


Open the VCD file in GTKWave:
```bash
gtkwave output/pre_synth_sim/pre_synth_sim.vcd
```

## Waveform Analysis
<img width="1917" height="1072" alt="image" src="https://github.com/user-attachments/assets/88b17755-c883-4234-b0b3-cd340ffd979f" />
We get 3 outputs in our .vcd file.

1. Digital output from the core  
   - A 10-bit bus representing the processed data from the RVMYTH core.  
   - This binary data is sent to the DAC for analog conversion.  

2. Analog output (normalized)  
   - The binary output normalized to 1.  
   - Scaled according to the source voltage (`VREFH` and `VREFL`).  

3. Simulation interpretation  
   - The waveform viewer treats the analog signal as a binary wire.  
   - Values below 0.5 appear as logic 0; above 0.5 appear as logic 1.

# pll
```
always @(posedge REF) begin
   if (lastedge > 0.0) begin
      refpd = $realtime - lastedge;
      period = (refpd / 8.0);
   end
   lastedge = $realtime;
end
```
This is the snippet from the behavioural code of the pll. From this it can be observed that the clk signal has a frequency 8 times faster than the reference signal that goes into the input of the pll. We can change the frequency of the clk by making the reference signal faster or slower.
At startup: output clock toggles at 40 MHz (25 ns period).
As REF clock runs, the module measures its period dynamically.

<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/95e36c8b-5d86-4acf-99fb-2e14a5f674e9" />

# DAC

<img width="1619" height="334" alt="image" src="https://github.com/user-attachments/assets/4277ebd1-9e3d-48a3-8609-524d4cbd2f6c" />

* D[9:0] — a 10-bit digital input code from the processor (binary value 0–1023).
* VREFH — high reference voltage (analog high level).
* VREFL — low reference voltage (analog low level).
* OUT — resulting analog voltage output.


```
OUT = VREFL + (D / 1023) × (VREFH – VREFL)
```
The output of the processor which is 10 bit, is converted into decimal and then normalized between the analog high and low, to output it to the suitable device.

Below is the behavioural code of the DAC.
```
always @(D or EN or VREFH or VREFL) begin
   if (EN == 1'b0) begin
      OUT <= 0.0;
   end
   else if (VREFH == NaN) begin
      OUT <= NaN;
   end
   else if (VREFL == NaN) begin
      OUT <= NaN;
   end
   else if (EN == 1'b1) begin
      OUT <= VREFL + ($itor(Dext) / 1023.0) * (VREFH - VREFL);
   end
   else begin
      OUT <= NaN;
   end
end
```
NaN is used to show invalid state. NaN is assigned the value 0.0/0.0 in this code.

The behavioural code shows that it updates the output voltage dynamically, whenever the output or the analog high or low changes. Similar to how the DAC would work.

</details>
