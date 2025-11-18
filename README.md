<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <title>摄像头调用</title>
    <style>
        body { margin: 0; padding: 10px; text-align: center; font-family: sans-serif; }
        #videoContainer { width: 100%; max-width: 400px; margin: 0 auto; border: 1px solid #eee; border-radius: 8px; overflow: hidden; }
        #videoPreview { width: 100%; height: auto; }
        #canvasScreenshot { display: none; }
        .btn-group { margin-top: 15px; }
        button { padding: 8px 20px; margin: 0 5px; border: none; border-radius: 4px; background: #2f54eb; color: #fff; cursor: pointer; }
        button:disabled { background: #ccc; cursor: not-allowed; }
        #tipText { color: #666; margin-top: 10px; font-size: 14px; }
    </style>
</head>
<body>
    <div id="videoContainer">
        <video id="videoPreview" autoplay playsinline muted></video>
        <canvas id="canvasScreenshot"></canvas>
    </div>
    <div class="btn-group">
        <button id="startBtn">启动摄像头</button>
        <button id="captureBtn" disabled>拍照截图</button>
        <button id="stopBtn" disabled>关闭摄像头</button>
    </div>
    <div id="tipText">请点击「启动摄像头」并允许浏览器权限</div>

    <script>
        const video = document.getElementById('videoPreview');
        const canvas = document.getElementById('canvasScreenshot');
        const startBtn = document.getElementById('startBtn');
        const captureBtn = document.getElementById('captureBtn');
        const stopBtn = document.getElementById('stopBtn');
        const tipText = document.getElementById('tipText');
        let mediaStream = null;

        startBtn.addEventListener('click', async () => {
            try {
                mediaStream = await navigator.mediaDevices.getUserMedia({ video: { facingMode: 'user' } });
                video.srcObject = mediaStream;
                startBtn.disabled = true;
                captureBtn.disabled = false;
                stopBtn.disabled = false;
                tipText.textContent = '摄像头已启动，可点击拍照截图';
            } catch (error) {
                if (error.name === 'NotAllowedError') tipText.textContent = '已拒绝摄像头权限，请在浏览器设置中重新开启';
                else if (error.name === 'NotFoundError') tipText.textContent = '未检测到可用摄像头';
                else tipText.textContent = '启动失败：' + error.message;
            }
        });

        captureBtn.addEventListener('click', () => {
            canvas.width = video.videoWidth;
            canvas.height = video.videoHeight;
            canvas.getContext('2d').drawImage(video, 0, 0);
            const screenshotBase64 = canvas.toDataURL('image/png');
            tipText.textContent = '截图成功！Base64已生成（控制台查看）';
            console.log('截图Base64:', screenshotBase64);
            // 若需回传Axure，可添加以下代码（需配合Axure的全局变量）
            // window.parent.Axure.SetVariableValue("screenshot", screenshotBase64);
        });

        stopBtn.addEventListener('click', () => {
            if (mediaStream) {
                mediaStream.getTracks().forEach(track => track.stop());
                video.srcObject = null;
                mediaStream = null;
                startBtn.disabled = false;
                captureBtn.disabled = true;
                stopBtn.disabled = true;
                tipText.textContent = '摄像头已关闭';
            }
        });
    </script>
</body>
</html>
