# Smart Parking System using STM32, VL53L5CX & LoRaWAN

## Overview

Urban parking areas often lack real-time occupancy monitoring, leading to congestion and inefficient space utilization.

This project implements a Smart Parking System using an STM32 microcontroller and a VL53L5CX multi-zone ToF sensor to detect vehicle presence across two adjacent parking slots using a single sensor. Occupancy data is transmitted via LoRaWAN to The Things Network (TTN) and visualized using a TagoIO dashboard.

The system demonstrates embedded firmware development, multi-zone sensor processing, wireless communication, and cloud integration.

---

## Problem Statement

Traditional parking systems rely on manual monitoring or camera-based solutions, which can be costly and infrastructure-heavy.

This project aims to:
- Detect parking slot occupancy accurately
- Monitor two slots using a single ToF sensor
- Transmit real-time status wirelessly over long distances
- Enable scalable and low-cost deployment
- Provide remote dashboard-based monitoring

---

## System Architecture

The VL53L5CX ToF sensor provides an 8x8 (64-zone) distance measurement grid.

In this implementation, a single sensor monitors two adjacent parking slots by dividing the 64 zones into two logical segments:

- Zones 0–31 → Slot 1
- Zones 32–63 → Slot 2

### Working Principle

1. Sensor captures 64 distance measurements.
2. Zones are divided into two halves (32 zones per slot).
3. For each slot:
   - Count zones below OCCUPIED_THRESHOLD_MM.
   - If more than 50% of zones are below threshold → slot marked OCCUPIED.
4. Slot states are packed into a compact 2-bit bitmap.
5. Bitmap is transmitted via LoRaWAN.
6. Data is forwarded to TTN and then to TagoIO for visualization.

---

## Cloud Integration & Dashboard

The LoRaWAN payload is transmitted to The Things Network (TTN).  
A webhook integration forwards decoded payload data to TagoIO.

### Data Flow

1. STM32 transmits slot bitmap via LoRaWAN.
2. Packet is received by TTN gateway.
3. TTN decodes the payload.
4. Webhook forwards data to TagoIO.
5. TagoIO dashboard visualizes slot occupancy in real-time.

### Dashboard Features

- Real-time parking slot status display
- Historical data logging
- Occupancy visualization
- Remote monitoring capability

---

## Hardware Components

- STM32 Nucleo (STM32L476RG)
- VL53L5CX ToF Sensor (8x8 multi-zone ranging)
- SX1262 LoRa Module
- Power supply and supporting circuitry

---

## Hardware Connections

### VL53L5CX (I2C Interface)
- SDA → PB9
- SCL → PB8
- INT → PA0
- VDD → 3.3V
- GND → GND

### SX1262 LoRa Module (SPI Interface)
- NSS (CS) → PA4
- SCK → PA5
- MISO → PA6
- MOSI → PA7
- RESET → PB0
- DIO1 → PB1
- VDD → 3.3V
- GND → GND

(Note: Update pin numbers according to your actual configuration.)

---

## Software Stack

- Embedded C
- STM32CubeIDE
- STM32 HAL drivers
- LoRaWAN stack (Semtech-based)
- I2C communication for ToF sensor
- SPI communication for LoRa module
- UART logging for debugging

---

## Firmware Architecture

### Main Processing Flow

1. Acquire 64-zone ranging data from VL53L5CX.
2. Divide zones into two 32-zone segments.
3. Apply threshold-based occupancy detection:
   - If distance > 0 and distance < OCCUPIED_THRESHOLD_MM → zone considered blocked.
4. If more than half of zones in a segment are blocked → slot marked OCCUPIED.
5. Calculate average distance per slot (for debugging/logging).
6. Pack results into 2-bit bitmap:
   - Bit 0 → Slot 1
   - Bit 1 → Slot 2
7. Transmit bitmap via LoRaWAN.
8. Log status using UART for validation.

### Design Considerations

- Majority voting improves robustness against noise.
- Compact bitmap reduces LoRa payload size.
- Modular firmware allows easy expansion to multiple slots.
- Error handling implemented for sensor and communication failures.

---

## Key Features

- Two-slot monitoring using a single ToF sensor
- Zone-based spatial segmentation
- Majority-voting occupancy detection
- Efficient bitmap data packing
- Long-range LoRaWAN communication
- TTN cloud integration
- Real-time TagoIO dashboard visualization

---

## Testing & Results

- Accurate vehicle detection using threshold-based zone analysis
- Reliable detection across repeated test cycles
- Stable LoRaWAN transmission during field testing
- Successful cloud forwarding and dashboard visualization

---

## Challenges & Debugging

- Sensor noise and environmental interference  
  → Implemented majority threshold logic to improve reliability.

- LoRaWAN join instability  
  → Corrected regional configuration parameters and join procedure.

- Communication timing issues  
  → Optimized scheduling and transmission intervals.

- Power stability concerns  
  → Improved supply filtering and decoupling.

---

## Future Improvements

- Multi-slot aggregation node
- Battery-powered optimization
- OTA firmware updates
- Adaptive threshold tuning
- Mobile app integration

---

## Learning Outcomes

- Real-time embedded firmware development
- Multi-zone ToF data processing
- Spatial segmentation techniques
- LoRaWAN stack configuration
- Cloud webhook integration
- IoT dashboard development
- System-level debugging and optimization

---

## Engineering Focus

This project demonstrates:

- Embedded system design
- Hardware-software integration
- Efficient sensor data processing
- Low-power wireless communication
- End-to-end IoT system implementation
