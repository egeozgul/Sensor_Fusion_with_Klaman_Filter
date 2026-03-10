# GPS-IMU Fusion with Kalman Filter

A Kalman Filter implementation for fusing GPS and IMU sensor data to estimate 2D vehicle pose. Built for EECE 7150 - Field Robotics.

## Overview

IMU sensors provide high-frequency measurements but drift over time. GPS provides accurate global positions but only updates at 1 Hz. This project fuses both using a Kalman Filter to get continuous, low-noise pose estimates at 100 Hz.

<img width="535" height="511" alt="sensorFusion" src="https://github.com/user-attachments/assets/f1e37407-4ba8-49e2-955c-874d38f7b8a9" />


## State Vector

The 7-dimensional state vector tracks:

| State | Description |
|-------|-------------|
| `x, y` | Vehicle position in ENU coordinates |
| `θ_gps` | GPS-derived heading |
| `vx, vy` | Velocity components |
| `θ_integrated` | Continuously integrated gyroscope heading |
| `heading_offset` | Calibrated offset between GPS and IMU heading |

## Key Features

- **Sensor fusion** at 100 Hz using IMU prediction + 1 Hz GPS correction
- **Adaptive measurement noise** — filter trusts high-quality GPS more than degraded signals (`R_good = 4.0`, `R_poor = 16.0`)
- **Automatic heading calibration** — continuously estimates IMU heading offset from GPS velocity vectors (converges to −82.40°)
- **Heading fallback logic** — switches between GPS heading, calibrated IMU heading, and raw integrated heading based on GPS availability

## Dataset

- **535,754** IMU measurements processed at 100 Hz
- **21,871** GPS measurements at 1 Hz
- IMU biases estimated from first 5 seconds of stationary data

## Results

- Significantly smoother trajectory compared to raw GPS
- Reliable pose estimation during short GPS outages using dead reckoning
- Known degradation in tunnels due to gyroscope drift accumulation — heading errors compound quickly without GPS corrections, causing lateral drift

## Limitations

- Dead reckoning accuracy is tightly coupled to yaw quality; low-cost MEMS gyroscopes drift too fast for reliable long-duration GPS-denied navigation
- Position errors accumulate without bound during extended GPS blackouts
- Accuracy is restored quickly upon GPS reacquisition

## Future Work

- Extend to 6-DOF pose estimation
- Integrate additional sensors (e.g., wheel odometry, magnetometer) to improve GPS-denied performance
- Develop initialization procedures that don't require initial GPS availability
