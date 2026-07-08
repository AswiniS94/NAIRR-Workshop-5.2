# NAIRR-Workshop 5.2 - Case Study: Robotics and Cybersecurity

## Overview

In this hands-on session, we explore the critical intersection of Artificial Intelligence, Autonomous Driving (AD), and Cybersecurity. As modern vehicles increasingly rely on deep learning, sensors, and large language models to navigate the real world, they open themselves up to novel cyber threats. 


## Workshop Structure

This workshop is divided into three comprehensive case studies, guiding you from AI-driven scene understanding to detecting network-level and sensor-level attacks.


| # | Case Study | Topic | Estimated Time |
|:---:|:---|:---|:---:|
| **01** | [Vision Language Models](hands_on_5.2/01_VLM_in_Autonomous_Driving) | Efficient Vision Language Models for Autonomous Driving | 60–90 min |
| **02** | [Intrusion Detection Systems](hands_on_5.2/02_IDS_on_CAN_traffic.ipynb) | Intrusion Detection on CAN (Vehicle Network) Traffic | 60–90 min |
| **03** | [Sensor Spoofing](hands_on_5.2/03_Sensor_Spoofing.ipynb) | Sensor Fusion & Spoofing Attacks in Autonomous Vehicles | 45–60 min |

---
 
## 📓 Case Study Details
 
### 1. Efficient Vision Language Models for Autonomous Driving
**Focus:** Training, evaluating, and running inference with multi-camera vision-language models to explain autonomous driving scenes.

**Key Features:**
* Explores and processes multi-frame QA data from DriveLM and nuScenes datasets
* Implements **Gated Pooling Attention (GPA)** to fuse 6 frozen ViT camera embeddings
* Trains **DriveVLM-T5** to generate free-form driving explanations
* Evaluates trained models using COCO captioning metrics (BLEU, METEOR, ROUGE-L, CIDEr)
* Includes a complete 3-step pipeline (`01_train`, `02_evaluate`, `03_inference`) with visualizations

**Learning Outcomes:**
* Understand the architecture and data pipelines of Vision-Language Models (VLMs)
* Learn how to fuse multi-view camera data for comprehensive spatial understanding
* Gain hands-on experience training, evaluating, and prompting LLM-based systems for autonomous vehicles
 
---

### 2. Intrusion Detection on CAN (Vehicle Network) Traffic
**Focus:** Building a machine-learning-based Intrusion Detection System (IDS) for a vehicle's internal Controller Area Network (CAN) bus.

**Key Features:**
* Explores the **OTIDS / HCRL Car Hacking Dataset** (specifically DoS and Fuzzy attack subsets)
* Parses and cleans raw CAN frame schemas (CAN ID, DLC, DATA0–DATA7, hex payloads)
* Converts hexadecimal CAN IDs and payload bytes into numeric features for machine learning
* Trains and evaluates a **Random Forest classifier** for 3-class intrusion detection (Normal / DoS / Fuzzy)

**Learning Outcomes:**
* Understand the mechanics and vulnerabilities of in-vehicle CAN bus networks
* Learn cybersecurity-specific data preprocessing and feature engineering techniques
* Identify key indicators of Denial of Service (DoS) and Fuzzy attacks in raw network traffic
* Evaluate ML-based IDS models using classification reports, confusion matrices, and ROC AUC
 
---

### 3. Sensor Fusion & Spoofing Attacks in Autonomous Vehicles
**Focus:** Exploring early sensor fusion techniques and analyzing their vulnerability to adversarial sensor spoofing attacks.

**Key Features:**
* Utilizes the **Wall-Following Robot Navigation dataset** (combining camera and LiDAR data)
* Implements early (feature-level) sensor fusion using `StandardScaler` normalization and multi-sensor feature extraction
* Simulates an adversarial **noise-injection spoofing attack** on the camera sensor (modeling blinding/jamming threats)
* Compares Logistic Regression models trained on single-sensor vs. fused-sensor features
* Quantifies the inter-sensor correlation collapse caused by targeted spoofing

**Learning Outcomes:**
* Understand why autonomous vehicles rely on sensor fusion for environmental robustness
* Learn how physical adversarial attacks translate into injected data noise
* Analyze the resilience (and breaking points) of fused ML models under single-sensor attacks
* Evaluate model behavior and decision-making when input streams become corrupted
 
---

## Slides

[Lecture](slides/5.2_slides.pdf) - How Autonomous systems work, and why security matters in Autonomous systems

## Getting Started

### Prerequisites

```bash
git clone https://github.com/AswiniS94/NAIRR-Workshop-5.2.git
cd NAIRR-Workshop-5.2
conda env create -f env.yml
conda activate EM-VLM4AD
pip install gdown
```

### Running the Notebooks
1. **Sequential Execution (Case Study 01):** Run the VLM notebooks in order (01_train → 02_evaluate → 03_inference).
2. **Standalone Execution (Case Studies 02 & 03):** The CAN Bus IDS and Sensor Fusion notebooks are self-contained and can be run independently.
3. **Data Dependencies:** Case Study 01 requires downloading the nuScenes data and T5 model checkpoints via gdown (see the folder's internal README). Case Studies 02 and 03 load their datasets internally.

### Expected Results
* **Vision Language Modeling:** A fine-tuned T5 model capable of generating multi-view (6-camera) textual explanations of complex driving scenarios.
* **Network Intrusion Detection:** A cleaned dataset of CAN bus traffic and an ML model capable of distinguishing benign driving from cyber attacks.
* **Sensor Fusion Analysis:** Clear statistical visualizations demonstrating how early sensor fusion degrades under targeted noise injection attacks.
---
 
## 📊 Key Insights
### Why AI & Cybersecurity for Autonomous Vehicles?
* **Life-Critical Safety:** Unlike traditional IT networks, cyber attacks on moving vehicles directly threaten physical safety.
* **Complex Attack Surfaces:** Modern AVs possess dozens of networked Electronic Control Units (ECUs) and externally facing sensors (cameras, LiDAR, radar) that attackers can target.
* **Explainable AI (XAI):** End-users, safety engineers, and regulators require natural-language explanations (via VLMs) to trust and audit AI driving decisions.
* **Sub-Millisecond Response:** In-vehicle networks (CAN bus) require ultra-low latency intrusion detection to prevent physical actuation delays during an attack.
* **Multi-Modal Resilience:** Vehicles cannot rely on single sensors; they require intelligent fusion to survive blinding or jamming attacks.

### Workshop Model Performance Comparison (Expected)

| Case Study / Task | Model Architecture | Primary Input | Target Metric | Expected Outcome |
|-------------------|--------------------|---------------|---------------|------------------|
| **01: Scene QA** | DriveVLM-T5 (Frozen ViT + T5) | 6× Images + Text | BLEU / CIDEr | Generates coherent free-form scene descriptions |
| **02: CAN Bus IDS** | Random Forest | Hex/Numeric CAN Frames | Accuracy / AUC | >95% detection of DoS and Fuzzy attacks |
| **03: Sensor Spoofing** | Logistic Regression | Camera + LiDAR Features | Accuracy Drop | Demonstrates sensor fusion failure under targeted noise |

### Autonomous System Constraints
* **Compute Asymmetry:** VLMs require massive GPU compute hubs, whereas CAN IDS algorithms must be lightweight enough to run on resource-constrained ECUs.
* **Data Misalignment:** Variable-length real-world data (e.g., CAN messages with DLC < 8) frequently causes parsing errors that ML pipelines must programmatically handle.
* **Sensor Correlation Collapse:** When a physical sensor is spoofed, its statistical relationship with other sensors breaks down—a key vulnerability in early fusion architectures.

---
## 🔒 Security Applications
### Real-World Use Cases
* **In-Vehicle Threat Detection:** Deploying machine learning models directly on the CAN gateway to quarantine compromised ECUs before malicious braking or steering commands are executed.
* **Adversarial Robustness in Perception:** Designing advanced multi-sensor architectures capable of "voting out" a compromised sensor stream (e.g., trusting LiDAR when the Camera is blinded).
* **Automated Incident Forensics:** Using Vision Language Models to automatically narrate, log, and explain edge cases, accidents, or suspected cyber-physical attacks to a central fleet server.
* **Predictive Fleet Maintenance:** Aggregating anomalous CAN behavior across a fleet of autonomous vehicles to detect zero-day cyber campaigns or degrading hardware.

### Attack Detection & Simulation Capabilities
* **Denial of Service (DoS):** Detecting network flood attacks on the CAN bus intended to overwhelm the network and block critical messaging.
* **Fuzzy Attacks:** Identifying randomized, malformed CAN frames designed to cause unpredictable vehicle actuation and logic errors.
* **Sensor Spoofing:** Simulating noise injection, blinding, or jamming attacks on physical hardware (e.g., shining lasers into cameras or confusing LiDAR point clouds).
* **Semantic Misdirection:** Understanding how complex visual scenarios or adversarial visual prompts could confuse an AV's underlying scene understanding.

## 🔬 Advanced Extensions

**Future Workshop Enhancements**
*   **Adversarial Robustness:** Defending Vision-Language Models against adversarial patches and securing sensor fusion algorithms against advanced phantom-object injection.
*   **Explainable AI (XAI) for Safety:** Leveraging VLMs to generate transparent, natural-language logs explaining *why* an AV made a specific braking/steering decision or flagged an anomaly.
*   **Federated Learning for Fleets:** Distributed, privacy-preserving training of Intrusion Detection Systems (IDS) across fleets of connected vehicles without sharing raw CAN data.
*   **Hardware-in-the-Loop (HIL) Testing:** Deploying trained CAN IDS and lightweight VLMs onto actual automotive hardware (e.g., NVIDIA DRIVE platforms or NXP microcontrollers).
*   **Zero-Trust Automotive Architectures:** Moving beyond perimeter CAN defense to authenticate every ECU message dynamically.

**Integration Opportunities**
*   **ROS2 (Robot Operating System 2):** Deploying the sensor fusion and VLM inference pipelines as asynchronous ROS2 nodes for real-world robotic platforms.
*   **V2X Communication (5G):** Sharing real-time threat intelligence (e.g., newly detected sensor spoofing signatures) between vehicles and infrastructure with ultra-low latency.
*   **AUTOSAR Compliance:** Adapting the Python-based CAN intrusion detection models into C/C++ for automotive-grade microcontrollers.
*   **Fleet Monitoring Dashboards:** Integrating model outputs with Prometheus and Grafana to visualize fleet-wide sensor health and CAN bus anomalies in real-time.

---

## 📚 References

*   **EM-VLM4AD / DriveLM:** Gopalkrishnan, A., Greer, R., & Trivedi, M. (2024). *Multi-Frame, Lightweight & Efficient Vision-Language Models for Question Answering in Autonomous Driving.* [arXiv:2403.19838](https://arxiv.org/abs/2403.19838)
*   **nuScenes Dataset:** Caesar, H., et al. (2020). *nuScenes: A Multimodal Dataset for Autonomous Driving.* CVPR. [nuscenes.org](https://www.nuscenes.org/)
*   **Car Hacking Dataset (OTIDS):** Song, H. M., et al. (2020). *In-vehicle network intrusion detection using deep convolutional neural network.* Vehicular Communications.
*   **Sensor Spoofing & AV Security:** Petit, J., & Shladover, S. E. (2014). *Cybersecurity of Connected and Automated Vehicles: State of the Art.* Transportation Research Record.
*   **Hugging Face Transformers / T5:** [Official Documentation](https://huggingface.co/docs/transformers/model_doc/t5)

---

## 🎯 Learning Objectives Summary

By completing this workshop, participants will:

✅ **Understand** the architecture and deployment of multi-modal Vision-Language Models (VLMs) in autonomous driving.  
✅ **Process** and analyze complex, multi-sensor automotive datasets (nuScenes, DriveLM).  
✅ **Build** machine learning systems to detect network-level intrusions (DoS, Fuzzy) on real-world in-vehicle CAN buses.  
✅ **Analyze** the vulnerabilities of sensor fusion systems to physical adversarial attacks (e.g., camera/LiDAR spoofing).  
✅ **Identify** and resolve real-world data engineering challenges, such as misaligned network payloads and inter-sensor correlation collapse.  
✅ **Evaluate** the critical intersection of physical safety, AI decision-making, and cybersecurity in modern vehicles.  

**Perfect for:** AI researchers, automotive engineers, cybersecurity analysts, and robotics developers looking to build and secure next-generation autonomous systems.

