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
```

## 📦 Install Required Dependencies

```bash
pip install opencv-python open3d pillow pillow-heif tqdm exifread matplotlib
```

## 🏗 3. Running the Pipeline

Open the notebook `main.ipynb` in Colab
Run all cells sequentially. Each stage prints logs and visualizes intermediate outputs.

## 🔍 4. Step-By-Step Instructions to Reproduce Results

(1) Load & Display Images

```bash
jpg_images = "/content/drive/MyDrive/CV_brikwallkhoka"
```

(2) Preprocessing (Grayscale Conversion)

```bash
orb = cv2.ORB_create(nfeatures=2000)
kp, des = orb.detectAndCompute(gray, None)

```

(3) ORB Feature Extraction

```bash
orb = cv2.ORB_create(nfeatures=2000)
kp, des = orb.detectAndCompute(gray, None)

```

(4) Feature Matching (Lowe’s Ratio Test)
```bash
matches = bf.knnMatch(des1, des2, k=2)
for m, n in matches:
    if m.distance < 0.75 * n.distance:
        good_matches.append(m)
```

(5) Camera Intrinsics Extraction from EXIF
```bash
f_mm = float(tags["EXIF FocalLength"].values[0].num) / ...
f_px = f_mm / sensor_width_mm * w
```

(6) Essential Matrix Estimation (RANSAC)
```bash
E, mask = cv2.findEssentialMat(pts1, pts2, K, method=cv2.RANSAC)
```

(7) Pose Recovery + Cheirality Check
```bash
R1, R2, t = cv2.decomposeEssentialMat(E)
```

(8) Triangulation
```bash
pts4D = cv2.triangulatePoints(P1, P2, pts1.T, pts2.T)
pts3D = (pts4D[:3] / pts4D[3]).T
```

(9) Visualization of the Initial Point Cloud
```bash
ax.scatter(pts3D[:,0], pts3D[:,1], pts3D[:,2])
```

(10) Incremental SfM
- Selecting best initial pair (highest inliers)
- Two-view initialization
- Keypoint-to-3D tracking dictionary
- Adding new cameras via PnPRansac
- Triangulating new points at each step
Wrapped inside:

```bash
add_next_camera(cam_idx)
triangulate_with_camera(cam_idx, prev_cam)
```

## 🌍 5. Visualizing Outputs of Each Stage

| Stage                     | Visualization Provided                       |
|---------------------------|-----------------------------------------------|
| Image loading             | Grid of all input images                      |
| ORB feature extraction    | Keypoints drawn over images                   |
| Feature matching          | `cv2.drawMatches` visualizations              |
| Essential matrix          | Inlier counts printed                         |
| Triangulation             | 3D scatter plot                               |
| Full reconstruction       | Final PLY file + 3D visualizer                |
| (Optional) Open3D         | `o3d.visualization.draw_geometries([pcd])`    |

## 📦 6. Exporting & Viewing the 3D Point Cloud

The notebook saves `point_cloud.ply`.

## 📝 7. Notes & Recommendations

- Ensure the input images are in correct sequential order.
- If ORB matches are too weak, try increasing nfeatures or switching to SIFT.
- You may add Bundle Adjustment later for improved accuracy.
- Use Open3D ICP later if you want a smoothed reconstruction.




