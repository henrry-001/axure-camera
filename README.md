<!DOCTYPE html>
<html>
<head>
    <title>Camera Simulator</title>
    <style>
        #video { border: 1px solid #ccc; width: 100%; max-width: 400px; }
        #captureBtn { margin-top: 10px; padding: 5px 10px; }
    </style>
</head>
<body>
    <video id="video" autoplay playsinline></video>
    <br>
    <button id="captureBtn">拍摄照片</button>
    <canvas id="canvas" style="display:none;"></canvas>

    <script>
        const video = document.getElementById('video');
        const captureBtn = document.getElementById('captureBtn');

        // 访问用户摄像头
        navigator.mediaDevices.getUserMedia({ video: true })
            .then(stream => {
                video.srcObject = stream;
            })
            .catch(err => {
                console.error("Error accessing camera: ", err);
                video.style.display = 'none';
                captureBtn.style.display = 'none';
                document.body.innerHTML += '<p style="color:red;">无法访问摄像头（可能因为非HTTPS环境或未授权）。此为模拟，在Axure预览中不会工作。</p>';
            });

        // 拍照功能（示例）
        captureBtn.addEventListener('click', () => {
            const canvas = document.getElementById('canvas');
            const context = canvas.getContext('2d');
            canvas.width = video.videoWidth;
            canvas.height = video.videoHeight;
            context.drawImage(video, 0, 0, canvas.width, canvas.height);
            // 这里可以处理拍到的照片数据，例如显示在另一个img元素里
            alert('照片已拍摄（模拟）！');
        });
    </script>
</body>
</html>
