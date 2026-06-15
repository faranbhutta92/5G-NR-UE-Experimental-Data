[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.20695140.svg)](https://doi.org/10.5281/zenodo.20695140)
# 5G NR UE Experimental Current Consumption Data

## Overview

This repository contains the experimental dataset collected for the Master's thesis:

> **"Experimental Characterization and ML-Based Prediction of 5G UE Energy Consumption Under Varying Channel Conditions"**
>
> Muhammad Faran Bhutta, University of Oulu, Faculty of Information Technology and Electrical Engineering, Degree Programme in Electronics and Communications Engineering, May 2026.
>
> Supervisor: Konstantin Mikhaylov | Second Examiner: Mohammad Khalili | Technical Advisor: Muhammad Danyal Khattak
>
> **Thesis link:** [University of Oulu Library — (link to be added upon publication)](http://jultika.oulu.fi/)

The dataset consists of UE current consumption measurements and network-measured UE Key Performance Indicators (KPIs) collected across twelve experiments under varying channel conditions in a controlled 5G NR Standalone (SA) laboratory environment. The data is intended to support research on UE-level energy consumption characterization and prediction in 5G NR networks.

---

## Measurement Setup

### Equipment

| Component | Model / Version | Role |
|-----------|----------------|------|
| 5G gNB + Core | OAIBOX 40 (Allbesmart), OpenAirInterface stack | Provides 5G NR SA network (gNB + 5GC) |
| 5G UE Module | Quectel RM520N-GL (3GPP Release 16) on 5G-M2 EVB | User Equipment under test |
| DC Power Supply & Logger | Keysight N6705B DC Power Analyzer | Supplies 3.8 V DC to UE; logs current at 1000 samples/s |
| RF Attenuator | JFW 50BR-112 (0–110 dB, 1 dB steps, DC–2700 MHz) | Emulates varying channel conditions via controlled attenuation |
| Control Computer | — | Runs automated Python scripts for experiment orchestration |

### Network Configuration

| Parameter | Value |
|-----------|-------|
| Network Mode | 5G NR Standalone (SA) |
| Duplex Mode | FDD |
| Frequency Band | n1 |
| Channel Bandwidth | 20 MHz |
| SubCarrier Spacing (SCS) | 15 kHz |
| MIMO Configuration | SISO (1×1) |
| Max MCS | 28 |
| RF Connection | Wired (coaxial cables, no over-the-air) |

### Power Supply Configuration

| Parameter | Value |
|-----------|-------|
| Supply Voltage | 3.8 V (constant) |
| Current Limit | 2.5 A |
| Power Supply Output Channel | Channel 1 |
| Power Delivery Point | External power connector J0105 on 5G-M2 EVB (directly to the 5G module) |
| Current Measurement Accuracy | ±(0.04% of reading + 0.16 mA) |

---

## Experimental Design

A total of **12 experiments** were conducted, each at a different RF attenuation level to emulate channel conditions ranging from excellent near-cell coverage to severely degraded cell-edge scenarios.

### Experiment-to-Attenuation Mapping

| Experiment | Attenuation (dB) | RSRP (dBm) | Channel Condition |
|:----------:|:----------------:|:----------:|:-----------------:|
| 1 | 0 | −63 | Excellent |
| 2 | 10 | −73 | Excellent |
| 3 | 20 | −83 | Good |
| 4 | 30 | −92 | Fair |
| 5 | 37 | −100 | Fair |
| 6 | 40 | −102 | Fair |
| 7 | 45 | −108 | Poor |
| 8 | 50 | −112 | Poor |
| 9 | 52 | −114 | Poor |
| 10 | 55 | −117 | Poor |
| 11 | 57 | −120 | Poor |
| 12 | 60 | −122 | Poor (cell edge) |

### Procedure per Experiment

1. Attenuation is manually set on the JFW attenuator.
2. A Python script powers on the UE, starts current logging, and waits for network attachment.
3. Five consecutive downlink throughput iterations are executed using iPerf3, each transferring **300 MB over TCP**.
4. An idle interval of approximately **30 seconds** separates each throughput iteration.
5. After all iterations, current logging stops and the UE is powered off.
6. Current consumption data (CSV) and UE KPI data (JSON) are saved.

Total experiment duration ranges from approximately 6 minutes (0 dB attenuation) to 22 minutes (60 dB attenuation), depending on the achieved data rate.

---

## Dataset Description

### File Structure

```
5G-NR-UE-Experimental-Data/
├── README.md                        # This file
├── DATASHEET.md                     # Detailed metadata and data dictionary
├── LICENSE                          # License information
├── .zenodo.json                     # Zenodo metadata for DOI generation
│
├── exp 1 (0 db).csv                 # Current consumption — Experiment 1 (0 dB)
├── exp 1(0 dB).json                 # UE KPIs — Experiment 1 (0 dB)
├── exp 2 (10 db).csv                # Current consumption — Experiment 2 (10 dB)
├── exp 2(10 dB).json                # UE KPIs — Experiment 2 (10 dB)
├── ...                              # (experiments 3–11 follow the same pattern)
├── exp 12 (60 db).csv               # Current consumption — Experiment 12 (60 dB)
├── exp 12(60 dB).json               # UE KPIs — Experiment 12 (60 dB)
│
└── UE KPIs/                         # Additional UE KPI data folder
```

### CSV Files — UE Current Consumption

- **Source instrument:** Keysight N6705B DC Power Analyzer
- **Measured quantity:** Instantaneous current drawn by the Quectel RM520N-GL 5G module (mA)
- **Sampling rate:** 1000 samples per second (1 ms resolution)
- **Supply voltage:** 3.8 V constant DC
- **Measurement accuracy:** ±(0.04% of reading + 0.16 mA)
- **File naming convention:** `exp <number> (<attenuation> db).csv`

### JSON Files — Network-Measured UE KPIs

- **Source:** OAI dashboard (OAIBOX 40)
- **Sampling rate:** 1 sample per second (1-second intervals)
- **File naming convention:** `exp <number>(<attenuation> dB).json`
- **KPIs included:**

| KPI | Description | Unit |
|-----|-------------|------|
| RSRP | Reference Signal Received Power | dBm |
| RSSI | Received Signal Strength Indicator | dBm |
| SINR | Signal-to-Interference-plus-Noise Ratio | dB |
| DL MCS | Downlink Modulation and Coding Scheme | index (0–28) |
| UL MCS | Uplink Modulation and Coding Scheme | index (0–28) |
| DL Bitrate | Downlink data rate | Mbps |
| UL Bitrate | Uplink data rate | Mbps |
| DL BLER | Downlink Block Error Rate | % |
| UL BLER | Uplink Block Error Rate | % |
| PUSCH SNR | Physical Uplink Shared Channel SNR | dB |
| PUCCH SNR | Physical Uplink Control Channel SNR | dB |
| PHR | Power Headroom Report | dB |

---

## Usage Notes

- To align the CSV current data with the JSON KPI data for machine learning, the current consumption data should be **down-sampled from 1 ms to 1-second resolution** by averaging consecutive blocks of 1000 samples.
- The KPIs are recorded only while the UE is registered on the network and in the RRC Connected state; the first few seconds of each experiment correspond to the UE power-up, network search, and attachment procedure.
- For the machine learning analysis in the thesis, **Experiments 5 and 10** were used as the test set, and the remaining ten experiments were used for training.

---

## Citation

If you use this dataset in your research, please cite the thesis:

```
M. F. Bhutta, "Experimental Characterization and ML-Based Prediction of 5G UE Energy
Consumption Under Varying Channel Conditions," M.S. thesis, Faculty of Information
Technology and Electrical Engineering, University of Oulu, Oulu, Finland, May 2026.
```

---

## License

This dataset is made available for academic and research purposes. See the [LICENSE](LICENSE) file for details.

---

## Contact

For questions about this dataset, please open an issue in this repository or contact the author at faranbhutta92@gmail.com.
