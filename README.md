# Video-Streaming-platform-AWS

<img width="512" height="421" alt="Screenshot 2026-07-04 062138" src="https://github.com/user-attachments/assets/9c20d5f2-6db5-47d5-8c00-ae925058fac8" />


The Web Server (EC2 + Flask): Serves the UI. When a user uploads an MP4, it uploads the file directly to an Input S3 Bucket and saves a "Queued" status in DynamoDB.
The Buffer (S3 Events + SQS): The moment the video hits S3, S3 automatically fires an event to an SQS Queue. This queue holds the job safely until a worker is ready.
The Worker (EC2 + FFmpeg): A dedicated background server constantly polls the SQS Queue. It downloads the raw video, uses FFmpeg to convert it into a streamable HLS format (.m3u8), uploads the chunks to an Output S3 Bucket, and updates DynamoDB to "Ready".

