# Case Study 1: Efficient Multi-View Vision-Language Model for Autonomous Driving 

## 📖 Overview

*   **Core Theme:** Training, evaluating, and running inference with a multi-camera Vision-Language Model (VLM).
*   **The Goal:** Empower autonomous vehicles to answer natural-language questions about driving scenes, providing critical Explainable AI (XAI) for safety and auditing.
*   **The Tech Stack:** Fuses 6 frozen Vision Transformer (ViT) camera embeddings via **Gated Pooling Attention (GPA)** with a **T5** encoder-decoder.
*   **Datasets Used:** DriveLM and nuScenes multi-frame QA data.

 
---

## 📓 Notebooks & Workflow

 **Workflow Design:** This case study is divided into three sequential steps. 


| Step | Notebook | Purpose | Key Output |
|:---:|---|---|---|
| **1** | `01_train_VLM.ipynb` | Fine-tunes the multi-view VLM (frozen ViT + GPA + T5) on multi-frame QA data. | `multi_frame_results/<timestamp>/best_model.pth` |
| **2** | `02_evaluate_VLM.ipynb` | Loads a trained checkpoint, generates answers on a test subset, and scores them using COCO NLP metrics. | `predictions.json`, `metrics.csv` |
| **3** | `03_inference_VLM.ipynb` | Loads a trained checkpoint and runs inference on custom prompts alongside real-time camera views. | Inline 6-camera visual inference |

 
---

## 🧠 Model Architecture Summary
 
```
6 Camera Images  →  Frozen ViT (vit_b_32)  →  Gated Pooling Attention  →  fused image tokens
Question Text    →  T5 Embedding Layer  ──────────────────────────────────────┐
                                                                              ▼
                                                Concatenate (text tokens + image tokens)
                                                                              ▼
                                                    T5 Encoder-Decoder → Generated Answer
```
 
- **Vision encoder:** `torchvision.models.vit_b_32` (pretrained, frozen)
- **View fusion:** Gated Pooling Attention (GPA) - learns per-view weights across the 6 cameras instead of simple averaging
- **Language backbone:** `google-t5/t5-base` 
- **Modal embeddings:** Learned embeddings that explicitly teach the model to distinguish text tokens from image tokens once they are concatenated.

## 📥 Setup: Downloading Data & Pretrained Checkpoints
 
### 1. Download the NuScenes Dataset
* **Action:** Download and extract the ~3.5GB dataset directly into this folder:
```bash
cd 01_VLM_in_Autonomous_Driving
gdown 1isiXXTg46nl5SqMiEV4XjFD71KCCzezi -O data.zip
unzip data.zip
rm data.zip
```
* **Result:** This populates `data/nuscenes/samples/` and `data/multi_frame/` as shown in the folder structure below.
 
### 2. Download the Model Checkpoints (Choose one)
* **Action:** Depending on where you want to start, download the necessary model weights inside the `multi_frame_results/` directory:

* **Option A: Pretrained T5-Base Checkpoint**
    * *Why?* Use this if you want to start fine-tuning the model yourself in Notebook 01.

    ```bash
    cd multi_frame_results
    gdown --folder 1K61Ou-m5c5UmN2ggT-Huw3rv7PhW5Wft -O T5-Base/
    ```
    This pulls down `latest_model.pth` (~1.28GB), `metrics.csv`, and `predictions.json`.

* **Option B: Fully Trained DriveVLM Checkpoint**
    * *Why?* Use this if you want to skip training entirely and jump straight to Notebook 02 for evaluation or Notebook 03 for Inference.

    ```bash
    cd multi_frame_results
    gdown 17sZMUA67MH3p9TLYMyw7djYxdxLh88kd -O 20250911-191157.zip
    unzip 20250911-191157.zip
    rm 20250911-191157.zip
    ```

## Folder Structure
 
```
01_VLM_in_Autonomous_Driving/
├── README.md
├── 01_train_DriveLM.ipynb          # Step 1: Train DriveVLM-T5 on multi-frame QA data
├── 02_evaluate_DriveLM.ipynb       # Step 2: Score a trained checkpoint with COCO captioning metrics
├── 03_inference_DriveLM.ipynb      # Step 3: Run qualitative, inference + visualization
│
├── data/
│   ├── nuscenes/
│   │   └── samples/                # Raw nuScenes camera JPEGs, one folder per camera view
│   │       ├── CAM_FRONT/
│   │       ├── CAM_FRONT_LEFT/
│   │       ├── CAM_FRONT_RIGHT/
│   │       ├── CAM_BACK/
│   │       ├── CAM_BACK_LEFT/
│   │       └── CAM_BACK_RIGHT/
│   │
│   ├── multi_frame/                     # Pre-processed multi-view QA data 
│   │   ├── multi_frame_train.json       
│   │   ├── multi_frame_val.json         
│   │   ├── multi_frame_test.json        
│   │   ├── multi_frame_test_coco.json   # Ground-truth answers in COCO-captioning format 
│   │   ├── image_id.json                # Maps (image_path, question) → numeric COCO image_id
│   │   ├── prompts_for_drivelm.json     # Custom/ad-hoc prompts used by 03_inference
│   │   └── my_prompts.json              # Additional custom prompts for inference run
│   │
│   └── QA_dataset_nus/
│       └── v1_0_train_nus.json      # Original DriveLM v1.0 nuScenes QA annotations 
│
└── multi_frame_results/             # All training run outputs, one subfolder per experiment
    ├── T5-Base/                     # Baseline checkpoint folder (Option A)
    ├── 20250911-191157/             # Fully-trained checkpoint (Option B)
    └── <YOUR_TIMESTAMP>/            # New runs generated by Notebook 01
        ├── best_model.pth               # Best validation-loss checkpoint 
        ├── predictions.json             # Generated captions  
        └── metrics.csv                  # Scoring metrics
```
### ⚠️ Important Configuration Note
* **Dynamic Folders:** Every time you run 01_train_VLM.ipynb, it creates a new timestamped folder (YYYYMMDD-HHMMSS) in multi_frame_results/.
* **Updating Notebooks:** To evaluate or test your newly trained model, you must update the model_name field in the configuration block of Notebooks 02 and 03 to match your exact timestamp folder.

## 📚 Acknowledgments

* This case study utilizes data from the **nuScenes** dataset and **DriveLM**.
* The model architectures are adapted from **EM-VLM4AD**.