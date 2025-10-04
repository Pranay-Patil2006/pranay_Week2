# pranay_Week2
SoC workshop week 2 deliverables.
<details>
<summary>Part 1</summary>

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
<summary>Part 2</summary>
Hi
</details>
