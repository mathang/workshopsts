# STS_Singapore
A STS web app for Singapore use. It is designed to log data from the phone sensors, then uses algorithms to autoamtically identify the STS movement.  

Sit-to-Stand (STS) Analyzer – Technical Summary
Overview

The STS Analyzer is a browser-based motion analysis tool that records accelerometer and gyroscope data from a smartphone to automatically detect and time a 5× Sit-to-Stand (STS) test.
It uses real-time signal processing and heuristic detection logic to identify the active movement window and compute its duration.

Algorithm Pipeline
1. Data Acquisition

Sensors: Device accelerometer and gyroscope

Sampling rate: 20 Hz (50 ms interval)

Recorded values: [timestamp, accelX, accelY, accelZ, gyroAlpha, gyroBeta, gyroGamma]

The Y-axis acceleration (vertical component) is used for movement detection.

2. Signal Processing

High-pass filter (0.25 Hz)
Removes gravity and drift using a simple recursive Butterworth-style filter.

Teager–Kaiser Energy Operator (TKEO)
Emphasises rapid acceleration changes:

ψ[i] = a[i]^2 − a[i+1] * a[i−1]


Envelope smoothing
Applies a 0.25 s moving average to the TKEO signal to create a clean energy envelope.

3. Baseline and Thresholds

The quietest 2 s segment within the first 15 % of data defines the rest baseline.

From this baseline:

Mean = μ

Standard deviation = σ

Dynamic thresholds are set as:

High: thrHi = μ + 7σ

Low: thrLo = μ + 3σ

4. Movement Detection Logic

Start: Envelope rises above thrLo.

End: Envelope remains below thrLo for ≥ 2 s and ≥ 75 % of those samples are below threshold.

Segments must include at least one crossing of thrHi to be considered valid.

If multiple segments exist, the one with the greatest total envelope energy is selected.

5. Duration and Validation

The STS duration is computed as:

duration = (t_end − t_start) / 1000


Valid durations are 4 s – 30 s.

Out-of-range detections are flagged as invalid.

6. Visualization

After analysis, the system plots:

Blue line: Smoothed TKEO energy envelope

Green dashed line: Low threshold (thrLo)

Red dashed line: High threshold (thrHi)

Orange shaded region: Detected STS window

The visual feedback assists with debugging and parameter tuning.

Key Features

Works entirely in-browser, no backend required

Robust to noise and slow transitions (2 s / 75 % rule)

Thresholds adapt dynamically to each recording

Real-time plot for validation and refinement

Author: Ross Clark, PhD
License: MIT
Version: 2.0 – November 2025
