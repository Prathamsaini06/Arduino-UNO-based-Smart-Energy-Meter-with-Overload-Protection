# Arduino UNO Based Smart Energy Meter with Overload Protection

A Proteus-simulated smart energy meter built around the Arduino UNO. The
system monitors AC voltage and load current, calculates power and energy
consumption, estimates electricity cost, and disconnects the load
through a relay when an overload condition is detected.

**Project domain:** Microcontrollers and Embedded Systems\
**Controller:** Arduino UNO (ATmega328P)\
**Simulation software:** Proteus Design Suite\
**Programming environment:** Arduino IDE\
**Project team:** Pratham Saini and Aditya Sharma

------------------------------------------------------------------------

## Table of Contents

-   [Project Overview](#project-overview)
-   [Features](#features)
-   [System Architecture](#system-architecture)
-   [Components Used](#components-used)
-   [Pin Configuration](#pin-configuration)
-   [Working Principle](#working-principle)
-   [Calculations](#calculations)
-   [Overload Protection](#overload-protection)
-   [Software and Libraries](#software-and-libraries)
-   [How to Run the Simulation](#how-to-run-the-simulation)
-   [Simulation Results](#simulation-results)
-   [Limitations and Safety](#limitations-and-safety)
-   [Future Improvements](#future-improvements)
-   [Contributors](#contributors)

## Project Overview

The project demonstrates an Arduino-based energy monitoring and
protection system. In the Proteus simulation, a 220 V AC source supplies
a load through relay contacts and an ACS712 current sensor.

A voltage-sensing circuit provides a scaled, biased signal to Arduino
analog input `A0`. The ACS712 current sensor provides its output to
analog input `A1`. The Arduino processes the sampled signals and
displays voltage, current, energy consumption, estimated cost, and
operating status on a 16×2 LCD.

When the measured RMS current crosses the configured overload threshold,
the Arduino de-energizes the relay driver to disconnect the load and
indicates the overload condition using the red LED and LCD.

> **Implementation note:** This project is documented as a Proteus
> simulation. Do not interpret the simulation as a tested,
> mains-connected hardware prototype.

## Features

-   AC voltage sensing using a transformer-based voltage-sensing circuit
    in Proteus.
-   Load-current sensing using an ACS712-30A Hall-effect current sensor.
-   Voltage and current measurement using Arduino analog inputs.
-   Power and energy-consumption calculation.
-   Electricity-cost estimation using a configurable tariff.
-   Live readings and status on a 16×2 LCD.
-   Relay-based load disconnection during overload.
-   Green, yellow, and red LED status indication.
-   Proteus simulation of the complete circuit.

## System Architecture

``` text
                 220 V AC Source
                 (Proteus model)
                        |
                   Relay Contacts
                        |
                  ACS712 Sensor
                        |
                    Load Bank
                        |
                  Return / Neutral

 Voltage-sensing circuit ---------> Arduino A0
 ACS712 current-sensor output -----> Arduino A1

                                  Arduino UNO
                               /       |       \
                            16x2 LCD  LEDs   Relay Driver
                                               |
                                             Relay
```

The voltage and current sensing circuits provide measurement signals to
the Arduino. The controller calculates the electrical quantities,
updates the LCD and controls the relay and LEDs according to the load
condition.

## Components Used

  -----------------------------------------------------------------------
  Component                           Purpose
  ----------------------------------- -----------------------------------
  Arduino UNO R3                      Main controller based on the
                                      ATmega328P

  TR1 transformer model               Simulated voltage-sensing stage
                                      representing a ZMPT101B-type
                                      arrangement

  ACS712ELCTR-30A-T                   Hall-effect current sensor

  16×2 LCD (LM016L)                   Displays measurements and system
                                      status

  5 V SPDT relay                      Connects or disconnects the
                                      simulated load

  BC547 transistor                    Relay coil driver

  1N4007 diode                        Flyback protection for the relay
                                      coil

  1 kΩ resistor                       Transistor base resistor

  10 kΩ resistors and 10 µF capacitor 2.5 V bias network for the
                                      voltage-sensing signal

  220 Ω resistors                     LED current limiting

  Load resistors                      Simulated electrical load
  -----------------------------------------------------------------------

## Pin Configuration

  Arduino pin   Connection                                      Function
  ------------- ----------------------------------------------- -----------------------------
  A0            Voltage-sensing circuit (TR1)                   Analog voltage input
  A1            ACS712 VIOUT                                    Analog current input
  D4            BC547 relay-driver base through 1 kΩ resistor   Relay control
  D5            Red LED                                         Overload indication
  D6            Yellow LED                                      Warning indication
  D7            Green LED                                       Normal-operation indication
  D8            LCD D7                                          LCD data
  D9            LCD D6                                          LCD data
  D10           LCD D5                                          LCD data
  D11           LCD D4                                          LCD data
  D12           LCD E                                           LCD enable
  D13           LCD RS                                          LCD register select

The LCD is operated in 4-bit mode. The LCD `RW` pin is connected to GND.
The LCD, sensor modules and relay-driver circuit use the appropriate 5 V
and GND connections in the simulation.

## Working Principle

1.  **Voltage sensing:** The voltage-sensing circuit scales the AC
    voltage signal and biases it around approximately 2.5 V so it can be
    sampled by the Arduino analog input.
2.  **Current sensing:** The ACS712 produces an analog output
    corresponding to the load current. Its zero-current output is
    approximately half its supply voltage.
3.  **Sampling:** The Arduino samples the voltage and current signals
    over a measurement interval.
4.  **Calculation:** The program calculates RMS voltage, RMS current and
    power using the sampled signals and calibration settings.
5.  **Energy accumulation:** Energy is accumulated over time in
    kilowatt-hours (kWh).
6.  **Cost estimation:** The accumulated energy is multiplied by the
    configured tariff.
7.  **Display:** The LCD alternates between measurement/status
    information and energy/cost information.
8.  **Protection:** If the overload condition is detected, the Arduino
    turns the relay off and indicates the trip condition.

## Calculations

The project uses the following relationships:

-   **Power:** (P = V `\times `{=tex}I) (for a simplified apparent-power
    estimate). If the code calculates real power from sampled voltage
    and current waveforms, the result depends on the implemented phase
    calibration and waveform processing.
-   **Energy:** (E`\text{ (kWh)}`{=tex} = P`\text{ (W)}`{=tex}
    `\times `{=tex}t`\text{ (hours)}`{=tex} / 1000)
-   **Estimated cost:** `Cost = Energy (kWh) × Tariff (₹/kWh)`

The presentation uses a sample tariff of **₹7 per kWh**. The tariff can
be changed in the Arduino program.

Voltage and current calibration constants must be verified against
suitable reference instruments for any real hardware implementation.
Simulation readings depend on the Proteus model and configured
calibration values.

## Overload Protection

The Arduino compares the measured RMS current with the overload
threshold defined in the program.

When the overload condition is detected:

-   The relay-control output on D4 is set LOW.
-   The relay opens and disconnects the simulated load.
-   The red LED on D5 indicates the overload condition.
-   The LCD displays an overload status.
-   The program includes a retry/reconnection interval configured in the
    code.

The program settings shown in the project presentation include an
overload threshold of **0.5 A** and a retry interval of **10 seconds**.
These are simulation/program settings, not certified protection ratings.

## Software and Libraries

-   **Arduino IDE** --- writing and compiling the Arduino program.
-   **Proteus Design Suite** --- circuit design and simulation.
-   **LiquidCrystal** --- controlling the 16×2 LCD.
-   **EmonLib** --- voltage/current measurement and power-related
    calculations.

The source code shown in the presentation includes:

``` cpp
#include <LiquidCrystal.h>
#include "EmonLib.h"
```

## How to Run the Simulation

1.  Clone or download this repository.
2.  Open the Proteus project file included in the repository.
3.  Open the Arduino sketch in Arduino IDE.
4.  Install the required libraries: `LiquidCrystal` (normally included
    with Arduino AVR boards) and `EmonLib`.
5.  Compile the sketch and generate the Arduino HEX file, if the Proteus
    Arduino model requires it.
6.  Load the compiled HEX file into the Arduino UNO component in
    Proteus.
7.  Start the simulation.
8.  Change the simulated load conditions and observe the LCD readings,
    LEDs and relay response.

**Repository note:** Add your actual `.pdsprj`/Proteus project file and
`.ino` sketch to the repository before expecting others to run the
simulation. Update filenames and screenshots in this README to match the
files you upload.

## Simulation Results

The project presentation records the following example readings:

  Simulated load condition     Voltage   Current Reported behaviour
  -------------------------- --------- --------- ------------------------
  R5 only                        220 V    0.33 A Normal operation
  R6 only                        220 V    0.40 A Normal operation
  R5 + R6                        220 V    0.78 A Overload trip reported

The presentation also gives example energy and cost values for the
individual-load cases after one hour. These are simulation/presentation
results, not independently verified laboratory measurements.

## Limitations and Safety

-   This repository documents a simulation, not a certified electricity
    meter or safety device.
-   The simulation's transformer model is used to represent the
    voltage-sensing stage; it is not the same as validating a physical
    ZMPT101B module.
-   Sensor calibration, phase correction, waveform sampling and
    power-factor handling affect measurement accuracy.
-   The presentation contains differing sensor-sensitivity and overload
    descriptions in different sections. Verify the actual sensor
    datasheet and the final sketch before treating any numerical value
    as a hardware specification.
-   **Mains electricity can cause fatal electric shock or fire. Do not
    connect this circuit to a live 220 V supply based only on this
    simulation or README.** Any physical prototype must be designed,
    enclosed, fused, isolated, tested and reviewed by a qualified person
    using appropriate electrical-safety practices.

## Future Improvements

-   ESP32-based IoT monitoring through a web dashboard or mobile
    application.
-   Data logging using EEPROM or an SD card.
-   Remote alerts through SMS, email or an application.
-   Configurable overload threshold and safer, well-defined reset
    behaviour.
-   Improved calibration and measurement accuracy.
-   A properly isolated, enclosed and safety-reviewed hardware
    prototype.

## Contributors

-   **Pratham Saini**
-   **Aditya Sharma**

B.Tech --- Electronics and Communication Engineering\
Graphic Era (Deemed to be University)

------------------------------------------------------------------------

If you find this project useful, feel free to ⭐ the repository.
