# Blood Cell Detector — Notebooks & Streamlit App

This repository contains the codebase and notebooks used for a reproducible pipeline to **detect, count, segment, and classify blood cells** in peripheral blood microscopy images. It supports:
- **Detection & counting** of **RBC / WBC / Platelets** (YOLO, bounding boxes)
- **Semantic segmentation** of blood components (U-Net trained using **SAM 2.0–generated masks** as pixel-level ground truth)
- **WBC subtype classification** from cropped WBC regions (EfficientNet + a custom CNN)

> ⚠️ **Disclaimer**  
> This is an **academic/demo** project. It is **not** a medical device and must **not** be used for clinical decision-making.

---

## Live demo (Streamlit)
https://blood-cell-detector.streamlit.app

---

## Project summary (what is implemented)

### 1) Detection & counting (YOLO)
- Trained and evaluated multiple YOLO variants (YOLOv8n/m/l and YOLOv11n/m/l) on DS1.
- Tested the impact of input resolution (**imgsz=320 vs 640**) and performed **external validation** across datasets.
- Performed **automatic fine-tuning on DS2** via Ultralytics `tune` to mitigate domain shift.

**Key reported results (from the report):**
- DS1 (YOLOv11m, imgsz=640): **mAP@0.5:0.95 = 0.88393**
- DS2 external validation without adaptation: **mAP@0.5:0.95 = 0.622**
- DS2 after automatic fine-tuning: **mAP@0.5:0.95 = 0.820**

### 2) Segmentation (U-Net + SAM 2.0 masks)
- Datasets mainly provide **bounding boxes**, so SAM 2.0 was used to generate pixel-level masks from those boxes.
- U-Net was trained to segment **Background / RBC / WBC / Platelets** using SAM-generated masks as ground truth.

**Key reported results (from the report):**
- SAM mask generation agreement: **~86%**
- U-Net (IoU / Dice):
  - RBC: **IoU 0.9618** / **Dice 0.9765**
  - WBC: **IoU 0.9231** / **Dice 0.9316**
  - Platelets: **IoU 0.8264** / **Dice 0.8659**

> Note: segmentation metrics are reported against **SAM-generated masks** (not manually drawn masks).

### 3) WBC subtype classification
- EfficientNet with transfer learning and a custom CNN baseline were trained for WBC subtype classification (DS6).
- Includes data augmentation and class-weighting strategies due to class imbalance.

---

## Datasets used (as in the report)
This project uses multiple public datasets:

- **DS1 — TXL-PBC** (YOLO format, 3 classes: WBC/RBC/Platelet)  
- **DS2 — OI-PBC** (originally 12 classes → remapped to 3 classes for alignment with DS1/DS7)  
- **DS7 — BCCD** (Pascal VOC → converted to YOLO; label index issue was fixed after sanity checks)  
- **DS6 — Raabin-WBC** (WBC subtypes; masks converted to bounding boxes for some steps)

> Please download datasets from their official sources (papers/repositories) and follow the folder structure described below.

---

## Repository structure (typical)
> Your exact structure may differ slightly depending on how you organised the notebooks and outputs.

```
.
├─ app.py                  # Streamlit app entry point
├─ pages/                  # Streamlit multipage UI (optional)
├─ notebooks/              # Jupyter notebooks (EDA, training, evaluation)
├─ data/                   # Datasets (not committed)
│  ├─ DS1_TXL-PBC/
│  ├─ DS2_OI-PBC/
│  ├─ DS6_Raabin-WBC/
│  └─ DS7_BCCD/
├─ runs/                   # Training outputs / checkpoints (optional)
├─ images/                 # Static assets (optional)
├─ requirements.txt        # Python dependencies
├─ packages.txt            # System packages for deployment (optional)
└─ README.md
```

---

## Quickstart

### 1) Create an environment & install dependencies
```bash
python -m venv .venv
# Windows
.venv\Scripts\activate
# macOS/Linux
source .venv/bin/activate

pip install --upgrade pip
pip install -r requirements.txt
```

### 2) Run the Streamlit app locally
```bash
streamlit run app.py
```

---

## Reproducibility workflow (recommended order)

1. **Dataset preparation & EDA**
   - Verify splits, class mapping (especially DS2 12→3), and label correctness (DS7 RBC/WBC swap check).
2. **YOLO training on DS1**
   - Baseline replication, then multi-model comparison (v8/v11; n/m/l).
3. **Resolution experiments**
   - Train/evaluate imgsz=320 vs 640 and compare class-wise behaviour (RBC overlap vs platelets).
4. **External validation**
   - Evaluate DS1-trained model on DS7 and DS2 to quantify domain shift.
5. **Fine-tuning on DS2**
   - Run Ultralytics `tune` and select the best trial based on validation evidence.
6. **SAM mask generation + U-Net training**
   - Generate masks from bounding boxes, train U-Net, evaluate IoU/Dice.
7. **WBC subtype classifiers**
   - Train EfficientNet and custom CNN; inspect confusion matrices and class imbalance effects.

---

## Notes on thresholds & evaluation
- YOLO evaluation artifacts typically include **F1–confidence curves**, **confusion matrices**, and **mAP** metrics.
- The confidence threshold that maximises F1 may differ from the default threshold used to plot confusion matrices.
- In dense regions, **missing annotations** can make visually correct detections appear as false positives.

---

## Deployment (Streamlit Community Cloud)
1. Push the repository to GitHub.
2. In Streamlit Cloud:
   - Select your repository and branch
   - Set the entry file to `app.py`

If you use OpenCV or other system-level dependencies, keep `packages.txt` in the repository root.

---

## Contributors
- Diogo Casquinha  
- Vicente Soares  
- Guilherme Pereira  
- Gabriel Afonso  

Supervisor: Simão Gonçalves  
Institution: NOVA Executive Education & Samsung

---

## License
Add a license file (`LICENSE`) appropriate for your course/project requirements.
