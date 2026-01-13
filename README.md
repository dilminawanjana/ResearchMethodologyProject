# Transformer-Based Segmentation with Tracking–based Refinement for Growth-Curve Analysis and Early Phenotypic Drug Susceptibility Detection in Time-Lapse Microscopy

A tracking-guided SegFormer pipeline for rapid, microscopy-based phenotypic drug susceptibility testing (pDST). This includes transformer-based binary segmentation of time-lapse microscopy (REF vs RIF), pseudo-tracking refinement to improve temporal consistency, and compute area curves, rolling exponential growth rates, and early-detection markers.

## What I implemented
- **SegFormer binary segmentation** trained end-to-end on microscopy frames (foreground/background).
- **Pseudo-tracking refinement** to improve temporal consistency of segmentation across frames.
- **Growth quantification**
  - Foreground **area curves** per well over time
  - **Rolling exponential growth rate** estimated from area
- **Early detection markers**
  - z-score, ratio, gap, and Welch t-test (p<0.05) 
- **Evaluation**
  - Acc / IoU / Dice for test dataset
  - Visualization of area and growth-rate curves

## Dataset / experiment setup
- Time-lapse phase-contrast microscopy from a microfluidic chip
- Two conditions:
  - **REF** (reference)
  - **RIF** (rifampicin treatment)
- Each condition: **10 wells**, **121 frames** (2 min interval)
- Split by well to avoid temporal leakage:
  - Train: 101–107
  - Val: 108
  - Test: 109–110 (and corresponding RIF wells)
- Separate test set for early detection of pDST

## Dataset setup (folder structure)

Find out the training dataset here. https://drive.google.com/drive/folders/135Ul9o_aQnDq61_OZHzYUO_2a4bDv3Fx?usp=drive_link

This project expects two main dataset folders: `REF/` and `RIF/`, each containing one folder per well (`Pos###`).

### Training dataset structure
For training/validation, each well folder contains three subfolders:
- `raw/`  : time-lapse phase-contrast frames
- `mask/` : ground-truth / reference masks used for supervised training
- `meta/` : Omnipose-derived area and growth signals (growth_areas.pickle, growth_rate.pickle)

```text
Data/
├── REF/
│   ├── Pos101/
│   │   ├── raw/
│   │   │   ├── img_000000000.tif
│   │   │   ├── img_000000001.tif
│   │   │   └── ...
│   │   ├── mask/
│   │   │   ├── MASK_img_000000000.tiff
│   │   │   ├── MASK_img_000000001.tiff
│   │   │   └── ...
│   │   └── meta/
│   │       ├── growth_areas.pickle
│   │       ├── growth_rate.pickle
│   │       ├── Growth rate.png
│   │       ├── Area.png
│   │       └── Cellpose_parameters_used.txt
│   ├── Pos102/
│   │   ├── raw/
│   │   ├── mask/
│   │   └── meta/
│   │       ├── growth_areas.pickle
│   │       ├── growth_rate.pickle
│   │       ├── Growth rate.png
│   │       ├── Area.png
│   │       └── Cellpose_parameters_used.txt
│   └── ...
└── RIF/
    ├── Pos201/
    │   ├── raw/
    │   ├── mask/
    │   └── meta/
    │       ├── growth_areas.pickle
    │       ├── growth_rate.pickle
    │       ├── Growth rate.png
    │       ├── Area.png
    │       └── Cellpose_parameters_used.txt
    ├── Pos202/
    │   ├── raw/
    │   ├── mask/
    │   └── meta/
    │       ├── growth_areas.pickle
    │       ├── growth_rate.pickle
    │       ├── Growth rate.png
    │       ├── Area.png
    │       └── Cellpose_parameters_used.txt
    └── ...
```

### Test dataset structure

Find out the test dataset here. 
https://drive.google.com/drive/folders/135Ul9o_aQnDq61_OZHzYUO_2a4bDv3Fx?usp=drive_link

For the independent test set, the same layout is used under a separate test root: test/Data with same folder structure.



## Methods summary
- **Segmentation model:** SegFormer (transformer-based) for binary semantic segmentation  
- **Refinement:** track-guided temporal consistency post-processing  
- **Growth rate:** rolling log-linear fit to area (exponential model)  
- **Detection:** sustained threshold crossing (k frames) after drug addition alignment

## How to run (Colab)
Recommended notebooks/scripts:
- `01_segmentation_train` – training + validation + refinement + visualizations
- `02_segmentation_infer_testdata` – inference on test set + refinement + visualizations


> Update paths in the notebook(s) to point to your Google Drive folders for REF/RIF data and output directory.

## Outputs
Saved into the configured output directory:
- Area curves and growth-rate curves per method
- Mean ± SD/SEM stacked plots (REF and RIF)
- Normalized plots (REF baseline at y=1)
- Early-detection reports + marker plots

## Key result 
Refinement improves frame-wise segmentation overlap (Acc/IoU/Dice), but does not always improve downstream growth dynamics or early-detection time, highlighting that pDST performance depends on temporally stable area measurements.






