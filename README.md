# 🎥 Video Streaming Platform on AWS

A production-style video streaming pipeline built using **AWS**, **Flask**, and **FFmpeg**.

Users upload an MP4 video, which is automatically processed into **HLS (.m3u8)** format for adaptive streaming using a scalable, event-driven architecture.

---

## 🏗️ Architecture

<p align="center">
  <img width="700" alt="Architecture" src="https://github.com/user-attachments/assets/9c20d5f2-6db5-47d5-8c00-ae925058fac8" />
</p>

---

## 🚀 Tech Stack

- 🖥️ Amazon EC2
- ☁️ Amazon S3
- 📬 Amazon SQS
- 🗄️ Amazon DynamoDB
- 🔐 AWS IAM Roles
- 🐍 Python + Flask
- 🎬 FFmpeg

---

## 📌 How It Works

### 1️⃣ Upload Server (EC2 + Flask)

- Hosts the web application.
- Accepts MP4 uploads from users.
- Uploads videos directly to the **Input S3 Bucket**.
- Creates a new record in **DynamoDB** with status:

```text
Queued
```

---

### 2️⃣ Event Pipeline (S3 + SQS)

As soon as the upload reaches S3:

```
S3 Upload
      │
      ▼
 S3 Event Notification
      │
      ▼
 Amazon SQS Queue
```

The queue stores upload jobs until a worker is available.

This ensures:

- ✅ No lost requests
- ✅ No server overload
- ✅ Automatic buffering during traffic spikes

---

### 3️⃣ Processing Worker (EC2 + FFmpeg)

A dedicated worker continuously listens to the SQS queue.

For every message it:

1. Downloads the original video from S3
2. Converts it into **HLS (.m3u8)** using FFmpeg
3. Uploads generated playlists and chunks to the **Output S3 Bucket**
4. Updates DynamoDB

```
Queued
   │
   ▼
Processing
   │
   ▼
Ready
```

---

# 🏛️ System Architecture

```
                    User
                     │
                     ▼
             Flask Web Server
                 (EC2)
                     │
                     ▼
             Input S3 Bucket
                     │
             S3 Event Notification
                     │
                     ▼
                Amazon SQS
                     │
                     ▼
             Worker EC2 (FFmpeg)
                     │
          Convert MP4 → HLS (.m3u8)
                     │
                     ▼
            Output S3 Bucket
                     │
                     ▼
               Amazon DynamoDB
```

---

# ⚙️ Worker Server Setup

## Connect to the Worker

Open the AWS Console:

**EC2 → Worker-Server → Connect → EC2 Instance Connect**

---

## Install Dependencies

```bash
cd ~

wget https://johnvansickle.com/ffmpeg/releases/ffmpeg-release-amd64-static.tar.xz

tar -xf ffmpeg-release-amd64-static.tar.xz

sudo mv ffmpeg-*-amd64-static/ffmpeg /usr/bin/ffmpeg
sudo mv ffmpeg-*-amd64-static/ffprobe /usr/bin/ffprobe

rm -rf ffmpeg-release-amd64-static.tar.xz ffmpeg-*-amd64-static

sudo dnf install -y python3.11-pip

python3.11 -m pip install boto3
```

---

## Create the Worker

```bash
nano worker.py
```

Paste your worker code and save.

---

## Start the Worker

```bash
python3.11 worker.py
```

Expected output:

```text
Worker started...
Listening for SQS messages...
```

Leave this terminal running.

---

# 🌐 Web Server Setup

## Connect to the Web Server

Open:

**EC2 → Web-Server → Connect → EC2 Instance Connect**

---

## Install Dependencies

```bash
sudo dnf install -y python3.11-pip

sudo python3.11 -m pip install flask boto3
```

---

## Create the Flask Server

```bash
nano server.py
```

Paste your Flask application and save.

---

## Start the Server

```bash
sudo python3.11 server.py
```

> `sudo` is required because the Flask application listens on **port 80**.

---

# 📂 AWS Services Used

| Service | Purpose |
|----------|---------|
| EC2 | Hosts the web server and worker |
| S3 | Stores uploaded and processed videos |
| SQS | Buffers processing jobs |
| DynamoDB | Tracks video processing status |
| IAM Roles | Secure AWS access without storing keys |

---

# 📁 Project Structure

```
.
├── server.py
├── worker.py
├── templates/
├── static/
├── uploads/
└── README.md
```

---

# 🎯 Features

- ✅ Direct video uploads
- ✅ Event-driven architecture
- ✅ Queue-based processing
- ✅ Automatic HLS conversion
- ✅ Background worker
- ✅ DynamoDB status tracking
- ✅ IAM Role authentication
- ✅ Production-style AWS architecture

---

# 📸 Demo

Add screenshots or a GIF here showing:

- Uploading a video
- Processing status
- Generated HLS output
- Final playback

---

## ⭐ If you found this project helpful, consider giving it a Star!
