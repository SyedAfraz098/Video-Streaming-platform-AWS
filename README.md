# Video-Streaming-platform-AWS

<img width="512" height="421" alt="Screenshot 2026-07-04 062138" src="https://github.com/user-attachments/assets/9c20d5f2-6db5-47d5-8c00-ae925058fac8" />


The Web Server (EC2 + Flask): Serves the UI. When a user uploads an MP4, it uploads the file directly to an Input S3 Bucket and saves a "Queued" status in DynamoDB.

The Buffer (S3 Events + SQS): The moment the video hits S3, S3 automatically fires an event to an SQS Queue. This queue holds the job safely until a worker is ready.

The Worker (EC2 + FFmpeg): A dedicated background server constantly polls the SQS Queue. It downloads the raw video, uses FFmpeg to convert it into a streamable HLS format (.m3u8), uploads the chunks to an Output S3 Bucket, and updates DynamoDB to "Ready".

Worker Server

Step 1: Connect to the Worker-Server

Go to your EC2 instances list, select Worker-Server, and click Connect at the top. Choose EC2 Instance Connect and click Connect to open a browser terminal.

Step 2: Install Dependencies

Run the following commands block-by-block to install FFmpeg and Python tools:

cd ~

wget https://johnvansickle.com/ffmpeg/releases/ffmpeg-release-amd64-static.tar.xz

tar -xf ffmpeg-release-amd64-static.tar.xz

sudo mv ffmpeg-*-amd64-static/ffmpeg /usr/bin/ffmpeg

sudo mv ffmpeg-*-amd64-static/ffprobe /usr/bin/ffprobe

rm -rf ffmpeg-release-amd64-static.tar.xz ffmpeg-*-amd64-static

sudo dnf install -y python3.11-pip

python3.11 -m pip install boto3

Step 3: Create the Script

Open a new file using the nano text editor:

nano worker.py

Step 4: Start the Worker

Run the script. It will print "Worker started. Listening…" and hang there waiting for SQS messages. Leave this tab open!


Web Server


Step 1: Connect to the Web-Server

Step 2: Install Dependencies

sudo dnf install -y python3.11-pip

sudo python3.11 -m pip install flask boto3

Step 3: Create the Server Script

nano server.py

Step 4: Start the Server

Run the Flask server. We use sudo because the script needs permission to open port 80 for HTTP traffic.

sudo python3.11 server.py

python3.11 worker.py
