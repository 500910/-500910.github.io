// ========== 視頻錄音功能預覽 ==========

async function startVideoRecording() {
    try {
        const stream = await navigator.mediaDevices.getUserMedia({
            audio: true,
            video: { 
                facingMode: "user",  // 前置鏡頭（對著自己拉琴）
                width: { ideal: 1280 },
                height: { ideal: 720 }
            }
        });
        
        // 顯示預覽畫面
        const videoPreview = document.getElementById('videoPreview');
        videoPreview.srcObject = stream;
        videoPreview.play();
        
        // 開始錄製
        mediaRecorder = new MediaRecorder(stream, {
            mimeType: 'video/webm;codecs=vp9,opus'
        });
        
        recordedChunks = [];
        mediaRecorder.ondataavailable = e => {
            if (e.data.size > 0) recordedChunks.push(e.data);
        };
        
        mediaRecorder.onstop = () => {
            const blob = new Blob(recordedChunks, { type: 'video/webm' });
            const url = URL.createObjectURL(blob);
            // 儲存影片連結，可下載或回放
            saveVideo(url);
            stream.getTracks().forEach(t => t.stop());
        };
        
        mediaRecorder.start();
    } catch (e) {
        alert('無法存取攝影機：' + e.message);
    }
}
