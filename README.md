This refined README incorporates the technical specifics from your project presentation and notebook while maintaining a professional structure for your repository.

# Cable and Tower Detection and Segmentation

**Authors:** Alessandro La Cava & Matteo Parrotta

This repository contains an educational project developed at the **Università della Calabria** focused on the automated monitoring of electrical infrastructure through computer vision.

## Project Overview

The objective is to evaluate and build a pipeline for **Object Detection** and **Instance Segmentation** to localize transmission towers and overhead electrical cables in aerial imagery. A key focus of the work is addressing the architectural challenge of segmenting thin, elongated structures like cables.

## Dataset

The project utilizes a specialized dataset of aerial images (700x700 pixels):

* **Training Set:** 842 images.
* **Test Set:** 400 images.
* **Format:** Annotations are provided in **COCO JSON format**, including labels and masks for four classes: `cable`, `tower_lattice`, `tower_tucohy`, and `tower_wooden`.

## Model Selection & Methodology

The study followed a comparative approach to identify the most effective architecture:

* **Detectron2 (Mask R-CNN):** Initially evaluated but found to be less effective for this specific task, particularly in mask reconstruction.
* **YOLOv8:** Implemented as a faster, single-stage alternative for real-time processing. Both `YOLOv8s-seg` and `YOLOv8l-seg` variants were tested.
* **HQ-SAM (High Quality Segment Anything Model):** Investigated as a state-of-the-art solution to "repair" suboptimal masks. It utilizes **HQ-Output tokens** and **global-local feature fusion** to provide high-fidelity segmentation for complex structures.

## Key Findings

* **Detection Excellence:** YOLOv8 performs "egregiously well" (eccellentemente) in generating bounding boxes and localizing objects across all classes.
* **Segmentation Bottleneck:** While standard models segment towers effectively, they struggle with thin **electric cable masks**.
* **Pipeline Optimization:** The project demonstrates that a two-step pipeline—using **YOLOv8 for detection** and **HQ-SAM for refinement**—yields significantly more precise context recognition and mask accuracy for cables.

## Repository Structure

* `electrical_infrastructure_detection.ipynb`: The main notebook containing the data pipeline, training loops, and comparative evaluation.
* `data/`: Directory for COCO-formatted images and annotations.
* `outputs/`: Stores trained weights, mAP@50 metrics, and qualitative visualizations.

## How to Run

1. **Clone:** `git clone https://github.com/<your-username>/cable-tower-detection.git`
2. **Environment:** Install dependencies (`ultralytics`, `segment-anything-fast`, `opencv-python`, etc.).
3. **Data:** Ensure images and `instances_default.json` are placed in the `data/` folder as per the notebook paths.
4. **Execute:** Run the cells in the Jupyter notebook to reproduce the benchmarking and HQ-SAM post-processing.

---

*Developed for the Computer Vision course at Università della Calabria.*
