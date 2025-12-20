# Augmentation Strategy for SEM Defect Dataset

To address the limited availability of SEM defect images and improve model robustness, each original image in the dataset was expanded using a **fixed, uniform augmentation policy**.

The augmentation strategy emphasizes **physical plausibility**, **uniformity**, and **annotation consistency**, rather than aggressive synthetic diversity. It was specifically designed to mimic realistic Scanning Electron Microscope (SEM) acquisition variations rather than applying arbitrary image manipulations. All transformations correspond to observable SEM phenomena encountered during practical materials inspection.

---

## 1. Augmentation Policy Overview

- **Original images:** 206  
- **Augmentations per image:** 43  
- **Total variants per base image:** 44 (43 augmented + 1 original)  
- **Total images after augmentation:** 9,064  

Each original image contributes **exactly the same number of augmented variants**, ensuring a perfectly balanced dataset with no augmentation-induced bias.

### Key Implementation Details
- 43 fixed augmentations per base image ensuring perfect balance  
- Progressive combination of transformations (not all applied simultaneously)  
- Memory-efficient batch processing (5 images per batch) preventing OOM errors  
- Deterministic randomness with fixed seeds for reproducibility  
- Bounding box preservation through geometric transformation matrices  

**Progressive Combination Clarification:**  
A total of **43 fixed augmentation sequences** were defined. Each sequence combines **2–4 transformations** selected from the 11 core augmentation types (e.g., *Flip → Brightness Adjustment → Gaussian Noise*). All sequences were explicitly designed to remain physically plausible and are applied uniformly across the dataset.

---

## 2. Augmentation Categories (11 Core Transformations)

Augmentations were selected to reflect realistic sources of variability in SEM image acquisition. All 11 transformation types correspond to measurable SEM parameters.

### 2.1 Spatial Transformations
- **Horizontal / Vertical Flips**  
  Represents sample reorientation on the SEM stage  
- **Rotation (±10°)**  
  Mimics stage rotation during multi-angle inspection  
- **ShiftScaleRotate**  
  Combines stage translation with minor magnification changes  

These operations preserve defect geometry while introducing positional variability.

### 2.2 Photometric Transformations
- **Brightness / Contrast Adjustment (±8% / ±10%)**  
  Models detector gain fluctuations and beam current variations  
- **Gamma Correction (0.9–1.1)**  
  Accounts for detector nonlinearity and post-processing adjustments  
- **Gaussian Noise Addition (σ = 3–15)**  
  Replicates shot noise and detector readout noise  
- **Gaussian Blur (σ = 1–3 pixels)**  
  Simulates electron beam spread and minor focus drift  
- **CLAHE**  
  Adaptive contrast enhancement simulating post-processing  

These transformations simulate physical acquisition variations rather than arbitrary manipulations.

### 2.3 Resolution and Compression Effects
- **Downsampling–Upsampling (0.75×–0.9×)**  
  Simulates resolution variations and pixel binning  
- **Image Compression (JPEG quality 90–95)**  
  Represents data storage and transmission artifacts  

These effects reflect common variations arising from different acquisition resolutions, data storage formats, and industrial data transmission pipelines.

---

## 3. Implementation Details

Each augmentation is applied within constrained parameter ranges to ensure physical realism.

### Parameter Ranges
- **Rotation:** ±10 degrees  
- **Translation:** ±5% of image dimensions  
- **Scaling:** 0.9× to 1.1×  
- **Brightness / Contrast:** ±8% / ±10%  
- **Gamma Correction:** 0.9 to 1.1  
- **Gaussian Noise:** σ = 3 to 15  
- **Gaussian Blur:** σ = 1 to 3 pixels  
- **Downsampling:** 0.75× to 0.9× of original resolution  
- **Compression:** JPEG quality 90–95  

### Processing Pipeline
- On-the-fly augmentation during training only  
- Batch-level variation (each epoch sees different augmented variants)  
- GPU-accelerated where possible for efficiency  
- Progressive combination approach using predefined, physically plausible augmentation sequences  

---

## 4. Bounding Box Handling

- Bounding boxes were transformed alongside images using geometric transformation matrices  
- Boxes with insufficient visibility (post-augmentation area < 25 pixels) were discarded  
- Extremely small boxes (< 10 pixels in any dimension) were filtered to prevent annotation noise  
- All defects were treated as a **single category** (localization only)  

### Annotation Integrity Metrics
- Only **0.22% bounding box loss** during transformations  
- All images retained valid annotations  
- No out-of-bound boxes were introduced  
- Annotation format remained consistent (YOLO format preserved)  
- **Baseline performance:** 0.864 IoU on unseen original images  

---

## 5. Physical Plausibility Considerations

Augmentation parameters were constrained to remain within ranges commonly observed in SEM practice. Each transformation corresponds to measurable SEM parameters.

### SEM Parameter Correspondence
- **Blur** → Beam focus quality (typically ±1–3 pixels at working magnification)  
- **Brightness** → Detector gain / beam current (5–15% variation between acquisitions)  
- **Noise** → Shot noise increasing with reduced electron dose  
- **Resolution changes** → Different acquisition resolutions or pixel binning  
- **Compression** → Data storage formats in industrial settings  

### Validation Outcomes
- Preservation of global intensity statistics  
- Controlled contrast variation (±15% maximum)  
- Edge attenuation consistent with focus variability  
- No artificial amplification of high-frequency content  
- Defect morphology remained recognizable and physically plausible  
- All **5 SEM-appropriate criteria** passed in physical plausibility assessment  

### Excluded Transformations

| Transformation Type        | Reason for Exclusion |
|---------------------------|----------------------|
| Elastic deformation       | Violates crystal structure and material properties |
| Perspective warping       | Incompatible with beam–sample geometry in SEM |
| Style transfer            | Disrupts material contrast physics and grayscale intensity relationships |
| Synthetic defect creation | Preserves annotation integrity and dataset purity |
| Color space manipulation  | SEM images are intrinsically grayscale |

---

## 6. Validation Metrics

The augmentation strategy was validated using both quantitative and qualitative measures.

- **Feature space analysis:** 5.009 inter/intra base distance ratio  
  - A ratio greater than 5 indicates that augmented variants of the same base image remain closer in feature space than images of different defects, confirming that the augmentations preserve semantic content while introducing controlled variance.
- **Physical plausibility:** 5/5 SEM-appropriate criteria passed  
- **Annotation integrity:** 0.22% bounding box loss  
- **Baseline performance:** A YOLOv8 model trained on the augmented dataset achieved **0.864 mIoU** on a hold-out set consisting only of original (non-augmented) images  
- **Statistical consistency:** Preservation of pixel value distributions  
- **Visual inspection:** Manual verification of augmented samples  

### Quality Assurance Pipeline
1. Visual inspection of sample augmented images  
2. Verification of bounding box transformations  
3. Statistical analysis of pixel value distributions  
4. Confirmation that no artifacts resemble defects  
5. Reversibility checks for debugging  
6. Feature space consistency analysis using embedding distances  

---

## 7. Intended Role of Augmentation

### Augmentations Are Intended To:
- Improve generalization of localization models  
- Provide regularization during training  
- Simulate acquisition variability  
- Increase effective dataset size  
- Reduce overfitting to specific acquisition conditions  
- Model realistic SEM parameter variations encountered in practice  

### Augmentations Are *Not* Intended To:
- Create new defect classes  
- Alter defect morphology beyond realistic SEM variability  
- Simulate physical scale changes  
- Replace diverse real data collection  
- Introduce non-physical artifacts or distortions  

### Limitations
This augmentation strategy models **intra-sample variability** but cannot simulate **inter-sample diversity**, such as entirely new defect morphologies or material systems. It is therefore intended as a complement to, rather than a replacement for, collecting more diverse base SEM images.

---

*The effectiveness and validity of this augmentation strategy are quantitatively and qualitatively verified, with all supporting results presented and analyzed in the associated research paper.*
