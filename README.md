# NAIRR-Workshop 5.2
# Lecture 09 - Case Study: Robotics and Cybersecurity

**NAIRR Workshop Series 2026** — Oakland University & Worcester Polytechnic Institute

Welcome to **Module 5.2** of the NAIRR Workshop! In this hands-on session, we explore the critical intersection of Artificial Intelligence, Autonomous Driving (AD), and Cybersecurity. As modern vehicles increasingly rely on deep learning, sensors, and large language models to navigate the real world, they open themselves up to novel cyber threats. 

This repository contains all the code, notebooks, and instructions needed to run our three hands-on case studies on your **Jetstream2** environments.

## Workshop Structure

This workshop is divided into three comprehensive case studies.

| # | Notebook | Topic |
|---|----------|-------|
| 01 | [Vision Language Models](hands_on_5.2/Case_Study_03_Autonomous_Driving) | Efficient Vision Language Models for Autonomous Driving |
| 02 | [Intrusion Detection Systems](hands_on_5.2/5_2_Case_Study_Cybersecurity_in_Autonomous_Driving_1.ipynb) | Intrusion Detection on CAN (Vehicle Network) Traffic |
| 03 | [Sensor Spoofing](hands_on_5.2/5_2_Case_Study_Cybersecurity_in_Autonomous_Driving_3.ipynb) | Sensor Fusion & Spoofing Attacks in Autonomous Vehicles |

---
 
## 📓 Case Study Details
 
### 01 — Efficient Vision Language Models for Autonomous Driving
**Folder:** `hands_on_5.2/Case_Study_03_Autonomous_Driving/`
 
Trains, evaluates, and runs inference with **DriveVLM-T5**, a multi-camera vision-language model that answers natural-language questions about driving scenes (DriveLM / nuScenes data). Fuses 6 frozen ViT camera embeddings via **Gated Pooling Attention (GPA)** with a **T5** encoder-decoder (optionally LoRA-adapted) to generate free-form driving explanations.
 
| Notebook | Purpose |
|---|---|
| `01_train_DriveLM.ipynb` | Fine-tunes DriveVLM-T5 on a workshop-sized subset of multi-frame QA data |
| `02_evaluate_DriveLM.ipynb` | Scores a trained checkpoint with COCO captioning metrics (BLEU, METEOR, ROUGE-L, CIDEr) |
| `03_inference_DriveLM.ipynb` | Runs qualitative, single-example inference with 6-camera visualization |
 
See `hands_on_5.2/Case_Study_03_Autonomous_Driving/README.md` for full folder structure, dataset/checkpoint download steps, and a detailed quick-start guide.
 
**Estimated time:** 60–90 minutes
 
---

### 02 — Intrusion Detection on CAN (Vehicle Network) Traffic
**Notebook:** `hands_on_5.2/5_2_Case_Study_Cybersecurity_in_Autonomous_Driving_1.ipynb`
 
Builds a machine-learning-based **Intrusion Detection System (IDS)** for the **Controller Area Network (CAN) bus** — the internal messaging backbone connecting a vehicle's Electronic Control Units (ECUs). Using the **OTIDS / HCRL Car Hacking Dataset** (DoS and Fuzzy attack subsets), the notebook walks through:
 
- Loading and understanding raw CAN frame schemas (CAN ID, DLC, DATA0–DATA7, hex-encoded payloads)
- Detecting and fixing a real-world data misalignment bug caused by variable-length CAN messages (`DLC < 8`)
- Feature engineering — converting hexadecimal CAN IDs and data bytes into numeric features
- Checking multicollinearity between DATA fields
- Training a **Random Forest classifier** for 3-class intrusion detection (Normal / DoS / Fuzzy)
- Evaluating with classification reports, confusion matrices, and ROC AUC
- Interpreting results and discussing generalization to unseen attack types (e.g., spoofing)
 
**Estimated time:** 60–90 minutes
 
---

### 03 — Sensor Fusion & Spoofing Attacks in Autonomous Vehicles
**Notebook:** `hands_on_5.2/5_2_Case_Study_Cybersecurity_in_Autonomous_Driving_3.ipynb`
 
Explores how autonomous vehicles fuse multiple sensor modalities (camera, LiDAR, etc.) for robustness, and how that robustness breaks down under adversarial **sensor spoofing** attacks. Using the **Wall-Following Robot Navigation dataset** (OpenML ID 1497, SCITOS G5 robot), the notebook covers:
 
- Why AV sensor suites combine complementary sensors, and how **early (feature-level) fusion** works
- Preprocessing: stratified train/test split, `StandardScaler` normalization, and multi-sensor feature extraction
- Simulating a **noise-injection spoofing attack** on the camera sensor (a Gaussian-noise attack model representing blinding/jamming-style threats)
- Training separate Logistic Regression classifiers on camera-only, LiDAR-only, and fused features
- Quantifying the **inter-sensor correlation collapse** caused by spoofing
- Evaluating clean vs. spoofed accuracy across all sensor configurations, and interpreting fusion's resilience (or lack thereof) to single-sensor attacks
 
**Estimated time:** 45–60 minutes
 
---

## Slides
-- [Lecture](slides/JHUB NAIRR Instructions OU 5.2.pptx) — Getting on to 5.2 jupyter hub instance
- [Lecture](slides/5.2_slides.pdf) — How Autonomous systems work, and why security matters in Autonomous systems

## Getting Started

```bash
git clone https://github.com/AswiniS94/NAIRR-Workshop-5.2.git
cd NAIRR-Workshop-5.2
conda env create -f env.yml
conda activate EM-VLM4AD
```

Once the environment is active, navigate into whichever case study you'd like to run:
 
```bash
cd hands_on_5.2/Case_Study_03_Autonomous_Driving   # Case Study 01 (VLM)
# or open the .ipynb files directly for Case Studies 02 and 03
```
 
> 📌 For Case Study 01, additional dataset/checkpoint download steps (via `gdown`) are required before running the notebooks — see that folder's own `README.md` for details.
 
---
 
## 📝 Notes
 
- All three case studies are self-contained Jupyter notebooks designed to run end-to-end within a single workshop session.
- Each notebook follows the same structure: **Learning Objectives → Theoretical Background → Hands-On (step-by-step) → Interpreting Results → Discussion Questions**.
- Recommended order: **01 (Vision Language Models) → 02 (Intrusion Detection) → 03 (Sensor Spoofing)** — this progresses from AI-driven scene understanding, to detecting network-level attacks, to detecting sensor-level attacks, giving a full picture of the AV security stack.

## 📚 References
 
Case Study 01 is built on top of **EM-VLM4AD**, the efficient multi-frame vision-language model architecture (frozen ViT + Gated Pooling Attention + T5) used throughout `01_train_DriveLM.ipynb`, `02_evaluate_DriveLM.ipynb`, and `03_inference_DriveLM.ipynb`:
 
> Gopalkrishnan, A., Greer, R., & Trivedi, M. (2024). *Multi-Frame, Lightweight & Efficient Vision-Language Models for Question Answering in Autonomous Driving*. arXiv preprint arXiv:2403.19838. https://arxiv.org/abs/2403.19838
 
Original codebase: [github.com/akshaygopalkr/EM-VLM4AD](https://github.com/akshaygopalkr/EM-VLM4AD)





