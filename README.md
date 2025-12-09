# 🏗️ Structure-from-Motion (SfM) — 3D Room Reconstruction

A complete incremental Structure-from-Motion pipeline that reconstructs 3D point clouds from sequential images of brick walls. This project demonstrates multi-view geometry, camera pose estimation, and 3D reconstruction using OpenCV and Open3D.

## 📋 Table of Contents
- [Overview](#overview)
- [Features](#features)
- [Prerequisites](#prerequisites)
- [Installation & Setup](#installation--setup)
- [Running the Pipeline](#running-the-pipeline)
- [Viewing Results](#viewing-results)
- [Pipeline Stages](#pipeline-stages)
- [File Structure](#file-structure)
- [Contributors](#contributors)
---

## 🎯 Overview

This project implements a complete Structure-from-Motion pipeline that:
1. Takes sequential images of two brick walls
2. Extracts and matches SIFT features
3. Estimates camera poses using essential matrices
4. Reconstructs 3D point clouds with color
5. Merges multiple reconstructions into a unified scene
6. Provides an interactive 3D viewer

**Result**: A colored 3D point cloud viewable in your browser with camera trajectory visualization.

---

## ✨ Features

- **SIFT Feature Extraction** with Lowe's Ratio Test matching
- **Essential Matrix Estimation** using RANSAC
- **Two-View Initialization** with cheirality checks
- **Incremental SfM** with PnP-based camera registration
- **Bundle Adjustment** for optimized reconstruction
- **Point Cloud Coloring** from source images
- **Multi-Wall Merging** with automatic alignment
- **Interactive 3D Viewer** (Three.js-based HTML viewer)

---

## 📦 Prerequisites

- **Google Colab** account (recommended) or local Python 3.7+
- **Google Drive** with ~500MB free space
- Modern web browser (Chrome, Firefox, Edge)

### Required Python Libraries
```
opencv-python
open3d
pillow
pillow-heif
tqdm
exifread
matplotlib
scipy
numpy
```

---

## 🚀 Installation & Setup

### Step 1: Clone the Repository
```bash
git clone https://github.com/fnauma/CV-Project-G16.git
cd CV-Project-G16
```

### Step 2: Upload Images to Google Drive

1. Download the repository or locate the image folders:
   - `wall1/` (8 images)
   - `wall2/` (12 images)

2. Upload these folders to your Google Drive:
   ```
   Google Drive/
   └── cv/
       ├── wall1/
       │   ├── 1.jpg
       │   ├── 2.jpg
       │   └── ... (8 total)
       └── wall2/
           ├── 1.jpg
           ├── 2.jpg
           └── ... (12 total)
   ```

### Step 3: Open Notebook in Colab

1. Upload `group_16.ipynb` to Google Colab
2. Or open directly from GitHub:
   - Go to [Google Colab](https://colab.research.google.com/)
   - File → Open Notebook → GitHub tab
   - Enter repository URL

### Step 4: Configure Image Paths

In the notebook, update these lines with your Google Drive paths:

```python
# For Wall 1
jpg_images = "/content/drive/MyDrive/cv/wall1"

# For Wall 2  
jpg_images = "/content/drive/MyDrive/cv/wall2"
```

### Step 5: Mount Google Drive

Run the first cell to mount your drive:
```python
from google.colab import drive
drive.mount('/content/drive')
```

---

## 🏃 Running the Pipeline

### Full Automatic Run (Recommended)
Simply execute **Runtime → Run All** in Colab. The pipeline will:

1. ✅ Load and display images
2. ✅ Extract SIFT features
3. ✅ Match features between consecutive images
4. ✅ Estimate essential matrices
5. ✅ Initialize two-view reconstruction
6. ✅ Add cameras incrementally
7. ✅ Perform bundle adjustment
8. ✅ Extract point colors from images
9. ✅ Merge both wall reconstructions
10. ✅ Export final outputs

### Manual Step-by-Step Execution

Run cells sequentially to inspect intermediate outputs:

```python
# 1. Load images
display_images(8)

# 2. Feature extraction
keypoints, descriptors = extract_features(preprocessed)

# 3. Feature matching
good_matches_list = match_consecutive_images(descriptors)

# 4. Two-view reconstruction
R, t, pts3D_filtered = triangulate_cheiralitycheck_ply(...)

# 5. Full incremental SfM
run_full_reconstruction(...)

# 6. Bundle adjustment
run_bundle_adjustment(...)

# 7. Export colored PLY
add_colors_to_reconstruction(...)

# 8. Merge walls
merge_and_save()
```

### Expected Console Output
```
✓ Using pair 0-1 with 847 inliers
  Triangulated 623 points
  Camera 0: 623 tracked
  Camera 1: 623 tracked

--- Adding Camera 2 ---
  Found 456 2D-3D correspondences
  ✓ Success: 401 inliers, 401 tracked
  Triangulated 89 new points

...

✓ Bundle adjustment complete!
  Final cost: 1247.8934
  
✓ Exported 8 cameras to cameras.json
✓ Saved colored point cloud to wall_A.ply
✓ Complete! Use merged_room.ply and all_cameras.json
```

---

## 👁️ Viewing Results

### Step 1: Download Output Files

From Colab, download these files (left sidebar → Files):
- `merged_room.ply` (colored point cloud)
- `all_cameras.json` (camera poses)

### Step 2: Setup Local Viewer

Create a folder structure:
```
reconstruction_viewer/
├── index.html          (download from repo)
├── merged_room.ply     (from Colab)
└── all_cameras.json    (from Colab)
```

### Step 3: Launch Viewer

**Option A: Python HTTP Server**
```bash
cd reconstruction_viewer
python -m http.server 8000
```
Then open: `http://localhost:8000`

**Option B: VS Code Live Server**
1. Install "Live Server" extension
2. Right-click `index.html` → "Open with Live Server"

**Option C: Direct File Open**
Some browsers allow: `file:///path/to/index.html`

### Viewer Controls

| Action | Control |
|--------|---------|
| **Rotate view** | Left mouse drag |
| **Pan view** | Right mouse drag |
| **Zoom** | Mouse wheel |
| **Next camera** | Space bar or "Next →" button |
| **Previous camera** | "← Prev" button |
| **Reset view** | "Reset" button |

---

## 🔬 Pipeline Stages

### 1. Image Preprocessing
- Convert to grayscale
- Normalize intensities
- Output: Preprocessed image array

### 2. Feature Extraction (SIFT)
```python
sift = cv2.SIFT_create()
keypoints, descriptors = sift.detectAndCompute(gray, None)
```
- Extracts scale-invariant features
- Typically 1000-3000 keypoints per image

### 3. Feature Matching
```python
bf = cv2.BFMatcher(cv2.NORM_L2)
matches = bf.knnMatch(des1, des2, k=2)

# Lowe's Ratio Test
for m, n in matches:
    if m.distance < 0.75 * n.distance:
        good_matches.append(m)
```

### 4. Essential Matrix Estimation
```python
E, mask = cv2.findEssentialMat(
    pts1, pts2, K,
    method=cv2.RANSAC,
    prob=0.999,
    threshold=1.0
)
```

### 5. Camera Pose Recovery
- Decompose E into 4 possible [R|t] solutions
- Test with triangulation
- Select pose with most points in front of both cameras

### 6. Two-View Initialization
```python
R1, R2, t = cv2.decomposeEssentialMat(E)
# Test all 4 combinations
# Keep pose with max valid 3D points
```

### 7. Incremental Camera Addition
```python
# For each new camera:
success, rvec, tvec, inliers = cv2.solvePnPRansac(
    pts_3d, pts_2d, K, None
)
```

### 8. Bundle Adjustment
- Jointly optimizes all camera poses and 3D points
- Minimizes reprojection error
- Uses sparse Jacobian for efficiency

### 9. Point Coloring
- Projects 3D points back to source images
- Samples RGB values from multiple views
- Averages colors for robustness

### 10. Multi-Wall Merging
- Scales second reconstruction to match first
- Applies rotation and translation alignment
- Combines point clouds and camera poses

---

## 📁 File Structure

```
CV-Project-G16/
├── group_16.py              # Main Python script
├── group_16.ipynb           # Jupyter notebook (recommended)
├── index.html               # 3D viewer
├── README.md                # This file
├── wall1/                   # First wall images
│   ├── 1.jpg
│   ├── 2.jpg
│   └── ...
├── wall2/                   # Second wall images
│   ├── 1.jpg
│   ├── 2.jpg
│   └── ...
└── output/                  # Generated files (not in repo)
    ├── merged_room.ply
    ├── all_cameras.json
    ├── cameras.json
    ├── cameras_B.json
    ├── view_graph.json
    └── transformation.npy
```

## 👥 Contributors

**Group 16**
- Fatima Nauman
- Manahil Shah

**Course**: Computer Vision
**Institution**: LUMS 
**Semester**: Fall 2025

---

## 🙏 Acknowledgments

- OpenCV documentation and tutorials
- Open3D library for 3D processing
- Three.js for web-based visualization
- Multiple View Geometry in Computer Vision (Hartley & Zisserman)
- TA Haseeb for rejecting juice box dataset

---
