# 📌 Structure-from-Motion (SfM) — Brick Wall Reconstruction

This repository implements a complete incremental Structure-from-Motion (SfM) pipeline using OpenCV and Open3D, reconstructing a 3D point cloud of a brick wall from sequential images.

The pipeline includes:

- Image loading and preprocessing
- ORB feature extraction
- Feature matching with Lowe’s Ratio Test
- Essential Matrix estimation (RANSAC)
- Two-view initialization with cheirality check
- Incremental camera registration via PnP
- Triangulation of new points
- 3D visualization + PLY export

---

## 🚀 1. Environment Setup

The code is designed for Google Colab.

Clone the repository:

```bash
git clone https://github.com/<fnauma>/<CV-Project-G16>.git
