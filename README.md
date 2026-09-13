# AI-Driven Real-Time Vehicle Speed Estimation 

An end-to-end computer vision and computational mechanics pipeline designed to estimate vehicle velocities in real-time across highway traffic video streams. The system utilizes advanced object detection linked with geometric coordinate mapping to compute precise real-world vehicle speeds ($km/h$).

---

## 🛠️ System Architecture & Framework

### 1. Deep Learning Object Detection & MOT
* **YOLO11m Model Integration:** Implemented the state-of-the-art `yolo11m.pt` weight framework optimized for multi-class vehicular topologies (`Car`, `Motorcycle/CNG`, `Bus`, `Truck`).
* **ByteTrack Multi-Object Tracking (MOT):** Deployed a robust temporal tracking association loop framework to preserve unique `trace_id` mappings across overlapping vehicle sequences and varying distances.

### 2. Camera-to-World Coordinate Perspective Transform
* Developed a high-precision spatial mapping module based on Planar Homography principles (`cv.getPerspectiveTransform`).
* Mapped the non-linear pixel coordinates of the calculated 4-point **Polygon ROI** to an exact 2D Cartesian real-world framework grid (**Width: 25.5m, Length: 100m** based on highway width regulations).
* This transform accounts for perspective camera distortions, allowing the system to compute linear displacements directly in meters.

### 3. Structural Velocity Extraction
* The algorithm tracks the consecutive frame-by-frame spatial displacements of unique `trace_id` nodes over a multi-frame rolling window.
* Employs real-time median displacement arrays along the structural coordinate matrix axes to stabilize speed computation against minor tracking fluctuations before outputting the final absolute velocities.

---

## 📊 Project Artifacts & Execution Demo

* 💻 **Source Code:** Open the uploaded `.ipynb` file in this repository to view the full execution pipeline.
* 🎥 **Live Action Demo Video:** Play the `Automobile speed  annotated-comp.mp4` file to view the bounding boxes, unique vehicle trace lines, and active speed logs in action.
