# Camera Pose Estimation from Landmarks

**Find exactly where a photograph was taken using only a handful of identifiable landmarks.**

Given a photo and the real-world coordinates (latitude, longitude, altitude) of a few recognizable landmarks visible in it, this notebook recovers the observer's 3D position, gaze direction, and focal length. 

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/ASpacesys/camera-pose-estimation-from-landmarks/blob/main/Camera_Pose_Estimation_from_Landmarks.ipynb)

---

## What it does

1. **Identify landmarks**: click points in your photo and provide their real-world lat/lon/altitude
2. **Fast rough search**: computes the line in 3D space the observer could lie on
3. **Precise position solve**: custom PyTorch/LBFGS gradient-descent solver, minimizes reprojection error to find the correct camera orientation, focal length, and position
4. **Export results**: writes a KML file with the observer's predicted position on the line of possible positions

---

## Requirements

- Google Colab (recommended) or a local Jupyter environment
- A Google Maps API key (recommended). A Maps Demo Key can be obtained for free from Google for personal use.

---

## Inspiration

This project was inspired by [colsto](https://www.youtube.com/watch?v=BumQicEt3Tw) and [MostOriginalIGN](https://github.com/mostoriginalign). To my knowledge, no easily accessible real-world pose solver exists online, which is why I created this notebook. The primary purpose of this project is to make OSINT more accessible to people, most notably for aerial imagery. For example, there may be many flights flying in and out of an airport every day, but each takes a different route on departure or arrival. Being able to pinpoint an aerial picture to a reasonable accuracy allows the exact airplane to be found.

---

## License
This project is licensed under the terms of the MIT license.
