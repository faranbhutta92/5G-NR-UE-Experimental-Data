# Dataset Metadata — DATASHEET

This document provides detailed metadata for the 5G NR UE Experimental Current Consumption Dataset, following open data metadata best practices. It describes what the data includes, how it was collected, and how it is structured.

---

## 1. General Information

| Field | Value |
|-------|-------|
| **Dataset Title** | 5G NR UE Experimental Current Consumption Data |
| **Creator** | Muhammad Faran Bhutta |
| **Affiliation** | University of Oulu, Faculty of Information Technology and Electrical Engineering |
| **Supervisor** | Konstantin Mikhaylov |
| **Date of Collection** | April–May 2026 |
| **Date Published** | May 2026 |
| **Geographic Location** | Center for Wireless Communications (CWC), University of Oulu, Oulu, Finland |
| **Language** | English |
| **Keywords** | 5G, New Radio, User Equipment, Standalone, energy consumption, current consumption, RSRP, MCS, KPI, Bi-LSTM, machine learning |
| **Field of Science** | Electronics and Communications Engineering; Wireless Communications |
| **Related Publication** | M. F. Bhutta, "Experimental Characterization and ML-Based Prediction of 5G UE Energy Consumption Under Varying Channel Conditions," M.S. thesis, University of Oulu, May 2026. |

---

## 2. Data Collection Methodology

### 2.1 Purpose

The data was collected to characterize the current consumption of a 5G NR User Equipment (UE) under varying channel conditions and to train a machine learning model (Bi-LSTM) for predicting UE current consumption from network-measured KPIs.

### 2.2 Testbed Architecture

The experimental testbed consists of:

- **OAIBOX 40** (Allbesmart): A compact 5G NR Standalone (SA) network-in-a-box providing both gNB and 5G Core Network functions using the OpenAirInterface open-source stack. Maximum transmit power: 10 dBm.
- **Quectel RM520N-GL**: A 5G Sub-6 GHz M.2 module (3GPP Release 16, SA and NSA capable) mounted on the Quectel 5G-M2 Evaluation Board (EVB). Operating voltage range: 3.135–4.4 V.
- **Keysight N6705B DC Power Analyzer**: Provides DC power to the UE module and simultaneously logs instantaneous current consumption.
- **JFW 50BR-112 Step Attenuator**: Introduces controlled RF signal attenuation (0–110 dB in 1 dB steps, DC–2700 MHz) to emulate varying channel conditions.
- **Wired RF connection** (coaxial cables) throughout: no over-the-air transmission, ensuring channel repeatability and no external interference.

### 2.3 Network Configuration

| Parameter | Value |
|-----------|-------|
| Network Mode | 5G NR Standalone (SA) |
| Duplex Mode | FDD (Frequency Division Duplex) |
| Frequency Band | n1 |
| Channel Bandwidth | 20 MHz |
| SubCarrier Spacing | 15 kHz |
| MIMO Configuration | SISO (1×1) — single transmit, single receive antenna |
| Downlink Antenna Ports | Port 1 |
| Uplink Antenna Ports | Port 1 |
| Maximum MCS | 28 |

### 2.4 Current Consumption Measurement

| Parameter | Value |
|-----------|-------|
| Instrument | Keysight N6705B DC Power Analyzer, Output Channel 1 |
| Measured Quantity | Instantaneous DC current drawn by the Quectel RM520N-GL module |
| Unit | Milliamperes (mA) |
| Sampling Rate | **1000 samples per second** (1 ms per sample) |
| Sampling Mode | Continuous data logging |
| Supply Voltage | 3.8 V constant DC |
| Current Limit | 2.5 A |
| Measurement Accuracy | ±(0.04% of reading + 0.16 mA) |
| Power Delivery Point | External power connector J0105 on the 5G-M2 EVB, directly feeding the 5G module |
| Output Format | CSV (Comma-Separated Values) |

### 2.5 KPI Data Collection

| Parameter | Value |
|-----------|-------|
| Source | OAI dashboard (OAIBOX 40 network platform) |
| Sampling Rate | **1 sample per second** (1-second intervals) |
| Collection Trigger | Automatic logging upon UE registration on the network |
| Output Format | JSON (JavaScript Object Notation) |

### 2.6 Traffic Generation

| Parameter | Value |
|-----------|-------|
| Tool | iPerf3 |
| Direction | Downlink (server: OAIBOX, client: UE) |
| Transport Protocol | TCP |
| Data Volume per Iteration | 300 MB |
| Number of Iterations per Experiment | 5 |
| Inter-Iteration Idle Interval | Approximately 30 seconds |
| Use Case Emulated | eMBB (e.g., OTA firmware/application update of 50–300 MB) |

### 2.7 Automation

All experiments were orchestrated by a custom Python script running on the control computer, which managed:
- DC Power Analyzer power on/off cycling (via PyVISA over USB)
- Time-synchronized current logging initiation and termination
- iPerf3 throughput iteration scheduling and execution
- Coordinated start/stop of all measurement processes

---

## 3. Experiment Design

### 3.1 Overview

Twelve experiments were conducted at non-uniformly spaced attenuation levels. Larger increments (10 dB) were used where RSRP remained in the excellent range, and finer increments (2–5 dB) were used in the poor/cell-edge range where channel degradation has a more pronounced effect on UE energy consumption.

### 3.2 Experiment Parameters

| Exp. | Attenuation (dB) | RSRP (dBm) | Approx. DL Data Rate (Mbps) | Approx. Iteration Duration (s) | Approx. Total Duration (min) |
|:----:|:----------------:|:----------:|:---------------------------:|:------------------------------:|:---------------------------:|
| 1 | 0 | −63 | 101 | 25 | ~6.5 |
| 2 | 10 | −73 | 101 | 25 | ~6.5 |
| 3 | 20 | −83 | 101 | 25 | ~6.5 |
| 4 | 30 | −92 | 101 | 25 | ~6.5 |
| 5 | 37 | −100 | 81 | 31 | ~7 |
| 6 | 40 | −102 | 70 | 36 | ~8 |
| 7 | 45 | −108 | 49.5 | 51 | ~10 |
| 8 | 50 | −112 | 40 | 63 | ~12 |
| 9 | 52 | −114 | 28 | 90 | ~14 |
| 10 | 55 | −117 | 23.1 | 109 | ~16 |
| 11 | 57 | −120 | 11.6 | 217 | ~21 |
| 12 | 60 | −122 | 11.4 | 219 | ~22 |

---

## 4. Data Dictionary

### 4.1 CSV Files — Current Consumption

**File naming:** `exp <N> (<attenuation> db).csv`

Each CSV file contains a time series of instantaneous current measurements recorded at 1000 samples/second. The exact column structure follows the native export format of the Keysight N6705B data logger. The measured values represent the DC current in milliamperes (mA) drawn by the 5G module during the full experiment duration, including the power-up, network attachment, idle phases, active throughput phases, and power-down.

To convert current to power: **P (mW) = I (mA) × V (V)**, where V = 3.8 V (constant supply voltage).

### 4.2 JSON Files — UE KPIs

**File naming:** `exp <N>(<attenuation> dB).json`

Each JSON file contains a time series of network-measured UE KPIs recorded at 1-second intervals by the OAI dashboard. The following fields are included:

| Field | Description | Unit | Typical Range in This Dataset |
|-------|-------------|------|-------------------------------|
| RSRP | Reference Signal Received Power — received signal strength at the UE | dBm | −63 to −122 |
| RSSI | Received Signal Strength Indicator — total received power including noise | dBm | −76 to −86 |
| SINR | Signal-to-Interference-plus-Noise Ratio — signal quality metric | dB | 2.6 to 23 |
| DL MCS | Downlink Modulation and Coding Scheme index assigned by the gNB | integer (0–28) | 6 to 27 |
| UL MCS | Uplink Modulation and Coding Scheme index | integer (0–28) | varies |
| DL Bitrate | Downlink MAC-layer throughput | Mbps | 0 to 101 |
| UL Bitrate | Uplink MAC-layer throughput | Mbps | 0 to ~5 |
| DL BLER | Downlink Block Error Rate | fraction (0–1) | 0 to ~0.8 |
| UL BLER | Uplink Block Error Rate | fraction (0–1) | 0 to ~0.1 |
| PUSCH SNR | Physical Uplink Shared Channel Signal-to-Noise Ratio | dB | 16 to 25 |
| PUCCH SNR | Physical Uplink Control Channel Signal-to-Noise Ratio | dB | 18 to 25 |
| PHR | Power Headroom Report — margin between UE transmit power and maximum | dB | 10 to 55 |

### 4.3 UE KPIs Folder

The `UE KPIs/` folder contains additional UE-side KPI data files organized by experiment. These complement the JSON files in the root directory.

---

## 5. Data Alignment Notes

The CSV and JSON data sources have different sampling rates:
- **CSV (current):** 1000 samples/second
- **JSON (KPIs):** 1 sample/second

To align them for analysis or machine learning:
1. Divide the CSV current data into consecutive blocks of 1000 samples.
2. Compute the arithmetic mean of each block to produce one averaged current value per second.
3. Align the resulting 1-second current values with the corresponding JSON KPI records.

The first few seconds of each CSV file correspond to the UE power-up and network attachment phase, during which KPIs may not yet be available.

---

## 6. Key Results Summary

| Metric | 0 dB Attenuation (near-cell) | 60 dB Attenuation (cell edge) |
|--------|:----------------------------:|:-----------------------------:|
| RSRP | −63 dBm | −122 dBm |
| Avg. throughput-phase current | 206 mA | 242 mA |
| Peak current | 276 mA | 745 mA |
| Avg. DL data rate | 101 Mbps | 11.4 Mbps |
| Iteration duration (300 MB) | 25 s | 219 s |
| Avg. baseline (idle) current | 111 mA | 128 mA |

The total energy consumed per 300 MB transfer at the cell edge was approximately **10 times greater** than under favourable near-cell conditions.

---

## 7. Reuse and Licensing

This dataset is made available for academic and research purposes. If you use this data, please cite the thesis (see README.md for the full citation).

---

## 8. Contact

For questions about this dataset or the measurement methodology, please open an issue in this GitHub repository or contact the author at faranbhutta92@gmail.com.
