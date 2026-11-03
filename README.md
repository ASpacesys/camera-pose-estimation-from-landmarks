# Camera Pose Estimation from Landmarks

**Find exactly where a photograph was taken using only a handful of identifiable landmarks.**

Given a photo and the real-world coordinates (latitude, longitude, altitude) of a few recognizable landmarks visible in it, this notebook recovers the observer's 3D position, gaze direction, and focal length.

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/ASpacesys/camera-pose-estimation-from-landmarks/blob/main/Camera_Pose_Estimation_from_Landmarks.ipynb)

---

## What it does

1. **Identify landmarks**: click points in your photo, then click the same landmarks, in the same order, on a map. Altitudes are looked up automatically (Open-Meteo elevation API), or you can enter lat/lon/altitude manually.
2. **Set your lens**: specify focal length in millimeters (35mm-equivalent by default) instead of raw pixels. Helpers convert between mm and pixels, and you can sweep a range of focal lengths when the exact value is unknown.
3. **Fast rough search**: computes the line in 3D space the observer could lie on (the focal-length/distance ambiguity line).
4. **Heatmap search**: brute-forces candidate camera positions in a 30-mile radius around the first landmark, solving the best orientation at each point and searching over camera height and focal length. The result is a map of reprojection error (red = low error, blue = high error).
5. **Interactive zoom**: drag a box on the heatmap to re-run the search over just that area at finer resolution, with more height and focal-length samples. Use *Back one level* or *Reset to original* at any time. The view resets automatically when you load a new photo or landmarks.
6. **Best-fit line and 3D view**: fits a line through the low-error region to compare against the rough-search line, and shows the low-error positions as an interactive 3D cloud (optionally with a convex hull) on a Google Map.
7. **Precise position solve**: custom PyTorch/LBFGS gradient-descent solver, minimizes reprojection error to find the correct camera orientation, focal length, and position.
8. **Export results**: writes a KML file (open in Google Earth) with both lines of possible positions, the heatmap's best cell, the landmarks, and the observer's predicted position. A convex-hull KML of the low-error region can also be exported.

---

## Requirements

- Google Colab (recommended) or a local Jupyter environment
- A Google Maps API key (recommended). A Maps Demo Key can be obtained for free from Google for personal use. It is used for the landmark map picker and the Google Maps visualizations. The heatmap and zoom tool use Leaflet with Esri satellite tiles and need no key.
- Python packages are installed by the notebook (numpy, scipy, pymap3d, folium, plotly, gmplot, PyTorch, etc.)

## Tips for better results

- **Know your focal length.** Distance estimates scale directly with it. Use EXIF (`FocalLengthIn35mmFilm`) when available, and account for digital zoom or crops.
- **Spread your landmarks out.** Landmarks clustered in a small area, at similar elevations, make camera distance weakly constrained. Widely separated landmarks and varied elevations help most.
- **Narrow the search once you have a rough answer.** After a first zoom, tighten the height and focal-length ranges around the winner to refine cheaply.
- Search cost grows with grid cells x heights x focal lengths, so lower any of them if a run is slow.

---

## Inspiration

This project was inspired by [colsto](https://www.youtube.com/@colsto) and [MostOriginalIGN](https://github.com/mostoriginalign). To my knowledge, no easily accessible real-world pose solver exists online, which is why I created this notebook. The primary purpose of this project is to make OSINT more accessible to people, most notably for aerial imagery. For example, there may be many flights flying in and out of an airport every day, but each takes a different route on departure or arrival. Being able to pinpoint an aerial picture to a reasonable accuracy allows the exact airplane to be found.

---

## License
This project is licensed under the terms of the MIT license.
