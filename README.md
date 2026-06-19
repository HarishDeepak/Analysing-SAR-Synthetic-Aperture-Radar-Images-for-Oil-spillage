# SAR Image Segmentation for Oil Spill Detection

Design and implementation of multiple image segmentation algorithms for automatic oil spill detection in **Synthetic Aperture Radar (SAR)** images. Six methods are implemented, compared quantitatively, and validated against published ground-truth masks.

![SAR Image](https://user-images.githubusercontent.com/96207365/183258716-74fe57a6-8c97-4d98-97e3-ff2ea7b8d79a.jpg)

---

## Problem Overview

SAR sensors image ocean surfaces regardless of weather or daylight, making them ideal for oil spill monitoring. Oil slicks appear as **dark spots** (low radar backscatter) in SAR imagery — but natural phenomena produce look-alikes. The challenge is to segment genuine oil spills while rejecting false positives.

**Ground-truth classes** (from benchmark dataset):
| Colour | Class |
|---|---|
| Cyan | Oil spill |
| Red | Look-alike |
| Brown | Ship |
| Green | Land |
| Black | Sea surface |

**Output classes produced by this project**: Oil spill (cyan), Land (green), Sea (black).

---

## Segmentation Methods

Six methods are implemented, covering classical and advanced approaches:

### 1. Manual Thresholding (`manual_thresholding.m`)
User-defined global intensity threshold. Baseline method; performance degrades under varying SAR image brightness.

### 2. Automatic Thresholding (`automatic_threshold.m` / `automatic_threshold_for_land.m`)
Threshold computed automatically from image statistics. Separate pipelines handle images with and without land regions.

### 3. Local Adaptive Thresholding (`local_threshold.m`)
Threshold computed per local neighbourhood window to handle non-uniform brightness and speckle noise across the image.

### 4. Superpixel Segmentation (`superpixel.m`)
Groups spatially coherent pixels into superpixels, then applies **dark-spot feature extraction**: geometric (area, aspect ratio, compactness) and intensity-based conditions filter candidate blobs to retain plausible oil spill regions only.

![Feature Extraction](https://user-images.githubusercontent.com/96207365/183258954-a0a04656-32d4-42a1-9884-b0668dc1b379.jpg)

### 5. Fuzzy Logic Edge Detection (`fuzzy_edgeDetect.m`)
Applies fuzzy membership functions to classify pixel transitions and detect oil spill boundaries. Dark-spot feature extraction applied to candidate blobs post-edge detection.

### 6. K-Means Clustering (`kmeansSegment.m` / `kmeansSegment_for_land.m`)
Unsupervised clustering in intensity space; colour-coded cluster maps produced for visual interpretation. Separate routine for land+sea images.

![K-means Clustering](https://user-images.githubusercontent.com/96207365/183259085-7a9be686-348b-4d3c-b775-c34ce0133a80.jpg)

---

## Preprocessing Pipeline

```
Raw SAR image
  → Lee filter          (speckle noise reduction — SAR-specific)
  → Wiener filter       (additional noise smoothing)
  → Median filter       (salt-and-pepper removal)
  → Histogram equalisation  (contrast enhancement)
  → Morphological operations
  → [Segmentation method]
  → Dark-spot feature extraction  (superpixel / fuzzy logic only)
  → Binary segmentation mask
```

![Image Enhancement](https://user-images.githubusercontent.com/96207365/183258816-0881c834-f437-41d3-a101-a8582dba555a.jpg)

---

## Evaluation Metrics

All six methods are evaluated against ground-truth masks using three quantitative metrics:

| Metric | What It Measures |
|---|---|
| **Jaccard Index (IoU)** | Intersection over union — precision of overlap |
| **Sørensen–Dice Similarity** | More sensitive to small spill regions |
| **BF Score** | Boundary F-measure — accuracy of detected spill edges |

Results visualised with a three-colour overlay:
- **Green** = Ground truth boundary
- **White** = Correctly segmented (true positive overlap)
- **Violet** = Over-segmented (predicted but not in ground truth)

![Evaluation Indices](https://user-images.githubusercontent.com/96207365/185741928-d8a9379d-d6f7-490b-8694-4d1b32435daf.jpg)

![Overlayed Mask](https://user-images.githubusercontent.com/96207365/185742093-b89e98e3-aa9e-43fa-9b67-394317a99cc7.jpg)

---

## File Structure

| File | Purpose |
|---|---|
| `ElicioDom__Progetto_Oil_Spill_Detection.m` | Main entry script |
| `manual_thresholding.m` | Global manual threshold |
| `automatic_threshold.m` | Automatic threshold (sea-only images) |
| `automatic_threshold_for_land.m` | Automatic threshold (land+sea images) |
| `local_threshold.m` | Local adaptive threshold |
| `superpixel.m` | Superpixel + dark-spot feature extraction |
| `fuzzy_edgeDetect.m` | Fuzzy logic edge detection |
| `kmeansSegment.m` | K-means clustering (sea-only) |
| `kmeansSegment_for_land.m` | K-means clustering (land+sea) |
| `land_mask.m` | Land masking preprocessing utility |
| `lee_filter.m` | Lee speckle filter implementation |
| `segmentation_evaluation.m` | Jaccard, Dice, BF score computation |
| `visualizeImages.m` | Overlay visualisation (colour-coded results) |
| `visualizeImages_for_land.m` | Overlay visualisation for land+sea images |

---

## Setup

**Requirements**: MATLAB with Image Processing Toolbox

1. Clone this repository
2. Download SAR images and ground-truth masks from the benchmark dataset
3. Run `ElicioDom__Progetto_Oil_Spill_Detection.m` as the entry point
4. Adjust thresholds, cluster counts, and blob-size parameters per image

See the [Code explanation PDF](https://github.com/HarishDeepak/Analysing-SAR-Synthetic-Aperture-Radar-Images-for-Oil-spillage/blob/main/Code%20explanation%20(technical%20implementation).pdf) for a detailed technical walkthrough of each algorithm.

---

## References

- Brekke C. and Solberg A.H.S., "Oil spill detection by satellite remote sensing" — *Remote Sensing of Environment*, 2005
- Oil Spill Identification from Satellite Images Using Deep Neural Networks — [ResearchGate](https://www.researchgate.net/publication/334715725_Oil_Spill_Identification_from_Satellite_Images_Using_Deep_Neural_Networks)
- MATLAB Image Processing Toolbox — [docs.mathworks.com](https://www.mathworks.com/products/image.html)
