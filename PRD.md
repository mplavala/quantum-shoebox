# Product Requirements Document (PRD)

## Vision & Success Criteria
- Build a controllable NV ensemble platform.
- **Minimal viable success:** Demonstrate of Zeeman effect with NV ensemble.
- **Full-Fledged success:** Operate NV ensemble as qubit, demonstrate initialization, unitary gates, measurements.
- Gates: smallest implemented gate will be rotaion over π/24 taking 10 ns, so π pulse will take 240 ns. Target coherence sufficient for up to 2 full rotations = 960 ns. Quantitative gate accuracy target is to distinguish π/24 from π/22.
- Final design of the system must be buildable by an enthusiast, usable for basic quantum experiments, and hackable for extensions (quantum games, quantum machine learning).
- Explicit limitations: no entanglement; ensemble measurements yield only probabilities, not single clicks.

## Target User & Use Cases
- Expected prior knowledge of user assembling the final product: basic QM of single qubit, basic soldering and electronics assembly. Should be learnable by an enthusiast from freely accessible sources.
- System assembly must not require RF/optics design skills.
- Use cases: Zeeman effect demonstration, simple contextuality tests, quantum neuron demonstrations, quantum games.
- Not aimed at quantum advantage claims.

## Qubit Definition & Physical Scope
- Logical qubit: NV ensemble, mean-field approximation.
- Optional demonstration: ODMR to verify NV level splittings.
- Diamond: commercial 1 ppm NV, 700–850 μm, T₁ ~ 4.394 ms.
- Orientation: any; single-axis selection not required.
- Coherence sufficient for two rotations (~500 ns–1 μs).
- Room temperature operation; no thermal isolation.
- Coherence measurement: Rabi oscillations, possibly spin echo.

## Control Requirements (Quantum Gates)
- Minimum: rotations around one axis; multi-axis as stretch goal.
- Gates implemented physically; no virtual Z rotations.
- FPGA and DAC are controlled via USB from a computer, initially directly via GUI software, later via Python interface.
- FPGA variable-duration pulses by controlling RF switch; Python interface to define sequences.
- Phase coherence is essential for multiple pulses.
- Pulse imperfections are tolerated if systematic and calibratable.
- Arbitrary user-defined sequences (within maximal length and duration); parameters changeable shot-to-shot.
- Shortest reliable pulse ~10–20 ns.
- No calibration procedure for MW chain nonlinearity; iterative tuning is acceptable.

## Timing & Synchronization
- Master clock: BASYS3 FPGA, 100 MHz (10 ns units).
- Coarse deterministic timing acceptable; ensemble averaging tolerates jitter.
- FPGA synchronizes optical pumping, MW, readout.
- Shot duration: sub-second; repetition rate: 10³ Hz ideal, 10² Hz acceptable.
- Python triggers via USB; single-call execution per sequence.
- Sub-ns synchronization not required.

## Initialization & Readout
- Initialization: pulsed 520 nm high-power LED, target >90% in |0⟩.
- Pulse duration: ~20 ms; exact duration determined experimentally. Suggested test: vary LED pulse duration while measuring red fluorescence via photodetector; find shortest pulse giving maximum signal.
- Readout: photodetector + boxcar averager; integration window ~100-200 ns. ADC packaged with BASYS3 FPGA. For initial tests photodetector connected directly to oscilloscope.
- Contrast: ideally several percent; SNR target to be tested experimentally.
- Averaging over integration window in hardware; Python receives averaged data. Averaging over multiple shots manually in Python (if necessary).
- Measurement destroys coherence; continuous-wave ODMR optional.

## Hardware Architecture
- Off-the-shelf: BASYS3 FPGA, ADF4351 DAC, Thorlabs DET36A2, common electronics and optics.
- Custom PCBs:
  - laser driver (MOSFET + resistor),
  - RF switch + amplifier + attenuator (50Ω),
  - boxcar averager.

- Modular setup; diamond and optics mounted in 3D-printed self-aligning cubes [OpenUC2](https://openuc2.com/).
- Diamond mounted on 3D-printed XY-adjustable mount, held with glue.
- Optical configuration based on [open-source ODMR design](https://o3q.de/odmr/) including
  - LED mount with focusable collimating lens,
  - objective to focues the green light,
  - objective to gather red emitted light,
  - long pass filter to block the green light from the photodiode.

- MW: square antenna with side 1mm in the plane of the diamond and enclosing the diamond; wire or PCB; terminated in a capacitor; iterative tuning acceptable.
- Power specifications:
  - FPGA, DAC powered via USB,
  - Amplifier requires 3 - 5.5 V,
  - RF switch requires ground, +5V, -5V, 4-9 mA,
  - LED requires 5.8V, 110 mA,
  - Photodetector is powered by internal batteries.

- Breadboard prototyping acceptable; minimal post-assembly tuning.
- Enclosure: cardboard box to reduce light leak and EMI; thermal buildup controlled on LED by heatsink if necessary.

## Software & Python Interface
- Python API: sequence-of-gates abstraction, minimal, hackable.
- Control: gate angles; single-call execution; returns metadata + data.
- Health/recalibration triggers.
- Logging handled by Python.

## Calibration & Automation
- Calibrate MW pulse amplitude for π/2; interpolate to π/24 by testing multiples (π/8, etc.).
- Pulse duration and LED intensity determined experimentally.
- Integration window: ~100-200 ns for boxcar averager.
- No reference signal for automatic drift detection.
- Python triggers calibration; slow but reliable acceptable.

## Safety & Practical Constraints
- Optical: avoid direct eye exposure; optical setup enclosed in a cardboard box.
- RF: low power; surface fields acceptable with expert consultation.
- Thermal: fans/heatsinks; monitor LED for overheating.
- Fire risk mitigated via fuses or current-limiting resistors.
- Unattended operation should be eventually possible; with occasional manual supervision.

## Validation & Acceptance Tests
- Validate initialization, sequence execution; ensemble-averaged data.
- Gate fidelity: quantitative estimates via Rabi oscillations.
- Contextuality inequality: violation where confidence interval fully above classical threshold.
- Automated scripts controlled via Python, able to run pre-scheduled experiments, regular health checks and calibration if necessary, with logging and safe shutdown + notification to admin on potential malfunctions or unexpected results.

## Future Extensions
- Out of scope: multiple qubits/entanglement, cryogenics, single-photon counting.
- Optional: multi-axis control.
