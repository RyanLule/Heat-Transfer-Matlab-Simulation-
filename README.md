# Heat Transfer & PID Temperature Control — MATLAB Simulation

MATLAB simulation of convective heat transfer in a flowing pipe system, and a PID
controller that drives the fluid outlet temperature to a setpoint by adjusting
heater power.

## Project structure

This project has two parts, run in order:

1. heat_transfer_analysis — Calculates the Reynolds number, Nusselt
   number (via the Dittus-Boelter correlation), and convective heat transfer
   coefficient for water flowing through a pipe at four velocities (0.5, 1.0,
   1.5, 2.0 m/s). Produces plots of Nu vs Re and h vs Re, and saves the
   results to `heat_transfer_results.mat` for use in the control script.

2. pid_temperature_control — Uses a lumped-capacitance energy balance
   to model the fluid in a pipe section being heated by a controllable
   heater, with heat loss to the surrounding air through an insulated pipe
   wall. A PID controller adjusts heater power to drive the fluid
   temperature from an initial condition of 293 K to a setpoint of 340 K.

## How to run

```matlab
heat_transfer_analysis      % run first - generates heat_transfer_results.mat
pid_temperature_control     % run second - loads results, simulates control loop
```

## Method

**Dittus-Boelter correlation** (turbulent internal flow):

```
Nu = 0.023 * Re^0.8 * Pr^0.4      (n = 0.4 for heating)
h  = Nu * k_fluid / D
```

**PID control loop** — the fluid control volume is modelled with:

```
m * cp * dT/dt = Q_heater(t) - U_loss * A_surf * (T - T_ambient)
```

where `Q_heater(t)` is the PID controller output (heater power, saturated
between 0 W and a maximum actuator limit).

**Important modelling note:** the convective coefficient `h` calculated in
`heat_transfer_analysis.m` describes internal wall-to-fluid heat transfer
and is large (thousands of W/m².K), appropriate for forced convection
inside the pipe. This is *not* reused as the ambient heat-loss coefficient
in the control model — an insulated pipe loses heat to the surrounding air
through a much smaller external coefficient (`U_loss`, ~15 W/m².K here).
Conflating the two would make the setpoint unreachable with a realistically
sized heater.

## Results

Running the scripts produces:

- Reynolds number, Nusselt number, and heat transfer coefficient for each
  of the four flow velocities (turbulent flow confirmed for all four cases)
- A temperature response curve showing the fluid heating from 293 K to the
  340 K setpoint, with a settling time of ~80 s and small overshoot
- Heater power (controller output) and error over time

## Skills demonstrated

- Convective heat transfer correlations (Dittus-Boelter)
- Reynolds/Nusselt number analysis for turbulent internal flow
- PID controller design (proportional, integral, derivative tuning) with
  actuator saturation and anti-windup
- Numerical simulation via explicit time-stepping (Euler integration)
- MATLAB scripting, plotting, and data handling (`.mat` file interchange
  between scripts)
