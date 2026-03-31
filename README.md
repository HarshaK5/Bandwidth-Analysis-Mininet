# 📡 Bandwidth Measurement and Analysis using Mininet & POX

## 📌 Overview
This project measures and analyzes network bandwidth under different configurations using **Mininet** and the **POX SDN controller**. The goal is to understand how factors like congestion, delay, and bandwidth limits affect network performance.

---

## 🛠️ Technologies Used
- Mininet (Network Emulator)
- POX Controller (SDN Controller)
- iperf (Bandwidth Measurement Tool)
- Python

---

## 🧠 Objectives
- Measure bandwidth using `iperf`
- Compare different network topologies
- Analyze performance under:
  - Normal conditions
  - Congestion
  - Limited bandwidth
  - Network delay

---

## 🧱 Project Structure
bandwidth-analysis-mininet/
│── controller/
│ └── bandwidth_controller.py
│── results/
│ ├── pox_running.png
│ ├── ping_success.png
│ ├── baseline_bandwidth.png
│ ├── real_congestion.png
│ ├── limited_bw.png
│ └── delay_effect.png
│── README.md


---

# 🛠️ Step-by-Step Implementation Guide

Follow these technical procedures to replicate the bandwidth measurement and analysis environment.

---

### 1. Environment Initialization
Before launching the network, the **SDN Control Plane** must be active to manage flow entries.

* **Navigate to the POX Directory**:
    ```bash
    cd ~/pox
    ```
* **Launch the Controller**:
    Run the POX controller with the `l2_learning` module and the custom `bandwidth_controller` script.
    ```bash
    sudo python3 pox.py forwarding.l2_learning misc.bandwidth_controller
    ```

---

### 2. Network Topology Creation
With the controller listening, instantiate the virtual network using **Mininet**.

* **Standard Topology (Baseline)**:
    Create a single switch connected to two hosts.
    ```bash
    sudo mn --topo single,2 --controller=remote
    ```
* **Custom Parameter Topology**:
    To test constraints like bandwidth limits or latency, use the `tc` (Traffic Control) link:
    ```bash
    sudo mn --topo single,2 --link tc,bw=10,delay=50ms --controller=remote
    ```

---

### 3. Connectivity Verification
Ensure the OpenFlow rules are correctly installed by the POX controller.

* **Execute Global Ping**:
    Within the Mininet CLI, verify that all hosts can reach each other.
    ```bash
    mininet> pingall
    ```
    *Note: 0% packet loss indicates the SDN controller is successfully learning MAC addresses.*

---

### 4. Performance Benchmarking (iperf)
Use the `iperf` tool to generate TCP traffic and measure the throughput.

* **Step A: Start the Server**:
    Set host 1 (**h1**) as the listening server.
    ```bash
    mininet> h1 iperf -s &
    ```
* **Step B: Start the Client**:
    Connect host 2 (**h2**) to the server to begin the 10-second bandwidth test.
    ```bash
    mininet> h2 iperf -c h1
    ```

---

## 🛠️ Technologies Used
* **Mininet**: Network Emulator
* **POX Controller**: Python-based OpenFlow Controller
* **iperf**: Traffic generator and bandwidth measurement tool
* **Python**: Logic for custom forwarding and bandwidth control

---

## 📊 Results Summary
The following table outlines the performance metrics captured during the experimental phase:

| Scenario | Configuration | Average Bandwidth | Primary Observation |
| :--- | :--- | :--- | :--- |
| **Baseline** | `single,2` | **~43 Gbps** | Maximum theoretical throughput; no constraints. |
| **Congestion** | `single,4` (multi-flow) | **~29 Gbps** | Throughput per flow drops as active hosts compete. |
| **Limited BW** | `bw=10` | **~8–10 Mbps** | Strict hardware-level capping via `tc` (Traffic Control). |
| **Delay Impact** | `delay=50ms` | **~70 Mbps** | Significant performance degradation due to TCP ACK latency. |

---

## 🧠 Key Analysis & Findings
1.  **TCP Windowing & Latency**: We observed that while the link might support high bandwidth, a **50ms delay** drastically reduces effective throughput. This is because TCP waits for acknowledgments before sending more data; higher latency slows this feedback loop.
2.  **Resource Contention**: In the **Congestion** scenario, bandwidth is shared logically among active flows. The SDN controller (POX) manages these flows, but the physical link capacity remains the ultimate bottleneck.
3.  **Deterministic Capping**: By utilizing the `bw` parameter in Mininet, we successfully demonstrated that software-defined constraints can reliably mimic real-world ISP throttling or hardware limitations.
4.  **SDN Advantage**: Using the **POX controller** allows for real-time flow table updates, enabling us to see how L2 learning switches handle high-volume `iperf` traffic without dropping packets.

---

## 📸 Screenshot Manifest
Visual evidence of the experiments is stored in the `/results` directory:

* `pox_running.png`: Successful initialization of the POX L2 learning module.
* `ping_success.png`: Connectivity check via `pingall` (0% packet loss).
* `baseline_bandwidth.png`: `iperf` output showing ~43 Gbps peak performance.
* `real_congestion.png`: Multi-client `iperf` results showing shared bandwidth.
* `limited_bw.png`: Throughput graph capped at the 10Mbps threshold.
* `delay_effect.png`: Data showing the drop in Mbps as latency increases.

---

## 🚀 Quick Start
1.  **Launch Controller**: 
    `python3 pox.py forwarding.l2_learning misc.bandwidth_controller`
2.  **Launch Mininet**: 
    `sudo mn --topo single,2 --link tc,bw=10,delay=50ms --controller=remote`
3.  **Measure**: 
    `h1 iperf -s &` then `h2 iperf -c h1`

---

## 👩‍💻 Author
**Harsha** *Computer Science Student | Networking & SDN Enthusiast*



=======
# Bandwidth-Analysis-Mininet
Bandwidth measurement and analysis using Mininet and POX controller across different network topologies and conditions.
