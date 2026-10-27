# SCCSTSForge Pro (v1.0.0)

**CCSDS 922.4-B-1 & CCSDS 921.1-B-2 Service Control Cross Support Transfer Service (SC-CSTS) Workstation, Real-Time Ground Antenna Reconfiguration Engine, Directive Invoker, and 10-Point Normative Ground Station Control Auditor**

## 1\. Executive Summary

**SCCSTSForge Pro** is an autonomous, zero-dependency developer studio and headless command-line interface implementing the **CCSDS 922.4-B-1 (Service Control Cross Support Transfer Service)** and **CCSDS 921.1-B-2 (CSTS Specification Framework)** standards.

In space communications, while offline scheduling tools like SLE-SM handle pre-pass bookings and transfer services (FF-CSTS, TD-CSTS, MD-CSTS) handle data movement, **Service Control (SC-CSTS)** provides the real-time operational control plane that enables a Mission Data Operations System (MDOS / User MOC) to command and reconfigure ground station equipment during an active pass. Without SC-CSTS, flight controllers cannot adjust uplink transmitter power, slew tracking antennas, or adapt carrier modulations dynamically, forcing ground engineers into high-friction phone coordination during critical spacecraft maneuvers.

SCCSTSForge Pro delivers:

1. **CSTS Association State Machine**: Full compliance with the CCSDS 921.1-B-2 lifecycle (`UNBOUND` \-\> `READY` \-\> `ACTIVE`).  
2. **Directive Invocation Procedure (`INVOKE-DIRECTIVE` / `DIRECTIVE-REPORT`)**: Enables remote execution of ground equipment directives (`TRANSMITTER_CARRIER_ON`, `TRANSMITTER_CARRIER_OFF`, `SET_TRANSMITTER_POWER`, `SLEW_ANTENNA`, `TOGGLE_AUTOTRACK`) with deterministic execution reporting.  
3. **Dynamic Parameter Reconfiguration (`SET-PARAMETER` / `GET-PARAMETER`)**: Allows real-time tuning of uplink carrier frequencies, telecommand bit rates, and polarizations without interrupting the active CSTS association.  
4. **Radiation Horizon Safety Interlock**: Enforces automated interlocks preventing RF carrier radiation whenever antenna elevation falls below the 10.0° site radiation horizon mask (ITU-R SA.509 compliance).  
5. **Active Radiating Carrier Protection**: Blocks abrupt carrier frequency shifts while the high-power amplifier is actively radiating energy, protecting transmitter tubes from damaging arcing.  
6. **Asynchronous Event Telemetry (`DATA-NOTIFICATION`)**: Pushes unsolicited hardware alerts, monopulse autotrack lock status, and safety trip notifications to the user MOC.  
7. **10-Point Normative Ground Station Control Auditor**: Automated verification of rules `SCCSTS-001` through `SCCSTS-010`.

## 2\. Standards Compliance

- **CCSDS 922.4-B-1**: Cross Support Transfer Services — Service Control (Blue Book / Recommendation for Space Data System Standards).  
- **CCSDS 921.1-B-2**: Cross Support Transfer Services — Specification Framework (Blue Book, Issue 2).  
- **ITU-R SA.509**: Space research radiation safety and ground station elevation masks.

## 3\. Protocol Architecture & Service Control Flow

\[Mission Data Operations System (MDOS / User MOC)\]

                     \|

                     v  (INVOKE-DIRECTIVE / SET-PARAMETER)

\+-------------------------------------------------------------+

\|             SCCSTSForge Pro Service Control Hub             \|

\|      \- CSTS Association State Machine (UNBOUND/READY/ACTIVE)\|

\|      \- Directive Dispatcher & DIRECTIVE-REPORT Handler      \|

\|      \- Parameter Engine (Uplink Freq, Bitrate, Power)       \|

\|      \- Safety Interlocks (10.0° Radiation Mask, Freq Lock)  \|

\+-------------------------------------------------------------+

                     \|

                     v  (Equipment Control Bus)

\+-------------------------------------------------------------+

\|          Cross Support Complex (CSC Ground Terminal)        \|

\|      \- Antenna Gimbal Motors (Azimuth / Elevation)          \|

\|      \- Monopulse Autotracking Receiver Feeds                \|

\|      \- 20 kW / 25 kW High-Power Uplink Transmitter (TWTA)   \|

\|      \- Asynchronous DATA-NOTIFICATION Telemetry Stream      \|

\+-------------------------------------------------------------+

## 4\. Quickstart & Usage

### 4.1 Headless Python 3 CLI

Invoke a ground station directive (e.g. radiated carrier engage):

python3 source-code/sccsts\_cli.py directive \--name TRANSMITTER\_CARRIER\_ON

Inspect real-time ground station equipment status:

python3 source-code/sccsts\_cli.py status

Slew ground antenna and adjust transmitter power:

python3 source-code/sccsts\_cli.py directive \--name SLEW\_ANTENNA \--az 145.2 \--el 28.5

python3 source-code/sccsts\_cli.py directive \--name SET\_POWER \--power 25000

Execute the 10-point normative aerospace ground station control standards audit:

python3 source-code/sccsts\_cli.py audit

Simulate an end-to-end service control pass operations cycle:

python3 source-code/sccsts\_cli.py run

### 4.2 Interactive Browser Control Console

Open `source-code/index.html` directly in any modern web browser:

- Real-time CSTS Association State Bar (`UNBOUND`, `READY`, `ACTIVE`).  
- Ground Equipment Directive Invocation Center (Slew Antenna, Set Power, Carrier On/Off).  
- Dynamic Radiation Horizon safety interlock indicator (\< 10.0° elevation block).  
- Asynchronous Ground Complex Telemetry & Event Feed (`DATA-NOTIFICATION`).  
- Real-time 10-point normative aerospace ground station control scorecard.

## 5\. Automated Verification Test Suite

Run the automated test runner:

python3 source-code/test\_runner.py

**Results**:

- Ran 10 tests in 0.002s.  
- **Pass Rate**: 100% (10/10 PASS).  
- **Certification Status**: `CCSDS_922_4_SCCSTS_CERTIFIED`.

## 6\. Commercial Model & Licensing

- **Community Open-Core**: Free CLI inspection tools and reference CSTS directive invokers for university satellite teams and research ground stations.  
- **Ground Network Engineering Seat (\$349/year)**: Commercial workstation license with real-time antenna slew monitors, automated interlock diagnostics, and Space-Track pass synchronization daemons.  
- **Enterprise Ground Complex Service Control License (\$2,499 one-time site license)**: Full source code license, automated DSN/ESTRACK equipment bus adapters, multi-antenna array remote control daemons, and commercial Mission Operations Center (MOC) integration support.

## 7\. Known Limitations

- Operates as a software service control gateway; physical motor drives, klystron high-voltage power supplies, and waveguide switches require hardware interface controllers.  
- Directive responses model deterministic hardware timing; physical antenna slew rates (e.g. 3°/sec) require continuous status polling.