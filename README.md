# Camera Pose Estimation from Landmarks

**Find where a photograph was taken using only a handful of identifiable landmarks.**

Given a photo and the real-world coordinates (latitude, longitude, altitude) of a few recognizable landmarks visible in it, this notebook searches for the observer's position and focal length. It returns a heatmap of likely camera locations and a line of possible positions in 3D space.

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/ASpacesys/camera-pose-estimation-from-landmarks/blob/main/Camera_Pose_Estimation_from_Landmarks.ipynb)

---

## What it does

1. **Identify landmarks**: upload your photo and click points in it. Then click the same landmarks, in the same order, on a Google Map. Altitudes (MSL) are looked up automatically with the Open-Meteo elevation API. For ground-level photos you can enter (latitude, longitude, altitude) manually instead, which lets you use points like building faces.
2. **Set your lens**: focal length is entered in millimeters (35mm-equivalent) and converted to pixels, so you can sweep realistic lenses instead of raw pixel values.
3. **Reprojection-error heatmap**: searches a 30-mile radius around the first landmark. At each candidate location it solves the best camera orientation, then searches over camera height and focal length. The result is a map where red is low error and blue is high error. A color-scale tool lets you adjust the range, colormap and opacity.
4. **Interactive zoom**: drag a box on the heatmap to re-run the search over just that area at finer resolution, with more height and focal-length samples. *Back one level* undoes a zoom and *Reset to original* returns to the full view. The view resets automatically when you load a new photo or new landmarks.
5. **Best-fit line**: fits a line through the low-error region of the (zoomed) heatmap and reports how well it fits. It is compared against the line from the focal-length reprojection search.
6. **Export and visualize**:
   - **KML** for Google Earth, containing both lines, the heatmap's best cell, the landmarks and the predicted observer position.
   - **Google Maps** view of both lines with the landmarks.

---

## Requirements

- Google Colab (recommended) or a local Jupyter environment
- A Google Maps API key (recommended). A Maps Demo Key can be obtained for free from Google for personal use. It is used for the landmark map picker and the Google Maps visualization. The heatmap and zoom tool use Leaflet with Esri satellite tiles and need no key.
- Python packages are installed by the notebook (pymap3d, opencv-python, gmplot, etc.). Colab already includes numpy, scipy, folium and matplotlib.

## Tips for better results

- **Use as many landmarks as you can.** Fewer points give much noisier results.
- **Spread them out** across the field of view and in depth. Clustered landmarks at similar elevations leave camera distance weakly constrained.
- **Know your focal length.** Distance estimates scale directly with it. Use EXIF (`FocalLengthIn35mmFilm`) when available, and account for digital zoom or crops.
- **Narrow the search once you have a rough answer.** After a first zoom, tighten the height and focal-length ranges around the winner to refine cheaply.
- Search cost grows with grid cells x heights x focal lengths, so lower any of them if a run is slow.

## Known limitations

- The line of possible locations can be flipped along the Z-axis of the ENU origin point.
- Earth's curvature is not accounted for. ENU coordinates may be replaced with ECEF/geodetics in the future.
- The search uses a reprojection-error method that needs camera intrinsics. It may be replaced with one that doesn't.

---

## Inspiration

This project was inspired by [colsto](https://www.youtube.com/@colsto) and [MostOriginalIGN](https://github.com/mostoriginalign). To my knowledge, no easily accessible real-world pose solver exists online, which is why I created this notebook. The primary purpose of this project is to make OSINT more accessible to people, most notably for aerial imagery. For example, there may be many flights flying in and out of an airport every day, but each takes a different route on departure or arrival. Being able to pinpoint an aerial picture to a reasonable accuracy allows the exact airplane to be found.

---

## License
This project is licensed under the terms of the MIT license.
