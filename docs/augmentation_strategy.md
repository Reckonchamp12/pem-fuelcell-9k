# Augmentation Strategy

To address the limited availability of SEM defect images and improve model robustness,
each original image in the dataset was expanded using a **fixed, uniform augmentation
policy**.

The augmentation strategy emphasizes **physical plausibility**, **uniformity**, and
**annotation consistency**, rather than aggressive synthetic diversity.

---

## 1. Augmentation Policy Overview

- **Original images:** 206
- **Augmentations per image:** 43
- **Total variants per base image:** 44 (43 augmented + 1 original)
- **Total images after augmentation:** 9,064

Each original image contributes **exactly the same number of augmented variants**, ensuring
a perfectly balanced dataset with no augmentation-induced bias.

---

## 2. Augmentation Categories

Augmentations were selected to reflect realistic sources of variability in SEM image
acquisition. Transformations fall into the following categories:

### Spatial Transformations
- Horizontal and vertical flipping
- Small-angle rotations (±10°)
- Minor translations and scaling

These operations preserve defect geometry while introducing positional variability.

---

### Photometric Transformations
- Brightness and contrast variation
- Gamma correction
- Gaussian noise
- Mild Gaussian blur

These transformations simulate:
- Detector gain fluctuations
- Beam current variability
- Focus drift and beam spread
- Shot noise and readout noise

---

### Resolution and Compression Effects
- Downsampling followed by upsampling
- Mild compression artifacts

These effects reflect common variations arising from:
- Pixel binning
- Resolution trade-offs
- Data storage and transmission

---

## 3. Bounding Box Handling

- Bounding boxes were transformed alongside images
- Boxes with insufficient visibility were discarded
- Extremely small boxes were filtered to prevent annotation noise
- All defects were treated as a **single category** (localization only)

After augmentation:
- All images retained valid annotations
- No out-of-bound boxes were introduced
- Annotation format remained consistent

---

## 4. Physical Plausibility Considerations

Augmentation parameters were constrained to remain within ranges commonly observed in SEM
practice. Validation confirmed:

- Preservation of global intensity statistics
- Controlled contrast variation
- Edge attenuation consistent with focus variability
- No artificial amplification of high-frequency content

Augmentations introduce **realistic acquisition variability** without creating
non-physical artifacts or new defect semantics.

---

## 5. Intended Role of Augmentation

Augmentations are intended to:
- Improve generalization of localization models
- Provide regularization during training
- Simulate acquisition variability

They are **not intended** to:
- Create new defect classes
- Alter defect morphology
- Simulate physical scale changes
