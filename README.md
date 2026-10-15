# Vehicle Speed Estimation System

A Flask-based computer vision application that detects vehicles in uploaded video footage, tracks them across frames, estimates speed, and exports both an annotated video and an Excel report for each detected vehicle.

The project integrates YOLOv8 for object detection, ByteTrack for multi-object tracking, and OpenCV-based perspective transformation and speed estimation.

## Features

- Vehicle detection using YOLOv8
- Multi-object tracking using ByteTrack
- Speed estimation from frame-to-frame displacement
- Annotated video output with bounding boxes, labels, and speed values
- Excel export of vehicle data for downstream reporting
- Web interface for uploading video files and viewing results
- Contact form data storage in SQLite

## Project Structure

```text
Deeplearning-using-a-vehicle-speed-estimation-system/
├── README.md
├── .gitignore
├── .env.example
├── requirements.txt
├── Dockerfile
├── docker-compose.yml
├── final_code/
│   ├── app.py
│   ├── your_backend.py
│   ├── uploads/
│   ├── static/
│   ├── templates/
│   └── __pycache__/
└── models/
    └── yolov8x.pt
```

## Tech Stack

- Python 3.10+
- Flask
- OpenCV
- Ultralytics YOLOv8
- supervision
- pandas
- openpyxl
- Gunicorn for production serving

## System Requirements

- Python 3.10 or later
- FFmpeg installed on the system
- Optional: NVIDIA GPU + CUDA for faster inference
- At least 8 GB RAM recommended for running YOLOv8 on larger videos

## Local Setup

1. Clone the repository:

```bash
git clone https://github.com/Sai-Niresh/Deeplearning-using-a-vehicle-speed-estimation-system.git
cd Deeplearning-using-a-vehicle-speed-estimation-system
```

2. Create a virtual environment:

```bash
python -m venv .venv
source .venv/bin/activate
```

3. Install dependencies:

```bash
pip install -r requirements.txt
```

4. Download or place the YOLO model weights:

```bash
mkdir -p models
# download yolov8x.pt to models/yolov8x.pt
```

You can download the official weights from the Ultralytics YOLOv8 release page or use the official `ultralytics` package download flow.

5. Run the app:

```bash
cd final_code
python app.py
```

Then open:

```text
http://localhost:5000
```

## Environment Variables

Create a `.env` file based on `.env.example`:

```bash
cp .env.example .env
```

Example `.env`:

```env
SECRET_KEY=change-me-to-a-secure-random-string
CSRF_SECRET_KEY=change-me-to-a-secure-random-string
MODEL_PATH=/app/models/yolov8x.pt
UPLOAD_FOLDER=/data/uploads
OUTPUT_FOLDER=/data/output
```

## Production Deployment

### Option 1: Docker Compose

Build and run the application:

```bash
docker compose up --build
```

Then access the app at:

```text
http://localhost:5000
```

### Option 2: Docker Run

```bash
docker build -t vehicle-speed-estimator .
docker run -d --name vehicle-speed-estimator \
  -p 5000:5000 \
  -v $(pwd)/models:/app/models \
  -v $(pwd)/data/uploads:/data/uploads \
  -v $(pwd)/data/output:/data/output \
  --env-file .env \
  vehicle-speed-estimator
```

### Option 3: Gunicorn on a Linux server

```bash
cd final_code
gunicorn --bind 0.0.0.0:5000 --workers 3 app:app
```

## Important Notes for Production

- Do not commit model weights (`yolov8x.pt`) to GitHub.
- Keep uploaded videos and generated reports in persistent volume storage.
- Use proper secret management for `SECRET_KEY` and `CSRF_SECRET_KEY`.
- For high-traffic or real-time processing, move the inference task to a background worker queue instead of running it synchronously inside the web request lifecycle.
- Consider upgrading SQLite to Postgres if you need robust production data handling.

## Deployment File Overview

### `requirements.txt`
Contains the Python dependencies needed to run the application locally and in Docker.

### `Dockerfile`
Builds a lightweight production-ready container for the Flask service.

### `docker-compose.yml`
Runs the project with mounted volumes for uploads, output files, and model weights.

### `.env.example`
Provides environment variables for local and containerized deployments.

## Troubleshooting

### App fails to start

- Verify Python version is 3.10+
- Ensure all dependencies are installed
- Check that the YOLO model file exists

### Video upload fails

- Confirm the file extension is one of: `.mp4`, `.avi`, `.mov`
- Ensure the upload folder has proper write permissions
- Check that the app has enough memory and CPU to process the file

### Output video is not generated

- Check logs in the terminal or container output
- Ensure `ffmpeg` is installed
- Evaluate whether the input video is too large or corrupted

## Recommended Future Improvements

- Move processing tasks to Celery or another background job system
- Add user authentication and upload quotas
- Store reports in cloud storage or a database
- Add tests for video pipeline reliability
- Add CI/CD automation with GitHub Actions

## License

This project does not yet declare a formal license. If you plan to distribute it publicly, consider adding an open-source license such as MIT.

## Author

Sai Niresh Narayan

Professional profile and contact details can be updated in the project documentation as needed.
