# Fiber Segmentation Challenge: Unsupervised Instance Segmentation of Microscope Fibers

<p align="center">
  <video width="800" controls>
    <source src="https://github.com/user-attachments/assets/YOUR_ATTACHMENT_ID_HERE" type="video/mp4">
    Your browser does not support the video tag.
  </video>
</p>

<p align="center">
  <i>Watch the video above to see the complete unsupervised segmentation pipeline in action: from raw grayscale microscope image → binary mask → cleaning → skeletonization → junction splitting → individual fiber instances → measurements.</i>
</p>

## Overview

This repository presents a challenging **unsupervised instance segmentation** task on grayscale microscope images of polyester and cotton fibers. The objective is to automatically detect, segment, and separate individual fibers—even when they heavily overlap or cross—then measure their lengths and widths to analyze size distribution, **all without any manual annotations**.

State-of-the-art foundation models such as **Segment Anything Model (SAM)**, SAM-HQ, and MedSAM were extensively tested but failed to deliver reliable results on this dataset due to the domain gap (natural-color vs. noisy grayscale microscopy), lack of prompts/labels, and severe fiber overlaps.

The solution provided here relies on a robust **classical image processing pipeline** that achieves high-quality segmentation and instance separation. It serves as a practical benchmark for unsupervised segmentation in microscopy, material science, and textile analysis.

## Why This Problem is Challenging

- **No Annotations**: Zero ground-truth masks or bounding boxes → supervised training impossible.
- **Complex Topology**: Thin, curved, branched fibers that frequently cross and touch, creating ambiguous junctions.
- **Grayscale Microscopy**: Low contrast, stitching artifacts, noise, and no color or staining cues.
- **Failure of Modern Foundation Models**:
  - SAM requires prompts; automatic prompting yields severe over-/under-segmentation.
  - Overlapping fibers are consistently merged into single instances.
  - Thin structures are often missed or fragmented.
- **High Variability**: 4 fiber types × 2 concentrations across 24 high-resolution images, showing extreme length variation (short fragments to very long entangled fibers).
- **Real-World Relevance**: Essential for textile quality control, microplastic detection, and material characterization.

This dataset is an excellent testbed for unsupervised segmentation, graph-based instance separation, or pseudo-labeling research.

## Dataset

- **24 Reconstructed Images**: High-resolution grayscale stitched composites.
- **Raw Tiles**: Original microscope tiles organized by:
  - Fiber type: `r02` (type 1), `r03` (type 2), `r04` (type 3), `r05` (type 4).
  - Concentration: `c02–c04` (low), `c05–c07` (high).
- **No Annotations**: Deliberately unlabeled to emphasize the unsupervised nature.

Sample images and processing results are included in the repository.

### Pipeline Visualization (Sample)

| Original | Binary Mask | Clean Mask | Skeleton | Split Skeleton | Color-coded Instances | Overlay |
|--------|-------------|------------|----------|----------------|-----------------------|---------|
| ![Original](images/original.png) | ![Binary](images/binary.png) | ![Clean](images/clean.png) | ![Skeleton](images/skeleton.png) | ![Split](images/split.png) | ![Instances](images/instances.png) | ![Overlay](images/overlay.png) |

## Approach

Fully unsupervised classical pipeline:

1. **Preprocessing** → Grayscale normalization + adaptive thresholding (Otsu).
2. **Mask Cleaning** → Morphological operations (opening, closing, hole filling) to remove noise and weak connections.
3. **Skeletonization** → Thinning to 1-pixel centerlines (`scikit-image`).
4. **Junction Detection & Splitting** → Identify branch points and split the skeleton graph.
5. **Instance Separation** → Connected component labeling + refinement on split skeletons.
6. **Measurements** → Length (skeleton-based), average width, and per-fiber statistics.

No deep learning is used — lightweight, deterministic, and highly effective for this domain.

## Results

The pipeline successfully segments and separates fibers across the entire dataset.

### Example Quantitative Results

- **Sample Image 1**: 30 fibers  
  Lengths (pixels): Min = 14.1, Max = 657.7, Mean = 80.8, Std = 146.1
- **Sample Image 2**: 41 fibers  
  Lengths (pixels): Min = 19.5, Max = 1927.5, Mean = 113.1, Std = 291.8

Outputs include:
- Color-coded instance images
- Overlays on original images
- CSV files with per-fiber metrics (ID, length, width, etc.)

See the video at the top for a full step-by-step demonstration.
