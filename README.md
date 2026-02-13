<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Suruh Akudus - Solusi Transportasi & PPOB 🛵</title>
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
    <style>
        :root {
            --primary: #007bff;
            --primary-dark: #0056b3;
            --success: #28a745;
            --card-bg: #ffffff;
            --text-color: #333333;
            --body-bg: #f4f7f9;
            --input-bg: #ffffff;
            --input-border: #cccccc;
        }

        .dark-theme {
            --card-bg: #1e2125;
            --text-color: #e9ecef;
            --body-bg: #121416;
            --input-bg: #2b3035;
            --input-border: #495057;
        }

        body { font-family: 'Segoe UI', Arial, sans-serif; background-color: var(--body-bg); color: var(--text-color); padding: 15px; margin: 0; transition: 0.5s; }
        .card { max-width: 450px; margin: auto; background: var(--card-bg); padding: 25px; border-radius: 20px; box-shadow: 0 10px 30px rgba(0,0,0,0.1); }
        
        .header-section { text-align: center; margin-bottom: 20px; }
        h2 { color: var(--primary); margin: 0; letter-spacing: 1px; }
        .subtitle { font-size: 14px; opacity: 0.8; margin-top: 5px; font-weight: 500; }

        .tabs { display: flex; gap: 10px; margin-bottom: 20px; background: rgba(0,0,0,0.05); padding: 5px; border-radius: 12px; }
        .tab-btn { flex: 1; padding: 12px; border: none; border-radius: 10px; cursor: pointer; font-weight: bold; background: none; color: var(--text-color); transition: 0.3s; }
        .tab-btn.active { background: var(--primary); color: white; box-shadow: 0 4px 10px rgba(0,123,255,0.3); }

        label { display: block; margin-top: 15px; font-weight: bold; font-size: 14px; color: var(--primary); }
        select, input, textarea { width: 100%; padding: 12px; margin-top: 6px; border: 1px solid var(--input-border); border-radius: 10px; box-sizing: border-box; font-size: 15px; background: var(--input-bg); color: var(--text-color); outline: none; }
        input:focus { border-color: var(--primary); box-shadow: 0 0 5px rgba(0,123,255,0.2); }
        
        .btn-lokasi { background: none; border: none; color: var(--primary); font-size: 12px; cursor: pointer; padding: 8px 0; font-weight: bold; display: flex; align-items: center; gap: 4px; }
        .search-results { background: var(--input-bg); border: 1px solid var(--input-border); position: absolute; z-index: 1000; width: 100%; max-height: 200px; overflow-y: auto; display: none; border-radius: 8px; box-shadow: 0 4px 12px rgba(0,0,0,0.1); }
        .search-item { padding: 12px; cursor: pointer; border-bottom: 1px solid var(--input-border); font-size: 13px; line-height: 1.4; }
        .search-item:hover { background: rgba(0,123,255,0.05); }

        .box-tarif { background: rgba(0, 123, 255, 0.08); padding: 18px; border-radius: 15px; margin-top: 25px; text-align: center; border: 1px solid var(--primary); }
        .total-label { font-size: 13px; font-weight: 600; text-transform: uppercase; color: var(--text-color); opacity: 0.7; }
        .total-harga { font-size: 34px; font-weight: 800; color: var(--primary); display: block; margin: 5px 0; }
        .info-minimal { font-size: 11px; font-weight: bold; color: var(--success); }

        .review-section { margin-top: 35px; padding-top: 20px; border-top: 1px solid var(--input-border); }
        .review-section h4 { text-align: center; margin-bottom: 15px; color: var(--primary); display: flex; align-items: center; justify-content: center; gap: 8px; }
        .review-form { background: rgba(0,0,0,0.02); padding: 15px; border-radius: 12px; margin-bottom: 15px; }
        .review-list { max-height: 250px; overflow-y: auto; padding-right: 5px; }
        .review-item { padding: 12px; border-bottom: 1px solid var(--input-border); margin-bottom: 8px; background: rgba(255,255,255,0.05); border-radius: 8px; }
        .rev-name { font-weight: bold; color: var(--primary); font-size: 14px; display: flex; justify-content: space-between; }
        .rev-date { font-weight: normal; color: #888; font-size: 10px; }
        .rev-text { font-size: 13px; margin-top: 4px; line-height: 1.4; }

        #loading-overlay { display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.85); z-index: 10000; flex-direction: column; align-items: center; justify-content: center; color: white; text-align: center; padding: 20px; }
        .spinner { width: 50px; height: 50px; border: 5px solid rgba(255,255,255,0.1); border-top: 5px solid var(--primary); border-radius: 50%; animation: spin 1s linear infinite; margin-bottom: 15px; }
        @keyframes spin { 0% { transform: rotate(0deg); } 100% { transform: rotate(360deg); } }

        #btn-pesan { width: 100%; padding: 18px; background-color: var(--success); color: white; border: none; border-radius: 12px; margin-top: 20px; font-size: 16px; cursor: pointer; font-weight: bold; box-shadow: 0 5px 15px rgba(40,167,69,0.3); transition: 0.3s; }
        #btn-pesan:hover { transform: translateY(-2px); box-shadow: 0 7px 20px rgba(40,167,69,0.4); }
        .hidden { display: none; }
    </style>
</head>
<body>

<div id="loading-overlay">
    <div class="spinner"></div>
    <b id="loading-text">Sedang menyiapkan pesanan Anda... 🕒</b>
</div>

<div class="card">
    <div class="header-section">
        <h2>SURUH AKUDUS 🚀</h2>
        <p class="subtitle" id="time-greeting">Mitra Setia Transportasi Anda</p>
    </div>

    <div class="tabs">
        <button class="tab-btn active" onclick="switchTab('jasa')">🛵 Layanan Jasa</button>
        <button class="tab-btn" onclick="switchTab('ppob')">💎 Produk Digital</button>
    </div>

    <div id="tab-jasa">
        <label>Silakan Pilih Layanan 📋</label>
        <select id="layanan" onchange="hitungTarif()">
            <option value="Ojek" data-harga="2500">Ojek Online (Rp2.500/km) 🛵</option>
            <option value="Kurir" data-harga="2000">Kurir Barang (Rp2.000/km) 📦</option>
            <option value="Jastip" data-harga="2000">Jasa Titip Belanja (Rp2.000/km) 🛒</option>
        </select>

        <div style="position: relative;">
            <label>Titik Penjemputan 📍</label>
            <input type="text" id="jemput" placeholder="Ketik nama desa atau lokasi..." oninput="cariAlamat('jemput')">
            <button type="button" class="btn-lokasi" onclick="ambilLokasiSaya()">✨ Gunakan Lokasi Saya Saat Ini</button>
            <div id="res-jemput" class="search-results"></div>
        </div>

        <div style="position: relative;">
            <label>Titik Tujuan 🏁</label>
            <input type="text" id="tujuan" placeholder="Ketik nama desa atau tujuan..." oninput="cariAlamat('tujuan')">
            <div id="res-tujuan" class="search-results"></div>
        </div>

        <div class="box-tarif">
            <span class="total-label">Estimasi Total Tarif 💰</span>
            <span class="total-harga" id="tampilan-tarif">Rp0</span>
            <span class="info-minimal">✅ Tarif minimal Rp8.000 untuk jarak 0-2 km</span>
        </div>
        <button id="btn-pesan" onclick="prosesPesan()">PESAN SEKARANG VIA WHATSAPP ✅</button>
    </div>

    <div id="tab-ppob" class="hidden">
        <div style="background: linear-gradient(135deg, var(--primary), #00c6ff); color: white; padding: 25px; border-radius: 15px; cursor: pointer; text-align: center; box-shadow: 0 5px 15px rgba(0,123,255,0.2);" onclick="window.open('https://link.speedcash.co.id/shopwarehouse', '_blank')">
            <h4 style="margin:0;">PEMBAYARAN DIGITAL (PPOB) ⚡</h4>
            <p style="font-size:12px; margin-top:10px; line-height: 1.5;">Nikmati kemudahan isi ulang pulsa, paket data, dan token listrik secara otomatis 24 jam nonstop! 🕒</p>
        </div>
    </div>

    <div class="review-section">
        <h4>💬 Apa Kata Mereka?</h4>
        <div class="review-form">
            <input type="text" id="rev-nama" placeholder="Nama Lengkap Anda..." required>
            <textarea id="rev-pesan" rows="2" placeholder="Bagikan ulasan positif Anda di sini..."></textarea>
            <button onclick="simpanReview()" style="width:100%; margin-top:10px; padding:12px; background:var(--primary); color:white; border:none; border-radius:8px; cursor:pointer; font-weight:bold;">Kirim Ulasan ✨</button>
        </div>
        <div id="review-list" class="review-list"></div>
    </div>
</div>

<script>
    // 1. TEMA & SAPAAN
    const jam = new Date().getHours();
    const greetingEl = document.getElementById('time-greeting');
    if (jam >= 18 || jam < 6) {
        document.body.classList.add('dark-theme');
        greetingEl.innerText = "Selamat Malam! Semoga istirahat Anda menyenangkan 🌙";
    } else if (jam < 12) {
        greetingEl.innerText = "Selamat Pagi! Semangat memulai aktivitas hari ini ☀️";
    } else {
        greetingEl.innerText = "Selamat Siang! Kami siap membantu perjalanan Anda 🌤️";
    }

    // 2. SEARCH LOGIC (Jateng, DIY, Jatim)
    let locJemput = null, locTujuan = null, jarakFinal = 0, searchTimeout = null;

    function switchTab(t) {
        document.getElementById('tab-jasa').classList.toggle('hidden', t !== 'jasa');
        document.getElementById('tab-ppob').classList.toggle('hidden', t !== 'ppob');
        document.querySelectorAll('.tab-btn').forEach((b, i) => b.classList.toggle('active', (i===0 && t==='jasa') || (i===1 && t==='ppob')));
    }

    async function cariAlamat(type) {
        clearTimeout(searchTimeout);
        const query = document.getElementById(type).value;
        const resDiv = document.getElementById('res-' + type);
        if (query.length < 3) {
            resDiv.style.display = 'none';
            return;
        }
        
        searchTimeout = setTimeout(async () => {
            // Filter lokasi mencakup Jawa Tengah, DIY, dan Jawa Timur
            const regionQuery = "Jawa Tengah, DIY, Jawa Timur, Indonesia";
            const response = await fetch(`https://nominatim.openstreetmap.org/search?format=json&q=${query}&viewbox=108.0,-6.0,116.0,-9.0&bounded=1&limit=6&addressdetails=1`);
            const data = await response.json();
            
            resDiv.innerHTML = '';
            if (data.length > 0) {
                resDiv.style.display = 'block';
                data.forEach(item => {
                    const div = document.createElement('div');
                    div.className = 'search-item';
                    // Menampilkan alamat yang lebih rapi
                    const addr = item.display_name.split(',').slice(0, 3).join(',');
                    div.innerHTML = `📍 <b>${addr}</b><br><small style="color:#777">${item.display_name}</small>`;
                    div.onclick = () => {
                        document.getElementById(type).value = addr;
                        resDiv.style.display = 'none';
                        if (type === 'jemput') locJemput = {lat: item.lat, lon: item.lon};
                        else locTujuan = {lat: item.lat, lon: item.lon};
                        if (locJemput && locTujuan) hitungRute();
                    };
                    resDiv.appendChild(div);
                });
            } else {
                resDiv.style.display = 'none';
            }
        }, 600);
    }

    async function ambilLokasiSaya() {
        if (!navigator.geolocation) return alert("Maaf, fitur lokasi tidak didukung oleh perangkat Anda.");
        document.getElementById('jemput').value = "Sedang mendeteksi lokasi Anda... 📡";
        navigator.geolocation.getCurrentPosition(async (pos) => {
            locJemput = {lat: pos.coords.latitude, lon: pos.coords.longitude};
            const response = await fetch(`https://nominatim.openstreetmap.org/reverse?format=json&lat=${locJemput.lat}&lon=${locJemput.lon}`);
            const data = await response.json();
            const addr = data.display_name.split(',').slice(0, 3).join(',');
            document.getElementById('jemput').value = addr;
            if (locTujuan) hitungRute();
        }, () => {
            alert("Gagal mengakses lokasi. Pastikan GPS Anda aktif.");
            document.getElementById('jemput').value = "";
        });
    }

    async function hitungRute() {
        const url = `https://router.project-osrm.org/route/v1/driving/${locJemput.lon},${locJemput.lat};${locTujuan.lon},${locTujuan.lat}?overview=false`;
        try {
            const res = await fetch(url);
            const data = await res.json();
            if (data.routes && data.routes[0]) {
                jarakFinal = (data.routes[0].distance / 1000).toFixed(1);
                hitungTarif();
            }
        } catch (e) {
            console.error("Gagal menghitung rute.");
        }
    }

    function hitungTarif() {
        const hargaPerKm = document.getElementById('layanan').selectedOptions[0].getAttribute('data-harga');
        let jarakNum = parseFloat(jarakFinal);
        // Minimal Rp8.000 untuk jarak <= 2km
        let total = (jarakNum <= 2) ? 8000 : (jarakNum * hargaPerKm);
        document.getElementById('tampilan-tarif').innerText = "Rp" + Math.round(total).toLocaleString('id-ID');
    }

    function prosesPesan() {
        if (jarakFinal <= 0) return alert("Silakan masukkan lokasi penjemputan dan tujuan Anda terlebih dahulu.");
        
        document.getElementById('loading-overlay').style.display = 'flex';
        const msgList = [
            "Sedang mencarikan mitra terbaik untuk Anda... 🛵",
            "Menghitung estimasi waktu perjalanan... 🕒",
            "Menghubungkan ke layanan WhatsApp... ✅"
        ];
        let i = 0;
        const interval = setInterval(() => {
            document.getElementById('loading-text').innerText = msgList[i];
            i++;
            if(i >= msgList.length) clearInterval(interval);
        }, 700);

        setTimeout(() => {
            const teks = encodeURIComponent(`*KONFIRMASI PESANAN SURUH AKUDUS*\n\n` +
                `📋 Layanan: ${document.getElementById('layanan').value}\n` +
                `📍 Jemput: ${document.getElementById('jemput').value}\n` +
                `🏁 Tujuan: ${document.getElementById('tujuan').value}\n` +
                `🛣️ Jarak: ${jarakFinal} KM\n` +
                `💰 Total Tarif: ${document.getElementById('tampilan-tarif').innerText}\n\n` +
                `Mohon segera diproses ya, terima kasih! 🙏`);
            window.open(`https://wa.me/6285124569347?text=${teks}`);
            document.getElementById('loading-overlay').style.display = 'none';
        }, 2500);
    }

    // 3. REVIEW LOGIC
    function simpanReview() {
        const nama = document.getElementById('rev-nama').value;
        const pesan = document.getElementById('rev-pesan').value;
        if (!nama || !pesan) return alert("Mohon lengkapi nama dan ulasan Anda.");

        const reviewBaru = { nama, pesan, tgl: new Date().toLocaleDateString('id-ID') };
        let reviews = JSON.parse(localStorage.getItem('suruh_reviews')) || [];
        reviews.unshift(reviewBaru);
        localStorage.setItem('suruh_reviews', JSON.stringify(reviews));

        document.getElementById('rev-nama').value = '';
        document.getElementById('rev-pesan').value = '';
        tampilkanReview();
    }

    function tampilkanReview() {
        const list = document.getElementById('review-list');
        let reviews = JSON.parse(localStorage.getItem('suruh_reviews')) || [
            {nama: "Budi Pratama", pesan: "Pelayanannya sangat memuaskan dan pengemudinya ramah sekali! ⭐⭐⭐⭐⭐", tgl: "13/02/2026"},
            {nama: "Lestari Wahyuni", pesan: "Jastipnya amanah banget, belanjaan sampai dengan selamat tanpa ada yang kurang. Terima kasih! 🙏", tgl: "12/02/2026"}
        ];
        list.innerHTML = reviews.map(r => `
            <div class="review-item">
                <div class="rev-name"><span>👤 ${r.nama}</span> <span class="rev-date">🗓️ ${r.tgl}</span></div>
                <div class="rev-text">"${r.pesan}"</div>
            </div>
        `).join('');
    }
    tampilkanReview();
</script>
</body>
</html>
