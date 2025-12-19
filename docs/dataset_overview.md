# Dataset Overview

The **PEM-SEM-Defect** dataset is a curated and augmented collection of scanning electron
microscopy (SEM) images designed for **defect localization** in polymer electrolyte
membrane (PEM) fuel cell materials.

The dataset is released as a **single unified collection**, containing both original and
augmented images along with corresponding bounding-box annotations.

---

## Dataset Composition

- **Total images:** 9,064
  - **Original images:** 206
  - **Augmented images:** 8,858
- **Augmentations per base image:** 43
- **Image format:** PNG (grayscale)
- **Task type:** Supervised defect localization

Each original image contributes exactly 43 augmented variants, ensuring **uniform dataset
expansion** and eliminating augmentation-induced imbalance.

---

## Annotation Summary

- **Total bounding box annotations:** 76,600
- **Average annotations per image:** 8.45
- **Images with annotations:** 100% (no empty labels)

Annotations specify rectangular regions enclosing defect areas and are provided in a
single JSON file.

---

## Design Philosophy

The dataset is designed to:
- Support localization rather than classification
- Preserve SEM-relevant visual characteristics
- Enable training of lightweight and deep learning models
- Provide a realistic testbed for data augmentation strategies

The dataset does **not** include acquisition metadata, scale-aware measurements, or
instrument-specific parameters.
