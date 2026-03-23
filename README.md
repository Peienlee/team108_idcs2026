# IDSC 2026: Hope for Glaucoma Detection using CNN (ResNet18 + Grad-CAM)

## Overview

Binary classification model (Healthy vs Glaucoma) using **ResNet18** trained on preprocessed retinal fundus photographs.  
The project emphasizes **high recall** — critical for screening to minimize missed glaucoma cases.
Grad-CAM visualizations show the model focuses primarily on the **optic disc**, **optic cup**, and **neuroretinal rim** — clinically relevant regions.

<br>

## Model Comparison

Three architectures were trained and evaluated on the same preprocessed dataset (Hillel Yaffe Glaucoma Dataset after quality filtering ≥ 0.6, CLAHE + 224×224 resize), using patient-level stratified split and data augmentation where applied.

Final test set results:

| Model                     | Threshold | Accuracy | Recall   | Precision | F1-score | AUC-ROC | Sensitivity | Specificity | Chosen? |
|---------------------------|-----------|----------|----------|-----------|----------|---------|-------------|-------------|---------|
| ResNet18 (selected + aug) | 0.6208    | 0.9583   | **0.9783** | 0.9837    | 0.9810   | **0.9797** | 0.9783      | 0.9231      | **Yes** |
| EfficientNet-B0           | 0.4000    | 0.9452   | 0.9216   | 1.0000    | 0.9592   | 0.9885  | 0.9216      | **1.0000**  | No      | 
| ResNet50                  | 0.5205    | 0.9452   | 0.9783   | 0.9574    | 0.9677   | 0.9753  | 0.9783      | 0.8846      | No      | 

**Key observations:**
- **ResNet18 (augmented)** achieves the best **recall (0.9783)** — most important metric for glaucoma screening (minimize false negatives / missed cases).
- All models show strong performance (AUC > 0.97), but ResNet18 offers the best overall trade-off.
- EfficientNet-B0 has perfect precision/specificity in this run but sacrifices recall — not ideal for screening.
- Final model selected: **ResNet18 (augmented version)** → deployed in Grad-CAM analysis and final evaluation.

<br>

## Project Structure

```bash
team108_idsc2026/
├── notebooks/
│   ├── 01_eda.ipynb
│   ├── 02_train_efficientnet_b0.ipynb
│   ├── 02_train_resnet18_selected.ipynb     # main / best version
│   ├── 02_train_resnet50.ipynb
│   ├── 03_test_evaluation_model.ipynb
│   └── 04_gradcam_auto_model.ipynb
├── reports/
│   └── figures/
│       ├── gradcam_outputs_auto/      # individual Grad-CAM results
│       ├── test/                      # test set plots
│       └── training/                  # training curves, losses, etc.
├── results/
│   └── test/                          # prediction CSVs
│       ├── test_predictions_resnet18.csv
│       └── (other model predictions…)
├── src/
├── .gitignore
├── LICENSE.txt
├── README.md
└── requirements.txt
```

<br>

## Dataset

This project uses the **Hillel Yaffe Glaucoma Dataset (HYGD): A Gold-Standard Annotated Fundus Dataset for Glaucoma Detection** (version 1.0.0).

- **Source**: PhysioNet  
- **Total images**: 747 fundus photographs (548 glaucomatous / 199 non-glaucomatous)  
- **Patients**: 288  
- **Image naming**: `patientID_imageNumber.jpg` (e.g. `188_1.jpg`)  
- **Labels file**: `Labels.csv` containing columns: `Image Name`, `Patient`, `Label` (GON+/GON-), `Quality Score` (FundusQ-Net, range 1–10)

<br>

### How to obtain the dataset

1. Go to: https://physionet.org/content/hillel-yaffe-glaucoma-dataset/1.0.0/
2. Download either:
   - ZIP file (~118.5 MB compressed, 125.8 MB uncompressed)  
   - or use one of the provided commands:
     ```bash
     # wget recursive
     wget -r -N -c -np https://physionet.org/files/hillel-yaffe-glaucoma-dataset/1.0.0/

After downloading, you should have at least:

```bash
hillel-yaffe-glaucoma-dataset/1.0.0/
├── Images/
│   ├── 1_0.jpg
│   ├── 1_1.jpg
│   └── ...
├── Labels.csv
├── LICENSE.txt
├── README.md
└── SHA256SUMS.txt
```

The expected directory structure is:

```bash
team108_idsc2026/
├── data/
│   ├── raw             # copy all images from "Images" folder in the dataset to here
│   ├── processed
│   └── Labels.csv
```

<br>

## How to Run

### 1. Clone Repository

```bash
git clone https://github.com/Peienlee/team108_idcs2026.git
cd <your-project-folder>
```

### 2. Install Dependencies

```bash
pip install -r requirements.txt
```

---

### 3. Run notebooks

1. notebooks/01_eda.ipynb
→ data cleaning, quality filtering, preprocessing pipeline
2. notebooks/02_train_resnet18_selected.ipynb
→ training, 5-fold CV, learning curves, performance stability plots
3. notebooks/03_test_evaluation_model.ipynb
→ final test evaluation, confusion matrix, ROC, risk grouping
4. notebooks/04_gradcam_auto_model.ipynb
→ Grad-CAM visualization & interpretability check

<br>

## 🖼️ Sample Output

* Very high & stable recall across folds → low miss rate
* 5-fold cross validation performance stability plot shows narrow distributions (reliable generalization)
* Confusion matrix & ROC curve → strong discrimination
* Grad-CAM outputs → clinically plausible attention (optic nerve head region)

Most result figures are saved in reports/figures/.

<br>

## Future / Possible Improvements

* Incorporate larger and more diverse datasets
* Improve clinical risk scoring system
* Deploy as a real-time screening tool

<br>

## Model File

* `models/augmented_glaucoma_resnet18.pth`
* Architecture: ResNet18
* Training: 5-Fold Cross Validation

<br>

## 👥 Authors

* Lee Pei En
* Lee Zhi Ying
* Sim Yu Yin
* Yah Tian Ling

<br>

## Citation

Abramovich O, Pizem H, Fhima J, Berkowitz E, Gofrit B, Meisel M, et al. (2025). Hillel Yaffe Glaucoma Dataset (HYGD) (version 1.0.0). PhysioNet. https://doi.org/10.13026/*****.
Abramovich O, Pizem H, Fhima J, Berkowitz E, Gofrit B, Meisel M, et al. GONet: A Generalizable Deep Learning Model for Glaucoma Detection [Internet]. arXiv.org. 2025.
Abramovich O, Pizem H, Eijgen JV, Oren I, Melamed J, Stalmans I, et al. FundusQ-Net: A regression quality assessment deep learning algorithm for fundus images quality grading. Comput Methods Programs Biomed. 2023;239:Art. no. 107522.