# Camera Pose Estimation from Landmarks

**Find exactly where a photograph was taken — and where the camera was pointing — using only a handful of identifiable landmarks.**

Given a photo and the real-world coordinates (latitude, longitude, altitude) of a few recognizable landmarks visible in it, this notebook recovers the **observer's 3D position, gaze direction, and focal length**. It's an implementation of the classic *camera resectioning* / *photogrammetry* problem, useful for photo geolocation, OSINT and photo verification, forensic analysis, and single-image 3D reconstruction.

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/ASpacesys/camera-pose-estimation-from-landmarks/blob/main/Camera_Pose_Estimation_from_Landmarks.ipynb)

---

## What it does

1. **Identify landmarks** — click points in your photo and provide their real-world lat/lon/altitude (manually, or by clicking the same landmarks on Google Maps).
2. **Fast rough search** — computes the line in 3D space the observer could lie on (accounts for scale/dolly-zoom ambiguity, where zooming and moving can produce identical images).
3. **Precise position solve** — two solvers are provided:
   - **OpenCV `solvePnP`** (P3P + Levenberg–Marquardt refinement) — fast, closed-form pose and focal-length estimation.
   - **Custom PyTorch/LBFGS gradient-descent solver** — minimizes reprojection error together with perspective-invariant angle, distance, and triangle-area losses computed over every combination of landmarks, with multi-restart optimization to avoid local minima. Useful when focal length and principal point aren't known in advance.
4. **Export results** — writes a KML file with the observer's position and gaze direction as a line, ready to open directly in **Google Earth**. An optional Google Maps visualization is also included.

---

## Requirements

- Google Colab (recommended) or a local Jupyter environment with GPU/CPU
- Python packages (installed automatically by the notebook): `pymap3d`, `opencv-python`, `torch`, `numpy`, `scipy`, `gmplot`
- (Optional) A **Google Maps JavaScript API key** with the *Maps JavaScript API* and *Elevation API* enabled, if you want to pick landmark coordinates interactively on a map or view results overlaid on Google Maps instead of Google Earth.

## Usage

1. Open the notebook in Colab (badge above) or upload it to your own Colab account.
2. Run the **Install Dependencies** cell.
3. **Input Pixel Coordinates**: upload your photo and click on each identifiable landmark to record its pixel location.
4. **World Coordinate Entry**: provide the real-world coordinates for each landmark, either by:
   - clicking the same points on an embedded Google Map (aerial/overhead images), or
   - manually entering `(latitude, longitude, altitude)` tuples (ground-level images, e.g. building corners).
5. Run **Solving for possible positions** to get the line of candidate observer locations.
6. Run **Solving for an exact position** to run the full gradient-descent solve (~90 seconds) and recover the best-estimate observer position and gaze direction.
7. Run the **Visualization** cells to export a `.kml` file (Google Earth) and/or view an interactive Google Map of the result.

**Tips for best results:**
- Use **5–8 landmarks** — fewer than 5 leaves the system underdetermined; more than ~10–12 slows the combinatorial angle/distance/area loss terms without adding much accuracy.
- Spread landmarks out with meaningful angular separation as seen from the camera; tightly clustered points make depth and focal length hard to disentangle.
- The exact-position gradient-descent solve is less reliable for **near-ground** photos than the rough line-of-positions search — for ground-level shots, treat the line as the primary output and use the exact solve as a secondary estimate.

---

## How it works

The core idea is standard pinhole-camera perspective projection: a 3D point `P` is projected into image coordinates, where `O` is the observer's 3D position, `(n, u, r)` form an orthonormal camera basis (gaze direction, up, right), `f` is the focal length in pixels, and `(cx, cy)` is the principal point. Given several landmarks with known 3D positions and their corresponding pixel coordinates, the notebook solves the inverse problem — recovering `O`, `n`, `u`, and `f` — either via OpenCV's PnP solver or by minimizing reprojection error (plus angle/distance/area consistency terms) with gradient descent.

---

## Limitations

- Assumes a simple pinhole camera model (no lens distortion) unless you supply `dist_coeffs`.
- Accuracy depends heavily on the precision of your landmark pixel-click locations and the accuracy of their real-world coordinates/altitudes.
- The gradient-descent solver is non-convex and can converge to local minima (including a mirrored/flipped solution); multi-restart optimization mitigates but doesn't eliminate this.
- Not intended for real-time or production use — this is an educational/investigative tool.

---

## License
This project is licensed under the terms of the MIT license."
