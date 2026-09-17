# AI-Driven Real-Time Highway Traffic Enforcement & Automated SMTP Notification System

An end-to-end computer vision and software engineering pipeline designed for intelligent transportation systems (ITS). This system tracks high-speed highway vehicles, estimates their peak real-world velocities (km/h) using planar homography camera calibration, and triggers an adaptive five-layer gating loop for local character recognition and dynamic cloud database synchronization.

---

## 🛠️ System Architecture & Mechanics

### 1. Computer Vision Pipeline (YOLO11m & ByteTrack)
* **Object Detection:** Deployed `yolo11m.pt` optimized for mixed multi-lane highway vehicle class structures (`Car`, `Motorcycle/CNG`, `Bus`, `Truck`).
* **Multi-Object Tracking (MOT):** Integrated a strict `bytetrack.yaml` association loop framework to assign unique `track_id` coordinates to each vehicle node across temporal frames, preventing indexing or mismatch errors.

### 2. Camera-to-World Planar Homography Calibration
* Programmed a geometric mapping module using `cv.getPerspectiveTransform` to map the non-linear pixel layouts of a 4-point **Polygon ROI** (`A`, `B`, `C`, `D`) directly to a 2D Cartesian real-world framework grid (**Width: 25.5m, Length: 100m** based on standard highway regulations).
* This transform completely eliminates perspective lens distortion, allowing the algorithm to trace true linear displacement directly in meters.

### 3. Adaptive 5-Layer OCR Gating Module (`ocr_gate`)
To optimize compute performance and prevent processing lag, the character extraction module bypasses generic frame modulos and evaluates localized vehicle bounding boxes against five strict spatial filters:
* **World Proximity Filter (`OCR_MIN_WORLD_Y = 72.0`):** By calculating the tire contact points on the road plane, OCR remains completely dormant while a vehicle is far away, automatically activating only in the nearest ~40% zone of the camera's view.
* **Vehicle Approach Direction Test:** Tracks bounding box surface area frames dynamically. If a vehicle's area shrinks below 90% of its peak recorded matrix, it indicates the vehicle is receding or leaving the optimal zone, terminating further extraction loops immediately.
* **Laplacian Motion Blur Filter (`cv.Laplacian`):** Computes pixel variance scores to automatically isolate and reject high-speed motion blurs, spending the OCR frame budget only on crystal-clear images.
* **Dynamic Fallback Catch:** If a plate remains illegible or `UNKNOWN` after 8 targeted attempts due to highway speed smears, the system terminates processing and locks onto a safe fallback identifier (e.g., `TRACK-ID-07`) to avoid computing loops indefinitely.

### 4. Automated Cloud Logging & Deduplicated SMTP Alert Engine
* **Excel Time-Series Telemetry:** Leverages Pandas to track absolute velocity adjustments, logging only the **Highest (Peak) Speed** achieved by a unique vehicle to a single, clean database row.
* **Google Drive Integration:** The data writes to a secure path (`/content/drive/MyDrive/Highway_Traffic_Enforcement_Database.xlsx`), automatically generating a timestamped backup before each session to secure existing records.
* **Manually-Triggered Email Alerts:** A separate SMTP notification module reads the database, parses rows where `Highest Speed > 70 km/h`, maps them to a manually entered `Driver Email` column, and securely dispatches a formal traffic violation alert using `smtplib` via Gmail's App Password infrastructure (`://gmail.com`, 465). It enforces a strict deduplication check so each vehicle receives exactly one notice.

---

## 💻 Local Workspace Setup (VS Code Deployment Guide)

To execute this `.ipynb` pipeline locally inside Visual Studio Code instead of a cloud sandbox, implement the following steps:

### 1. IDE Extensions Required
Open your VS Code Extensions terminal (`Ctrl + Shift + X`) and install:
* `Python` (Official Microsoft Release)
* `Jupyter` (Official Microsoft Release for notebook rendering)

### 2. Environment Dependencies
Open a fresh terminal window inside your project workspace directory and run:

```bash
# Clone the repository files
git clone https://github.com
cd your-repository-name

# Install required deep learning and processing engines
pip install ultralytics supervision easyocr pandas openpyxl opencv-python scipy matplotlib
```

### 3. Execution Adjustments
1. Open the `.ipynb` file in VS Code and select your local Python kernel interpreter in the top-right corner.
2. Update the `SOURCE_VIDEO` path string at the beginning of the notebook from the default cloud path to your local hard drive location (e.g., `C:/Users/Name/Downloads/highway_traffic.mp4`).
3. For local script execution of the SMTP block without utilizing Colab Secrets, save your password key to your local OS environment variables or enter it via the safe runtime password prompt (`getpass.getpass()`).

---

## 📊 Data Attribution & Licensing

* 🌐 **Video Source Credit:** The underlying traffic footages evaluated across this research framework were sourced entirely from open-access transportation databases on YouTube, utilized strictly for non-commercial academic testing, algorithm benchmarking, and computer vision validation.
* 📜 **Open-Source License:** Published as an open-access repository under the terms of the liberal **MIT License**. You are free to modify, deploy, and scale this project architecture with zero legal liability, provided that original author attribution is maintained.
