# ASIC Implementation of a Password-Based Security Controller

**Verilog RTL | SKY130HD | OpenROAD | Physical Design | 16-Corner Static Timing Analysis**

## 1. Project Overview

The **ASIC Password Controller** is a digital security controller designed using Verilog Hardware Description Language (HDL). It verifies a user-entered password and controls access based on whether the password is correct or incorrect.

The project demonstrates the complete digital ASIC implementation workflow, starting from RTL design and functional simulation and continuing through synthesis, floorplanning, placement, routing, and final GDS layout generation.

The design was implemented using the SKY130HD standard-cell technology and the OpenROAD physical design flow.

A programmed password of `1234` is used for verification. The controller accepts the correct password and generates an unlock indication. Incorrect password entries generate an error indication, and the system enters a locked state after three incorrect attempts.

The project also includes static timing analysis (STA) across 16 timing corners to evaluate setup and hold timing under different process, voltage, and temperature conditions.

### Project Highlights

- Verilog RTL implementation of a password-based security controller.
- Four-digit password verification.
- Error detection for incorrect passwords.
- Lockout after three incorrect attempts.
- RTL functional simulation using Icarus Verilog.
- Waveform inspection using GTKWave.
- ASIC synthesis and physical implementation using OpenROAD.
- SKY130HD standard-cell technology.
- Final synthesized netlist and GDS layout generation.
- Static timing analysis across 16 timing corners.
- Area and timing report collection.

## 2. Problem Statement

Digital access-control systems require a reliable mechanism for checking user credentials and preventing repeated incorrect password attempts.

A password controller must distinguish valid and invalid inputs, indicate the result, and restrict further access after repeated failures.

This project implements these functions as a synchronous digital controller using Verilog RTL. The design is then taken through an ASIC implementation flow to understand how a hardware description can be converted into a physical integrated-circuit layout.

## 3. Project Objectives

The main objectives are:

1. Design a password verification controller using Verilog HDL.
2. Implement correct-password and incorrect-password detection.
3. Generate an error indication for invalid password attempts.
4. Lock the system after three incorrect password attempts.
5. Verify the design using a Verilog testbench.
6. Inspect the simulation waveforms.
7. Synthesize and implement the design using the SKY130HD technology.
8. Generate the final standard-cell netlist and GDS layout.
9. Evaluate setup and hold timing across 16 timing corners.
10. Document the area, timing, and physical-design results.

## 4. Hardware Description and Design Technology

| Parameter | Description |
|---|---|
| Project title | ASIC Password Controller |
| Design implementation | RTL-based digital controller |
| Hardware description language | Verilog HDL |
| Standard-cell technology | SKY130HD |
| RTL simulator | Icarus Verilog |
| Waveform viewer | GTKWave |
| Physical design tool | OpenROAD |
| Timing analysis | Static Timing Analysis (STA) |
| Programmed password | `1234` |
| Incorrect-attempt limit | 3 |
| Clock period constraint | 10 ns |
| Final layout format | GDS |

The project is implemented as a standard-cell ASIC design rather than a microcontroller program. The controller's behavior is described using hardware registers and combinational logic, which are mapped to standard cells during synthesis.

## 5. System Architecture

The controller accepts a sequence of password digits and compares them against the programmed password.

The design uses a clock and reset interface to control the internal state and digit-processing sequence.

### Main functional blocks

**1. Input interface**

The input interface accepts the password digit and the enter control signal. The digit input is represented by a 4-bit signal, allowing one hexadecimal digit to be represented at a time.

**2. Password verification logic**

The controller checks the entered digits against the programmed password sequence `1234`. The design keeps track of the digit sequence and whether the preceding digits match the expected password prefix.

**3. Attempt tracking logic**

The controller maintains the state required to track incorrect password attempts. This state determines whether the system should continue accepting attempts or enter the locked condition.

**4. Output control logic**

The controller generates the outputs:

- `unlock`: indicates successful password verification.
- `error`: indicates an incorrect password.
- `locked`: indicates that the controller has entered the lockout state.

**5. Reset control**

The reset input initializes the controller to its defined starting condition.

### High-level block diagram

```text
        Password Digit Input
                 |
                 v
        +-------------------+
        | Input Processing  |
        +-------------------+
                 |
                 v
        +-------------------+
        | Password Matching |
        |      Logic        |
        +-------------------+
                 |
                 v
        +-------------------+
        | Attempt Tracking  |
        +-------------------+
                 |
                 v
        +-------------------+
        | Output Controller |
        +-------------------+
                 |
        +---------+---------+
        |         |         |
        v         v         v
      Unlock     Error    Locked
```

The diagram represents the functional organization of the controller. It is a conceptual diagram, not a gate-level schematic.

## 6. Password Verification and Lockout Operation

The programmed password is `1234`.

The controller processes the password sequence and determines whether the entered digits match the expected sequence.

### Correct password

When the user enters `1234`, the controller recognizes the correct password and asserts the unlock indication according to the RTL's control sequence.

### Incorrect password

If the entered password does not match the programmed password, the controller generates an error indication and updates its incorrect-attempt tracking logic.

### Lockout after three failures

The controller is designed to enter the locked state after three incorrect password attempts.

The lockout behavior prevents another normal password attempt from being accepted until the controller is reset or otherwise returned to its starting condition, as defined by the RTL.

### Example test sequence

| Test | Entered password | Expected behavior |
|---|---|---|
| 1 | `1234` | Correct password accepted |
| 2 | `1256` | Incorrect password detected |
| 3 | `9999` | Second incorrect attempt |
| 4 | `8888` | Third incorrect attempt; system locks |

These four scenarios were exercised by the recorded testbench, and all four tests passed in the final OPT7 RTL simulation.

## 7. Verilog RTL Design

RTL stands for Register Transfer Level. It describes how data moves between registers and how logic operates on that data during clock cycles.

The password controller is implemented using Verilog constructs such as:

- Module declarations.
- Input and output ports.
- Registers and internal signals.
- Clocked procedural blocks.
- Conditional statements.
- Password comparison logic.
- Attempt tracking and output control.

### Important design signals

| Signal | Function |
|---|---|
| `clk` | Clock input |
| `reset` | Initializes the controller |
| `digit[3:0]` | Current password digit |
| `enter` | Indicates a digit-entry event |
| `unlock` | Successful password indication |
| `error` | Incorrect password indication |
| `locked` | Lockout status |

The final optimized candidate is stored in `digital_lock_opt7.v`.

The optimization process used an area-oriented synthesis configuration while preserving the RTL behavior. In particular, the OPT7 configuration sets `ABC_AREA = 1`, selecting the area-oriented ABC synthesis script in the OpenROAD flow.

This is a synthesis configuration change; it should not be described as a new password algorithm.

## 8. Functional Verification Using Icarus Verilog

Functional verification checks whether the RTL behaves according to the design requirements.

The project uses Icarus Verilog to compile the RTL and its testbench. The resulting simulation executable is run using `vvp`.

### Simulation procedure

From the project root, assuming the repository contains the following files:

```text
RTL/
├── digital_lock_opt7.v
└── digital_lock_tb.v
```

Run:

```bash
iverilog -g2012 -s digital_lock_tb \
  -o opt7_sim \
  RTL/digital_lock_opt7.v \
  RTL/digital_lock_tb.v

vvp opt7_sim
```

### Recorded simulation results

| Test | Result |
|---|---|
| Correct password `1234` | PASS |
| Incorrect password `1256` | PASS |
| Second incorrect password `9999` | PASS |
| Third incorrect password `8888` | PASS |
| Lockout after three failures | PASS |

The recorded terminal output confirms that the four password tests passed and the system entered the locked state after the third incorrect attempt.

Functional simulation demonstrates the tested behavior. It does not, by itself, prove physical timing closure or silicon-level correctness.

## 9. Waveform Analysis Using GTKWave

GTKWave can be used to inspect the waveform file generated by the simulation testbench.

The waveform allows the designer to observe how the clock, reset, password digit, enter signal, and output signals change over simulation time.

### Signals to inspect

- `clk`
- `reset`
- `digit`
- `enter`
- `unlock`
- `error`
- `locked`

The waveform should be interpreted together with the testbench sequence. It can demonstrate when the password is entered, when the controller responds, and when the lockout condition is reached.

If the testbench generates `digital_lock.vcd`, it can be opened with:

```bash
gtkwave digital_lock.vcd
```

The actual VCD filename depends on the testbench's `$dumpfile` statement.

## 10. ASIC Implementation Flow

After functional verification, the RTL is processed through the OpenROAD physical design flow.

The major stages are as follows.

### Stage 1: RTL design

The behavior of the password controller is described in Verilog.

### Stage 2: Functional simulation

The RTL and testbench are compiled and simulated to verify the intended behavior.

### Stage 3: Logic synthesis

The RTL is translated into a gate-level netlist using standard cells from the SKY130HD technology library.

### Stage 4: Floorplanning

The physical design region is established, including the floorplan dimensions and core configuration.

### Stage 5: Placement

Standard cells are positioned within the core to prepare the design for routing.

### Stage 6: Clock-tree and timing preparation

The physical implementation flow handles clock distribution and timing-related optimization according to its configured stages.

### Stage 7: Routing

Signal connections are implemented through the routing layers.

### Stage 8: Final physical outputs

The flow generates the final netlist, physical database, design exchange format, and GDS layout.

The final GDS represents the physical layout generated by the implementation flow. Its generation alone does not constitute complete manufacturing signoff.

## 11. SKY130HD Standard-Cell Technology

SKY130HD is the standard-cell platform used in this project.

During synthesis and physical implementation, logic operations are mapped to cells available in the technology library. Examples include NAND gates, NOR gates, inverters, buffers, and flip-flops.

The selected standard cells affect the design's:

- Area.
- Propagation delay.
- Transition behavior.
- Setup and hold timing.
- Power consumption.

The final physical implementation depends on the RTL, synthesis choices, timing constraints, placement, and routing.

## 12. Timing Constraints

The project uses a 10 ns clock-period constraint.

The design's SDC constraints specify the clock and the relevant input and output timing assumptions used by the flow.

The main constraint values include:

| Constraint | Value |
|---|---:|
| Clock period | 10 ns |
| Input delay | 2 ns |
| Output delay | 2 ns |
| Clock uncertainty | 0.2 ns |

These values describe the configured timing environment. They should be interpreted together with the specific SDC file used for each report.

Timing constraints must remain consistent when comparing implementation candidates. Changing constraints solely to make timing results appear better would not constitute a valid optimization.

## 13. Static Timing Analysis Across 16 Corners

Static Timing Analysis evaluates timing paths without relying on a functional simulation sequence.

The project includes 16 corner-specific STA scripts and logs. These use corner-specific SKY130HD Liberty libraries and estimated global-routing parasitics.

The corners represent different process, voltage, and temperature conditions, including slow, typical, and fast library models.

### Important timing concepts

**Setup timing**

Setup analysis checks whether data reaches a receiving register sufficiently early before its active clock edge.

Positive setup slack indicates that the analyzed path meets its reported setup requirement.

**Hold timing**

Hold analysis checks whether data remains stable long enough after the receiving register's active clock edge.

Positive hold slack indicates that the analyzed path meets its reported hold requirement.

**Timing corner**

A timing corner represents a particular library and operating condition. Different corners can produce different propagation delays and timing margins.

### Recorded OPT7 results

| Metric | Recorded result |
|---|---:|
| Number of timing corners | 16 |
| Worst reported setup slack | +0.0733 ns |
| Corner with worst setup slack | `ss_n40C_1v28` |
| Worst reported hold slack | +0.12 ns |
| Corner with worst hold slack | `ff_100C_1v95` |
| Parasitic estimation | Global-routing estimates |

The hold-corner name above must be checked against the actual 16-corner logs before final publication. Use the precise corner identifier printed in the log that contains the minimum hold slack.

### Timing interpretation

All 16 recorded corner logs show positive reported setup and hold slack.

However, the worst setup slack is only 0.0733 ns, equivalent to 73.3 ps. This is a narrow timing margin, so the result should be described as positive reported slack with limited margin.

These results use estimated global-routing parasitics. They are not signoff timing results, and they do not establish that the design is ready for fabrication.

## 14. Final Physical-Design Results

The final OPT7 implementation report records the following metrics.

| Parameter | Recorded value |
|---|---:|
| Final standard-cell area | 589.315 µm² |
| Final GDS filename | `6_final.gds` |
| Approximate GDS file size | 213 KB |
| Configured clock period | 10 ns |
| Flow-reported setup violation count | 0 |
| Flow-reported hold violation count | 0 |
| Flow-reported minimum clock period | 3.29 ns |
| Flow-reported maximum frequency | 303.66 MHz |

The final standard-cell area is taken from the finish-stage JSON report.

The flow's zero setup and hold violation counts refer to its own timing-reporting context. They should not be confused with the separate corner-specific STA results.

The minimum clock period and maximum frequency are also flow-reported values. They should not be treated as guaranteed operating specifications for a fabricated chip.

## 15. Power Estimation

A power-estimation screenshot is included in the project evidence.

Power estimates depend on the mapped cell netlist, library power models, switching activity, clock assumptions, and the analysis setup.

The project should report a numerical power value only when the underlying report provides the value, units, and analysis conditions.

The existence of a power-estimation screenshot alone is not sufficient to claim a specific power consumption or power reduction.

## 16. Repository Structure

The final submission is organized into the following main directories:

```text
Password-Based-Security-Controller/
├── RTL/
│   ├── digital_lock_opt7.v
│   └── digital_lock_tb.v
├── Constraints/
│   └── digital_lock_opt7.sdc
├── Final_Outputs/
│   ├── 6_final.v
│   ├── 6_final.def
│   ├── 6_final.odb
│   ├── 6_final.sdc
│   └── 6_final.gds
├── STA_Reports/
│   ├── Corner-specific TCL scripts
│   └── Corner-specific STA logs
├── OpenROAD/
│   ├── config.mk
│   ├── 6_finish.rpt
│   └── 6_report.json
├── Screenshots/
└── RESULTS_SUMMARY.txt
```

This structure separates source code, timing constraints, physical outputs, timing evidence, and screenshots.

## 17. Limitations

The current implementation has several limitations:

1. The password is programmed into the RTL rather than stored in a configurable secure credential system.
2. The interface is a hardware-level digit-entry interface; no physical keypad or display is included in the documented implementation.
3. The worst setup slack is positive but narrow.
4. The STA uses estimated global-routing parasitics rather than a complete signoff extraction flow.
5. No silicon fabrication or physical-chip measurement results are claimed.
6. Security features such as password encryption, tamper detection, and persistent lockout are outside the demonstrated scope.

## 18. Future Enhancements

Potential improvements include:

- A keypad interface for direct user entry.
- A UART interface for password input.
- Configurable password storage.
- An LCD or OLED status display.
- A configurable reset and lockout recovery mechanism.
- Improved setup timing margin across all timing corners.
- Detailed power analysis using appropriate switching activity.
- Additional physical verification and signoff analysis.

These are possible future improvements, not features claimed to be implemented in the current version.

## 19. Conclusion

The ASIC Password Controller demonstrates a complete educational RTL-to-layout workflow for a small digital control circuit.

The controller was implemented using Verilog HDL, functionally simulated, mapped to SKY130HD standard cells, and processed through the OpenROAD physical design flow.

The final implementation generated a standard-cell netlist and GDS layout. The recorded results include a final standard-cell area of 589.315 µm² and 16-corner STA with positive reported setup and hold slack.

The narrow worst-case setup margin highlights the importance of multi-corner timing analysis and further optimization. The project provides practical experience in digital RTL design, simulation, synthesis, physical implementation, GDS generation, and static timing verification.

## 20. Watch the Project Demonstration

[▶ Watch the ASIC Password-Based Security Controller Presentation](https://drive.google.com/file/d/11AN30ovUOXhc4GHK5VgnwguEy2Ap3YmA/view?usp=sharing)

This video demonstrates my ASIC Implementation of a Password-Based Security Controller project, including:

- RTL design using Verilog
- Password-based security controller implementation
- Testbench development and verification
- Functional simulation and waveform analysis
- Logic synthesis
- ASIC physical design flow
- Static Timing Analysis (STA)
- Final implementation results and reports

A demonstration video can follow this sequence:

1. Introduce the ASIC Password Controller and its objective.
2. Show the Verilog RTL and explain the password-entry logic.
3. Demonstrate the correct-password and incorrect-password test cases.
4. Show the Icarus Verilog simulation output.
5. Open the GTKWave waveform and explain the key signals.
6. Show the OpenROAD configuration and timing constraints.
7. Demonstrate floorplanning, placement, and routing evidence.
8. Show the final layout and generated GDS.
9. Explain the final standard-cell area.
10. Present the 16-corner STA results and discuss the narrow setup margin.
11. Summarize the project limitations and future improvements.

## Author

**Aravind Reddy**

GitHub repository: [Password-Based-Security-Controller](https://github.com/Aravind6082/Password-Based-Security-Controller)

## License

No license is specified. Add an appropriate license if the project is intended for public reuse or modification.
