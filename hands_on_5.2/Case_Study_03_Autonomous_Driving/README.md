# Case Study 3: Hands-On: Multi-View Vision-Language Model for Driving Scene Explanation 🧠👁️

This project trains, evaluates, and runs inference with **DriveVLM-T5**, a multi-camera vision-language model that answers natural-language questions about driving scenes (DriveLM / nuScenes data). It fuses 6 frozen ViT camera embeddings via **Gated Pooling Attention (GPA)** with a **T5** encoder-decoder to generate free-form driving explanations.
 
## 📓 Notebooks
 
| Notebook | Purpose | Key Output |
|---|---|---|
| **`01_train_DriveLM.ipynb`** | Fine-tunes DriveVLM-T5 (frozen ViT + GPA + T5) on the multi-frame QA data | `multi_frame_results/<timestamp>/best_model.pth`, `latest_model.pth` |
| **`02_evaluate_DriveLM.ipynb`** | Loads a trained checkpoint, generates answers on a test subset, and scores them with `pycocoevalcap` (BLEU, METEOR, ROUGE-L, CIDEr) | `predictions.json`, `metrics.csv` |
| **`03_inference_DriveLM.ipynb`** | Loads a trained checkpoint and runs inference on custom prompts, plotting all 6 camera views alongside the generated answer for qualitative inspection | `predictions-test.json`, inline visualizations |
 
**Recommended run order:** `01_train_DriveLM.ipynb` → `02_evaluate_DriveLM.ipynb` → `03_inference_DriveLM.ipynb`
 
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
- **View fusion:** Gated Pooling Attention (GPA) — learns per-view weights across the 6 cameras instead of simple averaging
- **Language backbone:** `google-t5/t5-base` 
- **Modal embeddings:** learned embeddings distinguish text tokens from image tokens once concatenated

## 📥 Setup: Downloading Data & Pretrained Checkpoints
 
Run these from the `Case_Study_03_Autonomous_Driving` folder **before** opening any notebook. Both downloads use [`gdown`](https://github.com/wkentaro/gdown) 
```bash
pip install gdown
``` 
 
### Download the NuScenes Dataset
Download and extract the ~3.5GB dataset directly into this folder:
```bash
cd Case_Study_03_Autonomous_Driving
gdown 1isiXXTg46nl5SqMiEV4XjFD71KCCzezi -O data.zip
unzip data.zip
rm data.zip
```
This populates `data/nuscenes/samples/` and `data/multi_frame/` as shown in the folder structure below.
 
### Download the Model Checkpoints (Choose one)
Depending on where you want to start, download the necessary model weights inside the `multi_frame_results/` directory:

## Option A: Pretrained T5-Base Checkpoint (To start Fine-Tuning in Notebook 01)

```bash
cd multi_frame_results
gdown --folder 1K61Ou-m5c5UmN2ggT-Huw3rv7PhW5Wft -O T5-Base/
```
This pulls down `latest_model.pth` (~1.28GB), `metrics.csv`, and `predictions.json`.

## Option B: Fully Trained DriveVLM Checkpoint (To skip directly to Inference in Notebook 03)

```bash
cd multi_frame_results
gdown 17sZMUA67MH3p9TLYMyw7djYxdxLh88kd -O 20250911-191157.zip
unzip 20250911-191157.zip
rm 20250911-191157.zip
```

## Folder Structure
 
```
Case_Study_03_Autonomous_Driving/
├── README.md
├── 01_train_DriveLM.ipynb          # Step 1: Train DriveVLM-T5 on multi-frame QA data
├── 02_evaluate_DriveLM.ipynb       # Step 2: Score a trained checkpoint with COCO captioning metrics
├── 03_inference_DriveLM.ipynb      # Step 3: Run qualitative, single-example inference + visualization
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
│   ├── multi_frame/                 # Pre-processed multi-view QA data (used by all 3 notebooks)
│   │   ├── multi_frame_train.json       # Training QA pairs + image paths (~341K examples)
│   │   ├── multi_frame_val.json         # Validation QA pairs + image paths
│   │   ├── multi_frame_test.json        # Held-out test QA pairs + image paths
│   │   ├── multi_frame_test_coco.json   # Ground-truth answers in COCO-captioning format (for 02_evaluate)
│   │   ├── image_id.json                # Maps (image_path, question) → numeric COCO image_id
│   │   ├── prompts_for_drivelm.json     # Custom/ad-hoc prompts used by 03_inference
│   │   └── my_prompts.json              # Additional custom prompts (draft/reference inference run)
│   │
│   └── QA_dataset_nus/
│       └── v1_0_train_nus.json      # Original DriveLM v1.0 nuScenes QA annotations (source data)
│
└── multi_frame_results/             # All training run outputs, one subfolder per experiment
    ├── T5-Base/                     # Named experiment folder (e.g. baseline T5-Base run)
    ├── 20250911-191157/             # Timestamped run — checkpoint used by 03_inference_DriveLM.ipynb
    └── 20260703-164941/             # Timestamped run — checkpoint used by 02_evaluate_DriveLM.ipynb
        ├── best_model.pth               # Best validation-loss checkpoint (saved during training)
        ├── latest_model.pth             # Final-epoch checkpoint
        ├── predictions.json             # Generated captions from 02_evaluate_DriveLM.ipynb
        ├── predictions-test.json        # Generated captions from 03_inference_DriveLM.ipynb
        └── metrics.csv                  # BLEU / METEOR / ROUGE-L / CIDEr scores from 02_evaluate_DriveLM.ipynb
```
> **Note:** Each training run creates its own timestamped folder (`YYYYMMDD-HHMMSS`) under `multi_frame_results/`. The `model_name` field in the `config` of `02_evaluate_DriveLM.ipynb` and `03_inference_DriveLM.ipynb` must be updated to point at whichever run's folder you want to load.