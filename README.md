# Passive Balanced Mixer with Schottky Diodes (AWR Microwave Office)

Lab project of *Radio Frequency and Antenna Subsystems*
(Master's in Telecommunication Engineering, UC3M).

Design of a **passive balanced mixer** built with self-biased Schottky diodes
(no DC bias network) in **microstrip technology** on an FR4 substrate, simulated
in AWR Microwave Office. The RF signal is a small signal applied to the mixer
input, the local oscillator (LO) is the pump that drives the diodes into their
non-linear region, and the output is the intermediate frequency (IF) produced by
the RF-LO beat.

## Specifications

| Parameter | Value |
|---|---|
| RF frequency | 2.3 GHz |
| RF bandwidth | 100 MHz |
| RF matching in band | < -15 dB |
| RF input power | < -10 dBm |
| Intermediate frequency | 200 MHz |
| LO frequency | 2.1 GHz (f_LO = f_RF - f_IF) |
| Maximum LO power | 5 dBm |
| LO matching | < -20 dB |
| Conversion loss | > -7 dB |
| LO-IF isolation | > 40 dB |
| LO-RF isolation | > 30 dB |
| Substrate (FR4) | h = 0.5 mm, er = 4.6, tan d = 0.01, t = 0.035 mm |
| Diodes | HSMS-28xx family (for example HSMS-2822, two diodes per package) |

The diode is modelled with the non-linear SPICE parameters from the
manufacturer datasheet, entered into the `SDIODE` component of Microwave Office.

## Design flow

1. **180-degree hybrid (rat-race) with ideal lines.** Four `TLIN` sections, three
   of them a quarter wavelength long and the fourth three quarters of a
   wavelength, which is what produces the 180-degree phase shift. All sections at
   Z0 = 50 ohm. Port assignment: 1 for RF, 2 for the in-phase (sum) output, 3 for
   the LO and 4 for the anti-phase (difference) output.
2. **Conversion to real microstrip.** Every `TLIN` is replaced by an `MLIN` whose
   width and physical length are computed with TXLine for the FR4 substrate,
   keeping the electrical length of the ideal design. The project uses global
   variables for frequencies, impedances and dimensions, so a change propagates
   through every schematic.
3. **Large-signal LO matching.** The matching network for the LO port of one
   diode is tuned with a harmonic-balance port, because the diode impedance
   depends on the incident LO power: matching has to be done under large-signal
   conditions, otherwise the incident power is reflected and no mixing occurs.
4. **IF low-pass filter.** A first-order LC filter passes the 200 MHz IF but its
   roll-off is too gentle to suppress the LO and RF products, which need at least
   about 30 dB of attenuation. A second-order LC filter provides the required
   slope with a nearly lossless IF path.
5. **Full mixer.** The stages above are combined into a single schematic. An
   intermediate version without the `TL5` and `TL6` transmission lines showed the
   IF tone more attenuated than expected in the output spectrum; adding those two
   lines recovered the IF level.

## Results

- The ideal rat-race reproduces the expected S-matrix, with a 180-degree phase
  difference between the sum and difference ports.
- Moving to real microstrip costs an extra 0.2 to 0.5 dB of insertion loss and
  slightly degrades port matching and isolation, while preserving the
  directional behaviour of the hybrid.
- The second-order IF filter exceeds the attenuation requirement for the LO and
  RF products.
- In the complete mixer the IF tone is recovered after inserting `TL5` and `TL6`.
  The final IF level stays slightly below the ideal target of about -10 dBm, so
  the mixer is not ideal but performs well.

Conversion loss and the LO-IF and LO-RF isolations are obtained with a
network-analyser-type measurement in the harmonic-balance simulation.

## Repository contents

- `PROYECTO_FINAL.emp` - final AWR Microwave Office project
- `PROYECTO_FINAL_vD.emp` - earlier design iteration
- `PROYECTO_FINAL.vin` - AWR auxiliary project file
- `REPORT.pdf` - lab report, in Spanish

## How to run

Open the `.emp` project in AWR Microwave Office (AWR Design Environment) and run
the linear and harmonic-balance analyses. Simulation data sets are not included
in the repository, since AWR regenerates them locally on the first run.

## Notes

The lab statement and the base material were provided by the course instructors
and are not included in this repository.

## Authors

[Noel Andolz Aguado](https://github.com/nooelanag) and
[Daniel Lozano Uceda](https://github.com/dalouc)
