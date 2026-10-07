# THERMAL-SUPER-RESOLUTION-FUSION

# 🎯 Problem Statement

## Optical-Guided Super-Resolution for Thermal IR Imagery

Thermal infrared remote sensing plays a vital role in urban heat island analysis, wildfire detection, and agricultural stress monitoring.

However, TIR sensors typically operate at lower spatial resolutions, limiting their ability to capture fine-scale variations in surface temperature.

Optical sensors provide much richer spatial information such as:

* Edges
* Textures
* Buildings
* Roads
* Vegetation boundaries
* Land-cover boundaries

but they do not directly provide thermal information.

Therefore, this project investigates:

> **How can high-resolution spatial information from optical imagery be used to enhance low-resolution thermal imagery while preserving physically meaningful thermal information?**

---

# 💡 Core Idea

The project combines two complementary sources of information:

### 🔥 Thermal Data

Provides:

* Temperature information
* Heat distribution
* Thermal patterns
* Surface thermal characteristics

### 🌈 Optical Data

Provides:

* Spatial details
* Edges
* Structures
* Textures
* Land-cover boundaries

The proposed system learns to combine these two modalities.

```text
             High-Resolution Optical
                     │
                     ▼
              Optical Encoder
                     │
                     │
                     ▼
              Feature Fusion
                     ▲
                     │
              Thermal Encoder
                     ▲
                     │
              Low-Resolution
              Thermal Image
                     │
                     ▼
              Thermal Decoder
                     │
                     ▼
        Super-Resolved Thermal Image
```

---

# 🛰️ Dataset

The project is based on **Landsat-8 imagery**.

Landsat-8 contains:

* **9 OLI optical bands**
* **2 TIRS thermal infrared bands**

for a total of:

> **11 spectral bands**

---

# 🌈 Landsat-8 Optical Bands

| Band   | Description     | Wavelength   | Resolution |
| ------ | --------------- | ------------ | ---------- |
| Band 1 | Coastal Aerosol | 0.43–0.45 µm | 30 m       |
| Band 2 | Blue            | 0.45–0.51 µm | 30 m       |
| Band 3 | Green           | 0.53–0.59 µm | 30 m       |
| Band 4 | Red             | 0.64–0.67 µm | 30 m       |
| Band 5 | Near Infrared   | 0.85–0.88 µm | 30 m       |
| Band 6 | SWIR 1          | 1.57–1.65 µm | 30 m       |
| Band 7 | SWIR 2          | 2.11–2.29 µm | 30 m       |
| Band 8 | Panchromatic    | 0.50–0.68 µm | 15 m       |
| Band 9 | Cirrus          | 1.36–1.38 µm | 30 m       |

---

# 🔥 Landsat-8 Thermal Bands

| Band    | Description        | Wavelength     | Native Resolution |
| ------- | ------------------ | -------------- | ----------------- |
| Band 10 | Thermal Infrared 1 | 10.60–11.19 µm | ~100 m            |
| Band 11 | Thermal Infrared 2 | 11.50–12.51 µm | ~100 m            |

### Primary thermal targets

The model focuses specifically on:

```text
Band 10
Band 11
```

These are the thermal infrared bands that the project aims to super-resolve.

---

# 🎯 Project Input and Output

The system can operate in two main configurations.

## Configuration 1 — RGB Guided

Input:

```text
RGB Optical
   +
Thermal Band 10 / Band 11
```

RGB consists of:

```text
Band 2 → Blue
Band 3 → Green
Band 4 → Red
```

The RGB image provides spatial guidance while the thermal band provides thermal information.

---

## Configuration 2 — Multispectral Guided

Instead of only RGB, multiple optical bands can be provided:

```text
Optical:
Band 1
Band 2
Band 3
Band 4
Band 5
Band 6
Band 7

        +

Thermal:
Band 10 / Band 11
```

This provides the model with richer information about:

* Vegetation
* Water
* Buildings
* Soil
* Roads
* Land-cover characteristics

---

# 🔢 Super-Resolution Scale

The project supports two target scales:

## 2× Super-Resolution

```text
100 m Thermal
      ↓
    2× SR
      ↓
50 m Thermal
```

## 4× Super-Resolution

```text
100 m Thermal
      ↓
    4× SR
      ↓
25 m Thermal
```

The model is therefore designed to investigate both:

```text
2× Super-Resolution
4× Super-Resolution
```

---

# 🧠 Proposed Model

The proposed architecture is called:

# OGTSRNet

### Optical-Guided Thermal Super-Resolution Network

The model follows a two-stream architecture.

```text
                     INPUT
                       │
          ┌────────────┴────────────┐
          │                         │
          ▼                         ▼
   Optical Image              Thermal Image
   HR Optical                  LR Thermal
          │                         │
          ▼                         ▼
 Optical Encoder              Thermal Encoder
          │                         │
          │                         │
          └──────────┬──────────────┘
                     ▼
             Feature Fusion
                     │
             Cross-Attention
                     │
                     ▼
              Fusion Features
                     │
                     ▼
             Thermal Decoder
                     │
               Upsampling
                     │
                     ▼
           SR Thermal Output
                     │
                     ▼
          High-Resolution Thermal
```

---

# 🏗️ Model Components

## 1. Optical Encoder

The optical encoder extracts spatial information from the optical image.

Important features include:

* Edges
* Boundaries
* Textures
* Structures
* Land-cover patterns

Example:

```text
Optical Image
     ↓
Convolution
     ↓
Residual Blocks
     ↓
Multi-scale Optical Features
```

---

## 2. Thermal Encoder

The thermal encoder processes the low-resolution thermal image.

Its primary responsibility is to preserve:

* Temperature information
* Thermal gradients
* Heat distribution
* Thermal structures

```text
Thermal LR
    ↓
Convolution
    ↓
Residual Blocks
    ↓
Thermal Features
```

---

## 3. Feature Fusion

The optical and thermal features are combined.

The important principle is:

> Optical imagery should guide the spatial reconstruction, but should not blindly overwrite thermal information.

Therefore, the fusion mechanism should selectively transfer useful spatial information.

---

## 4. Cross-Attention Fusion

A cross-attention mechanism can be used to allow thermal features to selectively attend to useful optical features.

Conceptually:

```text
Thermal Features
      │
      ▼
    Query
      │
      │
      ├───────────────┐
      │               │
      ▼               ▼
 Optical Features   Attention
   Key/Value          │
      │               │
      └───────┬───────┘
              ▼
       Fused Features
```

This helps reduce unnecessary optical texture transfer.

---

# 🚀 5. Thermal Decoder

The decoder reconstructs the high-resolution thermal image.

It performs progressive upsampling:

```text
Fused Features
      ↓
Upsampling ×2
      ↓
Feature Refinement
      ↓
Upsampling ×2
      ↓
Thermal Reconstruction
      ↓
SR Thermal
```

For 2×:

```text
100 m → 50 m
```

For 4×:

```text
100 m → 50 m → 25 m
```

---

# ⚙️ Data Processing Pipeline

Before training the neural network, the satellite data must be prepared carefully.

The complete pipeline is:

```text
Landsat-8 Raw Data
       │
       ▼
Band Extraction
       │
       ▼
Radiometric Processing
       │
       ▼
Thermal Conversion
       │
       ▼
Optical-Thermal Alignment
       │
       ▼
Resampling / Registration
       │
       ▼
Normalization
       │
       ▼
Patch Extraction
       │
       ▼
Training Dataset
```

---

# 🛰️ 1. Raw Landsat Data

The raw scene contains:

```text
B1
B2
B3
B4
B5
B6
B7
B8
B9
B10
B11
```

The project primarily uses:

```text
Optical → B2, B3, B4
or
Optical → B1–B7

Thermal → B10, B11
```

---

# 🌡️ 2. Thermal Processing

Thermal bands contain sensor measurements that need to be converted appropriately before being treated as physical temperature.

The general processing pipeline is:

```text
Raw Thermal DN
      ↓
Radiance
      ↓
Brightness Temperature
      ↓
Kelvin
```

The thermal values should be handled carefully so that the model does not simply generate visually attractive images while losing thermal meaning.

---

# 📐 3. Optical-Thermal Alignment

This is one of the most important parts of the project.

Optical and thermal images do not initially have identical spatial grids.

Therefore:

```text
Optical Image
      +
Thermal Image
      ↓
Geometric Alignment
      ↓
Co-registered Dataset
```

Possible techniques include:

* Reprojection
* Resampling
* Feature matching
* ORB
* SIFT
* RANSAC
* Mutual-information-based registration
* Spatial Transformer Networks for residual alignment

The objective is:

> The same geographical location should correspond to the correct location in both modalities.

---

# 🧩 4. Patch Extraction

Large satellite images are divided into smaller patches.

Example:

```text
Large Satellite Scene
          ↓
 ┌────┬────┬────┬────┐
 │ P1 │ P2 │ P3 │ P4 │
 ├────┼────┼────┼────┤
 │ P5 │ P6 │ P7 │ P8 │
 └────┴────┴────┴────┘
```

Each training sample contains:

```text
Optical HR Patch
        +
Thermal LR Patch
        +
Thermal HR Reference
```

---

# 🔬 Training Strategy

A major challenge is obtaining true high-resolution thermal ground truth.

The training strategy therefore uses controlled degradation / synthetic downsampling where appropriate.

Conceptually:

```text
Higher-resolution thermal reference
                │
                ▼
          Downsampling
                │
                ▼
        Synthetic LR Thermal
                │
                │
                ▼
        OGTSRNet + Optical
                │
                ▼
         Reconstructed HR
                │
                ▼
      Compare with Reference
```

This allows supervised training when a suitable higher-resolution thermal reference is available.

The experimental setup must clearly distinguish:

* Native Landsat thermal resolution
* Resampled products
* Synthetic training degradation
* Actual independent evaluation data

---

# 📚 Dataset Structure

The project uses a structured dataset organization.

```text
data/
│
├── raw/
│   ├── scene_001/
│   │   ├── B1.TIF
│   │   ├── B2.TIF
│   │   ├── B3.TIF
│   │   ├── B4.TIF
│   │   ├── B5.TIF
│   │   ├── B6.TIF
│   │   ├── B7.TIF
│   │   ├── B8.TIF
│   │   ├── B9.TIF
│   │   ├── B10.TIF
│   │   └── B11.TIF
│   │
│   └── scene_002/
│
└── processed/
    ├── train/
    ├── validation/
    └── test/
```

---

# 🗂️ Complete Project Structure

```text
OGTSR/
│
├── README.md
├── LICENSE
├── .gitignore
├── requirements.txt
├── environment.yml
│
├── configs/
│   ├── default.yaml
│   └── experiment_x4.yaml
│
├── data/
│   ├── raw/
│   │   ├── scene_001/
│   │   └── scene_002/
│   │
│   └── processed/
│       ├── train/
│       ├── validation/
│       └── test/
│
├── notebooks/
│   ├── 01_data_preprocessing.ipynb
│   ├── 02_visualize_samples.ipynb
│   └── 03_inference_demo.ipynb
│
├── src/
│   ├── __init__.py
│   │
│   ├── data/
│   │   ├── dataset.py
│   │   ├── transforms.py
│   │   └── loader_utils.py
│   │
│   ├── models/
│   │   ├── ogtsr_net.py
│   │   ├── blocks.py
│   │   └── losses.py
│   │
│   ├── training/
│   │   ├── train.py
│   │   ├── evaluate.py
│   │   └── lr_scheduler.py
│   │
│   ├── inference/
│   │   ├── infer_tile.py
│   │   └── batch_infer.py
│   │
│   ├── utils/
│   │   ├── io.py
│   │   ├── metrics.py
│   │   └── logging.py
│   │
│   └── scripts/
│       ├── prepare_dataset.py
│       └── compute_emissivity_map.py
│
├── experiments/
│   ├── logs/
│   ├── checkpoints/
│   └── results/
│
├── deployment/
│   ├── docker/
│   │   ├── Dockerfile
│   │   └── entrypoint.sh
│   │
│   └── inference_service/
│       ├── app.py
│       └── model_loader.py
│
├── docs/
│   ├── architecture.md
│   ├── evaluation_plan.md
│   └── methodology.md
│
└── examples/
    ├── sample_input/
    └── demo_commands.sh
```

---

# 🧮 Loss Functions

The model should optimize multiple objectives.

The total loss can be represented as:

```text
L_total =
      α L_temperature
    + β L_SSIM
    + γ L_edge
    + δ L_physics
```

---

## 1. Temperature Reconstruction Loss

The primary objective is to keep the predicted thermal values close to the reference.

An L1 loss can be used:

```text
L_temperature = |T_predicted - T_reference|
```

This helps preserve thermal fidelity.

---

## 2. SSIM Loss

Structural Similarity Index helps preserve spatial structures.

```text
L_SSIM = 1 - SSIM(predicted, reference)
```

This encourages structural similarity between predicted and reference thermal images.

---

## 3. Edge Consistency Loss

Optical imagery contains strong spatial edges.

However, every optical edge should NOT automatically become a thermal edge.

Therefore, an edge-aware constraint can be used to encourage meaningful boundaries while reducing false texture transfer.

---

## 4. Physics-Based Loss

A future/advanced version of the system can incorporate physical constraints related to:

* Emissivity
* Radiative transfer
* Surface temperature
* Energy balance
* Thermal radiance

The objective is:

> Make the generated thermal image not only visually sharp, but physically meaningful.

---

# 📊 Evaluation

The model is evaluated using both quantitative and qualitative measures.

---

## Quantitative Metrics

### PSNR

Peak Signal-to-Noise Ratio.

Higher PSNR generally indicates better reconstruction quality.

```text
Higher PSNR → Better reconstruction
```

---

### SSIM

Structural Similarity Index.

```text
Higher SSIM → Better structural similarity
```

---

### RMSE

Root Mean Square Error.

For thermal imagery, RMSE should be reported in **Kelvin** where the evaluation data supports physically calibrated temperature values.

```text
Lower RMSE → Better thermal accuracy
```

---

# 📈 Evaluation Pipeline

```text
Reference Thermal
       │
       ├──────────────┐
       │              │
       ▼              ▼
Predicted Thermal   Difference Map
       │
       ▼
 ┌───────────────┐
 │ PSNR          │
 │ SSIM          │
 │ RMSE (Kelvin) │
 └───────────────┘
```

---

# 👁️ Qualitative Evaluation

Visual evaluation should include:

```text
Optical Image
      │
      ├── RGB
      │
      ▼
Low-Resolution Thermal
      │
      ▼
Bicubic Baseline
      │
      ▼
OGTSR Output
      │
      ▼
Reference Thermal
      │
      ▼
Difference Map
```

The generated result should be checked for:

* Sharp boundaries
* Correct thermal patterns
* Alignment
* Heat-region preservation
* Reduced artifacts
* Absence of artificial optical textures

---

# 🧪 Baseline Comparisons

The project should compare OGTSR against simpler approaches.

Recommended baselines:

### Baseline 1 — Bicubic

```text
LR Thermal
    ↓
Bicubic Upsampling
    ↓
SR Thermal
```

### Baseline 2 — Thermal-only Super-Resolution

Uses only thermal information.

```text
LR Thermal
    ↓
SR Network
    ↓
HR Thermal
```

### Baseline 3 — Optical + Thermal Concatenation

```text
Optical + Thermal
       ↓
CNN
       ↓
HR Thermal
```

### Proposed

```text
Optical
   +
Thermal
   ↓
Dual Encoder
   ↓
Cross-Attention Fusion
   ↓
Thermal Decoder
   ↓
HR Thermal
```

This comparison demonstrates whether optical guidance and the proposed fusion architecture actually improve performance.

---

# 🔬 Ablation Study

To understand which components are important, the following experiments can be performed.

| Experiment | Optical | Attention | Physics | Purpose                |
| ---------- | ------- | --------- | ------- | ---------------------- |
| A          | ❌       | ❌         | ❌       | Thermal-only baseline  |
| B          | ✅       | ❌         | ❌       | Basic optical fusion   |
| C          | ✅       | ✅         | ❌       | Cross-attention fusion |
| D          | ✅       | ✅         | ✅       | Full proposed system   |

Metrics:

```text
PSNR
SSIM
RMSE
Edge Quality
```

The objective is to demonstrate the contribution of each component.

---

# 🧠 Key Research Challenge

The biggest challenge is:

> **Optical imagery contains spatial information, but optical information does not necessarily imply temperature information.**

For example:

```text
Optical Image:

████████████
████ ROAD ██
████████████

```

A visible boundary does not automatically mean that there is an equivalent temperature boundary.

Therefore, the model must learn:

```text
Useful Optical Information
             ↓
        Selective Fusion
             ↓
      Thermal Reconstruction
```

rather than:

```text
Copy Optical Texture
          ↓
Fake Thermal Texture ❌
```

This is one of the most important design principles of this project.

---

# 🌍 Real-World Applications

## 🏙️ Urban Heat Island Analysis

High-resolution thermal maps can help identify:

* Hot roads
* Hot rooftops
* Industrial areas
* Heat-island regions
* Cooler vegetation zones

---

## 🔥 Wildfire Monitoring

Improved thermal spatial resolution can help:

* Detect thermal anomalies
* Identify fire boundaries
* Monitor fire spread
* Support emergency response

---

## 🌾 Precision Agriculture

Thermal information can help identify:

* Crop water stress
* Irrigation problems
* Heat stress
* Crop health variations

---

## 💧 Environmental Monitoring

Potential applications include:

* Water-body temperature analysis
* Soil temperature monitoring
* Ecosystem monitoring
* Drought analysis

---

# 🖥️ Technology Stack

## Programming

* Python

## Deep Learning

* PyTorch
* TorchVision

## Computer Vision

* OpenCV
* NumPy
* scikit-image

## Geospatial Processing

* Rasterio
* GDAL
* GeoPandas
* QGIS

## Visualization

* Matplotlib

## Experiment Tracking

* TensorBoard

## Deployment

* FastAPI
* Docker

---

# 📦 Installation

Clone the repository:

```bash
git clone https://github.com/YOUR_USERNAME/OGTSR.git
cd OGTSR
```

Create a virtual environment:

```bash
python -m venv venv
```

Activate it on Windows:

```bash
venv\Scripts\activate
```

Install dependencies:

```bash
pip install -r requirements.txt
```

---

# ⚙️ Configuration

Example:

```yaml
dataset:
  processed_dir: ./data/processed
  patch_hr: 256
  scale: 4
  optical_bands: [2, 3, 4]

model:
  base_channels: 64
  n_blocks: 8

training:
  batch_size: 8
  learning_rate: 0.0001
  epochs: 150

loss:
  temperature_weight: 1.0
  ssim_weight: 0.5
  edge_weight: 0.2
  physics_weight: 0.2
```

---

# 🚀 Training

Prepare the dataset:

```bash
python src/scripts/prepare_dataset.py
```

Train the model:

```bash
python src/training/train.py \
    --config configs/default.yaml
```

For 4× super-resolution:

```bash
python src/training/train.py \
    --config configs/experiment_x4.yaml
```

---

# 📊 Evaluation

Run:

```bash
python src/training/evaluate.py \
    --ckpt experiments/checkpoints/best.pt
```

The evaluation system produces:

```text
PSNR
SSIM
RMSE
MAE
Difference Maps
Visual Comparisons
```

---

# 🔍 Inference

Given new optical and thermal imagery:

```text
Optical Image
      +
Thermal Band 10 / 11
      ↓
Preprocessing
      ↓
OGTSR Model
      ↓
Super-Resolution
      ↓
High-Resolution Thermal Map
```

Example:

```bash
python src/inference/infer_tile.py \
    --optical path/to/optical.tif \
    --thermal path/to/thermal.tif \
    --checkpoint experiments/checkpoints/best.pt
```

---

# 🗺️ Large-Area Inference

Large satellite scenes cannot always be processed at once.

Therefore, the system uses tiled inference:

```text
Large Satellite Image
        ↓
 ┌────┬────┬────┐
 │ T1 │ T2 │ T3 │
 ├────┼────┼────┤
 │ T4 │ T5 │ T6 │
 └────┴────┴────┘
        ↓
   OGTSR Model
        ↓
   Tile Outputs
        ↓
Overlap / Blending
        ↓
Final Thermal Map
```

Overlapping tiles can be used to reduce boundary artifacts.

---

# 🌐 Deployment Architecture

The final system can be exposed through an API.

```text
                 User
                  │
                  ▼
             Web Interface
                  │
                  ▼
              FastAPI
                  │
                  ▼
          OGTSR Model Server
                  │
          ┌───────┴────────┐
          ▼                ▼
      Optical Data      Thermal Data
          │                │
          └───────┬────────┘
                  ▼
            SR Processing
                  │
                  ▼
        High-Resolution Thermal
                  │
                  ▼
                User
```

Docker can be used to package the inference system.

---

# 🧪 Expected Output

The final system should generate:

```text
Input:
    Low-resolution Thermal
            +
    High-resolution Optical

              ↓

        OGTSRNet

              ↓

Output:
    High-resolution Thermal Map
```

The output should demonstrate:

### ✅ Improved spatial detail

### ✅ Preserved thermal information

### ✅ Better edge localization

### ✅ Reduced optical artifacts

### ✅ Improved PSNR

### ✅ Improved SSIM

### ✅ Lower RMSE

---

# 🏆 Project Innovation

The key innovation is not simply:

> "Upscale a thermal image."

Instead, the project aims to:

> **Intelligently use high-resolution optical information to reconstruct spatial details in low-resolution thermal imagery while preventing false thermal textures and preserving temperature fidelity.**

The important concepts are:

```text
Optical Guidance
       +
Thermal Fidelity
       +
Deep Learning
       +
Cross-Modal Fusion
       +
Super-Resolution
       +
Physics-Aware Constraints
```

---

# 🔬 Future Enhancements

The project can be extended with:

### 1. Physics-Aware Learning

Integrate:

* Emissivity estimation
* Radiative transfer
* Planck-based constraints
* Energy balance

---

### 2. Advanced Cross-Attention

Use:

* Multi-head attention
* Cross-modal transformers
* Multi-scale attention

---

### 3. Automatic Registration

Develop a learnable registration module to handle residual optical-thermal misalignment.

---

### 4. Multi-Spectral Guidance

Instead of only RGB:

```text
B1–B7
```

can be used as the optical input.

---

### 5. Multi-Task Learning

The model could simultaneously estimate:

```text
Thermal Super-Resolution
        +
Land Cover
        +
Temperature Uncertainty
```

---

### 6. Uncertainty Estimation

A future version can estimate confidence:

```text
SR Thermal
     +
Uncertainty Map
```

This is particularly important for scientific and environmental applications.

---

# 📋 Project Workflow

The complete workflow is:

```text
                 ┌──────────────────┐
                 │ Landsat-8 Data   │
                 └────────┬─────────┘
                          │
                          ▼
                 ┌──────────────────┐
                 │ Band Extraction  │
                 └────────┬─────────┘
                          │
                          ▼
              ┌─────────────────────────┐
              │ Radiometric Processing  │
              └────────────┬────────────┘
                           │
                           ▼
              ┌─────────────────────────┐
              │ Optical-Thermal         │
              │ Registration            │
              └────────────┬────────────┘
                           │
                           ▼
              ┌─────────────────────────┐
              │ Normalization &         │
              │ Patch Extraction        │
              └────────────┬────────────┘
                           │
                           ▼
          ┌──────────────────────────────────┐
          │         OGTSRNet                 │
          │                                  │
          │ Optical Encoder                  │
          │        +                         │
          │ Thermal Encoder                  │
          │        ↓                         │
          │ Cross-Modal Fusion               │
          │        ↓                         │
          │ Thermal Decoder                  │
          └────────────────┬─────────────────┘
                           │
                           ▼
                ┌─────────────────────┐
                │ SR Thermal Output   │
                └──────────┬──────────┘
                           │
                           ▼
              ┌────────────────────────┐
              │ Evaluation             │
              │                        │
              │ PSNR                   │
              │ SSIM                   │
              │ RMSE (Kelvin)         │
              │ Edge Quality           │
              └────────────┬───────────┘
                           │
                           ▼
                High-Resolution
                Thermal Map
```

---

# 📅 Development Roadmap

## Phase 1 — Dataset Preparation

* [ ] Obtain Landsat-8 scenes
* [ ] Extract Bands 1–11
* [ ] Process thermal bands
* [ ] Prepare optical data
* [ ] Align optical and thermal imagery
* [ ] Normalize data
* [ ] Extract patches

---

## Phase 2 — Baseline

* [ ] Implement bicubic baseline
* [ ] Implement thermal-only SR
* [ ] Establish PSNR/SSIM/RMSE benchmarks

---

## Phase 3 — OGTSRNet

* [ ] Implement optical encoder
* [ ] Implement thermal encoder
* [ ] Implement fusion block
* [ ] Implement cross-attention
* [ ] Implement decoder
* [ ] Implement 2× SR
* [ ] Implement 4× SR

---

## Phase 4 — Loss Functions

* [ ] L1 temperature loss
* [ ] SSIM loss
* [ ] Edge consistency loss
* [ ] Thermal consistency loss
* [ ] Physics-based loss

---

## Phase 5 — Training

* [ ] Train 2× model
* [ ] Train 4× model
* [ ] Tune hyperparameters
* [ ] Save checkpoints
* [ ] Track experiments

---

## Phase 6 — Evaluation

* [ ] PSNR
* [ ] SSIM
* [ ] RMSE
* [ ] MAE
* [ ] Edge sharpness
* [ ] Alignment accuracy
* [ ] Artifact analysis
* [ ] Ablation studies

---

## Phase 7 — Deployment

* [ ] Build inference pipeline
* [ ] Implement tiled processing
* [ ] Build FastAPI service
* [ ] Dockerize application
* [ ] Create visualization interface

---

# 📊 Final Research Comparison

The final research should ideally demonstrate:

```text
                         PSNR ↑
                         SSIM ↑
                         RMSE ↓

Bicubic
   ↓
Thermal-only SR
   ↓
Optical + Thermal CNN
   ↓
Optical + Thermal Attention
   ↓
OGTSRNet
   ↓
OGTSRNet + Physics
```

The goal is to establish that the proposed optical-guided architecture provides better thermal reconstruction while avoiding artificial optical textures.

---

# ⚠️ Important Scientific Consideration

A higher-resolution thermal image generated by a neural network does **not automatically mean that every pixel represents a directly measured temperature**.

The model is performing an inference/reconstruction task.

Therefore, this project emphasizes:

```text
Spatial Enhancement
        +
Thermal Fidelity
        +
Physical Consistency
        +
Uncertainty Awareness
```

rather than simply producing visually sharper images.

This distinction is important for scientific remote-sensing applications.

---

# 🎯 Main Objective

The main objective of this project is:

> **To develop a deep-learning-based optical-guided super-resolution framework that transforms low-resolution Landsat-8 thermal infrared imagery into higher-resolution thermal maps by exploiting spatial information from optical imagery while preserving thermal fidelity and minimizing false optical artifacts.**

---

# 🌍 Why This Project Matters

Conventional thermal satellite imagery may not provide sufficient spatial detail for many applications.

By intelligently combining:

```text
🌈 Optical Spatial Information
              +
🔥 Thermal Information
              +
🧠 Deep Learning
              +
⚛️ Physics-Based Constraints
```

the project aims to generate thermal maps that are more useful for:

* Urban planning
* Heat island analysis
* Wildfire monitoring
* Precision agriculture
* Environmental monitoring

---

# 👥 Project Type

**Domain:** Remote Sensing + Computer Vision + Deep Learning

**Primary Technologies:**

```text
Python
PyTorch
OpenCV
Rasterio
GDAL
NumPy
scikit-image
QGIS
FastAPI
Docker
```

**Satellite:** Landsat-8

**Thermal Inputs:**

```text
Band 10
Band 11
```

**Optical Guidance:**

```text
RGB (B2, B3, B4)
```

or

```text
Multispectral OLI bands
```

**Super-Resolution:**

```text
2×
4×
```

**Evaluation:**

```text
PSNR
SSIM
RMSE (Kelvin)
MAE
Edge Sharpness
Alignment Accuracy
```

---

# 📌 Project Status

### Current development stage

```text
✅ Problem formulation
✅ Landsat-8 dataset identified
✅ Optical and thermal bands identified
✅ Band 10/11 selected as thermal targets
✅ RGB / multispectral optical guidance defined
✅ 2× and 4× SR objectives defined
✅ Data preprocessing pipeline designed
✅ Optical-thermal registration strategy defined
✅ OGTSRNet architecture designed
✅ Fusion strategy defined
✅ Loss functions defined
✅ Evaluation metrics defined
✅ Project folder structure defined
⬜ Dataset preprocessing implementation
⬜ Baseline implementation
⬜ OGTSRNet implementation
⬜ Model training
⬜ Quantitative evaluation
⬜ Ablation study
⬜ Deployment
```

---

# 🚀 Vision

The long-term vision of this project is to build a **scalable, physically-aware thermal super-resolution system** capable of processing large satellite scenes and producing high-resolution thermal maps suitable for real-world environmental applications.

> **From coarse thermal observations to detailed, intelligent thermal maps.**

---

## ⭐ Keywords

```text
Thermal Super-Resolution
Optical-Guided Super-Resolution
Landsat-8
Thermal Infrared
TIRS
OLI
Band 10
Band 11
Remote Sensing
Deep Learning
Computer Vision
Image Fusion
Multi-Modal Fusion
Cross-Attention
Super-Resolution
Satellite Imagery
Temperature Mapping
Physics-Aware AI
Urban Heat Island
Wildfire Detection
Precision Agriculture
```

---

# 📜 License

This project is intended for academic and research purposes.

Add the appropriate license before public release.

---

# 🙏 Acknowledgements

This project builds upon publicly available satellite remote-sensing data and open-source deep-learning and geospatial-processing technologies.

The project is intended to explore how artificial intelligence can improve the spatial usability of thermal remote-sensing data while maintaining scientific reliability.

```

### One important correction to keep in mind

I deliberately **did not claim that the complete model is already implemented or trained**. From our work so far, you have designed the methodology, dataset structure, architecture, preprocessing strategy, evaluation strategy, and project organization. The README marks the actual implementation/training stages as pending.

Also, for your final project, we should be very careful about the phrase **“100 m → 25 m/50 m”**: those are the requested 2×/4× *super-resolution scales*, but they should not automatically be described as equivalent to having a physically measured 25 m/50 m thermal sensor. That distinction will make your project much more scientifically defensible.
```
