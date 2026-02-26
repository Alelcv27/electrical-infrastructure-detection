## Cable and Tower Detection and Segmentation

**Authors**

- Alessandro La Cava 
- Matteo Parrotta

### Project Overview

This project aims to build a model capable of performing **object detection** and **instance segmentation** to recognize and localize:

- Transmission towers  
- Overhead electrical cables

The goal of **instance segmentation** is to distinguish and identify each individual object instance at pixel level, while **object detection** focuses on locating and classifying objects using bounding boxes.  
The project compares different approaches and focuses on improving the segmentation quality, especially for thin, difficult structures such as cables.

### Dataset

The dataset consists of **aerial images of transmission towers and electrical cables** with the following characteristics:

- Image size: **700 × 700** pixels  
- **Training set**: 842 images  
- **Test set**: 400 images  
- Annotations: for each split, a **COCO-format JSON** file provides:
  - Image metadata  
  - Object labels (tower, cable, etc.)  
  - Segmentation masks for each object

These annotations enable both **bounding box detection** and **pixel-accurate segmentation**.

### Model Selection

The project explores multiple models for instance segmentation and object detection.

- **Detectron2 (Mask R-CNN based)**  
  Initially, Detectron2 (a framework based on Mask R-CNN) was used for the instance segmentation task.  
  However, the results were not satisfactory, particularly in terms of segmentation accuracy on the cable class.

- **YOLOv8 for Detection and Segmentation**  
  Due to the limitations encountered with Detectron2, the project adopted the **YOLOv8** family:
  - `YOLOv8s-seg` (small variant)  
  - `YOLOv8l-seg` (large variant)  

  YOLO (You Only Look Once) is a **single-stage object detector** that uses a CNN to jointly predict bounding boxes and class probabilities in a single forward pass, making it fast and efficient compared to two-stage detectors (e.g. R-CNN variants).

- **HQ-SAM for High-Quality Segmentation (Future/Extended Work)**  
  Since YOLOv8 segmentation struggles in accurately reconstructing cable masks, the project also considers **HQ-SAM (High Quality Segment Anything Model)** from Meta as a complementary model.  
  The idea is:
  - Use **YOLOv8** as an object detector to obtain high-quality **bounding boxes**.  
  - Use **HQ-SAM** to generate refined **segmentation masks** from those bounding boxes.

This leads to a **two-model pipeline**: YOLOv8 for detection, HQ-SAM for mask refinement.

### Training

The training phase focuses on the YOLOv8 segmentation variants:

- Models used:
  - `YOLOv8s-seg`  
  - `YOLOv8l-seg`
- Input format:
  - Data converted to the **YOLO format** when needed (different from COCO), while the original dataset is provided in **COCO JSON** for masks and labels.


### Testing and Evaluation

The testing phase evaluates both:

- **Bounding box metrics**  
- **Segmentation metrics**

In particular, the project examines:

- **mAP@50** (mean Average Precision at IoU 0.50) for both bounding boxes and masks.

Findings:

- **Bounding box performance** (detection of towers and cables) is **very good**, with strong mAP@50 values.  
- **Segmentation performance** is good for most objects **except for cables**, where the masks are often poorly reconstructed.

This confirms that:

- YOLOv8 is **effective for object detection** (bounding boxes).  
- Thin, elongated structures like cables remain challenging for the segmentation head.

### HQ-SAM Integration (Extended Approach)

To address the difficulties in cable segmentation, the project investigates **Segment Anything Model (SAM)** and its variant **HQ-SAM**:

- **SAM** (from Meta) is designed to "segment anything" in an image, including objects or regions that were not part of its original training classes.
- **HQ-SAM** improves mask quality, providing **more precise segmentation**.

Proposed combined pipeline:

1. Use a **YOLOv8** object detection model to detect towers and cables and output bounding boxes.  
2. Feed those bounding boxes into **HQ-SAM**.  
3. Use HQ-SAM to generate **high-quality segmentation masks** for the detected regions.

This combination aims to:

- Preserve YOLOv8’s strong **detection** capabilities.  
- Leverage HQ-SAM’s strengths in **fine-grained segmentation**, especially for cables.

### Results and Discussion

From the experiments:

- **YOLOv8** performs **very well for bounding box detection**, accurately localizing towers and cables.  
- For **segmentation masks**:
  - YOLOv8 reconstructs non-cable objects (e.g. towers) well.  
  - Cable masks are often incomplete or inaccurate.

This motivates the exploration of **HQ-SAM** (and similar models) to improve segmentation quality for difficult, thin structures.

### Repository Contents

The repository is designed to be simple and centered around a notebook-based workflow:

- **Main notebook**  
  - A Jupyter notebook (e.g. `electrical_infrastructure_detection.ipynb`) that:
    - Loads the dataset  
    - Trains YOLOv8 segmentation models  
    - Evaluates detection and segmentation performance  
    - Optionally demonstrates HQ-SAM post-processing

- **Dataset folder**  
  - A `data/` directory (or similar) containing:
    - Training and test images (700 × 700)  
    - COCO-format JSON files with labels and masks

- **Outputs folder**  
  - An `outputs/` directory where the notebook saves:
    - Trained model checkpoints  
    - Evaluation metrics  
    - Qualitative visualizations (detections and masks)

All the logic needed to reproduce the experiments is contained in the notebook + dataset.

### How to Run

1. **Clone the repository**

   ```bash
   git clone https://github.com/<your-org>/electrical-infrastructure-detection.git
   cd electrical-infrastructure-detection
   ```

2. **Create and activate a virtual environment (recommended)**

   ```bash
   python -m venv .venv
   .venv\Scripts\activate    # on Windows
   # source .venv/bin/activate  # on macOS / Linux
   ```

3. **Install dependencies**

   Install the libraries used in the notebook, for example:

   - PyTorch and CUDA (if using GPU)  
   - Ultralytics YOLOv8  
   - HQ-SAM / SAM implementation (if used)  
   - Common Python packages: `numpy`, `pandas`, `matplotlib`, `opencv-python`, etc.

   Once you have a `requirements.txt`, you can install everything with:

   ```bash
   pip install -r requirements.txt
   ```

4. **Prepare the dataset**

   - Place the training and test images in a `data/` folder.  
   - Place the COCO-format JSON annotation files in the appropriate subfolders (e.g. `data/train/annotations.json`, `data/test/annotations.json`), or adjust the paths inside the notebook.

5. **Open and run the notebook**

   - Start Jupyter Lab / Notebook or open the notebook in VS Code.  
   - Open the main notebook (e.g. `electrical_infrastructure_detection.ipynb`).  
   - Check and, if necessary, update dataset paths and configuration cells.  
   - Run all cells to:
     - Train YOLOv8 models  
      - Evaluate detection and segmentation  
      - Optionally apply HQ-SAM for refined masks  
      - Save results to the `outputs/` folder

Developed as an educational project at the Università della Calabria.