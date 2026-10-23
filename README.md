# CMOS Master–Slave D Flip-Flop Design in GPDK 90 nm

A transistor-level design and physical implementation study of a negative-edge-triggered D flip-flop using a static CMOS master–slave architecture in a 90 nm technology.

This personal mini project explores the custom IC design flow for a sequential cell, from schematic design and functional verification to custom layout, physical verification, parasitic extraction, and post-layout timing characterization.

## Project Highlights

- **Circuit Design:** 18-transistor static CMOS master–slave D flip-flop using transmission-gate latches.
- **Functional Verification:** Confirmed negative-edge-triggered behavior through transient simulation.
- **Custom Layout:** Manually designed transistor-level layout with dedicated power rails and well/substrate connections.
- **Physical Verification:** DRC-clean layout and LVS-clean schematic-to-layout correspondence.
- **Parasitic Extraction:** Extracted layout-dependent parasitic resistance and capacitance for post-layout simulation.
- **Timing Characterization:** Evaluated clock-to-Q propagation delay before and after parasitic extraction, observing approximately 80% higher average delay in the extracted implementation.

## 1. Design Overview

### Architecture

The circuit uses two cascaded transmission-gate latches to implement edge-triggered data storage.

| Component | Function |
|---|---|
| Master latch | Transparent when CLK is HIGH; stores data when CLK is LOW |
| Slave latch | Transparent when CLK is LOW; stores data when CLK is HIGH |
| Clock inverter | Generates the complementary clock signal, `CLK_Bar` |

When CLK transitions from HIGH to LOW, the master latch closes while the slave latch becomes transparent. The stored master-stage value propagates to the output, making the implemented circuit **negative-edge triggered**.

Each latch consists of an input transmission gate, a forward inverter, and a feedback path controlled by another transmission gate. This structure enables the circuit to retain its state while the input path is disabled.

### Design Parameters

| Parameter | Value |
|---|---|
| Technology | GPDK090, 90 nm CMOS |
| Supply voltage | 1.2 V |
| PMOS dimensions (W/L) | 240 nm / 100 nm |
| NMOS dimensions (W/L) | 120 nm / 100 nm |
| Input rise/fall time | 10 ps |
| Transient simulation duration | 20 ns |
| Simulation engine | Spectre |

The PMOS-to-NMOS width ratio of 2:1 was selected to help balance pull-up and pull-down performance. The 10 fF output load was used as a simulation assumption.

## 2. Custom Layout

The transistor-level layout was manually constructed with an emphasis on circuit connectivity, device placement, and power distribution.

Key layout features:

- PMOS devices placed in the upper n-well region and NMOS devices in the lower substrate region.
- Shared VDD and GND rails along the top and bottom of the layout.
- Well and substrate contacts for appropriate body connections.
- Clock and data inputs placed on the left, with `Q` and `Q_Bar` outputs on the right.
- Hierarchical circuit structure based on transmission-gate and inverter subcircuits.

The final layout contains **18 transistors: 9 PMOS and 9 NMOS**.

The implementation prioritizes functional correctness and physical verification rather than minimum area or optimized routing.

## 3. Verification Results

### Functional Verification

Transient simulation confirmed the expected negative-edge-triggered behavior:

- The input data can change while the master latch is transparent.
- The output updates following the falling edge of the clock.
- The output remains stable between active clock edges, provided the circuit operates within valid conditions.
- `Q_Bar` tracks the complement of `Q`, subject to propagation delay.

### Physical Verification

| Verification | Result |
|---|---|
| Design Rule Check (DRC) | Clean — no reported DRC errors |
| Layout Versus Schematic (LVS) | Clean — zero net, device, pin, or parameter mismatches |

These results confirm that the layout satisfies the checked design rules and matches the reference schematic under the verification setup used.

## 4. Pre-Layout vs. Post-Layout Timing

Clock-to-Q propagation delay was measured from the 50% VDD crossing of the falling clock edge to the 50% VDD crossing of the output transition.

| Metric | Pre-layout | Post-layout | Increase |
|---|---:|---:|---:|
| `tpHL` — Q falling | 30.416 ps | 53.685 ps | 76.50% |
| `tpLH` — Q rising | 41.430 ps | 75.560 ps | 82.38% |
| Average clock-to-Q delay | 35.9 ps | 64.6 ps | ~80% |

### Observations

Post-layout simulation shows a substantial increase in clock-to-Q delay compared with the schematic-level results.

A likely contributor is the additional parasitic capacitance introduced by physical interconnects and device connections, particularly around the clock distribution, complementary clock, and internal latch nodes. These capacitances increase the load driven by the circuit's transistors and can slow internal switching.

Although the inspected extracted-netlist resistances were relatively small, this alone does not establish the dominant source of delay degradation. Further investigation would require isolating individual parasitic contributions and comparing node capacitances and transition waveforms.

## 5. Limitations and Future Improvements

The current implementation establishes a verified functional and physical baseline. Further work could extend the characterization in several directions:

- **Setup and hold characterization:** Sweep the input-data transition relative to the active clock edge and define explicit timing pass/fail criteria.
- **Transistor sizing:** Evaluate alternative PMOS/NMOS dimensions and compare delay, power, and area.
- **Power analysis:** Measure dynamic and static power under defined input activity and simulation conditions.
- **Layout optimization:** Explore shorter clock routes, improved placement, and shared diffusion where electrically and physically appropriate.
- **Timing analysis:** Investigate the contribution of extracted capacitance and resistance to individual internal-node delays.

All reported results are simulation-based and depend on the selected technology models, device dimensions, loading conditions, and verification setup.


## Notes

- The technology files and models are not included in this repository.
- Simulation results, layout data, and verification reports should be included only where available and permitted for redistribution.
- The project is intended for educational and engineering practice; results do not guarantee fabricated-silicon performance.

## Author

**Nguyen Duc Minh**  
Integrated Circuit Design  
FPT University

[LinkedIn](https://www.linkedin.com/in/minhnd71205)
